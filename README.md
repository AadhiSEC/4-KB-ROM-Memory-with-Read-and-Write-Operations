# 4 KB-ROM-RAM-Memory And FIFO with-Read-and-Write-Operations
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
## 4Kb RAM Program
```
module ram(
    input clk,
    input write_enable,
    input [11:0] address,    
    input [7:0] data_in,
    output reg [7:0] data_out
);
reg [7:0] ram_block[0:4095];  
always @(posedge clk) begin
    if (write_enable)
        ram_block[address] <= data_in;
    else
        data_out <= ram_block[address];
end
endmodule
```
## RAM Testbench
```
module ram_tb;

    reg clk;
    reg write_enable;
    reg [11:0] address;
    reg [7:0] data_in;
    wire [7:0] data_out;

    ram uut (
        .clk(clk),
        .write_enable(write_enable),
        .address(address),
        .data_in(data_in),
        .data_out(data_out)
    );

    initial clk = 0;
    always #5 clk = ~clk;

    initial begin
        write_enable = 0;
        address = 0;
        data_in = 0;

        #10;

        write_enable = 1;

        address = 12'd0; data_in = 8'hAA; #10;  
        address = 12'd100; data_in = 8'h55; #10;  
        address = 12'd4095; data_in = 8'hFF; #10;  

        write_enable = 0;

        address = 12'd0; #10;
        $display("Read from address 0: %h (expected AA)", data_out);

        address = 12'd100; #10;
        $display("Read from address 100: %h (expected 55)", data_out);

        address = 12'd4095; #10;
        $display("Read from address 4095: %h (expected FF)", data_out);

        address = 12'd500; #10;
        $display("Read from address 500 (unwritten): %h", data_out);

        $finish;
    end
endmodule
```
## OUTPUT
![WhatsApp Image 2025-05-26 at 19 49 36_896d5675](https://github.com/user-attachments/assets/cbf96586-4dd2-4be9-bc2e-193cd5d76217)
## FIFO Program
```
module fifo_4kb (
    input wire clk,
    input wire rst,
    input wire write_en,
    input wire read_en,
    input wire [7:0] data_in,
    output reg [7:0] data_out,
    output wire full,
    output wire empty
);

    // Parameters
    localparam DEPTH = 4096;
    localparam ADDR_WIDTH = 12;

    // Memory and pointers
    reg [7:0] mem [0:DEPTH-1];
    reg [ADDR_WIDTH-1:0] write_ptr = 0;
    reg [ADDR_WIDTH-1:0] read_ptr = 0;
    reg [ADDR_WIDTH:0] fifo_count = 0;  // one bit wider to count 0-4096

    // Status signals
    assign full = (fifo_count == DEPTH);
    assign empty = (fifo_count == 0);

    // Write operation
    always @(posedge clk) begin
        if (rst) begin
            write_ptr <= 0;
        end else if (write_en && !full) begin
            mem[write_ptr] <= data_in;
            write_ptr <= write_ptr + 1;
        end
    end

    // Read operation
    always @(posedge clk) begin
        if (rst) begin
            read_ptr <= 0;
            data_out <= 8'd0;
        end else if (read_en && !empty) begin
            data_out <= mem[read_ptr];
            read_ptr <= read_ptr + 1;
        end
    end

    // FIFO counter logic
    always @(posedge clk) begin
        if (rst) begin
            fifo_count <= 0;
        end else begin
            case ({write_en && !full, read_en && !empty})
                2'b10: fifo_count <= fifo_count + 1; // Write only
                2'b01: fifo_count <= fifo_count - 1; // Read only
                default: fifo_count <= fifo_count;   // No change or simultaneous read/write
            endcase
        end
    end

endmodule
```
## FIFO Testbench
```
`timescale 1ns / 1ps

module tb_fifo_4kb;

    // Testbench signals
    reg clk;
    reg rst;
    reg write_en;
    reg read_en;
    reg [7:0] data_in;
    wire [7:0] data_out;
    wire full;
    wire empty;

    // Instantiate the FIFO
    fifo_4kb uut (
        .clk(clk),
        .rst(rst),
        .write_en(write_en),
        .read_en(read_en),
        .data_in(data_in),
        .data_out(data_out),
        .full(full),
        .empty(empty)
    );

    // Clock generation: 100 MHz clock (10 ns period)
    always #5 clk = ~clk;

    // Task to write a single byte to FIFO
    task write_fifo;
        input [7:0] data;
        begin
            @(posedge clk);
            if (!full) begin
                write_en = 1;
                data_in = data;
            end
            @(posedge clk);
            write_en = 0;
        end
    endtask

    // Task to read a single byte from FIFO
    task read_fifo;
        begin
            @(posedge clk);
            if (!empty) begin
                read_en = 1;
            end
            @(posedge clk);
            read_en = 0;
        end
    endtask

    // Test sequence
    integer i;
    initial begin
        // Initialize signals
        clk = 0;
        rst = 1;
        write_en = 0;
        read_en = 0;
        data_in = 8'd0;

        // Hold reset for a few cycles
        @(posedge clk);
        @(posedge clk);
        rst = 0;

        // Write 3 values to FIFO
        $display("Writing values to FIFO...");
        write_fifo(8'hA1);
        write_fifo(8'hB2);
        write_fifo(8'hC3);

        // Read the values back
        $display("Reading values from FIFO...");
        read_fifo(); #1 $display("Data out = %h", data_out);
        read_fifo(); #1 $display("Data out = %h", data_out);
        read_fifo(); #1 $display("Data out = %h", data_out);

        // Check empty flag
        if (empty)
            $display("FIFO is empty as expected.");
        else
            $display("FIFO is NOT empty - ERROR!");

        // Try to read when FIFO is empty
        read_fifo(); #1 $display("Data out (should be unchanged or invalid) = %h", data_out);

        // Fill the FIFO completely
        $display("Filling FIFO to full...");
        for (i = 0; i < 4096; i = i + 1) begin
            write_fifo(i[7:0]); // Only low 8 bits are stored
        end

        if (full)
            $display("FIFO is full as expected.");
        else
            $display("FIFO is NOT full - ERROR!");

        // Done
        $display("Testbench finished.");
        $finish;
    end

endmodule
```
## OUTPUT
![image](https://github.com/user-attachments/assets/d60848ac-c25d-4b13-b703-b1ef0ac2599a)




## Conclusion
In this experiment, a 4KB ROM memory with read and write operations was designed and successfully simulated using Verilog HDL. The testbench verified both the write and read functionalities by simulating the memory operations and observing the output waveforms. The experiment demonstrates how to implement memory operations in Verilog, effectively modeling both the reading and writing processes for ROM.
