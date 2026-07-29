WIP Progress Update 1

时间：2026-07-29T
状态：在 KiCad 中开始绘制原理图，已放置并连接以下模块的符号（待映射 footprints 并完成 ERC）：

1) 电源模块
   - BAT connector (J1) - 2 pin through-hole JST/VH connector
   - D1 - SS34 肖特基 二极管 (标注: 阳极->BAT+, 阴极->VIN)
   - CIN - 47uF/25V MLCC + 0.1uF 0603 陶瓷 去耦 (接 VIN-GND)
   - U1 - TPS61088 升压 芯片 (VQFN) - 引脚符号已放置: VIN, SW, FB, EN, BST, PG, GND
   - L1 - 2.2uH 高电流 电感 (SW -> L1 -> VOUT)
   - COUT - 220uF/25V 低 ESR + 10uF MLCC 并联 (VOUT-GND)
   - Rfb_top, Rfb_bot - 220k & 10k (VOUT->Rtop->FB->Rbot->GND)
   - PTC - 2A 可恢复保险丝 (VOUT -> PTC -> V_MOTOR)
   - TVS - SMBJ12A 并联保护 (V_MOTOR-GND)

2) 3.3V 电源模块
   - U2 - TPS62840 降压芯片 (引脚: VIN, EN, SW/OUT, FB, GND)
   - CIN2 / COUT2 - 去耦电容 (10uF + 0.1uF)
   - V3V3 net created

3) MCU 最小系统 (STM32F103C8T6 - LQFP48)
   - U3 symbol placed (VDD/VSS pins, NRST, BOOT0, OSC_IN/OSC_OUT, PA9, PA10, PA8, SWD pins)
   - NRST connected to R_reset(10k) to 3.3V and C_reset(100nF) to GND
   - BOOT0 connected to GND via R_boot0 10k (with solder jumper footprint planned)
   - OSC placed (8 MHz) with two 22pF caps to GND (symbols placed)
   - SWD header (J_SWD 2x5) connected to SWDIO, SWCLK, NRST, 3.3V, GND

4) UART / Direction control and level-shifting
   - U4 - 74LVC1G125 (single-channel tri-state buffer) symbol placed: A=input (PA9), OE=TX_EN (PA8), Y -> R_SER -> DATA_BUS
   - R_SER - 100R series resistor (near Y) symbol placed
   - U5 - SN74LVC1T45 (single-channel level-shifter) symbol placed: A->PA10 (RX), B->DATA_BUS, DIR->TX_EN (note: verify active polarity during layout)
   - R_PULL - 4.7k pull-up to 5V placed as solder jumper (JMP_PULL) so user can fit or leave open
   - DATA_BUS net created and connected to J_MOTOR1..J_MOTOR12 pin3 (pin named DATA)

5) Motor connectors
   - J_MOTOR1..J_MOTOR12 - 3PIN JST-SM through-hole placed; pin1 V_MOTOR, pin2 GND, pin3 DATA (in daisychained net configuration)
   - J_BAT - Battery connector mapped to VIN source

6) Misc
   - Multiple decoupling 0.1uF (near MCU VDD pins) placed
   - LEDs: POWER LED (with R_limit 2.2k) connected to V3V3
   - Test points: TP_VBAT, TP_VMOTOR, TP_3V3, TP_DATA, TP_TXEN created

下一步计划（接下来的 24–48 小时）
- 在 KiCad 中给每个 symbol 分配合适的 footprint（SOT-23-5, 0603, LQFP48, JST through-hole 等）
- 完成 ERC 并修正连线与未接引脚（注：特别检查 SN74LVC1T45 的 DIR 极性与 74LVC1G125 OE 极性配合）
- 导出高分辨率原理图 PDF
- 生成 BOM.csv（JLC-friendly）并把 footprints 映射在 BOM 中
- 提交 KiCad 源文件、PDF、BOM 到仓库（commit 链接在完成时提供）

注：如需我在推送前采用特定厂商料号（MPN）填 BOM，请回复“需要 MPN”。
