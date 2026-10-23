---
name: dynamic-two-way-flowchart
description: Generate dark tech-style animated flowcharts with bidirectional data-packet particles. Use for dynamic flowcharts, architecture flow, end-to-end call chains, data-flow diagrams. Do not use for static diagrams.
---

# 双向动态流程图（科技风）

把「节点 + 连线 + 流向」渲染成深色科技风（HUD）的动态 SVG 流程图，含双向数据包粒子、三阶段配色、流动/呼吸/脉动动画。适合端到端调用链、架构流程、鉴权链路等「谁调谁、数据怎么流」的场景。

## 何时使用

- 用户要「动态流程图」「架构流程图」「端到端调用链」「数据流图」
- 需要体现双向流向（请求去程 + 应答返回）、阶段分层、核心枢纽
- 需要科技风 / HUD / 监控台视觉

## 何时不用

- 纯静态、无流向的简单框图（用普通 SVG 或 Markdown 即可）
- 时序图（消息顺序用 sequence-diagram）
- 层级/依赖树（用 tree-flow）
- 需要可编辑/拖拽的图（用 whiteboard 或 draw.io）

## 输入契约

从用户需求提炼两类数据，再决定布局。

### 节点

| 字段 | 说明 |
| --- | --- |
| id | 唯一标识 |
| 标签 | 2–5 字（如「网关」「DSH 客户端」） |
| 副标 | 组件名/技术名（如 `agentgateway`、`deepseek harness`），可多行 |
| 角色 | `core`（核心枢纽，绿色荧光边框 + 脉动）或普通 |

### 连线

| 字段 | 说明 |
| --- | --- |
| from / to | 节点 id |
| label | 1–3 词（如 `tools/list`、`SSO 认证`） |
| 方向 | 去程（实线 + 荧光 + 粒子）或返回（虚线 + 暗灰 + 粒子） |
| 阶段 | 决定连线颜色：认证 / 发现 / 调用 |

## 视觉规范（固定写死，无主题切换）

| 用途 | 色值 |
| --- | --- |
| 背景 | `#0B1220`，叠加网格点阵 `rgba(96,165,250,0.12)` |
| 普通节点 | fill `#101A2E`，stroke `#3A4A63` |
| 核心节点 | fill `#0E2B1F`，stroke `#3EF08C` |
| 认证连线 | `#4F8EF7`（蓝） |
| 发现连线 | `#2DD4BF`（青） |
| 调用连线 | `#3EF08C`（绿，加粗 1.4） |
| 返回连线 | `#4A5B73`（暗灰虚线） |
| 主文字 | `#E8EEF7` |
| 副文字 | `#8FA3BF` |

线宽：去程 1.2（调用 1.4），返回 1.0；箭头 7×7。节点状态灯 `#3EF08C`，四角 HUD 角标 `#2C3E58`。

## 动画规范

- **连线流动**：`stroke-dasharray: 6 10` + `stroke-dashoffset` 动画（表示方向）
- **数据包粒子**：SMIL `animateMotion` 沿连线路径移动；光晕圆（r=4, opacity .2）+ 核心圆（r=2）
- 去程粒子用阶段色，返回粒子用暗灰 `#7A8CA8`（r 略小、更淡）
- **节点淡入**：`fadeIn` 0.3s，按流程顺序 stagger
- **核心节点脉动** + **状态灯呼吸**（`blink`）
- 所有连续动画包在 `@media (prefers-reduced-motion: no-preference)`；`reduce` 下隐藏粒子

## 布局规则

- z-order（从下到上）：连线 → 粒子 → 节点 → label
- 节点间距：水平 ≥60，垂直 ≥28
- 去程实线、返回虚线；箭头方向与路径一致
- 核心枢纽（网关类）居中，决策/依赖放右，客户端放左
- 完整可复用模板见 `assets/template.svg`（6 节点 13 连线示例，含全部动画）

## 输出

内联 SVG（`viewBox="0 0 720 H"`，`width="100%"`），可直接内联渲染（dynamic-ui），或导出成独立 `.svg` 文件（自包含，浏览器可直接打开播放动画）。
