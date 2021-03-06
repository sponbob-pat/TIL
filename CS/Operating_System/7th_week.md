## Context switch 

- CPU가 다른 프로세스로 `전환`할 때 시스템은 `이전 프로세스의 상태`를 `저장`해야 한다. 이때, 저장은 `PCB`에 한다.
- 그러고 나서 `context switch`를 통해 새로운 프로세스에 대해서 저장한 내용을 load 해야 한다.

![image](https://user-images.githubusercontent.com/64796257/147666994-a206514c-ac59-48c4-a2d5-27afc87fe33f.png)

1) `P0`를 실행하고 있다가 `context switching`이 일어났다. (이제 `P1`을 실행할 것이다)
2) `P0의 현재 상태`를 `PCB_0`에 저장한다. 
3) 저장하고 나서 `CPU 스케쥴링`을 통해 어떤 프로세스를 `먼저 실행`할지 정한다. 
4) `선택한 프로세스 정보`를 `해당 프로세스의 PCB`로부터 load 해야 하는데 여기서는 `1번 프로세스(P₁)`를 선택했다고 하자.  
5) `PCB₁`로 부터 1번 프로세스의 마지막 실행 순간을 load 한다. 

1번에서 0번으로 바뀔 때도 마찬가지의 동작을 한다.

### Context switching의 `overhead`

- 발생하는 이유 3가지 
1) context switching 자체에 `소요되는 시간`과 `CPU 스케쥴링 시간`
2) TLB flush : context switching을 할 때마다 `TLB`를 `비워줘야` 하는 `TLB flush`가 필요하다.

cf) `TLB` : 주소값에 대한 cache. 내가 원하는 데이터가 어떤 주소에 있는지 저장하는 cache
              
3) cache flush : 프로세스의 데이터에 대한 `cache의 내용`을 `비워주는 것`을 말한다.

이렇게 2), 3)과 같이 `저장했던 cache`를 `비우면` 프로세스의 성능은 떨어질 수밖에 없다. 이러한 과정에서 `overhead`가 `크게 발생`한다.

- PCB(Process Control block) - 여기서의 process는 thread와 동일한 의미를 가진다. 

여기에 프로세스의 정보들이 저장되고 현대 OS에서는 `Thread Control Block(TCB)`이라 불린다.

## Process 

다양한 정보를 저장하지만 중요한 내용으로는 

1) process state – running, waiting 등등
2) program number 
3) program counter(PC) 이 있다. 

### Process state - 5가지 

![image](https://user-images.githubusercontent.com/64796257/147667470-4a09ff2e-72c3-4f1e-9d54-933b4bcce775.png)

| 상태 | 설명 | 
| --- | --- |
| new | 프로세스가 생성 중인 상태 |
| ready | 프로세스가 processor에 할당되길 기다리는 상태 |
| running | 명령어들이 실행되는 상태 |
| waiting | 프로세스가 어떤 이벤트가 일어나길 기다리는 상태 |
| terminated | 프로세스의 실행이 종료되는 상태 |

이때, 커널 내에서 ready_q, waiting_q, running_q를 두고 프로세스들의 상태에 따라 관리한다.

- timer interrupt : 프로세스의 `명령어를 실행`하는 와중에 `주어진 시간이 완료`되면 interrupt를 보내서 `해당 프로세스의 동작`을 `중지`하고 `다른 프로세스`가 `동작`하도록 한다. 
   이때, `원래 동작하던 프로세스`는 다시 `ready_q`로 돌아간다.

- scheduler dispatch : 프로세스가 실행되도록 `cpu를 할당` 받는 것.

나머지 동작들은 단어만 봐도 알 수 있는 내용들이다.

### 프로세스 생성 및 종료 

- Process Creation(프로세스 생성) : 프로세스의 생성에는 크게 2가지 방식이 있다.
	1) 처음에 프로세스를 만들 때는 일일이 모든 내용을 채워줘야 한다.

	2) 그 이후에는 처음 만든 프로세스를 복사하고 나서 그 프로세스를 바탕으로 새로운 내용을 추가하는 방식으로 프로세스가 만들어진다.  
   	⇒ 이런 식으로 만들어지는 방식에는 `parent` 와 `child`가 있다.

시스템 콜 `fork()`를 통해서 똑같은 프로세스를 생성한다. 

이때 새롭게 생성되는 프로세스는 `child process`라 하고 기존에 있었던 프로세스는 `parent process`라 한다. 

`child process`는 pid 빼고 모든 내용이 parent process와 동일하다.

이때, `child process`에 `새로운 내용`을 넣고 싶다면 `exec()` 라는 시스템 콜을 이용한다.

![image](https://user-images.githubusercontent.com/64796257/147667859-d5ba67f0-fb06-41c5-8a86-ea4eaf0809fd.png)

- Process Termination(프로세스 종료)

	⇒ 프로세스가 마지막 문장의 실행을 끝내고 `exit` 시스템 콜을 사용해서 OS에 삭제를 요청하면 해당 프로세스는 종료된다.

	⇒ 프로세스를 종료할 때 부모 프로세스가 종료되기 전에 `자식 프로세스`가 `먼저 종료되어야` 한다.  
    	그렇지 않으면 자식 프로세스는 계속해서 pid값을 가지게 되면서 제한적인 pid의 재료들이 쓸데없이 낭비된다.
    
## Scheduling(스케쥴링) 

### Basic Concepts : Process Scheduling 

⇒ `코어가 하나인 시스템`에서는 한 순간에 `오직 하나의 프로세스만`을 `실행`할 수 있다.  
    나머지 프로세스들은 CPU의 코어가 가용상태가 되서 다시 스케쥴 될 수 있을 때까지 기다려야 한다. 

이때, 어떤 프로세스를 CPU에서 실행시킬 건지 정하는 프로그램을 `CPU scheduler`라고 한다.

### 전체적인 과정 

![image](https://user-images.githubusercontent.com/64796257/147668054-e04e8ae7-baac-49cc-8d19-d957dbb25ba5.png)

1) new 상태에서 ready_queue로 진입한다. 
2) CPU로 진입해서 프로세스가 끝나면 terminated 상태가 된다.
3) 그렇지 않으면 위 그림과 같이 여러 상태로 진행될 수 있다.

각각의 상태를 살펴보면 

| 상태 | 설명|
| --- | --- |
| I/O 요청 | 해당 프로세스가 `I/O작업을 요청`하면 그 요청을 수행하기 위해서 I/O 큐에서 기다렸다가 I/O 작업을 처리하고 나서 `ready_q`로 돌아간다. |
| Time slice expired | 각각의 프로세스에게 할당된 `timer`가 다 끝나면 `ready_q`로 돌아가야 한다. |
| Fork a child | child process를 fork하고 나서 `자식 프로세스`가 `동작을 완료`할 때까지 `wait_q`에서 `처리를 기다리다`가 처리가 끝나면 `ready_q`로 돌아간다. |
| Wait for an interrupt | `일반적인 interrupt`가 들어왔을 때 해당 인터럽트를 처리하고 나서 `ready_q`로 돌아간다. |

여기서 ready_q에서 CPU로 가는 과정에서 `cpu-scheduling`을 통해 `ready_q에 있는 프로세스들` 중 `어떤 프로세스`를 `먼저 실행할지` 선택하고 나서 CPU로 이동한다.

### CPU-I/O burst cycle : 프로세스의 실행은 `CPU 실행(CPU burst)`과 `I/O wait(I/O burst)`로 구성됨

![image](https://user-images.githubusercontent.com/64796257/147668324-ecf0b8a7-8663-481c-8e63-595354c80f97.png)

프로세스의 실행은 `CPU 실행(CPU burst)`과 `I/O의 대기(I/O burst)`의 사이클(cycle)로 구성된다.

프로세스의 실행은 위 그림과 같이 CPU burst -> I/O burst -> CPU burst -> I/O burst -> CPU burst ... 를 반복하면서 진행된다. 

그러다가 마지막에는 `CPU burst` 뒤에 `I/O burst`가 뒤따라 오는 대신에 `실행을 종료하기 위한 시스템 콜`과 함께 끝난다.

cf) `CPU burst`를 주로 하는 프로세스를 `CPU-bound process`라 하고 `I/O burst`를 주로 하는 프로세스를 `I/O bound process`라 한다.

![image](https://user-images.githubusercontent.com/64796257/147670044-66e0ad74-1646-4bd9-aeb2-3ed0a9ad1b34.png)

위 그래프는 `CPU Burst의 동작 길이(Burst Duration)`에 따른 `burst의 빈도수(frequency)`를 나타낸 그래프다.

ex. cpu burst의 동작 길이 = 16 ⇒ 20회 미만의 burst 빈도수를 기록한다.

`cpu burst의 동작 길이`를 너무 짧게 잡으면 그만큼 `burst의 빈도수`가 늘어나면서 I/O 전환이 불필요하게 많아진다.

반대로 `cpu burst의 동작 길이`를 너무 길게 잡으면  
`파란색 그래프를 나타내는 프로세스`는 동작을 다 하고 나서 남는 시간이 많아지만  
`빨간색 그래프를 나타내는 프로세스`에게는 CPU를 충분히 활용할 수 있는 시간이 될 수 있다.  

이러한 `공정성 문제`가 생길 수 있어서 적절한 시간을 정해주는게 중요하다.

### Scheduler

1) short-term scheduler(or CPU scheduler) : `ready queue`에 있는 프로세스 중에서 `CPU에서 동작할 프로세스`를 고르는 스케쥴러.

2) Long-term scheduler(or job scheduler) : `storage`에 있는 작업들 중에서 어떤 작업을 `M.M에 올릴지` 결정하는 스케쥴러.

3) Medium-term scheduler : 프로세스가 작업을 종료하지 않았지만 `해당 프로세스`를 `storage로` 내리고 `storage에 있는 다른 작업`을 대신 `M.M`로 올리는 작업을 의미한다.

⇒ 그래서 이 작업은 `degree of multi-programming`을 증가시킬 수 있다.

cf) degree of multi-programming : `하나의 CPU`에서 `얼마나 많은 프로세스를 사용할 수 있는지 나타내는 정도`  
   대체적으로 이 값이 커질수록 `CPU의 활용`은 `증가`한다. 그래서 3)은 이를 증가시킬 수 있다.	


### Non-preemptive & preemptive

1) Non-preemptive(비선점형) : 강제로 종료시킬 수 없도록 설정한 스케쥴링.  
⇒ 때문에 `자발적`으로 CPU에서 프로세스의 동작이 끝날 때까지 기다려야 한다.  
⇒ 그래서 `context-switching`은 줄어들지만 `공평성(fairness)`은 떨어진다.

2) preemptive(선점형) : 타이머 또는 다른 이유로 동작하고 있는 프로세스를 강제로 내리고 다른 프로세스를 동작하도록 하는 스케쥴링. 

⇒ 그래서 `context-switching`은 늘어날 수 있지만 `공평성`은 증가한다. 다만, 이 방식을 사용할 때 고려해야할 점이 있다.

- 고려해야 할 점 
1) 공유하고 있는 데이터에 접근할 때  
  A라는 프로세스가 어떤 데이터에 접근해서 작업을 하고 있었다고 하자.  
  `preemptive(선점형)` 이라서 기존에 작업하고 있던 A라는 프로세스를 종료했다.  
  이때, `B 프로세스`가 동작하려 하는데 `A가 이용했던 데이터`에 접근하려 한다.  
  만약에 하나의 프로세스가 어떤 데이터에 접근할 때 하나의 프로세스만 접근할 수 있다고 하면  
  A 프로세스가 동작하던 것이 아직 끝나지 않았기 때문에 B는 해당 데이터에 접근할 수 없게 된다.

2) kernel mode에서 동작하는 명령어를 수행하는 동안에 선점할 때  
⇒ kernel mode는 하드웨어의 자원을 건드릴 수 있는 민감한 명령어가 많이 포함되어 있다.  
⇒ 그런 중요한 작업을 하던 와중에 선점형 스케쥴러에 의해서 동작이 멈춘다면 해당 자원에 대한 작업이 불안정하게 끝나면서 문제를 발생시킬 수 있다.

3) 중요한 OS 동작을 하는 와중에 인터럽트가 왔을 때 

cf) Dispatcher : short-term scheduler에서 하나의 프로세스에서 다음 프로세스로 작업을 전환하는 context-switching를 구현한 코드.






