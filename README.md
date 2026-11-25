# astrbot_plugin_parser_lite
![Python](https://img.shields.io/badge/Python-%3E%3D3.11-blue) ![AstrBot](https://img.shields.io/badge/AstrBot-%3E%3D4.28%2C%3C5-blue) [![license](https://img.shields.io/badge/license-MIT-yellow)](https://github.com/sokoko-org/nonebot-plugin-parser-lite) [![upstream](https://img.shields.io/badge/upstream-v1.4.1rc5-informational)](https://github.com/sokoko-org/nonebot-plugin-parser-lite/releases)

[nonebot-plugin-parser-lite](https://github.com/sokoko-org/nonebot-plugin-parser-lite) 的AstrBot 桥接插件
## 桥接说明（AstrBot 用户只需看本节）
- **安装与更新**：AstrBot 插件市场安装，或 WebUI「插件」页上传发布 zip；手动部署的覆盖后重启
- **配置**：WebUI 插件页共 41 个配置项（8 桥接 + 33 上游），保存后自动重载；上游另有 4 个开关的实现面在 NoneBot 宿主层、本桥没有对应行为，故不进面板（逐项理由见 `doc/CONTEXT.md`）
- **版本与许可证**：随上游快照滚动自动再生（当前上游 v1.4.1rc5），与 release tag 锁步；许可证随上游（MIT），vendor 快照零修改
- **架构与滚动同步机制**：见仓库 `doc/CONTEXT.md`（两层注入与各数据面的术语定义）
- **问题反馈**：[仓库 Issues](https://github.com/Chuubururin/astrbot_plugin_parser_lite/issues)，请附可复现的分享链接与 WebUI 日志
### 安全说明
- **出站 SSRF 防护**：媒体下载经四层校验（scheme → 端口 → 逐 IP → 「解析即连接」钉扎
  防 rebinding），守卫挂载失败即**拒启**（fail-closed）。唯一例外：ffmpeg HLS 子进程——仅
  入口 URL 校验 + 启动后自毁监听，存在无法钉扎 IP 的 DNS TOCTOU 残窗（vendor_patches 头注）。
- **fake-IP 代理环境**：fake-IP 模式（Clash/mihomo）把域名解析进 `198.18.0.0/15`，日志即「解析到被拒绝的地址
  198.18.x.x」。首选在代理 `fake-ip-filter` 排除该域名（防护完整保留）；改不了代理时打开
  `plite_ssrf_trust_fake_ip`（默认关闭），它只让出这一条段，内网与云元数据照旧拒绝。
- **TLS 校验姿态**：vendor 下载链继承上游 `verify=False`（CDN 证书链兼容的既定取舍）。
- **卡面不外发**：卡片在宿主进程内绘制（上游 Blitz 引擎），不发往任何渲染服务；
  上游 1.3.11rc2 前经宿主远程 t2i 截图的形态已退役。
