# Shadowrocket Config

个人使用的 Shadowrocket 主配置与 YouTube 增强模块。配置采用英文名称、中文说明；注释按功能分段，每个有效字段都有用途说明。

## 文件

| 名称 | 类型 | 用途 | 查看 | 订阅 |
| --- | --- | --- | --- | --- |
| **Bypass China** | 主配置 | 中国大陆、局域网及 Apple 资源直连，其余按规则代理 | [配置文件](./Bypass-China.ini) | [Raw](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/Bypass-China.ini) |
| **YouTube Enhance** | 模块 | YouTube / YouTube Music 去广告、后台播放与可选 UMP Worker | [模块文件](./YouTube-Enhance.sgmodule) | [Raw](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/YouTube-Enhance.sgmodule) |

> **更名提示：** `whitelist.ini` → `Bypass-China.ini`；`YouTube-Max.sgmodule` → `YouTube-Enhance.sgmodule`。旧的 Raw 订阅地址不再使用，请在 Shadowrocket 中重新导入新地址。原模块参数 `启用UMPWorker` 改为 `EnableUMPWorker`，切换后请检查开关状态。

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

## YouTube Enhance

### 功能与开关

该模块只处理 YouTube 与 YouTube Music，使用 [Maasea/sgmodule](https://github.com/Maasea/sgmodule) 的 Protobuf 请求及响应脚本，组合 URL Rewrite 和针对性的 UDP 拒绝规则。远程 JavaScript 引用上游 `master`，**没有固定版本**；刷新脚本资源后可能获得上游修复，也可能遇到新兼容性问题。

| 字段 | 作用 |
| --- | --- |
| `#!name` | 模块显示名称：`YouTube Enhance`。 |
| `#!desc` | 功能摘要，说明去广告、后台播放与可配置的附加功能。 |
| `#!author` | 标注使用的上游实现与社区规则来源。 |
| `#!homepage` | 关联 Maasea 上游仓库。 |
| `#!version` | 当前模块配置版本，不代表上游脚本构建日期。 |
| `#!arguments` | 定义六个可编辑参数及其默认值；布尔参数使用 `true` / `false`，字幕翻译使用语言代码。 |
| `#!arguments-desc` | 在模块编辑参数界面逐项说明各参数的效果与可填写值。 |

进入 Shadowrocket「配置 → 模块 → YouTube Enhance → 编辑参数」即可调整。默认值保持原先使用行为，不强制隐藏界面按钮，也不自动开启字幕翻译与调试；**这些功能现在都能自行选择**。

| 参数 | 默认值 | 修改后的效果 |
| --- | --- | --- |
| `EnableUMPWorker` | `true` | `true` 启用 `initplayback` 和 `log_event` 请求脚本，可能使用第三方 Worker；`false` 停用两条请求脚本，其他去广告规则仍有效。 |
| `BlockUpload` | `false` | `true` 尝试隐藏上传按钮；`false` 保留原始入口。 |
| `BlockImmersive` | `false` | `true` 尝试隐藏选段或沉浸式入口；`false` 保留原始入口。 |
| `BlockShorts` | `false` | `true` 尝试隐藏 Shorts 入口；`false` 保留 Shorts。此项不是 Shorts 广告过滤开关。 |
| `CaptionLang` | `off` | `off` 不自动翻译字幕；填写 `zh-Hans`、`zh-Hant`、`en`、`ja` 等语言代码可请求对应字幕翻译。它是文本参数，不是布尔开关。 |
| `Debug` | `false` | `true` 启用上游脚本的调试日志；`false` 保持安静。 |

**注意：** 这些选项控制 Maasea 脚本的附加行为，实际效果取决于上游脚本、YouTube App 版本及是否匹配到相应请求。修改 `CaptionLang` 时，响应脚本和 UMP 请求脚本会使用同一个目标语言；仅打开 `BlockShorts` 不代表关闭 Shorts 视频广告过滤。

### Rule 与 URL Rewrite 字段

| 字段 / 匹配路径 | 作用 |
| --- | --- |
| `AND` + `DOMAIN-SUFFIX,googlevideo.com` + `PROTOCOL,UDP` | 拒绝 `googlevideo.com` 域名及子域名的 UDP 请求，促使适用连接回落 TCP。 |
| `AND` + `DOMAIN,youtubei.googleapis.com` + `PROTOCOL,UDP` | 拒绝 YouTube API 主机上的 UDP 请求，以便 HTTPS 解密与脚本生效。 |
| `player/ad_break` | 对独立中插广告接口使用 `reject-200`；此策略可能与部分 App 版本不兼容。 |
| `googlevideo.com` + `oad` | 拦截旧式广告视频请求；排除 `dclk_video_ads`、`videoplayback` 和 `initplayback`。 |
| `api/stats/ads` | 拦截广告统计接口。 |
| `pagead`、`ptracking` | 拦截广告与跟踪接口。 |
| `api/stats/qoe?adcontext` | 仅拦截查询串以 `adcontext` 开始的广告质量统计请求。 |

`AND` 表示条件需同时成立，`REJECT` 拒绝匹配流量；URL Rewrite 左侧是请求 URL 正则，`reject-200` 表示返回空的 HTTP 200，并不保证应用始终正常处理。

### Script 与 MITM 字段

| 字段 | 作用 |
| --- | --- |
| `youtube.response` | 对 `browse`、`next`、`player`、`search`、`reel/reel_watch_sequence`、`guide`、`account/get_setting`、`get_watch`、`log_event`、`config` 响应运行 Maasea Protobuf 脚本。 |
| `youtube.request.init` | 对匹配的 `googlevideo.com/initplayback` 加密播放请求运行请求脚本，受 `EnableUMPWorker` 控制。 |
| `youtube.request.log_event` | 对 `youtubei/v1/log_event` 请求运行同一请求脚本，配合 UMP 状态处理，受同一开关控制。 |
| `type=http-response` / `http-request` | 指定处理响应正文或发出前的请求。 |
| `pattern` | 请求 URL 的正则匹配范围，不是所有 YouTube 流量都会被脚本改写。 |
| `requires-body=1` | 要求脚本取得 HTTP 正文。 |
| `max-size=-1` | 不通过此字段限制交给脚本的正文大小。 |
| `binary-body-mode=1` | 二进制正文模式，用于处理 Protobuf。 |
| `script-path` | 从 Maasea `master` 分支获取请求/响应 JavaScript。 |
| `argument` | 将模块参数传给 Maasea：`captionLang` 接收 `CaptionLang`；`blockUpload`、`blockImmersive`、`blockShorts`、`debug` 分别使用对应布尔参数。响应脚本读取全部五项，`initplayback` 请求脚本共用 `CaptionLang`。 |
| `enable={{{EnableUMPWorker}}}` | 将模块开关传给两条请求脚本；关闭时不运行这两条脚本。 |
| `hostname = %APPEND%` | 将 YouTube API、视频、广告请求主机追加到现有 MITM 清单，而非覆盖原清单。 |
| `-redirector*.googlevideo.com` | 将重定向类视频主机排除在 MITM 清单之外。 |

### 使用与风险

1. **HTTPS 解密：** 启用 Shadowrocket HTTPS 解密，安装并信任自己的 CA 证书；确认所需连接可进入脚本处理。解密和脚本允许读取、修改匹配流量中的播放与相关请求数据。
2. **第三方 Worker：** Maasea 请求脚本可能将 `initplayback` 重定向到 `https://init-stream.maasea.workers.dev/`，URL 参数可能包括原始播放 URL `target`、客户端密钥 `ck` 和配置项；无法仅凭客户端代码证明其服务端不记录数据。关闭 `EnableUMPWorker` 可避免本模块触发该重定向。
3. **兼容性：** 不要叠加其他 YouTube 响应脚本或旧式 `initplayback` Map Local。出现中插、卡顿或播放失败时，先分别关闭 `EnableUMPWorker`、停用本模块进行对照；重点关注 `player/ad_break` 规则。
4. **持续更新：** 上游脚本和远程规则集均可能变化；本配置不保证所有 App 版本 100% 去广告。不要将其视作完成独立安全审计的软件。
