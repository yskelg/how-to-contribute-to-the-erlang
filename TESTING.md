Erlang/OTP 테스트
==================



테스트를 시작하기 전에 테스트할 Erlang 릴리스가 있어야 합니다.
릴리스가 있어야 합니다. Erlang 릴리스를 빌드하는 방법은 [$ERL_TOP/HOWTO/INSTALL.md][]를 참조하세요.
를 참조하세요.



*참고*: 이 지침은 Erlang/OTP 버전에 따라 다를 수 있습니다,
따라서 테스트 중인 버전에 대한 지침을 반드시 읽어보세요.



짧은 버전
-------------
예를 들어 테스트하려는 Erlang 릴리스의 최상위 디렉토리로 이동합니다:
cd $HOME/git/otp



```bash
export ERL_TOP=`pwd` # 빌드 루트 위치를 정의합니다.
./configure && make # 모든 Erlang/OTP를 빌드합니다.
make test # 모든 Erlang/OTP를 테스트합니다.
```



테스트는 완료하는 데 시간이 오래 걸리며 테스트가 통과하더라도 많은 로그를
콘솔에 많은 로그를 출력합니다. 전체 실행은 비교적 최신 머신에서 약 6시간 정도 걸립니다.
6시간 가까이 걸립니다. 테스트의 일부만 실행하는 방법에 대한 자세한 내용은 [개발 중 테스트 실행하기][]를 참조하세요.
테스트의 하위 집합만 실행하는 방법에 대한 자세한 내용은 [개발 중 테스트 실행]을 참조하세요.



테스트는 어디에 있나요?
-------------------



Erlang/OTP를 테스트하는 많은 테스트(2012년 기준 약 12,000개)가 있습니다.
소스 트리의 여러 위치에 있습니다. 아래는 목록입니다.
테스트를 찾을 수 있는 위치의 목록입니다:



* $ERL_TOP/lib/AppName/test/
* $ERL_TOP/erts/test/
* $ERL_TOP/erts/emulator/test/
* $ERL_TOP/erts/epmd/test/



테스트 작성
-------------



모든 테스트는 [common_test][] 제품군이며 모든 테스트와 동일한 패턴을 따릅니다.
[common_test][] 스위트와 동일한 패턴을 따릅니다. 그러나 몇 가지 코너 케이스는
라는 테스트 래퍼가 처리합니다. t`를 사용하면 테스트 작성자가
data_dir][]에 `Makefile.src` 및 `Makefile.first` 파일과 `all_SUITE_dir`라는 특수
모든_SUITE_data`라는 디렉터리에 저장할 수 있습니다.



Makefile.first`는 다른 Makefile보다 먼저 실행되며 일반적으로 다음과 같은 용도로 사용됩니다.
다른 테스트 스위트에서 필요한 .hrl 파일을 생성하는 데 사용됩니다. 현재로서는
erl_인터페이스 테스트만 이 기능을 사용하며 앞으로도 계속 사용해야 합니다.



Makefile.src`는 다음과 같은 경우 생성된 `변수`를 사용하여 `Makefile`로 구성됩니다.
[테스트를 구성][] 할 때 생성된 `변수`를 사용하여 `Make파일`로 구성됩니다. 이 `메이크파일`은 나중에 테스트 스위트에서 실행되어
에서 실행하여 테스트가 실행하는 데 필요한 플랫폼별 코드를 컴파일합니다.



개발 중 테스트 실행
------------------------------



make test` 명령은 현재 디렉터리에 test라는 디렉터리가 있을 때 작동합니다.
라는 디렉터리가 있고 소스 코드 트리의 루트 디렉터리에 있을 때 작동합니다.



다음은 `make test`가 어떻게 사용되는지 보여주는 몇 가지 예입니다.
사용법을 보여줍니다:



```bash
# ERL_TOP을 올바르게 설정해야 합니다.
cd /path/to/otp
export ERL_TOP=`pwd`



# Erlang/OTP 빌드
#
# make test는 다음과 같은 경우를 제외하고 테스트 코드만 컴파일합니다.
# make test가 $ERL_TOP에서 실행되는 경우를 제외하고는 테스트 코드를 컴파일합니다.
./otp_build setup -a



# 애플리케이션에 대한 모든 테스트 스위트를 실행합니다.
(cd $ERL_TOP/lib/asn1 && make test)
make asn1_test



# 테스트 스위트를 실행합니다(ARGS 변수는 ct_run에 전달됩니다).
(cd $ERL_TOP/lib/stdlib && make test ARGS="-suite ets_SUITE")
make stdlib_test ARGS="-suite ets_SUITE"



# 테스트 케이스 실행
(cd $ERL_TOP/erts/emulator && make test ARGS="-suite binary_SUITE -case deep_bitstr_lists")
make emulator_test ARGS="-suite binary_SUITE -case deep_bitstr_lists"



# 모든 테스트 실행
#
ERL_TOP에서 실행하면 "make test"가 먼저 릴리스되고 # 모든 테스트를 구성한 다음
# 모든 테스트를 구성한 다음 `ts:run`으로 모든 테스트를 실행하려고 시도합니다.
# 이 작업은 몇 시간이 걸립니다.
(cd $ERL_TOP && make test)
```



더 많은 예제는 [DEVELOPMENT.md](DEVELOPMENT.md)를 참조하세요.



테스트 릴리스
---------------



소스 트리에서 테스트하지 않을 때는 먼저 모든 테스트를 릴리스해야 합니다.
가장 쉬운 방법은 다음과 같이 `otp_build`를 사용하는 것입니다:



```bash
export ERL_TOP=`pwd`; ./otp_build tests
```



이렇게 하면 Erlang/OTP의 모든 테스트가 `$ERL_TOP/release/tests/`로 릴리스됩니다. 다음과 같은 경우
테스트가 릴리스되는 디렉터리를 변경하려면 `TESTROOT`
환경 변수를 사용하도록 변경합니다.



이제 `$TESTROOT`에 *_test 폴더가 표시됩니다. 이 폴더에는 다음이 포함됩니다.
Erlang/OTP를 테스트하는 데 필요한 모든 것이 포함되어 있으며 플랫폼에 독립적입니다.
여러 플랫폼에서 Erlang을 테스트하는 경우 한 플랫폼에서 릴리스하고 다른 모든 플랫폼에 테스트를 복사하기만 하면 됩니다.
테스트를 다른 모든 플랫폼에 복사하기만 하면 됩니다.



### 크로스 테스트 릴리스



교차 컴파일 환경에서 테스트를 릴리스하는 방법은 [$ERL_TOP/HOWTO/INSTALL-CROSS.md][]를 참조하세요.



테스트 구성 및 실행
-----------------------------



테스트 실행은 먼저 테스트를 릴리스할 때 생성한 `$TESTROOT/test_server` 폴더로 이동한 후
폴더로 이동한 다음 해당 디렉토리에서 `erl`을 시작합니다. 지정된
에뮬레이터 플래그가 테스트 실행에 사용됩니다. 예를 들어
비동기 스레드를 사용하여 테스트하려면 시작할 때 `+A 10`을 `erl`에 제공해야 합니다.



테스트를 구성하고 실행하려면 `ts`가 사용됩니다. t`는 래퍼 모듈입니다.
[common_test][]에 대한 래퍼 모듈로, 구성 및 빌드 문제를 먼저 처리합니다.
[common_test][]가 시작되기 전에 구성 및 빌드 문제를 처리합니다.



t`에는 다음과 같은 경우에 유용할 수 있는 많은 특수 옵션과 함수가 있습니다.
테스트할 때 유용할 수 있습니다. 전체 목록은 erlang 셸에서 `ts:help()`를 참조하세요.



### 테스트 환경 구성하기



릴리즈된 테스트를 실행하기 전에 대상 시스템에 테스트를 설치해야 합니다.
테스트 설치는 테스트하려는 Erlang 셸에서 `ts:install().`을 호출하여 수행합니다.
을 호출하면 됩니다. 기본적으로 `ts:install()`은 기본적으로
구성 스크립트와 시스템의 모양과 에뮬레이터의 종류를 파악하는 Erlang 코드를
어떤 종류의 에뮬레이터로 테스트할 것인지 파악하는 에를랑 코드입니다. t:install()`은 또한
필요한 경우 몇 가지 인수를 받을 수도 있습니다. 자세한 내용은 `ts:help()`를 참조하세요.



t:install()`에 의해 생성된 모든 변수는 다음 위치에서 찾을 수 있습니다.
`$TESTROOT/test_server/variables`에 있습니다.



### 테스트 실행하기



모든 테스트 스위트를 실행하려면 `$TESTROOT/test_server`로 이동하여 Erlang 셸을 실행하고 다음과 같이 입력합니다:



```erlang
ts:run().
```



모든 테스트를 실행하려면 몇 시간이 걸리므로 단일 애플리케이션에 대한 테스트 케이스를
단일 애플리케이션에 대한 테스트 케이스



```erlang
ts:run(Application, [batch]).
```



또는 응용 프로그램에 대한 테스트 스위트의 일부(예



```erlang
ts:run(emulator, bs_construct_SUITE, [batch]).
```



를 사용하여 `bs_construct_SUITE` 모듈에서 테스트를 실행할 수 있습니다.
바이너리의 구성 테스트).



모듈 이름과 함수 이름을 지정하여 단일 테스트 케이스를 실행할 수도 있습니다:



```erlang
ts:run(emulator, bs_bincomp_SUITE, byte_aligned, [batch]).
```



자세한 내용은 `ts:help().`를 실행하세요.



R14B02부터는 에러 인터페이스 테스트를 제외한 모든 테스트를 시작할 수도 있습니다.
를 제외한 모든 테스트를 릴리스된 애플리케이션 테스트 디렉토리에서 직접 호출하여 시작할 수도 있습니다,
즉



```bash
cd $TESTROOT/test_server
ERL_TOP/bin/ct_run -suite ../compiler_test/andor_SUITE -case t_orelse
```



명령줄에서 [ct_run][]을 실행하는 경우에도 여전히
t:install()` 단계를 수행해야 합니다.



결과 살펴보기
---------------------



웹 브라우저에서 `$ERL_TOP/release/tests/test_server/index.html` 파일을 엽니다. 또는
테스트 스위트가 실행 중일 때 `$ERL_TOP/release/tests/test_server/suite.log.latest.html`을 열어서
현재 실행 중인 테스트 스위트에 대한 지금까지의 결과를 확인합니다.






주소 검사기로 테스트 실행
--------------------------------



빌드 타겟을 `asan`으로 설정하여 에뮬레이터를 먼저 빌드합니다.
ERL_TOP/HOWTO/INSTALL.md][]를 참조하세요.



에러 로그가 생성될 디렉터리로 환경 변수 `ASAN_LOG_DIR`을 설정합니다.
디렉터리로 설정합니다.



```bash
export ASAN_LOG_DIR=$TESTROOT/test_server/asan_logs
mkdir $ASAN_LOG_DIR
```



환경 변수 `TS_RUN_EMU`를 `asan`으로 설정합니다.



```bash
export TS_RUN_EMU=asan
```



그런 다음 [위에서 설명한](#running-the-tests)처럼 `ts:run`으로 원하는 테스트를 실행합니다.
로그 파일을 직접 검사하거나 다음 위치에서 스크립트를 사용하세요.
모든 로그 파일을 읽으려면 `$ERL_TOP/erts/emulator/asan/asan_logs_to_html`의 스크립트를 사용하세요.
에서 모든 로그 파일을 읽고 하나의 html 페이지로 추출합니다.
asan_summary.html`로 추출합니다. 동일한 메모리 누출에 대한 반복적인 보고는
스크립트에서 무시되어 분석하기가 더 쉬워집니다.






Valgrind로 테스트 실행
-----------------------



먼저 [valgrind][]가 설치되어 있는지 확인한 다음, 소스에서 OTP를 빌드하고
를 빌드하고 `valgrind` 빌드 타겟으로 에뮬레이터를 빌드합니다. 참조
[$ERL_TOP/HOWTO/INSTALL.md][].



환경 변수 `VALGRIND_LOG_DIR`을 valgrind 에러 로그가 있는 디렉토리로
디렉터리로 설정합니다.



```bash
export VALGRIND_LOG_DIR=$TESTROOT/test_server/vg_logs
mkdir $VALGRIND_LOG_DIR
```



환경 변수 `TS_RUN_EMU`를 `valgrind`로 설정합니다.



```bash
export TS_RUN_EMU=valgrind
```



그런 다음 위에서 설명한 대로 `ts:run`으로 원하는 테스트를 실행합니다(#running-the-tests).
로 원하는 테스트를 실행하고 `$VALGRIND_LOG_DIR`에서 로그 파일을 검사합니다.






   [ct_run]: http://www.erlang.org/doc/man/ct_run.html
   [CT HOOK]: http://www.erlang.org/doc/apps/common_test/ct_hooks_chapter.html
   [$ERL_TOP/HOWTO/INSTALL.md]: INSTALL.md
   [$ERL_TOP/HOWTO/INSTALL-CROSS.md]: INSTALL-CROSS.md#크로스 컴파일 시스템 테스트 중
   [common_test]: http://www.erlang.org/doc/man/ct.html
   [data_dir]: http://www.erlang.org/doc/apps/common_test/write_test_chapter.html#data_priv_dir
   [테스트 구성]: 테스트 환경 구성]: #configuring-the-test-environment
   [VALGRIND]: https://valgrind.org
   [개발 중 테스트 실행]: 개발 중 테스트 실행]: #running-tests-while-developing



   [?TOC]: true
