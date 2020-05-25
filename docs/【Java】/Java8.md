# Java8

- [ ] 不理解

valueOf不是接受一个i，返回一个Integer吗，那对这个Integer[]，每项都应用这个方法，怎么感觉反过来了

```java
 // Integer[] 转 int[]
        int[] arr = Arrays.stream(integerArr).mapToInt(Integer::valueOf).toArray();
```

[待看博客](https://www.jianshu.com/p/d93f3ebd41a6)