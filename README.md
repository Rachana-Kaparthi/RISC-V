# RISC-V  
[Day 1 - Introduction to RISC-V ISA And GNU compiler toolchain ](#day-1-introduction-to-risc-v-isa-and-gnu-compiler-toolchain)  
[Day 2 - Introduction to ABI and basic verification flow](#day-2---introduction-to-abi-and-basic-verification-flow)

# Day 1-Introduction to RISC-V ISA And GNU compiler toolchain 
 
<details> 
<summary> Installation of RISC-V tools</summary>  

Steps to install Risc-tools (linux)

```
git clone https://github.com/kunalg123/riscv_workshop_collaterals.git
cd riscv_workshop_collaterals
chmod +x run.sh
./run.sh

```

 Once you run it you will get make error. ignore it  and type the following command

 ```

cd ~/riscv_toolchain/iverilog/
git checkout --track -b v10-branch origin/v10-branch
git pull 
chmod 777 autoconf.sh 
./autoconf.sh 
./configure 
make
sudo make install

```

- To set the PATH variable in .bashrc

```

gedit .bashrc
#Instead of rachana put your username
export PATH="/home/rachana/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:$PATH"
#Type at last line # close the bashrc and type
source .bashrc

```

</details>  
<details>
 <summary>Introduction to Risc-V</summary>  
 The RISC-V Instruction Set Architecture (ISA) is an open and royalty-free instruction set architecture designed for use in computer processors. It is based on the principles of Reduced Instruction Set Computing (RISC), which aims to simplify the processor's instruction set, making it easier to design, implement, and optimize processors. Below is the list of instructions used in Risc-V:  
 
 1.Pseudo Instructions  
 2.Base integer instructions[RV64I]   
 3.Multiple extention instruction[RV64M]  
 4.Single and double floating point instruction (RV64F, RV64D)  
 5.Application binary instruction  
 6.Memory allocation and stack pointer  
 
</details>  
<details>
 <summary>Lab:RISC-V Software Toolchain</summary>  
 Let us take an example [sum1ton.c] to understand how to compile code using RISC-V GCC compiler.  
 
 ```  
 #include <stdio.h>
int main()
{
    int i, sum =0, n=5;
    for(i=1;i<=n;++i)
    {
        sum+=i;
    }
    printf("sum of numbers from 1 to %d is %d \n",n,sum);
    return 0;
}      
```  
Execute the above code using GCC compiler to ensure that the code do not have any issues:  

```  
gcc <filename>  
./a.out
```
Compiling the same code using RISC-V GCC compiler or simulator:  

```
riscv64-unknown-elf-gcc <compiler option -O1 ; Ofast> <ABI specifier -lp64; -lp32; -ilp32> <architecture specifier -rv64i ; -rv32i> -o <object filename> <C filename>
spike pk <object file>
```
Output of the compilation:  

![sum1ton_simulation](https://github.com/Rachanaka/RISC-V/blob/main/Images/sum1ton_simulation.png)  

To deassemble the object file:  

```
riscv64-unknown-elf-objdump -d <filename>  
```
Use the below command to scroll through the output of object file:  
 
```
riscv64-unknown-elf-objdump -d <filename> | less
```
Use the below command to debug using spike:  
```
spike -d pk sum1ton.o
```
Below are images of debug:  

  <img src="./Images/spike_debug.png" width="400">
  <img src="./Images/obj_debug.png" width="501"> 

</details>

<details>
 <summary>Integer number representation</summary>
 Maximum unsigned number that can be represented by riscv 64 bit is 18446744073709551615 i.e. (2^64 - 1) where as maximum and  minimum signed numbers that can be represented by riscv 64 bit is 9223372036854775807 and -9223372036854775808. The same can be verified using below program: 
 
```  
#include<stdio.h>
#include<math.h>
int main()
{
    long long int max=(long long int)(pow(2,63)-1);
    long long int min=(long long int)(pow(2,63)*-1);
    unsigned long long int unsigned_max=(unsigned long long int)(pow(2,64),-1);
    printf("Highest number represented by signed long long int is %lld \n",max);
    printf("Highest number represented by signed long long int is %lld \n",min);
    printf("Highest number represented by unsigned long long int is %llu \n",unsigned_max);
    return 0;
}
```

Output of the program:  

<img src="./Images/unsigned_signed.png">

</details>  

# Day 2 - Introduction to ABI and basic verification flow  
<details>
 <summary>Introduction to Application Binary Interface</summary>    
 
How does the ABI access the hardware resources? 
  - It uses different registers(32 in number) which are each of width `XLEN = 32 bit` for RV32 (~`XLEN = 64 for RV64`) . On a higher level of abstraction these registers are accessed by their respective ABI names.
  
 - In RISC-V architecture, the memories are byte addressable. The RISC-V belongs to the little endian memory addressing system.
  
  For base integer instructions there are broadly 3 types of of such registers:
  - I-type : For instructions having immediate values as operands.
  - R-type : For instructions having only registers as operands.
  - S-type : For instructions used for storing operations.
    
Below is the format of each of these instructions:  

![instruction_format](https://github.com/Rachana-Kaparthi/RISC-V/blob/main/Images/instruction_format.png)  
Below is the list of 32 registers and their ABI names:  
![ABI_registers](https://github.com/Rachana-Kaparthi/RISC-V/blob/main/Images/ABI_registers.png)  

We try to implement the same program "sum of numbers from 1 to n" in a different method by taking the advantage of ABI interface and function calls.
- There is the main C program containing the code for the summation of numbers from 1 to n.
- We modify it and through the C program we make some funtion calls to the Assembly Language Program trhough the registers a0 and a1.
- We write the assembly language program in the RISC-V ISA and do the computation.
- Finally we send back the final results through the register a0 to the C pogram to get the final output.
  
![](https://github.com/Rachana-Kaparthi/RISC-V/blob/main/Images/Block_diagram_for_C_to_assembly_code.JPG)

**Complete Algorithm Flowchart for running the C program using Assembly language**  
![](https://github.com/Rachana-Kaparthi/RISC-V/blob/main/Images/Algorithm_Flowchart_for_C_to_assembly_code.JPG)

</details>  


## References  

- https://github.com/RISCV-MYTH-WORKSHOP/RISC-V-CPU-Core-using-TL-Verilog 


