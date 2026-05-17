# 架构师执行摘要

> 本周最影响芯片架构决策的 3 件事：
> 1. **[P0 事项]** — 架构影响: [一句话]
> 2. **[P1 事项]** — 架构影响: [一句话]
> 3. **[P2 关注]** — 长期意义: [一句话]

## 协议与互联标准状态一览

| 协议 | 最新版本 | 硅验证状态 | 架构影响 | 优先级 |
|------|---------|-----------|---------|--------|
| AMBA CHI | Issue H | Synopsys VIP 已支持 | snoop filter 层级化扩展 | P1 |
| AMBA AXI | Issue L (2025.8) | Credit-Based 模式待验证 | 需重新设计 NoC 流控 FSM | **P0** |
| UCIe | 3.0 | GUC/Cadence N3P 64GT/s 已投片 | PHY 可用性确认 | **P0** |
| CXL | 3.1 | 硅验证进行中 | 内存池化多级交换 | P1 |
| PCIe | 6.0 | SerDes PHY 可用 | 与 UCIe 3.0 速率对齐 | P1 |
| JEDEC HBM | HBM4 | Samsung 量产, SK hynix 量产 | 2048-bit 接口，MC 必须重新设计 | **P0** |

## 制程就绪度评估

| 工艺节点 | PDK 成熟度 | EDA 认证 | PHY 可用性 | 建议开始设计时间 |
|---------|-----------|---------|-----------|--------------|
| TSMC N2 | 0.9 (GA版本) | Synopsys/Cadence 已认证 | 基础 IP 已可用 | 现在可开始 |
| Intel 18A | 0.7 (早期) | 认证进行中 | 部分 IP 可用 | 2026 Q3 |
| Samsung SF2 | 0.6 (试产) | 认证进行中 | 部分 IP 可用 | 2026 Q4 |
| TSMC N2P | 0.3 (规划) | 待认证 | 待定 | 2027 |

---

# 第一部分：协议与互联标准演进

> **无变化的协议，正文精简为一句话；有实质更新的协议，详细展开。**

## 1. UCIe 3.0 Die-to-Die 互连

> UCIe 3.0 维持 64 GT/s，GUC/Cadence N3P 投片验证已完成。

如本周有实质更新，则展开：
**实质更新**：新增 Alphawave Semi 64GT/s 投片 / 新封装方案 / 新速率

## 2. AMBA CHI Issue H / AXI Issue L

> AMBA AXI Issue L 维持 Credit-Based Transport 待验证状态，CHI Issue H 维持 Synopsys VIP 支持状态。

## 3. CXL 3.1 与 PCIe 6.0

> CXL 3.1 维持内存池化语义扩展状态，PCIe 6.0 维持 SerDes PHY 可用状态。

## 4. JEDEC HBM4

> HBM4 维持 Samsung/SK hynix 量产状态，HBM4E 维持 Samsung 5月出样预期。

## 5. Chiplet 架构方法学

> OCP FCSA (2026.04.09) 和 GB/T 46280 (2026.03.01) 维持已发布/已生效状态。

---

# 第二部分：制程与器件进展

## 0. 海外先进工艺追踪（技术方向参考）

> TSMC N2 / Intel 18A / Samsung SF2 仅供技术趋势追踪，每条附带"对 N+2/N+3 的可迁移启示"。

**篇幅控制**：此板块不超过制程部分总篇幅的 30%。

## 1. SMIC N+2/N+3 国产先进工艺（核心板块）

### 1.1 N+2 量产状态
> N+2 (等效 7nm FinFET, DUV 多重曝光)。如有良率/产能/PDK/新客户等实质更新，则详细展开。

- N+2 最新良率（D0 数据）
- DUV 多重曝光（LELE/LELELE）的 overlay 精度和 CD 均匀性
- PDK 成熟度：器件模型精度、corner model、互连线 RC 提取
- 主要客户和 tape-out 数量

### 1.2 N+3 研发进展
> N+3 (目标等效 5nm)。如有实质更新，则详细展开。

- EUV 替代方案：DUV 多重曝光极限 or 国产 EUV 进展
- 目标规格（性能/功耗/密度对标 N5 的比例）
- 预计 PDK 开放时间表

### 1.3 N+2/N+3 低功耗器件特性
- multi-Vt 库范围和漏电特性
- FinFET 沟道工程国产实现
- 电源门控(power gating)工艺级支持
- DVFS 支持的电压域数量和粒度

### 1.4 N+2/N+3 良率提升关键技术
- DUV 多重曝光特有的缺陷模式
- 缺陷密度（D0）趋势
- 工艺窗口优化
- SRAM 良率和修复方案

## 2. 国产特色工艺与存储
> CXMT/YMTC/华虹 如有实质更新（产能/良率/层数/管制），则详细展开。

## 3. 国产半导体设备与 EDA 突破
> 国产光刻机/刻蚀/薄膜沉积/离子注入/量测设备对 N+2/N+3 的支持状态。华大九天/国微/芯华章/概伦等国产 EDA 对 N+2/N+3 PDK 的支持。

## 4. 先进封装（CoWoS/混合键合/玻璃基板）
> 如有实质更新（产能/良率/pitch），详细展开。每条附"对国产 Chiplet 封装的可迁移启示"。

## 5. 制程级低功耗技术
- FinFET 漏电控制（GIDL/BTBT/栅极漏电）
- 近阈值/亚阈值计算的 FinFET 工艺支持
- multi-Vt 优化策略在国产工艺上的可行性

---

# 第三部分：学术论文精选

> 学术论文不受变化检测限制，每期均重新搜索，选取最新发表的论文。

## AI 加速器架构

## Chiplet 与 NoC

## 神经形态与新型计算

## AI 赋能芯片设计

## RISC-V 生态

## 后量子密码硬件

## 低功耗架构与设计方法学

## 功耗/能效建模仿真方法学

## 良率提升学术方案

---

# 第四部分：设计工具链与 IP 生态

## 1. EDA 2nm 就绪度

> Fusion Compiler / Innovus / PrimeTime / Calibre 认证状态较上次无显著变化。

## 2. AI 赋能芯片设计进展

> AI 赋能板块不受变化检测限制，此领域进展快，每期应选取最新发布。

### 2.1 LLM 辅助 RTL 设计
### 2.2 AI 驱动综合与 PPA 优化
### 2.3 AI 辅助物理设计
### 2.4 AI 辅助验证
### 2.5 AI 辅助 DFT
### 2.6 大厂实践案例

## 3. IP 可用性清单

> IP 可用性如有变化（新增供应商/新节点支持/新 IP 发布），则详细列出。其余维持。

## 4. 功耗/能效建模仿真工具

> 功耗建模和热仿真工具如有实质更新，则详细展开。

### 4.1 Power Estimation 工具（PrimePower / Joules / RedHawk）
### 4.2 Thermal Simulation 工具（RedHawk-SC Electrothermal / Celsius）
### 4.3 IR-drop/EM 分析对先进节点的适配
### 4.4 AI 驱动功耗建模

## 5. AI 驱动功耗与良率优化

### 5.1 AI 驱动功耗优化（DSO.ai Power / Cerebrus SmartPower）
### 5.2 良率分析 EDA 工具（PrimeYield / Legato / Calibre Yield 增强）
### 5.3 ML 驱动缺陷检测与良率预测

## 6. 低功耗 IP

### 6.1 低功耗 NoC IP（Arteris / NetSpeed / FlexNoC 能效特性）
### 6.2 低功耗 SerDes PHY
### 6.3 低功耗内存控制器 IP（LPDDR6 / HBM low-power mode）
### 6.4 电源管理 IP（DVFS controller / voltage regulator / power switch）

---

# 第四点五部分：最新专利动态（本期新增/关键授权）

> 专利不受变化检测限制，每期必搜，取最近 6 个月公布/授权的最相关专利。

## 1. 先进制程专利

| 专利号 | 受让人 | 关键技术点 | 架构影响 | 来源 |

## 2. 先进封装专利

## 3. 协议与互连专利（NoC / 灵衢 / UCIe / CXL）

## 4. AI 芯片架构专利

## 5. 提高能效专利

## 6. 功耗/能效建模仿真专利

## 7. 落后工艺良率提升专利

## 8. 国产芯片专利（CNIPA）

---

# 第五部分：架构决策影响分析

## 近期 P0 行动项（当前项目立即受影响）

1. **[协议]** [具体行动项]
2. **[IP 选型]** [具体行动项]
3. **[制程]** [具体行动项]

## 中期 P1 技术储备（6-12 个月内）

## 长期 P2 趋势跟踪

## 协议迁移路线图

```
2026 Q2  Q3  Q4  2027    2028    2029    2030
───────────────────────────────────────────────
AXI-L 评估→采用→
CHI-H ──────────────────→CHI Issue I(预期)
UCIe 3.0 64G ──────────────────→UCIe 4.0(预期)
CXL 3.1───────────────────→CXL 4.0(预期)
HBM4 量产→HBM4E─────→HBM5(预期)
N2GAA→N2P→A16→A14
```

---

# 第六部分：来源索引

## 搜索数据源（按技术价值排序）
1. SemiEngineering — 工程进展
2. SemiWiki — 深度分析
3. IEEE Xplore / arXiv — 学术论文
4. Synopsys / Cadence / Siemens 官方博客 — EDA 更新
5. ARM Developer / RISC-V International — 协议规范
6. Chiplet Marketplace / Converge Digest — Chiplet 生态
7. Tom's Hardware / AnandTech — 制程/架构深度分析
8. TrendForce / Omdia — 产能/价格数据，仅限此用途
9. 中国半导体行业协会 / 芯思想 / 芯智讯 — 国产制程/政策/产业动态
10. 半导体行业观察 / 集微网 — 国产替代进展/设备材料突破

---

*生成日期：YYYY-MM-DD | 报告周期：每周/每日*
