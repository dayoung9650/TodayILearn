# 병렬데이터 처리와 성능

```java
ExecutorService newFixedThreadPool = Executors.newfixedThreadPool(5);

//pork
Callable<Integer> task = new Callavle<Integer>(){
  public Integer call() throws Exception{
    return a + b;
  }
}
Future<Integer> submit = newFixedThreadPool.submit(task);
Future<Integer> submit2 = newFixedThreadPool.submit(task);
Future<Integer> submit3 = newFixedThreadPool.submit(task);
Future<Integer> submit4 = newFixedThreadPool.submit(task);
Future<Integer> submit5 = newFixedThreadPool.submit(task);

//join
Integer result = submit.get();
Integer result2 = submit2.get();
Integer result3 = submit3.get();
Integer result4 = submit4.get();
Integer result5 = submit5.get();

result2+result3 ...
```

포크 : 나눔

조인 : 합침

스트림이 병렬처리를 할 땐 포크조인풀을 사용함

```	java
List<String> lit = new ArrayList<String>();
Strean<String> stream = list.stream();
stream.parallel().forEach(x -> system.out.println(x));
```



포크조인풀 사용하는 예시

