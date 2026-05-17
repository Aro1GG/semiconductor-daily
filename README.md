# semiconductor-daily

半导体行业每日/每周技术资讯报告生成器（架构师视角）—— Claude Code Agent 技能。

> 面向芯片架构师的自动化技术情报系统。自动搜索全球半导体技术源，经「架构影响翻译层」处理后输出结构化报告。每项信息都回答 **「这对芯片设计决策意味着什么」**。

---

## 用户工艺定位

本技能围绕用户的**实际工艺平台**设计搜索策略：

- **核心平台**：SMIC N+2（等效 7nm FinFET，DUV 多重曝光，量产）/ N+3（等效 5nm，研发中）
- **海外先进工艺**（TSMC N2/Intel 18A/Samsung SF2）：仅供技术趋势追踪，每条发现必须附带"对 N+2/N+3 的可迁移启示"
- **报告重心**：国产同代节点的良率提升、低功耗设计、DUV 多重曝光优化、设备/EDA 国产化
- **成熟节点**：仅华虹/晶合等特色工艺按需追踪，不作为报告主体

---

## 核心原则：无变化不展开

变化检测是**强制步骤**。每期生成前与 `references/protocol_versions.md` 基线比对：

| 判定 | 含义 | 报告处理 |
|------|------|----------|
| **有实质更新** | 新版本 / 新硅验证 / 具体数字变化 / 新政策 | 详细展开 |
| **无变化** | 与基线一致或无新进展 | 一句话带过 |

---

## 两种模式

| 模式 | 耗时 | 搜索方式 | 产出 |
|------|------|----------|------|
| **完整版 (full)** | ~30 min | 五路并行 Agent 深度搜索 | `.md` + `.json` + `.html` PPT |
| **快速版 (quick)** | ~10 min | 单路搜索 | `.md` 精简报告 |

---

## 工作流（完整版）

```
阶段零：加载协议基线 (protocol_versions.md)
    ↓
阶段一：五路并行 Agent 搜索
    ├── Agent A — 制程与器件（N+2/N+3 核心 + 海外追踪 + 设备/EDA/低功耗）
    ├── Agent B — 协议与互联（AMBA/UCIe/CXL/PCIe/HBM/Chiplet/NoC/灵衢）
    ├── Agent C — EDA 与 IP（双轨：海外追踪 + 国产 EDA 对 N+2/N+3 支持）
    ├── Agent D — 学术论文（arXiv cs.AR/cs.ET/cs.CR 9 个方向）
    └── Agent E — 专利搜索（7 个数据库 8 个分类）
    ↓
阶段二：架构影响翻译层
    每条发现 → 架构影响 1-2 句 + 优先级 P0/P1/P2
    ↓
阶段二点五：变化检测（内部流程，不写入报告）
    ↓
阶段三：按模板整合 Markdown 报告
    ↓
阶段四：生成 JSON + 调用 guizang-ppt-skill 生成杂志风网页 PPT
```

---

## 覆盖维度

### 协议与互联
AMBA CHI Issue H / AXI Issue L、UCIe 3.0、CXL 4.0、PCIe 7.0、JEDEC HBM4/HBM4E、UALink 1.0、OCP FCSA、中国 GB/T 46280 Chiplet 标准、华为灵衢

### 制程与器件（核心板块：N+2/N+3）
SMIC N+2 量产状态（良率/PDK/DUV 多重曝光）、N+3 研发进展、N+2/N+3 低功耗器件特性、DUV 缺陷模式与良率优化。海外先进工艺追踪（TSMC N2/Intel 18A/Samsung SF2 + 可迁移启示）。先进封装（CoWoS/Hybrid Bonding/玻璃基板）

### 国产设备与 EDA
国产光刻机（SMEE DUV）、刻蚀/薄膜沉积/离子注入/量测对 N+2 支持。华大九天/国微/芯华章/概伦对 N+2/N+3 PDK 的适配。美国出口管制影响。

### 学术论文（9 个方向）
AI 加速器、Chiplet & NoC、神经形态计算、AI 赋能芯片设计、RISC-V 生态、后量子密码硬件、低功耗架构、功耗/能效建模、良率提升方案

### EDA & IP 生态（双轨视角）
海外：Synopsys/Cadence/Siemens 2nm 认证、DSO.ai/VSO.ai、HBM4 IP、UCIe 3.0 PHY。国产：华大九天 N+2 signoff 工具链、OpenROAD SMIC 支持、低功耗 IP 对 N+2 可用性

### AI 赋能芯片设计（重点板块）
LLM 辅助 RTL 设计、AI 驱动综合与 PPA 优化、AI 辅助物理设计/验证/DFT、Synopsys.ai Copilots、Google/Amazon/NVIDIA 实践

---

## 输出文件

| 文件 | 说明 |
|------|------|
| `半导体行业资讯日报_YYYYMMDD.md` | 六段式 Markdown 架构师报告 |
| `trend_data_YYYYMMDD.json` | 结构化趋势数据 |
| `半导体行业资讯日报_YYYYMMDD.html` | 杂志风横向翻页网页 PPT |

---

## 报告结构

### 完整版（七段式）

1. **架构师执行摘要** — 本周最影响架构决策的 3 件事
2. **协议与互联标准演进** — 各协议版本状态 + 变化检测
3. **制程与器件进展** — N+2/N+3 核心板块 + 海外追踪 + 低功耗
4. **学术论文精选** — 9 个方向精选论文
5. **设计工具链与 IP 生态** — 双轨 EDA + AI 赋能 + 低功耗 IP
6. **最新专利动态** — 8 个分类 20 条专利
7. **架构决策影响分析** — P0 行动项 + P1 技术储备 + P2 趋势 + 路线图

### 快速版（九段式）

1. 本周最重要的 3 件事
2. 协议与互联更新
3. SMIC N+2/N+3 国产先进工艺动态
4. 海外先进工艺追踪（附可迁移启示）
5. AI 赋能芯片设计案例
6. 专利速览（3-5 条）
7. 学术速览（3-5 篇）
8. 架构师 P0 行动项
9. 本周关键词

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
| 9 | 中国半导体行业协会 / 芯思想 / 芯智讯 | SMIC 制程/良率/产能 |
| 10 | 半导体行业观察 / 集微网 | 国产 EDA/设备/材料进展 |
| 11 | SCMP / 南华早报 | 中国半导体产业动态 |
| 12 | 大半导体产业网 / 摩尔精英 | SMIC N+2/N+3 PDK 状态 |

---

## 依赖

- **[guizang-ppt-skill](https://github.com/Aro1GG/claude-skills)** — 生成杂志风网页 PPT
- **agent-browser** — 访问 SemiEngineering（该站封 WebFetch）
- `references/protocol_versions.md` — 协议版本基线，变化检测对照基准
- `assets/reference_template.md` — 报告模板
- `assets/trend_data_template.json` — JSON 数据模型

---

## 使用方式

```
/semiconductor-daily           # 默认完整版
/semiconductor-daily full      # 完整版（七段 + JSON + PPT）
/semiconductor-daily quick     # 快速版（九段精简报告）
```

---

## 文件结构

```
semiconductor-daily/
├── SKILL.md                          # 技能主文件
├── README.md
├── .gitignore
├── assets/
│   ├── reference_template.md         # 报告模板
│   └── trend_data_template.json      # JSON 数据模型
└── references/
    └── protocol_versions.md          # 协议版本基线 + 专利追踪
```

---

## 许可证

MIT
