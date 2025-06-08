# Week 1 Task Report - RISC-V Toolchain Setup

## 1. Download and Extraction

- Downloaded the RISC-V toolchain archive from:  
  https://vsd-labs.sgp1.cdn.digitaloceanspaces.com/vsd-labs/riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz

- Extracted the archive in the Downloads folder using the command:  
  ```bash
  tar -xvzf riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz

## 2. Added Toolchain to PATH

- Navigated to the extracted folder:  
  ```bash
  cd riscv-toolchain-rv32imac-x86_64-ubuntu
### 3. Verify RISC-V Toolchain Commands

Check GCC version:

```bash
riscv32-unknown-elf-gcc --version
