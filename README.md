# 4-bit-Ripple-Carry-Adder-using-Task-and-4-bit-Ripple-Counter-using-Function-with-Testbench
Aim:
To design and simulate a 4-bit Ripple Carry Adder using Verilog HDL with a task to implement the full adder functionality and verify its output using a testbench.
To design and simulate a 4-bit Ripple Counter using Verilog HDL with a function to calculate the next state and verify its functionality using a testbench.

Apparatus Required:
Computer with Vivado or any Verilog simulation software.
Verilog HDL compiler.

// Verilog Code // ------------------------------------------------------------- // 4-bit Ripple Carry Adder using Task // 4-bit Ripple Counter using Function // Testbench for both // -------------------------------------------------------------

// 4-bit Ripple Carry Adder module ripple_carry_adder_4bit( input [3:0] A, B, input Cin, output [3:0] Sum, output Cout ); reg [3:0] sum_reg; reg carry_out_reg;

assign Sum = sum_reg;
assign Cout = carry_out_reg;

task ripple_adder;
    input [3:0] a, b;
    input cin;
    output [3:0] sum;
    output cout;
    reg c0, c1, c2;
    begin
        sum[0] = a[0] ^ b[0] ^ cin;
        c0     = (a[0] & b[0]) | (cin & (a[0] ^ b[0]));

        sum[1] = a[1] ^ b[1] ^ c0;
        c1     = (a[1] & b[1]) | (c0 & (a[1] ^ b[1]));

        sum[2] = a[2] ^ b[2] ^ c1;
        c2     = (a[2] & b[2]) | (c1 & (a[2] ^ b[2]));

        sum[3] = a[3] ^ b[3] ^ c2;
        cout   = (a[3] & b[3]) | (c2 & (a[3] ^ b[3]));
    end
endtask

always @(*) begin
    ripple_adder(A, B, Cin, sum_reg, carry_out_reg);
end
endmodule

// -------------------------------------------------------------

// 4-bit Ripple Counter module ripple_counter_4bit( input clk, input reset, output reg [3:0] count );

function [3:0] increment;
    input [3:0] data;
    begin
        increment = data + 1;
    end
endfunction

always @(posedge clk or posedge reset) begin
    if (reset)
        count <= 4'b0000;
    else
        count <= increment(count);
end
endmodule

// -------------------------------------------------------------

// Testbench for both modules module tb_all; // For Adder reg [3:0] A, B; reg Cin; wire [3:0] Sum; wire Cout;

// For Counter
reg clk;
reg reset;
wire [3:0] count;

// Instantiate Adder
ripple_carry_adder_4bit uut_adder (
    .A(A), .B(B), .Cin(Cin),
    .Sum(Sum), .Cout(Cout)
);

// Instantiate Counter
ripple_counter_4bit uut_counter (
    .clk(clk),
    .reset(reset),
    .count(count)
);

// Clock generation
initial begin
    clk = 0;
    forever #5 clk = ~clk; // Clock with 10 time units period
end

// Stimulus
initial begin
    $monitor("Time=%0t | Adder: A=%b B=%b Cin=%b -> Sum=%b Cout=%b || Counter: Reset=%b Count=%b", 
              $time, A, B, Cin, Sum, Cout, reset, count);

    // Initial Reset for Counter
    reset = 1; #10;
    reset = 0; 

    // Test Adder
    A = 4'b0001; B = 4'b0010; Cin = 0; #10;
    A = 4'b0101; B = 4'b0011; Cin = 0; #10;
    A = 4'b1111; B = 4'b1111; Cin = 0; #10;
    A = 4'b1010; B = 4'b0101; Cin = 1; #10;

    // Toggle Counter Reset mid-way
    #30;
    reset = 1; #10;
    reset = 0;

    #50;
    $finish;
end
endmodule
## output: ![Exp 4](https://github.com/user-attachments/assets/935eae91-bb9d-4967-9eba-9cffbc166fe4)

Conclusion:
The 4-bit Ripple Carry Adder was successfully designed and implemented using Verilog HDL with the help of a task for the full adder logic. The testbench verified that the ripple carry adder correctly computes the 4-bit sum and carry-out for various input combinations. The simulation results matched the expected outputs.

The 4-bit Ripple Counter was successfully designed and implemented using Verilog HDL. A function was used to calculate the next state of the counter.

