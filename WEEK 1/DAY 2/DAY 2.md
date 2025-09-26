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



## 3️⃣ Glitch in Combinational Circuits & How to Overcome It

##  What is a Glitch?
A **glitch** is a short, unwanted pulse that appears at the output of a combinational logic circuit.  
It occurs because of **unequal propagation delays** along different logic paths when inputs change.

### Key Points
- Happens during input transitions.
- Can lead to **false triggering** of other circuits.
- Increases **dynamic power consumption** due to unnecessary switching.

---

##  Overcoming Glitches with Flip-Flops

###  Solution
Place a **flip-flop (FF)** at the output of the combinational circuit.

- The FF samples the output **only on the clock edge**.
- Any short spikes or hazards between clock edges are **ignored**.
- Only the **stable, final value** is passed to the next stage.

---

##  Design Tip
Follow **synchronous design practices**:
- Register all outputs with clocked flip-flops.
- This filters glitches and ensures **reliable, noise-free operation**.


## 4️⃣ Simulation and Synthesis of Flip Flops 

## 1 "D-Flip FLop with Asyncronous reset"

# SIMULATION
# 'dff_asyncres.v'
```verilog
module dff_asyncres ( input clk ,  input async_reset , input d , output reg q );
always @ (posedge clk , posedge async_reset)
begin
	if(async_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```

# 'tb_dff_asyncres.v'
```verilog

`timescale 1ns / 1ps
module tb_dff_asyncres;
	// Inputs
	reg clk, async_reset,   d;
	// Outputs
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_asyncres uut (
		.clk(clk),
		.async_reset(async_reset),
		.d(d),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_asyncres.vcd");
	$dumpvars(0,tb_dff_asyncres);
	// Initialize Inputs
	clk = 0;
	async_reset = 1;
	d = 0;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #23 d = ~d;
always #547 async_reset=~async_reset;
endmodule
```

🛠️ Yosys Commands
```
iverilog dff_asyncres.v tb_dff_asyncres.v
./a.out
gtkwave tb_dff_asyncres.vcd
```
<img width="1919" height="1013" alt="Screenshot 2025-09-26 051523" src="https://github.com/user-attachments/assets/a898938b-ba7e-4bd5-b743-e41b94c6c6f7" />

# SYNTHESIS

🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v
synth -top dff_asyncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="1057" height="160" alt="Screenshot 2025-09-26 060508" src="https://github.com/user-attachments/assets/641b60e7-d1b0-40ac-91d7-0160a9bce147" />




## 2 "D-Flip FLop with Asyncronous set"

# SIMULATION
# 'dff_async_set.v'
```verilog
module dff_async_set ( input clk ,  input async_set , input d , output reg q );
always @ (posedge clk , posedge async_set)
begin
	if(async_set)
		q <= 1'b1;
	else	
		q <= d;
end
endmodule
```

# 'tb_dff_async_set.v'
```verilog


`timescale 1ns / 1ps
module tb_dff_async_set;
	// Inputs
	reg clk, async_set,   d;
	// Outputs
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_async_set uut (
		.clk(clk),
		.async_set(async_set),
		.d(d),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_async_set.vcd");
	$dumpvars(0,tb_dff_async_set);
	// Initialize Inputs
	clk = 0;
	async_set = 1;
	d = 0;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #23 d = ~d;
always #547 async_set=~async_set;
endmodule
```

🛠️ Yosys Commands
```
iverilog dff_async_set.v tb_dff_async_set.v
./a.out
gtkwave tb_dff_async_set.vcd
```

<img width="1913" height="980" alt="Screenshot 2025-09-26 051811" src="https://github.com/user-attachments/assets/96621d0d-b59a-490b-ac5c-90891d510c93" />

# SYNTHESIS
🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_async_set.v
synth -top dff_async_set
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="966" height="173" alt="Screenshot 2025-09-26 060950" src="https://github.com/user-attachments/assets/fbd48555-3189-4b49-98d6-4a270d95242a" />





## 3 "D-Flip FLop with Syncronous set"

# SIMULATION
# 'dff_syncres.v'
```verilog
module dff_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk )
begin
	if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```

# 'tb_dff_syncres.v'
```verilog
`timescale 1ns / 1ps
module tb_dff_syncres;
	// Inputs
	reg clk,  sync_reset,  d;
	// Outputs
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_syncres uut (
		.clk(clk),
		.sync_reset(sync_reset),
		.d(d),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_syncres.vcd");
	$dumpvars(0,tb_dff_syncres);
	// Initialize Inputs
	clk = 0;
	sync_reset = 0;
	d = 0;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #23 d = ~d;
always #113 sync_reset = ~sync_reset;
endmodule
```

🛠️ Yosys Commands
```
iverilog dff_syncres.v tb_dff_syncres.v
./a.out
gtkwave tb_dff_syncres.vcd
```

<img width="1907" height="989" alt="Screenshot 2025-09-26 052437" src="https://github.com/user-attachments/assets/e824b33a-95f9-419e-bb2a-c8b8162f3d2a" />


# SYNTHESIS
🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_syncres.v
synth -top dff_syncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="961" height="179" alt="Screenshot 2025-09-26 061141" src="https://github.com/user-attachments/assets/3c0cb3d5-af21-4262-8769-a3cc1d52e3c0" />









