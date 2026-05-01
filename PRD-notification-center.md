# PRD + 设计规范 — Inspova 通知中心

**版本：** 1.0 · **分支：** profile-page-r1 · **状态：** 设计完成，待开发交付

---

## 1. 概述

通知中心是 Inspova 创作者获取平台互动信号的核心入口，以滑出面板的形式锚定在左侧导航栏旁边。支持 8 种通知类型，按"最新"与"更早"两个优先级分区展示。本文档涵盖产品需求与像素级设计规范，用于开发交付。

---

## 2. 相关人员

| 姓名 | 角色 | 职责 |
|------|------|------|
| 待定 | 产品经理 | 需求负责人 |
| 待定 | 设计负责人 | 设计规范负责人 |
| 待定 | 前端工程师 | 功能实现 |
| 待定 | 后端工程师 | 通知 API 与推送 |

---

## 3. 背景

Inspova 是一个以创意内容为核心的社交平台，用户可以创作、remix、收藏并关注其他创作者的作品。随着内容图谱不断扩张，创作者没有统一的地方了解自己的内容是否被互动——比如是否有人 remix 了自己的作品、顶级创作者是否 fork 了自己的内容、或者某个运营 campaign 正在趋势中。

缺乏完善的通知系统，创作者会错过激励他们持续创作的社交动力。通知中心通过一个不打断创作流的滑入面板，将及时、有上下文的信号推送给用户，无需离开当前页面。

---

## 4. 目标

**核心目标：** 让创作者实时感知平台相关活动，提升日活跃度和创作者留存。

**关键结果：**
- 上线 30 天内通知面板打开率 ≥ 40%
- Campaign 类通知的 dismiss 率 ≤ 15%（设计层面不提供 dismiss 入口）
- 通知内"回关"CTA 的转化率 ≥ 25%
- 与通知中心有互动的用户，D7 留存率比未使用用户高 ≥ 10 个百分点

---

## 5. 目标用户

**主要用户：** Inspova 活跃创作者（发布过 ≥ 1 件作品的用户），希望实时追踪自己作品的互动情况并发现社交连接。

**次要用户：** 关注他人的普通用户，希望在不离开主界面的情况下了解趋势 campaign 或内容动态。

**约束条件：**
- v1 仅支持桌面端 Web
- 通知为只读，v1 内唯一可操作的 CTA 是"回关"按钮
- v1 不含推送通知或邮件通知，仅支持站内面板

---

## 6. 价值主张

| 用户诉求 | 获得的收益 | 避免的痛点 |
|---------|-----------|-----------|
| 知道自己的作品被 remix 或收藏 | 获得认可感，保持创作动力 | 不用手动翻个人主页查看动态 |
| 发现谁关注了我，拓展社交圈 | 一键回关，高效增粉 | 错过社交连接机会 |
| 了解当前流行的 campaign | 在热度最高时加入病毒式传播 | 错过 campaign 窗口期 |
| 清理已读旧通知 | 保持界面整洁、专注 | 被过期通知干扰 |

---

## 7. 解决方案

### 7.1 布局与面板行为

```
关闭状态：
┌──────┬────────────────────────────────────────┐
│ 64px │          主内容区                       │
│ 侧栏 │          margin-left: 64px             │
└──────┴────────────────────────────────────────┘

打开状态：
┌──────┬──────────────┬──────────────────────────┐
│ 64px │ 通知面板      │   主内容区                │
│ 侧栏 │ 400px        │   margin-left: 464px      │
└──────┴──────────────┴──────────────────────────┘
```

**触发方式：**
- 点击侧栏铃铛图标 → 切换面板开/关
- 面板内 X 按钮 → 收起面板
- 铃铛图标上的徽标（7px 紫点 #a78bfa）：有未读通知时显示，面板打开后隐藏

**动画参数：**
- 打开：`translateX(calc(-100% - 74px))` → `translateX(0)`
- 时长：300ms
- 缓动：`cubic-bezier(0.4, 0, 0.2, 1)`
- 主内容区 margin-left 同步过渡：64px → 464px，参数相同
- `visibility`：打开时立即变为 visible（无延迟）；关闭时延迟 300ms 后变为 hidden

---

### 7.2 面板头部规范

| 属性 | 值 |
|------|-----|
| 内边距 | 上 28px · 左 24px · 右 16px · 下 16px |
| 标题文案 | "Notification" |
| 标题样式 | #fff · 17px · font-weight 600 |
| 关闭按钮尺寸 | 30×30px · border-radius 8px |
| 关闭按钮图标 | 17×17px · 默认色 #666 · hover 色 #fff |

---

### 7.3 通知列表结构

列表垂直滚动（隐藏滚动条），分两个区域：

```
[ 最新 ]          ← 分区标签
  通知条目
  通知条目
  ...
──────────────    ← 1px 分隔线 #1e1e1e
[ 更早 ]          ← 分区标签
  通知条目
  通知条目
```

**分区标签样式：** 12px · font-weight 500 · color #555 · padding 6px 24px 4px · letter-spacing 0.03em

---

### 7.4 通知条目基础结构

```
┌──────────────────────────────────────────────────┐
│  [图标]  [文字区]              [缩略图] [点] [×]  │
└──────────────────────────────────────────────────┘
```

| 区域 | 规范 |
|------|------|
| 条目内边距 | 上下 11px · 左右 20px |
| 条目悬停背景 | rgba(255,255,255,0.04) |
| 图标与文字间距 | 10px |
| 图标容器 | 34×34px · border-radius 50% · 背景 #1e1e1e · 图标 15×15px · 颜色 #999 |
| 主文字 | 13px · font-weight 500 · #e8e8e8 · line-height 1.4 |
| 副文字 | 11.5px · #555 · 超出截断 |
| 单张缩略图 | 42×42px · border-radius 7px · 背景占位 #252525 · object-fit cover |
| 未读紫点 | 7px · border-radius 50% · 背景 #a78bfa |
| Dismiss 按钮 | 22×22px · border-radius 50% · 颜色 #666 · 默认透明度 0 |

**Dismiss 按钮显示规则：**
- 默认：`opacity: 0`
- 父级条目 hover 时：`opacity: 1`，过渡 150ms
- Dismiss 按钮自身 hover：背景 rgba(255,255,255,0.08) · 颜色 #bbb

---

### 7.5 八种通知类型详细规范

#### 类型 1 — 被 Remix（最新区）
- **图标：** Remix 箭头
- **主文案：** `{用户名} remixed your version`
- **副文案：** `"{作品名}" · {时间}`
- **右侧区域：** 单张缩略图 + 紫点 + Dismiss
- **所属区域：** 最新

#### 类型 2 — 内容趋势（最新区）
- **图标：** 火焰
- **主文案：** `Your remix chain is trending 🔥`
- **副文案：** `+{N} remixes today, keep it going · {时间}`
- **右侧区域：** 单张缩略图 + 紫点 + Dismiss
- **所属区域：** 最新

#### 类型 3 — 运营 Campaign 趋势（最新区）⚠️ 特殊规则
- **图标：** 火焰
- **主文案：** `#Campaign`（颜色 #a78bfa）` is trending in Inspova🔥`
- **副文案：** `Click it to explore more · {时间}`
- **右侧区域：** **双图叠放缩略图** + 紫点，**不支持 Dismiss**
- **所属区域：** 最新
- **说明：** Campaign 为平台级运营消息，不允许用户删除

**双图叠放缩略图规范：**
```
容器：宽 44px × 高 42px · position: relative

左卡（背层）：
  宽 32px · 高 42px · border-radius 7px
  position: absolute · left: 0 · top: 0 · z-index: 1
  transform: rotate(-4deg)
  border: 2px solid #111   ← 与面板背景同色，产生视觉分隔

右卡（前层）：
  宽 32px · 高 42px · border-radius 7px
  position: absolute · left: 12px · top: 0 · z-index: 2
  transform: rotate(4deg)
  border: 2px solid #111
```

#### 类型 4 — 顶级创作者 Remix（最新区）
- **图标：** Remix 箭头
- **主文案：** `Top creator {用户名}`
- **副文案：** `just remixed your work · {时间}`
- **右侧区域：** 单张缩略图 + 紫点 + Dismiss
- **所属区域：** 最新

#### 类型 5 — Fork 获得关注（最新区）
- **图标：** Remix 箭头
- **主文案：** `Your fork is gaining traction`
- **副文案：** `Your remix has {N}+ likes, {N}+ saves · {时间}`
- **右侧区域：** 单张缩略图 + 紫点 + Dismiss
- **所属区域：** 最新

#### 类型 6 — 被收藏（最新区）
- **图标：** 书签
- **主文案：** `Your work was saved`
- **副文案：** `{用户名} saved your version to "{合集名}" · {时间}`
- **右侧区域：** 单张缩略图 + 紫点 + Dismiss
- **所属区域：** 最新

#### 类型 7 — 收到评论（更早区）
- **图标：** 对话气泡
- **主文案：** `Comment on your remix`
- **副文案：** `@{用户名}: {评论预览} · {时间}`
- **右侧区域：** 单张缩略图 + **无紫点** + Dismiss
- **所属区域：** 更早

#### 类型 8 — 新增关注者（更早区）⚠️ 特殊规则
- **图标：** 用户
- **主文案：** `{用户名}`
- **副文案：** `started following you · {时间}`
- **右侧区域：** **回关 CTA 按钮** + **无紫点** + Dismiss
- **所属区域：** 更早

---

### 7.6 回关 CTA 状态机

```
[回关] ──点击──> [加载中 1.6s] ──完成──> [已关注]
                                            │
                                          点击
                                            │
                                            ▼
                                        [回关]  ← 立即切换，无加载
```

**各状态样式规范：**

| 状态 | 背景 | 边框 | 文案 | 文字颜色 | 鼠标 |
|------|------|------|------|---------|------|
| 回关（默认） | #c4b5fd | 无 | "Follow back" | #2e1065 | pointer |
| 加载中 | #c4b5fd | 无 | 隐藏 | — | default（禁止点击）|
| 已关注 | transparent | 1px solid rgba(255,255,255,0.2) | "Following" | rgba(255,255,255,0.6) | pointer |
| 已关注 hover | rgba(255,255,255,0.12) | 1px solid rgba(255,255,255,0.3) | "Following" | rgba(255,255,255,0.6) | pointer |

**按钮基础样式：**
- `font-size: 13px · font-weight: 500 · border-radius: 20px · padding: 8px 18px · min-width: 90px`

**加载 Spinner 规范：**
- `宽高 14px · border-radius 50%`
- `border: 2px solid rgba(46,16,101,0.2)`
- `border-top-color: #2e1065`
- 动画：`spin 0.65s linear infinite`（顺时针旋转）
- 位置：绝对定位居中于按钮内

**交互规则：**
- 点击"回关" → 禁用按钮 → 显示 Spinner → 1600ms 后切换为"已关注"
- 点击"已关注" → 立即回到"回关"状态（无加载中间态）
- 悬停"已关注" → 背景变浅，无 Spinner，无文案变化

---

### 7.7 Dismiss 消除交互时序

**触发：** 鼠标悬停通知条目 → Dismiss X 按钮淡入（opacity 0→1，150ms）

**点击 Dismiss 后执行顺序：**
1. 阻止事件冒泡（避免触发条目点击）
2. 添加 `.dismissing` 类：`opacity: 0`，`translateX(-10px)`，200ms ease
3. 200ms 后：获取当前 `offsetHeight`，设置显式高度，`overflow: hidden`
4. 下一帧：`height → 0`，`paddingTop/Bottom → 0`，`marginTop/Bottom → 0`（200ms ease）
5. 共 220ms 后：从 DOM 中移除元素

**不支持 Dismiss 的通知类型：**
- 类型 3：运营 Campaign 趋势

---

### 7.8 未读紫点规则

| 所属区域 | 是否显示紫点 |
|---------|------------|
| 最新（New） | ✅ 全部显示 |
| 更早（Earlier） | ❌ 全部不显示 |

**紫点样式：** `7×7px · border-radius 50% · background #a78bfa · flex-shrink 0`

---

### 7.9 空态（无通知时）

当通知列表为空时，替换列表区域展示空态。

**布局：** 垂直居中，3 张扇形排列的趋势卡片 + 提示文案

**趋势卡片规范：**
```
卡片尺寸：118px × 157px · border-radius: 14px · cursor: pointer
静止边框：1px solid #111

扇形位置：
  卡片 1（左/底层）：rotate(-13deg) translate(-5px, 16px) · z-index 1
  卡片 2（中/前层）：rotate(-2deg)  translate(3px, 2px)   · z-index 2
  卡片 3（右/底层）：rotate(12deg)  translate(8px, 18px)  · z-index 1

悬停任意卡片：
  transform: rotate(0deg) translate(0, -20px) scale(1.07)
  z-index: 10
  border-color: transparent
  发光效果：
    0 0 0 1px rgba(185,162,251,0.3)
    0 0 28px rgba(185,162,251,0.6)
    0 0 60px rgba(185,162,251,0.3)
    0 20px 44px rgba(0,0,0,0.7)

悬停后显示"Explore"按钮：
  绝对定位 · 底部 12px · 水平居中
  padding: 6px 16px · 背景: rgba(255,255,255,0.92) · 颜色: #000
  font-size: 12px · font-weight: 600 · border-radius: 20px
  opacity: 0 → 1（卡片悬停时，180ms）
```

**提示文案：** `"Hover a card to learn the latest trend"` · 13px · color #444 · 居中 · line-height 1.5

---

### 7.10 缩略图分配策略

- 所有通知缩略图从统一图库中抽取
- 面板每次渲染时对图库执行 Fisher-Yates 洗牌，按顺序分配给各缩略图槽位
- 效果：每次打开面板图片顺序不同，界面更有生命力
- v1：静态资源；v2：后端返回与触发事件关联的真实内容缩略图 URL

---

## 8. 设计 Token（全局）

| Token 名称 | 值 | 用途 |
|-----------|-----|------|
| `color-bg-global` | `#000000` | 页面背景 |
| `color-bg-panel` | `#111111` | 通知面板背景 |
| `color-bg-icon` | `#1e1e1e` | 图标容器背景 |
| `color-bg-thumb` | `#252525` | 缩略图占位背景 |
| `color-divider` | `#1e1e1e` | 区域分隔线 |
| `color-primary` | `#a78bfa` | 未读紫点、campaign 标签、发光 |
| `color-primary-light` | `#c4b5fd` | 回关按钮背景 |
| `color-primary-dark` | `#2e1065` | 回关按钮文字、加载 spinner |
| `color-text-primary` | `#e8e8e8` | 通知主文案 |
| `color-text-secondary` | `#555555` | 副文案、分区标签 |
| `color-text-muted` | `#666666` | 图标颜色、Dismiss 按钮 |
| `color-hover-item` | `rgba(255,255,255,0.04)` | 条目悬停背景 |
| `font-family` | `Inter, -apple-system, sans-serif` | 所有文字 |
| `panel-width` | `400px` | 通知面板宽度 |
| `sidebar-width` | `64px` | 左侧导航宽度 |
| `panel-open-margin` | `464px` | 面板打开时主内容左边距 |
| `anim-panel-duration` | `300ms` | 面板滑动动画时长 |
| `anim-panel-easing` | `cubic-bezier(0.4, 0, 0.2, 1)` | 面板滑动缓动曲线 |

---

## 9. 上线计划

### v1 — 当前版本（设计完成）
- ✅ 面板开/关动画
- ✅ 8 种通知类型
- ✅ Campaign 双图叠放缩略图（含旋转）
- ✅ 回关 CTA 状态机（回关→加载→已关注→回关）
- ✅ Dismiss 折叠动画
- ✅ 最新/更早分区与紫点规则
- ✅ 空态互动趋势卡片
- ✅ 缩略图随机分配

### v2 — 下一阶段
- 对接真实通知 API（替换静态 mock 数据）
- 缩略图改为后端返回的真实内容关联图片
- "全部标为已读"操作
- 铃铛图标数字徽标
- 推送通知 / 邮件通知

### v3 — 未来规划
- 通知偏好设置
- 聚合通知（如"5 人 remix 了你的作品"）
- 通知内更多快捷操作（不只是回关）
- 移动端 Web 响应式布局

---

## 10. 给工程师的待确认问题

| # | 问题 | 负责方 | 优先级 |
|---|------|--------|--------|
| 1 | 通知实时更新用轮询还是 WebSocket？轮询间隔是多少？ | 后端 | 高 |
| 2 | "最新"与"更早"如何区分？基于时间阈值还是已读状态？ | 后端 | 高 |
| 3 | 通知内的回关操作是否需要同步到个人主页的关注状态？ | 前端 + 后端 | 高 |
| 4 | 每个分区最多展示多少条通知？超出后是分页还是截断？ | 产品 | 中 |
| 5 | Dismiss 的通知是永久删除还是仅客户端隐藏？ | 后端 | 中 |
| 6 | Campaign 通知由谁触发？CMS 后台还是工程推送？ | 产品 + 运营 | 中 |

---

*文档负责人：设计 / 产品 · 最后更新：2026-05-02 · Vercel 预览：https://inspova-redesign.vercel.app/inspova%20notification%20page/*
