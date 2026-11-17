# Shadowrocket 配置

个人自用：一份主配置、一份 YouTube 模块，独立更新。

## 主配置 · 白名单

[查看](./whitelist.ini) · [订阅](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/whitelist.ini)

- **分流：** 局域网、Apple、中国域名及 IP 直连；Google 和其余流量代理。
- **DNS：** 代理 Cloudflare/Google DoH，直连腾讯 DNS；禁用系统 DNS 回退。
- **网络：** 阻断代理 QUIC、禁用 IPv6，UDP 不支持时拒绝。

## YouTube Enhance

[查看模块](./modules/youtube-enhance.module) · [导入模块](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/modules/youtube-enhance.module)

- **功能：** YouTube / Music 去广告、画中画、后台播放；支持字幕翻译及界面按钮隐藏。
- **隐私：** UMP Worker 默认关闭。手动开启 `启用UMP第三方Worker` 后，部分加密播放请求可能转发至 [Maasea Worker](https://init-stream.maasea.workers.dev/)，并携带客户端密钥相关信息和原始目标 URL。
- **取舍：** 不强制拒绝 `player/ad_break`，避免未经验证的规则影响播放；关闭 Worker 可能降低部分新版广告的拦截覆盖。
- **安装：** 导入并启用模块，开启 HTTPS 解密并信任 Shadowrocket CA；不要与其他 YouTube 增强模块同时启用。

脚本来自 [Maasea/sgmodule](https://github.com/Maasea/sgmodule)，固定提交 `65075cdb388fc5e3094afd7e7314c67b243f3525`，许可证 [Apache-2.0](https://github.com/Maasea/sgmodule/blob/65075cdb388fc5e3094afd7e7314c67b243f3525/LICENSE)。

> 尚未完成最新 YouTube App 实机验证；无法保证完全去广告。即使关闭 Worker，模块仍通过本机 HTTPS 解密处理相关请求。
