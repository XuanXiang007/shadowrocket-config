# Shadowrocket 配置

个人自用，只保留一份主配置和一份 YouTube 模块，两者独立管理。

## 主配置：sr.ini

[查看配置](./sr.ini) · [订阅地址](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/sr.ini)

- 分流：局域网、中国域名及中国 IP 直连；Google 和其余流量代理。
- DNS：直连用腾讯 DNS；基础 DNS 为代理 Cloudflare DoH，备用为代理 Google DoH；关闭系统 DNS 回退。
- 其他：阻断代理 QUIC、禁用 IPv6、UDP 不支持时拒绝。

## YouTube Clean 模块

[查看模块](./modules/YouTube.Clean.module) · [导入地址](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/modules/YouTube.Clean.module)

过滤部分广告及统计请求，尝试启用画中画和后台播放。效果依赖 YouTube 客户端版本；不能保证去除所有广告，部分中插广告和加密 UMP 未覆盖。

安装：
1. 在 Shadowrocket 的模块页面导入并启用模块。
2. 开启 HTTPS 解密，安装并信任自己生成的 Shadowrocket CA 证书。
3. 重启 YouTube 测试；遇到播放问题先禁用模块或 HTTPS 解密。不要同时启用其他 YouTube 增强模块。

**隐私提示：** 模块会对指定 YouTube API 和 `*.googlevideo.com` 等域名进行 HTTPS 解密。理解证书风险后再使用；勿分享证书私钥、Cookie 或含凭据的日志。

**脚本来源与许可：** 引用 [Maasea/sgmodule 的 YouTube 响应脚本](https://github.com/Maasea/sgmodule/blob/65075cdb388fc5e3094afd7e7314c67b243f3525/Script/Youtube/youtube.response.js)，固定提交 `65075cdb388fc5e3094afd7e7314c67b243f3525`，与清理前仓库的脚本副本具有相同 Git Blob SHA（`becad8eaa6094c189ea8db6d644de68ac2d66f61`）。原作者版权保留，许可为 [Apache-2.0](https://github.com/Maasea/sgmodule/blob/65075cdb388fc5e3094afd7e7314c67b243f3525/LICENSE)。不加载上游请求脚本或其第三方 Worker 处理。

模块不改变主配置的 DNS 或分流。此模块仅经静态检查，尚未针对最新 YouTube App 实机验证。
