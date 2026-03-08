--
name: 云上蜕壳
description: OpenClaw 自动升级技能，通过临时交换空间解决内存不足问题，安全完成版本升级。
author: 大钳子
version: 1.4.0
homepage: https://docs.openclaw.ai
triggers:
  "升级 openclaw"
  "更新大钳子"
  "云上蜕壳"
metadata: {"clawdbot":{"emoji":"🦞","requires":{"bins":["bash","pnpm","dd","swapon","swapoff","openclaw","curl"]}}}
--

云上蜕壳 (Cloud Molting)

OpenClaw 安全升级技能，专为低内存环境设计。通过创建临时交换空间确保升级过程顺利完成。

功能特性

🦞 自动检测内存状态：识别系统资源限制
💾 智能交换空间管理：自动创建和清理临时交换空间  
🔧 安全升级流程：使用官方更新机制，包含回退保护
📊 完整状态报告：提供升级前后的详细状态对比
⚡ 一键执行：单命令完成整个升级过程
✅ 版本准确性保障：强制使用 `openclaw --version` 获取真实版本，避免缓存错误
🌐 智能在线版本检测：自动搜索最新版本，仅在有更新时才执行升级

使用方法

命令行使用
```bash

执行智能安全升级（自动检测在线最新版本）
uv run {baseDir}/scripts/cloud-molting.py upgrade

仅检查在线最新版本（不执行升级）

uv run {baseDir}/scripts/cloud-molting.py check-online

强制升级（跳过在线检查，直接升级）
uv run {baseDir}/scripts/cloud-molting.py upgrade --force

验证版本准确性
uv run {baseDir}/scripts/cloud-molting.py verify-version

清理临时资源（通常不需要手动调用）
uv run {baseDir}/scripts/cloud-molting.py cleanup
```

聊天交互使用
直接在聊天中说：
"升级大钳子"
"执行云上蜕壳" 
"更新 OpenClaw"
"检查最新版本"
"检查真实版本"

升级流程

1. 在线版本检测：使用 searxng 搜索 OpenClaw 最新版本信息
2. 本地版本验证：使用 `openclaw --version` 获取准确本地版本（避免 session_status 缓存错误）
3. 智能版本对比：比较本地版本与在线最新版本
4. 条件决策：
   如果本地版本 >= 在线版本 → 跳过升级，报告"已是最新版本"
   如果本地版本 < 在线版本 → 继续执行升级流程
5. 资源准备：创建 2GB 临时交换空间（如内存 < 4GB）
6. 执行升级：使用 `pnpm add -g openclaw@latest` 安全更新
7. 验证结果：确认新版本正常工作，再次验证版本准确性
8. 清理资源：移除临时交换空间
9. 报告状态：向用户汇报最终结果（升级成功或已是最新）

安全保障

✅ 无破坏性：不会删除用户数据或配置文件
✅ 可回退：如升级失败可重新安装指定版本
✅ 资源保护：自动清理临时文件，避免磁盘占用
✅ 权限安全：仅使用必要权限，避免系统风险
✅ 版本准确性：始终使用命令行验证，避免显示错误版本
✅ 智能升级：仅在真正需要时才执行升级，避免不必要的操作
✅ 网络容错：网络不可用时自动降级到本地检查模式

配置选项

可通过环境变量自定义行为：
`CLOUD_MOLTING_SWAP_SIZE_MB`：交换空间大小（默认 2048）
`CLOUD_MOLTING_TIMEOUT_MIN`：升级超时时间（默认 10 分钟）
`CLOUD_MOLTING_DRY_RUN`：仅模拟升级（不实际执行）
`CLOUD_MOLTING_FORCE_VERSION_CHECK`：强制版本验证（默认 true）
`C
LOUD_MOLTING_ONLINE_CHECK`：启用在线版本检测（默认 true）
`CLOUD_MOLTING_SEARCH_TIMEOUT_SEC`：在线搜索超时时间（默认 30 秒）

适用环境

阿里云 ECS：特别优化的低内存实例
树莓派/ARM 设备：资源受限的嵌入式环境  
容器环境：Docker/Podman 中的 OpenClaw 实例
任何 Linux 系统：支持标准 swap 工具的环境

故障排除

常见问题及解决方案

问题：无法找到 swapon/mkswap 命令
原因：工具不在标准 PATH 中或未安装
解决方案：
  ```bash

检查工具位置
  which swapon mkswap || find /usr -name "swapon" -o -name "mkswap"

如果未安装，在 CentOS/RHEL 中运行：
  sudo yum install util-linux

在 Ubuntu/Debian 中运行：
  sudo apt-get install util-linux
  ```

问题：权限不足创建交换空间
原因：需要 root 权限执行 swapon
解决方案：确保以管理员权限运行，或使用 sudo

问题：云服务器禁用 swap
原因：某些云服务商默认禁用 swap 功能
解决方案：直接跳过交换空间创建，尝试直接升级

问题：版本显示错误或不一致
原因：session_status 可能显示缓存的旧版本信息，而非真实版本
解决方案：
  ```bash

始终使用以下命令获取真实版本（强制规则）
  openclaw --version

如果发现版本不一致，执行版本验证修复
  uv run {baseDir}/scripts/cloud-molting.py verify-version

手动强制刷新版本缓存
  openclaw gateway restart
  ```

问题：在线版本检测失败或超时
原因：网络连接问题或搜索引擎不可用
解决方案：
  ```bash

禁用在线检查，使用本地模式
  CLOUD_MOLTING_ONLINE_CHECK=false uv run {baseDir}/scripts/cloud-molting.py upgrade

或者增加超时时间
  CLOUD_MOLTING_SEARCH_TIMEOUT_SEC=60 uv run {baseDir}/scripts/cloud-molting.py upgrade
  ```

手动执行步骤
如遇问题，可手动执行以下步骤：
```bash

检查当前真实版本（关键步骤）
openclaw --version

手动检查在线最新版本
curl -s "https://github.com/openclaw/openclaw/releases/latest" | grep -o 'v[0-9]*\.[0-9]*\.[0-9]*' | head -1

手动创建交换空间
sudo dd if=/dev/zero of=/tmp/swapfile bs=1M count=2048
sudo mkswap /tmp/swapfile
sudo swapon /tmp/swapfile

手动升级（仅当确认有新版本时）
pnpm add -g openclaw@latest

验证升级后的真实版本
openclaw --version

清理交换空间
sudo swapoff /tmp/swapfile
sudo rm /tmp/swapfile
```

版本历史

1.4.0：品牌重塑，从"空中蜕壳"更名为"云上蜕壳"，英文名从"Air Molting"改为"Cloud Molting"；所有配置选项前缀更新为 CLOUD_MOLTING
1.3.0：新增智能在线版本检测功能，自动搜索最新版本并仅在需要时执行升级；添加网络容错和配置选项
1.2.0：新增版本准确性保障功能，强制使用 `openclaw --version` 获取真实版本，解决版本显示错误问题；增强故障排除指南
1.1.0：改进故障排除，添加工具路径检查和权限处理
1.0.0：初始版本，支持基础升级功能
```
