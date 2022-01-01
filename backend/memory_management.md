# Memory Management

## Main Momory

### Background

- Process가 실행되기 위해서는 프로그램이 반드시 메모리에 적재돼야 함.
- Main memory와 registers는 CPU가 유일하게 직접 접근할 수 있는 storage.
    - Registers는 1 CPU Clock에 접근 가능함.
    - Main memory의 접근에는 많은 CPU cycles가 소요될 수 있음. -> [processor stall](https://en.wikipedia.org/wiki/Pipeline_stall)
    - Cache는 CPU와 main memory 사이에 추가됨.
- 정확한 연산을 보장하기 위해 memory 보호가 요구됨.
    - 이 보호는 hardware에 의해 제공됨.

#### Basic Hardware

##### Memory Management Requirement

- Protection
    - 다른 processes로부터의 원하지 않는 간섭으로부터 보호.
- Relocation
    - multiprogramming
    - swapping
- Sharing
- Local organization
    - memory: linear organization
    - program: module organization, different protection per module
- Physical Organization
    - multiple level memory hierarchy
        - fast/high cost/volatile vs. slow/low cost/non-volatile
    - moving information between two levels of memory
        - programmer's job -> overlay
        - OS's job: in modern OS

##### Protection - Base and Limit Registers

![image](https://user-images.githubusercontent.com/9735030/146662042-1f3c0554-8039-4688-8892-7bc30afef367.png)

- 각각의 Processes들이 분리된 memory 공간을 가지도록 보장해야 함.
- Base와 limit registers의 쌍은 logical address space를 정의함.

##### Hardware Address Protection with Base/Limit Registers

- Base와 limit registers는, 특별한 privileged instruction을 사용하는 OS에 의해서만 로드될 수 있음.
- User mode에서 프로그램을 실행하여 OS나 다른 users의 memory에 엑세스할 수 없음.

#### Address Binding

- 일반적으로, Instructions와 data를 memory addresses에 binding하는 것은 모든 스텝에서 수행할 수 있음:
    - Compile time: Compile time에 process가 memory에 상주할 위치를 알고 있으면 absolute code가 생성될 수 있음. 시작 위치가 바뀌면 recompile해야 함. (static binding)
    - Load time: Process가 memory에 상주할 위치를 Compile time에 알 수 없는 경우, Compiler는 relocatable code를 생성해야 함. Binding이 load time까지 지연됨. (static binding)
    - Execution time: Process가 실행 중에 한 memory segment에서 다른 memory segment로 이동할 수 있는 경우, binding은 runtime까지 지연됨. (dynamic binding)
        - Address maps에 대한 HW의 지원이 필요함. -> [MMU](https://en.wikipedia.org/wiki/Memory_management_unit)
        - 대부분의 범용 OS는 이 방법을 사용함.

#### Logical Versus Physical Address Space

- Logical vs. physical
    - Logical address (virtual address): 프로그램이 보는 address.
    - Physical address: Memory unit이 보는 address.
- Memory management unit (MMU)
    - Logical address로부터 physical address로의 runtime mapping을 수행.
    - 프로그램은 logical address를 다루며 실제 physical address는 절대 볼 수 없음.

##### Dynamic relocation using a relocation register

#### Dynamic Loading

- Dynamic loading: Routine이 호출될 때까지 로드되지 않음.
- 이점:
    - 더 나은 memory 공간 활용. 사용되지 않는 routine은 절대 로드되지 않음.
    - 드물게 발생하는 경우를 처리하기 위해 많은 양의 code가 필요할 때 유용
- OS의 특별한 지원이 필요하지 않음.
    - Program을 설계하는 사용자의 책임.
    - OS는, 구현을 위해 library routines를 제공할 수 있음.

#### Dynamic Linking and Shared Libraries

- Static vs. dynamic
    - Static linking: Loader에 의해 executable image에 link됨.
    - Dynamic linking: Execution time까지 linking이 연기됨.
