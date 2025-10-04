# 🍼 Week 2 – BabySoC Fundamentals & Functional Modelling

## 🎯 Objective

To build a **solid understanding of SoC fundamentals** and practice **functional modelling** of the BabySoC using open-source simulation tools (**Icarus Verilog & GTKWave**).

This week bridges the gap between **conceptual theory** and **practical simulation**, ensuring we are equipped with both **system-level knowledge** and the **hands-on ability** to validate our designs.

---

## 📘 Part 1 – Theory (Conceptual Understanding)

### 🔹 What is a System-on-Chip (SoC)?

A **System-on-Chip (SoC)** is a highly integrated circuit that combines the major building blocks of a computer system onto a **single piece of silicon**. Instead of having separate chips for the processor, memory, and peripherals, an SoC places everything on one die.

Key points:

* **Integration:** CPU, memory, and I/O peripherals are merged onto one chip.
* **Efficiency:** High performance with lower power consumption compared to multi-chip solutions.
* **Scalability:** SoCs are tailored for specific use cases – from mobile devices to servers.

📱 **Real-life Examples:**

* Smartphones use SoCs like **Qualcomm Snapdragon** or **Apple A-series chips**.
* IoT devices use lightweight SoCs like **ESP32** or **ARM Cortex-M series**.
* Automotive systems use specialized SoCs for ADAS, vision, and sensor fusion.

👉 In short, **SoCs = compact systems** that provide powerful computing with minimal hardware overhead.

![01](./images/soc_overview.png)

---

### 🔹 Components of a Typical SoC

Every SoC is designed around a few **core components** that interact seamlessly:

1. **CPU (Processor):**

   * The **brain of the system**, executes instructions, runs programs, and manages the flow of data.
   * Can be simple (microcontroller-class cores like ARM Cortex-M0, RISC-V RV32I) or complex (multi-core ARM Cortex-A series, x86 cores).

2. **Memory (RAM, ROM, Cache):**

   * RAM stores temporary data for active programs.
   * ROM/Flash stores permanent firmware or boot code.
   * Cache accelerates CPU performance by reducing memory access time.

3. **Peripherals:**

   * Provide **interfaces** to the outside world (UART, SPI, I2C, USB, Ethernet, GPIO, ADC/DAC).
   * Essential for communication, sensing, and control in embedded systems.

4. **Interconnect/Bus:**

   * The “nervous system” of the SoC, linking CPU ↔ Memory ↔ Peripherals.
   * Examples: **AMBA (AXI/AHB/APB)**, **Wishbone**, **TileLink**.


📌 Together, these components form a **self-sufficient computing platform**.

![02](./images/soc_components2.png)

---

### 🔹 Why BabySoC?

Designing an industrial-grade SoC is **too complex** for beginners, so we use a **simplified SoC model** – the **BabySoC** – as a learning platform.

The BabySoC integrates **just three essential modules**:

* 🧠 **RISC-V CPU core (rvmyth)** – executes basic instructions.
* ⏱ **PLL (Phase-Locked Loop)** – generates stable clock signals.
* 🎵 **DAC (Digital-to-Analog Converter)** – converts CPU-generated digital signals into analog outputs.

👉 Benefits of BabySoC:

* Simple enough to **understand each module in depth**.
* Small scale means **faster simulation and debugging**.
* Directly connects to **real-world concepts**: data generation (CPU), timing (PLL), and output (DAC).

📌 Think of it as a **training SoC** – small but powerful enough to teach you the principles of **integration, synchronization, and verification**.

![03](./images/vsdbabysoc_block_diagram.png)

> Credits: VSD BabySoC

---

### 🔹 Role of Functional Modelling

Before diving into RTL coding and full physical design, **functional modelling** ensures the **system works logically**.

**What is functional modelling?**

* It is the process of **simulating the SoC at a higher level** to check correctness of behavior.
* Instead of transistors or layouts, we test **functionality and data flow**.

**Why is it important?**

* 🛠 **Early validation:** Catch logical errors before spending effort on RTL and synthesis.
* ⏱ **Clocking & Reset checks:** Ensures PLL provides proper timing and reset initializes the system.
* 🔄 **Data movement verification:** CPU → DAC transfer should be correct.
* 💸 **Cost-effective:** Fixing errors early avoids expensive re-design in later GDSII stages.

📊 **Design flow visualization:**

![04](./images/soc_components1.png)

**Tools used:**

* **Icarus Verilog (iverilog):** Open-source simulator for compiling and running Verilog models.
* **GTKWave:** Waveform viewer to inspect signals, timing, and data paths.

This step acts as a **bridge** between **theory** (block diagrams, concepts) and **hands-on RTL design**.

---

## 📄 Deliverable (This Week)

This week’s write-up (Part 1) covers:

* ✅ What an SoC is, with real-world relevance.
* ✅ Breakdown of SoC components and their role.
* ✅ BabySoC’s simplified structure and purpose.
* ✅ Importance of functional modelling in the design cycle.

➡️ **Next (Part 2):** Practical lab work –

* Simulating BabySoC functional model in **Icarus Verilog**.
* Viewing waveforms in **GTKWave**.
* Documenting test results and observations.

---

# ⚙️ Part 2 – Practical Lab Work (Functional Model Simulation)

## 🎯 Objective

To **simulate and verify** the functional behavior of the **BabySoC** before synthesis using **open-source EDA tools** — ensuring that the SoC components (RISC-V CPU, PLL, DAC) interact correctly.

---

## 🧩 Step 1 – Project Setup

1. Clone or open the **VSD BabySoC** repository inside your workspace:
    
    ```bash
    git clone https://github.com/manili/VSDBabySoC.git
    cd VSDBabySoC
    ```

2. Verify folder structure:
    
    ```bash
    ls
    ```
    
    You should see directories such as `src/`, `output/`, `images/`, and the `Makefile`.
    

![05](./images/05.png)

---

## 🧱 Step 2 – Understanding Module Files

Inside `src/module/`, each block of the SoC is defined:

| **File Name** | **Description** |
| --- | --- |
| `rvmyth.v` | RISC-V CPU core implementing simple instruction set. |
| `avsdpll.v` | Phase-Locked Loop generating stable internal clock. |
| `avsddac.v` | Digital-to-Analog Converter converting CPU output to analog signal. |
| `clk_gate.v` | Clock gating logic for power optimization. |
| `vsdbabysoc.v` | Top-level integration of CPU + PLL + DAC. |
| `testbench.v` | Functional testbench to apply stimulus and observe responses. |

📸 *(Insert screenshot of `src/module/` listing)*

---

## ▶️ Step 3 – Pre-Synthesis Simulation

Run the **functional simulation** to check correct behavior **before synthesis**.

```bash
iverilog -o output/pre_synth_sim src/module/*.v
vvp output/pre_synth_sim
gtkwave output/pre_synth_sim.vcd
```

The command sequence:

1. **Compiles** all Verilog modules.
2. **Runs** the testbench to generate `pre_synth_sim.vcd`.
3. **Opens** the VCD waveform in GTKWave.

📸 *(Add screenshots showing terminal execution and GTKWave waveform)*

---

## 🧠 Step 4 – Observing Functional Waveforms

In GTKWave, you’ll see:

- `reset` asserted briefly at start (system initialization)
- `ENb_VCO` enabling the PLL
- `REF` and `VCO_IN` establishing phase relation
- `OUT` produced by DAC
- `VREFH = 3.3 V`, `VREFL = 0 V` — stable reference levels

📊 **Analysis of signals:**

| **Signal** | **Meaning** | **Expected Behavior** |
| --- | --- | --- |
| `reset` | Initializes all internal registers | High → Low transition at start |
| `ENb_VCO` | PLL enable | High to activate VCO |
| `REF` | Input reference clock | Periodic pulse sequence |
| `VCO_IN` | Feedback to PLL | Follows REF with delay |
| `OUT` | DAC output waveform | Converts digital signal to analog form |
| `VREFH / VREFL` | Reference voltages | 3.3 V and 0 V constant |

📸 *(Insert screenshot of pre-synthesis waveform with labeled markers)*

---

## 🧩 Step 5 – Synthesis Preparation (Yosys)

Next, synthesize the design to verify RTL-to-gate-level compatibility.

1. Create a `synth.ys` script (if not present):
    
    ```
    read_verilog src/module/*.v
    synth -top vsdbabysoc
    write_verilog output/vsdbabysoc_synth.v
    write_json output/vsdbabysoc_synth.json
    ```
    
2. Run synthesis:
    
    ```bash
    yosys -s synth.ys
    ```
    

📸 *(Include screenshot showing successful Yosys run)*

---

## ⚙️ Step 6 – Post-Synthesis Simulation

To ensure synthesized netlist retains correct logic:

```bash
iverilog -o output/post_synth_sim src/module/testbench.v output/vsdbabysoc_synth.v
vvp output/post_synth_sim
gtkwave output/post_synth_sim/post_synth_sim.vcd
```

📸 *(Add terminal screenshot showing commands and GTKWave launch)*

---

## 📈 Step 7 – Waveform Analysis (Post-Synthesis)

From your uploaded waveform (`post_synth_sim.vcd`):

- **Reset pulse** correctly initializes the SoC.
- **ENb_VCO** enables PLL operation.
- **REF** and **VCO_IN** show a clear timing relationship – indicating PLL lock.
- **OUT** rises and falls corresponding to DAC activity.
- **VREFH/VREFL** stable at 3.3 V and 0 V.

✅ All signals show logical transitions with **no undefined (X/Z) states**, confirming **functional equivalence** between pre- and post-synthesis designs.

📸 *(Place your waveform screenshot here — the one you shared above)*

---

## 📜 Step 8 – Results & Discussion

| **Stage** | **Tool** | **Outcome** |
| --- | --- | --- |
| Pre-Synthesis | Icarus Verilog + GTKWave | Verified functional behavior |
| Synthesis | Yosys | Generated gate-level netlist |
| Post-Synthesis | Icarus Verilog + GTKWave | Confirmed functional equivalence |
| Visualization | GTKWave | Observed clock, reset, DAC outputs |

🔍 The BabySoC behaved as expected — **the PLL and DAC responded correctly to RISC-V core stimulus**, validating the integrated SoC architecture.

---

## 🧾 Key Learnings

- Functional modelling is a **critical verification checkpoint** before RTL synthesis.
- **Icarus Verilog** + **GTKWave** enable a full open-source verification flow.
- **Yosys synthesis** produces a clean gate-level netlist compatible with simulation.
- Consistent waveforms across both stages indicate **design correctness**.

---

## 📌 Conclusion

The **Week 2 functional modelling** stage successfully:

- Demonstrated the **logical operation** of BabySoC.
- Validated the interaction between **RISC-V CPU**, **PLL**, and **DAC**.
- Provided a strong foundation for subsequent stages of RTL design flow (synthesis, PnR, and tapeout).

> 🏁 Next Step → Week 3: Start exploring RTL synthesis in depth, timing reports, and gate-level analysis using open-source EDA tools.

---
## 📌 Key Takeaway

The **BabySoC project** provides a **stepping stone** into the world of semiconductor design. By first mastering **functional modelling**, we ensure that later stages of **RTL, synthesis, and layout** are built on a **robust and error-free foundation**.

---
