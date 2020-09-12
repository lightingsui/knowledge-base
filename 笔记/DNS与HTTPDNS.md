**什么是 DNS**

DNS（Domain Name System，域名系统），DNS 服务用于在网络请求时，将域名转为 IP 地址。能够使用户更方便的访问互联网，而不用去记住能够被机器直接读取的 IP 数串。

传统的基于 UDP 协议的公共 DNS 服务极易发生 DNS 劫持，从而造成安全问题。

![img](http://misc.linkedkeeper.com/misc/img/blog/201707/linkedkeeper0_d6ffa75c-1cf0-4b5e-94af-1da37e3af9a4.jpg)

**DNS 域名系统结构**

![img](http://misc.linkedkeeper.com/misc/img/blog/201707/linkedkeeper0_a3e930b3-d268-4e17-b1f9-eb45255aa5ef.jpg)

Root 域名：DNS 域名使用时，规定由尾部句号来指定名称位于根或更高级别的域层次结构

Top Level 顶级域名：用来指示某个国家、地区或组织使用的名称的类型名称。如 .com

Second Level 域名：个人或组织在 Internet 上使用的注册名称。如 linkedkeeper.com

Third Level 域名：已注册的二级域名派生的域名。如 misc.linkedkeeper.com

**DNS 解析过程**

![img](http://misc.linkedkeeper.com/misc/img/blog/201707/linkedkeeper0_7b85360c-a1a2-4ead-8afe-418761c547c6.jpg)

1. 浏览器中输入 www.linkedkeeper.com，发出解析请求。
2. 本机的域名解析器 resolver 程序查询本地缓存和 host 文件中是否为域名的映射关系，如果有则调用这个 IP 地址映射，完成解析。
3. 如果 hosts 与本地解析器缓存都没有相应的网址映射关系，则本地解析器会向 TCP/IP 参数中设置的首选 DNS 服务器（我们叫它 Local DNS 服务器）发起一个递归的查询请求。

4. 服务器收到查询时，如果要查询的域名由本机负责解析，则返回解析结果给客户机，完成域名解析，此解析具有权威性。如果要查询的域名，不由 Local DNS 服务器解析，但该服务器已缓存了此网址映射关系，则调用这个 IP 地址映射，完成域名解析，此解析不具有权威性。
5. 如果 Local DNS 服务器本地区域文件与缓存解析都失效，则根据 Local DNS 服务器的设置（是否递归）进行查询，如果未用开启模式，Local DNS 就把请求发至13台 Root DNS。如果用的是递归模式，此 DNS 服务器就会把请求转发至上一级 DNS 服务器，由上一级服务器进行解析，上一级服务器如果不能解析，或找根 DNS 或把转请求转至上上级，以此循环。
6. Root DNS 服务器收到请求后会判断这个域名是谁来授权管理，并会返回一个负责该顶级域名服务器的一个 IP。
7. Local DNS 服务器收到 IP 信息后，将会联系负责 .com 域的这台服务器。

8. 负责 .com 域的服务器收到请求后，如果自己无法解析，它就会找一个管理 .com 域的下一级 DNS 服务器地址给本地 DNS 服务器。

9. 当 Local DNS 服务器收到这个地址后，就会找 linkedkeeper.com 域服务器，10、11重复上面的动作，进行查询。
10. 最后 www.linkedkeeper.com 返回需要解析的域名的 IP 地址给 Local DNS 服务器。
11. Local DNS 服务器缓存这个解析结果（同时也会缓存，6、8、10返回的结果）。
12. Local DNS 服务器同时将结果返回给本机域名解析器。
13. 本机缓存解析结果。
14. 本机解析器将结果返回给浏览器。
15. 浏览器通过返回的 IP 地址发起请求。

**递归查询和迭代查询**

- 递归查询：如果主机所询问的本地域名服务器不知道被查询域名的 IP 地址，那么本地域名服务器就以 DNS 客户的身份，向其他根域名服务器继续发出查询请求报文，而不是让该主机自己进行下一步的查询。
- 迭代查询：当根域名服务器收到本地域名服务器发出的迭代查询请求报文时，要么给出所要查询的 IP 地址，要么告诉本地域名服务器：你下一步应当向哪一个域名服务器进行查询。然后让本地域名服务器进行后续的查询，而不是替本地域名服务器进行后续的查询。

由此可见，客户端到 Local DNS 服务器，Local DNS 与上级 DNS 服务器之间属于递归查询；DNS 服务器与根 DNS 服务器之前属于迭代查询。

实际环境中，因为采用递归模式会导致 DNS 服务器流量很大，所以现在大多数的 DNS 都是迭代模式。

**结合 Wireshark 分析 DNS 协议**

![img](http://misc.linkedkeeper.com/misc/img/blog/201707/linkedkeeper0_6f5bb2bd-acc9-42f3-88b6-6162d3e29044.jpg)

**HttpDns 是什么**

HTTPDNS 利用 HTTP 协议与 DNS 服务器交互，代替了传统的基于 UDP 协议的 DNS 交互，绕开了运营商的 Local DNS，有效防止了域名劫持，提高域名解析效率。另外，由于 DNS 服务器端获取的是真实客户端 IP 而非 Local DNS 的 IP，能够精确定位客户端地理位置、运营商信息，从而有效改进调度精确性。

![img](http://misc.linkedkeeper.com/misc/img/blog/201707/linkedkeeper0_430832a1-fa08-4475-9eff-f19c4c1e99eb.jpg)

**HttpDns 主要解决的问题**

- Local DNS 劫持：由于 HttpDns 是**通过 IP 直接请求 HTTP 获取服务器 A 记录地址**，不存在向本地运营商询问 domain 解析过程，所以从根本**避免了劫持问题**。
- 平均访问延迟下降：由于是 IP 直接访问省掉了一次 domain 解析过程，通过智能算法排序后找到最快节点进行访问。
- 用户连接失败率下降：通过算法**降低以往失败率过高的服务器排序**，通过**时间近期访问过的**数据**提高服务器排序，通过历史访问成功记录提高服务器排序**。

**如何进行改造支持 HttpDns**

目前，国内有一部分厂商已经提供了这个解析服务，可以直接使用第三方服务。目前，提供 HttpDns 解析服务的有：阿里云HttpDNS

阿里云的 HttpDNS 服务的 API 比较标准，直接发一个 Get 请求，带上请求参数，返回结果以 json 返回。

请求成功时，返回结果如下：

在移动端，将由 HttpDns 获得的 IP 地址在原有 URL 的基础上，将域名替换为 IP，然后用新的 URL 发起 HTTP 请求。