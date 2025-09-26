# Week 1  Day 3

# Combinational and Sequential Optimization

### 1️⃣ Indroduction to Optimization

## Combinational Logic Optimization
   -Squeezing the logic to get the most optimised design
    -Area and Power saving
   -Constant Propagation
    -Direct Optimization
   -Boolean Logic Optimization
    -Quine McKluskey
    -K MAP

## Sequential Optimization
## 1. Basic

   -Sequential Constant propagation
   
## 2. Advanced
   -State optimization
   
   -Retiming
   
   -Sequential Logic
   
   -Cloning


## 2️⃣ **Implementation of Combinational Optimization**

## 1️. "opt_check.v"
```verilog
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```
🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<img width="600" height="137" alt="Screenshot 2025-09-26 165746" src="https://github.com/user-attachments/assets/a54c525b-8831-499c-b4e1-047b91ff8f39" />



## 2. "opt_check2.v"
```verilog
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```
🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check2.v
synth -top opt_check2
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<img width="603" height="152" alt="Screenshot 2025-09-26 170003" src="https://github.com/user-attachments/assets/2658ecad-6ba6-4abb-a83d-10562b49990e" />

## 3. "opt_check3.v"
```verilog
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```
🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check3.v
synth -top opt_check3
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```

<img width="605" height="223" alt="Screenshot 2025-09-26 174400" src="https://github.com/user-attachments/assets/6126c0af-da35-4129-8eee-25c516d1a3a4" />


## 3️⃣ **Implementation of Sequential Optimization**

## 1️. "dff_const1.v"
```verilog
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end

endmodule
```
## "tb_dff_const1.v"
```verilog
`timescale 1ns / 1ps
module tb_dff_const1;
	// Inputs
	reg clk, reset   ;
	// Output
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_const1 uut (
		.clk(clk),
		.reset(reset),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_const1.vcd");
	$dumpvars(0,tb_dff_const1);
	// Initialize Inputs
	clk = 0;
	reset = 1;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #1547 reset=~reset;
endmodule
```

```
iverilog dff_const1.v tb_const1.v
./a.out
gtkwave tb_dff_const1.vcd
```
<img width="1838" height="907" alt="Screenshot 2025-09-26 182313" src="https://github.com/user-attachments/assets/5e49626b-b5e8-45ed-8409-eca26e38afa0" />



🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<img width="598" height="111" alt="Screenshot 2025-09-26 182724" src="https://github.com/user-attachments/assets/6ea7ac13-6289-4d4b-9624-cf02aae8dc0a" />



## 2. "dff_const2.v"
```verilog
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
```

## "tb_dff_const2.v"
```verilog
`timescale 1ns / 1ps
module tb_dff_const2;
	// Inputs
	reg clk, reset   ;
	// Output
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_const2 uut (
		.clk(clk),
		.reset(reset),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_const2.vcd");
	$dumpvars(0,tb_dff_const2);
	// Initialize Inputs
	clk = 0;
	reset = 1;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #1547 reset=~reset;
endmodule
```

```
iverilog dff_const2.v tb_const2.v
./a.out
gtkwave tb_dff_const2.vcd
```

<img width="1845" height="528" alt="Screenshot 2025-09-26 182447" src="https://github.com/user-attachments/assets/4d8fa3db-6a74-4c93-8484-a2982a757bd6" />



🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
synth -top dff_const2
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<img width="604" height="526" alt="Screenshot 2025-09-26 182944" src="https://github.com/user-attachments/assets/7b4de3d0-e97a-4a09-afd9-7c1625f645df" />


## 3. "dff_const3.v"
```verilog
module dff_const3(input clk, input reset, output reg q);
reg q1;
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule
```

## "tb_dff_const3.v"
```verilog
`timescale 1ns / 1ps
module tb_dff_const3;
	// Inputs
	reg clk, reset   ;
	// Output
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_const3 uut (
		.clk(clk),
		.reset(reset),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_const3.vcd");
	$dumpvars(0,tb_dff_const3);
	// Initialize Inputs
	clk = 0;
	reset = 1;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #1547 reset=~reset;
endmodule

```

```
iverilog dff_const3.v tb_const3.v
./a.out
gtkwave tb_dff_const3.vcd
```

<img width="1839" height="898" alt="Screenshot 2025-09-26 184252" src="https://github.com/user-attachments/assets/c2100057-a8d9-48df-bf7e-349a17d65360" />




🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<img width="1094" height="174" alt="Screenshot 2025-09-26 184448" src="https://github.com/user-attachments/assets/9fa1888e-c766-427c-a7be-f268e2bd94cd" />





  

