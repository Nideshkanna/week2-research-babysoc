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

Excellent 👍 — you’ve provided the **exact command history** from your terminal, which clearly reflects the **hands-on process** for Week 2: cloning the repo, setting up the environment, generating Verilog from TLV, simulating the BabySoC (pre- and post-synthesis), synthesizing using Yosys, and verifying results in GTKWave.

Here’s the **final detailed, structured README continuation (Part 2)** written in the same format and tone as your existing Part 1, and directly aligned with your command log.
You can paste this below the Part 1 section in your repo’s `README.md`.

---

# ⚙️ Part 2 – Practical Implementation & Functional Simulation

## 🎯 Objective

To **functionally model and verify** the BabySoC design by simulating its RISC-V CPU, PLL, and DAC modules using **Icarus Verilog**, **GTKWave**, and **Yosys**.
This step validates the logical operation of the SoC before moving to physical design.

---

## 🧩 Step 1 – Repository Setup

Start by cloning the official BabySoC repository and navigating into it:

```bash
git clone https://github.com/manili/VSDBabySoC.git
cd VSDBabySoC/
ls
```

You’ll see directories such as `src/`, `output/`, and a `Makefile`.

![01](./images/01.png)

---

## 🧱 Step 2 – Explore Source Modules

List the available modules:

```bash
ls src/
ls src/module/
```

These are the key RTL modules of the BabySoC:

| File           | Description                                     |
| -------------- | ----------------------------------------------- |
| `rvmyth.tlv`   | TL-Verilog RISC-V core (converted to Verilog).  |
| `avsddac.v`    | 10-bit DAC converting digital output to analog. |
| `avsdpll.v`    | PLL generating stable internal clock.           |
| `clk_gate.v`   | Clock-gating for low-power operation.           |
| `vsdbabysoc.v` | Top-level integration of CPU + PLL + DAC.       |
| `testbench.v`  | Stimulus generator and monitor for simulation.  |

![02](./images/02.png)

---

## 🧠 Step 3 – Set Up Python Environment & SandPiper SaaS

The RISC-V core (`rvmyth.tlv`) needs to be converted from TL-Verilog to standard Verilog.

```bash
python3 -m venv sp_env
source sp_env/bin/activate
pip install pyyaml click sandpiper-saas
sandpiper-saas -i ./src/module/rvmyth.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
```

![03](./images/03a.png)

This produces `rvmyth.v` inside the `src/module/` directory.

![03](./images/03b.png)

---

## ▶️ Step 4 – Pre-Synthesis Simulation

Create an output directory and compile all modules for **functional (behavioral) simulation**:

```bash
mkdir -p output/pre_synth_sim
iverilog -o output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM \
-I src/include -I src/module \
src/module/testbench.v
vvp output/pre_synth_sim/pre_synth_sim.out
```

![04](./images/04.png)

This generates `pre_synth_sim.vcd`.
View the waveform in GTKWave:

```bash
gtkwave pre_synth_sim.vcd
```

![05](./images/05.png)

---

## 📊 Step 5 – Analyze Pre-Synthesis Waveform

**Expected Observations:**

| Signal        | Function              | Behavior                        |
| ------------- | --------------------- | ------------------------------- |
| `reset`       | System initialization | Asserted high → low release     |
| `ENb_VCO`     | PLL enable            | Goes low to activate VCO        |
| `REF`         | Reference clock       | Stable periodic waveform        |
| `VCO_IN`      | PLL feedback          | Phase-locked to REF             |
| `OUT`         | DAC output            | Smooth periodic analog response |
| `VREFH/VREFL` | DAC references        | Constant 3.3 V / 0 V            |

✅ The DAC output (`OUT`) shows the expected waveform corresponding to the CPU digital output.

![06](./images/06.png)

---

## 🏗 Step 6 – Synthesis Using Yosys

To verify the RTL’s readiness for gate-level implementation:

```bash
yosys -s synth.ys
```

or, using the provided Makefile:

```bash
make synth
```

![07](./images/07.png)

This produces the synthesized netlist:

```
output/synthesized/vsdbabysoc.synth.v
```

![08](./images/08.png)

---

## 🧩 Step 7 – Post-Synthesis Simulation

Next, re-simulate the synthesized design to ensure functional equivalence:

```bash
make post_synth_sim

gtkwave output/post_synth_sim/post_synth_sim.vcd
```

![09](./images/09.png)

---

## 📈 Step 8 – Post-Synthesis Waveform Analysis

The post-synthesis waveform should closely match the pre-synthesis signals:

* **Reset** triggers clean initialization.
* **PLL** locks to reference frequency.
* **DAC OUT** reproduces the analog pattern.
* **No X/Z states** observed, indicating synthesis-correct behavior.

![10](./images/10.png)

✅ Functional equivalence confirmed between behavioral and synthesized designs.

---

## 📜 Step 9 – Results Summary

| Stage                     | Tool                     | Outcome                          |
| ------------------------- | ------------------------ | -------------------------------- |
| Functional Modelling      | Icarus Verilog + GTKWave | Verified SoC logic               |
| Synthesis                 | Yosys                    | Generated clean netlist          |
| Post-Synthesis Simulation | Icarus Verilog           | Confirmed equivalence            |
| Visualization             | GTKWave                  | Observed clocks, resets, outputs |

---

## 🧾 Key Insights & Learnings

* TL-Verilog → Verilog translation enables modular reuse.
* Functional simulation validates SoC logic before synthesis.
* Open-source tools (Icarus, Yosys, GTKWave) form a complete verification chain.
* Matching waveforms = design correctness ✔️

---

## 🧩 Conclusion

This completes the **functional modelling phase** of the BabySoC under **Week 2** of the RISC-V SoC Tapeout Journey.

We have:

* Successfully generated Verilog from TLV,
* Verified logical behavior in pre-synthesis simulation,
* Synthesized using Yosys, and
* Validated post-synthesis functional correctness.

🚀 Next we explore post-synthesis Gate-Level Simulation (GLS) and gain hands-on experience with Static Timing Analysis (STA) using OpenSTA in → **![Week 3:](https://github.com/Nideshkanna/week3_VSDBabySoC_GLS_STA)**. 

---
