---
layout: article
title: [SP] 12. Linking
tags: System Programming
category: System Programming
picture_frame: shadow
use_math: true
---

# 12. Linking


# Linking

## What’s linking?

- A process of collecting and combining various pieces of code and data into a single file that can be loaded (copied) into memory and executed
여러 코드와 데이터를 모아 memory에 load될 수 있고 executable file 하나로 만드는 작업
    - 큰 규모의 프로그램을 한 개의 소스 파일로 구성하는 대신
    - 별도로 editable, compilable smaller module로 나눌 수 있음
    - edit할 때 단순히 해당 파일을 recompile하고 다른 file을 recompile할 필요 없이 link함.
- 작은 프로그램을 만든다고 하면 linking이 필요 없지만 큰 프로그램을 만들면 잘게 잘게 기능을 나누어 쪼갤 필요가 있다.
- Linking can be performed at compile time, load time, and run time
    - compile time 시 수행 - src code는 기계어로 translate됨
    - load time - application program에 의해 수행
    - run time
- Linking is performed automatically by programs called linkers on modern systems
    - 초기에는 수동으로 수행된 linking → 현재는 linker가 대신 수행해줌

## Why bother learning about linking?

- Understanding linkers will help you

### build large programs 큰 프로그램 작성에 도움

- Unless you understand how a linker resolve references, what a library is, and how a linker uses a library to resolve references, these kinds of errors will be baffling and frustrating.
- 발생되는 linker error들에 대한 식견
- 작은 프로그램 compile하고 linking하는 과정
    - local + external defined symbol들을 어떻게 reference할 수 있느냐.
    - Malloc / printf reference : 어떻게 library를 활용하여 reference하느냐
- linux에서 직접 link해보면 link error가 많이 뜨는데 이해하기 쉬워질 것

### avoid dangerous programming errors 위험한 에러를 피할 수 있음

- Programs that incorrectly define multiple global variables can pass through the linker without any warnings in the default case. The resulting programs can exhibit baffling run-time behavior and are extremely difficult to debug.
- global var을 여러 분야에서 잘못 선언 : run-time시에 여러 문제가 생길 수 있음
- linker에서 잡아주지 못하는 / linker의 기능과 방식 을 잘 이해하지 못한 상태에서 runtime 때 찾기는 어렵다
    - debug하기도 어려운 상황에서→ 어떻게 권장하는지를 보고 어떻게 발생하고 회피하는지 알 수 있음

### understand how language scoping rules are implemented

- What’s the difference between global and local variables? (전역 vs 지역)
- What does it really mean when you define a variable or function with the **static** attribute?

### understand other important systems concepts

- The executable object files produced by linkers play key roles in important systems functions such as loading and running programs, virtual memory, paging, and memory mapping
- system의 중요한 여러 개념들을 이해하는 데 도움

### enable you to exploit shared libraries

- With the increased importance of shared libraries and dynamic linking in modern operating systems, linking is a sophisticated process that provides the knowledgeable programmer with significant power.
현대 사회에서 중요해진 shared library, dynamic linking의 중요성 → 주요 능력을 제공하는 process
    - 쉬우면서도 간과하는 부분이 많은 linking
- For example, many software products use shared libraries to upgrade shrink-wrapped binaries at run time.
    - shared library : dynamic linking 다양하게 쓰임
    - smaller하게 된 binary를 runtime때 upgrade
- Many Web servers rely on dynamic linking of shared libraries to serve dynamic content.5
    - 많은 sw들은 runtime 시에 linking많이 씀 : service할 때 동적으로 mapping

## Example C Program

```c
int sum(int *a, int n);
int array[2] = {1, 2};
int main()
{
    int val = sum(array, 2);
    return val;
}
```

```cpp

int sum(int *a, int n)
{
    int i, s = 0;
    for (i = 0; i < n; i++)
    {
        s += a[i];
    }
    return s;
}
```

- 각각에 대해서 linking을 수행하여 하나의 executable file
- sum이라는 procedure는 sum.c로부터 reference하여 구동시켜 Main 함수에서 integer array

# Static Linking

- Programs are translated and linked using a compiler driver:
    
    ```cpp
    linux> gcc -Og -o prog main.c sum.c
    linux> ./prog
    ```
    

## Compiler driver

- Preprocessor, compiler, assembler, linker를 필요에 따라 user 대신하여 호출

![Untitled](12/Untitled.png)

- C preprocessor (cpp) : `main.c` → `main.i`
    - translates the C source file main.c into an ASCII intermediate file main.i
- C Compiler (cc1) : `main.i` → `main.s`
    - translates main.i into an ASCII assembly-language file main.s
- Assembler (as)`main.s` → `main.o`
    - translates `main.s` into a binary relocatable object file `main.o`
- Linker (ld)
    - combine `main.o` and `sum.o` along with the necessary object files,to create the binary executable object file
    - ld 실행 : prog을 생성하기 위해 `main.o / sum.o` 연결
- → `./prog` : prog 실행 
- Shell은 loader라는 OS Function을 호출하여, 실행 파일 prog의 code, data를 memory로 복사하고 control을 program 시작 부분으로 전환한다.
- Source File
    - 각 symbol들의 주소, access하는 주소
    memory할 때 어디에 올라가는지
    VM에서 어디 부분에 올라가는지 등은 잘 정해져 있지 않기 때문에 relocate필요
- Separately compiled relocatable obj files
    - linking해야지 virtual memory 상 어디에 올라가는지 상대적인 offset 부분을 나중에 알 수 있기 때문에, 나중에 relocate되는 obj file이라고 부른다.
    그러고 나서 나중에 linking되게 되면 실행 file 생성
- Fully linked

## Why Linkers?

### Reason 1: Modularity

Program can be written as a collection of smaller source files, rather than one monolithic mass.
구조적으로 쪼개서 작은 source file로 만든다 → 내가 필요한 부분들만 쓸 수 있게 하는데 도움이 된다

- Can build libraries of common functions (more on this later)
    - Standard c library, math library : 다른 application에서도 사용 가능
    - e.g., Math library, standard C library

### Reason 2: Efficiency

- Time: Separate compilation
    - sum.c code만 수정하고 sum.c만 recompile
    - monolithic한 프로그램이 아니라면 코드 전체를 모두 compile하는 것보다 더 빨라짐
    - Change one source file, compile, and then relink.
    - No need to recompile other source files.
- Space: Libraries
    - Common functions can be aggregated into a single file...
        - 하나의 compile로 만들어질 수 있음
        - 모두다 사용하는게 아니고, 특정한 부분만 사용할 수 있기 때문에 필요한 특정한 부분만 사용하는거기 때문에 필요에 따라 linker를 돌아 사용하는게 효과적
    - Yet executable files and running memory images contain only code for the functions they actually use.
        - memory에 다 올라갈 필요가 없음 → powerful software programming

## What Do Linkers Do?

- to make executable file → linker는 두 가지 step task를 수행해야 한다.

### Step 1: Symbol resolution

- Programs define and reference symbols (global variables and functions):
    - program 내부에서 여러 symbol을 가져다가 reference
        ```cpp
        void swap() {...} /* define symbol swap */
        swap(); /* reference symbol swap */
        int *xp = &x; /* define symbol xp, reference x */
        ``` 
    - Swap function definition - reference
    - xp라는 symbol : x라는 variable의 address value
- Symbol definitions are stored in object file (by assembler) in symbol table.
    - Symbol table is an array of structs → Executable file 생성 이전 object file : section에 symbol table이 들어감.
        - Each entry includes name, size, and location of symbol.
        - compile시 preprocess다음 c compile하여 obj 파일 만들어질 때 elf라는 file format에서 symbol table 안에 저장되는데 각 symbol마다 크기, 이름, 위치 생성 정보를 가진 table
- **During symbol resolution step, the linker associates each symbol reference with exactly one symbol definition.** symbol이 있으면 각각의 reference가 정확히 하나의 symbol definition과 대응되어야 한다.
    - cnt가 두 번 initialized -> linker error
        ```cpp
        a.c :
        int cnt= 5;
        main(){…}
        
        b.c : 
        int cnt = 0;
        Fun(){…}
        ```
    

### Step 2: Relocation

- Merges separate code and data sections into single sections
    - 두개의 다른 object file (code/data)를 모아 하나의 section으로 만emsek.
    - 0번지에서 시작하는 code, data section
- Executable file로 linux가 만들게 되는데 memory location에 올라가는 절대 주소로 변환 : 실제 가상메모리 주소는 어떻게 할 것이냐
    - Relocates symbols from their relative locations in the `.o` files to their final absolute memory locations in the executable.
    - linker : code/data section을 symbol def과 연결 
        - → relocate하며 모든 reference를 모두 수정하여 절대주소를 가리키도록 함
- relocation entry : symbol에 대한 모든 reference 가 새로운 위치를 가리킬 수 있게 해 줌
    - Updates all references to these symbols to reflect their new positions.
    

Let’s look at these two steps in more detail....

# Three Kinds of Object Files (Modules)

### Relocatable object file (.o file)

- Contains code and data in a form that can be combined with other relocatable object files to form executable object file.
- Each .o file is produced from exactly one source (.c) file
- `Main.o, .o`
- code, data를 가지고 있는데 다른 relocatable file과 같이 compile되어 실행 file을 만들기 위한 code, data를 가진 object file

### Executable object file (a.out file)

- Contains code and data in a form that can be copied directly into memory and then executed.
- 명명 of `a.out`
    - BELL Lab에서 compile하고 executable file 이름을 지정하지 않을 때 a.out으로 명명
    - 특정한 의미는 없음

### Shared object file (.so file)

- Special type of relocatable object file that can be loaded intomemory and linked dynamically, at either load time or run-time.
- Called Dynamic Link Libraries (DLLs) by Windows13

# Executable and Linkable Format (ELF)


- Standard binary format for object files
- One unified format for
    - Relocatable object files (.o),
    - Executable object files (a.out)
    - Shared object files (.so)
- Generic name: ELF binaries

![Untitled](12/Untitled_1.png)

### Elf header (16 B)

- Describes word size, byte ordering, file type (.o, exec, .so), machine type, file offset of section header table, etc.

### Section header table

- Offsets and sizes of each section

![Untitled](12/Untitled_2.png)

### `.text` section

- Code (the machine code of the compiledprogram)
- compile된 program의 기계어 code가 들어가 있음
- c compile후 assembly에 의해 기계어 코드 (add, multiply, load, store에 대한 assembly에 대한 기계어 코드가 들어가 있음)

### `.rodata` section

- Read only data: jump tables for switch
- Read only data section : read only data, jump table, format string 가지고 있음

### `.data` section

- Initialized global variables
    - Initialized global var : int count = 6;
    - Local variable static : static int a =3;

### `.bss` section

- Uninitialized global variables
    
    아직은 yet initialized : 위치만을 가지고 잇음 (공간 할당 x)
    
- 이름 : “Block Started by Symbol”, “Better Save Space”

initialize된 global var을 구분함으로서 공간을 할당하지만 .bss는 공간을 할당하지 않아 공간 효용성을 구분할 수 있음 →Has section header but occupies no space

- 나중에 memory에 load될 때는 0으로 초기화 : 그 때의 메모리 공간 생성

![Untitled](12/Untitled_3.png)

### `.symtab` section

- Symbol table with info. about functions and global variables (procedures and static variable names) that are defined and referenced in the program-

### `.rel.text`section

- Relocation info for .text section
- Addresses of instructions that will need to be modified in the executable
    - Instructions for modifying.-

### `.rel.data`section

- Relocation info for .data section
- Addresses of pointer data that will need to be modified in the merged executable-

### `.debug` section

- Info for symbolic debugging (gcc -g)ELF header.text section.rodata section.data section.bss section.symtab section.rel.txt section.rel.data section.debug sectionSection header table18

# Symbols and Symbol Tables

- Each relocatable object module, m, has a symbol table
    - 각각의 relocatable object (module) : 어떠한 module에 의해 reference / define되어 있는 symbol에 대한 정보를 가진 symbol table이 가진 정보
- The symbol table contains information about the symbols that are defined and referenced by m
- In the context of a linker, there are three different kinds of symbols:
    - Global symbols
    - External symbols
    - Local symbols

## Linker Symbols

### Global symbols

- Symbols defined by module m that can be referenced by other modules.
- E.g.: non-static C functions and non-static global variables.
    - 다른 module에 의해 reference될 수 있는 global variable
        ``` 
        a.c/Int count = 5
        b.c/Int count;
        ```

### External symbols

- Global symbols that are referenced by module m but defined by some other module.
- module에 의해서 reference되는 symbol이지만 다른 module에 의해서 defined되어 있는 것
    - a.c입장에서 b.c.의 count symbol

### Local symbols

- Symbols that are defined and referenced exclusively by module m.
- E.g.: C functions and global variables defined with the static attribute.
- **Local linker symbols are not local program variables 20**

## Local linker symbols vs. Local program variables

- `.symtab` does not contain any symbols that correspond to local non-static program variables
    - var 중 non static variable들은 Symbol table에 들어가지 않음 / linker에 연관 x
    - Local var : stack / Local symbol : symbol table에 들어감
- Local non-static program variables are managed at run time on the stack and are not of interest to the linker

See more details in next slides...

# Step 1: Symbol Resolution

![Untitled](12/Untitled_4.png)


## Local Symbols

Local static var : global variable -> .data

- 단 local var이기 때문에 함수를 다시 호출하게 되면 재접속 가능
Local nonstatic var : static x, 일반 local var -> @stack
- f, g에서 같은 variable x를 쓰지만 다른 symbol
X.1, x.2 이런식으로 compiler가 놓고 compiler가 unique한 이름을 가지도록 함.
- Local non-static C variables vs. local static C variables
    - local non-static C variables: stored on the stack
    - local static C variables: stored in either .bss, or .data
        - Local static var : global variable -> .data
        - 단 local var이기 때문에 함수를 다시 호출하게 되면 재접속 가능
        - Local nonstatic var : static x, 일반 local var -> @stack

![Untitled](12/Untitled_5.png)

- Compiler allocates space in .data for each definition of x
- Creates local symbols in the symbol table with unique names,
    - f, g에서 같은 variable x를 쓰지만 다른 symbol
    → x.1, x.2 이런식으로 compiler가 놓고 compiler가 unique한 이름을 가지도록 함.
    - e.g., x.1 and x.2

## How Linker Resolves Duplicate Symbol Definitions

- Program symbols are either strong or weak
- **Strong:** procedures and initialized globals
    - initialize되어 있는 variable
- **Weak:** uninitialized globals
    - uninitialize되어 있는 variable

![Untitled](12/Untitled_6.png)

## Linker’s Symbol Rules

- **Rule 1: Multiple strong symbols are not allowed**
    - Each item can be defined only once
    - Otherwise: Linker error
    > 만일 symbol이 strong하다 - 단 한번만 선언 가능
    - compiler가 보고 나서 foo=5(p1.c) / foo=10(p2.c) 
        - -> link error
    - 같은 게 strong이면 안됨
    - rule 1. : 만일 symbol이 strong하다 - 단 한번만 선언 가능
        - compiler가 보고 나서 foo=5(p1.c) / foo=10(p2.c) -> link error
        - 같은 게 strong이면 안됨
- **Rule 2: Given a strong symbol and multiple weak symbols, choose the strong symbol**
    - References to the weak symbol resolve to the strong symbol
    - Weak가 strong을 쫓아간다.
    - P2의 foo : weak-> p1의 foo : strong을 쫓아감
    p2에서 foo 접근 = p1에서의 foo 접근
    - Weak가 strong을 쫓아간다.
        - P2의 foo : weak-> p1의 foo : strong을 쫓아감
        - p2에서 foo 접근 = p1에서의 foo 접근
- **Rule 3: If there are multiple weak symbols, pick anarbitrary one**
    - 둘다 weak면 compiler가 임의로 설정


## Linker Puzzle

![Untitled](12/Untitled_7.png)

- link error - 모종의 이유로 version이 달라 뜨는 error

![Untitled](12/Untitled_8.png)

![Untitled](12/Untitled_9.png)

- 둘 중에 무엇을 선택할 지 모름 (weak)
    - 1->2, : y를 overwrite하지 않음 / 2->1 : overwrite
- → ‘might’라는 용어 사용 : 해당 xy가 다른 strong symbol reference할 수 있기 때문이다

![Untitled](12/Untitled_10.png)

- double로 선언한 x (8byte) - integer 로 원래 선언된 형태 → y에게도 악영향

![Untitled](12/Untitled_11.png)

- Nightmare scenario : 2 identical weak structs, compiled by different compilers with different alignment rules (compiler dependent)
- link error : 모종의 이유로 version이 달라 뜨는 error

## Two weak definitions of x (rule 3)

- Run-time bugs
    - Can cause some insidious run-time bugs that are incomprehensible to the unwary programmer
    - compiler error가 발생하지는 않는데
    - 둘 다 uninitialized된 형태, f 수행 후 15212로 변화
        ![Untitled](12/Untitled_12.png)

## Another example (rule 2)

- Subtle and nasty run-time bugs!
    - On an x86-64/Linux machine, doubles are 8 bytes and ints are 4 bytes
    - Suppose the address of x is 0x601020 and the address of y is 0x601024
    - The assignment x = 0.0 in lin6 6 will overwrite the memory locations for x and y with the double-precision floating-point representation of negative zero!
        ![Untitled](12/Untitled_13.png)
    - Compile error 전혀 안 뜸
    - 나도 모르게 y값이 바뀜, 그러나 link error를 runtime때 찾기에는 너무 어렵다
        - → 주의깊게 익혀야 한다.
    - library에서 우연치 않게 같은 symbol을 써서 이상하게 돌아가는 경우도 발생가능    
    - Compile error 전혀 안 뜸
    - 나도 모르게 y값이 바뀜
    - link error를 runtime때 찾기에는 너무 어렵다
    - -> 주의깊게 익혀야 한다.
    - library에서 우연치 않게 같은 symbol을 써서 이상하게 돌아가는 경우도 발생가능
    

## Global Variables

- Avoid if you can
- Otherwise
    - Use static if you can
        - data 에서 선언되지만 해당 함수 내에서만 접근 가능
    - Initialize if you define a global variable : initialize해서 가급적 문제가 발생하지 않도록 함.
    - Use extern if you reference an external global variable
        - Initialize된 형태로 써라 (쓰지 않을 수 있다면 쓰지 마라)
        - → 예기치 않은 runtime bug에 빠지지 않도록 해라.
1. static 선언
    - data 에서 선언되지만 해당 함수 내에서만 접근 가능
2. initialize해서 가급적 문제가 발생하지 않도록 함.
3. extern
    - Initialize된 형태로 써라 (쓰지 않을 수 있다면 쓰지 마라)
    - > 예기치 않은 runtime bug에 빠지지 않도록 해라.

# Step 2: Relocation

- relocation
    - 내가 penn university에서 졸업하고 job을 구하면 relocate : relocate negotiation (주소 바뀌는 것에 대한 signing bonus 등)
    - 내가 penn university에 있다가 다른 회사 사무소 주소로 바귐
    - 이처럼, 내가 현재 접근하는 변수의 주소를 모르기 때문에 relocate하는 과정 : 주소 찾아서 assign해주는 작업

**Step1: Symbol resolution**

- Once the linker has completed the symbol resolution step, it has associated each symbol reference in the code with exactly one symbol definition
    - linker가 symbol resolution step에 따라 resolve하게 되면 모든 symbol reference는 오직 한개의 symbol definition에 associate된다
- At this point, the linker knows the exact sizes of the code and data sections in its input object modules.
    - obj file 생성하게 되면 code, data에 대한 정확한 정보를 알게 됨
- 만일 그렇지 않다면 link error

**Now, next step is the relocation step (Step 2)**

- Merges the input modules and assigns run-time addresses to eachsymbol
    - 두 개의 object module을 병합하여 실제 각각의 symbol에 run time address를 부여해주는 작업
- Moe details will come in the next slides...
- relocation
    - 내가 penn university에서 졸업하고 job을 구하면 relocate : relocate negotiation (주소 바뀌는 것에 대한 signing bonus 등) - 내가 penn university에 있다가 다른 회사 사무소 주소로 바귐
    - 내가 현재 접근하는 변수의 주소를 모르기 때문에 relocate하는 과정 : 주소 찾아서 assign해줌
1. Symbol resolution 복습
    - linker가 symbol resolution step에 따라 resolve하게 되면 모든 symbol reference는 오직 한개의 symbol definition에 associate된다
        - 만일 그렇지 않다면 link error
    - obj file 생성하게 되면 code, data에 대한 정확한 정보를 알게 됨
2. relocation step
    - ₩main.c , .c₩ : 두 개의 object module을 병합하여 실제 각각의 symbol에 run time address를 부여해주는 작업
    ![Untitled](12/Untitled_14.png)
    - relocation : 이 세 가지를 하나로 병합하는 과정
- ELF File Format
    - `.Text / .Data`
    - 각 symbol에다 address assign하는 것에 대해 알아보자
    - relocation : 이 세 가지를 하나로 병합하는 과정
    - ELF File Format
    - .Text / .Data : 각 symbol에다 address assign하는 것에 대해 알아보자

## Relocation Entries

- object module : Compiler 내 assembler가 기계어 code를 만들어 내는데 이를 obj module
- When an assembler generates an object module, it does not know where the code and data will ultimately be stored in memory.: 이를 생성해 낼 때, 1. code, data가 나중에 executible file 생성하는 시점에서 memory 어디에 적재될지 모름
- Nor does it know the location of any externally defined functions of global variables that are referenced by the module.
    - 2. main.c에서 sum이라는 함수는 외부에서 externally defined : sum이라는 함수를 호출할 때 그 위치가 어디인지 모른다.
- So, whenever the assembler encounters a reference to an object whose ultimate location is unknown, it generates a relocation entry that tells the linker how to modify the reference when it merges the object file into an executable.
    - → 결국에는 location 어디인지 모르는 (global var, external function)은 알려져 있지 않기 때문에 relocation entry라고 해서 하나씩 만든다.
    - ELF file format의 .rel.text / .rel.data에 들어간다
    - Linker에게 ‘이 주소를 잘 모르니까 나중에 다른 obj와 merge되어 executable file만들 때 이 reference를 수정해야 한다’고 알려줌
        - .rel을 보고 symbol들이 address가 확인되지 않음을 보고 linking 과정에서 병합하며 그 address를 채워준다
    ```c
    int array[2] = {1, 2};
    int main()
    {
        int val = sum(array, 2);
        return val;
    }
    main.c0000000000000000
    <main> : 0 : 484 : be9 : bfe :
    e813 : 48 17 : c383ec08 020000 00000000 00 00 83c40800 0000sub $0x8, % rspmov $0x2, % esimov $0x0, % edi # % edi = &arraya : R_X86_64_32 array #Relocation entrycallq 13 < main + 0x13 > #sum() f : R_X86_64_PC32 sum - 0x4 #Relocation entryadd $0x8, % rspretqmain.oRelocation EntriesSource : objdump –r –d main.o33
    ```
- Relocation entries for code are placed in .rel.text.
- Relocation entries for data are placed in .rel.data. 32

## Relocated .text section

![Untitled](12/Untitled_15.png)

- Relocation: 앞의 obj module들을 병합한 다음에 각 symbol에 대해 runtime address
    - (Vm address O, not physical) E8 05 00 00 00
    - instruction 수행할 때 relative
    - 현재 주소 :  PC는 항상 다음 주소 (명령어 fetch하는 그 순간에) PC가 가리키는 주소에 5 더하면 <sum>의 주소가 나온다 :
    - 상대적인 주소 : detail은 assembler, linking / 2 path scanning을 하기 때문에

# Loading Executable Object Files

![Untitled](12/Untitled_16.png)

- ELF 보면 되고 process address space : 0x400000~2^48 -1 (process)
    - kernel
    - shaerd library : 동적으로 linking 만들어지는 library, API memory map을 위함
    - Heap : break point만큼 heap의 크기
    - segment : r/w data
    - Shared memory 하나 올라오고 Application a, b 뜰 때 필요에 따라 reference함 
        - (그냥 copy하는게 아님) 
        - c가 shared library라고 할 때 ptr로 그냥 갈 수 있기 때문에 c는 하나만 있으면 되어 duplicate하지 않아도 됨
    - memory mapped region에 대한 ptr가 들어가있는 것.

## Packaging Commonly Used Functions

- How to package functions commonly used by programmers?
    - 자주 쓰는 함수 모아둠  - library 형태로 모아 둠
- Math, I/O, memory management, string manipulation, etc.- Awkward, given the linker framework so far:
    - memory management : malloc, calloc 등 모아서 자주 쓰니까 어떻게 하나의 패키지로 쓰는가
- Option 1: Put all functions into a single source file
    1. 모든 함수들을 코드들에 집어넣음
    - Programmers link big object file into their programs
    - Space and time inefficient
        - 생성되는 executable code 자체가 많이 커짐 → 시간, 공간 많이 차지함
- Option 2: Put each function in a separate source file
    2. modular 접근 방법처럼 기능 별로 file들을 쪼갠다
    - 각 file들에는 특정 기능 수행하는 fn들을 따로 따로 만들어 줌
    - 필요한 것만 링킹 - 모든 소스 파일을 다 집어넣을 필요 없음
    - Programmers explicitly link appropriate binaries into their programs
    - More efficient, but burdensome on the programmer36
        - 효율적이지만 프로그래머가 이해해주어야 함

## Old-fashioned Solution: Static Libraries

- Static libraries (.a archive files)
- Concatenate related relocatable object files into a single file with an index (called an archive).
- Enhance linker so that it tries to resolve unresolved external references by looking for the symbols in one or more archives.
- If an archive member file resolves reference, link it into the executable.
- static linking - static library
- archive file
    - Object files 들을 concatenate해서 하나의 file로 만듬
    - index를 통해 archive 안에 sum, average 등의 함수를 찾아서 참조할 수 있도록 함
- archive 안에서 symbol 찾아보고 나서 실행 file linking

## Creating Static Libraries

> 함수별로 모듈만들어 하나의 archive로 만들어 static link 만들 수 있다.
![Untitled](12/Untitled_17.png)
- Archiver allows incremental updates-
- Recompile function that changes and replace .o file in archive.38

## Commonly Used Libraries
- `libc.a` (the C standard library)
    - 4.6 MB archive of 1496 object files.
- I/O, memory allocation, signal handling, string handling, data and time, random numbers, integer mathlibm.a (the C math library)
    - 2 MB archive of 444 object files.
![Untitled](12/Untitled_18.png)

# Linking with Static Libraries

- libvector.a

    ```c
    #include <stdio.h>
    #include "vector.h"
    int x[2] = {1, 2};
    int y[2] = {3, 4};
    int z[2];
    int main()
    {
        addvec(x, y, z, 2);
        printf("z = [%d %d]\n", z[0], z[1]);
        return 0;
    }

    main2.c40
    void addvec(int *x, int *y, int *z, int n)
    {
        int i;
        for (i = 0; i < n; i++)
    }
    z[i] = x[i] + y[i];
    addvec.c
    void multvec(int *x, int *y, int *z, int n)
    {
        int i;
        for (i = 0; i < n; i++)
            z[i] = x[i] * y[i];
    }
    ```
- 41page - addvec.o는 무엇을 의미하는가:
    - Main 함수에서 사용하는 addvec
    - 다 올리는 게 아니라 archive라는 utility를 통해서 object concatenate하는 건데 addvec만 사용하기에 이거만 빼서 link한다는 의미이다.
- static link : 사용되는 object file 100개를 모아두고 archive처럼 code generation할 때 다 모아서 symbol reolution하고 link하는 것임.
    - static linking에서 duplication생기는 이유
    - 똑같이 main함수 생성해서 addvec(x,y,z, 3)을 선언해서 넣었다고 하자.
    이 코드들이 생성될 때 addvec.o가 proc2에 포함되어 있다.
    나중에 또 proc3을 생성할 때에도 addvec.o가 들어가 있다.

## Using Static Libraries

### Linker’s algorithm for resolving external references:

- symbol resolving algorithm : linker가 사용하는 algorithm
- Scan `.o` files and `.a` files in the command line order.
- During the scan, keep a list of the current unresolved references.
- As each new `.o` or `.a` file, obj, is encountered, try to resolve each unresolved reference in the list against the symbols defined in obj.
- If any entries in the unresolved list at end of scan, then error.

### Problem:

![Untitled](12/Untitled_19.png)

- Command line order matters!
- Moral: put libraries at the end of the command line.unix> gcc -L. libtest.o -lmineunix> gcc -L. -lmine libtest.olibtest.o: In function main': libtest.o(.text+0x4): undefined reference to libfun'42
- Compiler를 통해 example - Gcc -L : object file에서 library에 있는 file을 찾는 것
1. Test file + library : test file 안에 선언되어 있는 걸 external reference해서 없으면 library를 찾아본다
    - Test라는 file obj module안에서 없으면 library안에 있으면 해결되기 때문에
2. library + test file 넣으면 error가 뜨게 됨. (reference 안되어 있음)
    - -lmine에는 있지만 libtest.o에는 없어서 그 다음으로 넘어가야 하는데 그 다음 file이 존재하지 않으므로 error 발생
    - command line 순서대로 수행 * order가 되게 중요 → library는 맨 뒤
    - scanning하면서 현재 resolve 안된 reference 기록
    그리고 각각에 대해 unresolved reference를 뒤에서 찾아본다 : 있으면 resolve, 없으면 error

# Modern Solution: Shared Libraries

### Static libraries have the following disadvantages:

- dynamic library = shared library
- Compile time때 static linking을 통한 linking
    - 지금 돌아가는 executable file이라든지 둘 다 library를 가져다 사용하게 되면 static하게 linking했기 때문에 static이던 running중이던 동일한 file을 본인의 executable에 포함 → 중복된 contents
- Duplication in the stored executables (every function needs libc)
    - Duplication in the running executables
    - Minor bug fixes of system libraries require each application to explicitly relink
        - bug 수정 :application 수정하고 linking 다시 해야 함

### Modern solution: Shared Libraries

- Object files that contain code and data that are loaded and linked
해당 library 부분만 수정해서 되는게 아니라 application을 relink시켜 또 다시 link해야 함
- into an application dynamically, at either load-time or run-time
    - load time, runtime에 동적으로 shared library가 linking됨
- Also called: dynamic link libraries, DLLs, .so files
- dynamic library = shared library
    - Compile time때 static linking을 통한 linking
    - 지금 돌아가는 executable file이라든지 둘 다 library를 가져다 사용하게 되면 static하게 linking했기 때문에 static이던 running중이던 동일한 file을 본인의 executable에 포함 → 중복된 contents
    - bug 수정 :application 수정하고 linking 다시 해야 함
    - 해당 library 부분만 수정해서 되는게 아니라 application을 relink시켜 또 다시 link해야 함
    - shared: load time, runtime에 동적으로 shared library가 linking됨
- Library를 여러개 dynamically 사용할 수 있어서 shared
- Loading time, rumtime이 될 수 있음

### Dynamic linking can occur when executable is first loaded and run (load-time linking).

- Common case for Linux, handled automatically by the dynamic linker ([ld-linux.so](http://ld-linux.so/)).
- Standard C library ([libc.so](http://libc.so/)) usually dynamically linked.

### Dynamic linking can also occur after program has begun (run-time linking).

- In Linux, this is done by calls to the dlopen() interface.
- Distributing software.
- High-performance web servers. Runtime library interpositioning.

### Shared library routines can be shared by multiple processes.

- More on this when we learn about virtual memory 44

## Dynamic Linking at Load-time

![Untitled](12/Untitled_20.png)

- static link : addvec, multvec library 모두 main2.o에 concatenate했어야 했음
- dynamic link : 사용되는 function 중 main2가 reference하겠다는 note정도만 partly 저장
- 그리고 나서 실제 program 실행할 때 library 해당하는 external reference 되어 있는 name들을 resolve하면서 나중에 loading할 때 dynamic linker에 의해 memory에 올라갈 때 비로소 shared library가 이어짐.
> (둘 다 memory에 load되어 있을 때)

## Dynamic Linking at Run-time

- dlopen을 사용해서 shaerd library 선언하고 dlsym을 통해 addvec을 pointer로 넘거받음
    - 그리고 그 함수를 가지고 원래 있는 함수처럼 사용
    - code 실행할 때 linking하겠다 : runtime
    - 필요에 따라 memory에 그 때 그때 옮긴다

    ```c
    #include <stdio.h>
    #include <stdlib.h>
    #include <dlfcn.h>
    int x[2] = {1, 2};
    int y[2] = {3, 4};
    int z[2];
    int main()
    {
        void *handle;
        void (*addvec)(int *, int *, int *, int);
        char error;
        /* Dynamically load the shared library that contains addvec() */
        handle = dlopen("./libvector.so", RTLD_LAZY);
        if (!handle)
        {
            fprintf(stderr, "%s\n", dlerror());
            exit(1);
        }

        /* Get a pointer to the addvec() function we just loaded */
        addvec = dlsym(handle, "addvec");
        if ((error = dlerror()) != NULL)
        {
            fprintf(stderr, "%s\n", error);
            exit(1);
        }
        /* Now we can call addvec() just like any other function */
        addvec(x, y, z, 2);
        printf("z = [%d %d]\n", z[0], z[1]);
        /* Unload the shared library */ 
        
        if (dlclose(handle) < 0)
        {
            fprintf(stderr, "%s\n", dlerror());
            exit(1);
        }
        return 0;
    }
    ```

## Linking Summary

- Linking is a technique that allows programs to be constructed from multiple object files.-
    - 여러 개의 object로 하나의 file을 만들 때 사용됨
- Linking can happen at different times in a program’s lifetime:
언제 하느냐에 따라 /방법에 따라 여러 많은 error들을 확인하여 resolve할 수 있음
    - 실제 memory 사용량, 실행 시간 등의 장단점을 구분할 수 있음
    - Compile time (when a program is compiled)
    - Load time (when a program is loaded into memory)
    - Run time (while a program is executing)-
- Understanding linking can help you avoid nasty errors and make you a better programmer.

# 7.13. Case study: Library interpositioning

- **Library interpositioning : powerful linking technique that allows programmers to intercept calls to arbitrary functions**
- **Interpositioning can occur at:**
    - Compile time: When the source code is compiled
    - Link time: When the relocatable object files are statically linked to form an executable object file
    - Load/run time: When an executable object file is loaded into memory, dynamically linked, and then executed.

## **Some Interpositioning Applications**

- Security
    - Confinement (sandboxing)
    - Behind the scenes encryption
- Debugging
    - In 2014, two Facebook engineers debugged a treacherous 1-year old bug in their iPhone app using interpositioning
    - Code in the SPDY networking stack was writing to the wrong location
    - Solved by intercepting calls to Posix write functions (`write, writev, pwrite`)
    - Source:  Facebook engineering blog post at https://code.facebook.com/posts/313033472212144/debugging-file-corruption-on-ios/
- Monitoring and Profiling
    - Count number of calls to functions
    - Characterize call sites and arguments to functions
    - Malloc tracing
        - Detecting memory leaks
        - Generating address traces52
> Developer가 기존의 function들을 본인의 function으로 intercept하여 실행하고 다시 돌아올 수 있도록 하는 기법
1. Compile time 
    - src code compile 될 때 interpositioning. Malloc함수 자체를 내가 짠 코드로 interpositioning
2. Link time 
    - executable file 만들 때
3. Load, run time 
    - obj file이 load될 때 동적 link

## Example program
- 32byte 할당하여 tracking
    - compile time에 mymalloc, myfree로 대치
- 지금 배우고 있는 interposition technique
    - compile, link, loadtime 때 할 수 있다.

    ```c
    #include <stdio.h>
    #include <malloc.h>

    int main()
    {
        int *p = malloc(32);
        free(p);
        return(0);
    }
    ```

- Goal: trace the addresses and sizes of the allocated and freed blocks, without breaking the program, and without modifying the source code.
- Three solutions: interpose on the lib malloc and free functions at compile time, link time, and load/run time.

# Compile-time Interpositioning

    ```cpp
    #ifdef COMPILETIME
    #include <stdio.h>
    #include <malloc.h>

    /* malloc wrapper function */
    void *mymalloc(size_t size)
    {
        void *ptr = malloc(size);
        printf("malloc(%d)=%p\n",
            (int)size, ptr);
        return ptr;
    }

    /* free wrapper function */
    void myfree(void *ptr)
    {
        free(ptr);
        printf("free(%p)\n", ptr);
    }
    #endif 
    #define malloc(size) mymalloc(size)
    #define free(ptr) myfree(ptr)

    void *mymalloc(size_t size);
    void myfree(void *ptr);
    ```

    ```cpp
    linux> make intc
    gcc -Wall -DCOMPILETIME -c mymalloc.c
    gcc -Wall -I. -o intc int.c mymalloc.o
    linux> make runc
    ./intc
    malloc(32)=0x1edc010
    free(0x1edc010)
    linux>
    ```

- mymalloc code
- C library에서 제공해 주는 malloc을 사용한 후 그 다음에 무엇을 할 것이냐 : 기존 c library에서 제공하는 malloc을 사용하는 게 아니라 내가 작성한 mymalloc, myfree로 replace (@compile time) 
    - -> 즉, 현재 mymalloc은 기존의 malloc의 wrapper함수처럼 쓰여지는데 앞에 있던 코드에서 malloc을 compile time때 mymalloc/myfree으로 대치되어 실행하도록 할 수 있다.
- mymalloc/myfree trace하기 위해서 printf code
    - 기존에는 malloc에다 ptr을 가져다가 안에서 mymalloc으로 대치함으로써 실제 malloc을 내부적으로 call하지만 tracing위한 printf 삽입하여 - 어떤 block이 alloc/free되었는지 확인
- preprocessing
    - compilie할 때 내부의 malloc이 mymalloc / free가 myfree로 바뀌어 구동되게 된다.
- (linux print 결과)
    - int.c, mymalloc.c 같이 compile
    - 원래는 아무것도 print하지 않는데 mymalloc, myfree를 통해서 원하는 정보를 tracing할 수 있게 됨. -> 이 주소에다가 우리가 allocate했음을 알고 vm주소를 deallocate했음을 확인할 수 있음

# Link-time Interpositioning

- linktime 때 하는 경우 
    - → object를 만들고 Wl option을 주고 `—wrap, malloc/free`

    ```cpp
    #ifdef LINKTIME
    #include <stdio.h>

    void *__real_malloc(size_t size);
    void __real_free(void *ptr);

    /* malloc wrapper function */
    void *__wrap_malloc(size_t size)
    {
        void *ptr = __real_malloc(size); /* Call libc malloc */
        printf("malloc(%d) = %p\n", (int)size, ptr);
        return ptr;
    }

    /* free wrapper function */
    void __wrap_free(void *ptr)
    {
        __real_free(ptr); /* Call libc free */
        printf("free(%p)\n", ptr);
    }
    #endif
    ```

    ```cpp
    linux> make intl
    gcc -Wall -DLINKTIME -c mymalloc.c
    gcc -Wall -c int.c
    gcc -Wall -Wl,--wrap,malloc -Wl,--wrap,free -o intl int.o mymalloc.o
    linux> make runl
    ./intl
    malloc(32) = 0x1aa0010
    free(0x1aa0010)
    linux>
    ```

- **The `-Wl` flag passes argument to linker, replacing each comma with a space.**
    - `-wl` flag 자체가 argument를 linker에게 넘겨주어 각각의 comma로 되어 있는 것을 space로 바꾸어 대치하라는 command
- **The  `--wrap_malloc` arg instructs linker to resolve references in a special way:**
    - 나중에 —wrap,malloc/free를 gcc compiler에게 넘겨줌으로서 linker에 이야기 : reference되어 있는데 Linker에게 gcc compilier가 이런 식으로 resolve하라고 만든다.
        - malloc → `wrap malloc`
        - `Real malloc` → malloc
    - Refs to malloc should be resolved as __wrap_malloc
    - Refs to   __real_malloc should be resolved as malloc
- → link time에 그 값을 가져다 reserve한 다음 원하는 대로 interposition

# **Load/Run-time Interpositioning**

```cpp
#ifdef RUNTIME
#define _GNU_SOURCE
#include <stdio.h>
#include <stdlib.h>
#include <dlfcn.h>

/* malloc wrapper function */
void *malloc(size_t size)
{
    void *(*mallocp)(size_t size);
    char *error;

    mallocp = dlsym(RTLD_NEXT, "malloc"); /* Get addr of libc malloc */
    if ((error = dlerror()) != NULL) {
        fputs(error, stderr);
        exit(1);
    }
    char *ptr = mallocp(size); /* Call libc malloc */
    printf("malloc(%d) = %p\n", (int)size, ptr);
    return ptr;
}
/* free wrapper function */
void free(void *ptr)
{
    void (*freep)(void *) = NULL;
    char *error;

    if (!ptr)
        return;

    freep = dlsym(RTLD_NEXT, "free"); /* Get address of libc free */
    if ((error = dlerror()) != NULL) {
        fputs(error, stderr);
        exit(1);
    }
    freep(ptr); /* Call libc free */
    printf("free(%p)\n", ptr);
}
#endif
```
> Load/runtime interpositioning
- dlsym : Library malloc의 address를 받아오기 위함
- mallocp(size) : malloc이라는 wrapper 함수와 free라는 wrapper 함수가 있는데 나중에 동적으로 malloc이라는 wrapper함수에서 동적으로 malloc이 호출되는 그 순간에 malloc의 ptr address를 return하고 이를 가지고 print하게 하는 방법
- 동적으로 interpositioning을 malloc./ free
    - 동일하게 free함수 호출했을 때 address를 받아오고 free를 한다음 printf를 통해 ptr값에 해당하는 heap에 있는 address 공간을 free
- The `LD_PRELOAD` environment variable tells the dynamic linker to resolve unresolved refs (e.g., to malloc) by looking in mymalloc.so first.
    - ‘LD Preload 환경 변수’를 통해 내가 찾고자 하는 함수를 code 안에서 찾을 수 있음.
    - 순차적으로 찾고 동적으로 실행되는 그 때, 앞에 print하고자 하는 value들과 size를 print하고 free하며 끝남
    - 환경 변수가 dynamic link에게 mymalloc.so file (shared library)를 찾아보고 malloc으로 대체할 수 있도록 하는 역할을 수행함

    ```cpp
    linux> make intr
    gcc -Wall -DRUNTIME -shared -fpic -o mymalloc.so mymalloc.c -ldl
    gcc -Wall -o intr int.c
    linux> make runr
    (LD_PRELOAD="./mymalloc.so" ./intr)
    malloc(32) = 0xe60010
    free(0xe60010)
    linux>
    ```

# **Interpositioning Recap**
- **Compile Time**
    - compile time : macro 확장처럼 mymalloc
    - Apparent calls to malloc/free get macro-expanded into calls to mymalloc/myfree
- **Link Time**
    - link time : linker에게 trick을 불러 이름 resolution을 바꾸어 준다
    - Use linker trick to have special name resolutions
    - malloc → `__wrap_malloc`
    - `__real_malloc` → malloc
- **Load/Run Time**
    - load/run time : dynamic linking을 통해서 실제 malloc, free를 다른 이름으로 load할 때 대치하게 함으로서 원하는 interpositioning을 수행할 수 있음
    - Implement custom version of malloc/free that use dynamic linking to load library malloc/free under different names
- → 세 가지 방법 중 load/runtime, compile time때 많이 수행. 물론 compile의 경우 preprocess 단계에서 내 것으로 가로채기 할 수 있으나 다 같이 compile해야 하는 문제점
- load/runtime의 경우에는 이에 반해 따로 compile할 필요 없고, so file을 찾도록 하여 내가 작성한 함수로 interpositioning 기술을 적용할 수 있다