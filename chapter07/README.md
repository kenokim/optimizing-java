Chapter 7. Advanced Garbage Collection Algorithms

GC 는 pluggable subsystem, 같은 어플리케이션 코드에 GC 를 plug 해서 쓸 수 있음.

중단 시간, 처리율, 중단 빈도, 회수 효율, 중단 일관성을 고민해야 한다.

빅 데이터 어플리케이션은 중단 시간보다 처리율이 더 중요하다.

OpenJDK 10 기준 3가지 주요 GC 가 있다. Parallel GC, CMS, G1
