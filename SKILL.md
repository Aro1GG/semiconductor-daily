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

### 用户工艺定位（强制遵循）

- **核心平台**：SMIC N+2（等效 7nm，DUV 多重曝光，量产）/ N+3（等效 5nm，研发中）
- **海外先进工艺**（TSMC N2/Intel 18A/Samsung SF2）：仅供技术趋势追踪，**每条发现必须附带"对 N+2/N+3 的可迁移启示"**
- **报告重心**：国产同代节点的良率提升、低功耗设计、DUV 多重曝光优化、设备/EDA 国产化
- **成熟节点**（28nm 及以上）：华虹/晶合等特色工艺（BCD/IGBT/SiC/GaN）按需追踪，不作为报告主体

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

- **完整版（full）**：五路并行 Agent 深度搜索 + 架构翻译层 + 七段式报告（含专利） + JSON + guizang-ppt-skill 生成网页 PPT（约 30 分钟）
- **快速版（quick）**：单路搜索 + 精简九段式报告（含专利速览）（约 10 分钟）

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

## 最高优先级网络访问规则

**所有外部链接访问必须优先使用 playwright-cli。WebFetch 不可用（SemiEngineering 返回 403，多数来源受限），WebSearch 不可用（API 模型不支持 tool_choice）。**

```
访问优先级：
1. playwright-cli goto + eval（高成功率，支持 JS 渲染，已验证 21/22 来源可用）
2. agent-browser（仅当页面需要复杂 GUI 交互如登录/表单提交时使用）
3. 不使用 WebFetch/WebSearch（已证实不可靠）
```

**playwright-cli 标准流程：**
```bash
# 1. 打开浏览器会话并导航
npx --no-install playwright-cli -s=<session_name> open "https://target-url.com/"

# 2. 提取内容：使用 eval 执行 JS 提取文本/链接
npx --no-install playwright-cli -s=<session_name> --raw eval "JSON.stringify([...document.querySelectorAll('a')].filter(a=>a.href&&a.textContent.trim().length>10).slice(0,30).map(a=>({title:a.textContent.trim(),href:a.href})))"

# 3. 提取正文段落
npx --no-install playwright-cli -s=<session_name> --raw eval "JSON.stringify([...document.querySelectorAll('p')].map(p=>p.textContent.trim()).filter(t=>t.length>40).slice(0,20))"

# 4. 关闭会话
npx --no-install playwright-cli -s=<session_name> close
```

---

## 完整版工作流（Full Mode）

### 阶段零：加载协议基线（必须执行）

读取 `references/protocol_versions.md` 获取当前已知的协议版本基线和上次报告日期。

**阶段一和阶段二之间，增加"变化检测"子阶段（见下）。**

---

### 阶段一：五路并行深度搜索

同时启动 5 个 Agent，各自负责一个维度（必须一次消息发送 5 个 Agent 调用）。

**访问方式统一规则：**
- **所有来源（含 SemiEngineering）优先使用 playwright-cli** 访问和提取页面内容
- agent-browser 仅在需要复杂 GUI 交互时使用
- WebFetch/WebSearch 仅作为 playwright 失败后的最后降级手段

**Agent A — 制程与器件**
```
搜索最新半导体工艺和器件进展，必须包含具体数字。

重要：使用 playwright-cli 访问 semiengineering.com 获取首页 Top Stories + 本周 Chip Industry Week In Review 文章正文内容。

## 板块零：海外先进工艺追踪（仅供技术方向参考）
   - TSMC N2 / Intel 18A / Samsung SF2：良率数字、月产能、PDK成熟度、GAA器件特性
   - CoWoS/SoIC 最新产能、交期；混合键合(Hybrid Bonding) pitch 进展
   - **强制要求**：每条海外发现必须附带"对 N+2/N+3 的可迁移启示"
   - **篇幅控制**：此板块占 Agent A 总输出的 30% 以内

## 板块一：SMIC N+2/N+3 国产先进工艺（核心板块，占 40%+ 篇幅）
   这是报告最重要的板块。N+2 等效 7nm FinFET，使用 DUV 多重曝光（无 EUV），N+3 目标等效 5nm。

1. N+2 量产状态：
   - 最新良率数字（与上期对比，±5% 需展开）、月产能变化
   - DUV 多重曝光（LELE/LELELE）的 overlay 精度和 CD 均匀性进展
   - PDK 成熟度：器件模型精度、corner model、互连线 RC 提取精度
   - 主要客户和 tape-out 数量

2. N+3 研发进展：
   - 等效 5nm 目标规格（性能/功耗/密度对标 N5 的比例）
   - EUV 替代方案：DUV 多重曝光极限探索 or 国产 EUV 光刻机进展
   - 预计 PDK 开放时间表

3. N+2/N+3 低功耗器件特性：
   - multi-Vt 库的 Vt 范围和漏电特性
   - FinFET 沟道工程（channel doping、strain engineering）的国产实现
   - 电源门控(power gating)工艺级支持：power switch 单元库、IR-drop 特性
   - DVFS 支持的电压域数量和粒度

4. N+2/N+3 良率提升关键技术：
   - DUV 多重曝光特有的缺陷模式（LELE 边界对齐误差、光刻胶残留）
   - 缺陷密度（D0）最新数据和趋势
   - 工艺窗口优化（focus/exposure dose 窗口）
   - CMP 平坦化对多层互连良率的影响
   - SRAM 良率（bit cell yield）和修复方案

## 板块二：国产特色工艺与存储
   - 华虹半导体：BCD/IGBT/SiC/GaN 特色工艺进展、出口管制影响
   - 长鑫存储(CXMT)：DDR5 良率（是否仍~50%？）、DDR5 芯片面积（比海外大~40%）、HBM2/HBM3 进展
   - 长江存储(YMTC)：3D NAND 层数、Xtacking 架构、设备国产化率

## 板块三：国产半导体设备与 EDA 突破
   - 国产光刻机：DUV 浸没式光刻机（SMEE）对 N+2 的支持状态
   - 国产刻蚀/薄膜沉积/离子注入/量测设备对 N+2/N+3 的适配进展
   - 美国出口管制最新动态（实体清单变化/设备限制新规/EDA 限制）
   - 国产 EDA 工具（华大九天/国微/芯华章/概伦）对 N+2/N+3 PDK 的支持状态
   - 国产 SPICE model 精度和签核(signoff)工具链覆盖度

## 板块四：先进制程级低功耗技术
   - FinFET 先进漏电控制（GIDL/BTBT/栅极漏电）
   - 近阈值/亚阈值计算在 FinFET 上的工艺支持
   - multi-Vt 优化策略（正向/反向体偏置在国产工艺上的可行性）

查询: SMIC N+2 yield 2026 DUV multi-patterning || SMIC N+3 process node 5nm equivalent || SMIC FinFET PDK || DUV LELE overlay defect density || SMIC low power FinFET multi-Vt || CXMT DDR5 yield die size || YMTC 3D NAND Xtacking || 国产光刻机 SMEE DUV || 华大九天 EDA N+2 || 美国芯片出口管制 SMIC 2026
```

**Agent B — 协议与互联**
```
搜索最新协议规范和片间互连进展：

重要：先用 playwright-cli 访问 semiengineering.com 获取本周 NoC Coherency 和 Chiplet Standards 专题文章正文内容。

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

4. **NoC 与低功耗互连**（新增板块）：
   - NoC 路由/仲裁拓扑创新（mesh/torus/butterfly/层次化）
   - 华为灵衢：灵衢互连架构最新进展、生态伙伴、硅验证状态
   - NoC 功耗优化技术（clock gating、DVFS、power gating 在 NoC 中的应用）
   - **低功耗互连标准**：UCIe/LPDDR 能效对比、低功耗 SerDes PHY
   - Die-to-Die 互连能效（pJ/b 指标追踪）

查询: AMBA CHI Issue H AXI Issue L || UCIe 3.0 64Gbps silicon || FCSA OCP chiplet || CXL 3.1 specification || NoC router arbitration topology || 华为灵衢 互连 || low power interconnect pJ/b NoC || UCIe LPDDR energy efficiency
```

**Agent C — EDA 与 IP（架构师双轨视角）**
```
搜索最新 EDA 工具、半导体 IP、AI 赋能芯片设计验证的进展。

重要：本 Agent 采用"双轨视角"——海外先进 EDA/IP 追踪 + 国产替代 EDA/IP 落地并行。

重要：先用 playwright-cli 访问 semiengineering.com 获取 Creating Agentic EDA Methodologies、AI Growing Impact On Chip Design 等文章的正文内容。

## 轨道一：海外先进 EDA/IP 追踪（40% 篇幅）

1. EDA 工具 2nm 认证：
   - Synopsys Fusion Compiler / Cadence Innovus / Siemens Aprisa 对 N2 GAA 的认证状态
   - 时序分析工具(PrimeTime/Tempus)对 BSPD 的支持
   - **每条附带"对国产 FinFET 节点的可迁移方法论"**

2. AI 赋能芯片设计验证（重点板块）：
   - Synopsys DSO.ai / Cadence Cerebrus 最新 benchmarks
   - LLM 辅助 RTL 设计/验证（自然语言→RTL、自动 testbench、覆盖率收敛）
   - 大厂实践（Google/Amazon/NVIDIA 内部 AI 辅助芯片设计流程）

3. 半导体 IP 生态：
   - HBM4 Memory Controller IP / UCIe 3.0 Controller + PHY IP 投片状态
   - ARM Neoverse CSS / RISC-V 商用 IP 核最新规格

## 轨道二：国产 EDA/IP 对 N+2/N+3 的支持（核心板块，60% 篇幅）

4. 国产 EDA 工具 N+2/N+3 适配状态：
   - 华大九天：Analog/RF 仿真器对 N+2 PDK 的支持、signoff 工具链覆盖度
   - 国微/芯华章：数字仿真/综合工具对 N+2 标准单元库的支持
   - 概伦电子：SPICE model 精度、器件建模对 N+2 FinFET 的覆盖
   - 国产 EDA 与 Synopsys/Cadence 在 N+2 节点上的功能差距清单

5. AI 赋能国产芯片设计：
   - 国产 EDA + AI 的进展（华大九天等是否有 AI 辅助功能？）
   - 可获取的 AI 驱动设计优化工具（不依赖受限出口的海外工具）

6. 低功耗/良率相关 EDA 工具：
   - Power estimation（功耗估算）对 N+2 FinFET 节点的支持
   - IR-drop/EM 分析对 DUV 多重曝光互连的适配
   - 良率分析（DFY/DFM）工具对 N+2 的覆盖
   - ML 驱动缺陷检测与良率预测在国产工艺上的应用

7. 低功耗 IP 对 N+2/N+3 的可用性：
   - 低功耗 NoC IP 对 SMIC 工艺的移植状态
   - 低功耗 SerDes PHY / 内存控制器 IP 对 N+2 的支持
   - 开源工具链（OpenROAD/OpenLane/Yosys）对 N+2 的探索

查询: 华大九天 N+2 PDK EDA || SMIC FinFET signoff tools || 国产EDA AI || power estimation IR-drop FinFET || DSO.ai Cerebrus benchmark 2026 || low power NoC SMIC || OpenROAD SMIC N+2
```

**Agent D — 学术论文**
```
搜索最新半导体学术论文（架构相关），使用 WebSearch（论文来源相对稳定）：

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

7. **低功耗架构与设计方法学**（新增板块）：
   arXiv cs.AR: low power chip architecture energy efficiency 2026
   关注：近阈值计算架构、动态功耗管理策略、能效比优化技术、DVFS/power gating 方法学

8. **功耗/能效建模仿真方法学**（新增板块）：
   arXiv cs.AR: power modeling simulation methodology chip 2026
   关注：pre-RTL 功耗预估、热仿真方法学、ML 驱动功耗建模、activity-driven energy analysis

9. **良率提升学术方案**（新增板块）：
   arXiv cs.AR: yield improvement semiconductor manufacturing 2026
   关注：可测试性设计与良率、缺陷容错架构、工艺偏差补偿、design-for-yield 方法学

每篇论文必须记录：标题、arXiv 编号/会议 Session 号、作者机构、核心性能数字、架构创新点
```

**Agent E — 专利搜索**（新增）
```
搜索最新半导体相关专利，优先使用 playwright-cli 访问各专利数据库。

时间窗口：最近 6 个月内公布/授权的专利。

搜索源（按覆盖方向分工）：
- **Google Patents**：全领域主力搜索引擎
- **FreePatentsOnline**：美国授权/申请专利全文检索
- **Justia Patents**：美国专利 + 法律状态追踪
- **WIPO**：国际 PCT 专利申请
- **EPO / Espacenet**：欧洲专利局
- **CNIPA**：中国国家知识产权局（华为海思/SMIC/CXMT/YMTC 等国产芯片专利）
- **KIPO**：韩国知识产权局（Samsung/SK hynix）

搜索分类与关键词：

1. **先进制程专利**：
   GAA transistor patent || nanosheet FET patent || backside power delivery patent || 2nm 3nm process patent
   
2. **先进封装专利**：
   hybrid bonding interconnect patent || CoWoS chiplet stacking patent || glass substrate 3D IC patent

3. **协议与互连专利**（含 NoC / 灵衢）：
   UCIe PHY patent || CXL interconnect patent || PCIe SerDes patent || HBM interface patent
   NoC router arbitration patent || 华为灵衢 patent || network-on-chip topology patent

4. **AI 芯片架构专利**：
   compute-in-memory accelerator patent || sparse neural network ASIC patent || transformer inference chip patent

5. **提高能效专利**：
   low power chip design patent || DVFS power gating patent || near-threshold computing patent || energy-efficient accelerator patent

6. **功耗/能效建模仿真专利**：
   power modeling EDA patent || energy simulation chip patent || thermal-aware design patent || power estimation methodology patent

7. **落后工艺良率提升专利**：
   mature node yield improvement patent || legacy process optimization patent || defect density reduction patent || DUV 工艺 良率 patent

8. **国产芯片专利**（重点 CNIPA）：
   SMIC 制程 patent || CXMT DRAM patent || YMTC 3D NAND patent || 华为海思 patent

每条专利必须记录：专利号、标题、受让人、分类、关键技术点（1 句）、架构影响（1 句）、来源数据库。

专利搜索**不受「无变化不展开」限制**——每期必搜，每期重新提取最新公布/授权的专利。
```

**关键**：五个 Agent 必须并行启动（一次消息发送 5 个 Agent 调用），等待全部完成后进入阶段二。

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

**数据整合优先级规则：**
playwright-cli 直接采集的页面内容具有**最高优先级**（来源权威、分析深度高、覆盖全面）。SemiEngineering 数据应与 Agent 搜索的其他来源交叉验证。如果一个主题在两者中均有覆盖，以 playwright 采集的内容为主，Agent 搜索为补充。若矛盾，以 playwright 采集的内容为准。

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
- 协议：`AMBA CHI AXI latest update || UCIe 3.0 chiplet || CXL 4.0 || HBM4 JEDEC || NoC router || 华为灵衢`
- **国产制程（核心）**：`SMIC N+2 yield DUV multi-patterning || SMIC N+3 5nm equivalent || SMIC FinFET PDK low power`
- 海外制程（追踪）：`TSMC N2 Intel 18A latest yield || CoWoS hybrid bonding`
- 低功耗：`FinFET leakage control multi-Vt DVFS || near-threshold computing`
- 良率：`DUV LELE overlay defect density || SMIC yield improvement || DFM DFY`
- 国产设备/EDA：`国产光刻机 SMEE || 华大九天 EDA SMIC || 美国出口管制 SMIC`
- AI赋能：`AI chip design verification EDA || low power optimization AI`
- 专利：`SMIC patent || FinFET yield patent || low power chip patent || DUV process patent`

快速版报告结构（九段式）：

```
# 一、本周最重要的 3 件事（只列有变化的）
# 二、协议与互联更新（精简表，标注✓无变化）
# 三、SMIC N+2/N+3 国产先进工艺动态（核心板块：良率/产能/低功耗/PDK，标注✓无变化）
# 四、海外先进工艺追踪（精简，每条附"对 N+2/N+3 可迁移启示"）
# 五、AI 赋能芯片设计案例（含国产 EDA 进展/功耗建模/良率分析工具）
# 六、专利速览（重点 SMIC/FinFET 良率/低功耗/DUV 工艺专利）
# 七、学术速览（3-5 篇最重要论文，优先选取对 N+2/N+3 有参考价值的）
# 八、架构师 P0 行动项
# 九、本周关键词（变化列表）
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

**所有来源均使用 playwright-cli 访问。**

| # | 来源 | URL | 用途 |
|---|------|-----|------|
| 1 | **SemiEngineering** 🏆 | semiengineering.com | 工程进展/深度分析，首页 Top Stories + Week in Review |
| 2 | **SEMI大半导体产业网** | semi.org.cn/site/semi | 国产制程/EDA/设备（华大九天/Intel 18A/GloFo CPO） |
| 3 | **集微网** | laoyaoba.com | 国产 EDA/IP/封测/AI 芯片产业动态 |
| 4 | **芯思想** | spaces.eepw.com.cn | 先进封装/Chiplet IO/制程路线图 |
| 5 | **半导体行业观察** | zmdlife.com | 国产替代进展/存储/设备材料 |
| 6 | **中国半导体行业协会** | web.csia.net.cn | 产业政策/统计/标准 |
| 7 | SemiWiki | semiwiki.com | 论坛深度讨论（Intel 18A 良率/ASML High-NA） |
| 8 | Tom's Hardware | tomshardware.com | 制程/架构深度分析 |
| 9 | IEEE Spectrum | spectrum.ieee.org | 半导体新闻/技术趋势 |
| 10 | Cadence Community | community.cadence.com | EDA 工具更新 |
| 11 | Siemens Blog | blogs.sw.siemens.com | EDA/DFT 策略 |
| 12 | ARM Developer | developer.arm.com | ARM 协议规范/IP |
| 13 | RISC-V Blog | riscv.org/blog | RISC-V 生态 |
| 14 | Chiplet Marketplace | chipletmarketplace.com | Chiplet 生态 |
| 15 | Converge Digest | convergedigest.com | 互联/数据中心 |
| 16 | TrendForce | trendforce.com | 产能/价格数据 |
| 17 | SCMP | scmp.com/tech/tech-war | 中国芯片产业动态（英文） |
| 18 | DIGITIMES | digitimes.com | 亚洲供应链 |
| 19 | EE Times Asia | eetasia.com | 亚洲电子工程 |
| 20 | SEMICON China | semiconchina.org | 展会/产业信息 |
| 21 | arXiv | arxiv.org | 学术论文（WebFetch 可访问） |

**已排除：** Synopsys Blog（TIMEOUT/TIMED_OUT 不可达）、AnandTech（ERR_CONNECTION_CLOSED）、摩尔精英（用户排除）

---

## Playwright-CLI 标准操作手册

### 核心命令

```bash
# 打开并导航
npx --no-install playwright-cli -s=<name> open "https://url.com/"

# 提取链接列表（最常用）
npx --no-install playwright-cli -s=<name> --raw eval "JSON.stringify([...document.querySelectorAll('a')].filter(a=>a.href&&a.textContent.trim().length>10).slice(0,30).map(a=>({title:a.textContent.trim(),href:a.href})))"

# 提取正文段落
npx --no-install playwright-cli -s=<name> --raw eval "JSON.stringify([...document.querySelectorAll('p')].map(p=>p.textContent.trim()).filter(t=>t.length>40).slice(0,20))"

# 关闭会话
npx --no-install playwright-cli -s=<name> close

# 关闭所有会话
npx --no-install playwright-cli close-all
```

### 错误处理
1. **TIMEOUT/CONNECTION_CLOSED** → 重试 1 次，仍失败则标记为不可达
2. **两次重试后仍失败** → 跳过该来源，继续其他搜索
3. **无 WebFetch/WebSearch 降级** — 已证实不可靠