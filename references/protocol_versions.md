# 半导体协议规范版本基线

> **用途**: Agent B 搜索前读取此文件作为已知版本基线，搜索时只关注是否有新版本发布。
> **更新规则**: 每次发现有新版本或新修订时，更新此文件。

---

## Die-to-Die 互连

| 协议 | 当前已知最新版本 | 发布/更新时间 | 关键特性 | 验证状态 |
|------|---------------|-------------|---------|---------|
| **UCIe** | 3.0 | 2026.02 (正式发布) | 64 GT/s QDR, CHI over UCIe 映射完成, 0.75pJ/b, 连续流模式 | GUC/Cadence N3P ✅ / Alphawave SoIC F2F ✅ / Synopsys N2P 流片 ✅ |
| **BoW** | — | — | Bunch-of-Wires, 开源替代 | 关注中 |
| **UALink** | 1.0 | 2026 Q2 可下载 | UALink-128(PCIe 7.0 PHY)/UALink-200(Ethernet 224G), Scale-up GPU 互连 | 规范即将发布 |

## 一致性互连

| 协议 | 当前已知最新版本 | 发布/更新时间 | 关键特性 | 验证状态 |
|------|---------------|-------------|---------|---------|
| **AMBA CHI** | Issue H | — | snoop filter 层级化扩展, multi-die 一致性 | Synopsys VIP 支持 ✅ |
| **AMBA AXI** | Issue L | 2025.08 | Credit-Based Transport, ACT 压缩, PAS Signalling | 待公开投片验证 |
| **CXL** | **4.0** | **2026.05** | **128 GT/s, bundled ports, 增强 RAS, 跳过 3.1** | 评估版可下载 |
| **CCIX** | — | — | 缓存一致性加速器互连 | 关注度下降 |

## I/O 互连

| 协议 | 当前已知最新版本 | 发布/更新时间 | 关键特性 | 验证状态 |
|------|---------------|-------------|---------|---------|
| **PCIe** | **7.0** | **2025.06.11** | **128 GT/s PAM-4, 242 GB/s x16单向** | 规范已发布 ✅ |
| **PCIe** | 6.0 | 2025 | 64 GT/s (与 UCIe 3.0 对齐) | SerDes PHY 可用 |
| **PCIe** | 8.0 | 2028 (计划) | 开发中 | 路线图阶段 |

## 内存接口

| 协议 | 当前已知最新版本 | 发布/更新时间 | 关键特性 | 验证状态 |
|------|---------------|-------------|---------|---------|
| **JEDEC HBM** | HBM4 | 2025.04 定稿 | 2048-bit, 8 Gb/s, 2 TB/s/stack, 64GB max | Samsung 量产 ✅ / SK hynix 量产 ✅ |
| **JEDEC HBM** | HBM4E | 2026.05 出样 | 16 Gbps/pin, 4.0 TB/s, TSMC 逻辑基底 Die 可编程 | Samsung 5月出样 |
| **JEDEC HBM** | HBM4 逻辑基底 Die | 2026.04 TSMC 发布 | 定制化 HBM 接口可编程, 近存计算, SoW-X 64 HBM→4TB | TSMC 已发布 |
| **JEDEC LPDDR** | LPDDR6 | 2026 规划中 | AI 数据中心场景 | 路线图阶段 |
| **JEDEC GDDR** | GDDR7 | 2025 | AI 推理 GPU | 量产中 |

---

## 制程节点基线

| 节点 | 状态 | 良率 | PDK | 关键特性 |
|------|------|------|-----|---------|
| **TSMC N2** | **HVM 量产** ✅ | D0 优于 N3 同期 | 1.0 GA | NanoFlex GAA, 20+ tape-out, 70+ pipeline |
| **TSMC N2P** | 规划中 | — | 0.3 | N2 增强 |
| **TSMC N2U** | 规划中 (2028) | — | — | N2P 增量增强 |
| **TSMC A16** | **推迟至 2027** | — | — | SPR 背面供电, 首个 BSPD 节点 |
| **TSMC A14** | 规划中 (2028) | — | — | NanoFlex Pro |
| **TSMC A13** | 规划中 (2029) | — | — | A14 shrink, 面积-6%, 设计规则兼容 |
| **TSMC A12** | 规划中 (2029) | — | — | A14 增强 + SPR 背面供电 |
| **Intel 18A** | **HVM 量产** ✅ | 提升中 | 0.9-1.0 | RibbonFET GAA + PowerVia BSPD |
| **Intel 18A-P** | 2026.05 VLSI 发布 | 改善 | — | 性能+9%, 热导率+50%, 变异性降低 |
| **Samsung SF2** | 风险生产 | 良率瓶颈 | 0.7 | MBCFET GAA, Taylor 厂 5万片/月规划 |
| **SMIC N+1/N+2** | 量产 | — | — | 等效 7nm, DUV 多重曝光 |
| **SMIC N+3** | 研发中 | — | — | 等效 5nm, 目标先进节点 10万片/月 |

---

## 中国 Chiplet 标准

| 标准编号 | 发布/生效日期 | 范围 | 状态 |
|---------|------------|------|------|
| **GB/T 46280.1-5** | 2026.02/2026.03.01 | 2D/2.5D 封装 Chiplet 协议/数据链路/物理层 | ✅ 生效 |
| **T/CCIASC 0054-2026** | 2026.03.06 | AI 芯片卡间互连 | ✅ 生效 |
| **HiPi Alliance** | 191 成员 | Chiplet 标准组 | 活跃 |

---

## 国产存储基线

| 厂商 | 技术 | 层数/节点 | 产能 | 状态 |
|------|------|---------|------|------|
| **YMTC** | 3D NAND Xtacking 4.0 | **294 层** | 50万片/月国产晶圆 | 目标 2026 年 15% 市场份额 |
| **CXMT** | DDR5 | — | — | 量产, 良率~50%, 芯片面积大 40% |
| **CXMT** | HBM2 | — | — | 已量产(提前) |
| **CXMT** | HBM3 | — | — | 目标 2026 年底 |

---

## OCP / 国际标准

| 标准 | 发布/更新时间 | 组织 | 关键信息 | 状态 |
|------|------------|------|---------|------|
| **OCP FCSA** | 2026.04.09 | OCP (ARM 主导) | ISA-neutral chiplet 架构规范 1.0.0, 3 级 compliance | ✅ 已发布 |

---

## 专利追踪基线

> **用途**: Agent E 搜索后更新此表，记录已发现的关键专利族，用于去重和趋势追踪。
> **更新规则**: 每期报告生成后，将新发现的专利族追加到此表。

| 专利号 | 受让人 | 分类 | 关键技术点 | 首次发现 | 架构影响 |
|--------|--------|------|-----------|---------|---------|
| US 2026/0010493 | DreamBig Semi | 协议互连 | Chiplet Hub 多种隔离 D2D PHY | 2026-05-17 | 统一 Chiplet 互连基座 |
| US 2026/0101542 | IBM | 先进制程 | 3D 堆叠纳米片 FET 双应力介质 | 2026-05-17 | 2nm GAA 应力工程 |
| US 2025/0292354 | Intel | AI芯片 | Chiplet GPU 脉动阵列矩阵加速器 | 2026-05-17 | 分布式 Chiplet GPU Tensor Core |
| US 2025/0357263 | Diamond Foundry | 先进封装 | 单晶金刚石散热片 >2000W/m·K | 2026-05-17 | 500W+ AI 芯片热管理 |
| US 2024/0321997 | SMIC | 国产芯片 | GAA/FinFET 选择性 W CVD 栅极接触 | 2026-05-17 | 国产先进制程栅极接触工艺 |

---

## 更新历史

- 2026-05-19: AMEC-SMIC 设备国产化从试产到规模量产; CXMT 利润激增+筹备IPO; AMD 2nm订单转Samsung; TSMC拒绝High-NA EUV; Arteris FlexGen AI NoC发布; Synopsys AgentEngineer+ASO.ai(7条AI产品线); Rambus HBM4E Controller(>4TB/s)+PCIe 7.0 Controller/Switch; Rambus PCIe 7.0 Controller/Switch/Retimer IP; CoWoS >98%良率确认; CXL 4.0规范已可下载
- 2026-05-17: UCIe 3.0 正式发布时间修正为 2026.02; ARM CHI over UCIe 映射完成; Synopsys N2P UCIe 流片; HBM4 逻辑基底 Die (TSMC); HBM4E Samsung Q2 出样确认; 新增 UALink 1.0; Synopsys HBM4 IP 3nm 硅验证; SiFive P570 Gen 3 + $400M + NVIDIA; Synopsys.ai Copilots; 华虹被美国禁运(2026.05); SMIC 全球#3代工(5.2%); TSMC CoWoS 5.5x >98%良率; 专利基线首次填充 5 条
- 2026-05-11: **重大更新** — PCIe 7.0 (128 GT/s) 发布; CXL 4.0 (128 GT/s) 发布跳过 3.1; TSMC N2/Intel 18A HVM 确认; TSMC A16 推迟至 2027; 新增 A13/A12/N2U 路线图; YMTC 294 层; SMIC 先进节点目标; Intel 18A-P VLSI 数据; Alphawave SoIC UCIe 3D tape-out; Rambus HBM4 Controller IP
- 2026-04-27: 初始版本，建立版本基线
