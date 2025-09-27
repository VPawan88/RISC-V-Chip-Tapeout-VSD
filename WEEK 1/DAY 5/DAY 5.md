
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
