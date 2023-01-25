# noontide-specification
An ongoing effort to design the Noontide SUBLEQ Computer.

# The CPU
The CPU is identical to that of the Dawn OS design. The following is its original description:  
64-bit big-endian SUBLEQ CPU. The opcodes are stored in ABC format, and they operate in the following way (pseudocode):

```c
long long A=GET_64_BIT_DATA(eip);
long long B=GET_64_BIT_DATA(eip+8);
long long C=GET_64_BIT_DATA(eip+16);
long long value_of_b=GET_64_BIT_DATA(B)-GET_64_BIT_DATA(A);
STORE_64_BIT_DATA(value_of_b, B);
if(value_of_b<=0){
 eip=C;
}else{
 eip+=24;
}
```

The instruction uses fixed memory addresses. The eip of the first core is 0 on boot.

# Memory Layout
0x00000000-0x10000000 Boot sector  
0x10000000-0x14000000 Hardware I/O  
0x13ED27E0-0x13ED27E8 Serial connected (1 if connected, 0 otherwise)  
0x13ED27E8-0x13ED27F0 Serial input (In 1-indexed Unicode. That is, put 11 instead of 10 when a newline is fed. Must be set to 0 after reading)  
0x13ED27F0-0x13ED27F8 Serial output (In 1-indexed Unicode. Must be set to 0 by the serial after outputting) 
0x13EE0000~0x13FE0000 CPU control area

## CPU control area
0x13EE0000 contains CPU 0's state. 0 means CPU detached, 1 means running, and 2 means stop requested. The CPU should set this field to 4 after it stops itself. It can then be reactivated by setting the state to 1.  
0x13EE0008 contains CPU 0's EIP. It's written by the CPU after every run cycle and read by the CPU prior to reactivation.  
0x13EE0010 then stores CPU 1's state, whereas 0x13EE0018 stores CPU 1's EIP.  
This is the case until CPU 65535, which would have its state at 0x13FDFFF0.

# Acknowledgements
This specification is heavily inspired by the work of Geri and their Dawn operating system.  
Dawn OS Homepage: http://users.atw.hu/gerigeri/DawnOS/index.html