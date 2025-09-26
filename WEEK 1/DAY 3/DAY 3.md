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

   - Cloning


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




  

