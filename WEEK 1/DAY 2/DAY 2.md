# Week 1  Day 2

### 1️⃣ Introduction to Timing Libraries (.lib)
##  **sky130_fd_sc_hd__tt_025C_1v80**  


A **timing library (.lib)** is a key component in modern digital design flows such as synthesis, place-and-route, and static timing analysis. It contains *characterization data*—delay, power, and functional information—for every standard cell (e.g., inverters, NAND gates, flip-flops) of a given technology.

This specific library—`sky130_fd_sc_hd__tt_025C_1v80`—belongs to the open-source **SkyWater 130 nm** process and provides the standard-cell definitions and timing models for a particular set of operating conditions.

---

### 🔑 Breaking Down the Name
| Segment | Meaning |
|--------|--------|
| **sky130_fd** | SkyWater 130 nm fully-depleted (FD) CMOS technology node |
| **sc** | Standard Cell library (collection of logic gates, flip-flops, etc.) |
| **hd** | High-Density variant (optimized for area efficiency) |
| **tt** | Typical–Typical process corner (average manufacturing conditions) |
| **025C** | Characterized at 25 °C operating temperature |
| **1v80** | Characterized at 1.80 V supply voltage |

---

### 📂 Key Elements Inside a .lib File
1. **Cell Definitions**  
   Each cell begins with the `cell` keyword and lists inputs, outputs, and Boolean functionality.  
   *Example:* `cell (AND2X1) { … }`

2. **PVT Corner**  
   Describes the **Process, Voltage, Temperature** conditions—here, *typical process*, *1.8 V*, *25 °C*.

3. **Timing Models**  
   Usually Non-Linear Delay Models (NLDM) with look-up tables to capture:  
   - Input transition (slew) times  
   - Output load capacitance

4. **Power Modeling**

The .lib also specifies power characteristics for every cell:
  - Dynamic Power
  - Internal switching power: energy consumed when internal nodes toggle.
  - Load-dependent power: based on output capacitance and transition activity.
  - Leakage Power
Static power consumption when the cell is not switching.

4. **Units**  
   Defines the measurement scales used throughout the library:  
   - Time: **ns**  
   - Voltage: **V**  
   - Capacitance: **pF**  
   - Power: **nW**

---

### 🧩 Why It Matters
Design tools such as **Yosys**, **OpenROAD**, and static timing analyzers use this library to:
- Map RTL logic to real physical cells
- Accurately compute timing paths and setup/hold margins
- Estimate power and area before tape-out

---

This refined explanation provides a clear, professional overview of the `sky130_fd_sc_hd__tt_025C_1v80.lib` file and its role in digital IC design.

## 2️⃣ **Hierarchical vs Flat Synthesis**

# 🧱 The Core Concept: Modularity

Modules and submodules are fundamental concepts in both software engineering and digital hardware design (HDL). They are critical for managing complexity, promoting code reuse, and enabling a hierarchical design approach.

Modularity is the design principle of breaking down a large, complex system into smaller, self-contained, and manageable units.

1. Module is the foundational building block of a system. 

    It is a major, self-contained unit that provides a specific, cohesive function.

    Represents a broad functionality, major component, or subsystem.

    Sits at a higher level; it may contain one or more submodules.

    Interacts with other modules and the top-level system via a well-defined interface (ports, APIs).

2. Submodule is a nested unit or a child of a main module. It handles a more specific or detailed aspect of the parent module's overall function.

   Represents a detailed function, internal component, or sub-function within the parent module.

   Sits at a lower level; it is instantiated or called by its parent module.

   Primarily interacts with its parent module and other submodules within the same parent.

# Modules and Submodules in Digital Hardware Design (HDL)

In Hardware Description Languages like Verilog or VHDL, a module is the primary container for describing a piece of hardware. This structure directly implements the principle of hierarchical design.

Module (Top-Level/Parent): In Verilog, a module block represents a major hardware component, like a CPU, a Peripheral Bus Interface, or a Memory Controller. It describes the component's external connections (ports) and its internal logic/structure.

Eg: Synthesis of multiple_modules
```
module sub_module2 (input a, input b, output y);
	assign y = a | b;
endmodule

module sub_module1 (input a, input b, output y);
	assign y = a&b;
endmodule


module multiple_modules (input a, input b, input c , output y);
	wire net1;
	sub_module1 u1(.a(a),.b(b),.y(net1));  //net1 = a&b
	sub_module2 u2(.a(net1),.b(c),.y(y));  //y = net1|c ,ie y = a&b + c;
endmodule
```

Submodule (Component Instantiation): A module that is defined separately and then instantiated (used as a component) inside a higher-level module. The higher-level module connects the submodule's ports to its own internal signals or external ports.

Synthesis of submodule 1
<img width="1920" height="909" alt="synthesis sub_module1" src="https://github.com/user-attachments/assets/899aea46-a396-4087-a5a6-f8b10f99b44d" />


# Hierarchical vs Flat Synthesis

Synthesis is the process of converting RTL (Register Transfer Level) code (like Verilog) into a gate-level netlist using standard cells from a library. The choice between hierarchical and flat synthesis impacts design compilation time and optimization quality.

1. Hierarchical Synthesis 
   
    Each major functional module is synthesized independently (out-of-context), and then the top-level is combined.

    Pros: Significantly faster runtimes and lower memory footprint, making it essential for large-scale SoC designs.

    Cons: Optimization is restricted within module boundaries. Timing closure is harder for paths that cross between modules because the tool relies on conservative

    Interface Logic Models (ILMs) or user-defined timing budgets/constraints.

   Use: The standard approach for large designs to manage complexity and runtime, using a timing budget/floorplan to meet top-level constraints.
```
Generated by Yosys 0.9 

module multiple_modules(a, b, c, y);
  input a;
  input b;
  input c;
  wire net1;
  output y;
  sub_module1 u1 (
    .a(a),
    .b(b),
    .y(net1)
  );
  sub_module2 u2 (
    .a(net1),
    .b(c),
    .y(y)
  );
endmodule

module sub_module1(a, b, y);
  wire _0_;
  wire _1_;
  wire _2_;
  input a;
  input b;
  output y;
  sky130_fd_sc_hd__and2_0 _3_ (
    .A(_1_),
    .B(_0_),
    .X(_2_)
  );
  assign _1_ = b;
  assign _0_ = a;
  assign y = _2_;
endmodule

module sub_module2(a, b, y);
  wire _0_;
  wire _1_;
  wire _2_;
  input a;
  input b;
  output y;
  sky130_fd_sc_hd__or2_0 _3_ (
    .A(_1_),
    .B(_0_),
    .X(_2_)
  );
  assign _1_ = b;
  assign _0_ = a;
  assign y = _2_;
endmodule
```
<img width="1920" height="909" alt="synthesis multiple_modules" src="https://github.com/user-attachments/assets/23c38bca-5a11-4f21-8b5a-8e6bc3915b79" />

2. Flat Synthesis
   
    The entire design (all modules) is synthesized together as a single, large netlist.

    Pros: Achieves the best overall timing optimization because the tool can optimize paths across module boundaries without constraints.

   Cons: Extremely long runtimes and high memory usage for very large designs.

   Use: Recommended for small to medium-sized designs or for the final optimization of critical, small blocks.

```
 Generated by Yosys 0.9 

(* top =  1  *)
(* src = "multiple_modules.v:10" *)
module multiple_modules(a, b, c, y);
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire _0_;
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire _1_;
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire _2_;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire _3_;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire _4_;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire _5_;
  (* src = "multiple_modules.v:10" *)
  input a;
  (* src = "multiple_modules.v:10" *)
  input b;
  (* src = "multiple_modules.v:10" *)
  input c;
  (* src = "multiple_modules.v:11" *)
  wire net1;
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire \u1.a ;
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire \u1.b ;
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire \u1.y ;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire \u2.a ;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire \u2.b ;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire \u2.y ;
  (* src = "multiple_modules.v:10" *)
  output y;
  sky130_fd_sc_hd__and2_0 _6_ (
    .A(_1_),
    .B(_0_),
    .X(_2_)
  );
  sky130_fd_sc_hd__or2_0 _7_ (
    .A(_4_),
    .B(_3_),
    .X(_5_)
  );
  assign \u1.a  = a;
  assign \u1.b  = b;
  assign net1 = \u1.y ;
  assign _1_ = \u1.b ;
  assign _0_ = \u1.a ;
  assign \u1.y  = _2_;
  assign \u2.a  = net1;
  assign \u2.b  = c;
  assign y = \u2.y ;
  assign _4_ = \u2.b ;
  assign _3_ = \u2.a ;
  assign \u2.y  = _5_;
endmodule
```
<img width="1920" height="909" alt="synthesis multiple_modules_flat" src="https://github.com/user-attachments/assets/34b110f4-5078-4072-8a58-fea882499ef0" />
