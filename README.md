# **5-Candidate Digital Voting Machine**

A complete, memory-backed digital logic voting system built using the **Digital** logic simulator.

This project simulates a secure, real-world election process at the hardware level. It features anti-spam edge detection, live total vote tracking, EEPROM data storage, multiplexed 7-segment result displays, and a hardware-level automated winner evaluation system.

## **Key Features**

* **Anti-Tamper & Debouncing:** Utilizes edge-triggered pulse generators and custom SR Latch logic to prevent button-mashing and double-voting.  
* **16-Bit BCD Counting:** Cascaded synchronous counters allow for up to 9,999 votes per candidate.  
* **Multiplexed Data Routing:** A custom-built 16-bit wide MUX bus funnels 96 bits of total candidate data into a single, clean "Results Dashboard" display.  
* **Memory Storage:** Election results are actively written to an onboard EEPROM for secure post-election retrieval.  
* **Automated Winner Evaluation:** A custom cascading comparison matrix evaluates bundled 16-bit BCD scores to identify the strict winner once the polls are closed.  
* **Suspense Mode:** Individual candidate vote counters are hidden. Only the "Total Votes" counter is visible during the election, with candidate results and the winner LED securely locked behind a votes\_done master switch.

## **System Architecture & Subcircuits**

The project is heavily modularized to keep the main canvas clean and organized. Below is a breakdown of the custom integrated circuits (ICs) designed for this machine.

### **1\. The Main Control Board (voting\_machine.dig)**

The central hub where all subcircuits integrate. It handles the user interface, candidate inputs, the votes\_done master lock, and the EEPROM memory addressing via s0, s1, s2 select lines.

### **2\. Input Security: vote\_lock**

A classic Set-Reset (SR) Latch built using cross-coupled NOR gates. When any vote is registered, the latch sets, disabling the master input AND gate to prevent simultaneous button presses or rapid-fire spamming. It must be manually or cyclically reset for the next voter.

### **3\. Tallying: VoteCounter**

A 4-stage cascaded ripple counter. Each block takes the overflow pulse from the previous decimal place (Ones \-\> Tens \-\> Hundreds \-\> Thousands), outputting a 16-bit Binary-Coded Decimal (BCD) number capable of tracking up to 9,999 votes.

### **4\. Data Routing: MUX4X1\_BUS & cmux\_core**

To save hardware space and avoid using 24 individual 7-segment displays, the candidate data is multiplexed.

* **cmux\_core**: Custom combinational logic gates mapping binary select lines (000 to 101\) to specific candidate inputs.  
* **MUX4X1\_BUS**: Four cmux\_core blocks running in parallel to route the full 16-bit wide data bus (4 digits x 4 bits) into the EEPROM and the single Results Dashboard.

### **5\. Winner Evaluation: VoteComparator**

Rather than comparing individual 4-bit digits, the 4-bit BCD outputs are bundled into 16-bit wires. This subcircuit uses a "brute-force" comparison matrix, checking a candidate's 16-bit score against every other candidate simultaneously. The winner's LED only illuminates if their score strictly \> all others, AND the votes\_done line is pulled high.

## **How to Run the Simulation**

1. **Prerequisites:** Download and install [Digital](https://github.com/hneemann/Digital) (A digital logic designer and circuit simulator).  
2. **Clone the Repository:**  
   git clone \[https://github.com/ViraajRandhawa/Digital-Voting-Machine.git\](https://github.com/ViraajRandhawa/Digital-Voting-Machine.git)

3. Open the voting\_machine.dig file in the Digital simulator.  
4. Click the **Start Simulation** (Play) button in the top toolbar.  
5. **To Vote:** Click the input pins (A through E). Notice the *Total Votes* counter increasing.  
6. **To Close Polls:** Toggle the votes\_done switch. The inputs will lock, and the winning candidate's LED will illuminate.  
7. **To View Results:** Toggle the s0, s1, s2 switches to cycle through the ROM addresses and view individual candidate totals on the Result Counter.

*Designed and implemented by Viraaj Randhawa & Pancham Chanjotra for Digital Design Fundamentals.*