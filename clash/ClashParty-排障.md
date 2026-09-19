# ClashParty（Windows）一直超时 —— 排查与修复

## 结论：原配置里有 4 个会直接导致「超时」的点

### 1. `rule-providers` 从 `raw.githubusercontent.com` 下载（最可能的原因）
内核启动时会**先下载全部 7 个规则集**才开始工作，这个下载是**直连**的（此时代理还没起来）。
`raw.githubusercontent.com` 在国内基本连不上，于是内核卡在加载阶段 → GUI 显示超时 / profile 加载失败。

**修复**：`windows` 改成把 615 条规则**全部内联**，启动零网络依赖。
另提供 `windows-ruleset.yaml`，规则集改走 jsDelivr 镜像。

### 2. `profile.store-fake-ip: true`
如果你在加上 `fake-ip-filter` 之前导入过配置，节点域名 `liuliu.139876.xyz` 已经被缓存成
`198.18.x.x` 的假 IP。之后无论怎么改配置，内核都会去连这个假 IP → **节点永远超时**。

**修复**：改为 `store-fake-ip: false`。
另外请手动删除缓存文件 `cache.db`（ClashParty 数据目录下），再重启内核。

### 3. DNS 只配了 DoH
```yaml
nameserver:
  - https://223.5.5.5/dns-query
  - https://1.12.12.12/dns-query
```
DoH 走 443 端口的 TLS。部分校园网 / 运营商 / 公司网络会拦截 DoH，一旦拦截，
**所有域名解析都超时**，表现就是"什么都打不开、一直转圈"。

**修复**：明文 UDP 的 `223.5.5.5`、`119.29.29.29` 放在最前面，DoH 只作为补充。
`proxy-server-nameserver` 同样改成明文，保证节点域名一定能解析出来。

### 4. `find-process-mode: strict`
Windows 上查进程名需要管理员权限。没有管理员权限时，每条连接都会先去查进程、失败、再放行，
明显拖慢首包，严重时表现为超时。配置里也没有任何 `PROCESS-NAME` 规则，开着没有意义。

**修复**：改为 `find-process-mode: 'off'`。

## 顺带做的改进
- 加 `external-controller: 127.0.0.1:9090`，保证 GUI 能连上内核 API。
- 加 `sniffer`（域名嗅探），并 `skip-domain: +.139876.xyz`，提高分流准确度。
- `fake-ip-filter` 补上 `time.*.com`、`ntp.*.com`、`*.localdomain` 等，避免对时失败。
- 规则里加 `msftconnecttest.com` / `msftncsi.com` 直连，否则 Windows 一直提示"无 Internet 访问"。
- `url-test` 加 `timeout: 5000` 和 `lazy: true`，避免启动时一次性测速把网卡住。

## 如果换了新配置还是超时

说明问题**不在配置，在节点本身**。按顺序自查：

1. **节点域名能不能解析**
   ```powershell
   nslookup liuliu.139876.xyz 223.5.5.5
   ```
   解析不出来 → 域名失效。解析出 `198.18.x.x` → fake-ip 缓存没清干净（见第 2 点）。

2. **443 端口通不通**
   ```powershell
   Test-NetConnection liuliu.139876.xyz -Port 443
   ```
   `TcpTestSucceeded: False` → 服务器挂了或 IP 被墙，配置救不了。

3. **REALITY 参数是否和服务端一致**
   `servername: www.amazon.com`、`public-key`、`short-id`、`flow: xtls-rprx-vision`
   四项中任意一项与服务端对不上，都会握手失败并表现为超时。
   请对照服务端 `config.json` 的 `realitySettings` 逐项核对。

4. **端口 7890 被占用**
   ```powershell
   netstat -ano | findstr 7890
   ```
   被占用就把 `mixed-port` 改成 7891 等。

5. **看日志**：把 `log-level` 临时改成 `debug`，重启后看 ClashParty 日志里到底是
   DNS 失败、TCP 连接失败，还是 TLS 握手失败 —— 这三种的处理方式完全不同。
