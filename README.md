# v2rayN Windows 使用、升级与故障排查指南

> 面向 Windows 用户的长期稳定使用指南。  
> 重点关注：**版本选择、正确升级、系统代理、路由模式、配置备份、日志排查、SQLite 数据库故障与安全回退**。

---

## 目录

- [1. 指南定位](#1-指南定位)
- [2. v2rayN 是什么](#2-v2rayn-是什么)
- [3. 官方下载与版本选择](#3-官方下载与版本选择)
- [4. Windows 下载包怎么选](#4-windows-下载包怎么选)
- [5. 推荐的安装与目录结构](#5-推荐的安装与目录结构)
- [6. 重要目录和文件](#6-重要目录和文件)
- [7. 第一次安装的标准流程](#7-第一次安装的标准流程)
- [8. 系统代理模式说明](#8-系统代理模式说明)
- [9. 路由模式说明](#9-路由模式说明)
- [10. 推荐的日常组合](#10-推荐的日常组合)
- [11. 节点与配置备份策略](#11-节点与配置备份策略)
- [12. 正确的升级策略](#12-正确的升级策略)
- [13. 什么时候应该升级](#13-什么时候应该升级)
- [14. 什么时候不要急着升级](#14-什么时候不要急着升级)
- [15. 新版测试与回退策略](#15-新版测试与回退策略)
- [16. 常用故障排查命令](#16-常用故障排查命令)
- [17. 通用故障排查流程](#17-通用故障排查流程)
- [18. 实战案例：进程存在但界面和托盘不显示](#18-实战案例进程存在但界面和托盘不显示)
- [19. SQLite 数据库损坏如何确认](#19-sqlite-数据库损坏如何确认)
- [20. SQLite 数据库损坏如何处理](#20-sqlite-数据库损坏如何处理)
- [21. 这类数据库异常可能是如何产生的](#21-这类数据库异常可能是如何产生的)
- [22. 如何降低再次发生的概率](#22-如何降低再次发生的概率)
- [23. 常见故障速查表](#23-常见故障速查表)
- [24. 日常维护建议](#24-日常维护建议)
- [25. 核心原则](#25-核心原则)
- [26. 官方资料](#26-官方资料)

---

# 1. 指南定位

这不是一份单纯的“v2rayN 安装教程”。

更重要的目标是建立一套可长期复用的使用方法：

```text
第一次安装
    ↓
日常使用
    ↓
版本升级
    ↓
配置备份
    ↓
出现异常
    ↓
日志定位
    ↓
安全恢复 / 快速回退
```

核心原则：

> **稳定优先、正式版优先、新版本独立目录、配置先备份、升级必须可回退、故障先看日志。**

---

# 2. v2rayN 是什么

v2rayN 可以简单理解为 Windows 上的代理客户端与管理界面。

它负责：

- 管理节点与订阅；
- 管理系统代理；
- 管理路由规则；
- 调用 Xray、sing-box 等 Core；
- 提供图形界面、托盘菜单和配置管理。

简化结构：

```text
浏览器 / Windows 应用
        ↓
Windows 系统代理
        ↓
v2rayN
        ↓
Xray / sing-box 等 Core
        ↓
代理节点
        ↓
互联网
```

因此看到多个相关后台进程并不一定异常。

`v2rayN.exe` 主要负责 GUI 与管理逻辑，真正承担网络代理工作的可能是 Xray、sing-box 等 Core。

---

# 3. 官方下载与版本选择

只建议从官方 GitHub 下载：

- 项目主页：<https://github.com/2dust/v2rayN>
- Releases：<https://github.com/2dust/v2rayN/releases>

## Latest 与 Pre-release

GitHub Releases 中经常同时存在：

| 类型 | 含义 | 建议 |
|---|---|---|
| `Latest` | 当前正式发布版 | **主力环境优先** |
| `Pre-release` | 预发布 / 测试版本 | 日常主力环境不建议追新 |

推荐原则：

> **只追“Latest 正式版”，不要为了版本号更大就追 Pre-release。**

### 版本状态示例

截至 **2026-08-31**：

- `7.24.8`：GitHub 标记为 `Latest`
- `7.24.9`：GitHub 标记为 `Pre-release`

版本状态会变化，实际升级前应重新查看官方 Releases。

## 安全更新优先级最高

如果 Release Notes 明确写明：

- 安全漏洞修复；
- 严重兼容问题修复；
- Core 关键安全更新；

应优先升级，而不是继续长期使用旧版。

---

# 4. Windows 下载包怎么选

v2rayN 的 Release 资产会随版本调整，下载前应优先查看官方：

<https://github.com/2dust/v2rayN/wiki/Release-files-introduction>

常见 Windows x64 包包括：

```text
v2rayN-windows-64.zip
v2rayN-windows-64-With-Core.zip
v2rayN-windows-64-SelfContained.zip
v2rayN-windows-64-desktop.zip
```

不同版本中这些包的具体组合可能变化。

## 简单理解

### 普通 Windows x64

优先考虑：

```text
v2rayN-windows-64.zip
```

如果该版本说明它不包含 Core，需要自行准备 Core。

### 第一次使用、希望 Core 一起带上

如果当前 Release 提供：

```text
v2rayN-windows-64-With-Core.zip
```

可以优先考虑。

### SelfContained

通常用于不希望另外安装 .NET Desktop Runtime 的场景。

### desktop

`desktop` 版本通常使用 Avalonia UI。

如果只在 Windows 上使用、以稳定为主，可以优先传统 Windows/WPF 版本。

> **注意：文件命名会随项目版本变化，应以当前官方 Release 文件说明为准，不要死记某一个历史文件名。**

---

# 5. 推荐的安装与目录结构

## 不推荐：长期覆盖同一个目录

```text
D:\Tools\v2rayN\
```

然后每次升级都直接覆盖。

这样容易产生：

```text
新版 EXE
+
旧 DLL
+
旧 Core
+
旧 guiConfigs
+
旧数据库
```

后续一旦出问题，很难判断到底是哪一层异常。

## 推荐：一个版本一个目录

```text
D:\Tools\
│
├─ v2rayN-7.24.7\
├─ v2rayN-7.24.8\
└─ v2rayN-backup\
```

核心原则：

> **一个版本 = 一个独立目录。**

升级不是“覆盖”，而是“并行测试”。

---

# 6. 重要目录和文件

## `guiConfigs`

v2rayN 的核心配置目录。

通常可以看到：

```text
guiConfigs/
├─ guiNConfig.json
└─ guiNDB.db
```

### `guiNConfig.json`

主要保存程序配置、界面设置、路由相关参数等。

### `guiNDB.db`

这是非常重要的 **SQLite 数据库**。

通常用于保存：

- 节点；
- 服务器信息；
- 订阅数据；
- 分组等结构化信息。

### `guiLogs`

v2rayN 的 GUI 日志目录。

出现异常时：

> **第一时间看 `guiLogs`。**

很多问题不需要猜，日志会直接给出真正的异常位置。

---

# 7. 第一次安装的标准流程

建议以后固定按照以下顺序：

```text
① 从官方 Releases 下载
        ↓
② 解压到独立目录
        ↓
③ 不导入任何旧配置，先空配置启动
        ↓
④ 确认主窗口正常
        ↓
⑤ 确认托盘图标正常
        ↓
⑥ 再导入订阅 / 节点
        ↓
⑦ 测试节点
        ↓
⑧ 设置系统代理
        ↓
⑨ 浏览器测试
        ↓
⑩ 重启 Windows 后再次测试
```

“**空配置第一次启动**”非常重要。

它可以先证明：

```text
v2rayN 程序本体
Windows 环境
.NET Runtime
GUI 初始化
基础依赖
```

基本正常，再开始导入自己的数据。

---

# 8. 系统代理模式说明

托盘菜单中常见：

```text
清除系统代理
自动配置系统代理
不改变系统代理
PAC 模式
```

这四项决定的是：

> **Windows / 浏览器的流量是否进入 v2rayN。**

简单理解：

```text
Windows / 浏览器
      ↓
【系统代理模式】
      ↓
v2rayN
      ↓
【路由规则】
      ↓
直连 / 代理
```

## 8.1 清除系统代理

含义：

> 清除 Windows 当前由 v2rayN 设置的系统代理，让使用系统代理的程序恢复直连。

例如原来：

```text
127.0.0.1:10808
```

清除后，浏览器不再自动把流量发送给 v2rayN。

适合：

- 暂时不用 v2rayN；
- v2rayN 出现故障；
- 本地代理端口已经没有程序监听；
- 需要恢复 Windows 直连。

需要注意：

> 清除系统代理 ≠ 关闭 v2rayN.exe。

## 8.2 自动配置系统代理

这是普通 Windows 用户最常用的模式。

v2rayN 会自动将 Windows 系统代理指向自己的本地监听端口，例如：

```text
127.0.0.1:10808
```

结构：

```text
Chrome / Edge
      ↓
Windows 系统代理
      ↓
127.0.0.1:10808
      ↓
v2rayN
```

日常使用一般可以保持：

> **自动配置系统代理**

## 8.3 不改变系统代理

含义：

> v2rayN 不修改 Windows 当前的系统代理设置。

它不是“不使用代理”，而是：

```text
Windows 当前是什么代理
v2rayN 就不碰它
```

适合：

- 自己手工管理系统代理；
- 同时使用 Clash、Fiddler、Charles 等工具；
- 软件内部单独指定 SOCKS/HTTP 代理；
- 不希望 v2rayN 自动改 Windows 设置。

普通用户一般不需要长期使用这个模式。

## 8.4 PAC 模式

PAC：

```text
Proxy Auto-Configuration
```

即代理自动配置脚本。

它会先判断：

```text
这个 URL
  ↓
DIRECT？
还是
PROXY？
```

结构：

```text
浏览器
  ↓
PAC 判断
├─ DIRECT → 直接联网
└─ PROXY  → 进入 v2rayN
```

PAC 属于“**入口分流**”。

而下面介绍的路由规则属于：

> **已经进入 v2rayN 之后的内部出站分流。**

这两层不要混为一谈。

---

# 9. 路由模式说明

常见路由预设：

```text
V4-绕过大陆(Whitelist)
V4-黑名单(Blacklist)
V4-全局(Global)
```

它们控制的是：

> **已经进入 v2rayN/Core 的流量，最终直连还是走代理。**

## 9.1 V4-绕过大陆（Whitelist）

日常使用最常见。

核心逻辑：

```text
中国大陆网站 / IP
        ↓
      DIRECT

其他目标
        ↓
      PROXY
```

简单理解：

> **国内直连，其他目标按代理方向处理。**

优点：

- 国内网站速度通常更自然；
- 不浪费代理节点处理国内流量；
- 日常使用体验较均衡。

## 9.2 V4-黑名单（Blacklist）

核心逻辑：

```text
命中代理规则
     ↓
   PROXY

没有命中
     ↓
   DIRECT
```

简单理解：

> **只有明确命中代理规则的目标才走代理，其余默认直连。**

优点：

- 代理流量较少；
- 国内访问通常自然。

风险：

- 规则可能漏掉某些目标；
- 某些国外站点可能因为未命中规则而被直连。

## 9.3 V4-全局（Global）

核心逻辑：

```text
进入 v2rayN 的流量
        ↓
      PROXY
```

可以简单理解成：

> **几乎全部走代理。**

适合：

- 临时测试节点；
- 排查是否是路由规则导致某网站打不开；
- 特殊临时场景。

不建议普通日常长期使用，因为：

- 国内网站也可能走代理；
- 延迟可能增加；
- 浪费节点流量；
- 某些本地服务可能不自然。

## 9.4 “V4”不是 IPv4

菜单里的：

```text
V4-绕过大陆
V4-黑名单
V4-全局
```

这里的 `V4` 是 v2rayN 内置路由预设的版本标识。

不要把它理解为：

```text
IPv4
```

两者不是同一个概念。

---

# 10. 推荐的日常组合

普通 Windows 用户日常使用，可以优先：

```text
系统代理：
自动配置系统代理

路由：
V4-绕过大陆(Whitelist)
```

流程：

```text
Chrome / Edge
      ↓
Windows 系统代理
      ↓
127.0.0.1:本地代理端口
      ↓
v2rayN
      ↓
V4-绕过大陆判断
   ┌───────┴───────┐
   ↓               ↓
国内目标          其他目标
   ↓               ↓
DIRECT           PROXY
```

这是比较容易理解和维护的组合。

---

# 11. 节点与配置备份策略

## 优先备份

推荐优先保存：

```text
① 订阅 URL
② 节点分享链接
③ 手工节点
④ 自定义路由规则
⑤ 当前可用版本安装目录
```

## `guiConfigs` 属于环境备份

整个：

```text
guiConfigs
```

也可以备份。

但需要明确：

> **备份 `guiConfigs` 是为了故障恢复与研究，不代表升级时应该直接把它整个复制给新版。**

### 一个重要区别

```text
订阅 / 节点
=
业务数据备份

guiConfigs
=
运行环境 / 状态备份
```

旧 `guiConfigs` 也可能同时带入：

- 旧数据库结构；
- 历史状态；
- 旧路由设置；
- 已经损坏的数据文件。

---

# 12. 正确的升级策略

推荐升级流程：

```text
查看 GitHub Releases
        ↓
确认是 Latest
        ↓
阅读 Release Notes
        ↓
备份订阅 / 节点 / 关键设置
        ↓
旧版本保持不动
        ↓
新版解压到全新目录
        ↓
空配置第一次启动
        ↓
确认 GUI / 托盘正常
        ↓
导入订阅或节点
        ↓
测试系统代理
        ↓
测试路由
        ↓
正常退出
        ↓
重启 Windows
        ↓
再次测试
        ↓
稳定使用几天
        ↓
最后再决定是否删除旧版
```

## 不建议的升级方式

```text
❌ 直接覆盖旧版本
❌ 新版下载后立即删除旧版
❌ 整个复制旧 guiConfigs
❌ 直接复用可疑的 guiNDB.db
❌ v2rayN 正在运行时复制数据库
❌ 为了版本号更高追 Pre-release
```

---

# 13. 什么时候应该升级

优先级从高到低：

## 13.1 严重安全更新

Release Notes 明确提到：

- 安全漏洞；
- MITM；
- 恶意文件下载风险；
- Core 严重安全问题；

应优先升级。

## 13.2 当前版本已经影响正常使用

例如：

```text
程序频繁崩溃
订阅无法更新
系统代理异常
DNS 异常
Core 无法正常工作
```

可以升级正式版尝试修复。

## 13.3 协议 / Core 兼容性变化

如果服务端、Xray、sing-box 等发生兼容性变化，旧版客户端已经影响连接，也应升级。

---

# 14. 什么时候不要急着升级

如果：

```text
当前版本稳定
+
没有严重安全问题
+
代理正常
+
新版只是增加功能
```

完全可以继续使用当前稳定版本。

推荐：

> **安全更新及时升，功能更新晚一点升。**

尤其不要形成：

```text
7.24.8
↓
看到 7.24.9
↓
立即升级
```

这种“只看版本号”的更新习惯。

---

# 15. 新版测试与回退策略

建议始终保留：

```text
当前主力稳定版
+
上一稳定版
```

例如：

```text
D:\Tools\
├─ v2rayN-7.24.8\      ← 当前主力
└─ v2rayN-previous\    ← 应急回退
```

如果新版出现：

- 启动异常；
- 订阅解析异常；
- TUN 异常；
- DNS 异常；
- Core 异常；

可以：

```text
退出新版
↓
清除错误的系统代理状态
↓
打开上一稳定版
↓
恢复工作
```

不要等到出问题以后才重新去网上找旧版。

---

# 16. 常用故障排查命令

以下命令建议在 PowerShell 中执行。

## 16.1 查看 v2rayN 进程

```powershell
Get-CimInstance Win32_Process |
Where-Object {$_.Name -ieq "v2rayN.exe"} |
Select-Object ProcessId,SessionId,ExecutablePath,CommandLine
```

## 16.2 查看常见本地代理端口

例如检查 10808：

```powershell
netstat -ano | findstr ":10808"
```

如果看到：

```text
LISTENING
```

说明有程序在监听这个端口。

## 16.3 查监听端口对应的进程

假设 PID 为 `12345`：

```powershell
Get-CimInstance Win32_Process -Filter "ProcessId=12345" |
Select-Object ProcessId,Name,ExecutablePath,CommandLine
```

## 16.4 强制结束异常 v2rayN

```powershell
Get-Process v2rayN -ErrorAction SilentlyContinue |
Stop-Process -Force
```

再确认：

```powershell
Get-Process v2rayN -ErrorAction SilentlyContinue
```

没有输出说明已经结束。

## 16.5 查看最新 GUI 日志

```powershell
Get-ChildItem ".\guiLogs" -File -ErrorAction SilentlyContinue |
Sort-Object LastWriteTime -Descending |
Select-Object -First 10 Name,Length,LastWriteTime
```

读取最新日志：

```powershell
$log = Get-ChildItem ".\guiLogs" -File |
Sort-Object LastWriteTime -Descending |
Select-Object -First 1

Get-Content $log.FullName -Tail 100
```

## 16.6 检查 SQLite 文件头

正常 SQLite 数据库开头应该包含：

```text
SQLite format 3
```

检查：

```powershell
Get-Content ".\guiConfigs\guiNDB.db" -Encoding Byte -TotalCount 32 |
Format-Hex
```

正常文件头对应十六进制：

```text
53 51 4C 69 74 65 20 66 6F 72 6D 61 74 20 33 00
```

---

# 17. 通用故障排查流程

建议遵循：

```text
v2rayN 出问题
        ↓
进程是否存在？
   ┌────┴────┐
   ↓         ↓
  NO        YES
   ↓         ↓
执行层问题   GUI / 配置 / Core 问题
   ↓         ↓
检查日志     先看 guiLogs
Runtime       ↓
安全软件     Exception？
执行权限       ↓
            根据异常定位
```

## 如果进程存在但界面没有出现

优先：

```text
① guiLogs
② guiConfigs
③ guiNDB.db
④ guiNConfig.json
```

而不是一上来：

```text
重装 .NET
关闭 Defender
关闭防火墙
修改注册表
```

---

# 18. 实战案例：进程存在但界面和托盘不显示

这是一次真实排查案例。

## 18.1 故障现象

新版 v2rayN 出现：

```text
双击 v2rayN.exe
↓
没有主窗口
↓
没有托盘图标
↓
任务管理器中却存在 v2rayN.exe
```

最初很容易误以为：

> v2rayN 根本没有启动。

实际情况是：

> **进程已经启动，但程序初始化没有完成。**

## 18.2 第一阶段排查

检查了：

- v2rayN 进程；
- Windows Defender；
- AppLocker；
- Code Integrity；
- .NET Runtime；
- 10808 端口；
- 旧版 v2rayN；
- Windows 事件日志。

这些方向都没有发现能够解释故障的直接证据。

## 18.3 真正的突破口：`guiLogs`

查看最新：

```text
guiLogs
```

发现错误：

```text
System.Data.SQLite.SQLiteException:
file is not a database
```

调用链显示程序在：

```text
AppManager.InitApp()
↓
SQLiteHelper.CreateTable()
↓
SQLiteConnection.GetTableInfo()
↓
SQLiteException
```

阶段失败。

这说明：

> v2rayN 在初始化本地 SQLite 数据库时失败。

---

# 19. SQLite 数据库损坏如何确认

检查：

```text
guiConfigs\guiNDB.db
```

正常 SQLite 文件头应该是：

```text
SQLite format 3
```

对应：

```text
53 51 4C 69 74 65 20 66 6F 72 6D 61 74 20 33 00
```

但故障文件开头完全不是 SQLite 格式。

因此可以确认：

> **`guiNDB.db` 已经不是有效 SQLite 数据库。**

这和日志中的：

```text
file is not a database
```

完全对应。

---

# 20. SQLite 数据库损坏如何处理

如果节点 / 订阅已有备份，通常没有必要抢救损坏数据库。

推荐：

## 20.1 结束所有 v2rayN 进程

```powershell
Get-Process v2rayN -ErrorAction SilentlyContinue |
Stop-Process -Force
```

确认：

```powershell
Get-Process v2rayN -ErrorAction SilentlyContinue
```

应无输出。

## 20.2 先备份配置

```powershell
Copy-Item ".\guiConfigs" ".\guiConfigs-backup" -Recurse
```

## 20.3 隔离整个旧配置目录

```powershell
Rename-Item ".\guiConfigs" "guiConfigs-broken"
```

不建议直接删除。

## 20.4 重新启动

```powershell
.\v2rayN.exe
```

v2rayN 会重新创建新的：

```text
guiConfigs
```

如果此时：

- 主窗口恢复；
- 托盘图标恢复；
- 程序正常运行；

则可以正式确认：

> **问题来自旧配置目录，而不是程序本体、Windows 或 Runtime。**

## 20.5 再重新导入节点 / 订阅

如果数据有备份：

```text
全新 guiConfigs
↓
重新导入订阅 / 节点
↓
测试
```

通常比修复已损坏 SQLite 数据库更可靠。

---

# 21. 这类数据库异常可能是如何产生的

这里要区分：

## 已确认事实

本案例中可以确认：

```text
guiNDB.db 已经不是有效 SQLite 数据库
```

## 不能确认的部分

仅凭普通 Windows 日志，无法精确证明：

> 是哪一次点击、复制或关机操作导致文件损坏。

因为 Windows 默认并不会记录普通文件每一次写入的来源程序。

## 比较值得怀疑的原因

### 21.1 升级 / 迁移时错误复制或覆盖

例如：

```text
旧 guiConfigs
↓
复制到新版
↓
文件复制不完整 / 来源文件异常
↓
新版继续使用损坏数据库
```

这是值得优先怀疑的场景。

### 21.2 新旧版本配置混用

例如：

```text
新版 EXE
+
旧版数据库
+
旧 Core
+
部分新文件
```

形成混合环境。

### 21.3 数据库正在写入时异常结束

例如：

```text
更新订阅
↓
SQLite 正在写数据
↓
强制结束程序 / 异常关机 / 断电
↓
数据异常
```

### 21.4 软件版本迁移 Bug

不能完全排除某些版本在数据库迁移过程中存在问题。

### 21.5 文件系统异常

概率通常较低，但磁盘 / 文件系统异常也可能造成数据损坏。

## 本案例没有证据支持的原因

当前没有证据显示故障来自：

```text
Windows 后台监控
Windows 域管理
Chrome 企业管理
Defender 主动破坏数据库
AppLocker
Code Integrity
10808 端口冲突
PowerShell 查询命令
```

特别是：

```powershell
Get-Service
Get-ScheduledTask
Get-NetTCPConnection
netstat
reg query
dsregcmd /status
Get-CimInstance
Get-WinEvent
```

这些主要属于读取 / 查询命令，本身不会修改 `guiNDB.db`。

---

# 22. 如何降低再次发生的概率

建议长期遵守：

```text
✅ 一个版本一个独立目录
✅ 升级前备份订阅和重要节点
✅ 新版先空配置启动
✅ 新版稳定后再迁移数据
✅ 正常退出 v2rayN 再进行目录复制
✅ 不在程序运行时复制 guiNDB.db
✅ 不直接覆盖安装
✅ 不无脑复制整个旧 guiConfigs
✅ 新版经过一次 Windows 重启测试
✅ 上一稳定版暂时保留
```

---

# 23. 常见故障速查表

| 故障现象 | 第一检查位置 |
|---|---|
| 双击完全无进程 | Runtime / 安全软件 / 执行权限 / 日志 |
| 进程存在但没界面 | **guiLogs / guiConfigs** |
| 托盘图标不显示 | guiLogs / GUI 初始化 / 配置 |
| `file is not a database` | **guiNDB.db** |
| 节点全部无法连接 | Core / 节点 / 网络 |
| 浏览器突然全部断网 | 系统代理 / 本地监听端口 |
| 10808 无监听 | v2rayN/Core 是否真正运行 |
| 国内网站明显变慢 | 是否误用了 Global |
| 部分国外站点打不开 | 路由规则 / Blacklist |
| 重启后突然异常 | 配置初始化 / 自动启动 / 数据库 |
| 新版问题多 | 回退上一稳定版 |

---

# 24. 日常维护建议

v2rayN 不需要高频维护。

推荐：

```text
平时
↓
稳定使用
↓
无需反复调整
```

偶尔：

```text
查看官方 Releases
↓
确认是否有新的 Latest
↓
阅读 Release Notes
```

### 有安全更新

```text
尽快升级
```

### 只是普通功能更新

```text
观察几天
↓
没有明显问题
↓
再决定是否升级
```

### Pre-release

主力环境一般不追。

---

# 25. 核心原则

最后可以只记住这 10 条：

1. **只从官方 GitHub 下载。**
2. **主力环境优先 Latest，不追 Pre-release。**
3. **安全更新及时升级，普通功能更新不必追新。**
4. **每个版本使用独立目录，不覆盖升级。**
5. **新版本第一次必须空配置启动。**
6. **优先备份订阅与节点，不要只依赖 `guiNDB.db`。**
7. **出现异常先查看 `guiLogs`。**
8. **进程存在但界面不显示，优先检查配置和数据库。**
9. **配置损坏时优先隔离并重建，不盲目修数据库。**
10. **新版经过实际使用和 Windows 重启验证后，再删除旧版。**

一句话概括：

> **稳定优先，可回退优先；先看日志，再做修改。**

---

# 26. 官方资料

## 官方项目

<https://github.com/2dust/v2rayN>

## Releases

<https://github.com/2dust/v2rayN/releases>

## Release 文件说明

<https://github.com/2dust/v2rayN/wiki/Release-files-introduction>

## Wiki

<https://github.com/2dust/v2rayN/wiki>

---

## 版本说明

本文档中的具体版本号、文件名和 Release 状态属于示例或编写时状态。

例如截至 **2026-08-31**：

```text
7.24.8 → Latest
7.24.9 → Pre-release
```

未来升级时必须重新查看官方 Releases，不应仅根据本文档中的历史版本号做决定。

---

## 免责声明

本文档用于记录 v2rayN 在 Windows 环境中的安装、升级、备份与故障排查方法。

涉及代理、网络访问与相关服务时，请遵守所在地法律法规、所在网络环境政策以及相关服务条款。
