# shadowrocket-config

自用 Shadowrocket 配置，重点是简单分流与 DNS 泄漏控制。

## 主配置

[config.ini](./config.ini)

`https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/config.ini`

## YouTube Clean 模块

[YouTube.Clean.module](./modules/YouTube.Clean.module) — 面向 Shadowrocket 的去广告、画中画与后台播放增强（实机兼容性需自行验证）。

导入链接：

`https://raw.githubusercontent.com/XuanXiang007/shadowrocket-config/main/modules/YouTube.Clean.module`

[安装方法、隐私审查和已知限制](./modules/README.md) · [上游脚本版权信息](./NOTICE.md)

模块与主配置独立管理；添加模块不会更改现有 DNS 与规则集。
