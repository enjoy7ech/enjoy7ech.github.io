# 个人 Rule Provider

`gfwlist.yaml` 是供 Clash / Mihomo 订阅的个人代理规则集，使用 `behavior: classical` 和 `format: yaml`。

这是手动维护的起始清单，包含 Google、YouTube、GitHub、部分 AI 服务等常用域名；不是上游 GFWList 的全量镜像，也不代表这些域名在每个网络环境都被封锁。可按自己的需求增删。

## 订阅地址

```text
https://raw.githubusercontent.com/enjoy7ech/enjoy7ech.github.io/master/subscription/gfwlist.yaml
```

## 接入现有配置

把下面的 `gfwlist` 项合并进现有 `rule-providers`，再将 `RULE-SET` 这一行加入现有 `rules`。如果这些顶层字段已经存在，不要重复创建。

```yaml
rule-providers:
  gfwlist:
    type: http
    behavior: classical
    format: yaml
    url: "https://raw.githubusercontent.com/enjoy7ech/enjoy7ech.github.io/master/subscription/gfwlist.yaml"
    path: ./ruleset/gfwlist.yaml
    interval: 86400

rules:
  - RULE-SET,gfwlist,Proxy
  # 后面继续保留你原有的规则，包括最终的 MATCH。
```

将 `Proxy` 替换为实际存在的代理策略组名。仓库现有 `clash.yaml` 的规则使用 `Proxy`，示例沿用这个名字。

规则按顺序匹配：把 `RULE-SET,gfwlist,Proxy` 放在你希望保留的局域网、直连或拦截例外之后，以及宽泛的 `GEOIP` 和最终 `MATCH` 之前。前面的规则会优先生效。

此示例只是需要合并的配置片段，不能单独作为完整客户端配置使用。现有 `clash.yaml` 尚未接入此规则集。

首次下载需要能够访问 Raw 地址。若该地址必须经代理访问，可在客户端先启用可用的代理，或在 provider 中按需设置 `proxy: Proxy`（替换为真实组名，并避免依赖尚未下载的规则集来选择下载线路）。

## 后续维护

在 GitHub 打开 [gfwlist.yaml](gfwlist.yaml)，点击编辑，修改 `payload:` 下的条目并提交。

```yaml
payload:
  - DOMAIN-SUFFIX,example.com
  - DOMAIN,api.example.net
```

- `DOMAIN-SUFFIX,example.com`：匹配 `example.com` 及其子域名。
- `DOMAIN,api.example.net`：只匹配这个完整域名。
- 每条规则保持两个空格缩进和 `- ` 前缀；只填写规则，不加 `https://`、URL 路径或末尾的策略组名。
- 删除对应行即可移除规则；以 `#` 开头的行是注释。
- 提交后客户端默认每 86400 秒（24 小时）检查一次更新；需要立即生效时，在客户端刷新该规则集。刷新规则集与更新节点订阅可能是不同的操作。
- 此仓库是公开仓库，规则内容和提交记录均公开可见。

格式参考：[Mihomo rule-providers](https://wiki.metacubex.one/config/rule-providers/)、[规则集内容](https://wiki.metacubex.one/config/rule-providers/content/)。
