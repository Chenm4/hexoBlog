---
title: 第九届蓝桥杯javaA
updated: '2023-03-21 08:10:50'
excerpt: >-
  分数每项是前一项的一半如果一共有项求这个和是多少结果用分数表示出来。类似_。当然这只是加了前项而已。分子分母要求互质。注意_需要提交的是已经约分过的分数中间任何位置不能含有空格。请不要填写任何多余的文字或符号。运用大正式biginteger分数_的最简分数形式publicclassmain{publicstaticvoidmain(string[]args){注意一旦使用大整数或大实数那么所有运算都需要使用大整数或大实数对象进行运算而不是原始类型大整数对象自带加减乘除幂运算求最大公约数的方法biginte
tags: []
categories: []
permalink: /post/ninth-blue-bridge-cup-javaa-zl6end.html
comments: true
---

# 分数

* 1/1 + 1/2 + 1/4 + 1/8 + 1/16 + .... 每项是前一项的一半，如果一共有20项，求这个和是多少，结果用分数表示出来。

  类似：3/2。当然，这只是加了前2项而已。分子分母要求互质。

  注意：需要提交的是已经约分过的分数，中间任何位置不能含有空格。请不要填写任何多余的文字或符号。

  # 运用大正式BigInteger

  分数2^19*2-1的最简分数形式

  * ```js
    public class Main {
        public static void main(String[] args) {
            // 注意，一旦使用大整数或大实数，那么所有运算都需要使用大整数或大实数对象进行运算，而不是原始类型
            // 大整数对象自带加减乘除幂运算求最大公约数的方法
            BigInteger two = new BigInteger("2"); // 用大整数对象表示2
            BigInteger den = two.pow(19); // 得到分母
            BigInteger num = den.multiply(two).subtract(BigInteger.valueOf(1)); // 得到分子
            BigInteger gcd = num.gcd(den);// 得到最大公约数用于约分
            // 约分
            num = num.divide(gcd);
            den = den.divide(gcd);
            System.out.println(num + "/" + den);
        }
    }
    ```

  * BigInteger类的方法：处理整数的运算

    1. BigInteger(String val) BigInteger类的构造方法，用于创建一个BigInteger对象，参数为字符串类型的数字。
    2. BigInteger pow(int exponent) BigInteger类的方法，用于返回此BigInteger的exponent次幂。
    3. BigInteger multiply(BigInteger val) BigInteger类的方法，用于返回此BigInteger与val的乘积。
    4. BigInteger subtract(BigInteger val) BigInteger类的方法，用于返回此BigInteger减去val的差。
    5. BigInteger valueOf(long val) BigInteger类的静态方法，用于返回指定long值的BigInteger。代码中指定值为1
    6. BigInteger gcd(BigInteger val) BigInteger类的方法，用于返回此BigInteger与val的最大公约数。
    7. BigInteger divide(BigInteger val) BigInteger类的方法，用于返回此BigInteger除以val的商。
  * BigDecimal类的方法：处理浮点数的运算

    1. BigDecimal(String val)：BigDecimal类的构造方法，用于创建一个BigDecimal对象，参数为字符串类型的数字。
    2. BigDecimal add(BigDecimal val)：返回此BigDecimal与val的和。
    3. BigDecimal subtract(BigDecimal val)：返回此BigDecimal减去val的差。
    4. BigDecimal multiply(BigDecimal val)：返回此BigDecimal与val的乘积。
    5. BigDecimal divide(BigDecimal val, int scale, RoundingMode roundingMode)：返回此BigDecimal除以val的商，scale为保留小数位数，roundingMode为舍入模式。
    6. BigDecimal pow(int n)：返回此BigDecimal的n次幂。
    7. BigDecimal abs()：返回此BigDecimal的绝对值。
    8. BigDecimal negate()：返回此BigDecimal的相反数。
    9. BigDecimal round(MathContext mc)：返回此BigDecimal按照指定精度进行舍入后的结果。

# 复数幂

设i为虚数单位。对于任意正整数n，(2+3i)^n ​^的实部和虚部都是整数。求 (2+3i)^123456^ 等于多少？ 即(2+3i)的123456次幂，这个数字很大，要求精确表示。

答案写成 "实部±虚部i" 的形式，实部和虚部都是整数（不能用科学计数法表示），中间任何地方都不加空格，实部为正时前面不加正号。(2+3i)^2 ​^写成: -5+12i，(2+3i)5 的写成: 122-597i。注意：需要提交的是一个很庞大的复数，不要填写任何多余内容。

“123456次幂，这个数字很大”这样的描述引导我们使用大整数BigInteger类，这也是本题的考点，因此最好掌握BigInteger和BigDecimal类。通过BigInteger提供的运算方法，循环计算，即可得到答案。

## 代码

```java
public class Main {
    public static void main(String... args) throws FileNotFoundException {
        BigInteger two = BigInteger.valueOf(2); // 用大整数表示2  BigInteger num = new BigInteger("2");也是一样
        BigInteger three = BigInteger.valueOf(3); // 用大整数表示3
        BigInteger real = BigInteger.valueOf(2); // 实部
        BigInteger imaginary = BigInteger.valueOf(3);// 虚部
        for (int i = 0; i < 123455; i++) // 不是123456
        {
            BigInteger temp1 = real;
            BigInteger temp2 = imaginary;
            real = temp1.multiply(two).subtract(temp2.multiply(three));
            imaginary = temp1.multiply(three).add(temp2.multiply(two));
        }
        // 输出到文件
        System.setOut(new PrintStream(new File("C:\\Users\\asus\\Desktop\\lanqiao.txt")));
        System.out.print(real);
        if (imaginary.compareTo(BigInteger.ZERO) > 0)
            System.out.print("+"); // 调整格式
        System.out.println(imaginary + "i");
    }
}
```

# 方格计数

在二维平面上有无数个1x1的小方格。我们以某个小方格的一个顶点为圆心画一个半径为 1000 的圆。 你能计算出这个圆里有多少个完整的小方格吗？

## 思路

以圆心为原点建立坐标系，根据对称性，只需要计算一个象限的结果再乘4即可。不妨来看第一象限，每个方格都可以用它的右上顶点来表示，在第一象限内，如果方格的右上顶点在圆内，则这个方格就在圆内。第一象限的方格右上顶点的横纵坐标范围都是[1,r]，用两重循环统计，若`x * x + y * y <= r * r`​，则在圆内。

## 代码

在第一象限中横坐标从1->1000遍历，纵坐标从1000->1遍历，只要满足小于等于，则ans+纵坐标

```java
public class Main {
    public static void main(String[] args) {
        long ans = 0;
        int r = 1000;
        for(int x = 1; x <= r; x++)
        {
            for(int y = r; y >= 1; y--)
            {
                if(x * x + y * y <= r * r)
                {
                    ans += y;
                    break;
                }
            }
        }
        System.out.println(ans * 4);
    }
}
```

3137548。

# 航班时间

小h前往美国参加了蓝桥杯国际赛。小h的女朋友发现小h上午十点出发，上午十二点到达美国，于是感叹到“现在飞机飞得真快，两小时就能到美国了”。仔细观察后发现飞机的起降时间都是当地时间。由于北京和美国东部有12小时时差，故飞机总共需要14小时的飞行时间。不久后小h的女朋友去中东交换。小h并不知道中东与北京的时差。但是小h得到了女朋友来回航班的起降时间。小h想知道女朋友的航班飞行时间是多少。

对于一个可能跨时区的航班，给定来回程的起降时间。假设飞机来回飞行时间相同，求飞机的飞行时间。

输入格式：

从标准输入读入数据。一个输入包含多组数据。输入第一行为一个正整数T，表示输入数据组数。每组数据包含两行，第一行为去程的起降时间，第二行为回程的起降时间。

起降时间的格式如下：

> h1:m1:s1 h2:m2:s2  
> 或  
> h1:m1:s1 h3:m3:s3 (+1)  
> 或  
> h1:m1:s1 h4:m4:s4 (+2)

表示该航班在当地时间h1时m1分s1秒起飞，第一种格式表示在当地时间**当日**h2时m2分s2秒降落，第二种格式表示在当地时间**次日**h3时m3分s3秒降落。第三种格式表示在当地时间**第三天**h4时m4分s4秒降落。

对于此题目中的所有以 hi:mi:si 形式给出的时间, 保证 ( 0<=hi<=23, 0<=mi,si<=59 )。

输出格式： 输出到标准输出。对于每一组数据输出一行一个时间hh:mm:ss，表示飞行时间为hh小时mm分ss秒。注意，当时间为一位数时，要补齐前导零。如三小时四分五秒应写为03:04:05。

样例输入：

> 3  
> 17:48:19 21:57:24  
> 11:05:18 15:14:23  
> 17:21:07 00:31:46 (+1)  
> 23:02:41 16:13:20 (+1)  
> 10:19:19 20:41:24  
> 22:19:04 16:41:09 (+1)

样例输出：

> 04:09:05  
> 12:10:39  
> 14:22:05

限制与约定： 保证输入时间合法，飞行时间不超过24小时。

资源约定： 峰值内存消耗（含虚拟机） < 256M CPU消耗 < 1000ms

请严格按要求输出，不要画蛇添足地打印类似：“请您输入...” 的多余内容。所有代码放在同一个源文件中，调试通过后，拷贝提交该源码。不要使用package语句。不要使用jdk1.7及以上版本的特性。主类的名字必须是：Main，否则按无效代码处理。

## 思路

根据题意，我们有一个去程的起飞时间记作t1，一个去程的降落时间记作t2，一个回程的起飞时间记作t3，一个回程的降落时间记作t4，此外还有两个未知数，时差c，以及飞行时间x。由此可得如下方程：

​![image](assets/image-20230220171934-az5afva.png)​

也就是说，对于每组的两行起降时间，我们都用降落时间减去起飞时间，再将这两个差值加起来后除以2。

为了方便，我们将得到的时间转化为秒来计算。

另外，本例使用了LocalTime存储时间，虽然这不是必须的，但还是建议熟悉LocalTime、LocalDate、LocalDateTime这些表示日期和时间的API，效率会得到提高。LocalTime[^1]LocalDate[^2]LocalDateTime[^3]

## 代码

```java
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int T = sc.nextInt();
        sc.nextLine();
        for (int i = 0; i < T; i++) {
            // 处理去程与回程的起降时间
            int time = helper(sc.nextLine()) + helper(sc.nextLine());
            time /= 2; // 得到飞行时间，接下来格式化输出
            LocalTime ans = LocalTime.of(time / 3600, time % 3600 / 60, time % 60);
            System.out.println(ans);
        }
    }

    // 处理起降时间
    private static int helper(String s) {
        //17:21:07 00:31:46 (+1)
        //23:02:41 16:13:20 (+1)
        String[] sa = s.split(" "); // 字符串包含起降时间以外还可能包含小括号表示次日之类的信息
        int res = getTime(sa[1]) - getTime(sa[0]); // 即t2-t1
        if (sa.length > 2) {//代表有括号
            String temp = sa[2].substring(2, sa[2].length() - 1);//调用sa[2].substring(2, sa[2].length() - 1)方法截取该元素的子字符串。其中，第一个参数2表示截取子字符串的起始位置，第二个参数sa[2].length() - 1表示截取子字符串的结束位置
            //去除(+和)只保留数字
            res += Integer.parseInt(temp) * 24 * 60 * 60; // 统一用秒表示时间，方便计算
            //调用Integer.parseInt(temp)方法将字符串temp解析成一个整数。
        }
        return res;
    }

    // 将起飞或降落时间转化为秒
    private static int getTime(String s) {
        // 年用y表示，月用M表示，日用d表示
        // 时用H表示，分用m表示，秒用s表示
        // 区分大小写
        LocalTime lt = LocalTime.parse(s, DateTimeFormatter.ofPattern("HH:mm:ss"));//用于将字符串解析成一个LocalTime对象
        int res = lt.getHour() * 3600 + lt.getMinute() * 60 + lt.getSecond();
        return res;
    }
}
```

# 三体攻击

三体人将对地球发起攻击。为了抵御攻击，地球人派出了 A × B × C 艘战舰，在太空中排成一个 A 层 B 行 C 列的立方体。其中，第 i 层第 j 行第 k 列的战舰（记为战舰 (i, j, k)）的生命值为 d(i, j, k)。

三体人将会对地球发起 m 轮“立方体攻击”，每次攻击会对一个小立方体中的所有战舰都造成相同的伤害。具体地，第 t 轮攻击用 7 个参数 lat, rat, lbt, rbt, lct, rct, ht 描述：所有满足 i ∈ [lat, rat],j ∈ [lbt, rbt],k ∈ [lct, rct] 的战舰 (i, j, k) 会受到 ht 的伤害。如果一个战舰累计受到的总伤害超过其防御力，那么这个战舰会爆炸。地球指挥官希望你能告诉他，第一艘爆炸的战舰是在哪一轮攻击后爆炸的。

输入格式： 从标准输入读入数据。第一行包括 4 个正整数 A, B, C, m；

第二行包含 A × B × C 个整数，其中第 (i − 1) × B × C + (j − 1) × C + k个数为 d(i, j, k)；

第 3 到第 m + 2 行中，第 (t + 2) 行包含 7 个正整数 lat, rat, lbt, rbt, lct, rct, ht。

输出格式： 输出到标准输出。输出第一个爆炸的战舰是在哪一轮攻击后爆炸的。保证一定存在这样的战舰。

样例输入：

> 2 2 2 3  
> 1 1 1 1 1 1 1 1  
> 1 2 1 2 1 1 1  
> 1 1 1 2 1 2 1  
> 1 1 1 1 1 1 2

样例输出： 2

样例解释： 在第 2 轮攻击后，战舰 (1,1,1) 总共受到了 2 点伤害，超出其防御力导致爆炸。

数据约定： 对于 10% 的数据，B = C = 1； 对于 20% 的数据，C = 1； 对于 40% 的数据，A × B × C, m ≤ 10, 000； 对于 70% 的数据，A, B, C ≤ 200； 对于所有数据，A × B × C ≤ 10^6, m ≤ 10^6, 0 ≤ d(i, j, k), ht ≤ 10^9。

资源约定： 峰值内存消耗（含虚拟机） < 256M ;CPU消耗 < 3000ms

## 思路

暴力枚举复杂度最坏情况下可达O(mABC)，数据规模取最大时显然会超时，必须进行优化。

通过暴力法，我们发现要对大量飞船的生命值进行m次修改，生命值用数组存储，这样涉及对区间频繁进行修改的场景，不难想到**差分数组**，关于一维、二维、三维差分数组的知识不在此讲述。本文使用二维差分数组解答，读者可自行改进为三维差分数组以达到更优的复杂度。

此外，由于飞船的生命值是单调递减的，因此可以用**二分查找**继续优化。对于第1轮（lo）到第m轮（hi）进攻，我们不断二分，直至找到飞船恰好开始爆炸的那轮。对于每个mid值，我们对飞船进行mid轮进攻，通过修改差分数组，实现以O(1)复杂度完成对区间的修改，然后我们检查飞船的生命值，判断是否有飞船爆炸。如此这般，不断缩小二分的范围直至得到答案。

最后来分析二维差分数组解法的复杂度。首先外层是二分查找的循环，我们有O(logm)，进入内层，我们要进行m轮进攻修改每层的差分数组（差分数组虽然是三维的，但使用的是二维的思想），这里是O(mA)，接着在同一层，m轮进攻完成后，我们要根据差分数组求各飞船的生命值，该过程中还需要借助前缀和数组，逐层地求生命值（因为差分使用的是二维的思想，所以只能逐层地求），这里的复杂度是O(ABC)。综上，总的时间复杂度是：O(logm(mA + ABC))。详情参考代码。

探测规则[^4]

## 代码

```java
public class Main {
    private static int A;
    private static int B;
    private static int C;
    private static int m;
    private static int[][][] HP; // 生命值，表示飞船的下标均从1开始计
    private static int[][][] diffArray;// 差分数组，下标从1开始计
    private static int[][] input; // 记录每轮攻击的7个三维参数

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        A = sc.nextInt();
        B = sc.nextInt();
        C = sc.nextInt();
        m = sc.nextInt();
        HP = new int[A + 1][B + 1][C + 1];
        for (int i = 1; i <= A; i++) {
            for (int j = 1; j <= B; j++) {
                for (int k = 1; k <= C; k++) {
                    HP[i][j][k] = sc.nextInt();
                }
            }
        }
        diffArray = new int[A + 1][B + 1][C + 1];
        input = new int[m][7];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < 7; j++) {
                input[i][j] = sc.nextInt();
            }
        }
        int lo = 0, hi = m;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            int[][][] tempHP = copy(HP);
            int[][][] tempDiffArray = copy(diffArray);
            attack(lo, mid);
            if (check()) // 第mid轮攻击已爆炸，减少攻击轮数
            {
                // 不能继续攻击，所以要恢复两个数组
                HP = tempHP;
                diffArray = tempDiffArray;
                hi = mid;
            } else // 攻击次数还不够，可以继续攻击
            {
                lo = mid + 1;
            }
        }
        System.out.println(lo + 1);
    }

    private static int[][][] copy(int[][][] a) {
        int[][][] res = new int[A + 1][B + 1][C + 1];
        for (int i = 1; i <= A; i++) {
            for (int j = 1; j <= B; j++) {
                for (int k = 1; k <= C; k++)
                    res[i][j][k] = a[i][j][k];
            }
        }
        return res;
    }

    // 攻击从第begin次累计到第end次
    private static void attack(int begin, int end) {
        for (int i = begin; i <= end; i++) {
            int lat = input[i][0];
            int rat = input[i][1];
            int lbt = input[i][2];
            int rbt = input[i][3];
            int lct = input[i][4];
            int rct = input[i][5];
            int ht = input[i][6];
            helper(lat, rat, lbt, rbt, lct, rct, ht); // 每进攻一次就修改一次差分数组
        }
        // 完成这些进攻后，根据差分数组求目前所有飞船的生命值
        for (int i = 1; i <= A; i++) {
            int[][] prefixSum = new int[B + 1][C + 1]; // 求每一层的前缀和数组
            for (int j = 1; j <= B; j++) {
                for (int k = 1; k <= C; k++) {
                    prefixSum[j][k] = diffArray[i][j][k] + prefixSum[j][k - 1] + prefixSum[j - 1][k]
                            - prefixSum[j - 1][k - 1];
                }
            }
            for (int j = 1; j <= B; j++) { // 根据前缀和修改飞船生命值
                for (int k = 1; k <= C; k++) {
                    HP[i][j][k] -= prefixSum[j][k];
                }
            }
        }
    }

    // 检查是否有飞船爆炸
    private static boolean check() {
        for (int i = 1; i <= A; i++) {
            for (int j = 1; j < B; j++) {
                for (int k = 1; k <= C; k++) {
                    if (HP[i][j][k] < 0)
                        return true;
                }
            }
        }
        return false;
    }

    // 每次进攻对差分数组进行修改
    private static void helper(int la, int ra, int lb, int rb, int lc, int rc, int val) {
        for (int i = la; i <= ra; i++) {
            diffArray[i][lb][lc] += val;
            if (rb < B)
                diffArray[i][rb + 1][lc] -= val;
            if (rc < C)
                diffArray[i][lb][rc + 1] -= val;
            if (rb < B && rc < C)
                diffArray[i][rb + 1][rc + 1] += val;
        }
    }
}
```

[^1]: ## LocalTime

    LocalTime是Java 8中的日期时间类，用于表示不带时区信息的时间。

    下面是​LocalTime​类的一些常见用法：

    ### 创建一个LocalTime对象

    可以使用​now()​方法获取当前时间：

    LocalTime now = LocalTime.now(); // 获取当前时间

    也可以使用静态的​of()​方法根据小时、分钟、秒来创建一个​LocalTime​对象：

    LocalTime time1 = LocalTime.of(12, 30); // 创建12:30的时间
    LocalTime time2 = LocalTime.of(16, 45, 30); // 创建16:45:30的时间

    ### 获取时间的小时、分钟、秒

    可以使用getHour()、getMinute()和getSecond()方法分别获取时间的小时、分钟和秒：

    int hour = now.getHour();  
    int minute = now.getMinute();  
    int second = now.getSecond();

    ### 修改时间的小时、分钟、秒

    可以使用​withHour()​、​withMinute()​和​withSecond()​方法修改时间的小时、分钟和秒：

    LocalTime modifiedTime = now.withHour(13).withMinute(45).withSecond(0); // 修改时间为13:45:00

    ### 时间的加减

    可以使用​plusHours()​、​plusMinutes()​和​plusSeconds()​方法向时间中添加小时、分钟和秒，也可以使用​minusHours()​、​minusMinutes()​和​minusSeconds()​方法从时间中减去小时、分钟和秒：

    LocalTime newTime = now.plusHours(2).minusMinutes(30); // 将时间增加2小时并减去30分钟

    ### 判断两个时间的先后关系

    可以使用​isBefore()​、​isAfter()​和​equals()​方法判断两个时间的先后关系：

    LocalTime time1 = LocalTime.of(12, 30);  
    LocalTime time2 = LocalTime.of(13, 45);  
    boolean before = time1.isBefore(time2); // true  
    boolean after = time2.isAfter(time1); // true  
    boolean equals = time1.equals(time2); // false

    以上是​LocalTime​类的一些常见用法，通过这些方法可以方便地处理不带时区的时间。


[^2]: ## LocalDate

    `java.time.LocalDate`​​ 是 Java 8 中的日期类，它表示本地日期，即没有时区偏移或时间部分的日期。以下是 `java.time.LocalDate`​​ 类的基本用法：

    ### 创建一个 LocalDate 实例

    可以使用静态方法 `now()`​​ 来获取当前日期：

    LocalDate now = LocalDate.now(); // 获取当前日期

    也可以通过指定年、月、日来创建 `LocalDate`​​ 实例：

    LocalDate date1 = LocalDate.of(2022, 2, 19); // 2022年2月19日  
    LocalDate date2 = LocalDate.parse("2022-02-19"); // 从字符串解析日期

    ### 获取日期信息

    可以使用以下方法获取 `LocalDate`​​ 实例中的日期信息：

    int year = date.getYear(); // 获取年份  
    Month month = date.getMonth(); // 获取月份 或者int month = date.getMonth().getValue();  
    int dayOfMonth = date.getDayOfMonth(); // 获取日期  
    int dayOfWeek = date.getDayOfWeek().getValue(); // 获取星期几，返回 1（星期一）到 7（星期日）的数字

    ​​LocalDate​ 类中的 ​getDayOfWeek()​ 方法返回一个 ​DayOfWeek​ 枚举类型的值，表示给定日期是星期几。例如，如果 ​date​ 是一个 ​LocalDate​ 对象，那么 ​date.getDayOfWeek()​ 将返回一个 ​DayOfWeek​ 枚举值，表示 ​date​ 日期是星期几，例如 ​DayOfWeek.MONDAY​ 表示星期一。  
    int dayOfYear = date.getDayOfYear(); // 获取这一年中的第几天

    ### 操作日期

    可以使用 `plus`​​ 和 `minus`​​ 方法来增加或减少日期：

    LocalDate date1 = LocalDate.now();  
    LocalDate date2 = date1.plusDays(1); // 增加一天  
    LocalDate date3 = date1.minusMonths(1); // 减少一个月

    ### 格式化日期

    可以使用 `DateTimeFormatter`​​ 类来将 `LocalDate`​​ 格式化为字符串：

    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd");  
    String formattedDate = date.format(formatter); // 格式化日期

    ### 比较日期

    可以使用 `compareTo`​​ 方法比较两个日期的大小：

    LocalDate date1 = LocalDate.of(2022, 2, 19);  
    LocalDate date2 = LocalDate.of(2022, 2, 20);  
    int result = date1.compareTo(date2); // 比较日期，返回 -1（date1 在 date2 之前）、0（日期相同）或 1（date1 在 date2 之后）

    以上是 `java.time.LocalDate`​​ 类的基本用法，还有其他更高级的用法和类，如 `java.time.LocalDateTime`​​ 和 `java.time.ZonedDateTime`​​，可以用来处理带有时区的日期和时间。


[^3]: ## LocalDateTime

    ​​LocalDateTime​是Java 8中的日期时间类，用于表示不带时区信息的日期和时间。

    ​​LocalDate​表示日期，而​LocalTime​表示时间。​LocalDateTime​同时包含日期和时间，因此可以看作是LocalDate和LocalTime的扩充。

    下面是​LocalDateTime​类的一些常见用法：

    ### 创建一个​LocalDateTime​​对象

    可以使用`now()`​方法获取当前日期和时间：

    LocalDateTime now = LocalDateTime.now(); // 获取当前日期和时间

    也可以使用静态的`of()`​方法根据年、月、日、小时、分钟、秒来创建一个`LocalDateTime`​对象：

    LocalDateTime datetime1 = LocalDateTime.of(2023, 2, 20, 12, 30); // 创建2023年2月20日12:30的日期和时间  
    LocalDateTime datetime2 = LocalDateTime.of(2023, 2, 20, 16, 45, 30); // 创建2023年2月20日16:45:30的日期和时间

    LocalTime.parse()方法用于将字符串解析成一个`LocalTime`​对象

    String timeStr = "10:15:30";  
    LocalTime time = LocalTime.parse(timeStr);  
    System.out.println(time); // 输出10:15:30

    ### 获取日期和时间的年、月、日、小时、分钟、秒

    可以使用`getYear()`​、`getMonthValue()`​、`getDayOfMonth()`​、`getHour()`​、`getMinute()`​和`getSecond()`​方法分别获取日期和时间的年、月、日、小时、分钟和秒：

    int year = now.getYear();  
    int month = now.getMonthValue();  
    int day = now.getDayOfMonth();  
    int hour = now.getHour();  
    int minute = now.getMinute();  
    int second = now.getSecond();

    ### 修改日期和时间的年、月、日、小时、分钟、秒

    可以使用`withYear()`​、`withMonth()`​、`withDayOfMonth()`​、`withHour()`​、`withMinute()`​和`withSecond()`​方法分别修改日期和时间的年、月、日、小时、分钟和秒：

    LocalDateTime modifiedDatetime = now.withYear(2024).withMonth(3).withDayOfMonth(15)  
            .withHour(14).withMinute(30).withSecond(0); // 修改日期和时间为2024年3月15日14:30:00

    ### 日期和时间的加减

    可以使用`plusYears()`​、`plusMonths()`​、`plusDays()`​、`plusHours()`​、`plusMinutes()`​和`plusSeconds()`​方法向日期和时间中添加年、月、日、小时、分钟和秒，也可以使用`minusYears()`​、`minusMonths()`​、`minusDays()`​、`minusHours()`​、`minusMinutes()`​和`minusSeconds()`​方法从日期和时间中减去年、月、日、小时、分钟和秒：

    LocalDateTime newDatetime = now.plusYears(1).minusMonths(2).plusDays(10)  
            .plusHours(2).minusMinutes(30).plusSeconds(15); // 将日期和时间增加1年10天2小时并减去2个月30分钟15秒

    ### 判断两个日期和时间的先后关系

    可以使用​isBefore()​、​isAfter()​和​equals()​方法判断两个日期和时间的先后关系：

    LocalDateTime datetime1 = LocalDateTime.of(2023, 2, 20, 12, 30);  
    LocalDateTime datetime2 = LocalDateTime.of(2023, 2, 20, 16, 45, 30);  
    boolean before = datetime1.isBefore(datetime2); // true  
    boolean after = datetime2.isAfter(datetime1); // true


[^4]: ## 探测规则

    根据已有块引的锚文本/定义块文本、块的命名和别名作为关键字搜索，如果在当前内容块的纯文本元素上搜索到这些关键字，那么就会作为虚拟引用。
