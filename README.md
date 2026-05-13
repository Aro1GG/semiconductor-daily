# semiconductor-daily

半导体行业每日/每周技术资讯报告生成器（架构师视角）—— Claude Code Agent 技能。

> 面向芯片架构师的自动化技术情报系统。自动搜索全球半导体技术源，经「架构影响翻译层」处理后输出结构化报告。每项信息都回答 **「这对芯片设计决策意味着什么」**。

---

## 设计理念

本工具严格剔除股价、补贴、并购等无架构决策价值的信息，只关注四件事：

- **协议规范演进** — AMBA / UCIe / CXL / PCIe / HBM 版本变化与硅验证状态
- **制程器件状态** — 先进节点良率、PDK 成熟度、封装工艺（含国产制程）
- **EDA/IP 生态** — 工具链就绪度、IP 可用性、AI 赋能芯片设计
- **学术架构创新** — 最新论文中的架构突破

---

## 核心原则：无变化不展开

变化检测是**强制步骤**，不是可选项。每期报告生成前，先与 `references/protocol_versions.md` 中的基线比对：

| 判定 | 含义 | 报告处理 |
|------|------|----------|
| **有实质更新** | 新版本 / 新硅验证 / 具体数字变化 / 新政策 | 详细展开 |
| **无变化** | 与基线一致或无新进展 | 一句话带过 |

**实质更新定义**：新版本发布、新硅验证完成、良率 ±5% 以上、产能变化、层数增加、重大政策变化。

**不视为实质更新**：厂商营销博客、路线图重申无新里程碑、行业会议声明无实质产品进展。

---

## 两种模式

| 模式 | 耗时 | 搜索方式 | 产出 |
|------|------|----------|------|
| **完整版 (full)** | ~25 min | 四路并行 Agent 深度搜索 | `.md` + `.json` + `.html` PPT |
| **快速版 (quick)** | ~10 min | 单路搜索 | `.md` 精简报告 |

---

## 工作流（完整版）

```
阶段零：加载协议基线 (protocol_versions.md)
    ↓
阶段一：四路并行 Agent 搜索
    ├── Agent A — 制程与器件（含国产制程/设备/出口管制）
    ├── Agent B — 协议与互联（AMBA/UCIe/CXL/PCIe/HBM/Chiplet）
    ├── Agent C — EDA 工具、半导体 IP、AI 赋能芯片设计
    └── Agent D — 学术论文（arXiv / IEEE Xplore）
    ↓
阶段二：架构影响翻译层
    每条发现 → 架构影响 1-2 句 + 优先级 P0/P1/P2
    ↓
阶段二点五：变化检测（内部流程，不写入报告）
    ↓
阶段三：按六段式模板整合 Markdown 报告
    ↓
阶段四：生成 JSON + 调用 guizang-ppt-skill 生成杂志风网页 PPT
```

---

## 覆盖维度

### 协议与互联
AMBA CHI Issue H / AXI Issue L、UCIe 3.0、CXL 3.1/4.0、PCIe 6.0/7.0、JEDEC HBM4/HBM4E、OCP FCSA、中国 GB/T 46280 Chiplet 标准

### 制程与器件
TSMC N2/N2P/A16/A14/A13/A12、Intel 18A/18A-P、Samsung SF2、GAA 器件对比、CoWoS/SoIC/Hybrid Bonding/玻璃基板

### 国产制程专项
SMIC N+1/N+2/N+3、YMTC 3D NAND Xtacking、CXMT DRAM/HBM、华虹特色工艺、国产设备/EDA、美国出口管制影响

### 学术论文
AI 加速器、Chiplet & NoC、神经形态计算、AI 赋能芯片设计（ML for EDA）、RISC-V 生态、后量子密码硬件

### EDA & IP 生态
Synopsys/Cadence/Siemens 2nm 认证、ARM Neoverse CSS、RISC-V 商用 IP、HBM4 Controller IP、UCIe 3.0 PHY

### AI 赋能芯片设计（重点板块）
LLM 辅助 RTL 设计、AI 驱动综合与 PPA 优化（DSO.ai / Cerebrus）、AI 辅助物理设计/验证/DFT、Google/Amazon/NVIDIA 内部实践

---

## 输出文件

| 文件 | 说明 |
|------|------|
| `半导体行业资讯日报_YYYYMMDD.md` | 六段式 Markdown 架构师报告 |
| `trend_data_YYYYMMDD.json` | 结构化趋势数据 |
| `半导体行业资讯日报_YYYYMMDD.html` | 杂志风横向翻页网页 PPT |

---

## 报告结构

### 完整版（六段式）

1. **架构师执行摘要** — 本周最影响芯片架构决策的 3 件事
2. **协议与互联标准演进** — 各协议版本状态 + 变化检测
3. **制程与器件进展** — 先进节点 + 封装 + 国产制程
4. **学术论文精选** — AI 加速器 / Chiplet / 神经形态 / AI 赋能设计 / RISC-V / PQC
5. **设计工具链与 IP 生态** — EDA 2nm 就绪度 + AI 赋能进展 + IP 可用性
6. **架构决策影响分析** — P0 行动项 + P1 技术储备 + P2 趋势跟踪 + 协议迁移路线图

### 快速版（八段式）

1. 本周最重要的 3 件事
2. 协议与互联更新
3. 制程动态（含国产制程）
4. AI 芯片架构突破
5. AI 赋能芯片设计案例
6. 学术速览（3-5 篇）
7. 架构师 P0 行动项
8. 本周关键词

---

## 数据质量要求

每条报告数据必须包含：

- **具体数字**：良率 %、带宽 TB/s 或 Gbps、功耗 W 或 pJ/b、面积 mm²、速度 GHz
- **协议版本号**：每次固定追踪版本变化
- **硅验证状态**：PHY 是否已投片？IP 是否可用？
- **架构影响注释**：每条发现附架构影响标注
- **行动等级**：P0（立即受影响）/ P1（6-12 月）/ P2（中长期关注）

---

## 搜索来源优先级（技术深度优先）

| 优先级 | 来源 | 覆盖内容 |
|--------|------|----------|
| 1 | **SemiEngineering** | 工程进展（需通过 agent-browser 访问） |
| 2 | SemiWiki | 深度分析 |
| 3 | IEEE Xplore / arXiv | 学术论文 |
| 4 | Synopsys / Cadence / Siemens 官方博客 | EDA 更新 |
| 5 | ARM Developer / RISC-V International | 协议规范 |
| 6 | Chiplet Marketplace / Converge Digest | Chiplet 生态 |
| 7 | Tom's Hardware / AnandTech | 制程/架构深度分析 |
| 8 | TrendForce / Omdia | 产能/价格数据 |
| 9 | 中国半导体行业协会 / 芯思想 / 芯智讯 | 国产制程/政策 |
| 10 | 半导体行业观察 / 集微网 | 国产替代进展 |

---

## 依赖

- **[guizang-ppt-skill](https://github.com/Aro1GG/claude-skills)** — 生成杂志风网页 PPT
- **agent-browser** — 访问 SemiEngineering（该站封 WebFetch）
- `references/protocol_versions.md` — 协议版本基线，变化检测对照基准
- `assets/reference_template.md` — 六段式报告模板
- `assets/trend_data_template.json` — JSON 数据模型

---

## 使用方式

在 Claude Code 中安装此技能后：

```
/semiconductor-daily           # 默认完整版
/semiconductor-daily full      # 完整版（六段 + JSON + PPT）
/semiconductor-daily quick     # 快速版（八段精简报告）
```

---

## 文件结构

```
semiconductor-daily/
├── SKILL.md                          # 技能主文件
├── README.md
├── assets/
│   ├── reference_template.md         # 报告模板
│   └── trend_data_template.json      # JSON 数据模型
└── references/
    └── protocol_versions.md          # 协议版本基线
```

---

## 许可证

MIT
