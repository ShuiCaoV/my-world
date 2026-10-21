# MY WORLD

本地工作区 `D:\deep seek工作区` 与 GitHub 仓库
[ShuiCaoV/MY-WORLD](https://github.com/ShuiCaoV/MY-WORLD) 的关联说明。

## 这台电脑的 Git 环境

- 远程使用 SSH：`git@github.com:ShuiCaoV/MY-WORLD.git`
- 密钥：`~/.ssh/id_ed25519`（已登记到 GitHub 账号）
- 提交身份：`蔡宏伟 <345419716+ShuiCaoV@users.noreply.github.com>`
- SSH 客户端：`C:/Windows/System32/OpenSSH/ssh.exe`（见全局 `core.sshCommand`）
- 提交身份按远程分流：`~/.gitconfig` 里的 `includeIf` 只在远程指向 github.com 时
  切到 GitHub noreply 邮箱，Gitee 仓库仍用 Gitee 专用地址

## 日常用法

```bash
git status              # 看改了什么
git add -A              # 暂存全部改动
git commit -m "说明"     # 提交
git push                # 推到 GitHub
git pull                # 从 GitHub 拉回最新
```

---

## 这台电脑的 DSH 环境

- 客户端形态：DeepSeek Harness 桌面端（Electron），读取的是 **`desktop` profile**：
  `C:\Users\蔡宏伟\.dsh\profiles\desktop`
- 命令行入口（未加入 PATH，需写全路径）：

  ```powershell
  $cli = 'C:\Users\蔡宏伟\AppData\Local\Programs\DeepSeek Harness\resources\runtime\cli\bin\dsh.cmd'
  ```

- 安装目录：`C:\Users\蔡宏伟\AppData\Local\Programs\DeepSeek Harness`
- 已知限制：
  - `dsh --profile desktop --dump-config` 会被拒绝（`profile "desktop" is managed exclusively by the Electron application`），
    但 `dsh plugin --profile desktop ...` 可用；
  - 命令行里 `dsh` 不在 PATH，直接敲会报「无法识别」。

## 已安装插件

### dsh-whale-widget v0.4.2 —— 右下角余额小鲸鱼

- 来源：[MeteorNOX/DeepSeek-Balance-Whale-Widget](https://github.com/MeteorNOX/DeepSeek-Balance-Whale-Widget)（MIT）
- 功能：常驻右下角显示余额、今日已用、每轮消耗（含峰谷价）；余额预警/今日预算气泡；
  自定义泡泡点击序列；30+ 厂商余额模板；任务结束音效
- 本机安装方式：**离线 `link:` 安装**（npm 源直连会卡住，别再用 `add dsh-whale-widget@x.y.z`）

  ```powershell
  & $cli plugin --profile desktop add --offline 'link:D:\deep seek工作区\plugins\dsh-whale-widget'
  ```

- 源码目录（`link:` 安装，**不能移动、不能删除**）：
  `D:\deep seek工作区\plugins\dsh-whale-widget`
  - 该目录已在 `.gitignore` 中忽略，不会进仓库
  - 删掉或改名 → 插件失效，需要重新 `add`
- 运行时数据（都写在 `~/.dsh`）：`.dshw-size.json`（外观）、`.dshw-usage.json`（记账）、
  `.dshw-turn.json`（每轮序号）、`.dshw-bubble.json`、`.dshw-api.json`（不含密钥）
- 余额来源（二选一）：
  1. DSH 账号登录态（本机 `provider: deepseek-account`，登录后在「设置 → 账号与余额」）
  2. 或配置 `DEEPSEEK_API_KEY`
- 升级：

  ```powershell
  # 拉新版源码覆盖 D:\deep seek工作区\plugins\dsh-whale-widget 后，重新登记一次
  & $cli plugin --profile desktop remove dsh-whale-widget
  & $cli plugin --profile desktop add --offline 'link:D:\deep seek工作区\plugins\dsh-whale-widget'
  # 然后完全退出客户端（含托盘）重开，浏览器 Ctrl+F5
  ```

- 卸载：

  ```powershell
  & $cli plugin --profile desktop remove dsh-whale-widget
  ```

- 自检：宿主机 `~/.dsh/.dshw-turn.json` 存在且 `seq` 随对话递增；
  前端在 `%APPDATA%\@deepseek-ai\dsh-desktop\Local Storage\leveldb` 里写入 `dshw-pos`

### 安装前的 profile 备份

`C:\Users\蔡宏伟\.dsh\backups\desktop-profile-20261021-185025`

含 `package.json`、`pnpm-lock.yaml`、`cordis.patch.yml`、`cordis.yml`、`pnpm-workspace.yaml`。
整体复位：

```powershell
Copy-Item 'C:\Users\蔡宏伟\.dsh\backups\desktop-profile-20261021-185025\*' `
          'C:\Users\蔡宏伟\.dsh\profiles\desktop\' -Force
```

## 换机器复现清单

1. 装 Git，配 SSH 密钥并登记到 GitHub，`core.sshCommand` 指向 Windows 自带 OpenSSH
2. 克隆本仓库到工作区，`git config` 检查提交身份
3. 装 DSH 桌面端，确认 profile 为 `desktop`
4. 按上面「已安装插件」小节重装鲸鱼挂件（源码目录放在工作区 `plugins/` 下，保持路径不变）
5. 登录 DSH 账号或配置 `DEEPSEEK_API_KEY`，确认余额可显示

## 成本提示

- DeepSeek 高峰时段：北京时间工作日 9:00–12:00、14:00–18:00；
  其余时段（含周末、法定节假日）为空闲时段，单价减半
- 比挑时段更省的是**缓存命中**：`deepseek-flash` 缓存命中输入 0.02 元/百万 tokens，
  未命中 1 元/百万 tokens（差 50 倍）——保持前缀稳定、少开新会话
