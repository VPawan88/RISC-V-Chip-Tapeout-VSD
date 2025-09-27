
# Day 5 – Priority Logic and Case Statements

# 📘 Introduction

Learned about priority logic in RTL and how conditional statements affect synthesis.

Understood how synthesis tools infer latches for incomplete assignments and missing branches.

⚡ Priority Logic Using IF-ELSE

IF…ELSE statements implement priority logic.
# Day 5 – Priority Logic and Case Statements

# 📘 Introduction

Learned about priority logic in RTL and how conditional statements affect synthesis.

Understood how synthesis tools infer latches for incomplete assignments and missing branches.


⚡ Priority Logic Using IF-ELSE

IF…ELSE statements implement priority logic.

Caution: missing else branches or incomplete assignments can infer unintended latches.

Proper coding ensures deterministic outputs and avoids synthesis warnings.


⏱️ Case Statements

CASE statements provide clear alternatives for mutually exclusive conditions.

Incomplete CASE statements can also infer latches, just like IF statements.

Solution: always include a default branch to cover all unspecified cases.

Partial assignments inside CASE statements need careful handling to prevent latches.


🔹 Comparison: IF…ELSEIF vs CASE

| Feature     | IF…ELSEIF                       | CASE                                                 |
| ----------- | ------------------------------- | ---------------------------------------------------- |
| Priority    | Highest priority executed first | All options considered equally, sequentially matched |
| Latch Risk  | High if branches missing        | High if default missing or partial assignments       |
| Use Case    | Priority logic                  | Multi-way selection with mutually exclusive options  |
| Readability | Good for small conditions       | Better for large, mutually exclusive conditions      |


🧹 Best Practices

Always provide else or default branches to avoid inferred latches.

Keep conditional logic simple and deterministic.

Use CASE statements for multi-way selection and IF-ELSE for priority logic.

Verify synthesis reports to ensure no unintended latches are inferred.


# 🧪 Priority Logic and Case Statements Expirements

During this lab, I implemented and simulated IF-ELSE and CASE-based priority logic, observing latch inference from incomplete assignments and how proper else/default ensures deterministic outputs.

🔹 Lab Modules

1) Incomplete if

RTL
```
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
end
endmodule
```

Testbench
```
`timescale 1ns / 1ps
module tb_incomp_if;
	//input
	reg i0,i1,i2;
	// Output
	wire y;

        // Instantiate the Unit Under Test (UUT)
	incomp_if uut (
		.i0(i0),
		.i1(i1),
		.i2(i2),
		.y(y)
	);

	initial begin
	$dumpfile("tb_incomp_if.vcd");
	$dumpvars(0,tb_incomp_if);
	// Initialize Inputs
	i0 = 1'b0;
	i1 = 1'b0;
	i2 = 1'b0;
	
	#3000 $finish;
	end

always #317 i0 = ~i0;
always #37 i1 = ~i1;
always #57 i2 = ~i2;

endmodule

```

⚙️Commands

```
iverilog incomp_if.v tb_incomp_if.v
./a.out
gtkwave tb_incomp_if.vcd

```
<img width="1920" height="909" alt="incomp_if gtkwave" src="https://github.com/user-attachments/assets/a311da8d-7050-4937-a279-7b11bcac5c22" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_if.v
synth -top incomp_if
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="incomp_if synthesis" src="https://github.com/user-attachments/assets/d200eff3-d8c8-47a4-8761-8d405da9bc76" />

2) Incomp_if2

RTL
```
module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
	else if (i2)
		y <= i3;

end
endmodule
```

Testbench
```
`timescale 1ns / 1ps
module tb_incomp_if2;
	//input
	reg i0,i1,i2,i3;
	// Output
	wire y;

        // Instantiate the Unit Under Test (UUT)
	incomp_if2 uut (
		.i0(i0),
		.i1(i1),
		.i2(i2),
		.i3(i3),
		.y(y)
	);

	initial begin
	$dumpfile("tb_incomp_if2.vcd");
	$dumpvars(0,tb_incomp_if2);
	// Initialize Inputs
	i0 = 1'b0;
	i1 = 1'b0;
	i2 = 1'b0;
	i3 = 1'b0;
	
	#3000 $finish;
	end

always #317 i0 = ~i0;
always #37 i1 = ~i1;
always #157 i2 = ~i2;
always #67 i3 = ~i3;

endmodule
```

⚙️Commands

```
iverilog incomp_if.v tb_incomp_if2.v
./a.out
gtkwave tb_incomp_if2.vcd

```
<img width="1920" height="909" alt="incomp_if2 gtk" src="https://github.com/user-attachments/assets/0fb17ccc-94f2-4643-be9a-5051a389fdfe" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_if2.v
synth -top incomp_if2
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="incompif2 synthesis" src="https://github.com/user-attachments/assets/097af9d7-8ae1-476f-94fe-b400c5c80f93" />

3) Incomp_case

RTL

```

module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
	endcase
end
endmodule

```

Testbench

```
`timescale 1ns / 1ps
module tb_incomp_case;
	//input
	reg i0,i1,i2;
	reg [1:0] sel;
	// Output
	wire y;
	//TB_SIGNALS
        reg clk,reset;	

        // Instantiate the Unit Under Test (UUT)
	incomp_case uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.i2(i2),
		.y(y)
	);

	initial begin
	$dumpfile("tb_incomp_case.vcd");
	$dumpvars(0,tb_incomp_case);
	// Initialize Inputs
	i0 = 1'b0;
	i1 = 1'b0;
	i2 = 1'b0;
	clk = 1'b0;
	reset = 1'b0; #1;
	reset = 1'b1; #10;
	reset = 1'b0; 

	
	#5000 $finish;
	end

always #317 i0 = ~i0;
always #600 clk = ~clk;
always #37 i1 = ~i1;
always #57 i2 = ~i2;


always @(posedge clk , posedge reset)
begin
if(reset)
	sel <= 2'b00;
else
	sel <= sel + 1;
end
endmodule

````
⚙️Commands

```
iverilog incomp_if.v tb_incomp_if2.v
./a.out
gtkwave tb_incomp_if2.vcd

```
<img width="1920" height="909" alt="incom_case gtk" src="https://github.com/user-attachments/assets/9d4876b5-a298-4f4f-adc2-0e4ee05aea75" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_case.v
synth -top incomp_case
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="incomp_case synthesis" src="https://github.com/user-attachments/assets/b885f7c8-cfc7-4352-a31e-716d7c92c25c" />

4) Complete case

RTL

```
module comp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
		default : y = i2;
	endcase
end
endmodule

```

Testbench

```
`timescale 1ns / 1ps
module tb_comp_case;
	//input
	reg i0,i1,i2;
	reg [1:0] sel;
	// Output
	wire y;
	//TB_SIGNALS
        reg clk,reset;	

        // Instantiate the Unit Under Test (UUT)
	comp_case uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.i2(i2),
		.y(y)
	);

	initial begin
	$dumpfile("tb_comp_case.vcd");
	$dumpvars(0,tb_comp_case);
	// Initialize Inputs
	i0 = 1'b0;
	i1 = 1'b0;
	i2 = 1'b0;
	clk = 1'b0;
	reset = 1'b0; #1;
	reset = 1'b1; #10;
	reset = 1'b0; 

	
	#5000 $finish;
	end

always #317 i0 = ~i0;
always #600 clk = ~clk;
always #37 i1 = ~i1;
always #57 i2 = ~i2;


always @(posedge clk , posedge reset)
begin
if(reset)
	sel <= 2'b00;
else
	sel <= sel + 1;
end
endmodule

```
⚙️Commands

```
iverilog comp_case.v tb_comp_case.v
./a.out
gtkwave tb_comp_case.vcd

```
<img width="1920" height="909" alt="comp_case gtk" src="https://github.com/user-attachments/assets/46e68f82-1484-4f78-987b-fd52071dd92d" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog comp_case.v
synth -top comp_case
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="comp_case synth" src="https://github.com/user-attachments/assets/82ac56f8-030f-4484-9f58-b9ac07c24704" />

5) Partial Case

RTL

```
module partial_case_assign (input i0 , input i1 , input i2 , input [1:0] sel, output reg y , output reg x);
always @ (*)
begin
	case(sel)
		2'b00 : begin
			y = i0;
			x = i2;
			end
		2'b01 : y = i1;
		default : begin
		           x = i1;
			   y = i2;
			  end
	endcase
end
endmodule

```

Testbench

```
`timescale 1ns / 1ps
module tb_partial_case_assign.v;
	//input
	reg i0,i1,i2,sel;
	// Output
	wire x,y;
	//TB_SIGNALS
        reg clk,reset;	

        // Instantiate the Unit Under Test (UUT)
	partial_case_assign.v uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.i2(i2),
		.i3(i3),
		.x(x),
		.y(y)
	);

	initial begin
	$dumpfile("tb_partial_case_assign.v.vcd");
	$dumpvars(0,tb_partial_case_assign.v);
	// Initialize Inputs
	i0 = 1'b0;
	i1 = 1'b0;
	i2 = 1'b0;
	clk = 1'b0;
	reset = 1'b0; #1;
	reset = 1'b1; #10;
	reset = 1'b0; 

	
	#5000 $finish;
	end

always #317 i0 = ~i0;
always #600 clk = ~clk;
always #37 i1 = ~i1;
always #57 i2 = ~i2;


always @(posedge clk , posedge reset)
begin
if(reset)
	sel <= 2'b00;
else
	sel <= sel + 1;
end
endmodule

```

⚙️Commands

```
iverilog partial_case_assign.v tb_partial_case_assign.v
./a.out
gtkwave tb_partial_case_assign.v

```

syntax error: at line 7 in tb_partial_case_assign.v

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog partial_case_assign.v
synth -top partial_case_assign.v
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="partial case synth" src="https://github.com/user-attachments/assets/bbf60f0e-2b74-4656-8e59-ed883fce1b7f" />

6) Bad case -incomplete overlapping

RTL
```
module bad_case (input i0 , input i1, input i2, input i3 , input [1:0] sel, output reg y);
always @(*)
begin
	case(sel)
		2'b00: y = i0;
		2'b01: y = i1;
		2'b10: y = i2;
		2'b1?: y = i3;
		//2'b11: y = i3;
	endcase
end

endmodule
```

Testbench
```


`timescale 1ns / 1ps
module tb_bad_case;
	// TB_SIGNALS
	reg clk, reset   ;
	//input
	reg i0,i1,i2,i3;
	reg [1:0] sel;
	// Output
	wire y;

        // Instantiate the Unit Under Test (UUT)
	bad_case uut (
		.i0(i0),
		.i1(i1),
		.i2(i2),
		.i3(i3),
		.sel(sel),
		.y(y)
	);

	initial begin
	$dumpfile("tb_bad_case.vcd");
	$dumpvars(0,tb_bad_case);
	// Initialize Inputs
	clk = 0;
	i0 = 1'b0;
	i1 = 1'b0;
	i2 = 1'b0;
	i3 = 1'b0 ;
	reset = 1'b0; #1;
	reset = 1'b1; #10;
	reset = 1'b0; 
	
	#3000 $finish;
	end

always #200 clk = ~clk;
always #17 i0 = ~i0;
always #37 i1 = ~i1;
always #57 i2 = ~i2;
always #97 i3 = ~i3;

always @ (posedge clk , posedge reset)
begin
	if(reset)
		sel <= 2'b00;
	else
		sel <= sel + 1;
end
endmodule

```

⚙️Commands

```
iverilog bad_case.v tb_bad_case.v
./a.out
gtkwave bad_case.v

```
<img width="1920" height="909" alt="bad_case gtk" src="https://github.com/user-attachments/assets/8d0be228-84ac-475d-978a-9d1b218714c5" />

GLC
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_case_net.v tb_bad_case.v
```
<img width="1920" height="909" alt="bad_case gtk" src="https://github.com/user-attachments/assets/8d0be228-84ac-475d-978a-9d1b218714c5" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_case.v
synth -top bad_case.v
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="bad_case synth" src="https://github.com/user-attachments/assets/2550dd07-242f-4fdb-af45-ada6db5771d0" />

# Looping Constructs and Generate Statements

During this lab, I explored looping constructs (`for`, `while`) and the `generate` block in Verilog to create repetitive hardware structures efficiently.

# 📘 Introduction

Learned how looping constructs (for) and generate blocks in Verilog help create repetitive hardware structures efficiently.

Understood that synthesis unrolls loops at compile time into parallel hardware, unlike software loops.

🔁 For Loop in RTL

Used inside always or initial blocks.

Helpful for repetitive assignments, testbenches, and describing similar logic patterns.

Example: initializing registers or building adders bit-by-bit.

⚡ Generate Statements

generate + for used to replicate hardware modules or blocks.

Enables parameterized design (e.g., N-bit adders, multiplexers, decoders).

Makes RTL modular, reusable, and scalable.

🧩 Key Differences

| Feature        | For Loop                                | For Generate                        |
| -------------- | --------------------------------------- | ----------------------------------- |
| Usage          | Procedural blocks (`always`, `initial`) | Structural blocks (`generate`)      |
| Purpose        | Repeated procedural logic               | Replicates hardware instances       |
| Synthesized As | Logic inside one block                  | Multiple module instances or blocks |

# 🧪 For loop and generate Expirements

1) Mux generate

RTL

```
module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
for(k = 0; k < 4; k=k+1) begin
	if(k == sel)
		y = i_int[k];
end
end
endmodule

```
Testbench

```
`timescale 1ns / 1ps
module tb_mux_generate;
	// Inputs
	reg i0,i1;
	reg i2,i3;
	reg [1:0] sel;
	
	//TB Signals
	reg clk,reset;

	// Outputs
	wire y;

        // Instantiate the Unit Under Test (UUT)
	mux_generate uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.i2(i2),
		.i3(i3),
		.y(y)
	);

	initial begin
	$dumpfile("tb_mux_generate.vcd");
	$dumpvars(0,tb_mux_generate);
	// Initialize Inputs
	i0 = 1'b0;
	i1 = 1'b0;
	i2 = 1'b0;
	i3 = 1'b0;
	clk = 1'b0;
	reset = 1'b0 ;  #1;
	reset = 1'b1 ;  #10;
	reset = 1'b0;

	#3000 $finish;
	end

always #17 i0 = ~i0;
always #37 i1 = ~i1;
always #88 i2 = ~i2;
always #155 i3 = ~i3;
always #300 clk = ~clk;

always @ (posedge clk , posedge reset)
begin
	if(reset)
		sel <= 2'b00;
	else
		sel <= sel + 1;
end
endmodule

```

⚙️Commands

```
iverilog mux_generate.v tb_mux_generate.v
./a.out
gtkwave tb_mux_generate.v

```
<img width="1920" height="909" alt="mux_generate gtk" src="https://github.com/user-attachments/assets/c20c9d29-8c0a-49c2-b5eb-938918d94e6f" />

Sythesized waveform

```
⚠️ Issue: Simulation could not produce waveform due to inferred latch (`$_DLATCH_P_`).  
Reason: Missing `else/default` in conditional statements caused latch inference.  
Solution: Ensure complete assignments by adding `else` (for if-else) or `default` (for case).  

```
Synthesis

````
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_case.v
synth -top bad_case.v
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

````
<img width="1920" height="909" alt="mux_gen synth" src="https://github.com/user-attachments/assets/7cdc19bb-1c1d-4c6b-915b-4326f5a7f506" />

2) Demux_case

RTL

```
module demux_case (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
	case(sel)
		3'b000 : y_int[0] = i;
		3'b001 : y_int[1] = i;
		3'b010 : y_int[2] = i;
		3'b011 : y_int[3] = i;
		3'b100 : y_int[4] = i;
		3'b101 : y_int[5] = i;
		3'b110 : y_int[6] = i;
		3'b111 : y_int[7] = i;
	endcase

end
endmodule
````

Testbench

```
`timescale 1ns / 1ps
module tb_demux_case;
	// Inputs
	reg i;
	reg [2:0] sel;
	
	//TB Signals
	reg clk,reset;

	// Outputs
	wire o7,o6,o5,o4,o3,o2,o1,o0;

        // Instantiate the Unit Under Test (UUT)
	demux_case uut (
		.sel(sel),
		.o0(o0),
		.o1(o1),
		.o2(o2),
		.o3(o3),
		.o4(o4),
		.o5(o5),
		.o6(o6),
		.o7(o7),
		.i(i)
	);

	initial begin
	$dumpfile("tb_demux_case.vcd");
	$dumpvars(0,tb_demux_case);
	// Initialize Inputs
	i = 1'b0;
	clk = 1'b0;
	reset = 1'b0 ;  #1;
	reset = 1'b1 ;  #10;
	reset = 1'b0;

	#3900 $finish;
	end

always #17 i = ~i;
always #300 clk = ~clk;

always @ (posedge clk , posedge reset)
begin
	if(reset)
		sel <= 3'b000;
	else
		sel <= sel + 1;
end
endmodule

```
⚙️Commands

```
iverilog demux_case.v tb_demux_case.v
./a.out
gtkwave tb_demux_case.v

```
<img width="1920" height="909" alt="demux_case gtk" src="https://github.com/user-attachments/assets/76e13e2c-303d-4349-97fb-24d29cc98a3d" />

Synthesized waveform
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_case_net.v tb_bad_case.v
```
<img width="1920" height="909" alt="demux_case synth waveform" src="https://github.com/user-attachments/assets/1502348f-11b3-47fe-9ebd-7eff954e3ae4" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog demux_case.v
synth -top demux_case.v
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="1920" height="909" alt="demux_case synthesis" src="https://github.com/user-attachments/assets/c26badd6-d875-4507-abf0-f4a00757afa8" />


3) Demux_generate

RTL

```
module demux_generate (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
for(k = 0; k < 8; k++) begin
	if(k == sel)
		y_int[k] = i;
end
end
endmodule
```

Testbench

````
`timescale 1ns / 1ps
module tb_demux_generate;
	// Inputs
	reg i;
	reg [2:0] sel;
	
	//TB Signals
	reg clk,reset;

	// Outputs
	wire o7,o6,o5,o4,o3,o2,o1,o0;

        // Instantiate the Unit Under Test (UUT)
	demux_generate uut (
		.sel(sel),
		.o0(o0),
		.o1(o1),
		.o2(o2),
		.o3(o3),
		.o4(o4),
		.o5(o5),
		.o6(o6),
		.o7(o7),
		.i(i)
	);

	initial begin
	$dumpfile("tb_demux_generate.vcd");
	$dumpvars(0,tb_demux_generate);
	// Initialize Inputs
	i = 1'b0;
	clk = 1'b0;
	reset = 1'b0 ;  #1;
	reset = 1'b1 ;  #10;
	reset = 1'b0;

	#3900 $finish;
	end

always #17 i = ~i;
always #300 clk = ~clk;

always @ (posedge clk , posedge reset)
begin
	if(reset)
		sel <= 3'b000;
	else
		sel <= sel + 1;
end



endmodule
````

⚙️Commands

```
iverilog mux_generate.v tb_mux_generate.v
./a.out
gtkwave tb_mux_generate.v

```
<img width="1920" height="909" alt="demux_gen gtkwave" src="https://github.com/user-attachments/assets/c1c91962-4781-4bb2-b96c-9dc8f2cca977" />

Synthesized waveform
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_case_net.v tb_bad_case.v
```
<img width="1920" height="909" alt="Demux_generate sythn waveform" src="https://github.com/user-attachments/assets/07174720-3c09-4f7c-94b9-bb98acb2da98" />

Synthesis
```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog demux_generate.v
synth -top demux_generate.v
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="1920" height="909" alt="demux gen synth" src="https://github.com/user-attachments/assets/bd5037f6-1ce7-4cec-a8fe-4368f62746fc" />

4) RCA

RTL
```
module rca (input [7:0] num1 , input [7:0] num2 , output [8:0] sum);
wire [7:0] int_sum;
wire [7:0]int_co;

genvar i;
generate
	for (i = 1 ; i < 8; i=i+1) begin
		fa u_fa_1 (.a(num1[i]),.b(num2[i]),.c(int_co[i-1]),.co(int_co[i]),.sum(int_sum[i]));
	end

endgenerate
fa u_fa_0 (.a(num1[0]),.b(num2[0]),.c(1'b0),.co(int_co[0]),.sum(int_sum[0]));


assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```

full_adder
```
module fa (input a , input b , input c, output co , output sum);
	assign {co,sum}  = a + b + c ;
endmodule
```

Testbench
```
`timescale 1ns / 1ps
module tb_rca;
	// TB_SIGNALS
	reg clk, reset   ;
	reg [7:0] num1 ;
	reg [7:0] num2;
	wire [8:0] sum_out;
	// Instantiate the Unit Under Test (UUT)
	rca uut (
		.num1(num1),
		.num2(num2),
		.sum(sum_out)
	);

	initial begin
	$dumpfile("tb_rca.vcd");
	$dumpvars(0,tb_rca);
	// Initialize Inputs
	clk = 0;
	reset = 0;
	reset = 1; #1;
	reset = 0; #10;
	#30000 $finish;
	end

always #10 clk = ~clk;


always @ (clk,reset)
begin
	if(reset)
	begin
		num1 <= 8'b0;
		num2 <= 8'b0;
	end
	else
	begin
		num1 = num1 + 1;
		if(num1[3:0] == 4'b1111)
			num2 = num2+1;	
	end

end
endmodule
```

After calling function fa.v
```
module rca (input [7:0] num1 , input [7:0] num2 , output [8:0] sum);
wire [7:0] int_sum;
wire [7:0]int_co;

genvar i;
generate
	for (i = 1 ; i < 8; i=i+1) begin
		fa u_fa_1 (.a(num1[i]),.b(num2[i]),.c(int_co[i-1]),.co(int_co[i]),.sum(int_sum[i]));
	end

endgenerate
fa u_fa_0 (.a(num1[0]),.b(num2[0]),.c(1'b0),.co(int_co[0]),.sum(int_sum[0]));


assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```

⚙️Commands

```
iverilog rca.v fa.v tb_rca.v
./a.out
gtkwave tb_rca.v

```
<img width="1920" height="909" alt="rca gtkwave" src="https://github.com/user-attachments/assets/b9b571c5-9a4b-4a71-ab03-1e5c4407c82b" />

Synthesized waveform

```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v rca.v tb_rca.v
```
<img width="1920" height="909" alt="rca syth wavform" src="https://github.com/user-attachments/assets/81fdde1b-8163-4642-8457-095d61341395" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog demux_generate.v
synth -top demux_generate.v
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```

✅ Key Learning from Lab

Loops in RTL are compile-time unrolled, not runtime like software.

Generate statements simplify scalable designs and reduce coding effort.

Proper use of for and generate avoids redundancy and improves readability.

Verified counter, mux, and adder designs using loop and generate constructs.
Caution: missing else branches or incomplete assignments can infer unintended latches.

Proper coding ensures deterministic outputs and avoids synthesis warnings.

⏱️ Case Statements

CASE statements provide clear alternatives for mutually exclusive conditions.

Incomplete CASE statements can also infer latches, just like IF statements.

Solution: always include a default branch to cover all unspecified cases.

Partial assignments inside CASE statements need careful handling to prevent latches.

🔹 Comparison: IF…ELSEIF vs CASE

| Feature     | IF…ELSEIF                       | CASE                                                 |
| ----------- | ------------------------------- | ---------------------------------------------------- |
| Priority    | Highest priority executed first | All options considered equally, sequentially matched |
| Latch Risk  | High if branches missing        | High if default missing or partial assignments       |
| Use Case    | Priority logic                  | Multi-way selection with mutually exclusive options  |
| Readability | Good for small conditions       | Better for large, mutually exclusive conditions      |

🧹 Best Practices

Always provide else or default branches to avoid inferred latches.

Keep conditional logic simple and deterministic.

Use CASE statements for multi-way selection and IF-ELSE for priority logic.

Verify synthesis reports to ensure no unintended latches are inferred.

# 🧪 Priority Logic and Case Statements Expirements

During this lab, I implemented and simulated IF-ELSE and CASE-based priority logic, observing latch inference from incomplete assignments and how proper else/default ensures deterministic outputs.

🔹 Lab Modules

1) Incomplete if

RTL
```
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
end
endmodule
```

Testbench
```
`timescale 1ns / 1ps
module tb_incomp_if;
	//input
	reg i0,i1,i2;
	// Output
	wire y;

        // Instantiate the Unit Under Test (UUT)
	incomp_if uut (
		.i0(i0),
		.i1(i1),
		.i2(i2),
		.y(y)
	);

	initial begin
	$dumpfile("tb_incomp_if.vcd");
	$dumpvars(0,tb_incomp_if);
	// Initialize Inputs
	i0 = 1'b0;
	i1 = 1'b0;
	i2 = 1'b0;
	
	#3000 $finish;
	end

always #317 i0 = ~i0;
always #37 i1 = ~i1;
always #57 i2 = ~i2;

endmodule

```

⚙️Commands

```
iverilog incomp_if.v tb_incomp_if.v
./a.out
gtkwave tb_incomp_if.vcd

```
<img width="1920" height="909" alt="incomp_if gtkwave" src="https://github.com/user-attachments/assets/a311da8d-7050-4937-a279-7b11bcac5c22" />

Synthesis

```
yosys

```

2) Incomp_if2

RTL
```
module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
	else if (i2)
		y <= i3;

end
endmodule
```

Testbench
```
`timescale 1ns / 1ps
module tb_incomp_if2;
	//input
	reg i0,i1,i2,i3;
	// Output
	wire y;

        // Instantiate the Unit Under Test (UUT)
	incomp_if2 uut (
		.i0(i0),
		.i1(i1),
		.i2(i2),
		.i3(i3),
		.y(y)
	);

	initial begin
	$dumpfile("tb_incomp_if2.vcd");
	$dumpvars(0,tb_incomp_if2);
	// Initialize Inputs
	i0 = 1'b0;
	i1 = 1'b0;
	i2 = 1'b0;
	i3 = 1'b0;
	
	#3000 $finish;
	end

always #317 i0 = ~i0;
always #37 i1 = ~i1;
always #157 i2 = ~i2;
always #67 i3 = ~i3;

endmodule
```

⚙️Commands

```

```

3) Incomp_case

RTL

```

module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
	endcase
end
endmodule
```

Testbench

```
`timescale 1ns / 1ps
module tb_incomp_case;
	//input
	reg i0,i1,i2;
	reg [1:0] sel;
	// Output
	wire y;
	//TB_SIGNALS
        reg clk,reset;	

        // Instantiate the Unit Under Test (UUT)
	incomp_case uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.i2(i2),
		.y(y)
	);

	initial begin
	$dumpfile("tb_incomp_case.vcd");
	$dumpvars(0,tb_incomp_case);
	// Initialize Inputs
	i0 = 1'b0;
	i1 = 1'b0;
	i2 = 1'b0;
	clk = 1'b0;
	reset = 1'b0; #1;
	reset = 1'b1; #10;
	reset = 1'b0; 

	
	#5000 $finish;
	end

always #317 i0 = ~i0;
always #600 clk = ~clk;
always #37 i1 = ~i1;
always #57 i2 = ~i2;


always @(posedge clk , posedge reset)
begin
if(reset)
	sel <= 2'b00;
else
	sel <= sel + 1;
end
endmodule

````
⚙️Commands

```

```
