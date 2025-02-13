<div align="center">

<img src="asset/logo.png" height="200px" alt="NextTrace Logo"/>

</div>

## NextTrace Lite

一款追求轻量的开源可视化路由跟踪工具，使用 Golang 开发。

NextTrace 一共有2个版本，专注于轻量的 Lite 版本以及更面向发烧友的 [Enhanced 版本](#nexttrace-enhanced)。

PS: Lite 版本追求轻量化，并不提供基于高德地图 / OpenStreetMap 的路由可视化功能，如有需要，请使用 Enhanced 版本。

## How To Use

### Automated Install

```bash
# Linux 一键安装脚本
bash <(curl -Ls https://raw.githubusercontent.com/xgadget-lab/nexttrace/main/nt_install.sh)

# GHPROXY 镜像（国内使用）
bash <(curl -Ls https://ghproxy.com/https://raw.githubusercontent.com/xgadget-lab/nexttrace/main/nt_install.sh)

# macOS brew 安装命令
brew tap xgadget-lab/nexttrace && brew install nexttrace
```

- `Release`里面为很多系统以及不同架构提供了编译好的二进制可执行文件，如果没有可以自行编译。
- 一些本项目的必要依赖在`Windows`上`Golang`底层实现不完全，所以目前`NextTrace`在`Windows`平台不可用。

### Get Started

`NextTrace` 默认使用`ICMP`协议发起`TraceRoute`请求，该协议同时支持`IPv4`和`IPv6`

```bash
# IPv4 ICMP Trace
nexttrace 1.0.0.1

# 表格打印（一次性输出全部跳数，需等待20-40秒）
nexttrace -table 1.0.0.1

# IPv6 ICMP Trace
nexttrace 2606:4700:4700::1111
```

`NextTrace` 现已经支持快速测试，有一次性测试回程路由需求的朋友可以使用

```bash
# 北上广（电信+联通+移动+教育网）IPv4 ICMP 快速测试
nexttrace -f

# 也可以使用 TCP SYN 而非 ICMP 进行测试
nexttrace -f -T
```

`NextTrace` 已支持指定网卡进行路由跟踪

```bash
# 请注意 Lite 版本此参数不能和快速测试联用，如有需要请使用 enhanced 版本
# 使用 eth0 网卡
nexttrace -D eth0 2606:4700:4700::1111

# 使用 eth0 网卡IP
# 网卡 IP 可以使用 ip a 或者 ifconfig 获取
# 使用网卡IP进行路由跟踪时需要注意跟踪的IP类型应该和网卡IP类型一致（如都为 IPv4）
nexttrace -S 204.98.134.56 9.9.9.9
```

`NextTrace` 也可以使用`TCP`和`UDP`协议发起`Traceroute`请求，不过目前只支持`IPv4`

```bash
# TCP SYN Trace
nexttrace -T www.bing.com

# 可以自行指定端口[此处为443]，默认80端口
nexttrace -T -p 443 1.0.0.1

# UDP Trace
nexttrace -U 1.0.0.1

nexttrace -U -p 53 1.0.0.1
```

`NextTrace`也同样支持一些进阶功能，如 TTL 控制、并发数控制、模式切换等

```bash
# 每一跳发送2个探测包
nexttrace -q 2 www.hkix.net

# 无并发，每次只发送一个探测包
nexttrace -r 1 www.hkix.net

# 从TTL为5开始发送探测包，直到TTL为10结束
nexttrace -b 5 -m 10 www.decix.net

# 关闭IP反向解析功能
nexttrace -n www.bbix.net

# 特色功能：打印Route-Path图
# Route-Path图示例：
# AS6453 塔塔通信「Singapore『Singapore』」
#  ╭╯
#  ╰AS9299 Philippine Long Distance Telephone Co.「Philippines『Metro Manila』」
#  ╭╯
#  ╰AS36776 Five9 Inc.「Philippines『Metro Manila』」
#  ╭╯
#  ╰AS37963 阿里云「ALIDNS.COM『ALIDNS.COM』」
nexttrace -report www.time.com.my
```

`NextTrace`支持用户自主选择 IP 数据库（目前支持：`LeoMoeAPI`, `IP.SB`, `IPInfo`, `IPInsight`, `IPAPI.com`）

```bash
# 可以自行指定IP数据库[此处为IP.SB]，不指定则默认为LeoMoeAPI
nexttrace -d IP.SB
## 特别的：其中 ipinfo API 需要从ipinfo自行购买服务，如有需要可以clone本项目添加其提供的token自行编译
##        TOKEN填写路径：ipgeo/tokens.go
## 另外：由于IP.SB被滥用比较严重，会经常出现无法查询的问题，请知悉。
##      IPAPI.com限制调用较为严格，如有查询不到的情况，请几分钟后再试。
```

`NextTrace`支持参数混合使用

```bash
Example:
nexttrace -d IPInsight -m 20 -p 443 -q 5 -r 20 -rdns 1.1.1.1
nexttrace -T -q 2 -r 1 -table -report 2001:4860:4860::8888
```

### IP 数据库

✨NextTrace `LeoMoeAPI` 的后端也开源啦

[GitHub - sjlleo/nexttrace-backend: NextTrace BackEnd](https://github.com/sjlleo/nexttrace-backend)

NextTrace 所有的的 IP 地理位置`API DEMO`可以参考[这里](https://github.com/xgadget-lab/nexttrace/blob/main/ipgeo/)

### 全部用法详见 Usage 菜单

```shell
Usage of nexttrace:
      'nexttrace [options] <hostname>' or 'nexttrace <hostname> [option...]'
Options:
  -D string
        Use the following Network Devices as the source address in outgoing packets
  -S string
        Use the following IP address as the source address in outgoing packets
  -T    Use TCP SYN for tracerouting (default port is 80)
  -U    Use UDP Package for tracerouting (default port is 53 in UDP)
  -V    Print Version
  -b int
        Set The Begin TTL (default 1)
  -d string
        Choose IP Geograph Data Provider [LeoMoeAPI, IP.SB, IPInfo, IPInsight, IPAPI.com] (default "LeoMoeAPI")
  -f    One-Key Fast Traceroute
  -m int
        Set the max number of hops (max TTL to be reached). (default 30)
  -n    Disable IP Reverse DNS lookup
  -p int
        Set SYN Traceroute Port (default 80)
  -q int
        Set the number of probes per each hop. (default 3)
  -r int
        Set ParallelRequests number. It should be 1 when there is a multi-routing. (default 18)
  -report
        Route Path
  -table
        Output trace results as table
```

## 项目截图

![NextTrace Screenshot](asset/nexttrace021.png)

## NextTrace Enhanced

`NextTrace Enhanced` 是面向发烧友的增强版，`Enhanced`提供Web API形式的路由跟踪调用，以及一个简单的自带可视化的Looking Glass网页。

`Enhanced` 版本支持很多`lite`版本没有的功能，如能够自定义设置超时时间，也能指定TTL作为起点进行路由跟踪等，对于普通用户来说，通常`lite`版本已经足够完成大部分需要。

https://github.com/OwO-Network/nexttrace-enhanced

## 捐助我们

为了能够尽可能提供精准的 IP 地理位置，项目组选择了自建 API (LeoMoeAPI) 并购买了多家 IP 地理位置的数据源 API，同时也修正的大量的骨干网 IP 地理位置错误，这需要耗费大量的时间和金钱。

您可以选择捐助我们，以支持我们的持续发展，在此先表示感谢了。

爱发电：https://afdian.net/@sjlleo

## FAQ 常见问题

如果你在安装或者使用的时候遇到了问题，我们建议你不要把新建一个 `issue` 作为首选项

以下是我们推荐的排错流程：

1. 查看是否为常见问题 -> [前往 Github Wiki](https://github.com/xgadget-lab/nexttrace/wiki/FAQ---%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98%E8%A7%A3%E7%AD%94)
2. 疑似 BUG、或者功能建议 -> [前往 Github Issues](https://github.com/xgadget-lab/nexttrace/issues)

## Thanks

[Vincent Young](https://github.com/missuo) (i@yyt.moe)

[Sam Sam](https://github.com/samleong123) (samsam123@samsam123.name.my)

[tsosunchia](https://github.com/tsosunchia)

[waiting4new](https://github.com/waiting4new)

[FFEE_CO](https://github.com/fkx4-p)

## IP Database Copyright

### IPv4 Database

#### China

| ISP      | 类型  | 数据源   | 占比  |
|:--------:|:---:|:-----:|:---:|
| 电信/联通/移动 | 骨干网 | 网络爱好者 | 10% |
| 电信/联通/移动 | 城域网 | 埃文科技  | 90% |

- 参与骨干网维护的朋友都是网络爱好者群体，尽管我们多名志愿者通过自己的网络进行了大量的勘测，但是由于信息不足，依旧存在很多错误。
- 对于更高精度的朋友，我们依旧强烈推荐IPIP.NET，他们开发的Besttrace是目前质量最好的路由可视化软件，我们多数爱好者能有今天这样的骨干网初步认知都是归功于他们，在此特表感谢。

#### WorldWide

##### Tier 01

| ISP     | 类型  | 数据源       | 占比  |
|:-------:|:---:|:---------:|:---:|
| Tier-01 | 骨干网 | IPInfo    | 2%  |
| Tier-01 | 骨干网 | 埃文科技      | 3%  |
| Tier-01 | 骨干网 | IPInSight | 5%  |
| Tier-01 | 城域网 | IPInSight | 90% |

##### Other ISP

| ISP    | 类型  | 数据源       | 占比  |
|:------:|:---:|:---------:|:---:|
| Others | 骨干网 | IPInSight | 5%  |
| Others | 城域网 | IPInSight | 95% |

### IPv6 Database

| ISP | 类型  | 数据源              | 占比   |
|:---:|:---:|:----------------:|:----:|
| All | 全部  | IP2Location Lite | 100% |

This product includes IP2Location LITE data available from <a href="https://lite.ip2location.com">https://lite.ip2location.com</a>.

### Others

其他第三方 API 尽管集成在本项目内，但是具体的 TOS 以及 AUP，请详见第三方 API 官网。如遇到 IP 数据错误，也请直接联系他们纠错。
