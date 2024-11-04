원본 노션 링크: https://pushfurther.notion.site/Week1-3-133448b869888041a7b3d3f243f359ac?pvs=4 

### Objectives

어셈블리 언어

x86-64 어셈블리 명령어

---

### ✔️**x64 어셈블리 언어**

동사에 해당하는 **명령어(Operation Code, Opcode)**와 목적어에 해당하는 **피연산자(Operand)**로 구성

- **명령어**

| 명령 코드 |  |
| --- | --- |
| 데이터 이동(Data Transfer) | `mov`, `lea` |
| 산술 연산(Arithmetic) | `inc`, `dec`, `add`, `sub` |
| 논리 연산(Logical) | `and`, `or`, `xor`, `not` |
| 비교(Comparison) | `cmp`, `test` |
| 분기(Branch) | `jmp`, `je`, `jg` |
| 스택(Stack) | `push`, `pop` |
| 프로시져(Procedure) | `call`, `ret`, `leave` |
| 시스템 콜(System call) | `syscall` |
- **피연산자**
    - 상수(Immediate Value)
    - 레지스터(Register)
    - 메모리(Memory)

메모리 피연산자는 []으로 둘러싸인 것으로 표현되며, 앞에 크기 지정자(Size Directive) TYPE PTR이 추가될 수 있음. 여기서 타입에는 BYTE, WORD, DWORD, QWORD가 올 수 있으며, 각각 1바이트, 2바이트, 4바이트, 8바이트의 크기를 지정.

### ✔️**x86-64 어셈블리 명령어**

- **데이터 이동 :** 데이터 이동 명령어는 어떤 값을 레지스터나 메모리에 옮기도록 지시
    - **mov dst, src : src에 들어있는 값을 dst에 대입**
    
    | mov rdi, rsi | rsi의 값을 rdi에 대입 |
    | --- | --- |
    | mov QWORD PTR[rdi], rsi | rsi의 값을 rdi가 가리키는 주소에 대입 |
    | mov QWORD PTR[rdi+8*rcx], rsi | rsi의 값을 rdi+8*rcx가 가리키는 주소에 대입 |
    - **lea dst, src : src의 유효 주소(Effective Address, EA)를 dst에 저장**
    
    | lea rsi, [rbx+8*rcx] | rbx+8*rcx 를 rsi에 대입 |
    | --- | --- |
- **산술 연산 :** 산술 연산 명령어는 덧셈, 뺄셈, 곱셈, 나눗셈 연산을 지시
    - **add dst, src : dst에 src의 값을 더함**
    
    | add eax, 3 | eax += 3 |
    | --- | --- |
    | add ax, WORD PTR[rdi] | ax += *(WORD *)rdi |
    - **sub dst, src: dst에서 src의 값을 뺌**
    
    | sub eax, 3 | eax -= 3 |
    | --- | --- |
    | sub ax, WORD PTR[rdi] | ax -= *(WORD *)rdi |
    - **inc op: op의 값을 1 증가 시킴**
    
    | inc eax | eax += 1 |
    | --- | --- |
    - **dec op: op의 값을 1 감소 시킴**
    
    | dec eax | eax -= 1 |
    | --- | --- |
- **논리 연산 :** 논리 연산 명령어는 and, or, xor, neg 등의 비트 연산을 지시. 이 연산은 비트 단위로 이루어 짐.
    - **and dst, src: dst와 src의 비트가 모두 1이면 1, 아니면 0**
    
    ```nasm
    [Register]
    eax = 0xffff0000
    ebx = 0xcafebabe
    
    [Code]
    and eax, ebx
    
    [Result]
    eax = 0xcafe0000
    ```
    
    - **or dst, src: dst와 src의 비트 중 하나라도 1이면 1, 아니면 0**
    
    ```nasm
    [Register]
    eax = 0xffff0000
    ebx = 0xcafebabe
    
    [Code]
    or eax, ebx
    
    [Result]
    eax = 0xffffbabe
    ```
    
    - **xor dst, src: dst와 src의 비트가 서로 다르면 1, 같으면 0**
    
    ```nasm
    [Register]
    eax = 0xffffffff
    ebx = 0xcafebabe
    
    [Code]
    xor eax, ebx
    
    [Result]
    eax = 0x35014541
    ```
    
    - **not op: op의 비트 전부 반전**
    
    ```nasm
    [Register]
    eax = 0xffffffff
    
    [Code]
    not eax
    
    [Result]
    eax = 0x00000000
    ```
    
- **비교 :** 비교 명령어는 두 피연산자의 값을 비교하고, 플래그를 설정
    - **cmp op1, op2: op1과 op2를 비교**
    
    ```nasm
    [Code]
    1: mov rax, 0xA
    2: mov rbx, 0xA
    3: cmp rax, rbx ; ZF=1
    ```
    
    - **test op1, op2: op1과 op2를 비교**
    
    ```nasm
    [Code]
    1: xor rax, rax
    2: test rax, rax ; ZF=1
    ```
    
- **분기 :** 분기 명령어는 `rip`를 이동시켜 실행 흐름을 바꿈
    - **jmp addr: addr로 rip를 이동시킴**
    
    ```nasm
    [Code]
    1: xor rax, rax
    2: jmp 1 ; jump to 1
    ```
    
    - **je addr: 직전에 비교한 두 피연산자가 같으면 점프 (jump if equal)**
    
    ```nasm
    [Code]
    1: mov rax, 0xcafebabe
    2: mov rbx, 0xcafebabe
    3: cmp rax, rbx ; rax == rbx
    4: je 1 ; jump to 1
    ```
    
    - **jg addr: 직전에 비교한 두 연산자 중 전자가 더 크면 점프 (jump if greater)**
    
    ```nasm
    [Code]
    1: mov rax, 0x31337
    2: mov rbx, 0x13337
    3: cmp rax, rbx ; rax > rbx
    4: jg 1  ; jump to 1
    ```
    
- **스택**
    - **push val : val을 스택 최상단에 쌓음**
    
    ```nasm
    rsp -= 8
    [rsp] = val
    ```
    
    - **pop reg : 스택 최상단의 값을 꺼내서 reg에 대입**
    
    ```nasm
    rsp += 8
    reg = [rsp-8]
    ```
    
- **프로시저**
    - **call addr : addr에 위치한 프로시져 호출**
    
    ```nasm
    push return_address
    jmp addr
    ```
    
    - **leave: 스택프레임 정리**
    
    ```nasm
    mov rsp, rbp
    pop rbp
    ```
    
    - **ret : return address로 반환**
    
    ```nasm
    pop rip
    ```
    
- **시스템 콜**
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/3e6507f6-9180-4db1-b0aa-a71b098133d7/1f647151-d8b3-43a4-804d-b29949eedc3a/image.png)
    
    - **커널 모드 :** 운영체제가 전체 시스템을 제어하기 위해 시스템 소프트웨어에 부여하는 권한. 파일시스템, 입력/출력, 네트워크 통신, 메모리 관리 등 모든 저수준의 작업은 사용자 모르게 커널 모드에서 진행. 커널 모드에서는 시스템의 모든 부분을 제어할 수 있기 때문에, 해커가 커널 모드까지 진입하게 되면 시스템은 거의 무방비 상태가 됨.
    - **유저 모드 :** 운영체제가 사용자에게 부여하는 권한. 브라우저를 이용하여 드림핵을 보거나, 유튜브를 시청하는 것, 게임을 하고 프로그래밍을 하는 것 등은 모두 유저 모드에서 이루어짐. 리눅스에서 루트 권한으로 사용자를 추가하고, 패키지를 내려 받는 행위 등도 마찬가지. 유저 모드에서는 해킹이 발생해도, 해커가 유저 모드의 권한까지 밖에 획득하지 못하기 때문에 해커로 부터 커널의 막강한 권한을 보호 가능.
    - **시스템 콜(system call, syscall) :** 유저 모드에서 커널 모드의 시스템 소프트웨어에게 어떤 동작을 요청하기 위해 사용. 유저 모드에서는 이를 직접 할 수 없으므로 커널이 **도움**을 줘야함. 여기서, 도움이 필요하다는 요청이 시스템 콜. 유저 모드의 소프트웨어가 필요한 도움을 요청하면, 커널이 요청한 동작을 수행하여 유저에게 결과를 반환.
        - x64아키텍처에서는 시스템콜을 위해 `syscall` 명령어 존재
            
            ```nasm
            요청: rax
            
            인자 순서: rdi -> rsi -> rdx -> rcx -> r8 -> r9 -> stack
            ```
            
    - **x64 syscall 테이블**
