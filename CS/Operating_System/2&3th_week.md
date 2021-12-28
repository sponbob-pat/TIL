처음에는 Batch(일괄처리) 방식으로 프로그램들을 처리했다. 이는 단순하게 `일단 시작한 job(작업)을 끝내고 다음 job(작업)을 수행하는 방식`이다.

옛날에는 Punch Card를 제출하는 방식으로 프로그램을 수행했기 때문에 결과를 받기까지 중간에 사람이 개입할 수 없었다. 

그래서 제출한 Punch Card의 내용을 다 수행하고 나서야 다른 작업을 수행할 수 있었다. 

이 때문에 2가지 문제점이 발생한다.

1) CPU가 빈번한 idle 상태로 전환된다 = CPU가 프로그램을 작업하는 중간중간에 `노는 시간`이 발생한다.

==> 이는 `I/O 장치에서 입출력하는 시간`이 `CPU의 계산 속도`보다 훨씬 느리기 때문이다.

2) Job의 전환이 오래걸린다.

==> Punch card를 이용해서 입력을 하다보니 입력하는 시간동안 mainframe은 아무것도 안하고 노는 상황이 빈번하다.

그렇다면 각각의 문제들을 어떻게 해결했을까?? 

1)의 해결방안 - Spooling(Simultaneous Peripheral Operation On-Line)
                : I/O가 동작함과 동시에 CPU에서 작업을 수행할 수 있도록 하는 방식
                
ex. 프린터 spooling 

==> 인쇄할 문서를 디스크나 메모리의 버퍼에 로드(load)한다.

==> 프린터는 버퍼에서 자신의 처리 속도로 인쇄할 데이터를 가져오고 그러는 동안 CPU는 다른 작업을 수행할 수 있게 된다.

(물론 기본적으로 여러 개의 작업을 저장할 수 있을 정도로 메모리의 용량이 많이 늘어난 상황이다)

이러한 Spooling을 통해서 사용자는 여러 개의 인쇄작업을 프린터에 순차적으로 요청할 수 있다. 

즉, 이전 작업의 종료를 기다리지 않고, CPU는 버퍼에다가 인쇄작업을 로드해 놓고나서 자기가 할 작업을 하면 된다.
                
![image](https://user-images.githubusercontent.com/64796257/147423395-78ff0c8c-5a01-4255-953c-185a325222e2.png)

이러한 작업은 `Asynchronous 작업`(각각의 작업이 서로 독립적인 작업)에 적합하다.

하지만, CPU의 작업이 I/O 작업에 의존적이라면 스풀링 자체로는 해결할 수는 없다. 이는 입출력의 속도 차원의 문제가 된다.

ex. ![image](https://user-images.githubusercontent.com/64796257/147423547-57dcca3b-d884-41f0-bd52-2b768286f350.png)

2)의 해결방안

Punch card를 이용해서 사람이 직접 입력을 하다보니 입력 동작을 `자동으로` 처리하고 싶어졌다. 이전 작업을 끝내고 나서 곧바로 다음 작업을 실행하려면

이전 작업이 끝나자마자 다음 작업이 곧바로 메모리에 이동해야 할 텐데 이를 위해서는 메모리에 2개 이상의 작업이 들어있어야 한다.

옛날에는 메모리가 하나의 작업만 저장할 수 있었지만 시간이 지나면서 용량과 관련된 문제는 해소되었다. 그렇다면, 어떻게 동작했을까??

바로, `Multi-programming`'이다. 

==> 운영체제가 여러 개의 작업을 메모리에 동시에 유지하면서 현재 실행중인 작업이 I/O일 경우 메모리에 저장되어 있던 바로 다음 작업을 순차적으로 실행하는 방식

==> 이를 통해 CPU의 활용률을 극대화할 수 있었다. 

![image](https://user-images.githubusercontent.com/64796257/147423652-1a7e0f03-48da-4b28-aa5e-7545779387dd.png)

하지만, 이 방식의 문제점은 다음과 같다. 

- 다른 작업(job)이 수행되기 위해서는 현재 수행되는 작업이 I/O를 해야한다.

==> 즉, 현재 실행하는 프로그램에서 I/O를 요청할 때만 다른 프로그램을 실행할 수 있는 방식이여서
    
어떤 프로그램을 의도적으로 빠르게 수행하고 싶다면 그 프로그램이 I/O를 하지 않거나 마지막에 몰아서 I/O가 동작하는 방식으로 설정해야 했다.
    
(이를 voluntary yield(자발적 양보)에 의존한다고 한다)
    
- High Priority(우선순위) 를 기준으로 수행할 필요가 생겼다.

==> 만약에 4개의 프로그램이 메모리에 있고 현재 실행하는 프로그램을 A라 하자. 

A를 실행하는 와중에 A가 I/O를 요청해서 다른 프로그램을 수행하려고 한다. 이때, 명확한 기준이 없어서 어떤 프로그램을 선택해야 할 지 애매한 상황이 펼쳐진다.

==> 이 때문에 특정 기준을 통해서 우선순위를 정해줘야 할 필요성이 대두되었다. 

이에 고안된 기술이 Time-Sharing이다. 

* TimeSharing : CPU에서 프로그램 실행시간을 타임 슬라이스로 나누어서 실행하는 방식(10ms, 5ms 와 같이 다양하게 설정 가능)

==> 모든 프로그램은 해당 타임 슬라이스 동안 CPU를 점유하고 그 시간이 끝나면 CPU를 양보하는 방식이다.

==> 타이머를 설정해서 임의로 정한 타임 슬라이스(10ms라 하자) 마다 CPU에 전기신호를 보낸다. 전기신호는 Interrupt(인터럽트)라 한다.

CPU가 어떤 프로그램을 작업하는 동안 타이머에서 설정한 시간인 10ms가 지나면 타이머가 CPU에게 인터럽트를 보내서 해당 프로그램의 작업을 중지시킨다.

그런 다음에 CPU는 OS에게 인터럽트가 왔다는 것을 알리고 OS는 CPU가 수행할 다른 작업을 전달한다.

이러한 과정을 통해서 프로그램을 실행하는 방식이 Timesharing이다. 

==> 이렇게 되면 프로그램의 I/O에 의존적이었던 상황에서 벗어나 OS가 CPU에 대한 제어권을 가지게 되면서 OS 또는 사용자가 원하는대로 프로그램을 실행할 수 있게 된다. 

==> 때문에, 여러 개의 작업들이 `동시에 실행되는 것 처럼` 동작한다.

하지만, CPU의 switching을 하면서 발생하는 overhead가 빈번하게 발생한다는 단점이 있다.

### Computer System
- 운영체제의 동작 환경인 `컴퓨터 시스템`에 대해서 알아본다.
- 운영체제와 연관된 `컴퓨터 시스템`의 이슈를 학습한다.

* 컴퓨터 시스템의 4가지 요소(OS 관점)
1. Hardware (ex. cpu, memory, I/O device)
2. OS
3. Application programs
4. Users(사용자)

![image](https://user-images.githubusercontent.com/64796257/147424447-92c9b4f3-2b81-410e-85b9-f0747b3f2524.png)

Application program은 컴퓨터 하드웨어를 직접적으로 제어하지 않는다. 모두 OS를 통해서 하드웨어에 접근하도록 되어 있다.

그래서 그림을 보면 OS는 하드웨어를 감싸고 있다.


■ 컴퓨터 시스템의 구조
- 일반적인 구조
1. 초창기 : 단일 버스 사용 
![image](https://user-images.githubusercontent.com/64796257/147425037-6480969a-f0ce-47c7-9ea4-0530344342a0.png)

그림과 같이 각각의 장치들이 서로 데이터와 정보를 주고 받기 위해서는 이들을 연결해줄 구리선(전선)이 있어야 한다. 

이러한 모든 연결을 정리한 곳이 바로 `MainBoard`이다. 여기서 하드웨어 사이에 데이터와 정보를 전송하는 통로를 `버스`라고 한다.

아래 그림과 같이 한 종류의 시스템 버스에 여러 모듈을 연결한 것이 `단일 버스`이다. 

![image](https://user-images.githubusercontent.com/64796257/147425094-611f3549-7dd6-461b-8a96-27c512a8e615.png)

모든 정보들이 하나의 `버스`에서 이동하는 구조. 만약에 하나의 부품이 버스를 통해 이동해서 Memory에 전달된다면 이동하는 동안 다른 부품들은 일정 시간 기다려야 한다. 

그 시간이 지나고 나서야 다른 부품들의 정보를 주고받을 수 있게 된다.

시대가 흐르면서 CPU와 Memory가 I/O 장치에 비해 훨씬 빠른 속도를 가지게 된다. 

이렇다 보니 속도가 느린 I/O 장치가 동작하고 있는 동안에 이보다 더 빠르게 동작할 수 있는 CPU와 Memory는 `병목현상(Bottle neck)`이 발생한다.

이를 보완하기 위해서 `계층적 이중 버스 구조` 방식이 생겨났다. 

![image](https://user-images.githubusercontent.com/64796257/147425228-1cdfb400-58d7-485f-9e75-13d251909637.png)

현재 대부분의 컴퓨터는 위와 같은 구조로 이루어져 있다. 

동작을 빠르게 처리하는 하드웨어는 System bus와 연결되어 있고 동작을 느리게 처리하는 하드웨어는 I/O bus와 연결되어 있다.

만약에 CPU와 I/O 장치가 서로 통신하고 싶다면 Extension Bus Interface를 통해 통신하면 된다.

계층적 이중 버스 구조를 좀 더 구체화하면 아래와 같다.

![image](https://user-images.githubusercontent.com/64796257/147425308-aa7626c0-5886-4d43-9c5f-48c5b8abd7bc.png)

- NorthBridge : 고속 장치를 제어하는 버스 컨트롤러 ex. CPU, 메모리, PCI-E 

최근에는 그래픽 카드 슬롯이 추가되었다. 원래는 I/O 버스에 있었지만 2D 뿐만 아니라 3D 그래프까지 처리해야할 필요성이 생겼기 때문이다.

- SouthBridge : 비교적 저속 장치를 제어하는 버스 컨트롤러 ex. IDE, SATA, USB 

cf) Peripheral device (주변 기기) : 시스템 버스에 연결되어 있지 않는 장치

cf) SSD(Solid-State Drive) : 반도체를 이용해서 정보를 저장하는 장치. 속도가 빠른 SSD가 등장하면서 NorthBridge에 연결하기 시작했다.

■ OS와 연관된 컴퓨터 시스템의 개념

운영체제와 사용자는 컴퓨터 시스템의 하드웨어 및 소프트웨어 자원을 공유한다. 그래서 올바르게 설계된 OS는 잘못된 프로그램으로 인해 다른 프로그램 또는 OS 자체가 잘못 실행될 수 없도록 보장해야 한다.

시스템을 올바르게 실행하려면 `OS 코드 실행`과 `사용자-정의 코드 실행`을 구분할 수 있어야 한다. 

이를 구분한 것이 `user mode`와 `kernel mode`이다. 

- User mode & Kernel mode : 이러한 mode를 표시하는 비트를 `모드 비트`라 하며 이는 컴퓨터 하드웨어에 추가되어 있다. (0이는 커널모드 / 1이면 사용자 모드)

User mode : 일반적인 user program을 실행할 때 CPU에서 설정하는 모드 / Kernel mode : OS가 동작할 때 CPU에서 설정하는 모드

![image](https://user-images.githubusercontent.com/64796257/147425631-c7a05a85-1c85-4186-8cad-2671f6a5c55e.png)

이러한 모드는 system protection을 위해서 필요하다. 실행모드의 권한에 따라 접근할 수 있는 메모리와 실행가능한 명령어를 제한해서 시스템과 하드웨어를 보호한다.

kernel mode에서만 실행할 수 있는 instruction을 Privileged Instruction이라 한다. 

![image](https://user-images.githubusercontent.com/64796257/147425696-efe35f7e-9f56-4f16-b758-c5b3329c5b88.png)

해당 instruction 들은 시스템이나 하드웨어를 직접 동작하는 instruction이다. 

만약에 user mode에서 위 instruction 들을 사용한다면, 사용하려 했다는 사실을 OS에게 알려주고 OS는 해당 요청을 종료(kill) 시킨다.

이처럼 user mode아 kernel mode에섯 실행할 수 있는 instruction 종류는 차이가 있다. 

위 명령어들을 사용하기 위해서는 CPU 상태가 kernel mode여야 하는데 이러한 mode에서 동작할 수 있는 유일한 소프트웨어가 바로 OS이다.

- 실행모드 전환 

1) kernel mode ⇒ user mode : OS에서 실행되는 동작. 

처음 컴퓨터를 부팅할 때는 kernel mode에서 시작한다. 부팅이 다 끝나면 user mode로 바뀌면서 user program이 프로그램을 실행할 수 있도록 한다.

2) user mode ⇒ kernel mode : CPU에서 실행하는 동작 

예를 들어, application이 privileged instruction을 사용한다면, 

해당 app은 privileged 명령어에 접근할 권한이 없기 때문에 이 사실을 CPU가 OS에게 알리기 위해서 user mode를 kernel mode로 바꾸고 나서 OS에게 해당 사실을 알려준다.

■ CPU의 이벤트 처리 기법 = Interrupt : CPU가 동작하는 동안 CPU에게 전달되는 전기 신호 

1) HW Interrupt : 비동기적 이벤트를 처리하기 위한 기법 (비동기적 이벤트 : 현재 작업과는 무관하게 외부에서 발생하는 이벤트)

ex. 네트워크 패킷 도착 이벤트, I/O 요청

- 인터럽트 처리순서 

⇒ 큰 맥락 : CPU가 하던 일을 중간에 멈추고 인터럽트가 들어왔을 때 해당 인터럽트에 관련된 동작을 실행하고 나서 돌아와 멈췄던 부분에서 다시 일을 시작한다.

1) 현재까지의 실행 상태(state) 저장

2) ISR(Interrupt Service Routine)으로 점프 

ex. 네트워크 패킷이 도착했다는 인터럽트를 받았다면 해당 인터럽트를 어떻게 처리할 지 정해놓은 코드가 ISR이다. 이와 같이 만들어 놓은 코드에 맞춰 인터럽트를 처리한다.

3) 저장한 실행 상태(state) 복원

4) 인터럽트로 중단된 지점부터 다시 시작

![image](https://user-images.githubusercontent.com/64796257/147426373-9459c703-1e10-44c4-a663-7e3f9ae1ba7b.png)

![image](https://user-images.githubusercontent.com/64796257/147426334-705ddf29-ebc5-4c9d-9bb3-9a2b5fd4e706.png)


2) SW Interrupt = Trap : 동기적인 이벤트를 처리하기 위한 기법

동기적인 이벤트 : 현재 실행중인 작업과 관련있는 이벤트 

ex. 연산을 하는 와중에 0으로 나누는 오류가 발생했다. ==> 이때 SW Interrupt를 통해 Trap Handler에 의해서 에러를 처리한다.

⇒ 현재 동작 중인 작업에 의해 발생하기 때문에 HW Interrupt와 달리 실행 상태(state)를 저장/복원하지 않는다.

![image](https://user-images.githubusercontent.com/64796257/147426768-39cb747e-a4a5-4be8-afe6-3a3526ad9a11.png)

■ I/O Device 기본 개념

- Bus : CPU, RAM, I/O 장치 간 데이터가 전송되는 통로. 전달하는 데이터의 종류에 따라 3가지 버스로 구성된다(Data, Address, Control)

ex. 학생 명단(data)dmf 100번지(Address)에 저장하고 이를 디바이스에 write 한다(control)

- Device Registers : 보통 하드웨어 장치는 4가지 종류의 레지스터를 가진다. 

1. Control Register : CPU가 장치에 명령을 내리기 위한 레지스터
2. Status Register : 장치의 현재 상태를 표시하는 레지스터
3. Input Register : 입력값을 설정하는 레지스터
4. Output Register

- Controller : 고수준의 I/O 요청을 저수준의 장치 명령어로 해석하는 디지털 회로. 이는 장치와 직접 상호작용한다.

■ I/O 처리 기법

1. Interrupt : I/O를 요청하고 해당 장치의 종료를 확인하는 방법

⇒ CPU가 다른 작업을 수행하는 동안 장치로부터 종료 메시지를 인터럽트로 수신하면 인터럽트에 대한 ISR을 수행해서 I/O 종료 작업을 수행한다

⇒ 이 방식은 속도가 느린 device 처리에 적합하다.

- 장점 : CPU가 I/O 작업 종료를 대기하지 않고 다른 작업을 수행하기 때문에 CPU의 활용률을 높일 수 있다.
- 단점 : 기존의 작업을 하다가 인터럽트를 수행하기 위해서 switching 할 때 발생하는 비용이 클 수 있다. 그리고 I/O 작업의 종료를 즉시 확인할 수 없다.

2. Polling : 특정 이벤트의 도착 여부를 주기적으로 확인하면서 기다리는 방법

⇒ 매 순간 이벤트의 발생 여부를 확인한다. 

⇒ 속도가 빠른 device 처리에 적합하다.

- 장점 : Controller 나 장치가 충분히 빠른 경우, I/O 작업의 종료를 즉각 확인하고 처리할 수 있다.
- 단점 : 이벤트의 도착 시간이 길어지면 이로 인한 CPU time을 낭비하게 된다.

3. DMA(Direct Memory Access) 

⇒ Interrupt와 Polling은 모두 CPU가 I/O 연산에 대해 직접 관여하는 방법이다. 

만약에 전송할 데이터가 클 경우 CPU를 `장치 상태 확인` 및 `버스에 데이터를 쓰는 행위`에 사용하는 건 낭비이다. 

이러한 문제점을 보완하기 위해서 I/O를 위한 별도의 프로세스를 DMA가 처리한다. 

⇒ 이 기술이 고안된 이후에는 CPU가 I/O 작업을 하는 일은 거의 없어졌다.

![image](https://user-images.githubusercontent.com/64796257/147427363-bd16a495-da2b-41a0-bbf9-3cc19869a8cd.png)

