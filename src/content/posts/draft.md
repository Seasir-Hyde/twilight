---
title: 分析追踪使用指南
published: 2026-03-29
updated: 2026-03-29
description: 本项目集成了 Google Analytics 4 (GA4) 和 SaleSmartly 聊天事件追踪功能，采用模块化设计，便于维护和扩展。
tags:
  - Markdown
draft: false
---
# 🚀 快速开始

### 1. 配置 GA4 测量 ID

需要配置两处（保持一致）：

#### ① 环境变量配置

打开项目根目录的 `.env` 文件，将 `G-XXXXXXXXXX` 替换为你的实际 GA4 测量 ID：

```env
# Google Analytics 4 测量 ID（请替换为你的实际 ID，如 G-ABC123DEF4）
VITE_GA_MEASUREMENT_ID = G-YOUR-ACTUAL-ID
```

#### ② HTML 配置

打开 `index.html` 文件，将两处 `G-XXXXXXXXXX` 替换为相同的 GA4 测量 ID：

```html
<!-- 第 25 行 -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-YOUR-ACTUAL-ID"></script>

<!-- 第 30 行 -->
gtag('config', 'G-YOUR-ACTUAL-ID');
```

**⚠️ 注意：两处配置必须保持一致！**

## 📦 项目结构

```
src/
├── config/
│   └── modules/
│       └── analytics.ts          # 分析追踪配置文件
├── utils/
│   └── analytics/
│       ├── ga-tracker.ts         # GA4 追踪器核心类
│       ├── salesmartly-tracker.ts # SaleSmartly 事件追踪器
│       └── index.ts              # 统一导出入口
├── types/
│   └── analytics.d.ts            # 类型定义文件
└── main.ts                       # 应用入口（初始化追踪器）
```

## 🎯 追踪的事件

### SaleSmartly 聊天事件


| 事件名称 | 事件类别 | 触发时机 | 参数 |
| -------------- | -------------- | --------- | ---------------------------------- |
| `chat_started` | SaleSmartly 聊天 | 用户发送第一条消息 | `event_label: 用户发起聊天` `value: 1` |
| `chat_opened` | SaleSmartly 聊天 | 用户打开聊天窗口 | `event_label: 用户打开聊天窗口` `value: 1` |
| `chat_closed` | SaleSmartly 聊天 | 用户关闭聊天窗口 | `event_label: 用户关闭聊天窗口` `value: 1` |


## 💡 在代码中使用

### 追踪自定义事件

```typescript
import { gaTracker } from '@/utils/analytics'

// 发送自定义事件
gaTracker.trackEvent('button_click', {
  event_category: '用户交互',
  event_label: '点击购买按钮',
  value: 1,
  custom_param: '自定义参数值'
})
```

### 追踪页面浏览

```typescript
import { gaTracker } from '@/utils/analytics'

// 追踪页面浏览
gaTracker.trackPageView('/product/123', '商品详情页')
```

### 设置用户属性

```typescript
import { gaTracker } from '@/utils/analytics'

// 设置用户 ID 和属性
gaTracker.setUserProperties('user_123', {
  user_type: 'premium',
  subscription_plan: 'annual'
})
```

### 重置聊天状态（可选）

```typescript
import { salesmartlyTracker } from '@/utils/analytics'

// 重置聊天状态（用于测试或特殊场景）
salesmartlyTracker.resetState()
```

## 🔧 调试模式

在开发环境下，`debug` 会自动设置为 `true`，所有事件都会显示 Element Plus 消息提示：

- 初始化成功 → 绿色消息
- 事件追踪成功 → 绿色消息
- 配置错误 → 黄色警告消息

生产环境下，`debug` 为 `false`，不会显示任何消息提示。

## 📊 查看 GA4 数据

配置完成后，可以在 Google Analytics 控制台查看追踪数据：

1. 登录 [Google Analytics](https://analytics.google.com/)
2. 选择你的媒体资源
3. 进入「报告」→「参与度」→「事件」
4. 查看自定义事件（`chat_started`、`chat_opened`、`chat_closed` 等）

## ⚠️ 注意事项

1. **必须替换 GA4 测量 ID**：
  - 在 `.env` 文件中配置 `VITE_GA_MEASUREMENT_ID`
  - 在 `index.html` 中替换两处 `G-XXXXXXXXXX`（第 25 行和第 30 行）
  - **确保两处 ID 完全一致！**
2. **环境区分**：
  - 开发环境会自动启用调试模式（显示消息提示）
  - 生产环境会关闭调试模式
3. **事件去重**：
  - `chat_started` 事件有防重复机制
  - 用户多次发送消息不会重复上报
4. **性能优化**：
  - 所有追踪代码在客户端执行
  - 不会影响页面加载性能

## 🔄 扩展功能

### 添加新的追踪事件

在 `src/utils/analytics/ga-tracker.ts` 中添加新方法：

```typescript
// 在 GATracker 类中添加新方法
trackFormSubmit(formName: string): void {
  this.trackEvent('form_submit', {
    event_category: '表单提交',
    event_label: formName,
    value: 1
  })
}
```

### 添加新的 SaleSmartly 事件监听

在 `src/utils/analytics/salesmartly-tracker.ts` 中添加新监听：

```typescript
// 在 setupEventListeners 方法中添加
window.ssq.push('onNewEvent', (obj: any) => {
  this.handleNewEvent(obj)
})
```

## 📚 相关文档

- [Google Analytics 4 文档](https://developers.google.com/analytics/devguides/collection/ga4)
- [SaleSmartly SDK 文档](https://www.salesmartly.com/)

&nbsp;