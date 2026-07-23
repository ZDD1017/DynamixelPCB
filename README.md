# STM32 - Dynamixel PCB

This repository contains the KiCad schematic sources (placeholder), BOM and a detailed schematic reference document for a PCB that drives up to 12 Dynamixel AX-12A servos from a single-cell Li-ion battery (boost to 11.1V onboard), using an STM32F103 (LQFP48) bare die and SN74LVC1T45/SN74LVC2T45 as level-shifters.

Files in this commit:
- project/sch/stm32_dynamixel.kicad_sch  (placeholder schematic file)
- docs/STM32_Dynamixel_board_schematic.md (detailed schematic & notes, exportable to PDF)
- BOM/BOM.csv (component list with recommended parts)
- project/pcb/placeholder.txt (placeholder for PCB files)

How to get the PDF (fastest options):
1) Download the repository as ZIP from GitHub (Code -> Download ZIP), open docs/STM32_Dynamixel_board_schematic.md and convert to PDF locally using your preferred tool (e.g., pandoc or print-to-PDF).

   Example (using pandoc):
   pandoc docs/STM32_Dynamixel_board_schematic.md -o STM32_Dynamixel_board_schematic.pdf

2) Or, open the .md file on GitHub and use the browser's Print -> Save as PDF.

You can safely shut down your computer while I work on future updates; the files will be pushed to the repository and available anytime.

If you want the actual KiCad schematic and PCB files (not placeholders) exported as a PDF by me, I can add them in a follow-up commit in ~24-48 hours.
