## CSE 331 - Computer Organization Course

### Verilog Tutorial

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
~				NOT
*, /, % 		mult, div, mod
+, -  			add,sub
<<, >> 			shift
<<<, >>> 		arithmetic shift
<, <=, >, >= 	comparison
==, != 			equal, not equal
&, ~& 			AND, NAND
^, ~^ 			XOR, XNOR
|, ^|  			OR, NOR
?: 				ternary operator
```

**Lowest**
