# Week 1 - Day 1

## 📝 Description

The first day of the India **RISC-V SoC Tapeout Program** focused on understanding the basics of Verilog design and open-source digital design tools. The sessions introduced key concepts and provided hands-on practice with open-source EDA tools for RTL design and synthesis.

### Topics Covered

1. **Introduction to Verilog RTL Design and Synthesis**

   * Overview of Register Transfer Level (RTL) design principles.
   * Basics of writing synthesizable Verilog code.

2. **Introduction to Open-Source Simulator Icarus Verilog (iverilog)**

   * Understanding the simulation flow.
   * Compiling and running Verilog testbenches using iverilog.

3. **Labs using Icarus Verilog and GTKWave**

   * Commands to compile and run Verilog code:

     ```bash
     iverilog -o output_file design.v testbench.v
     vvp output_file
     ```
   * Viewing waveforms with GTKWave:

     ```bash
     gtkwave dump.vcd
     ```

4. **Introduction to Yosys and Logic Synthesis**

   * Basics of logic synthesis and technology mapping.
   * Flow from RTL to gate-level netlist.

5. **Labs using Yosys and Sky130 PDKs**

   * Example Yosys commands:

     ```bash
     yosys
     read_verilog design.v
     synth -top <top_module>
     write_json design.json
     write_verilog design_synth.v
     ```
   * Integration with Sky130 PDKs for open-source synthesis.

---

This concludes **Day 1** with a strong foundation in Verilog RTL design and essential open-source EDA tools.

