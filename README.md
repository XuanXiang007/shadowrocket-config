# Shadowrocket Config

个人使用的 Shadowrocket 主配置，采用英文名称、中文说明；注释按功能分段，逐项解释配置字段。

## 文件

| 名称 | 类型 | 用途 | 查看 | 订阅 |
| --- | --- | --- | --- | --- |
| **Bypass China** | 主配置 | 中国大陆、局域网及 Apple 资源直连，其余按规则代理 | [配置文件](./Bypass-China.ini) | [Raw](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/Bypass-China.ini) |

## Bypass China

### 运行逻辑

按配置顺序匹配：局域网和 Apple 直连 → Google 代理 → 中国大陆域名与 IP 直连 → 其余流量走 `PROXY`。规则集来自 [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)，外部规则内容可随上游变动。

### General 字段

| 字段 | 作用 |
| --- | --- |
| `block-quic = all-proxy` | 阻断走代理路径的 QUIC，促使适用连接回落到 TCP；不是全局阻断所有 UDP。 |
| `ipv6 = false` | 禁用 IPv6。 |
| `prefer-ipv6 = false` | 不优先选择 IPv6；与禁用 IPv6 的方向一致。 |
| `udp-policy-not-supported-behaviour = REJECT` | 节点不支持 UDP 时拒绝对应请求，避免隐式回退。 |
| `dns-server` | 默认解析服务；Cloudflare DoH，`#proxy` 表示经代理访问。 |
| `direct-dns-server` | 直连流量的解析服务；当前为腾讯 DNS `119.29.29.29`。 |
| `proxy-dns-server` | 代理节点主机名的解析服务；当前也使用 `119.29.29.29`。 |
| `fallback-dns-server` | 默认 DNS 不可用时的备用服务；Google DoH，经代理访问。 |
| `dns-fallback-system = false` | 关闭向系统 DNS 的备用回退。 |
| `dns-direct-system = false` | 直连域名不采用系统 DNS。 |
| `dns-direct-fallback-proxy = false` | 直连解析失败时不改用代理解析。 |
| `hijack-dns` | 接管发往列出的 Google/Cloudflare 公共 DNS 地址的 53 端口查询；并非拦截一切 DNS 协议。 |
| `update-url` | Shadowrocket 主配置更新地址，已改为新的 `Bypass-China.ini` 路径。 |

### Rule 字段

| 规则 | 作用 |
| --- | --- |
| `RULE-SET / Lan` | 局域网规则集，`DIRECT` 直连。 |
| `DOMAIN-SET / Apple` | Apple 域名清单，`DIRECT` 直连。 |
| `RULE-SET / Apple` | Apple 补充规则集，`DIRECT` 直连。 |
| `RULE-SET / Google` | Google 规则集，交给 `PROXY`。 |
| `DOMAIN-SET / ChinaMaxNoIP` | 中国大陆域名清单，`DIRECT` 直连。 |
| `RULE-SET / ChinaMaxNoIP` | 中国大陆补充规则集，`DIRECT` 直连。 |
| `GEOIP,CN,DIRECT,no-resolve` | 已知目标 IP 属于中国大陆时直连；`no-resolve` 不为该规则单独解析域名。 |
| `FINAL,PROXY` | 最后兜底；未命中的流量代理。 |

`DOMAIN-SET` 用于域名清单，`RULE-SET` 用于规则清单；`DIRECT` 表示直连，`PROXY` 由 Shadowrocket 当前代理策略负责。先匹配的规则优先执行。

### URL Rewrite 与 MITM

主配置新增 Google 中国域名重定向：`g.cn` 和 `google.cn`（含 `www`）的 HTTP(S) URL 按 302 跳转到 `https://www.google.com`，保留原路径及查询参数。

`[MITM]` 仅列出这四个域名。HTTPS URL Rewrite 需要在 Shadowrocket 中启用 HTTPS 解密，并安装、信任其 CA 证书；**仅有主配置并不能保证 HTTPS 重定向生效**。这些主机名以外的 HTTPS 域名不因这项配置而被加入解密列表。
