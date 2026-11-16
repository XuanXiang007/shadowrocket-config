# YouTube Clean for Shadowrocket

面向 iOS Shadowrocket 的**保守型** YouTube 增强模块。

- 模块：[`YouTube.Clean.module`](./YouTube.Clean.module)
- 一键导入地址：`https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/modules/YouTube.Clean.module`
- 上游响应脚本：[`scripts/youtube-response.js`](../scripts/youtube-response.js)
- 版本：v1.0（2026-11-16）

## 功能范围

| 功能 | 实现/限制 |
| --- | --- |
| 首页、搜索、部分视频广告 | 使用 Maasea protobuf 响应脚本去除部分广告元素 |
| 画中画 / 后台播放 | 修改播放器与设置中的相关响应字段；是否生效依赖 YouTube 客户端版本 |
| 广告统计请求 | 精确匹配并拒绝部分 `api/stats/ads`、`pagead`、`ptracking` |
| 旧版视频广告资源 | 只拒绝带 `&oad` 的指定资源请求，避开常规 `videoplayback` |
| QUIC | 拒绝 googlevideo.com 与 youtubei.googleapis.com 的 UDP 流量，让客户端尝试 TCP；可能增加连接耗时 |
| 中插广告 `player/ad_break` | **尚不支持可靠过滤**。不直接拦截，以减少播放失败风险 |
| 加密 Onesie / UMP | **不保证过滤**。本模块刻意不使用远程 Worker 播放流处理 |
| YouTube Music | 复用上游响应解析，兼容性未在当前版本实机验证 |

## 安装和使用

1. 在 Shadowrocket 配置 → 模块里增加上述 Raw 地址并启用。
2. 开启 HTTPS 解密，在 iOS 系统中信任你**自己创建的** Shadowrocket CA 证书。
3. 确保只启用一个 YouTube 播放增强模块，不要与 Maasea、iab0x00、其他重复的 protobuf 去广告脚本共用。
4. 完全退出 YouTube 并重开，分别测试首页、播放前广告、中插、Shorts、画中画及锁屏后台播放。
5. 如果播放卡顿、YouTube Music 失效或无法连接，先禁用模块；必要时关闭 HTTPS 解密恢复正常。

注意：配置文件 `../config.ini` 未改动；本模块不替代现有 DNS/代理分流设置。该项目不是 YouTube Premium 的完整替代，也没有对所有版本兼容性作保证。

## 设计和安全边界

- **只加载一个 HTTP 响应脚本**：本地仓库的 `scripts/youtube-response.js`，复制自 [Maasea/sgmodule](https://github.com/Maasea/sgmodule) 的 Git 提交 `65075cdb388fc5e3094afd7e7314c67b243f3525` 中 `Script/Youtube/youtube.response.js`，原样保留（Apache-2.0）。
- 模块内 `script-path` 锁定到本仓库提交 `9e6ead9cf15b0677344711461a78fee1906701e6`。修改 main 上的脚本不会静默替换已锁定版本；修复时必须审核并主动更新链接。
- **没有**装载 Maasea 的 `youtube.request.js`，因此不会运行其中处理加密 `initplayback` 并调用 `init-stream.maasea.workers.dev` 的逻辑。
- 响应脚本的静态检查未发现硬编码的 Worker 地址；但包含多平台网络 API 适配方法、第三方 protobuf 代码和持久化配置逻辑，不能由静态检查推导为绝对安全。
- HTTPS 解密域名仅用于指定的 YouTube API、视频 CDN 和广告统计域名；但 `*.googlevideo.com` 会让该域名范围内 HTTPS 流量进入 MITM。私密使用时需自行判断风险。
- 模块默认**不隐藏**上传、选段及 Shorts 按钮，翻译关闭，Debug 关闭。可以在模块参数中调整。
- 未加入通杀 `player/ad_break` 的 reject 规则：该接口数据结构未做充分验证，盲目拒绝可能造成视频故障。

## 上游参考与社区问题

- [Maasea/sgmodule](https://github.com/Maasea/sgmodule)：YouTube Protobuf 去广告、画中画与后台播放核心实现。
- [Maasea Issue #110](https://github.com/Maasea/sgmodule/issues/110)：2026-10-23 用户报告 `player/ad_break` 中插广告接口绕过旧匹配规则。
- [akiralereal/shadowrocket-toolkit](https://github.com/akiralereal/shadowrocket-toolkit)：Shadowrocket 模块、QUIC UDP 限制、视频广告请求过滤参考。
- [zhaomo08/youtube-surge-modules](https://github.com/zhaomo08/youtube-surge-modules)：版本固定、脚本归档与不重复运行相同 Protobuf 响应脚本的经验。
- [iab0x00/ProxyRules](https://github.com/iab0x00/ProxyRules)：Shadowrocket 配置与模块参考。
- [DualSubs/YouTube](https://github.com/DualSubs/YouTube)：字幕专用实现；不和本模块叠加修改 `player/get_watch`。

本项目是基于上游和社区信息的配置设计，**完成了代码静态检查及规则样例匹配检查，没有在真实 iPhone/YouTube App 上进行端到端测试**。报告故障时请附 iOS、Shadowrocket、YouTube 版本及脱敏日志，勿提交 Cookie、Token 或证书。
