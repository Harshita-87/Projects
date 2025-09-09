# Sequence Detector with Timer (Verilog)

This project implements a **Sequence Detector with a Timer** in Verilog.  
It detects the input pattern `1101`, then shifts in 4 additional bits to determine a delay duration, starts a countdown timer, and finally notifies the user when the countdown is complete.

## 🚀 Features
- **Sequence Detector**: Detects the sequence `1101`.
- **Programmable Timer**: Loads 4 bits after sequence detection to set delay.
- **Countdown Mechanism**: Delay = count × 1000 clock cycles.
- **Acknowledgement Stage**: Waits for user acknowledgment after the timer finishes.

## 🖥️ Inputs/Outputs
| Signal   | Direction | Description                    |
|----------|-----------|--------------------------------|
| clk      | Input     | System clock                   |
| reset    | Input     | Synchronous reset              |
| data     | Input     | Serial data input              |
| ack      | Input     | User acknowledgment after done |
| count    | Output    | 4-bit countdown register       |
| counting | Output    | High during countdown          |
| done     | Output    | High when timer completes      |

## 📊 FSM States
- **A–D** → Sequence detection (1101)
- **E, s1, s2, s3** → Shifting 4-bit count value
- **timer** → Countdown stage
- **user_ack** → Waits for acknowledgment before resetting

## 📂 Project Structure
- `src/top_module.v` → Main Verilog implementation
- `tb/top_module_tb.v` → Testbench (to be added)
- `docs/design_notes.md` → FSM diagram & explanation

## 🛠️ How to Run
1. Clone this repo:
   ```bash
   git clone https://github.com/your-username/sequence-detector-timer.git
   cd sequence-detector-timer
