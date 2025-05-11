### **Day 50: Interface Basics in SystemVerilog**

---

### **Objective:**

Understand what **interfaces** are in SystemVerilog, how they simplify signal connections, and how to use them in modules and testbenches to improve code readability and reuse.

---

## ✅ 1. What is an Interface?

An **interface** in SystemVerilog is a container for signals (wires, regs) and optional logic (like tasks, functions, modports) that can be **shared** between design and testbench components.

It solves the problem of **bundling related signals** and passing them easily between modules.

---

## ✅ 2. Why Use Interfaces?

Without interfaces, connecting multiple signals between modules requires listing each one individually. Interfaces allow you to:

* Group signals logically
* Simplify module connections
* Avoid mismatches and errors
* Reuse the same interface across design and testbench

---

## ✅ 3. Basic Syntax of an Interface

```systemverilog
interface simple_bus;
    logic clk;
    logic rst;
    logic [7:0] data;
    logic valid;
endinterface
```

---

## ✅ 4. Connecting Interface to a Module

### **Using the Interface in a Module:**

```systemverilog
module dut (simple_bus sb);
    always @(posedge sb.clk) begin
        if (sb.rst)
            sb.data <= 0;
        else if (sb.valid)
            sb.data <= sb.data + 1;
    end
endmodule
```

---

## ✅ 5. Testbench Using the Interface

```systemverilog
module tb;

    // Instantiate the interface
    simple_bus sb();

    // Instantiate the DUT and connect the interface
    dut d1(sb);

    // Clock generation
    initial begin
        sb.clk = 0;
        forever #5 sb.clk = ~sb.clk;
    end

    // Stimulus
    initial begin
        sb.rst = 1; sb.valid = 0; #10;
        sb.rst = 0;

        repeat (5) begin
            sb.valid = 1;
            sb.data = $urandom_range(0, 255);
            #10;
        end

        sb.valid = 0;
        #20 $finish;
    end

endmodule
```

---

## ✅ 6. Modports (Advanced Use)

To control **directionality**, interfaces use `modports`. Example:

```systemverilog
interface simple_bus;
    logic clk, rst;
    logic [7:0] data;
    logic valid;

    modport master (input clk, rst, output data, valid);
    modport slave  (input clk, rst, data, valid);
endinterface
```

Then in the module:

```systemverilog
module master(simple_bus.master sb); // only accesses clk, rst (input) and data, valid (output)
```

---

## ✅ Summary

| Feature     | Description                                        |
| ----------- | -------------------------------------------------- |
| `interface` | Groups related signals                             |
| `modport`   | Defines signal directions for roles (master/slave) |
| Connection  | One interface can be passed to multiple modules    |
| Benefit     | Reduces signal clutter and improves readability    |

