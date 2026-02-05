# 4-bit ALU Design in Verilog

This repository contains a simple **4-bit Arithmetic Logic Unit (ALU)** implemented in Verilog.  
The ALU performs basic arithmetic operations and generates common status flags.

---

## 📌 Module Overview

The ALU operates on two 4-bit inputs and supports four operations selected via a 2-bit control signal.

### Supported Operations

| Select | Operation |
|------:|-----------|
| `00`  | Addition (`a + b`) |
| `01`  | Subtraction (`a - b`) |
| `10`  | Multiplication (`a * b`) |
| `11`  | Division (`a / b`) |

---

## 📥 Inputs

| Signal  | Width | Description |
|--------|-------|-------------|
| `a`    | 4-bit | Operand A |
| `b`    | 4-bit | Operand B |
| `select` | 2-bit | Operation select |

---

## 📤 Outputs

| Signal     | Width | Description |
|------------|-------|-------------|
| `out`      | 4-bit | ALU result |
| `carry`    | 1-bit | Carry-out from operation |
| `zero`     | 1-bit | Set if result is zero |
| `sign`     | 1-bit | MSB of result (sign bit) |
| `parity`   | 1-bit | Even parity flag |
| `overflow` | 1-bit | Signed overflow flag |

---

## 🧠 Flag Logic Description

- **Zero flag (`zero`)**  
  Set when all output bits are zero.

- **Sign flag (`sign`)**  
  Reflects the most significant bit (MSB) of the result.

- **Parity flag (`parity`)**  
  Indicates even parity of the output.

- **Carry flag (`carry`)**  
  Captures the carry-out from arithmetic operations.

- **Overflow flag (`overflow`)**  
  Detects signed overflow for addition and subtraction:
  - Overflow occurs when two operands with the same sign produce a result with a different sign.

---

## 🧩 Verilog Implementation

```verilog
module alu(
           a         ,  //input
           b         ,  //input
           select    ,  //input
           zero      ,  //zero flag
           carry     ,  //carry flag
           sign      ,  //sign flag
           parity    ,  //parity flag
           overflow  ,  //overflow flag
           out          //output
           )         ;

input [3:0]a, b                                 ;
input [1:0] select                              ;
output reg [3:0]out                             ;
output reg zero, carry, sign, parity, overflow  ;

always @ (a or b)
   begin
      case (select)
         0 : {carry, out} = a + b   ;
         1 : {carry, out} = a - b   ;
         2 : {carry, out} = a * b   ;
         3 : {carry, out} = a / b   ;
      endcase

      zero     =  ~|out                                              ;
      sign     =  out[3]                                             ;
      parity   =  ~^out                                              ;
      overflow =  (a[3] & b[3] & ~out[3]) | (~a[3] & ~b[3] & out[3]) ;

   end
 
endmodule
