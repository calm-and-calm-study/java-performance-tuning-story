메서드 특정부분에만 동기화하는 것이 더 효율적일 경우에 메서드 안에 동기화 블록을 사용할 수 있다.

1. 인스턴스 메서드 내부의 synchronized block
  ```java
  public class Contribution {
      private int amount = 0;
      public void donate() {
          synchronized(this){
            this.amount++;
          }
      }

      public synchronized void refund() {
          amount--;
      }
  }
  ```

  - `synchronized(this)`에서 `this` 는 `donate()` 를 호출한 객체이다. 동기화 블록 안에 전달된 객체를 모니터 객체(a monitor object)라고 하고, 이 모니터 객체를 기준으로 동기화가 된다. 
  - 같은 모니터 객체를 기준으로 synchronized block 안의 코드를 한번에 한 쓰레드만 실행 가능하다.
      - 만약 동기화 블럭 안에 this 대신 다른 객체를 전달한다면 동기화 기준이 달라지므로 한 쓰레드에서 donate()와 refund() 두 메서드를 동시에 실행 가능하므로 synchronized 키워드 보다 synchronized block이 좀 더 유연하다고 할 수 있다.

2. static 메소드 내부의 synchronized block
    
    ```java
    public class Contribution {
        private static int amount = 0;
        public static void donate() {
    				synchronized(Contribution.class){
    	        this.amount++;
    		    }
    		}
    		
    		public static synchronized void refund() {
    				amount--;
    		}
    }
    ```
    
    - 한 시점에 한 쓰레드만 동기화된 메서드 `donate()`, `refund()` 중 하나만을 실행할 수 있다.
    - `synchronized(Contribute.class)`에 `Contribute.class`아닌 다른 객체를 전달하면, 한 쓰레드에서 동시에 각 메소드를 실행할 수 있다.
    
    
    ### Synchronized(Object)
    `synchronized(this)` 는 모든 block에 락이 걸리는 반면에 `synchronized(Object)` 방식은 블록마다 다른 lock이 걸리게 할 수 있다.

    1. synchronized(this) : this를 기준으로 모든 synchronized block에 락이 걸린다.
      ```java
          public class SynchronizedBlock {
              public static void main(String[] args) {
                  SynchronizedBlock block = new SynchronizedBlock();

                  Thread thread1 = new Thread(() -> {
                      System.out.println("thread1 start =======");
                      block.method1("thread1");
                      System.out.println("thread1 end =======");
                  });

                  Thread thread2 = new Thread(() -> {
                      System.out.println("thread2 start =======");
                      block.method2("thread2");
                      System.out.println("thread2 end =======");
                  });
                  thread1.start();
                  thread2.start();
              }

              private void method1(String msg) {
                  synchronized (this) {
                      System.out.println(msg + "의 method1: " + LocalDateTime.now());
                      try {
                          Thread.sleep(5000);
                      } catch (InterruptedException e) {
                          e.printStackTrace();
                      }
                  }
              }

              private void method2(String msg) {
                  synchronized (this) {
                      System.out.println(msg + "의 method2: " + LocalDateTime.now());
                      try {
                          Thread.sleep(5000);
                      } catch (InterruptedException e) {
                          e.printStackTrace();
                      }
                  }
              }
          }
          ```

          ```
          thread1 start =======
          thread2 start =======
          thread1의 method1: 2022-08-19T20:28:23.938160
          thread1 end =======
          thread2의 method2: 2022-08-19T20:28:28.949814
          thread2 end =======
          ```

  - 결과를 보면 method1과 method2의 synchronized(this) 블록끼리 동기화가 되고 있다.

     2. synchronized(Object)
    
        ```java
        public class SynchronizedBlock {

            private final Example ex1 = new Example();
            private final Example ex2 = new Example();

            public static void main(String[] args) {
                SynchronizedBlock block = new SynchronizedBlock();

                Thread thread1 = new Thread(() -> {
                    System.out.println("thread1 start =======");
                    block.method1("thread1");
                    System.out.println("thread1 end =======");
                });

                Thread thread2 = new Thread(() -> {
                    System.out.println("thread2 start =======");
                    block.method2("thread2");
                    System.out.println("thread2 end =======");
                });
                thread1.start();
                thread2.start();
            }

            private void method1(String msg) {
                synchronized (ex1) {
                    System.out.println(msg + "의 method1: " + LocalDateTime.now());
                    try {
                        Thread.sleep(5000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }

            private void method2(String msg) {
                synchronized (ex2) {
                    System.out.println(msg + "의 method2: " + LocalDateTime.now());
                    try {
                        Thread.sleep(5000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
        ```

        ```
        thread1 start =======
        thread2 start =======
        thread2의 method2: 2022-08-19T20:36:17.898234
        thread1의 method1: 2022-08-19T20:36:17.898233
        thread1 end =======
        thread2 end =======
        ```

      - 위 결과를 보면 각 쓰레드 간의 동기화가 지켜지지 않는다. 따라서 this 대신 다른 객체를 인자로 넘겨주면 동시에 lock이 걸려야 하는 부분을 따로 지정해 줄 수 있다.
      
