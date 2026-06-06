# RuleSet

个人使用的 Clash / Mihomo 分流配置与规则集，覆盖常见代理、直连、广告拦截、流媒体、AI、游戏平台等场景。

## 文件说明

```text
.
├── kiro-config.yaml           # 完整 Clash 配置，使用本仓库中的规则集
├── kiro-config-override.yaml  # 配置覆写，使用 ACL4SSR 远程规则
└── ruleSet/                   # 按服务分类的域名、IP CIDR 与进程规则
```

常用规则集包括：

- `openai.yaml`、`google.yaml`、`microsoft.yaml`：在线服务
- `youtube.yaml`、`netflix.yaml`、`disney.yaml`、`streaming.yaml`：流媒体
- `steam.yaml`、`steamCN.yaml`：游戏平台
- `direct.yaml`、`proxy.yaml`、`reject.yaml`：直连、代理与拦截
- `lancidr.yaml`、`cncidr.yaml`、`telegramcidr.yaml`：IP CIDR
- `applications.yaml`：进程规则

## 使用方式

### 完整配置

1. 打开 `kiro-config.yaml`。
2. 将 `proxy-providers.myproxy.url` 修改为自己的订阅地址。
3. 在 Clash / Mihomo 客户端中导入该配置。

配置中的规则集通过以下地址获取，并每 24 小时更新一次：

```text
https://raw.gitmirror.com/LSauce/RuleSet/master/ruleSet/<规则文件>.yaml
```

### 配置覆写

`kiro-config-override.yaml` 适用于支持覆写功能的客户端。它提供代理组、节点地区筛选和基于 ACL4SSR 的远程规则，但不包含订阅信息。具体导入方式取决于所使用的客户端。

## 规则格式

域名规则：

```yaml
payload:
  - "+.example.com"
  - "specific.example.com"
```

IP CIDR 规则：

```yaml
payload:
  - "192.0.2.0/24"
  - "2001:db8::/32"
```

进程规则：

```yaml
payload:
  - "PROCESS-NAME,example"
  - "PROCESS-NAME,example.exe"
```

## 添加规则

向已有分类添加规则时，直接编辑 `ruleSet/` 下对应的 YAML 文件。

创建新分类时：

1. 在 `ruleSet/` 中创建新的 YAML 文件，并添加 `payload`。
2. 在 `kiro-config.yaml` 的 `rule-providers` 中注册该规则集。
3. 在 `kiro-config.yaml` 的 `rules` 中将规则集绑定到目标代理组。

示例：

```yaml
rule-providers:
  example:
    type: http
    behavior: domain
    url: "https://raw.gitmirror.com/LSauce/RuleSet/master/ruleSet/example.yaml"
    path: ./ruleset/example.yaml
    interval: 86400

rules:
  - RULE-SET,example,🔰国外流量
```

提交信息建议使用中文描述，并采用以下前缀：

```text
feat: 添加新规则
fix: 修正规则
refactor: 整理规则集
```

## 注意事项

- 使用前请确认客户端支持配置中使用的 Clash / Mihomo 字段。
- 订阅地址属于敏感信息，不应提交到公开仓库。
- 规则按顺序匹配，新增规则时应检查其位置是否会被更早的规则覆盖。
- 本项目仅供个人网络分流使用，请遵守所在地法律法规及相关服务条款。
