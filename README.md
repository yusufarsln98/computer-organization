## CSE 331 - Computer Organization Course

### Verilog Tutorial

**Some Notes**

1. Do not create a project inside the root folder.
2. Starting a project:
   - Create a root repo like `Verilog-Projects`.
   - Create a project folder. (e.g. `Project-1`)
   - By using New Project wizard start to create a project.
     - Give a project name, then next and next...
     - Find the EDA Tool Settings and set ModelSim Altera as simulation tool.
   - Create a Verilog HDL file. (Not other types!)
   - Save it inside a folder like Modules. (For good coding practice)
   - From `Project Navigator` navigation type from `Hierarchy` to `Files`. (To show files like VS Code folder structure)
   - To set a module as `top level`, right click the name of the file and set is as `top level entity`.
   - Do display the circuit:
     `Compile Design (on the left) > Analysis & Synthesis > Netlist Viewer > RTL Viewer`
   - To set ModelSim Altera Path:
     - `Tools > Options > EDA Tool Options`
       Then, set ModelSim Altera field to:
       `/home/user/intelFPGA_lite/20.1/modelsim_ase/linuxaloem/`
   - To run simulation:
     `Tools > Run Simulatioin Tool > RTL Simulation`
   - Adding a test bench:
     `Assignment > Settings > Simulation > Compile Test Bench -> Test Benches > New`
     Then name the test bench file and add the file as a new test bench
     `NOT:` The module you are testing in the test bench should be setted as `Top Level Entity`.

---

**Defining a Module**

```verilog
module example(a, b, c, y);
    input a;
    input b;
    input c;
    output y;

    /*
        Your Circuit Description in Here
    */

endmodule
```

`input` and `output` types can be specified inline:

```verilog
module example(
    input a,
    input b,
    input c,
    output y
    );

    /*
        Your Circuit Description in Here
    */

endmodule
```

Both codes are identical.

---

**Busses**
Multi bit busses shown as:
`[range_start : range_end]` where, both start and end bits are included.

```verilog
input [31:0] a; // a[31], a[30] .. a[0]
output [15:8] b1; // b1[15], b1[14] .. b1[8]
output [7:0] b2; // b2[7], b2[6] .. b1[0]
input clk; // single signal
```

---

**Basic Syntax**

- Comments are like C programming.
- Verilog is case sensetive.
- Names cannot start with a number.

---

**Good Practice**

- Use consistent naming style. (camelCase, snake_case)
- Use MSB to LSB ordering for **busses**. (little-endian)
  - Try using `a[31:0]` and not `a[0:31]`
- Define one module per file.
- Use a file name that equals module name.
  - i.e use `TryThis` defined in a file `TryThis.v`

---

**Main Styles of HDL**

There are 2 main styles in `HDL`.

- Structural Design
  - Describes how modules are interconnected
  - Each module contains other modules. (instances)
  - ... and interconnections between these modules
  - Describes hierarchy
- Behavioral
  - The module body contains functional description of the circuit
  - Contains logical and mathematical operators

Practical circuits, uses both.

---

**Strucural HDL Example**

Small Module:

```verilog
module small (A, B, Y);
    input A;
    input B;
    output Y;

    // description of small

endmodule
```

Top Module (Uses Small Modules):

```verilog
module top (A, SEL, C, Y);
    input A, SEL, C;
    output Y;
    wire n1;

// alternative
small i_first ( A, SEL, n1 );

/* Shorter instantiation, pin order very important */

// any pin order, safer choice
small i_second ( .B(C), .Y(Y), .A(n1) );

endmodule
```

---

**Behavioral HDL: Defining Functionality Example**

This definition is more declarative.
To set `not` just use `~` or `&` for `and` etc.

```verilog
module example (a, b, c, У);
    input a;
    input b;
    input c;
    output y;

    // here comes the circuit description

    assign y = ~a & ~b & ~c |
                a & ~b & ~c |
                a & ~b & c;

endmodule
```

---

**Bitwise Operators Example**

```verilog
module gates(
    input [3:0] a, b,
    output [3:0] y1, y2, у3, у4, у5);

    /* Five different two-input logic
       gates acting on 4 bit busses */

assign y1 = a & b; // AND
assign y2 = a | b; // OR
assign y3 = a ^ b; // XOR
assign y4 = ~(a & b); // NAND
assign y5 = ~(a | b); // NOR

endmodule
```

---

**Reduction Operators**

```verilog
module and8(input 17:0] a,
            output У);

  assign y = &a;
    // &a is much easier to write than
    // assign y = a[7] & a[6] & a[5] & a[4] &
    // a[3] & a[2] & a[1] & a[0];

endmodule
```

**Conditional Assignment**

```verilog

module mux2(input [3:0] d0, d1,
            input S,
            output [3:0] y);

  assign y = S ? d1 : d0;
    // if (S) then y = d1 else y = d0;

endmodule
```

`?:` is also called a ternary operator as it operates on three inputs: `• S • d1 • d0`.

More Conditional Assigments...

```verilog
module mux4(input [3:0] d0, d1, d2, d3,
            input [1:0] s,
            output [3:0] y);

assign y = (s == 2'b11) ? d3 :
           (s == 2'b10) ? d2 :
           (s == 2'b01) ? d1 : d0;

// if (s = "11") then y= d3
// else if (s = "10") then y= d2
// else if (s = "01") then y= d1
// else y = d0

endmodule
```

---

**How to Express numbers?**

`N'Bxx`

example: `8'b0000_0001`

- (N) Number of bits
  - Expresses how many bits will be used to store the value
- (B) Base
  - Can be b (binary), h (hexadecimal), d (decimal), o (octal)
- (xx) Number
  - The value expressed in base, apart from numbers it can also have X and Z as values.
  - Underscore \_ can be used to improve readability

---

**Precedence of operations in Verilog**

**Highest**

```
~                NOT
*, /, %         mult, div, mod
+, -              add,sub
<<, >>             shift
<<<, >>>         arithmetic shift
<, <=, >, >=     comparison
==, !=             equal, not equal
&, ~&             AND, NAND
^, ~^             XOR, XNOR
|, ^|              OR, NOR
?:                 ternary operator
```

**Lowest**

---

**A Complete 4-Bit Full Adder Project**

file: MyAnd.v

```verilog
module MyAnd(output r, input a, b);

  //  assign r = a & b;
  and and1(r, a, b);

endmodule
```

file: MyOr.v

```verilog
module MyOr(output r, input a, b);

  //  assign r = a | b;
  or or1(r, a, b);

endmodule
```

file: MyXor.v

```verilog
// It is a XOR component that created with AND modules and NOT operator.
module MyXor(output r, input a, b);
  MyAnd myAnd1(.a(a), .b(b), .r(MyAndResult1));
  MyAnd myAnd2(.a(a), .b(~MyAndResult1), .r(MyAndResult2));
  MyAnd myAnd3(.a(~MyAndResult1), .b(b), .r(MyAndResult3));
  MyAnd myAnd4(.a(~MyAndResult2), .b(~MyAndResult3), .r(MyAndResultFinal));

  //  assign r = ~MyAndResultFinal;
  not not1(r, MyAndResultFinal);

endmodule
```

file: MyXorTestBench.v

```verilog
module MyXorTestBench();
  reg a;
  reg b;
  wire result;

  MyXor myXor1(.a(a), .b(b), .r(result));

  initial begin
    #10 a = 0; b = 0;
    #10 $display("%d XOR %d = %d", a, b, result);
    #10 a = 1; b = 0;
    #10 $display("%d XOR %d = %d", a, b, result);
    #10 a = 0; b = 1;
    #10 $display("%d XOR %d = %d", a, b, result);
    #10 a = 1; b = 1;
    #10 $display("%d XOR %d = %d", a, b, result);
  end

endmodule
```

file: HalfAdder.v (not used in 4-Bit Full Adder)

```verilog
// Half Adder Module
/*
  sum = a XOR b
  c_out = a AND b
*/

module HalfAdder(output sum, c_out, input a, b);

  MyXor myXor1(sum, a, b);
  MyAnd myAnd1(c_out, a, b);

endmodule
```

file: HalfAdderTestBench.v

```verilog
module HalfAdderTestBench();

  reg a;
  reg b;
  wire sum;
  wire c_out;

  HalfAdder halfAdder(sum, c_out, a, b);

  initial begin
    #0 a = 0; b = 0;
    #10 $display("time: %0t -> %d + %d = %d ; c_out = %d", $time, a, b, sum, c_out);
    #10 a = 1; b = 0;
    #10 $display("time: %0t -> %d + %d = %d ; c_out = %d", $time, a, b, sum, c_out);
    #10 a = 0; b = 1;
    #10 $display("time: %0t -> %d + %d = %d ; c_out = %d", $time, a, b, sum, c_out);
    #10 a = 1; b = 1;
    #10 $display("time: %0t -> %d + %d = %d ; c_out = %d", $time, a, b, sum, c_out);
  end

endmodule
```

file: FullAdder.v

```verilog
// Full Adder Module
/*
  aXorB = a XOR b
  sum = aXorB XOR c_in

  c_inAndAXorB = c_in AND aXorB
  aAndB = a AND b
  c_out = c_inAndAXorB OR aAndB
*/

module FullAdder(output sum, c_out, input a, b, c_in);

  MyXor myXor1(aXorB, a, b);
  MyXor myXor2(sum, aXorB, c_in);

  MyAnd myAnd1(c_inAndAXorB, c_in, aXorB);
  MyAnd myAnd2(aAndB, a, b);
  MyOr myOr1(c_out, c_inAndAXorB, aAndB);

endmodule
```

file: FullAdderTestBench.v

```verilog
module FullAdderTestBench();

  reg a;
  reg b;
  reg c_in;
  wire sum;
  wire c_out;

  FullAdder fullAdder(sum, c_out, a, b, c_in);

  initial begin
    #0 a = 0; b = 0; c_in = 0;
    #10 $display("time: %0t -> %d + %d + %d = %d ; c_out = %d", $time, a, b, c_in, sum, c_out);
    #10 a = 1; b = 0; c_in = 1;
    #10 $display("time: %0t -> %d + %d + %d = %d ; c_out = %d", $time, a, b, c_in, sum, c_out);
    #10 a = 0; b = 1; c_in = 0;
    #10 $display("time: %0t -> %d + %d + %d = %d ; c_out = %d", $time, a, b, c_in, sum, c_out);
    #10 a = 1; b = 1; c_in = 1;
    #10 $display("time: %0t -> %d + %d + %d = %d ; c_out = %d", $time, a, b, c_in, sum, c_out);
  end

endmodule
```

file: FullAdder4Bit.v

```verilog
// 4 Bit Full Adder File
module FullAdder4Bit(output[3:0] sum,
              output c_out,
              input[3:0] a,
              input[3:0] b,
              input c_in);


// a  =  a3 a2 a1 a0
// b  =  b3 b2 b1 b0
// +________________
// sum = s0 s1 s2 s3

  FullAdder full_addder1(sum[0], c_out_0, a[0], b[0], c_in);
  FullAdder full_addder2(sum[1], c_out_1, a[1], b[1], c_out_0);
  FullAdder full_addder3(sum[2], c_out_2, a[2], b[2], c_out_1);
  FullAdder full_addder4(sum[3], c_out, a[3], b[3], c_out_2);

endmodule
```

file: FullAdder4BitTestBench.v

```verilog
// Full Adder 4 Bit Test Bench
module FullAdder4BitTestBench();
  reg[3:0] a;
  reg[3:0] b;
  reg c_in;
  wire[3:0] sum;
  wire c_out;

  FullAdder4Bit fullAdder4Bit(sum, c_out, a, b, c_in);

  initial begin
    #0 a = 4'b0000; b = 4'b0000; c_in = 0;
    #10 $display("time: %0t -> %d + %d + %d = %d ; c_out = %d", $time, a, b, c_in, sum, c_out);
    #10 a = 4'b1100; b = 4'b0011; c_in = 1;
    #10 $display("time: %0t -> %d + %d + %d = %d ; c_out = %d", $time, a, b, c_in, sum, c_out);
    #10 a = 4'b0101; b = 4'b0110; c_in = 1; // a = 5; b = 6
    #10 $display("time: %0t -> %d + %d + %d = %d ; c_out = %d", $time, a, b, c_in, sum, c_out);
    #10 a = 4'b1111; b = 4'b1111; c_in = 1;
    #10 $display("time: %0t -> %d + %d + %d = %d ; c_out = %d", $time, a, b, c_in, sum, c_out);
  end

endmodule
```
