# STM32 Dynamixel Board Schematic

本文件为可直接导出为 PDF 的详细原理图说明文档（参考用）。包含：
- 电源（单节锂电 3.7V 升压到 11.1V，使用 TPS61088）
- STM32F103 最小系统（裸片 LQFP48）
- Dynamixel AX-12A 总线收发（SN74LVC1T45 / 74LVC1G125 方案）
- 12 个舵机菊链接口与保护元件
- BOM（关键件）
- PCB 布局与测试建议

请将本文件导出为 PDF 以便离线查看。

---

页面 1：总体电源与输入（Battery / 输入保护 / 升压 TPS61088）

目标：把单节 Li-ion（典型 3.7V，范围 ~3.0–4.2V）升为 11.1V（用于 AX-12A 电源），峰值输出能力至少 2 A（设计裕量；舵机峰总 1.2 A）。

连接器：
- BAT+（J1-1）来自单节电池（注意极性）
- BAT-（J1-2）共地（GND）

反接与输入保护：
- 反向保护：建议使用肖特基（SS34）或理想二极管方案。输入并联 TVS（可选）。
- 输入滤波：Cin = 47 µF~100 µF + 0.1 µF 陶瓷并联。

升压芯片：TPS61088（TI，推荐）。
- L1：2.2 µH，Isat >= 8 A，低 DCR。
- Cin：47 µF（25 V MLCC）+ 0.1 µF 陶瓷。
- Cout：220 µF（25 V，固态/低 ESR）+ 10 µF MLCC 并联。
- 反馈电阻按 VOUT 计算（例：Rbot=10k，Rtop≈220k）。
- EN 引脚可由 MCU 控制或通过跳线拉高。

输出：V_MOTOR = 11.1 V（标注 max 12.6V 充满电状态）
- 在输出路上加入 PTC 或 2–3 A 可恢复保险丝，输出并联 TVS（SMBJ12A）以防浪涌。

---

页面 2：3.3V 与 STM32F103 最小系统

3.3V 电源：使用同步降压芯片（推荐：TPS62840 或等效），把 11.1V 降为 3.3V，为 MCU 与逻辑供电。

STM32F103 裸片（LQFP48）最小系统：
- VDD 接 3.3V，VSS 接 GND，每个 VDD 旁放 100 nF 陶瓷。
- NRST：10k 上拉 + 100 nF 至 GND（可选外部 reset supervisor）。
- 晶振：8 MHz + 两个 22 pF（或依据你选择的外部时钟）。
- BOOT0：默认 GND（10k 下拉，焊盘可选）。
- SWD：2x5 接针。

UART 引脚与 TX_EN：
- USART1_TX -> PA9
- USART1_RX -> PA10
- TX_EN（DIR） -> 例如 PA8（由固件控制）

---

页面 3：Dynamixel AX-12A 总线收发与连接（半双工）

总线拓扑：菊链串联（每舵机 3-pin 插座：V_MOTOR，GND，DATA）。

总线上拉：
- R_PULL = 4.7 kΩ 到 5V，上拉焊盘可选（默认不开）。
- 5V 来源：可从板上小型降压或外部提供（推荐做成可选焊点）。

收发电路（推荐方案）：
- TX 驱动：74LVC1G125 三态缓冲，OE = TX_EN，高时驱动总线，经 100 Ω 串联电阻到 DATA。
- RX 方向移位：SN74LVC1T45（或 SN74LVC2T45）用于 B->A（BUS->MCU_RX）方向移位，DIR 与 TX_EN 配合控制。

保护：R_SER = 100 Ω 在驱动端，DATA 上可放小 TVS（可选）。

舵机接口 J2..J13：
- Pin1 = V_MOTOR (11.1V)
- Pin2 = GND
- Pin3 = DATA（菊链连接）

---

页面 4：固件与时序（UART/DIR 控制）

发送流程：
1) TX_EN = 1（使能驱动）
2) 延时 10–50 µs
3) 发送数据（UART）
4) 等待 UART TC（Transmission Complete）标志
5) 延时 10–50 µs
6) TX_EN = 0（回到接收）

UART：推荐 1 Mbps 波特率（AX-12A 默认）。

---

页面 5：BOM（关键器件与替代）

| Ref | Description | Example Part | Footprint | Note |
|-----|-------------|--------------|----------|------|
| U1  | Boost IC    | TPS61088     | VQFN     | 升压到 11.1V |
| L1  | Inductor    | 2.2uH Isat>=8A | Power Ind | Coilcraft/Wurth |
| C_IN| Input cap   | 47uF 25V MLCC| 1210     | +0.1uF nearby |
| C_OUT| Output cap  | 220uF 25V    | 1210/1812| low ESR |
| U2  | Buck 3.3V   | TPS62840     | QFN     | 或其它降压芯片 |
| U3  | MCU         | STM32F103C8T6 | LQFP48 | 裸片放板上 |
| U4  | TX Buffer   | 74LVC1G125   | SOT23-5 | OE -> TX_EN |
| U5  | Level shifter | SN74LVC1T45 | SOT23-5 | 或 SN74LVC2T45 双通道 |
| Rpull| Pull-up    | 4.7k         | 0603    | 可焊跳线 |

（完整 CSV 已放在 BOM/BOM.csv）

---

页面 6：PCB 布局与散热注意（要点）

- SW 节点短宽线，电感靠近 IC。大地平面。输出端去耦靠近负载。TPS61088 热垫焊到大面积铜。
- 电源走线粗、短，GND 星形或短回路连接。
- DATA 线短，靠近驱动端放 R_SER。

---

页面 7：测试与验证步骤（建议）

1) 无负载下测试升压输出是否稳定到 11.1V±容差；
2) 给 MCU 上电并测试 SWD 连接；
3) 用示波器观测 TX_EN 与 DATA 时序；
4) 逐步挂载舵机测试至 12 台；注意热量与电流。

---

参考资料：TPS61088 datasheet, SN74LVC1T45 datasheet, Dynamixel AX-12A documentation (Robotis)

