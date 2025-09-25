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

This task explores **Hierarchical** and **Flat** synthesis using Yosys with the `sky130_fd_sc_hd__tt_025C_1v80.lib` standard cell library.

---

## 1️ Hierarchical Synthesis

In hierarchical synthesis, submodules are preserved in the final gate-level netlist.

### `multiple_modules.v`
```verilog
module sub_module2 (input a, input b, output y);
    assign y = a | b;
endmodule

module sub_module1 (input a, input b, output y);
    assign y = a & b;
endmodule

module multiple_modules (input a, input b, input c , output y);
    wire net1;
    sub_module1 u1(.a(a),.b(b),.y(net1));  // net1 = a & b
    sub_module2 u2(.a(net1),.b(c),.y(y));  // y = net1 | c  → y = (a & b) + c
endmodule
```
🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top multiple_modules
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show multiple_modules
```
<img width="600" height="129" alt="Screenshot 2025-09-25 054044" src="https://github.com/user-attachments/assets/75469d2a-73c3-49a6-b713-d077e46679ff" />

```
write_verilog -noattr multiple_modules_heir.v
!gvim multiple_modules_heir.v
```

### `multiple_module_heir.v`
```verilog 
module multiple_modules(a, b, c, y);
    input a;
    input b;
    input c;
    output y;
    wire net1;
    
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
    input a;
    input b;
    output y;
    wire _0_;
    wire _1_;
    wire _2_;
    
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
    input a;
    input b;
    output y;
    wire _0_;
    wire _1_;
    wire _2_;
    
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



## 2 Flat Synthesis

Flattening removes module boundaries so the tool directly optimizes all logic at the top level.

🛠️ Yosys Commands
```
flatten
write_verilog -noattr multiple_modules_flat.v
!gvim multiple_modules_flat.v
show multiple_modules
```
<img width="1106" height="150" alt="Screenshot 2025-09-25 061224" src="https://github.com/user-attachments/assets/a5577fb8-f12b-49ab-90ba-b1deeb12b296" />



### `multiple_modules_flat.v`
```verilog
module multiple_modules(a, b, c, y);
    input a;
    input b;
    input c;
    output y;
    
    wire net1;
    wire \u1._2_ ;
    wire \u1.a ;
    wire \u1.b ;
    wire \u1.y ;
    wire \u2._2_ ;
    wire \u2.a ;
    wire \u2.b ;
    wire \u2.y ;
    
    sky130_fd_sc_hd__and2_0 \u1._3_ (
        .A(\u1.b),
        .B(\u1.a),
        .X(\u1._2_)
    );
    
    sky130_fd_sc_hd__or2_0 \u2._3_ (
        .A(\u2.b),
        .B(\u2.a),
        .X(\u2._2_)
    );
    
    assign \u2.y = \u2._2_;
    assign \u2.a = net1;
    assign \u2.b = c;
    assign y = \u2.y;
    assign \u1.y = \u1._2_;
    assign \u1.a = a;
    assign \u1.b = b;
    assign net1 = \u1.y;
endmodule
```


## Sub module sysnthesis

You can synthesize a single submodule independently for reuse or analysis.

🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top sub_module1
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="597" height="116" alt="Screenshot 2025-09-25 061946" src="https://github.com/user-attachments/assets/8e78fedf-ffdf-4b7a-a09e-12aa5d75abdd" />








