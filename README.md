# Shadowrocket 配置

个人自用的 Shadowrocket 主配置。

## 主配置 · 白名单

[查看](./whitelist.ini) · [订阅](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/whitelist.ini)

- **分流：** 局域网、Apple、中国域名及 IP 直连；Google 和其余流量代理。
- **DNS：** 代理 Cloudflare/Google DoH，直连腾讯 DNS；禁用系统 DNS 回退。
- **网络：** 阻断代理 QUIC、禁用 IPv6，UDP 不支持时拒绝。
