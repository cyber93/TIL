# ftrace framework 구조 및 소스파일 구조
> ◈ References
>
>  - 박진범(jinb.park7@gmail.com)



## ftrace framework 구조

ftrace는 Linux Kernel을 Trace하기 위한 하나의 framework이다. 아래 그림은 ftrace framework의 구조이다.

![logo](_media/ftrace1.png)

- common components
  - ring buffer: trace의 결과를 저장할 버퍼이다. trace 결과 기록 및 사용자가 요청시 이 버퍼의 내용을 출력해준다.
  - debugfs I/F: debugfs interface. 사용자<->ftrace 인터페이스 역할을 한다. debugfs 파일을 통해 사용자는 tracer 세팅 및 trace 결과 확인이 가능하다.

- plugin tracers / stack tracer / event tracer
  - 특정 요소를 후킹하여 원하는 것을 tracing 한다. 각 tracer는 ring buffer에 tracing 결과 기록을 한다.

- hook mechanisms
  - tracing을 위해선 후킹 기법을 사용하는데, mcount, tracepoint, kprobes 3가지 방식이 있다. 여기에서는 mcount를 다룬다.

- 요약하면 각 tracer는 특정 hook mechanism을 이용하여 tracing을 수행하여 그 결과를 ring buffer에 저장한다. 사용자가 이 ring buffer에 있는 결과 조회하려면 debugfs를 통해서 할 수 있다.



## ftrace 소스파일 구조

아래 그림은 커널 소스 중 ftrace가 구현된 소스 파일들이다.

arch-common한 파일은 말 그대로 arch(ARM, x64, powerpc ...)와 상관없이 공통된 소스 파일이다. arch-specific은 arch 마다 다르게 구현된 파일들이다.

![logo](_media/ftrace2.png)

### **ftrace part**

- ftrace framework 그림에서 hook mechanism에 해당하는 부분을 담당한다고 보면 된다. mcount 후킹의 경우, 여기서 후킹시 사용할 최상위 함수 포인터인 ftrace_trace_function을 관리한다. (몇몇 debugfs 파일도 담당한다.)
  - kernel/trace/ftrace.c: mcount 후킹의 경우, 여기서 후킹시 사용할 최상위 함수 포인터인 ftrace_trace_function을 관리한다. 이 함수 포인터는 현재 세팅된 tracer의 trace function을 가리킨다. 또한 몇몇 debugfs 파일 관리한다.
  - arch/*/kernel/ftrace.c: 각 arch에 맞는 ftrace.c 파일이 구현된다. 예를 들면, x86의 경우 code rewrite를 하기 전, text 영역 page 를 RW 권한으로 수정한다. 하지만 arm은 이미 RW 이므로 수정하지 않는다. 이러한 차이점으로 인해서, 같은 기능 함수여도 구현을 달리한다.*
  - arch/*/kernel/entry*.S: 실제 mcount 어셈함수가 구현된 파일이다. mcount 내부에서 kernel/trace/ftrace.c -  ftrace_trace_funtion() 혹은 ftrace_stub()을 호출하는 부분 코드도 여기 파일에 있다. (mcount는 tracing의 시작 지점이 되는 함수이다. 모든 함수에 mcount 호출이 포함됨.)

### **tracer part**

- ftrace framework 그림에서 common components와 각 tracer들을 담당하는 부분이다.
  - kernel/trace/trace.c: ring buffer와 tracer 관련 debugfs 파일들을 관리한다.
  - kernel/trace/trace*.c : 각 tracer.tracer 마다 자기 자신의 trace function을 가지며, init 과정에서 자기 자신의 trace type 등록하는 등의 작업을 한다.
