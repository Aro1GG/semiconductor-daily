---
name: semiconductor-daily
description: |
  半导体行业每日/每周技术资讯报告生成器（架构师视角）。当用户需要半导体行业新闻、
  芯片产业动态、协议规范追踪、EDA/IP生态、技术趋势分析时使用。
  面向芯片架构师，强调"每项信息对芯片设计决策意味着什么"。
---

# 半导体行业每日技术资讯报告生成器（架构师视角）

## 设计理念

**面向芯片架构师**。核心原则：每个信息点必须回答 **"这对芯片设计决策意味着什么"**。剔除股价/补贴/M&A等无架构价值的信息。重点追踪：协议规范演进、制程器件状态、EDA/IP 生态、学术架构创新、AI 赋能芯片设计验证。

---

## 核心原则：无变化不展开

**变化检测是强制步骤，不是可选项。**

每个模块在报告正文中展开前，必须先与 `protocol_versions.md` 基线做比对：
- **有实质更新**（新版本/新硅验证/具体数字变化/新政策）→ 详细展开
- **无变化** → 表格中标注"✓"，正文只保留一句话"维持 [上次状态]"

**实质更新的定义：**
- 新版本发布或新修订 Issue
- 新硅验证完成（IP 供应商投片、量产）
- 具体数字变化（良率 ±5% 以上、产能变化、层数增加）
- 重大政策/管制变化
- 新学术论文发表（论文不受变化检测限制，每期必搜）

**不视为实质更新：**
- 厂商官方博客的营销性更新
- 路线图重申而无实际里程碑
- 行业活动/会议声明而无实质产品进展

---

## 模式选择

首先询问用户选择模式（若用户未指定，默认选择完整版）：

- **完整版（full）**：四路并行 Agent 深度搜索 + 架构翻译层 + 六段式报告 + JSON + guizang-ppt-skill 生成网页 PPT（约 25 分钟）
- **快速版（quick）**：单路搜索 + 精简八段式报告（约 10 分钟）

---

## 输出路径

- **默认**：`D:\obsidian\repository\AIgenerate\my_obsidian\半导体行业资讯\`
- **用户可指定**：若用户给出自定义路径，使用该路径
- 文件名格式：`半导体行业资讯日报_YYYYMMDD.md`（日报）或 `半导体行业资讯周报_YYYYMMDD.md`（周报）

---

## 文件依赖

- **PPT 生成技能**：`guizang-ppt-skill` — 生成"电子杂志 × 电子墨水"风格横向翻页网页 PPT（必须已安装）
- **报告格式模板**：`assets/reference_template.md` — 六段式架构师报告结构
- **JSON 模板**：`assets/trend_data_template.json` — 架构数据模型
- **协议规范参考**：`references/protocol_versions.md` — AMBA/UCIe/CXL/PCIe 版本基线（必须读取）

---

## 完整版工作流（Full Mode）

### 阶段零：加载协议基线（必须执行）

读取 `references/protocol_versions.md` 获取当前已知的协议版本基线和上次报告日期。

**阶段一和阶段二之间，增加"变化检测"子阶段（见下）。**

---

### 阶段一：四路并行深度搜索

同时启动 4 个 Agent，各自负责一个维度（必须一次消息发送 4 个 Agent 调用）。

**搜索方式统一规则**：
- SemiEngineering 是最高优先级来源，但其所有页面（含 `/feed/`）均被 WebFetch 返回 403
- **Agent 在搜索 SemiEngineering 时，必须先使用 agent-browser 技能**（而非 WebFetch）来访问和提取页面内容
- 其他来源可继续使用 WebSearch/WebFetch

**Agent A — 制程与器件**
```
搜索最新半导体工艺和器件进展，必须包含具体数字：

重要：使用 agent-browser 访问 semiengineering.com 获取首页 Top Stories + 本周 Chip Industry Week In Review 文章，提取制程/封装相关段落。WebFetch 访问 SemiEngineering 会失败（403），必须用 agent-browser 技能。

1. 先进制程量产状态：
   - TSMC N2 / Intel 18A / Samsung SF2：良率数字、月产能、PDK成熟度、背面供电状态
   - 重点：agent-browser 打开 TSMC Tech Symposium 2026 文章，提取新节点(A13/A12/N2U)和封装路线图数据

2. GAA 器件与 PDK：
   - Nanosheet/RibbonFET/MBCFET 器件特性差异

3. 先进封装工艺：
   - CoWoS/SoIC 最新产能、交期
   - 混合键合(Hybrid Bonding) pitch 进展
   - 玻璃基板/硅桥/RDL 互连工艺

4. **国产制程与器件**（新增板块）：
   - SMIC：FinFET 先进制程量产状态（N+1/N+2/N+3 等效节点、良率、月产能、主要客户）
   - 华虹半导体：特色工艺平台进展（BCD/IGBT/SiC/GaN）
   - 晶合集成/芯联集成：CIS/DDIC/MEMS 工艺进展
   - 长鑫存储(CXMT)：DRAM 工艺节点（DDR4/DDR5/LPDDR5）、产能扩张、良率
   - 长江存储(YMTC)：3D NAND 层数（Xtacking 架构）、产能、设备国产化率
   - 国产半导体设备突破：光刻机/刻蚀/薄膜沉积/离子注入/量测 最新进展
   - 美国出口管制对国产先进制程的影响（实体清单/设备限制/EDA 限制）
   - 国产 EDA 工具进展（华大九天/国微/芯华章/概伦）

查询: SMIC N+3 yield mass production || CXMT DDR5 DRAM 2026 || YMTC 3D NAND layers || 国产光刻机进展 || 华虹半导体 制程 || 美国芯片出口管制 2026
```

**Agent B — 协议与互联**
```
搜索最新协议规范和片间互连进展：

重要：先用 agent-browser 访问 semiengineering.com 获取本周 NoC Coherency 和 Chiplet Standards 专题文章的正文内容（WebFetch 会403）。

1. 协议版本追踪（参照 references/protocol_versions.md 已知版本，重点搜索是否有新版本/新修订）：
   - AMBA CHI：Issue H 之后是否有新修订？snoop filter 层级化扩展状态
   - AMBA AXI：Issue L Credit-Based Transport 硅验证状态、NoC IP 支持情况
   - UCIe：3.0 64GT/s PHY 各厂商投片状态(GUC/Cadence/Alphawave/Synopsys)
   - CXL：3.1 规范细节、内存池化/多级交换的硅实现
   - PCIe 6.0：SerDes PHY 可用性、与 UCIe 3.0 的速率对齐
   - JEDEC HBM4：逻辑基底 die 可编程能力、定制化 HBM 接口

2. Die-to-Die 互连 IP 生态：
   - 各 IP 厂商(GUC/Cadence/Synopsys/Alphawave) UCIe 3.0 PHY 硅验证状态
   - AXI/CHI over UCIe 的桥接方案
   - CXL/PCIe/UCIe 多协议整合方案

3. Chiplet 架构方法学：
   - OCP FCSA 规范最新采纳状态
   - 中国 GB/T 46280 Chiplet 标准落地进展
   - multi-die 一致性的设计验证方法论

查询: AMBA CHI Issue H AXI Issue L || UCIe 3.0 64Gbps silicon || FCSA OCP chiplet || CXL 3.1 specification
```
```
搜索最新 EDA 工具、半导体 IP、AI 赋能芯片设计验证的进展：

重要：先用 agent-browser 访问 semiengineering.com 获取 Creating Agentic EDA Methodologies、AI Growing Impact On Chip Design 等文章的正文内容。

1. EDA 工具 2nm 认证：
   - Synopsys Fusion Compiler / Cadence Innovus / Siemens Aprisa 对 N2 GAA 的认证状态
   - 时序分析工具(PrimeTime/Tempus)对 BSPD 的支持
   - IR-drop/EM 分析对背面供电网络的适配

2. 验证与仿真：
   - formal verification 对 multi-die 一致性的支持
   - emulation (Veloce/Zebu/Palladium) 对 Chiplet/2nm 规模设计的容量
   - UVM/Portable Stimulus 最新方法学

3. 半导体 IP 生态：
   - ARM Neoverse CSS (Compute Subsystem) 最新版本
   - RISC-V 商用 IP 核最新规格(SiFive P870-D / Tenstorrent Ascalon-X / Andes / Codasip)
   - HBM4 Memory Controller IP 可用性
   - UCIe 3.0 Controller + PHY IP 供应商清单和投片状态

4. **AI 赋能芯片设计验证（重点板块）**：
   - LLM 辅助 RTL 设计：自然语言生成 RTL、自动 testbench 生成、RTL 代码审查
   - AI 驱动综合与 PPA 优化：Synopsys DSO.ai / Cadence Cerebrus 最新 benchmarks
   - AI 辅助物理设计：布图规划/时钟树综合/布线优化
   - AI 辅助验证：ML 驱动覆盖率收敛/形式化属性自动生成/智能 debug
   - AI 辅助 DFT：测试向量自动生成/故障覆盖率优化
   - 大厂实践案例：
     ○ Google/Amazon/NVIDIA 内部 AI 辅助芯片设计流程
     ○ Siemens Veloce + Nvidia AI 加速验证案例
     ○ 新创公司：AI-native EDA 工具(如 Anari/Motivo/ChipStack)
     ○ 学术突破：NeurIPS/MLSys 2026 AI辅助芯片设计论文

5. 开源工具链：
   - OpenROAD/OpenLane 对先进节点的支持
   - Yosys/Verilator/iverilog 生态更新

查询: Synopsys DSO.ai benchmark 2nm || Cadence Cerebrus PPA ||
       LLM RTL generation chip design 2026 || AI verification coverage ||
       ML physical design floorplan || Siemens Veloce Nvidia ||
       ARM Neoverse CSS || HBM4 controller IP || UCIe 3.0 PHY
```

**Agent D — 学术论文**
```
搜索最新半导体学术论文（架构相关），使用 WebSearch：

1. 架构创新：
   arXiv cs.AR: SoC chiplet accelerator NoC architecture 2026
   关注：新计算范式(CIM/近存计算/光计算)、NoC 拓扑创新、chiplet 一致性协议

2. AI 加速器：
   arXiv cs.AR: AI accelerator DNN inference training 2026
   关注：Transformer 加速、稀疏计算、混合精度、HBM4 带宽优化

3. 神经形态与新型计算：
   arXiv cs.ET: neuromorphic spiking compute-in-memory 2026
   关注：SNN 芯片、忆阻器阵列、存算一体

4. **AI 赋能芯片设计论文**：
   arXiv cs.AR: AI chip physical design RTL verification automation
   关注：ML for EDA、AI 驱动时序优化、LLM 辅助验证

5. RISC-V 生态：
   arXiv cs.AR: RISC-V processor accelerator 2026
   关注：RVV 向量扩展 AI 性能、RISC-V Chiplet、开源 NoC

6. 后量子密码硬件：
   arXiv cs.CR: post-quantum cryptography hardware accelerator
   关注：NTT 加速单元、ML-KEM/ML-DSA 硬件实现、侧信道防护

每篇论文必须记录：标题、arXiv 编号/会议 Session 号、作者机构、核心性能数字、架构创新点
```

**关键**：四个 Agent 必须并行启动（一次消息发送 4 个 Agent 调用），等待全部完成后进入阶段二。

---

### 阶段二：架构影响翻译层（必须执行）

对每个 Agent 的搜索发现，执行"架构影响翻译"。每条关键发现必须标注：

```
发现: [原始搜索结果]
架构影响: [1-2 句：对 RTL/微架构/物理设计/验证流程的具体影响]
优先级: [P0/P1/P2] — P0=当前项目立即受影响, P1=6-12个月内, P2=中长期关注
```

---

### 阶段二点五：变化检测（内部流程，不写入报告）

**在整合报告前，必须完成此步骤（但输出报告不显式标注对比）：**

1. 读取 `references/protocol_versions.md`，记录每个协议/制程的"上次状态"
2. 将阶段一的搜索结果逐条与基线比对
3. 对每个模块做出判定：

| 判定 | 含义 | 报告处理方式 |
|------|------|------------|
| **有实质更新** | 新版本/新硅验证/具体数字变化/新政策 | 详细展开 |
| **无变化** | 与基线一致或无新进展 | 正文精简为一句话，不标注来源 |

4. 变化检测结果仅供内部整合使用，**不写入报告正文**

---

### 阶段三：按模板整合报告

**必须读取 `assets/reference_template.md` 了解完整六段式结构。**

**数据整合优先级规则**：
SemiEngineering 通过 agent-browser 直接采集的正文内容具有**最高优先级**（来源权威、分析深度高、覆盖全面）。SemiEngineering 数据应与 Agent 搜索的其他来源交叉验证。如果一个主题在两者中均有覆盖，以 SemiEngineering 的分析为主，Agent 搜索为补充。若矛盾，以 SemiEngineering 为准。

根据阶段二点五的变化检测结果：
- **有变化的模块**：详细展开，包含具体数字和架构影响
- **无变化的模块**：正文精简为一句话，**不标注"较上次无变化"等来源说明**

**执行摘要要求：**
- 一句话概括本周对芯片架构决策影响最大的 3 件事（只说有实质变化的）
- 无变化的协议/制程不列在执行摘要中

---

### 阶段四：生成 PPT 文件

1. 从报告中提取核心数据，构建 JSON 文件（参照 `assets/trend_data_template.json`）

2. **调用 guizang-ppt-skill 生成杂志风网页 PPT**：
   - 将报告内容转换为 PPT 页面，使用 guizang-ppt-skill 的布局模板：
     - 封面页（报告标题 + 日期 + 来源标识）
     - 章节幕封（执行摘要 / 协议与互联 / 制程与器件 / 学术论文 / EDA 与 IP / 架构决策）
     - 数据大字报（P0 行动项 / 关键数字）
     - 图文混排（协议版本对比 / 制程就绪度评估）
     - 图片网格（学术论文精选）
     - 结尾页（来源索引 + 协议迁移路线图）
   - 主题选择：推荐使用"ink classic"（墨典）或"indigo porcelain"（靛瓷），匹配技术报告风格
   - 输出单 HTML 文件：`半导体行业资讯日报_YYYYMMDD.html`

3. **禁止在 PPT 中直接粘贴大段文字**：每页 PPT 内容必须经过提炼，使用关键词+数字+架构影响一句话的形式呈现，详细数据保留在 Markdown 报告中供查阅。

4. 验证 3 个输出文件（.md 报告 + .json 数据 + .html PPT）

---

## 快速版工作流（Quick Mode）

快速版跳过学术论文和 EDA/IP 生态深度搜索，聚焦"协议+制程+重大架构突破+AI 赋能芯片设计"。

**快速版同样执行变化检测规则**——无变化不展开。

单路搜索关键词：
- 协议：`AMBA CHI AXI latest update || UCIe 3.0 chiplet || CXL 3.1 || HBM4 JEDEC`
- 制程：`TSMC N2 Intel 18A Samsung SF2 latest yield mass production`
- 国产制程：`SMIC N+3 yield || CXMT DRAM || YMTC 3D NAND || 国产半导体设备`
- 架构：`AI chip architecture NoC chiplet innovation 2026`
- AI赋能：`AI chip design verification RTL physical design EDA`

快速版报告结构（八段式）：

```
# 一、本周最重要的 3 件事（只列有变化的）
# 二、协议与互联更新（精简表，标注✓无变化）
# 三、制程动态（含国产制程板块，标注✓无变化）
# 四、AI 芯片架构突破（新论文/新进展）
# 五、AI 赋能芯片设计案例
# 六、学术速览（3-5 篇最重要论文）
# 七、架构师 P0 行动项
# 八、本周关键词（变化列表）
```

---

## 数据质量要求

### 命名规则
- 报告文件：`半导体行业资讯日报_YYYYMMDD.md`
- JSON 文件：`trend_data_YYYYMMDD.json`
- PPT 文件：`半导体行业资讯日报_YYYYMMDD.html`（guizang-ppt-skill 生成）

### 必须包含的数据特征
1. **具体数字**：良率 %、带宽 TB/s 或 Gbps、功耗 W 或 pJ/b、面积 mm²、速度 GHz
2. **协议版本号**：每次报告固定追踪协议版本变化
3. **硅验证状态**：PHY 是否已投片？IP 是否 available？
4. **架构影响注释**：每条关键发现附"架构影响"标注
5. **行动等级**：每条建议标 P0/P1/P2

### 搜索源优先级（技术深度优先，不使用财经/一般新闻源）
1. **SemiEngineering**（工程进展）— **必须使用 agent-browser 访问**（WebFetch 返回403），直接采集首页 Top Stories + 本周 Review + TSMC 专题文章
2. SemiWiki（深度分析）
3. IEEE Xplore / arXiv（学术论文）
4. Synopsys / Cadence / Siemens 官方博客（EDA 更新）
5. ARM Developer / RISC-V International（协议规范）
6. Chiplet Marketplace / Converge Digest（Chiplet 生态）
7. Tom's Hardware / AnandTech（制程/架构深度分析）
8. TrendForce / Omdia（产能/价格数据，仅限此用途）
9. 中国半导体行业协会 / 芯思想 / 芯智讯（国产制程/政策/产业动态）
10. 半导体行业观察 / 集微网（国产替代进展/设备材料突破）

