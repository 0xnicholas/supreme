# DESIGN — Vercel.com 首页

## 1. 设计风格概述

- 暗色极简：纯黑背景（#000）+ 白字（#ededed），层级靠灰阶与字号，无彩色大色块
- 大面积留白：区块间距 80–160px，卡片间隙 20px
- 标题不加粗（font-weight 400），靠字号撑层级
- 品牌蓝 #0070f7 仅做点缀：链接、焦点环、辉光，不做背景色
- 渐变克制使用：标题局部黄→蓝渐变、agent 视觉彩虹渐变
- 等宽字体承载技术性 UI（代码行、标签），像素字体做装饰
- 圆角小而克制（6–16px）；pill 只用于主 CTA 与徽章

## 2. 设计令牌

### 颜色

**背景 / 前景**

| 角色 | 暗色 | 亮色 |
|---|---|---|
| background-100（页面主背景） | `#000` | `#fff` |
| background-200（次级表面） | `#000` | `#fafafa` |
| 前景文本（gray-1000） | `#ededed` | `#171717` |

**中性色阶（gray-100 ~ gray-1000）**（暗色 / 亮色）

| 级别 | 暗色 | 亮色 | 用途 |
|---|---|---|---|
| gray-100 | `#1a1a1a` | `#f2f2f2` | hover 背景 |
| gray-200 | `#1f1f1f` | `#ebebeb` | 暗色下 hover 背景 |
| gray-300 | `#292929` | `#e6e6e6` | 边框（subtle） |
| gray-400 | `#2e2e2e` | `#eaeaea` | 边框（默认） |
| gray-500 | `#454545` | `#c9c9c9` | 分割线/弱边框 |
| gray-600 | `#878787` | `#a8a8a8` | 次要文本 |
| gray-700 | `#8f8f8f` | 待确认 | 禁用文本 |
| gray-800 | `#7d7d7d` | 待确认 | 弱文本 |
| gray-900 | `#4d4d4d` | `#a0a0a0` | 正文弱化文本 |
| gray-1000 | `#ededed` | `#171717` | 主文本 |

**半透明中性色（gray-alpha，遮罩/浅背景，hex8）**：alpha-100 `#ffffff0f` / `#0000000d`、alpha-200 `#ffffff17` / `#00000014`、alpha-300 `#ffffff21` / `#0000001a`、alpha-400 `#ffffff24` / `#00000014`、alpha-500 `#ffffff3d` / `#00000036`、alpha-600 `#ffffff82` / `#0000003d`、alpha-700 `#ffffff8a` / `#00000070`、alpha-800 `#ffffff78` / `#00000082`、alpha-900 `#ffffff9c` / `#000000b3`、alpha-1000 `#ffffffeb` / `#000000e8`

**品牌蓝（accent）**

| 级别 | 暗色 | 亮色 | 用途 |
|---|---|---|---|
| blue-100 | `#06193a` | `#f0f7ff` | 蓝色微光/浅底 |
| blue-200 | `#022248` | `#eaf4ff` | 辉光外圈 |
| blue-400 | `#003771` | `#cce7ff` | 辉光内圈 |
| blue-500 | `#004287` | `#97ccff` | 链接/强调 |
| blue-600 | `#0090ff` | `#51aeff` | 强调亮蓝 |
| blue-700 | `#0070f7` | `#0071f6` | 主强调色（品牌蓝） |
| blue-900 | `#0064e2` | `#50a8ff` | focus 色 |

**语义色**：success green-500 `#82eb8d`（亮）`#00661d`（暗）；danger red-500 `#ffb5b6` / `#88151f`；warning amber-600 `#fa0` / `#e99c00`

**渐变**
- 标题局部渐变：`linear-gradient(to_right, #FFDC30 0%, #38A2FF 100%)`（黄→蓝），配合 `text-transparent` + `bg-clip-text`
- Agent 可视化彩虹渐变：`linear-gradient(to_top, #00E5FF 0%, #9500FF 25%, #FF1744 50%, #FFD000 75%, #00FF95 100%)`
- 区块淡出遮罩：`linear-gradient(#0000 156px, #000 320px 100%)` 等 fade-to-black

### 字体

- **主字体**：Geist Sans（`--font-geist-sans: "GeistSans"`，可变字重 100–900，自托管）
- **等宽**：Geist Mono（`--font-mono`，可变字重 100–900）
- **装饰像素字体**：GeistPixel（square / circle / line 三变体，`--font-geist-pixel-*`，配 `image-rendering: pixelated`）
- **回退链**：Geist Sans → 系统 sans；GeistPixelCircle → Geist Mono → ui-monospace…；serif 回退 Georgia

**字号阶梯（text-heading-*，首页实际使用）**

| 级别 | 大小 | 用途 |
|---|---|---|
| text-heading-16 | 16px | 小标题 |
| text-heading-20 | 20px | 区块小标题 |
| text-heading-24 | 24px | Hero 副标题 |
| text-heading-32 | 32px | 区块标题 |
| text-heading-48 | 48px | H1（移动端） |
| text-heading-56 | 56px | 大区块标题 |
| text-heading-64 | 64px | H1（≥640px） |

Tailwind 常规级别：text-xs 12px / sm 14px / base 16px / lg 18px / xl 20px / 2xl 24px / 3xl 30px / 4xl 36px / 5xl 48px / 7xl 72px / 8xl 96px（实测出现）

**表单字号**：小按钮 14px（`--geist-form-small-font`）、大按钮 16px（`--geist-form-large-font`）

**字重**：标题 `font-normal`（400）、正文/按钮 `font-medium`（500）

### 间距

- 基准：Geist space 体系——`--geist-gap-*` 已引用但定义值在未抓取的 chunk（按 Geist 标准假设 gap=16px、half=8px、quarter=4px、large=32px，**待确认**）
- 按钮内边距（实测确认）：小按钮水平 6px、大按钮水平 14px
- 区块间距（实测类名）：`mt-20`（80px）、`mt-40`（160px）、`gap-20`、`gap-64`
- 卡片栅格间隙：`gap-5`（20px）

### 圆角

| 用途 | 值 |
|---|---|
| rounded-md（小按钮、常规控件） | 6px（`.375rem`） |
| 大按钮 | 8px |
| rounded-lg | 8px（`.5rem`） |
| rounded-xl | 12px（`.75rem`） |
| rounded-2xl | 16px（1rem，卡片） |
| rounded-3xl | `var(--radius-3xl)`，**待确认**（按 Tailwind 默认 ≈24px） |
| pill（主 CTA、徽章） | 9999px（`3.40282e38px`） |
| 部分卡片 | 12px（实测 12 处） |
| 装饰大圆角 | 128px（6 处） |

### 阴影

| 级别 | 值 |
|---|---|
| shadow-xs | `0 1px 2px #00000029`（暗）/ `0 1px 2px #0000000a`（亮） |
| shadow-small | `0 1px 2px #00000029` / `0 2px 2px #0000000a` |
| shadow-medium | `0 2px 2px #00000052, 0 8px 8px -8px #00000029`（亮色弱化） |
| shadow-large | `0 2px 2px #0000000a, 0 8px 16px -4px #0000000a` |
| shadow-xl | `0 1px 1px #00000005, 0 4px 8px -4px #0000000a, 0 16px 24px -8px #0000000f` |
| shadow-2xl | `0 1px 1px #00000005, 0 8px 16px -4px #0000000a, 0 24px 32px -8px #0000000f` |
| shadow-menu / modal / tooltip / fullscreen | 组合式（border-base + 分层黑色 alpha + background-border） |

**边框（shadow-border 体系）**：border-base `0 0 0 1px #ffffff25`（暗）/ `#00000014`（亮）；`--ds-shadow-border: border-base + background-border`

### 断点与栅格

- **媒体断点**：Tailwind 标准 sm 640 / md 768 / lg 1024 / xl 1280 / 2xl 1536
- **容器查询**：`@container` + `@sm` / `@smd` / `@lg` 变体（如 `grid-cols-1 @smd:grid-cols-12`、`@sm:text-heading-64`）
- **容器宽度**：`--container-2xl: 1400px`、`--container-lg: 961px`、`--container-md: 601px`、`--container-sm: 401px`；正文内容常见 max-width 600px 与 960px

### 动效

| 用途 | 值 |
|---|---|
| 按钮 hover/focus 过渡 | `transition-[border-color,background,color,transform,box-shadow] 150ms ease-in-out` |
| fade-in（入场） | `1.25s cubic-bezier(.4,.04,.04,1) forwards` |
| fade-slide-in | `0.35s cubic-bezier(.16,1,.3,1) forwards` |
| logo 走马灯 | `linear infinite`，速度 `--marquee-speed` 40s / 50s / 80s，间距 `--marquee-gap` 40px / 48px / 64px；`translate(0) → translateX(calc(-100% - gap))` |
| logo-carousel（轮播淡入） | 0% 透明 → 3% 显现 → 22% 保持 → 25% 消失，循环 |
| blue-glow（agent 元素辉光脉冲） | `0%,to: 0 0 10px blue-200 + 0 0 20px blue-100; 50%: 0 0 30px blue-400 + 0 0 60px blue-200` |
| bar-pulse | `0.8s ease-in-out infinite` |
| blink | `1s infinite` |
| skeleton 加载 | `1.5s ease-in-out infinite reverse` |
| thinking-loader | `1.5s / .85s linear infinite` |
| accordion 展开 | `0.2s ease-out`（accordion-down/up） |
| cmd 命令面板 | `--ds-motion-overlay-duration/timing`（值**待确认**） |
| flip（卡片翻转） | `0.5s cubic-bezier(.4,.04,.04,1) forwards` |

### 层级

- z-index 具体数值未提取（**待确认**），层级感知由阴影分层体系（shadow-menu / modal / tooltip / fullscreen）承担
- 焦点环：`0 0 0 2px background-100, 0 0 0 4px focus-color`（focus-color = blue-700 / blue-900）

## 3. 组件清单

### 顶部导航 Nav
- **结构**：左三角 logo（内联 SVG）→ 中链接组（Products / Resources / Solutions / Pricing）→ 右「Log in」描边小按钮 +「Sign Up」实心小按钮
- **变体**：桌面完整导航 / 移动端形态未验证（**待确认**）
- **状态**：链接 hover 文字变 gray-600；按钮 hover 换背景
- **容器**：sticky 顶部（吸附行为未验证，**待确认**）；背景近似为 background-100 半透明 + backdrop-blur（**待确认**）
- **下拉菜单**：Products/Resources 展开形态与动画未验证（**待确认**）

### 按钮 Button（Geist Button 体系）
- **变体**：
  - 实心（primary）：背景 `gray-1000`、文字 `background-100`（暗色下白底黑字）、圆角 8px（大）/ 6px（小）；主 CTA 为 **pill**（`9999px`）
  - 描边（secondary/ghost）：`1px solid gray-400` + 背景 `background-100`，hover 背景 gray-alpha-200 / gray-100
  - 禁用：文字 gray-700、背景 gray-100、`cursor: not-allowed`、opacity 40–50%
- **尺寸**：小——高 32px、水平内边距 6px、字号 14px；大——高 40px（`--ds-size-large`，值**待确认**）、水平内边距 14px、字号 16px
- **状态**：hover 背景切换（亮 gray-100 / 暗 gray-200），150ms；focus 焦点环 `0 0 0 2px bg + 4px focus-color`（focus 时禁过渡）
- **结构**：可选 prefix/suffix 图标（16px，描边风格）

### 徽章 Pill
- **结构**：Hero 顶部小徽章，胶囊形（9999px）
- **规格**：1px 描边 gray-alpha-400、透明底、字号 14px、文字 gray-600、水平内边距 12–16px、高 28–32px（精确值**待确认**）

### Hero 区块
- **结构**：徽章 → H1「Agentic Infrastructure」（48px → 640px 起 64px，font-normal；"Infrastructure" 用黄→蓝渐变 + bg-clip-text + text-transparent）→ 副标题（24px，gray-900）→ CTA 行（主 CTA pill 实心 + 次 CTA pill 描边）→ agent 可视化面板
- **可视化面板**：约 1284×1026 比例容器，暗色；装饰含彩虹渐变（`--gradient-agent`）、blue-glow 脉冲元素、等宽字体代码行/节点标签（CSS/SVG 近似实现，内部细节不做像素级还原）
- **入场**：fade-in `1.25s cubic-bezier(.4,.04,.04,1)`
- **移动端**：CTA 按钮整行宽（100%），≥640px 恢复自适应

### 客户 Logo 走马灯
- **结构**：横排 logo 流，内容复制两份实现无缝滚动
- **动画**：marquee `40s linear infinite`（`--marquee-speed` 可调 40–80s），间距 `--marquee-gap` 48px，边缘 mask 淡出（`linear-gradient(90deg, transparent, black 6% 94%, transparent)`）
- **素材**：单色 logo（见 §7 视觉素材）

### 平台特性区块 ×3
- **结构**：大标题（56px、font-normal、居中）→ 客户证言（20–24px、gray-600、居中）→ 特性卡片栅格
- **栅格**：`grid-cols-1`（移动）→ 容器 768px 起 12 列（卡片横跨 4 列）；间隙 20px
- **卡片**：圆角 12–16px、1px 描边 gray-400、内边距 24–32px、标题 18–20px 白、描述 14–16px gray-600；hover 描边变亮 + 轻微阴影（精确 hover 值**待确认**）

### Recently shipped 栅格
- **结构**：标题（32–48px、font-normal）→ 容器查询栅格（`@container` + `p-px` 描边包裹 → 1 列 / 768px 起 12 列），条目为链接卡片（标题 + 日期/描述）

### Built by you, or your agents 区块
- **结构**：居中大标题（48px、font-normal）→ 说明文案 → 行动按钮（实心 pill）；完整形态未完全验证（**待确认**）

### 页脚
- **结构**：12 组链接列（Agent Stack / Core Platform / Security / Tools / Frameworks / SDKs / Build / Learn / Explore / Company / Legal & Trust / Social）+ 底部 logo 与版权行
- **规格**：链接 14px、gray-1000，hover 变亮；组名 14px、gray-600

### 其他组件（CSS 层确认存在，首页未逐个验证）
- 代码窗口 / 终端面板（terminal-core、代码高亮配色）
- 命令面板 cmdk（⌘K，overlay 动画）
- Skeleton 加载占位（1.5s 反向脉冲）
- 表单控件（Geist form 字号体系：14px / 16px）
- 标签 chip、conic 进度环等工具类

## 4. 页面与布局

**页面标识**：首页（单页范围）

**布局骨架（自上而下）**：
1. 顶部导航（sticky）
2. Hero：徽章 → H1 → 副标题 → CTA 行 → 可视化面板
3. 客户 Logo 走马灯
4. 平台特性区块 ×3（Build agents… / Ship apps… / Host platforms…）
5. Recently shipped 栅格
6. "Built by you, or your agents" 行动区块
7. 页脚（12 组链接列）

## 5. 交互行为

| 条件 | 结果 |
|---|---|
| 按钮 hover | 背景切换（描边 → gray-alpha-200；实心 → gray-200），150ms ease-in-out |
| 按钮 focus | 焦点环 `0 0 0 2px background-100 + 4px focus-color` |
| 按钮 disabled | gray-700 文字、gray-100 背景、not-allowed 光标、opacity 40–50% |
| 链接 hover | 颜色弱化 / 变亮 |
| 走马灯 | 无限滚动（marquee），边缘 mask 淡出 |
| 客户 logo 轮播 | 3%–22% 保持可见后淡出（logo-carousel） |
| agent 装饰元素 | blue-glow 阴影脉冲（10px/20px ↔ 30px/60px） |
| ⌘K | 打开命令面板（cmdk overlay 动画） |
| 加载中 | skeleton 占位（1.5s 反向脉冲）或 thinking-loader |
| 折叠面板 | accordion 展开/收起 0.2s ease-out |

## 6. 响应式行为

| 断点 | 变化 |
|---|---|
| 移动（<640px） | H1 48px、CTA 整行宽（100% → `@sm:w-max`）、特性栅格单列 |
| ≥640px（sm） | H1 → 64px；CTA 恢复自适应宽度 |
| 容器 768px（@smd） | 特性/发布栅格 1 列 → 12 列 |
| ≥1024px（lg） | Hero 副标题取消 mt-10 偏移（`@lg:mt-0`）；导航完整展开（汉堡切换点未验证） |
| 主题 | 暗色由根元素 `dark-theme` class 控制；亮色由 `[data-theme=light]` 切换（两套令牌见 §2） |

## 7. 视觉素材

- **Hero 可视化面板图**：暗色渲染图，1284×1026 尺寸、lazy 加载；彩虹渐变与蓝辉光点缀（复刻时以 CSS/SVG 近似，无需还原图内细节）
- **客户 logo**：单色（白）logo 流，走马灯展示；版权受限时以文本 logo 替代
- **图标**：内联 SVG、16px（按钮内）、描边风格
- **logo**：Vercel 三角（内联 SVG，单色）

## 8. 无障碍

- 焦点环：双层高可见（`0 0 0 2px background-100 + 4px focus-color`），focus 时禁用过渡避免闪烁
- 交互元素带 aria-label；按钮禁用态同时设 `disabled` 与 `aria-disabled`
- 键盘支持：React Aria 驱动（data-react-aria-pressable）
- 对比度：gray-600（#878787 暗色）作次要文本与纯黑背景 ≈4.1:1，接近但未达 AA 大字标准（**待确认**实际使用场景）
- prefers-reduced-motion：未发现处理（**待确认**）
