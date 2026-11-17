# Shadowrocket 配置

个人自用，只保留一份主配置和一份 YouTube 模块，两者独立管理。

## 主配置：whitelist.ini（白名单模式）

[查看配置](./whitelist.ini) · [订阅地址](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/whitelist.ini)

- 分流：白名单模式；局域网、Apple（含 iCloud）、中国域名及中国 IP 直连，Google 和其余未命中流量走代理（`FINAL,PROXY`）。Apple 规则优先匹配，iCloud 等服务不再默认走代理。
- DNS：直连用腾讯 DNS；基础 DNS 为代理 Cloudflare DoH，备用为代理 Google DoH；关闭系统 DNS 回退。
- 其他：阻断代理 QUIC、禁用 IPv6、UDP 不支持时拒绝。

## YouTube Enhance Pro 模块（偏向拦截覆盖率）

[查看模块](./modules/youtube-enhance.module) · [导入地址](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/modules/youtube-enhance.module)

- **广告与增强：** 使用 Maasea 固定版本响应脚本，覆盖首页、搜索、Shorts、常规播放器广告，以及画中画、后台播放、可选字幕翻译。新增 `config` / `log_event` 响应与 `initplayback` 请求处理，以应对新版 Onesie/UMP 加密播放链路。
- **新式中插：** 针对 `youtubei/v1/player/ad_break` 增加定向拒绝规则。该接口有社区抓包报告，但直接拒绝可能导致某些 App 版本播放异常；如出现转圈或无法播视频，**先移除此条规则后重试**。
- **旧广告请求：** 阻断部分广告统计请求；对 `googlevideo.com` 旧式 `oad` 资源拦截，但保护正常 `videoplayback` 和 `initplayback` 请求。没有添加高风险的全局 `ctier` 改写。
- **隐私变化：** 与原先的“不依赖第三方 Worker”版本不同，**现在 Maasea 请求脚本可能把特定 `initplayback` 请求转发至 `https://init-stream.maasea.workers.dev/`**。请理解第三方处理的隐私风险后再启用；模块仅固定脚本版本，不等于将全部流量留在设备本地。
- **独立性：** 不改动 `whitelist.ini` 主配置的 DNS 和分流。不得与其他 YouTube 增强模块同时启用。

安装与排障：
1. 在 Shadowrocket 模块页面导入并启用上述地址。
2. 开启 HTTPS 解密，安装并信任自己生成的 Shadowrocket CA 证书。
3. 强制退出并重新打开 YouTube；确认脚本命中，并测试首页、搜索、Shorts、播放前广告、长视频中插、画中画、后台播放、YouTube Music。
4. 如遇播放问题，先排除 `player/ad_break` 拒绝规则；再临时停用模块定位是否为 UMP 请求脚本；必要时清理脚本持久化数据 `YouTubeConfig` 与 `YouTubeAdvertiseInfo`。
5. 不要长期打开 Debug，不要公开证书私钥、Cookie、访问令牌或包含账户信息的网络日志。

**脚本来源与许可：** Maasea/sgmodule，固定提交 `65075cdb388fc5e3094afd7e7314c67b243f3525`；响应脚本 Git Blob `becad8eaa6094c189ea8db6d644de68ac2d66f61`，请求脚本 Git Blob `f0e2a9b9b823f2f289a00446c2876a5097bc6a63`；[Apache-2.0](https://github.com/Maasea/sgmodule/blob/65075cdb388fc5e3094afd7e7314c67b243f3525/LICENSE)，原作者版权保留。

**验证状态：** 已完成静态正则与脚本路径检查，尚未进行最新 YouTube App + Shadowrocket 的实机验证。广告机制持续变化，无法承诺 100% 去广告或 Premium 的全部功能。
