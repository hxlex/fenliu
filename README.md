# fenliu — 分流规则集

QuantumultX / Clash(mihomo) 通用的分流规则，以及一份 ClashParty 配置模板。

## 仓库必须保持 Public

QuantumultX、Clash 下载远程规则时是**匿名请求**，不会带任何凭证。
仓库一旦设为 Private，`raw.githubusercontent.com` 对匿名请求一律返回 **404**，
所有客户端的规则订阅都会失效。

所以：**规则公开放仓库，节点配置不进仓库。**

## 目录

| 路径 | 用途 |
|---|---|
| `*.list` | QuantumultX 格式规则 |
| `clash/*.yaml` | Clash / mihomo 的 rule-provider 格式规则 |
| `windows.example.yaml` | ClashParty 配置**模板**（自包含版，规则已内联） |
| `windows-ruleset.example.yaml` | ClashParty 配置模板（规则走在线订阅） |
| `clash/ClashParty-排障.md` | 连不上 / 超时的排查记录 |

## 规则订阅地址

QuantumultX（`[filter_remote]`）：

```
https://raw.githubusercontent.com/hxlex/fenliu/main/AI.list, tag=AI, force-policy=AI, update-interval=86400, enabled=true
https://raw.githubusercontent.com/hxlex/fenliu/main/TikTok.list, tag=TikTok, force-policy=TikTok, update-interval=86400, enabled=true
https://raw.githubusercontent.com/hxlex/fenliu/main/SocialMedia.list, tag=社交媒体, force-policy=社交媒体, update-interval=86400, enabled=true
https://raw.githubusercontent.com/hxlex/fenliu/main/PrivateTracker.list, tag=PT, force-policy=direct, update-interval=86400, enabled=true
https://raw.githubusercontent.com/hxlex/fenliu/main/AppleIntelligence.list, tag=AppleAI, force-policy=AI, update-interval=86400, enabled=true
https://raw.githubusercontent.com/hxlex/fenliu/main/grok.list, tag=Grok, force-policy=AI, update-interval=86400, enabled=true
https://raw.githubusercontent.com/hxlex/fenliu/main/chuguo.list, tag=出国, force-policy=PROXY, update-interval=86400, enabled=true
```

国内直连不稳的话，把 `raw.githubusercontent.com/hxlex/fenliu/main`
换成 `cdn.jsdelivr.net/gh/hxlex/fenliu@main` 即可。

## 用配置模板

`windows.example.yaml` 里的这几个占位符要换成你自己的值
（3x-ui 面板点节点的「二维码 / 分享链接」就能拿到）：

| 占位符 | 分享链接里的字段 |
|---|---|
| `YOUR_SERVER_DOMAIN` | `@` 后面的域名 |
| `YOUR_UUID` | `vless://` 后面的 id |
| `YOUR_REALITY_PUBLIC_KEY` | `pbk=` |
| `YOUR_SHORT_ID` | `sid=` |
| `www.amazon.com` | `sni=` |

填好后**另存为本地文件**再导入客户端。仓库里的 `.gitignore`
已经挡住了 `windows` 和 `windows-ruleset.yaml` 这两个文件名，
防止误提交真实节点信息。

> `support-x25519mlkem768: true` 这行别删 —— Xray-core v26.9.8+
> 没有它会握手失败，详见 `clash/ClashParty-排障.md`。
