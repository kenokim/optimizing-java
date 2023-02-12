## Chapter 6 가비지 수집 기초

GC 는 쓸모없는 객체를 알아서 제거하는 것

### 1. Mark and sweep

```c
void markAndSweep() {
	// mark
	for (all pointers P on the run-time stack or in the static data area) {
		mark *P;
	}
	
	// sweep
	for (all objects *P on the heap) {
		if *P is not marked then
			delete P
		else
			unmark *P
	}

	template <</span>class T>
	void mark(T* p) {
		if *p is not already marked
			mark *p;
			for (all pointers q inside *p) {
				mark *q;
			}
	}
}
```

jmap -histo 로 힙의 스냅샷을 확인할 수 있다.

VisualVM 의 sampling 으로 힙의 변하는 모습을 볼 수 있다.

정확하게 분석하려면 GC 로그를 봐야 한다.

STW 란 GC 사이클이 발생할 때 모든 애플리케이션 스레드가 중단되는 것

### 2. Hotspot runtime 개요

핫스팟은 런타임에 OOP (ordinary object pointer) 라는 structure (c/cpp structure) 로 나타낸다.

OOP 는 2개의 헤더, Mark 워드, Klass 워드와 객체 인스턴스 필드로 구성된다. (객체의 종류에 따라 다를 수 있음)

GC 루트란 메모리 풀 외부에서 내부를 가리키는 포인터

- stack frame, JNI, register, 코드 루트, 전역 객체, 로드된 클래스의 메타데이터

### 3. 할당과 수명

GC 가 일어나는 원인은 1. 할당률, 2. 객체 수명 이다.

약한 세대별 가설 (weak generational hypothesis) 에 따르면 1. 대부분의 객체가 짧은 시간만 살아 있다. 2. 늙은 객체가 젊은 객체를 참조할 일은 거의 없다.

### 5. 병렬 수집기

자바 8 이전의 JVM 디폴트로, young GC, full GC 모두 STW 를 일으킨다.

처리율에 최적화되어 있다.

```java
import java.util.concurrent.Executor;
import java.util.concurrent.Executors;

public class ModelAllocator implements Runnable {
    private volatile boolean shutdown = false;

    private double chanceOfLongLived = 0.02;
    private int multiplierForLongLived = 20;
    private int x = 1024;
    private int y = 1024;
    private int mbPerSec = 50;
    private int shortLivedMs = 100;
    private int nThreads = 8;
    private Executor exec = Executors.newFixedThreadPool(nThreads);

    @Override
    public void run() {
        final int mainSleep = (int) (1000.0 / mbPerSec);
         while (!shutdown) {
             for (int i = 0; i < mbPerSec; i++) {
                 ModelObjectAllocation to = new ModelObjectAllocation(x, y, lifetime());
                 exec.execute(to);
                 try {
                     Thread.sleep(mainSleep);
                 } catch (InterruptedException e) {
                     shutdown = true;
                 }
             }
         }
    }

    public int lifetime() {
        if (Math.random() < chanceOfLongLived) {
            return multiplierForLongLived * shortLivedMs;
        }

        return shortLivedMs;
    }

    public class ModelObjectAllocation implements Runnable {
        private final int[][] allocated;
        private final int lifeTime;

        public ModelObjectAllocation(final int x, final int y, final int liveFor) {
            allocated = new int[x][y];
            lifeTime = liveFor;
        }

        @Override
        public void run() {
            try {
                Thread.sleep(lifeTime);
                System.out.println(System.currentTimeMillis() + ": " + allocated.length);
            } catch (InterruptedException e) {}
        }
    }

    public static void main(String[] args) {
        ModelAllocator modelAllocator = new ModelAllocator();
        modelAllocator.run();
    }
}
```


