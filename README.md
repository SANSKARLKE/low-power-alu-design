# Post-Implementation Power Optimization of an Operand-Gated ALU using FPGA Fabric

## 📌 Project Overview
High-activity workloads in parallel computing blocks represent a primary driver of dynamic power dissipation in modern digital design due to redundant transistor switching. This project addresses the challenge of parasitic power waste in deep-bus arithmetic architectures by designing and validating a **32-bit hierarchical Arithmetic Logic Unit (ALU)** optimized through **input-isolation operand gating**. 

While a standard ALU concurrently ripples data across all internal units, this architecture strategically clamps data paths to idle functional blocks based on immediate opcode decoding, forcing local switching activity factors to zero ($\alpha \rightarrow 0$). 

### Key Achievements
* **Dynamic Power Reduction:** Achieved a notable **17.65% reduction** in physical fabric dynamic power under high stress workloads compared to the un-gated baseline.
* **Physical Signoff:** Verified using a high-fidelity, simulation-driven Switching Activity Interchange Format (SAIF) back-annotation pipeline mapping exact parasitic wire capacitances on an AMD Xilinx Artix-7 FPGA die.

---

## 🛠 Prerequisites & Environment
* **Design Software:** AMD Xilinx Vivado Design Suite (Recommended version: 2020.1 or newer).
* **Target Device:** Artix-7 FPGA Fabric (`xc7a100tcsg324-1`).
* **Hardware Language:** VHDL / RTL Description.

---

## 🚀 Execution & Replication Steps

### 1. Download and Extract the Workspace
1. Clone this repository or download the repository ZIP file from GitHub.
2. Extract the ZIP archive to a clean directory on your local machine (e.g., `C:/Vivado_Projects/Gated_ALU/`).

### 2. Open the Project in Vivado
1. Launch the **Vivado Design Suite**.
2. Click **Open Project** on the welcome screen.
3. Browse to your extracted directory, select the `.xpr` project file, and click **OK**.

### 3. RTL Synthesis & Physical Implementation
1. In the *Flow Navigator* panel on the left, click **Run Synthesis**. Wait for completion to generate the logical netlist.
2. Once synthesis completes, select **Run Implementation**. 

---

## 📊 Simulation and Dynamic Power Signoff Pipeline

To extract accurate physical dynamic power metrics, follow this automated SAIF profiling script flow to inject high-entropy switching data back into the placed-and-routed netlist.

### Step 4: Run Stress Simulation & Generate SAIF Matrix
Open the **Tcl Console** at the bottom of the Vivado interface and run the following automated commands to run the maximum-toggling stress testbench:

```tcl
set proj_dir [get_property DIRECTORY [current_project]]
open_saif "$proj_dir/switching_activity.saif"
log_saif [get_objects -r /tb_top_alu/UUT/*]
run 6460 ns
close_saif
```

### Step 5: Read SAIF Data & Export Power Signoff Report
With the simulation switching profile generated, map it directly against the physical wire lengths and parasitic capacitances of the implemented layout to generate your signoff metrics:

```tcl
create_clock -period 20.000 -name clk [get_ports clk]
set proj_dir [get_property DIRECTORY [current_project]]
read_saif "$proj_dir/switching_activity.saif"
report_power -name structural_power_signoff_report
```

## 📈 Verifying Timing and Power Performance

### Checking the Power Reports
* Expand the **Power** tab under the *Implemented Design* section in the Flow Navigator.
* Compare the dynamic power numbers between the standard baseline run and the operand-gated run to confirm the power reduction.

### Checking the Timing Summaries
1. Under *Implemented Design*, click **Report Timing Summary**.
2. Ensure that under **Setup**, the **Worst Negative Slack (WNS)** displays a safe, positive value, validating complete system stability.

---

## 🏛 Industrial Relevance
The signoff methodology utilized in this project mirrors the exact protocols used by enterprise VLSI teams designing modern microprocessors and Systems-on-Chip (SoCs). While compiled here on an FPGA fabric, this exact front-end RTL input-gating architecture is regularly fed into premier industrial ASIC signoff tools to manage severe power and thermal profiles before final multi-million dollar silicon foundry tape-outs.

---

## ✒️ Author
* **Sanskar Chaudhary**
* Department of Electrical and Electronics Engineering
