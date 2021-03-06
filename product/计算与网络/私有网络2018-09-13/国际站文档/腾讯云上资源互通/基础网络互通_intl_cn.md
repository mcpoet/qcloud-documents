## 简介
基础网络互通（Classiclink）是指将基础网络内的云服务器关联至指定私有网络，使基础网络中的云服务器可以与私有网络内的云服务器、数据库等云服务通信。默认情况下，私有网络是完全隔离的网络空间，不管是其他私有网络还是基础网络都不能通过内网与其互通。[对等连接](https://cloud.tencent.com/doc/product/215/5000)解决了不同私有网络之间互相通信的问题，而基础网络与某个私有网络通信问题则可以通过基础网络互通解决。如下图所示，基础网络云服务器可以访问私有网络内的云服务器、云数据库、内网负载均衡、云缓存等云资源，而私有网络内的云服务器仅可访问互通的基础网络云服务器，无法访问基础网络内其他计算资源。此功能限制在同地域内连通，如下图所示。
![](//mccdn.qcloud.com/static/img/ec304e11bfe74202087af2ecdc043397/image.png)

## 路由、安全组与网络ACL对基础网络互通主机的影响
- 关联后的基础网络云服务器内网 IP 会自动添加至私有网络内路由表 Local 策略中，您无需手动修改当前私有网络内的路由表规则，私有网络内的云主机和该基础网络云服务即可以实现互访。 
- 基础网络内云服务器与私有网络关联后，各自的安全防火墙与网络 ACL 仍生效。即私有网络子网可以通过设定网络 ACL 限制基础网络内关联云服务器的访问，基础网络和私有网络的云服务器也均可以设定安全组规则限制双向的网络访问。

## 使用约束
- 仅支持基础网络和私有网络互相通信，不支持云服务器切换所属网络环境。云服务器一旦选择了网络环境（私有网络或基础网络）后便不可变更。
- 一个基础网络云服务器同一时间只可以关联一个私有网络。
- 私有网络与基础网络互通暂时只支持同地域下。
- 基础网络互通功能仅支持 `10.[0~47].0.0/16` 网段内的私有网络，其他网段的私有网络 IP 范围可能与基础网络 IP 段冲突。
- 基础网络互通中云服务器的流量只能路由至私有网络中的内网 IP 地址，无法路由至私有网络以外的其他目标。即基础网络云服务器不可以经由本私有网络的 VPN 网关、专线网关、公网网关、对等连接、NAT网关等网络设备访问本私有网络外的公网或私网资源。同样VPN、专线、对等连接等的对端也无法访问本基础网络云服务器。
- 私有网络内的负载均衡实例不能绑定与本私有网络互通的基础网络云主机。
- 基础网络云主机内网 IP 的变更将导致私有网络关联失效，即原记录将失效。如果需要关联请重新在私有网络控制台进行添加。
- 云服务器欠费隔离、安全隔离、冷迁移、故障迁移、修改配置、切换操作系统等操作均不解绑私有网络互通关系。
- 云服务器退还后将自动解绑与私有网络的互通关系。

| 资源 | 限制 |说明|
|---------|---------| ---|
| 每个私有网络支持关联基础网络主机的个数 | 100 | 　　|
| 支持网段| 仅支持网段为 `10.[0~47].0.0/16（含子集）`的私有网络 |原因：防止基础网络里主机IP与私有网络内的IP冲突|
| 支持云资源| 云主机（CVM） |不支持访问基础网络CDB、CMEM、LB等资源|

## 计费模式
基础网络互通功能免费。有关 VPC 的其他服务价格，可以查看[腾讯云私有网络费用总览](https://cloud.tencent.com/doc/product/215/3079)。

## 操作指南
### 将基础网络云服务器关联至私有网络
示例：
通过基础网络互通使云服务器TomCVM可以与私有网络TomVPC通信，您需要完成以下步骤:
1)	登录[腾讯云控制台](https://console.cloud.tencent.com/)点击导航条【私有网络】，进入[私有网络控制台](https://console.cloud.tencent.com/vpc/vpc?rid=8)。
2)	选择**地域：北京**，点击需要与基础网络互通的 VPC`TomVPC`，进入详情页。
3)	点击【基础网络互通】选项卡，点击【关联云主机】按钮。 
4)	在弹窗页选择基础网络内需要关联至此私有网路的云服务器:`TomCVM`。
5)	点击【确定】按钮完成操作，关联关系确认后立即生效。

### 查看与基础网络互通云服务器
1)	登录[腾讯云控制台](https://console.cloud.tencent.com/)点击导航条【私有网络】，进入[私有网络控制台](https://console.cloud.tencent.com/vpc/vpc?rid=8)。
2)	选择地域，点击需要与基础网络互通的 VPC ID，进入详情页。
3)	点击【基础网络互通】选项卡，即可查看与该私有网络关联的基础网络云服务器列表。


### 解除私有网络与基础网络内云服务器关联
1)	登录[腾讯云控制台](https://console.cloud.tencent.com/)点击导航条【私有网络】，进入[私有网络控制台](https://console.cloud.tencent.com/vpc/vpc?rid=8)。
2)	点击需要与基础网络互通的 VPC ID，进入私有网络详情页。
3)	点击【基础网络互通】，在基础网络云服务器列表中选择需要解关联的云主机，并点击【解除关联】按钮。
4)	点击【确认】按钮即可完成解关联操作。

## API概览
您可以使用API操作来设置和管理您的私有网络与基础网络互通，有关 VPC API 的更多服务内容可以参考 [VPC 所有 API 概览](https://cloud.tencent.com/doc/api/245/909)。

| 接口功能 | Action ID | 功能描述 |
|---------|---------|---------|
| 创建私有网络和基础网络设备互通 | [AttachClassicLinkVpc](https://cloud.tencent.com/doc/api/245/2098) | 创建私有网络和基础网络设备互通。 |
| 删除私有网络和基础网络设备互通 | [DetachClassicLinkVpc](https://cloud.tencent.com/doc/api/245/2097) | 删除私有网络和基础网络设备互通。 |
| 查询私有网络和基础网络设备互通 | [DescribeVpcClassicLink](https://cloud.tencent.com/doc/api/245/2097) | 查询私有网络和基础网络设备互通。 |



