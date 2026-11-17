# Shadowrocket 配置

个人自用，只保留一份主配置和一份 YouTube 模块，两者独立管理。

## 主配置：whitelist.ini（白名单模式）

[查看配置](./whitelist.ini) · [订阅地址](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/whitelist.ini)

- 分流：白名单模式；局域网、Apple（含 iCloud）、中国域名及中国 IP 直连，Google 和其余未命中流量走代理（`FINAL,PROXY`）。Apple 规则优先匹配，iCloud 等服务不再默认走代理。
- DNS：直连用腾讯 DNS；基础 DNS 为代理 Cloudflare DoH，备用为代理 Google DoH；关闭系统 DNS 回退。
- 其他：阻断代理 QUIC、禁用 IPv6、UDP 不支持时拒绝。

## YouTube Enhance Pro 模块（默认隐私优先，按需增强 UMP）

[查看模块](./modules/youtube-enhance.module) · [导入地址](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/modules/youtube-enhance.module)

- **默认模式（`启用UMP第三方Worker=false`）：** 使用固定版本 Maasea 响应脚本，清理 YouTube/YouTube Music 的首页、搜索、Shorts 与播放器相关广告数据，并支持画中画、后台播放和可选字幕翻译。默认禁用可能将加密 `initplayback` 请求重定向到第三方 Worker 的请求脚本。默认模式无法保证清理全部新式 UMP 中插广告。
- **增强模式（用户显式改为 `启用UMP第三方Worker=true`）：** 允许固定版本 Maasea 请求脚本在匹配的 Onesie/UMP 加密播放场景使用 `https://init-stream.maasea.workers.dev/`。其重定向 URL 可能带有客户端密钥相关信息（`ck`）及原始目标 URL（`target`）。该 Worker 的服务器端处理、日志及保留政策无法仅通过本仓库验证；不应视为纯本地去广告。
- **新式中插：** 已有社区关于 `youtubei/v1/player/ad_break` 的漏拦截报告，但直接拒绝的可靠性不明。v2.1 移除了此前直接 `reject` 的规则，优先保护正常播放。参考 [Maasea issue #110](https://github.com/Maasea/sgmodule/issues/110)。
- **其他过滤：** 保留广告统计请求及旧式 `oad` 资源的有限拦截；保护正常 `videoplayback` 与 `initplayback`。不使用全局 `ctier=L` 改写。对视频相关域名阻断 QUIC/UDP，以允许 HTTPS/TCP 解密；若节点或 App 版本不兼容，可能影响播放。
- **隐私边界：** 即使 Worker 关闭，本模块仍要对指定 YouTube/Googlevideo 域名进行本机 HTTPS MITM，运行从 GitHub 固定提交下载的第三方脚本，并使用正常 YouTube 网络连接/用户原有代理。所谓「默认本地」只表示默认不启用该 Worker 重定向，不表示端到端隐私保护。
- **独立性：** 模块不修改 `whitelist.ini` 的 DNS、节点或分流，勿与其他 YouTube 增强模块同时启用。

### 安装与设置

1. 在 Shadowrocket 模块页面导入上述地址并启用；全局路由使用「配置」模式。
2. 开启 HTTPS 解密并安装、信任自己的 Shadowrocket CA；如可用，开启 HTTP/2 MITM。
3. 打开 **配置 → 模块 → YouTube Enhance Pro → 编辑参数**，保持 `启用UMP第三方Worker=false`（默认）。只有在理解数据可能交由第三方处理、且有必要增强加密链路广告处理时才改为 `true`。
4. 完全退出并重新打开 YouTube；检查首页、搜索、Shorts、播放前广告、中插、画中画、后台播放及 YouTube Music。出现转圈或无法播放时，先将 Worker 参数设回 `false`，再暂时停用模块，区分模块与应用/网络问题。
5. 必要时清理脚本持久化键 `YouTubeConfig` 与 `YouTubeAdvertiseInfo`；不要长期开启 Debug，也不要公开 CA 私钥、Cookie、令牌或包含账户信息的代理日志。
6. **升级注意：** Shadowrocket 的模块「编辑参数」可能跨更新保留旧值。升级自旧版本时，主动查看该开关，确认实际配置符合预期。

**脚本来源与许可：** [Maasea/sgmodule](https://github.com/Maasea/sgmodule)，固定提交 `65075cdb388fc5e3094afd7e7314c67b243f3525`；响应脚本 Git Blob `becad8eaa6094c189ea8db6d644de68ac2d66f61`，请求脚本 Git Blob `f0e2a9b9b823f2f289a00446c2876a5097bc6a63`；[Apache-2.0](https://github.com/Maasea/sgmodule/blob/65075cdb388fc5e3094afd7e7314c67b243f3525/LICENSE)，原作者版权保留。

**验证状态：** 静态检查了规则、开关、固定脚本来源与配置路径，**未进行最新版 YouTube App + Shadowrocket 实机验证**。广告机制持续变化，不承诺 100% 去广告或 Premium 全部功能；开关在各 Shadowrocket 版本中的实际行为仍以客户端实现为准。
