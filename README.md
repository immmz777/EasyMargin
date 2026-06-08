# 毛利计算器

一个 iOS 玻璃质感风格的毛利计算器网页工具，零依赖，打开即用。

![screenshot](screenshot.png)

## 功能

### 三种计算模式

| 模式 | 输入 | 输出 | 使用场景 |
|------|------|------|----------|
| **正向** | 售价 + 成本 | 毛利额、毛利率、成本利润率 | 已知定价和成本，看赚多少 |
| **定价** | 成本 + 目标毛利率 | 建议售价 | 进货后想达到目标毛利，该卖多少钱 |
| **核价** | 售价 + 目标毛利率 | 最高进货价 | 市场价已定，想达到目标毛利，最多能花多少钱进货 |

### 交互细节

- **颜色标识** — 盈利显示绿色，亏损显示红色
- **实时预览** — 输入数字即时显示结果
- **回车触发** — 任意输入框按 Enter 键执行计算并记录
- **验证反馈** — 输入不完整时按钮抖动 + 红色提示
- **清空按钮** — 一键重置当前输入
- **毛利率预设** — 定价/核价模式提供 20% / 30% / 40% / 50% 快捷按钮
- **历史记录** — 自动保存最近 20 次计算，显示最近 5 条（localStorage 持久化）
- **历史恢复** — 点击历史记录自动切换到对应模式并回填数据
- **单条删除** — 每条历史记录可单独删除（悬停显示 × 按钮）
- **移动端适配** — 专为手机屏幕设计，也适合桌面端

## 快速开始

直接双击 `index.html` 用浏览器打开即可，无需安装任何东西。

## 计算公式

| 指标 | 公式 |
|------|------|
| 毛利额 | 售价 − 成本 |
| 毛利率 | 毛利额 ÷ 售价 × 100% |
| 成本利润率 | 毛利额 ÷ 成本 × 100% |
| 定价（反向） | 成本 ÷ (1 − 目标毛利率) |
| 核价（最高进价） | 售价 × (1 − 目标毛利率) |

### 举例

**正向：** 售价 100 元，成本 60 元  
→ 毛利额 40 元，毛利率 40%，成本利润率 66.67%

**定价：** 成本 60 元，目标毛利率 40%  
→ 建议售价 100 元

**核价：** 市场售价 100 元，目标毛利率 40%  
→ 最高进货价 60 元（超过这个价就别进）

## 技术栈

纯原生 HTML/CSS/JS，没有框架、没有构建工具、没有依赖。

### CSS 要点

毛玻璃效果的核心是 `backdrop-filter`，对元素**后方区域**进行模糊和饱和度处理：

```css
.card {
  background: rgba(255, 255, 255, 0.52);       /* 半透明白底 */
  backdrop-filter: blur(20px) saturate(180%);    /* 毛玻璃核心 */
  border-radius: 22px;
  box-shadow:                                     /* 多层阴影模拟厚度 */
    0 4px 24px rgba(0, 0, 0, 0.04),
    inset 0 0 0 1px rgba(255, 255, 255, 0.55);   /* 内阴影高光边 */
}
```

关键属性说明：

| 属性 | 作用 |
|------|------|
| `backdrop-filter: blur(20px)` | 将卡片后面的内容模糊 20px |
| `backdrop-filter: saturate(180%)` | 提高后方颜色的饱和度 |
| `rgba(255,255,255,0.52)` | 半透明白底，透出后方背景 |
| `inset box-shadow` | 模拟玻璃边缘的厚度感和高光线 |
| `-webkit-backdrop-filter` | Safari 兼容写法 |

背景光晕用伪元素的径向渐变实现：

```css
body::before {
  content: '';
  position: fixed;
  background: radial-gradient(circle, rgba(0,122,255,0.12) 0%, transparent 70%);
  border-radius: 50%;
}
```

验证失败时的摇动动画：

```css
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  20%      { transform: translateX(-6px); }
  40%      { transform: translateX(6px); }
  60%      { transform: translateX(-4px); }
  80%      { transform: translateX(4px); }
}
```

### JS 要点

**三模式切换** — 通过显示/隐藏对应的卡片区域：

```js
function switchMode(m) {
  cardForward.style.display = m === 'forward' ? '' : 'none';
  cardReverse.style.display = m === 'reverse' ? '' : 'none';
  cardBargain.style.display = m === 'bargain' ? '' : 'none';
}
```

**实时计算** — 输入框绑定 `oninput`，每次输入即时更新预览：

```html
<input type="number" id="price" placeholder="0" oninput="calcForward()">
```

**核价模式** — 已知售价和目标毛利率，反推最高进价：

```js
const maxCost = price * (1 - rate / 100);
```

**历史记录** — 点击"计算"按钮触发 `record()`，存入 `localStorage`（存 20 条，显示 5 条）。点击历史项调用 `restoreRecord()` 恢复数据：

```js
function record(type, inputs, outputs) {
  const records = JSON.parse(localStorage.getItem('margin_history') || '[]');
  records.unshift({ type, inputs, outputs, time: Date.now() });
  if (records.length > 20) records.length = 20;
  localStorage.setItem('margin_history', JSON.stringify(records));
  renderHistory();
}
```

## 文件结构

```
EasyMargin/
├── index.html    # 完整应用（HTML + CSS + JS）
└── README.md     # 项目说明
```

## 许可

MIT
