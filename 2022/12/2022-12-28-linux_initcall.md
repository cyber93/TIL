# [Kernel 3.16.6] Init call mechanism - 디바이스 드라이버 호출 체계
> ◈ References
>
>  - [꿈꾸는 사람](https://dreamlog.tistory.com/336)

리눅스 디바이스 드라이버는 특정한 하드웨어나 장치를 구동하는 커널의 일부로 동작하는 프로그램이다.

리눅스에는 디바이스 드라이버들의 초기화 코드를 호출하는 최적화된 체계가 있다.

이번 글은 리눅스의 내장(built-in) 디바이스 드라이버의 초기화 호출 체계를 분석한다.

## 1. 디바이스 드라이버 작성

디바이스 드라이버는 커널 공간에 적재될 때 장치를 제어하는 기능을 하며 이를 위해 다음 함수를 만들어야 한다.

```
module_init(my_func_init);
module_exit(my_func_exit);
```

my_func_init가 커널이 부팅될 때 어떻게 불리는지 찾아보자.

## 2. 디바이스 드라이버 호출 과정.

my_func_init이 호출되는 함수 호출 과정은 아래 그림과 같다.

![img](_media/linux_initcall1.jfif)



do_one_initcall 함수의 fn()이 my_func_init()이다.

```
if (initcall_debug)
    ret = do_one_initcall_debug(fn);
else 
    ret = fn();
```

이 과정을 소스를 추적하며 분석한다.



## 3. 초기화 호출 체계 분석

커널 내장 모듈의 module_init은 매크로로 아래와 같이 확장된다.

```
#define module_init(x)  __initcall(x);
```



```
#define __initcall(fn) device_initcall(fn)
```



```
#define device_initcall(fn)    __define_initcall(fn, 6)
```



```
 #define __define_initcall(fn, id) \        static initcall_t __initcall_##fn##id __used \
        __attribute__((__section__(".initcall" #id ".init"))) = fn; \
        LTO_REFERENCE_INITCALL(__initcall_##fn##id)
 static initcall_t __initcall_my_func_init6 __used __attribute__((__section__(".initcall6.init"))) = my_func_init;
```

**__initcall_my_func_init****6** 심벌이 만들어 지고 .initcall6.init란 EFL 영역에 추가 한다.

정리된 내용은 아래 그림과 같다.

![img](_media/linux_initcall2.jfif)



```
for (fn = initcall_levels[level]; fn < initcall_levels[level+1]; fn++)
        do_one_initcall(*fn);
```

module_init으로 초기화 되는 모든 내장 모듈들은 6번째 레벨이고 __initcall6_start에서 시작해서 __initcall7_start 직전에 끝난다. 

__initcall6_start는 .initcall6.init 영역에 등록된 첫 함수 포인터의 주소이다.

**6번째 레벨의 함수들이 초기화 되는 순서는 Makefile내의 파일 순서를 따른다.**



```
obj-y                           += gpio/ 
...

obj-y                           += tty/ 
```

즉, gpio 디바이스 드라이버가 tty 디바이스 드라이버보다 먼저 생성되고 올라 온다.

정리한 내용 전체는 파일로 첨부한다.

![img](_media/linux_initcall3.jfif)
