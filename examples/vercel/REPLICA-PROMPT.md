# 任务：复刻 Vercel.com 首页

> 参考站点: https://vercel.com/ · 设计规范: DESIGN.md（同目录）

## 1. 任务与范围

从零实现 Vercel.com 首页的**像素级视觉平价**复刻（暗色主题）。纯前端静态页面，无需后端。

**要复刻的区块（自上而下）**：
1. 顶部导航（logo + 链接 + 登录/注册按钮）
2. Hero（徽章 + 标题 + 副标题 + CTA + agent 可视化面板）
3. 客户 Logo 走马灯
4. 三个平台特性区块（Build agents / Ship apps / Host platforms）
5. "Recently shipped" 发布栅格
6. "Built by you, or your agents" 行动区块
7. 页脚（12 组链接列）

范围外见 §8。

## 2. 技术栈与工程约定

- **技术栈**：Tailwind + 原生 HTML/TS（纯静态，无框架、无构建）；有指定技术栈则照办
- **令牌承载方式自选**：CSS 变量、Tailwind 主题配置或内联 tokens 皆可——值以 DESIGN.md §2 为准
- **实现方式自由**：走马灯、入场、辉光等用 CSS 或 JS 均可，满足 DESIGN.md §5 的行为要求即可
- **建议文件结构**（可调整）：
  ```
  replica/
  ├── index.html      # 语义化结构，区块注释标记
  ├── styles.css      # 令牌 + 基础样式 + 组件样式
  └── script.js       # 走马灯、hover 增强、导航滚动
  ```

## 3. 设计数据（必读）

> 完整设计数据在 **DESIGN.md**（与本提示词同目录）。
> 开工前**逐章节读完**：§2 设计令牌、§3 组件清单、§4 页面与布局、§5 交互行为、§6 响应式行为、§7 视觉素材、§8 无障碍。
> 本文件只承载执行规则。设计值（颜色、字号、间距、圆角、阴影、动效参数、焦点环、断点）一律以 DESIGN.md 为准，**不得自创、不得"优化"**。
> 若 DESIGN.md 不在同目录，将本节路径替换为实际位置。

## 4. 真实文案清单（嵌入原文，禁止编造）

- **Hero 徽章**：Automated by agents
- **H1**：Agentic Infrastructure（"Infrastructure" 一词用 DESIGN.md §2 的黄→蓝渐变填充）
- **副标题**：To ship apps and agents
- **CTA**：主「Deploy now」（→ /new）· 次「Talk to sales」
- **特性区块标题**：
  - Build agents on infrastructure that thinks like them
  - Ship apps that scale from zero to millions instantly
  - Host platforms that serve every customer
- **客户证言**：
  - Notion powers millions of agent conversations daily on Vercel.
  - Zapier serves over 100 million monthly website visits on Vercel.
  - Mintlify powers documentation for over 20,000 companies on Vercel.
- **特性清单**：
  - Build agents：Durable Orchestration / Sandboxed Environments / AI Model Gateway / Fluid Compute
  - Ship apps：Global Delivery / Deployment Environments / Serverless Functions / Web Application Firewall
  - Host platforms：Tenant Isolation / Domain Management / Custom SSL Certificates / Preview URLs
- **页脚组**：Agent Stack / Core Platform / Security / Tools / Frameworks / SDKs / Build / Learn / Explore / Company / Legal & Trust / Social（组内链接从参考站点页脚抓取原文；抓不到用该组常见条目并标注）
- **Recently shipped 条目**：从参考站 /changelog 抓取 3–6 条真实标题；抓不到则占位并标注

## 5. 页面构建顺序

1. **通读 DESIGN.md 全部章节**
2. **令牌落地**：DESIGN.md §2 全部值进令牌体系
3. **基础样式**：背景、主文本、字体、标题字重 400（按 DESIGN.md §2）
4. **布局骨架**：DESIGN.md §4 七个区块纵向排布，间距按 §2 间距令牌
5. **组件**：DESIGN.md §3 逐条实现（结构/变体/状态/关键样式）
6. **交互与动效**：DESIGN.md §5 条件→结果逐条
7. **响应式**：DESIGN.md §6 断点行为逐档

## 6. 验收标准（视觉平价）

- **颜色**：并排截图后取色器对比 DESIGN.md §2 关键令牌（背景、主文本、边框、品牌蓝、渐变端点），ΔE ≤ 2
- **字号**：与 DESIGN.md §2 字号阶梯对比，±1px（DevTools 计算样式）
- **间距**：区块间距、卡片内边距、栅格间隙，±4px
- **圆角 / 阴影**：与 DESIGN.md §2 视觉一致（无生硬边角、无突兀浮起）
- **断点**：320 / 640 / 768 / 1024 / 1440 宽度逐档截图对比（响应式行为按 DESIGN.md §6）
- **状态清单**：DESIGN.md §3 每个组件的每个状态（hover / focus / disabled）逐项实现并勾选；焦点环参数见 DESIGN.md §8
- **走马灯**：无缝循环，无跳变、无空白间隙
- **无占位内容**：§4 文案清单全部落地；无 Lorem ipsum、无灰色占位块
- **判定方法**：并排截图 + DevTools 取计算值；逐项列出通过/失败清单

## 7. 约束

- 文案以 §4 清单为准，**不编造**
- 客户 logo、Hero 配图有版权：用文本 / 简化 SVG 替代，并在交付说明中**明确标注**替代品
- **实现方式不限定**——CSS、JS、任何框架皆可；唯一要求是设计值以 DESIGN.md 为准（冲突时以 DESIGN.md 为准）
- 参考站未验证的细节（DESIGN.md 内「待确认」标注）：取合理近似，**在交付说明中列出所有近似点**

## 8. 范围外

- 后端 / 认证 / 真实部署
- 亮色主题（本次只做暗色）
- 导航下拉菜单的展开交互（静态呈现可接受）
- ⌘K 命令面板
- 非首页页面（/pricing、/docs 等只作为链接存在）
