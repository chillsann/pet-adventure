# 宠物大冒险 🐱🛵

一个 9 岁小朋友用 AI 做出来的游戏：宠物 + 载具 + 6 大主题随机关卡 + 收集礼物，横版跑酷平台跳跃。
**在线玩**：<https://chillsann.github.io/pet-adventure/>（推送 main 后自动生效）

## 最新改动

### 小朋友的 5 条修改意见（全部实现）

| # | 意见 | 实现 |
|---|------|------|
| ① | 动物大一点、迪士尼风、头大身小 | 视觉放大 1.2×（碰撞盒不变，跳跃手感零影响），Q 版比例保持 |
| ② | 礼物开出装饰品 + 完美通关攒大房子 + 小屋互动 | 7 件装饰品按累计礼物解锁；整关无伤 → 房子 +1，3 段建成；小屋里点电视/饭碗/小床，宠物就去做，底部按钮换装 |
| ③ | 好听的背景音乐，每关不同 | 6 大主题 6 首曲子（程序化音序器），HUD 喇叭可静音 |
| ④ | 路要长、难度递增、新障碍 | 关卡 3200+300×关数 px；三角齿轮 lv4+ / 挡路小怪兽 lv6+ |
| ⑤ | 撞到就死、死了回选宠重头 | **改回三心机制**（家长反馈一击死亡太难）—— 撞障碍/掉坑各扣 1 心，扣光才死；死亡界面看广告复活/再来一次/返回首页 |

### 家长追加的留存与变现（全部实现）

| # | 要求 | 实现 |
|---|------|------|
| 1 | 改回三心别太难 | 三心机制 + 掉坑回检查点 + 广告复活满血；E2E 校验：撞一次扣 1 心不死、扣光才死 |
| 2 | 广告复活 + 小游戏变现 | 广告接口已就绪（web 模拟 1.2s 走通流程；微信/抖音小游戏端填 adUnitId 即可接真实激励视频）。详见 [docs/上线小游戏与广告变现.md](docs/上线小游戏与广告变现.md) |
| 3 | 2.5D 画风升级 | 动态光晕（太阳 radial gradient）、双层柔和投影（空中缩小变淡）、屏幕暗角（vignette），保留孩子手绘 2D 角色与可爱风格，手机零性能压力 |
| 4 | 留住人、成人也爱玩 | **4 大留存系统**：① 评分+连击（S/A/B/C 评级，冲 S 是目标）+ 完美通关记录 + 通关数累计；② 本地排行榜 top10（金银铜名次）；③ 每日挑战（按日期 seed 固定关卡，全球同关冲分）；④ 8 项成就（首次通关/无伤/连击/S 评价/老玩家等） |

> 设计取舍说明：「奥德赛级 3D」单靠 AI 在沙箱里做不出，且手机小游戏跑不动那个画质；
> 「2.5D 精致化」是性价比最高的方案（成本低/见效快/不推翻手绘/性能零压力）。
> 画风从来不是留人的关键——"羊了个羊"纯 2D 也爆了。真正能留住人的是玩法钩子和成长系统，已落地。

### E2E 自动化验收（38 项全绿）

```bash
npm i playwright && npx playwright install chromium   # 首次
node tools/e2e-kid-requests.mjs
```
```

## 目录结构

```
pet-adventure/
├── index.html            # 游戏本体（单文件：内核 + 手绘角色库 + Web 适配层）
├── tools/
│   ├── e2e-kid-requests.mjs  # E2E：5 条意见验收（Playwright）
│   └── verify-levels.mjs 等  # 旧版离线验证器（针对 legacy 内核）
├── legacy/               # 上一代拆分架构存档（src/ + build.mjs + 小游戏工程）
└── docs/                 # 优化报告 + 设计文档 + 视觉检查截图
```

> 历史说明：本仓库曾经历「拆分架构重构」，后以小朋友持续迭代的单文件版为唯一基准，
> 旧架构完整保存在 `legacy/`（含 git 历史）。改代码请直接改 `index.html`。

## 上传 GitHub（在本机有网的环境执行）

```bash
cd pet-adventure
git add index.html README.md tools/ docs/
git commit -m "小朋友的 5 条意见：一击死亡/新障碍/BGM/装饰品与小屋/宠物放大"
git push origin main           # 弹窗登录：用户名填 GitHub 用户名，密码填 Token
```

推送后刷新 <https://chillsann.github.io/pet-adventure/> 即可玩到新版。

**开 GitHub Pages 让孩子直接玩**：仓库 Settings → Pages → Source 选 `main` 分支根目录，保存。
之后浏览器访问 `https://<你的用户名>.github.io/pet-adventure/` 即玩，手机也能开。

## 微信/抖音小游戏（后续步骤，参考 legacy/）

拆分架构与小游戏工程已存档在 `legacy/`（含 `build.mjs`、`minigame/`）。
若要上线小游戏：把新版 `index.html` 的内核段落移植进 `legacy/src/core.js` 的对应位置再走原构建流程，或直接以单文件接入 Canvas 宿主。

## 数值 tuning 速查

| 参数 | 位置 | 当前值 | 说明 |
|------|------|--------|------|
| 关卡长度 | `generateLevel` | 3200 + 300×lv | [PLACEHOLDER] 目标单局 30-60s，待 playtest |
| 新障碍解锁 | `HAZARDS.unlock` | patrol 2 · saw/fire 3 · slider/triGear 4 · gate 5 · blocker 6 | 分档出新鲜感 |
| 装饰品解锁 | `ACCESSORIES.need` | 8 → 80 累计礼物 | 确定性表，孩子可预期 |
| 房子建成 | `onCleared` | 完美 ×3 关 | 每段 +1，不倒退 |
| BGM 音量 | `createSfx` | 旋律 0.062 / 低音 0.085 | 不盖过音效 |

## 安全提醒

⚠️ 之前在对话里粘贴过的 GitHub Token 视为已泄露：**推送完成后立即去
https://github.com/settings/tokens 撤销（Revoke）它**，需要时再生成新的。
本仓库任何文件都不包含 Token，`git push` 时手动输入即可。
