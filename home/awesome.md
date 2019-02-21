# Awesome SOFA

```plain
                       .---                                                                                                   ---.
                     /  ..-          ___   ____    __    ____  _______     _______.  ______   .___  ___.  _______             -.. \
                    _|_|_           /   \  \   \  /  \  /   / |   ____|   /       | /  __  \  |   \/   | |   ____|              _|_|_
                  /    O  \        /  ^  \  \   \/    \/   /  |  |__     |   (----`|  |  |  | |  \  /  | |  |__               /  O    \
                  \_______/       /  /_\  \  \            /   |   __|     \   \    |  |  |  | |  |\/|  | |   __|              \_______/
                   /   \         /  _____  \  \    /\    /    |  |____.----)   |   |  `--'  | |  |  |  | |  |____                /   \
                  /   \/        /__/     \__\  \__/  \__/     |_______|_______/     \______/  |__|  |__| |_______|               \/   \
                 /'---'\                                                                                                         /'---'\
           _____/    |  \____                                                                                               ____/  |    \_____
        _/      \____/\__     _______.  ______    _______    ___           _______.___________.    ___       ______  __  ___    __/\____/      \_
      /           |          /       | /  __  \  |   ____|  /   \         /       |           |   /   \     /      ||  |/  /         |            \
    _'      /\  __/ \       |   (----`|  |  |  | |  |__    /  ^  \       |   (----`---|  |----`  /  ^  \   |  ,----'|  '  /         / \__  /\      '_
  /        / __/     \       \   \    |  |  |  | |   __|  /  /_\  \       \   \       |  |      /  /_\  \  |  |     |    <         /     \__ \        \
/_________/_/        /   .----)   |   |  `--'  | |  |    /  _____  \  .----)   |      |  |     /  _____  \ |  `----.|  .  \        \        \_\_________\
   /     /          /    |_______/     \______/  |__|   /__/     \__\ |_______/       |__|    /__/     \__\ \______||__|\__\        \          \     \
        /          /                                                                                                                 \          \
```

## 目录

- [资源 Resources](#resources)
- [项目 Projects](#projects)
- [文章 Articles](#articles)
- [工具 Tools](#tools)
- [社区 Community](#community)
- [贡献 Contributors](#contributors)
- [用户 End User](#enduser)

<span id="resources" />

## 资源

- [开源官网](https://www.sofastack.tech)
- [Github源码](https://github.com/alipay?q=sofa)
- [蚂蚁金融科技](https://tech.antfin.com/sofa)

<span id="projects" />

## 项目 

已开源项目：

- [SOFABoot](https://github.com/alipay/sofa-boot) 是基于 Spring Boot 的一套研发框架，完全兼容 Spring Boot 的基础上，还提供了启动期监控检查，上下文隔离，模块化开发，类隔离，日志空间隔离等等能力。
- [SOFARPC](https://github.com/alipay/sofa-rpc) 是一个高可扩展性、高性能、生产级的 Java RPC 框架。
- [SOFAMesh](https://github.com/alipay/sofa-mesh) 是基于 Istio 改进和扩展而来的 Service Mesh 大规模落地实践方案。
- [SOFAMosn](https://github.com/alipay/sofa-mosn) 是 Golang 开发的 Service Mesh 数据平面代理，旨在提供分布式，模块化，可观察，智能化的代理能力。
- [SOFATracer](https://github.com/alipay/sofa-tracer) 是基于 OpenTracing 规范 的分布式链路跟踪系统。
- [SOFALookout](https://github.com/alipay/sofa-lookout) 是一款解决系统的度量和监控问题的轻量级中间件服务。
- [SOFABolt](https://github.com/alipay/sofa-bolt) 是基于 Netty 实现的网络通信框架。
- [SOFAArk](https://github.com/alipay/sofa-ark) 是一款基于 Java 实现的轻量级类隔离容器。
- [SOFAJarslink](https://github.com/alipay/sofa-jarslink) 是基于SOFAArk的动态模块和合并部署的解决方案。

更多已项目直接关注：[github/alipay](https://github.com/alipay?q=sofa)

未开源项目可通过[SOFAStack 使用调查问卷](https://wj.qq.com/s/2698623/5fe1)反馈。包括但不限于注册中心、消息、配置中心、熔断限流、分库分表、分布式事务等等组件。

<span id="articles" />

## 文章

- [SOFAStack 官网文章](https://www.sofastack.tech/posts)
- [SOFAStack 知乎专栏](https://zhuanlan.zhihu.com/sofastack)
- [SOFA 公开文章](https://www.yuque.com/huarou/gd4szw)

<span id="tools" />

## 工具

<span id="community" />

## 社区

- [Github](https://github.com/alipay/)
- <SOFA:Lab /> SOFA源码研究实验室：加入我们，回到写代码最初的样子
  - [SOFARPCLab](https://www.yuque.com/sofarpclab)
  - [SOFABoltLab](https://www.yuque.com/sofaboltlab)
  - [SOFAMosnLab](https://www.yuque.com/sofamosn)
- 微信
  - 公众号：金融级分布式架构（Antfin_SOFA）：致力于打造一流的分布式技术在金融场景应用实践的技术交流平台，专注于交流金融科技行业内最前沿、可供参考的技术方案与实施路线。
  - 微信群：目前九个群，四千多一线开发用户交流群体，加`蚂蚁金服科技小助手(ant-techfin02)`为好友，回复 `SOFA` 入群。
- 钉钉
  - 钉钉交流群：金融级分布式架构 SOFA，群号：23127468
  - SOFA 公司用户群：公司用户实名认证，SOFA开发团队直接提供支持。 准备中。
- [新浪微博](https://weibo.com/sofastack)

- [SegmentFault](https://segmentfault.com/t/sofa)
- [掘金](https://juejin.im/user/5a42596ff265da43062b06e8)

<span id="contributors" />

## 贡献

目前已经几十位小伙伴为 SOFA 贡献了文章和代码，他们分别是：
> 本列表线上的 Github 用户 ID，按首字母排序，每月初更新

| -- | -- | -- | -- | -- |
| -- | -- | -- | -- | -- |
| ![315157973](https://avatars2.githubusercontent.com/u/9758905?v=4&s=30) @315157973 | ![boliza](https://avatars3.githubusercontent.com/u/1076043?v=4&s=30) @boliza | ![caojie09](https://avatars2.githubusercontent.com/u/38058846?v=4&s=30) @caojie09 | ![chenhui0212](https://avatars3.githubusercontent.com/u/6693957?v=4&s=30) @chenhui0212 | ![choleraehyq](https://avatars3.githubusercontent.com/u/8923413?v=4&s=30) @choleraehyq |
| ![coolme200](https://avatars0.githubusercontent.com/u/1400114?v=4&s=30) @coolme200 | ![cp3fantasy](https://avatars2.githubusercontent.com/u/5997617?v=4&s=30) @cp3fantasy | ![cytnju](https://avatars2.githubusercontent.com/u/23069836?v=4&s=30) @cytnju | ![dawxy](https://avatars1.githubusercontent.com/u/11092308?v=4&s=30) @dawxy | ![dbl-x](https://avatars3.githubusercontent.com/u/3138469?v=4&s=30) @dbl-x |
| ![detailyang](https://avatars3.githubusercontent.com/u/3370345?v=4&s=30) @detailyang | ![easonzhang1992](https://avatars1.githubusercontent.com/u/25562228?v=4&s=30) @easonzhang1992 | ![elseifer](https://avatars3.githubusercontent.com/u/16420699?v=4&s=30) @elseifer | ![eonezhang](https://avatars3.githubusercontent.com/u/1627260?v=4&s=30) @eonezhang | ![fleeto](https://avatars3.githubusercontent.com/u/6958477?v=4&s=30) @fleeto |
| ![FzNl](https://avatars1.githubusercontent.com/u/8958571?v=4&s=30) @FzNl | ![glmapper](https://avatars1.githubusercontent.com/u/35319286?v=4&s=30) @glmapper | ![guanchao-yang](https://avatars3.githubusercontent.com/u/32863418?v=4&s=30) @guanchao-yang | ![gxcsoccer](https://avatars2.githubusercontent.com/u/1207064?v=4&s=30) @gxcsoccer | ![hqq2023623](https://avatars2.githubusercontent.com/u/6971152?v=4&s=30) @hqq2023623 |
| ![huangyunbin](https://avatars2.githubusercontent.com/u/1046037?v=4&s=30) @huangyunbin | ![ITGrocery](https://avatars3.githubusercontent.com/u/10252344?v=4&s=30) @ITGrocery | ![JamazRuan](https://avatars1.githubusercontent.com/u/44109623?v=4&s=30) @JamazRuan | ![jaredleechn](https://avatars3.githubusercontent.com/u/5318333?v=4&s=30) @jaredleechn | ![JarVZhao](https://avatars1.githubusercontent.com/u/39182231?v=4&s=30) @JarVZhao |
| ![JervyShi](https://avatars0.githubusercontent.com/u/1862729?v=4&s=30) @JervyShi | ![jewin](https://avatars1.githubusercontent.com/u/5440811?v=4&s=30) @jewin | ![jiangyunpeng](https://avatars3.githubusercontent.com/u/1752994?v=4&s=30) @jiangyunpeng | ![jjtyro](https://avatars1.githubusercontent.com/u/1267286?v=4&s=30) @jjtyro | ![JoeKerouac](https://avatars1.githubusercontent.com/u/18147095?v=4&s=30) @JoeKerouac |
| ![junxiong](https://avatars3.githubusercontent.com/u/721533?v=4&s=30) @junxiong | ![kelvinji2009](https://avatars0.githubusercontent.com/u/881201?v=4&s=30) @kelvinji2009 | ![kevin21th](https://avatars2.githubusercontent.com/u/41314762?v=4&s=30) @kevin21th | ![khotyn](https://avatars1.githubusercontent.com/u/584455?v=4&s=30) @khotyn | ![killagu](https://avatars1.githubusercontent.com/u/6897780?v=4&s=30) @killagu |
| ![kiral](https://avatars1.githubusercontent.com/u/3823960?v=4&s=30) @kiral | ![kswapd](https://avatars2.githubusercontent.com/u/6203557?v=4&s=30) @kswapd | ![lazyrabbit2004](https://avatars0.githubusercontent.com/u/42604021?v=4&s=30) @lazyrabbit2004 | ![ldxdl](https://avatars2.githubusercontent.com/u/40663988?v=4&s=30) @ldxdl | ![leizhiyuan](https://avatars3.githubusercontent.com/u/2684384?v=4&s=30) @leizhiyuan |
| ![leoner](https://avatars1.githubusercontent.com/u/546535?v=4&s=30) @leoner | ![leyou240](https://avatars2.githubusercontent.com/u/9214525?v=4&s=30) @leyou240 | ![liangyuanpeng](https://avatars1.githubusercontent.com/u/28711504?v=4&s=30) @liangyuanpeng | ![lichuang](https://avatars0.githubusercontent.com/u/1998569?v=4&s=30) @lichuang | ![Lingtaonju](https://avatars2.githubusercontent.com/u/19946268?v=4&s=30) @Lingtaonju |
| ![liymg](https://avatars0.githubusercontent.com/u/36815383?v=4&s=30) @liymg | ![lonng](https://avatars3.githubusercontent.com/u/1638682?v=4&s=30) @lonng | ![lookuptable](https://avatars1.githubusercontent.com/u/5387584?v=4&s=30) @lookuptable | ![luyiisme](https://avatars0.githubusercontent.com/u/15273963?v=4&s=30) @luyiisme | ![meua](https://avatars3.githubusercontent.com/u/11570442?v=4&s=30) @meua |
| ![michalyao](https://avatars0.githubusercontent.com/u/17451490?v=4&s=30) @michalyao | ![minli04g](https://avatars3.githubusercontent.com/u/18209024?v=4&s=30) @minli04g | ![moeyui1](https://avatars0.githubusercontent.com/u/11503525?v=4&s=30) @moeyui1 | ![Moriadry](https://avatars0.githubusercontent.com/u/6925549?v=4&s=30) @Moriadry | ![NeGnail](https://avatars2.githubusercontent.com/u/16985636?v=4&s=30) @NeGnail |
| ![nejisama](https://avatars0.githubusercontent.com/u/16449274?v=4&s=30) @nejisama | ![neverhook](https://avatars0.githubusercontent.com/u/41352674?v=4&s=30) @neverhook | ![OrezzerO](https://avatars2.githubusercontent.com/u/13081689?v=4&s=30) @OrezzerO | ![popomore](https://avatars1.githubusercontent.com/u/360661?v=4&s=30) @popomore | ![pxzero](https://avatars2.githubusercontent.com/u/7530125?v=4&s=30) @pxzero |
| ![QilongZhang](https://avatars3.githubusercontent.com/u/8833026?v=4&s=30) @QilongZhang | ![quaff](https://avatars0.githubusercontent.com/u/143040?v=4&s=30) @quaff | ![rootsongjc](https://avatars3.githubusercontent.com/u/3328185?v=4&s=30) @rootsongjc | ![ScienJus](https://avatars3.githubusercontent.com/u/8350653?v=4&s=30) @ScienJus | ![sevennt](https://avatars0.githubusercontent.com/u/10843736?v=4&s=30) @sevennt |
| ![shaoshuai0102](https://avatars0.githubusercontent.com/u/456108?v=4&s=30) @shaoshuai0102 | ![silentred](https://avatars0.githubusercontent.com/u/3345293?v=4&s=30) @silentred | ![skyao](https://avatars3.githubusercontent.com/u/1582369?v=4&s=30) @skyao | ![stdupp](https://avatars3.githubusercontent.com/u/6189788?v=4&s=30) @stdupp | ![SteNicholas](https://avatars2.githubusercontent.com/u/10048174?v=4&s=30) @SteNicholas |
| ![straybirdzls](https://avatars3.githubusercontent.com/u/7148759?v=4&s=30) @straybirdzls | ![taoyuanyuan](https://avatars3.githubusercontent.com/u/2707372?v=4&s=30) @taoyuanyuan | ![tinycedar](https://avatars0.githubusercontent.com/u/8019222?v=4&s=30) @tinycedar | ![tonyhawkwen](https://avatars2.githubusercontent.com/u/4549311?v=4&s=30) @tonyhawkwen | ![TreeLin](https://avatars0.githubusercontent.com/u/2898747?v=4&s=30) @TreeLin |
| ![ujjboy](https://avatars1.githubusercontent.com/u/1424920?v=4&s=30) @ujjboy | ![wanderxjtu](https://avatars2.githubusercontent.com/u/621135?v=4&s=30) @wanderxjtu | ![wudidapaopao](https://avatars3.githubusercontent.com/u/13719073?v=4&s=30) @wudidapaopao | ![XadillaX](https://avatars3.githubusercontent.com/u/2842176?v=4&s=30) @XadillaX | ![xiaoguoqiang](https://avatars0.githubusercontent.com/u/29472210?v=4&s=30) @xiaoguoqiang |
| ![xmtsui](https://avatars0.githubusercontent.com/u/1542690?v=4&s=30) @xmtsui | ![xzchaoo](https://avatars0.githubusercontent.com/u/8319940?v=4&s=30) @xzchaoo | ![yangl](https://avatars3.githubusercontent.com/u/231353?v=4&s=30) @yangl | ![zhaojigang](https://avatars1.githubusercontent.com/u/14085645?v=4&s=30) @zhaojigang | ![zhenhua](https://avatars0.githubusercontent.com/u/1314275?v=4&s=30) @zhenhua |
| ![zorkeAccount](https://avatars3.githubusercontent.com/u/19190735?v=4&s=30) @zorkeAccount | ![zyjibmcn](https://avatars0.githubusercontent.com/u/4906448?v=4&s=30) @zyjibmcn | ![ZZMarquis](https://avatars1.githubusercontent.com/u/7624583?v=4&s=30) @ZZMarquis 


想要参与贡献，可以直接去[项目列表](#projects)找到您关注的项目。

<span id="enduser" />

## 用户

此处会列出使用 SOFAStack 开源版的公司或组织，大家可以通过 [SOFAStack 使用者登记](https://github.com/alipay/sofastack-doc/issues/42) 进行登记，或者可以通过 [SOFAStack 使用调查问卷](https://wj.qq.com/s/2698623/5fe1) 进行反馈。

排名不分先后

- 杭州米雅信息科技
- 申通快递
- 深圳市大头兄弟传媒有限公司
- 译筑科技
- 运满满
- 烽火科技
- 挖财
- OPPO 金融
- 拍拍贷