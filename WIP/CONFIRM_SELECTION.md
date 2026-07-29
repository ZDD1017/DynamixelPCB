确认：使用 SMD 降压 IC (方案 a)，不需要填写 MPN

时间：2026-07-29T

根据你的选择：
- 5V 电源将使用板载 SMD 降压 IC（占位器件：TPS563201 或 等效推荐，由我在原理图中按参考设计绘制）。
- BOM 中暂不填写制造商料号（MPN）。

后续行动计划：
1) 在 KiCad 中把降压 IC U6 与其外围器件（输入/输出电感、电容、使能/反馈电阻等）完整绘制并分配 footprint。
2) 把 V5 连接到 SN74LVC1T45 VCCB、R_PULL（4.7k）默认焊接，并完成其它模块的封装映射。
3) 运行 ERC 并修正连线错误。
4) 导出高分辨率原理图 PDF、生成 BOM.csv（不含 MPN）与 Pick-and-Place 文件。
5) 把 KiCad 源文件、PDF、BOM 推送到仓库（ZDD1017/DynamixelPCB main）。

预计时间：24–48 小时内完成并提交首版（首版含完整原理图 PDF 与 BOM）。

我现在开始在 KiCad 中绘制，完成后会把 commit 链接和下载说明发给你。