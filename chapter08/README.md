## Chapter 8 GC 로깅, 모니터링, 튜닝, 툴

### 3. GC 기본 튜닝

엔지니어는 튜닝을 수행하면서 네 가지를 측정해야 한다.

1. 할당 2. 중단 민감도 3. 처리율 추이 4. 객체 수명

자바의 힙 크기를 조정하는 플래그는 -Xms, -Xmx, XX:MaxPermSize, XX:MaxMetaspaceSize 가 있으며 permsize 플래그는 java 8 이후에 아무런 효과가 없다. (지워야 한다. 이거 프로덕션 코드에서 봤는데..)
