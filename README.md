# 毛利计算器

一个 iOS 玻璃质感风格的毛利计算器网页工具，零依赖，打开即用。

![screenshot](screenshot.png)

## 功能

- **正向计算** — 输入售价和成本，自动算出毛利额、毛利率、成本利润率
- **反向计算** — 输入成本和目标毛利率，自动算出建议售价
- **颜色标识** — 盈利显示绿色，亏损显示红色
- **历史记录** — 自动保存最近 5 次计算（localStorage 持久化）
- **移动端适配** — 专为手机屏幕设计，也适合桌面端

## 快速开始

直接双击 `index.html` 用浏览器打开即可，无需安装任何东西。

## 计算公式

| 指标 | 公式 |
|------|------|
| 毛利额 | 售价 − 成本 |
| 毛利率 | 毛利额 ÷ 售价 × 100% |
| 成本利润率 | 毛利额 ÷ 成本 × 100% |
| 反向定价 | 成本 ÷ (1 − 目标毛利率) |

### 举例

**正向：** 售价 100 元，成本 60 元  
→ 毛利额 40 元，毛利率 40%，成本利润率 66.67%

**反向：** 成本 60 元，目标毛利率 40%  
→ 建议售价 100 元

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

### JS 要点

**模式切换** — 通过显示/隐藏两个卡片区域实现正向/反向模式切换：

```js
function switchMode(m) {
  document.getElementById('cardForward').style.display = m === 'forward' ? '' : 'none';
  document.getElementById('cardReverse').style.display = m === 'reverse' ? '' : 'none';
  if (m === 'forward') calcForward(); else calcReverse();
}
```

**实时计算** — 输入框绑定 `oninput` 事件，每次输入都即时更新结果：

```html
<input type="number" id="price" placeholder="0" oninput="calcForward()">
```

**历史记录** — 监听 `change` 事件（用户编辑完离开输入框时触发），将结果存入 `localStorage`：

```js
function record(type, inputs, outputs) {
  const records = JSON.parse(localStorage.getItem('margin_history') || '[]');
  records.unshift({ type, inputs, outputs, time: Date.now() });
  if (records.length > 5) records.length = 5;  // 只保留最近5条
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
