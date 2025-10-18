# RISC-V Talent Development Program  
### *(Samsung SSIR × VSD Collaboration)*  

**Author:** K Snehith  
**College:** Sahyadri College of Engineering & Management, Mangaluru  
**Email:** snehithgowda591@gmail.com  

---

## 🧭 Overview
A hands-on implementation of a complete **RISC-V workflow** — from native and cross-compilation to ISA-level debugging, instruction encoding, RTL simulation of an RV32I core, and a Verilog-based object-detection module on the **VSDSquadron Mini** board.

This repository demonstrates practical exposure to:
- RISC-V toolchain and Spike simulator  
- Assembly instruction encoding and verification  
- Functional simulation of a custom RV32I CPU  
- RTL design, waveform debugging, and board-level integration  

---

## 📁 Repository Structure
🧩 Task 1 — VM Setup & Host/RISC-V Builds

Goal: Validate native and cross-compiled builds using a parameterized C program computing Σ 1→n.
#include <stdio.h>
int main() {
    long long n, sum = 0;
    printf("Enter n: ");
    if (scanf("%lld", &n) != 1) return 1;
    for (long long i = 1; i <= n; ++i) sum += i;
    printf("Sum(1..%lld) = %lld\n", n, sum);
    return 0;
}
Task 2 — Spike Run, Debug & Disassembly

Goal: Run an interactive C program on Spike, step through execution, and map disassembly.

#include <stdio.h>
int main() {
    int n;
    printf("Enter an integer: ");
    scanf("%d", &n);
    for (int i = 1; i <= 10; ++i)
        printf("%d * %d = %d\n", n, i, n*i);
    return 0;
}


Compile & Run:

riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o mt.o multiplication_table.c
spike pk mt.o


Debug & Disassemble:

spike -d pk mt.o
riscv64-unknown-elf-objdump -d mt.o | less
🧮 Task 3 — RISC-V Instruction Encoding

Goal: Encode and verify I/S/J/U/R instruction formats from Task 2 disassembly.

Examples:

Type	Example	Key Fields
I-type	ADDI sp, sp, −32	opcode 0010011, funct3 000
S-type	SD ra, 24(sp)	opcode 0100111, funct3 011
J-type	JAL ra, 10448	opcode 1101111
R-type	ADD x1,x2,x3	opcode 0110011, funct3 000

Reference tables in Task 3/instruction_encoding.md.

💻 Task 4 — Functional RTL Simulation of RV32I Core

Goal: Simulate an RV32I processor, inspect signals in GTKWave.

iverilog -o iiitb_rv32i iiitb_rv32i.v iiitb_rv32i_tb.v
./iiitb_rv32i
gtkwave iiitb_rv32i.vcd


Observe PC, instruction bus, register file writes, ALU outputs, and branch signals.
Validated instructions:
ADD, SUB, AND, OR, XOR, SLT, ADDI, BEQ

🤖 Task 5 — Object Detection on VSDSquadron Mini

Goal: Implement and test an object-detection Verilog module with real hardware feedback.

module object_detect (
  input  wire clk,
  input  wire rst_n,
  input  wire sensor_in,
  output reg  detect
);
  reg s1, s2;
  reg [15:0] cnt;
  always @(posedge clk or negedge rst_n) begin
    if (!rst_n) begin
      s1<=0; s2<=0; cnt<=0; detect<=0;
    end else begin
      s1<=sensor_in;
      s2<=s1;
      if (s2) cnt <= (cnt<16'd1000)? cnt+1 : cnt;
      else    cnt <= 0;
      detect <= (cnt >= 16'd500);
    end
  end
endmodule


Simulation:

iverilog -o objdet_tb object_detect.v object_detect_tb.v
./objdet_tb
gtkwave object_detect.vcd


Hardware Validation:

Map detect to LED or UART output.

Verify response under variable sensor input patterns.

🚀 Quick Start Commands
# Host build
gcc Task\ 1/sum1ton.c -o sum1ton && ./sum1ton

# RISC-V build + Spike
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o mt.o "Task 2/multiplication_table.c"
spike pk mt.o

# Debug + Disassembly
spike -d pk mt.o
riscv64-unknown-elf-objdump -d mt.o | less

# RV32I Core Simulation
iverilog -o iiitb_rv32i Task\ 4/iiitb_rv32i.v Task\ 4/iiitb_rv32i_tb.v
./iiitb_rv32i
gtkwave iiitb_rv32i.vcd

# Object-Detection Simulation
iverilog -o objdet_tb Task\ 5/rtl/object_detect.v Task\ 5/rtl/object_detect_tb.v
./objdet_tb
gtkwave object_detect.vcd

🧠 Results & Learnings

End-to-end validation of RISC-V flow: native → cross-compile → Spike debug → objdump correlation.

Manual instruction encoding verified against compiled binaries.

Functional RTL simulation of RV32I confirming correct decode/execute stages.

Designed and verified a Verilog-based object-detection module on real hardware.

📜 License

Educational use under the RISC-V Talent Development Program.
Include appropriate third-party licenses if adding external RTL or libraries.


---

✅ **This version is recruiter-ready:**  
- Clean markdown layout  
- Accurate technical flow  
- Concise command sections for reproduction  
- Highlights all tasks and deliverables clearly  

You can paste this directly into your GitHub repository’s `README.md`.
