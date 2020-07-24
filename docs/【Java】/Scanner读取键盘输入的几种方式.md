# Scanner读取键盘输入的几种方式

首先：`Scanner in = new Scanner(System.in);`

最后记得：`in.close()`

> 读取n，m

```java
int n = in.nextInt();
int m = in.nextInt();
System.out.println("n= " + n + " m= " + m);
```

> 读取一个len，然后len个数字，读取一个二维数组也一样

```java
int len = in.nextInt();
int[] arr = new int[len];
for (int i = 0; i < len; ++i) {
    arr[i] = in.nextInt();
}
System.out.println(Arrays.toString(arr));
// 二维数组用deepToString
// System.out.println(Arrays.deepToString(a));
```

> 读字符串(空格、tab分开)

```java
String s1 = in.next();
String s2 = in.next();
System.out.println("s1= " + s1 + " s2= " + s2);
```

> 读一行，然后用函数切分空格

```java
String line = in.nextLine();
System.out.println("line= " + line);
String[] split = line.split(" ");
for (String s : split) {
    System.out.print(s + "---");
}
```

> 一个个字符读。-1 代表文件尾

```java
// 方式一
char ch;
int tmp;
while ((tmp = System.in.read()) != -1) {
    ch = (char)tmp;
    System.out.println(ch);
}

// 方式二
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
int tmp;
while ((tmp = br.read()) != -1) {
    System.out.println((char)tmp);
}
```



## 扩展

> next() 方法默认的token分隔符（*delimiter*）为 `空格、回车和tab`

也就是说next()方法是将输入用delimeter(空格，回车和Tab)，**切割成一个一个Token**

!> xxx分隔符xxx分隔符。光标会读到分隔符前，也就是xxx和分隔符之间

注：如果输入的第一个字符就是分隔符（空格、回车和tab），则**会忽略**

可以使用`Scanner.useDelimiter(“xxx”)`指定特定的String作为分隔符

> next或者nextxxx 和 nextLine搭配的注意

所有nextXXX()的实现都是next()方法，只是对得到Token进行了再判断而已

所以这里就分两种来说

next或者nextxxx：读到分隔符

nextLine：返回当前光标位置到行末（也就是换行符的前面）的内容，将光标换行到下一行开头

!> 刚刚说了默认的delimeter有：空格，回车和Tab。都有回车，所以这两者就差别就差了nextLine会换行到下一行开头，next、nextxxx不会，就存在这些函数搭配用可能读得不对（因为没换行）

?> 解决：要么不混合用，统一用一种；要么自己判断情况用nextLine把光标换行一下

具体参考博客：https://blog.csdn.net/ProLayman/article/details/89103252