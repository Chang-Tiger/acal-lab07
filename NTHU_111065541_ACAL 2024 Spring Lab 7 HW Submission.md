(NTHU_111065541_張騰午) ACAL 2024 Spring Lab 7 HW Submission
===


## Gitlab code link


- Gitlab link - https://course.playlab.tw/git/Tiger_Chang/lab07

## Hw 7-1 - RISC-V M-Standard Extension
### C code - MUL (範例)
> 請參考 Lab6-1和下方範例, **將新增的 code**放在下方並加上註解, 讓 TA明白你是如何完成的。
```cpp=
// C code you add & comment
// Please DON'T copy all your code, just copy the part you add

// Instructionuction : MUL
// Line 48
typedef enum {
UNIMPL = 0,
  MUL,  
} instr_type;

//line 95
instr_type parse_instr(char* tok) {
  if ( streq(tok , "mul")) return MUL;
}

//line 522
switch( op ) {
    case UNIMPL: return 1;
    case MUL:
        if ( !o1 || !o2 || !o3 || o4 ) print_syntax_error( line,  "Invalid format" );
        i->a1.reg = parse_reg(o1 , line);
        i->a2.reg = parse_reg(o2 , line);
        i->a3.reg = parse_reg(o3 , line);
        return 1;
}

//line 642
switch (i.op) {
    case MUL: rf[i.a1.reg] = rf[i.a2.reg] * rf[i.a3.reg]; break;
}




```
#### Simulation Result & Assembly Code
> 1. 請放上你用來驗證 instruction的 assembly code, 並加上預期結果的註解。
> 2. 使用 RV32 Emulator模擬, 驗證程式碼的正確性。
> 3. 用以驗證的 assembly code可以有不只一組, 也可以只有一組, 確保 function正確就好。
- Assembly code to test MUL function
```mipsasm=
main:
addi x28,x0 ,2     ## x28 = 2
addi x29,x0 ,3     ## x29 = 3
mul  x30,x28,x29   ## x30 = 2*3 = 6
hcf                ## Terminate
```
- Simulation result
    ![](https://course.playlab.tw/md/uploads/caa38c6b-01bf-49ae-93e0-6dee5f1d0c69.png)



### C code - MULHU,REM,REMU (TODO)
> 
> 所有c code都在這
```cpp=
// Line 56
typedef enum
{
	UNIMPL = 0,

	// instruction added
	MUL,
	MULHU,
	REM,
	REMU,
	//*****************
}
// Line 107
instr_type parse_instr(char *tok)
{
	// instruction added
	if (streq(tok, "mul"))
		return MUL;
	if (streq(tok, "mulhu"))
		return MULHU;
	if (streq(tok, "rem"))
		return REM;
	if (streq(tok, "remu"))
		return REMU;
	//*****************
}
//line 732
switch( op ) {
    case UNIMPL: return 1;
    case MUL:
    case MULHU:
    case REM:
    case REMU:
        if (!o1 || !o2 || !o3 || o4)
            print_syntax_error(line, "Invalid format");
        i->a1.reg = parse_reg(o1, line);
        i->a2.reg = parse_reg(o2, line);
        i->a3.reg = parse_reg(o3, line);
        return 1;
        //****************
}

//line 1075
switch (i.op) {
    case MUL:
        rf[i.a1.reg] = rf[i.a2.reg] * rf[i.a3.reg];
        break;
    case MULHU:
    {
        uint64_t tmp = (uint64_t)rf[i.a2.reg] * (uint64_t)rf[i.a3.reg];
        printf("rf[i.a2.reg] = 0x%x\n", rf[i.a2.reg]);
        printf("rf[i.a3.reg] = 0x%x\n", rf[i.a3.reg]);
        printf("tmp = 0x%llx\n", tmp);
        rf[i.a1.reg] = tmp >> 32;
        break;
    }
    case REM:
    {
        if ((uint32_t)rf[i.a3.reg] == 0){
            printf("Division by zero!!");
            rf[i.a1.reg] = rf[i.a2.reg];
        } else if(rf[i.a2.reg] == INT32_MIN && rf[i.a3.reg] == -1){ // may overflow
            printf("Overflow!!");
            rf[i.a1.reg] = 0;
        }else{
            rf[i.a1.reg] = (int32_t)rf[i.a2.reg] % (int32_t)rf[i.a3.reg];
        }
        break;
    }
    case REMU:
    {
        if ((uint32_t)rf[i.a3.reg] == 0){
            printf("Division by zero!!");
            rf[i.a1.reg] = rf[i.a2.reg];
        } else {
            rf[i.a1.reg] = (uint32_t)rf[i.a2.reg] % (uint32_t)rf[i.a3.reg];
        }
        break;
    }

}

			//*****************
```
###  MULHU (TODO)
#### Simulation Result & Assembly Code

- Assembly code to test MULHU function
```mipsasm=
main:
lui x28, 0xFF100
ori x28, x28, 0x00000
lui x29, 0xFFF00
ori x29, x29, 0x00000
mulhu  x30,x28,x29
hcf
```
- Simulation result
> ![](https://course.playlab.tw/md/uploads/d75ca972-1fb1-4d6b-9438-85fcf61e6606.png)
> tmp中print 0xff100000*0xfff00000的64bit答案，前32bit為ff000f00，也就是x30中的答案


###  REM (TODO)

#### Simulation Result & Assembly Code

- Assembly code to test REM function
```
main:
addi x28,x0 ,-11
addi x29,x0 ,3
rem  x30,x28,x29
hcf
```
- Simulation result
> ![](https://course.playlab.tw/md/uploads/f47fa59b-a86d-485e-a49a-642fec090872.png)
> -11 % 3有號數會是-2也就是x30中的0xfffffffe




###  REMU (TODO)
> 請參考 Lab6-1和範例, **將新增的 code**放在下方並加上註解, 讓 TA明白你是如何完成的。

#### Simulation Result & Assembly Code

- Assembly code to test REMU function
```mipsasm=
main:
addi x28,x0 ,-11
addi x29,x0 ,3
remu  x30,x28,x29
hcf
```
- Simulation result
> ![](https://course.playlab.tw/md/uploads/a468a2c6-9d52-4c85-91fe-df660e56db20.png)
> 同樣用-11還有3進行REMU，因為是無號數，-11會變成4294967285，4294967285%3=2




## HW7-2 - RISC-V Bit Manipulation Extension
### Gitlab code link (Your own branch)


- Gitlab link of your branch - https://course.playlab.tw/git/funfish111065531/lab07-group/-/tree/Tiger_Chang_branch?ref_type=heads


- Gitlab link of your group project repo - 
https://course.playlab.tw/git/funfish111065531/lab07-group.git




### C Code - asm
> 請參考 Lab7-1和範例, **將新增的 code**放在下方並加上註解, 讓 TA明白你是如何完成的, 如果超過一個指令請依照 HW7-1的方式新增作業說明。
```cpp=
typedef enum
{
	UNIMPL = 0,

	// instruction added Tiger_Chang
	ANDN,
	CLMUL,
	CLMULH,
	CLMULR,
	CLZ,
	CPOP,
	CTZ,
	MAX,
}
instr_type parse_instr(char *tok)
{
	// instruction added
	if (streq(tok, "andn"))
		return ANDN;
	if (streq(tok, "max"))
		return MAX;
	if (streq(tok, "clmul"))
		return CLMUL;
	if (streq(tok, "clmulh"))
		return CLMULH;
	if (streq(tok, "clmulr"))
		return CLMULR;
	if (streq(tok, "clz"))
		return CLZ;
	if (streq(tok, "ctz"))
		return CTZ;
	if (streq(tok, "cpop"))
		return CPOP;
}

int parse_instr(int line, char *ftok, instr *imem, int memoff, label_loc *labels, source *src)
{
     // instruction added
    case ANDN:
    case CLMUL:
    case CLMULH:
    case CLMULR:
    case MAX:
    {
        if (!o1 || !o2 || !o3 || o4)
            print_syntax_error(line, "Invalid format");
        i->a1.reg = parse_reg(o1, line);
        i->a2.reg = parse_reg(o2, line);
        i->a3.reg = parse_reg(o3, line);
        return 1;
    }
    case CPOP:
    case CTZ:
    case CLZ:
        if (!o1 || !o2 || o3 || o4)
            print_syntax_error(line, "Invalid format");
        i->a1.reg = parse_reg(o1, line);
        i->a2.reg = parse_reg(o2, line);
        return 1;
    //****************   
}
switch (i.op){
    case ANDN:
        rf[i.a1.reg] = rf[i.a2.reg] & (~rf[i.a3.reg]);
        break;
    case MAX:
        rf[i.a1.reg] = ((int32_t)rf[i.a2.reg]) < ((int32_t)rf[i.a3.reg]) ? ((int32_t)rf[i.a3.reg]) : ((int32_t)rf[i.a2.reg]);
        break;
    case CPOP:
    {//counts the number of 1’s
        printf("cpop\n");
        int count = 0;
        uint32_t num = rf[i.a2.reg];
        while (num != 0)
        {
            if (num & 1)
            {
                count++;
            }
            num >>= 1;
        }
        rf[i.a1.reg] = count;
        break;
    }
    case CLZ:
    {//Count leading zero bits
        printf("clz\n");
        printf("rf[i.a2.reg] = 0x%x\n", rf[i.a2.reg]);
        uint32_t mask = 0x80000000;
        uint32_t num = 32;
        for (int bit = 31; bit >= 0; --bit, mask >>= 1)
        {
            if (rf[i.a2.reg] & mask)
            {
                num = 31 - bit;
                break;
            }
        }
        rf[i.a1.reg] = num;
        printf("rf[i.a1.reg] = 0x%x\n", rf[i.a1.reg]);
        break;
    }
    case CTZ:
    {//Count trailing zeros
        uint32_t mask = 1;
        int bit;
        for (bit = 0; bit < 32; ++bit, mask <<= 1)
        {
            if (rf[i.a2.reg] & mask)
            {
                rf[i.a1.reg] = bit;
                break;
            }
        }
        rf[i.a1.reg] = bit;
        break;
    }

    case CLMUL:
    {
        uint32_t rs1 = rf[i.a2.reg];
        uint32_t rs2 = rf[i.a3.reg];
        uint32_t rd = 0;
        for (int bit = 0; bit < 32; ++bit)
        {//檢查rs2第一位是否為1，是就rd = (rd ^ rs1 << bit)，否則rd不變
            rd = ((rs2 >> bit) & 1) ? (rd ^ rs1 << bit) : rd;
        }
        rf[i.a1.reg] = rd;
        break;
    }
    case CLMULR:
    {   //to check 64bit CLMUL res
        // uint64_t rs1_ = (uint64_t)rf[i.a2.reg];
        // uint64_t rs2_ = (uint64_t)rf[i.a3.reg];
        // uint64_t rd_ = 0;
        // for (int bit = 0; bit < 32; ++bit)
        // {
        // 	rd_ = ((rs2_ >> bit) & 1) ? (rd_ ^ rs1_ << bit) : rd_;
        // }
        // printf("64res : 0x%llx\n", rd_);
        uint32_t rs1 = rf[i.a2.reg];
        uint32_t rs2 = rf[i.a3.reg];
        uint32_t rd = 0;
        printf("rf[i.a2.reg] = 0x%x\n", rf[i.a2.reg]);
        printf("rf[i.a3.reg] = 0x%x\n", rf[i.a3.reg]);
        for (int bit = 0; bit < 32; ++bit)
        {//根據文件檢查rs2第一位是否為1，是就rd ^ (rs1 >> (32 - bit - 1))否則rd不變
            rd = ((rs2 >> bit) & 1) ? (rd ^ (rs1 >> (32 - bit - 1))) : rd;
        }
        rf[i.a1.reg] = rd;
        break;
    }
    case CLMULH:
    {    //to check 64bit CLMUL res
        // uint64_t rs1_ = (uint64_t)rf[i.a2.reg];
        // uint64_t rs2_ = (uint64_t)rf[i.a3.reg];
        // uint64_t rd_ = 0;
        // for (int bit = 0; bit < 32; ++bit)
        // {
        // 	rd_ = ((rs2_ >> bit) & 1) ? (rd_ ^ rs1_ << bit) : rd_;
        // }
        // printf("64res : 0x%llx\n", rd_);

        uint32_t rs1 = rf[i.a2.reg];
        uint32_t rs2 = rf[i.a3.reg];
        uint32_t rd = 0;

        for (int bit = 1; bit < 32; ++bit)
        {
            if ((rs2 >> bit) & 1)
            {
                rd = rd ^ (rs1 >> (32 - bit));
            }
            // rd = ((rs2 >> bit) & 1) ? (rd ^ (rs1 >> (32 - bit))) : rd;
        }
        printf("rf[i.a1.reg] = 0x%x\n", rf[i.a1.reg]);
        rf[i.a1.reg] = rd;
        break;
    }

    //*****************
}

```

#### Simulation Result & Assembly Code
- Assembly code to test instruction you picked
- ANDN
```mipsasm=
main:

addi x28,x0 ,0xA5
addi x29,x0 ,0xF0
andn x30,x28,x29

hcf
```

>![](https://course.playlab.tw/md/uploads/f4074885-7d63-4ef0-b32a-1e56b818feda.png)




- CLMUL
```mipsasm=
main:

addi x28,x0,15
addi x29,x0,5
clmul x30,x28,x29

hcf
```
>![](https://course.playlab.tw/md/uploads/2aebe572-3c05-4600-913b-ba7c89581292.png)



- CLMULH
```mipsasm=
main:
lui x28, 0x11111
ori x28, x28, 0x111
lui x29, 0xFFFFF
ori x29, x29, 0xFFF
clmulh x30,x28,x29

hcf
```
>![](https://course.playlab.tw/md/uploads/85c49d8d-852b-4051-8d4c-9f593e96056e.png)
>64res是0x11111111與0xffffffff做CLMUL的64 bit答案前32 bit與x30中的相符0x0f0f0f0f


- CLMULR
```mipsasm=
main:

lui x28, 0xFFFF0
ori x28, x28, 0x000
lui x29, 0xFFFF
ori x29, x29, 0xFFF
clmulr x30,x28,x29

hcf
```

>![](https://course.playlab.tw/md/uploads/4ae81193-1e3b-45da-85ff-52034d199fc2.png)
>clmulr produces bits 2·XLEN−2:XLEN-1 of the 2·XLEN carry-less product.
>64res是0xffff0000與0xffffffff做CLMUL的64 bit答案。
bits 2·XLEN−2:XLEN-1 of the 2·XLEN carry-less product x30中的相符. 0xaaaa0000


- CLZ
```mipsasm=
main:

addi x27,x0, 9
clz x28,x27
addi x29,x0, 0
clz x30,x29

hcf
```
>![](https://course.playlab.tw/md/uploads/2372ed2c-b614-4c37-b029-5244b0ba5fb5.png)
>counts the number of 0’s before the first 1 from MSB, if the input is 0, the output is XLEN
>測9為28(1c)測0則為32



- CPOP
```mipsasm=
main:
addi x29,x0 ,-1
cpop  x30,x29

hcf
```
>![](https://course.playlab.tw/md/uploads/6c85e1bb-0cae-4d02-86d2-4ced798758ec.png)
>counts the number of 1’s (i.e., set bits) in the source register用-1也就是0xffffffff算出來是32



- CTZ
```mipsasm=
main:

addi x27,x0 ,12
ctz x28,x27

addi x29,x0 ,1
ctz x30,x29

hcf
```
>![](https://course.playlab.tw/md/uploads/e7fbe655-4a55-4686-8abd-9bab957f02c4.png)
>This instruction counts the number of 0’s before the first 1, starting at the least-significant bit (i.e., 0) and progressing to the most-significant bit (i.e., XLEN-1). Accordingly, if the input is 0, the output is XLEN, and if the least-significant bit of the input is a 1, the output is 0.測12(也就是c)則為1，測1則為0

- MAX
```mipsasm=
main:

addi x28,x0 ,2
addi x29,x0 ,-3
max  x30,x28,x29

hcf
```
>![](https://course.playlab.tw/md/uploads/19113a05-93b7-4a86-982b-a619ec913821.png)
>-3和2比大小結果為2









## Bonus
> Bonus請依照 lab6 document中的 bonus template進行繳交。

