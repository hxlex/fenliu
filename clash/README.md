# Clash / mihomo 分流规则

由根目录的 Quantumult X `.list` 规则转换而来，格式为 mihomo `rule-provider`
（`behavior: classical`，`format: yaml`），适用于 ClashParty / mihomo 内核。

## 用法

在配置文件中加入 `rule-providers`：

```yaml
rule-providers:
  AI:
    type: http
    behavior: classical
    format: yaml
    interval: 86400
    url: https://raw.githubusercontent.com/hxlex/fenliu/main/clash/AI.yaml
    path: ./rules/AI.yaml
  AppleIntelligence:
    type: http
    behavior: classical
    format: yaml
    interval: 86400
    url: https://raw.githubusercontent.com/hxlex/fenliu/main/clash/AppleIntelligence.yaml
    path: ./rules/AppleIntelligence.yaml
  PrivateTracker:
    type: http
    behavior: classical
    format: yaml
    interval: 86400
    url: https://raw.githubusercontent.com/hxlex/fenliu/main/clash/PrivateTracker.yaml
    path: ./rules/PrivateTracker.yaml
  SocialMedia:
    type: http
    behavior: classical
    format: yaml
    interval: 86400
    url: https://raw.githubusercontent.com/hxlex/fenliu/main/clash/SocialMedia.yaml
    path: ./rules/SocialMedia.yaml
  TikTok:
    type: http
    behavior: classical
    format: yaml
    interval: 86400
    url: https://raw.githubusercontent.com/hxlex/fenliu/main/clash/TikTok.yaml
    path: ./rules/TikTok.yaml
  chuguo:
    type: http
    behavior: classical
    format: yaml
    interval: 86400
    url: https://raw.githubusercontent.com/hxlex/fenliu/main/clash/chuguo.yaml
    path: ./rules/chuguo.yaml
  grok:
    type: http
    behavior: classical
    format: yaml
    interval: 86400
    url: https://raw.githubusercontent.com/hxlex/fenliu/main/clash/grok.yaml
    path: ./rules/grok.yaml
```

再在 `rules` 里引用（**顺序即优先级，越靠前越优先**）：

```yaml
rules:
  - RULE-SET,chuguo,PROXY
  - RULE-SET,PrivateTracker,DIRECT
  - RULE-SET,AppleIntelligence,AI
  - RULE-SET,grok,AI
  - RULE-SET,AI,AI
  - RULE-SET,TikTok,TikTok
  - RULE-SET,SocialMedia,PROXY
  - GEOIP,CN,DIRECT
  - MATCH,PROXY
```

策略名（`PROXY` / `AI` / `TikTok` / `DIRECT`）按自己 `proxy-groups` 里的名字改。

## 与 Quantumult X 版本的差异

| Quantumult X | Clash / mihomo |
| --- | --- |
| `IP6-CIDR` | `IP-CIDR6` |
| `.list` 纯文本 | `.yaml`，`payload:` 列表 |
| 策略写在规则里 | 策略写在 `RULE-SET` 那一行 |

`DOMAIN` / `DOMAIN-SUFFIX` / `DOMAIN-KEYWORD` / `IP-CIDR` / `IP-ASN` / `no-resolve`
两边写法一致，直接沿用。

> `IP-ASN` 需要 mihomo 内核（ClashParty 满足），老的 Clash Premium 不支持。
