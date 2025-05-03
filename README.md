# 4 KB-ROM-Memory-with-Read-and-Write-Operations
## AADHITHYA SV 
## 212223060001
## Aim
To design and simulate a 4KB ROM memory with read and write operations using Verilog HDL and verify the functionality through a testbench in the Vivado 2023.1 simulation environment.

## Apparatus Required
Vivado 2023.1 or equivalent Verilog simulation tool.
Computer system with a suitable operating system.
## Procedure
Launch Vivado 2023.1:

Open Vivado and create a new project.
Design the Verilog Code for ROM:

Write the Verilog code for a 4KB ROM memory with read and write capabilities.
Create the Testbench:

Write a testbench to simulate both the read and write operations, verifying that the data is correctly written to and read from the memory.
Add the Verilog Files:

Add the ROM Verilog module and the testbench file to the project.
Run Simulation:

Run the behavioral simulation in Vivado and check the memory's read and write operations.
Observe the Waveforms:

Analyze the waveform to verify that the memory read and write operations work as expected.
Save and Document Results:

Capture the waveform and include the simulation results in the final report.
Verilog Code for 4KB ROM Memory with Read and Write Operations
In this design, we will implement a 4KB ROM. Since ROM is typically read-only, we will simulate the behavior as if it's writable, but in actual hardware, ROM is typically pre-programmed.
## Program 
4KB = 4096 Bytes = 4096 x 8 bits
The address width for 4KB memory is 12 bits (2^12 = 4096).
~~~
module ram_4kb(input clk,rst,wr,
input [11:0]addr,
input [7:0]data_in,
output reg [7:0] data_out
    );
reg[7:0]mem[4095:0];
always @(posedge clk)
begin
if(rst)
data_out=8'b0;
else if (wr)
mem[addr]=data_in;
else
data_out=mem[addr];
end
endmodule

~~~


## Testbench for 4KB ROM Memory

~~~
`timescale 1ns / 1ps
module ram_4kb_tb;


reg clk;
reg write_enable;
reg [11:0] address;
reg [7:0] data_in;
reg reset;


wire [7:0] data_out;

ram_4kb uut (
    .clk(clk),
    .rst(reset),
    .wr(write_enable),
    .addr(address),
    .data_in(data_in),
    .data_out(data_out)
);

always #5 clk = ~clk;  

initial begin
    reset=1;
    #10 reset=0;
    clk = 0;
    write_enable = 0;
    address = 0;
    data_in = 0;

    
    #10 write_enable = 1; address = 12'd0; data_in = 8'hA5;  
    #10 write_enable = 1; address = 12'd1; data_in = 8'h5A;  
    #10 write_enable = 1; address = 12'd2; data_in = 8'hFF;  
    #10 write_enable = 1; address = 12'd3; data_in = 8'h00;  

    
    #10 write_enable = 0; address = 12'd0;
    #10 address = 12'd1;
    #10 address = 12'd2;
    #10 address = 12'd3;
end
endmodule
~~~
## Output
![WhatsApp Image 2025-05-03 at 13 57 12_39880914](https://github.com/user-attachments/assets/ef4b4265-9bf1-48b0-89d3-fa9cc1c24418)


## Conclusion
In this experiment, a 4KB ROM memory with read and write operations was designed and successfully simulated using Verilog HDL. The testbench verified both the write and read functionalities by simulating the memory operations and observing the output waveforms. The experiment demonstrates how to implement memory operations in Verilog, effectively modeling both the reading and writing processes for ROM.
