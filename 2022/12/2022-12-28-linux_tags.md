# 특정 아키텍처용 태크 생성 tag.sh 스트립트 분석
> ◈ References
>
>  - [꿈꾸는 사람](https://dreamlog.tistory.com/69)

리눅스 커널 소스를 분석할 때 방대한 소스로 인해 ctag와 cscope를 이용한다.

리눅스 커널에는 많은 아키텍처를 지원하는데 분석할 아키텍처별 ctags용 태그를 생성할 수 있는 tag.sh 스크립트를 제공한다. 이 스크립트는 커널 소스 바로 아래 scripts 디렉토리에 있다.



아래 명령어를 입력하여 ARM 아키텍쳐용 태그를 생성한다.

```bash
$ make tags ARCH=[아키텍쳐명]
```

ARM용 태그를 생성하는 명령은 아래와 같다.

```bash
$ make tags ARCH=arm
```

아키텍쳐명을 확인하려면 해당 커널 소스 폴더/arch에서 확인할 수 있다.

> 1. ctags는 소스로부터 태그를 생성해 준다. 태그는 일종의 이정표 역할을 하여, 이것으로 방대한 커널 소스에서 자신이 찾고자 하는 부분을 쉽게 찾을 수 있도록 도와준다.
>
> 2. cscope와 결합하면 소스에 나타나는 심볼, 함수, 클래스, 변수 등의 정의부를 찾거나, 특정 함수를 호출하는 곳을 찾고자 할때 많은 도움이 되는 강력한 검색 도구가 된다.
>
>    - 소스 폴더에서 아래 명령어로 태그 데이터베이스를 만든다.
>
>      ```bash
>      $ make cscope ARCH=[아키텍쳐명]
>      ```



이 명령은 커널 최상위 Makefile를 통하여 tag.sh 스크립트를 호출하게 된다.

![img](_media/linux_tags1.jfif)



파일 내의 tags란 타켓에서 $(call cmd, tags)를 실행하게 된다.

이것은 위 2번을 수행하는데 실제 아래와 같은 명령을 실행하게 된다.

```bash
$ /bin/bash ./scripts/tags.sh ctags ARCH=arm
```



위 명령이 실행하는 tags.sh 스크립트를 분석하자.

![img](_media/linux_tags2.jfif)



첫 패러미터인 tags로 xtags란 함수를 호출하게 된다.

```bash
case "$1" in
	"tags")
		rm -f tags
		xtags ctags
		;;
esac
```

시스템에 설치된 ctags의 버전이 Exuberant로 시작하면 exuberant 함수를 호출한다.

exuberant 함수가 모든 소스에 대한 태그를 생성하게 된다. exuberant 함수를 분석하면 find와 xargs 도구로 태그를 생성하게 된다.
