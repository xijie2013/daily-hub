# Daily Hub · 每日中枢

任务看板 + 生理与心理状态追踪，合二为一的单文件 PWA。
取代原来的 `life-work-kanban`（生活工作助手）和 `health-tracker`（健康追踪）。

- 线上地址（部署后）：https://xijie2013.github.io/daily-hub/
- 数据存储：私有 GitHub Gist `337496bdc9d3c0c145fb756648dbf283` 中的 **`daily-hub-data.json`**
- 凭据：Gist ID + PAT token 已用 **PIN 加密内置**在 index.html 里（PBKDF2-SHA256 600k 迭代 + AES-256-GCM）。
  清缓存 / 换设备后只需重输 PIN，不用再填 Gist ID 和 token。

## 一次性部署步骤

1. **建仓库**：GitHub 上新建 public 仓库 `daily-hub`（不要加 README）。
2. **本地初始化**（在本文件夹打开终端，或用 GitHub Desktop 添加此文件夹）：
   ```
   git init
   git add -A
   git commit -m "init daily-hub"
   git branch -M main
   git remote add origin https://github.com/xijie2013/daily-hub.git
   git push -u origin main
   ```
3. **开启 Pages**：仓库 Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`。
4. 1-2 分钟后访问 https://xijie2013.github.io/daily-hub/ ，输入 PIN 解锁。
5. **首次解锁会自动迁移**：app 发现 gist 里还没有 `daily-hub-data.json`，会自动读取旧的
   `kanban-data.json`（同一 gist）和旧 health gist 的 `health-data.json`，在浏览器里合并后写入新文件。
   旧文件原样保留作为备份，之后不再更新。
6. 手机上打开同一网址 → 「添加到主屏幕」即可作为 App 使用（同样输一次 PIN）。

以后更新代码：改完文件后双击 `deploy.bat`。

## 功能

- **今日**：到期提醒条 · 进行中任务速览（可一键完成）· 生理打卡（睡眠/精力/体重/标签/补剂）· 心理打卡（情绪/压力/专注 1-5 分 + 心情日记）
- **看板**：任务 / 提醒 两个子页——三栏任务看板（手机上按状态切换，沿用「每项目一个进行中任务」自动规则和依赖优先级）+ 提醒管理
- **日志**：日志 / 检测 / 补剂 三个子页——历史每日记录、实验室检测记录、补剂管理
- **总结**：心理状态多线趋势、睡眠、精力、体重、经期分析、标签统计、任务概况

## 安全说明

- 页面是公开的，但内置凭据是密文；不知道 PIN 无法还原 token。
- PIN 是 6 位数字：对随意浏览者足够，但**有决心的攻击者理论上可离线暴力破解**。
  token 只有 gist 权限，最坏损失范围 = gist 数据。介意的话可换更长的密码（找 Claude 重新生成密文即可）。
- 若 token 泄漏/更换：GitHub 上 revoke + 重新生成后，在 app 右上角 ⚙️ 设置→凭据 里手动覆盖本机凭据，
  并找 Claude 用新 token 重新生成 index.html 里的加密 blob（否则新设备解锁得到的还是旧 token）。
- 修改 PIN：同样需要找 Claude 重新生成加密 blob 并重新部署。

## Claude 集成

Claude 会话可直接读写 gist 中的 `daily-hub-data.json`（token 在
`C:\Users\xijie\OneDrive\Claude\life\scheduler\.kanban\token.txt`），
数据结构 = 旧 kanban 字段（tasks/reminders）+ 旧 health 字段（dailyLogs/supplements/labResults/tagDefs），
dailyLogs 每条新增 `mood` / `stress` / `focus`（0=未记录，1-5）和 `journal`（字符串）。
