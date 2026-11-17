# Shadowrocket 配置

个人自用的 Shadowrocket 主配置。

## 主配置 · 白名单

[查看](./whitelist.ini) · [订阅](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/whitelist.ini)

- **分流：** 局域网、Apple、中国域名及 IP 直连；Google 和其余流量代理。
- **DNS：** 代理 Cloudflare/Google DoH，直连腾讯 DNS；禁用系统 DNS 回退。
- **网络：** 阻断代理 QUIC、禁用 IPv6，UDP 不支持时拒绝。

## YouTube Max · 去广告与后台播放

[查看模块](./YouTube-Max.sgmodule) · [订阅](https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/YouTube-Max.sgmodule)

- **功能：** 针对 YouTube / YouTube Music 的视频和信息流广告进行规则拦截及 Protobuf 响应处理，并尝试启用后台播放；实际效果随 App 版本及接口调整而变化，不保证 100% 去广告。
- **范围：** 仅提供去广告与后台播放相关配置；未主动启用字幕翻译、隐藏按钮、屏蔽 Shorts 等额外功能。
- **实现：** 使用 URL Rewrite、针对性 QUIC/UDP 拦截与 [Maasea/sgmodule](https://github.com/Maasea/sgmodule) 的 YouTube 请求/响应脚本，包含新式 UMP/Onesie 播放链路处理。
- **使用：** 在 Shadowrocket「模块」中添加订阅地址并启用；启用 HTTPS 解密，安装并在系统中信任其 CA 证书。避免与其他 YouTube 改写模块叠加。
- **兼容：** 若出现视频卡顿、播放失败或中插异常，先停用本模块对比；YouTube 更新后可能需要调整规则或脚本。

### 隐私与安全说明

本模块**不是完全本地、零第三方依赖**的去广告方案。使用前需了解：

1. **HTTPS 解密：** 模块对指定 YouTube 相关主机启用 MITM。解密与脚本处理在 Shadowrocket 所在设备上执行，脚本因此有机会读取和修改匹配请求/响应中的数据，例如视频播放信息和相关会话参数。CA 证书应仅在可信设备上使用。
2. **第三方 Worker：** 当前引用的 [Maasea 请求脚本](https://github.com/Maasea/sgmodule/blob/master/Script/Youtube/youtube.request.js) 包含 `https://init-stream.maasea.workers.dev/`，在部分 `initplayback` 请求中可能重定向到该服务，URL 参数包括 `target`（原始播放请求 URL）、`ck`（客户端密钥参数）和脚本参数。该服务可能接触播放请求相关数据及连接元数据；**无法从客户端脚本证明其服务端不记录或不处理这些数据**。
3. **远程脚本：** 两个 JavaScript 文件均从上游 GitHub 的 `master` 分支加载，上游后续更新可能改变数据处理行为，存在供应链风险。当前检查未发现直接上传 Google 账号密码的明确逻辑，但这**不代表已完成全面安全审计**。
4. **取舍：** 如果更重视隐私，可移除第三方 Worker 所依赖的请求处理、固定经过审查的脚本版本或只采用本地拦截规则；相应地，部分新式视频广告可能无法拦截。

**建议：** 定期检查上游脚本更新；不要安装来源不明的 CA 证书，也不要将本模块当作已经过独立安全审计的软件。
