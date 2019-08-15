#### stream的使用

```java
List<Integer> list = new ArrayList<>();
list.add(1);
list.add(2);
list.add(3);
list.add(4);
list.add(4);
list.add(5);
Stream<Integer> stream = list.stream();//创建一个stream流对象
//stream.forEach((a)-> System.out.println(a));
Stream<Integer> integerStream = stream.filter((a) -> a > 2);//过滤出大于2的数据
//integerStream.forEach(a-> System.out.println(a));
Stream<String> stringStream = integerStream.map(a -> a.toString());//将Integer流转换成String流
//stringStream.forEach(a-> System.out.println(a.contains("3")));
Stream<Integer> integerStream1 = stringStream.map(a -> Integer.parseInt(a));//将String流转换成Integer流
//integerStream1.forEach(a-> System.out.println(a));
Stream<Integer> distinct = integerStream1.distinct();//将流进行去重
distinct.forEach(a-> System.out.println(a));
```

