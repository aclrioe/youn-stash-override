# 个人 Stash 覆写

可跨多份 Stash 主配置复用的广告拦截、自定义分流、Apple 与 ChatGPT 规则。

## 安装

- [Stash 一键安装（CDN）](https://link.stash.ws/install-override/cdn.jsdelivr.net/gh/aclrioe/youn-stash-override@main/Stash/personal-rules.stoverride)
- [覆写 CDN URL（默认）](https://cdn.jsdelivr.net/gh/aclrioe/youn-stash-override@main/Stash/personal-rules.stoverride)
- [GitHub Raw URL（备用）](https://raw.githubusercontent.com/aclrioe/youn-stash-override/main/Stash/personal-rules.stoverride)

也可在 Stash 的覆写管理中从 URL 添加：

```text
https://cdn.jsdelivr.net/gh/aclrioe/youn-stash-override@main/Stash/personal-rules.stoverride
```

默认使用 jsDelivr CDN；不同网络的直连可用性可能不同。CDN 存在缓存，仓库更新后内容可能延迟同步。此地址仅用于下载覆写文件，不会改变覆写内部广告规则集的下载地址。

## 依赖与内容

- 不要求机场主配置有特定名称的策略组；覆写自己创建 `🧩个人覆写出口`，通过 `include-all: true` 引用当前配置的所有节点和远程代理集。
- 更新并启用覆写后，到 Stash 策略组中进入 `🧩个人覆写出口`，手动选择当前机场的可用节点。切换机场主配置后检查该组的候选和当前选择。
- 覆写有 99 条前置规则：Apple 智能/Siri/Relay 10 条、ChatGPT 与原有依赖 13 条、Apple 通用直连 31 条、自定义优先例外 11 条、广告拦截 22 条、其他既有直连 12 条。
- 保留 Apple 相关七组真实 DNS 与系统 DNS 策略，不修改主配置的全局上游 DNS。
- Apple 专用代理规则先于 Apple 通用直连；ChatGPT 规则先于广告拦截。
- BanAD、GEOSITE category-ads 和手写广告规则启用；BanProgramAD 的 RULE-SET 引用仍为注释。
- 不包含节点、密码、UUID 或 MATCH 兜底；仅新增一个独立代理组，不替换机场原有节点、代理组或兜底规则。

### 跨机场配置的实现

```yaml
proxy-groups:
  - name: 🧩个人覆写出口
    type: select
    include-all: true
```

所有原先需要代理的 29 条规则统一指向这个组；DIRECT、REJECT 及其余规则的顺序和内容保持不变。无需知道机场原先叫 `Proxy`、`节点选择` 或其他名称，也不依赖这些组的内部结构。

该组读取的是**当前启用主配置**里的 `proxies` 和 `proxy-providers`，不会把手机上所有已保存但未启用的机场配置混在一起，也不自动继承机场原有组的自动选择、地区筛选或分流策略。已有节点和远程代理集需能在 Stash 正常加载。

如果该组没有候选节点，Stash 会把空组作为 DIRECT 处理；请先确认当前主配置包含可用节点/远程代理集，再使用代理规则。若主配置已有同名 `🧩个人覆写出口` 组，应避免重复定义。

## 合并与更新

Stash 将覆写的数组前置、字典递归合并。可以在原主配置上试用，但会与已有规则重复；使用已迁出这部分规则的配套主配置可以避免重复。精简主配置需与覆写配套使用。

修改仓库中同一路径的覆写文件后，在 Stash 更新并启用远程覆写。规则提供者的 `interval: 86400` 仅指广告规则集的更新，不代表覆写文件更新频率。

## 原有策略说明

- `apple-relay` 关键词、`cloudfront.net` 与 `eo.dnse4.com` 后缀属于广泛匹配，保留原配置范围。
- ChatGPT 分类包含共享域名，不代表仅对 ChatGPT 生效，也不是官方完整域名清单。
- 个别 DIRECT 规则仍排在广告拦截后；如 `msg.umeng.com` 会被更早的 BanAD 规则拦截。
- Stash iOS 忽略的五条 PROCESS-NAME 规则已从此覆写移除。
- 此文件只整理 DNS 与路由，不包含 MitM、地区修改或设备信息重写。手机上的服务可用性需实际验证。

恢复迁移前配置时，关闭此覆写并切回原始完整主配置。

## 参考

- [Stash 覆写合并规则](https://stash.wiki/configuration/override)
- [Stash 策略组与 include-all](https://stash.wiki/proxy-protocols/proxy-groups)
- [Stash 规则类型](https://stash.wiki/rules/rule-types)
- [Stash 远程覆写安装链接](https://stash.wiki/faq/url-schema)
