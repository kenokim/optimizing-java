### 
java HelloWorld 를 실행했을 때 일어나는 일들
1. OS - 가상 머신 프로세스 시작
2. 자바 가상 환경 구성, 스택 머신 초기화


Hotspot JVM - 현재 가장 일반적인 jvm, hotspot 을 찾아서 JIT 을 이용하는 방법

1. 자바 소스를 javac 로 컴파일하여 바이트코드를 만든다.
2. Class loader 를 통해 method cache 에 올린다.
3. Interpreter 로 method cache 에서 읽어서 실행하고, 자주 사용되는 메소드는 JIT 컴파일러를 통해 바이트코드 -> 머신코드로의 컴파일을 한다.
4. 컴파일 된 머신코드는 code cache 에 올리고 interpreter 는 이것을 참조해서 코드를 실행한다.

이러한 핫스팟의 컴파일 서브시스템과 garbage collector 는 자바의 성능 최적화의 중심 주제이다.
