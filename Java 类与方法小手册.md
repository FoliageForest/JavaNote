# java.io.File 类

示例:

```java
import java.io.File;

public class Test {
    public static void main(String[] args) {
        File file = new File("C:\\Users\\肖瑞\\11_Top_FF");
        String[] list = file.list();
        System.out.println(list.length);
        System.out.println("===============");
        for (String s :
                list) {
            System.out.println(s);
        }
        System.out.println("===============");
    }
}
```


#### 小练习: 读取 `C:\Windows` 文件夹下的文件和子文件夹信息

```java
import java.io.File;
import java.util.Scanner;

public class Test {
    public static void main(String[] args) {
        File file = new File(new Scanner(System.in).next());
        System.out.println("isDirectory: " + file.isDirectory());
        if (!file.isDirectory()) {
            return;
        }
        String[] list = file.list();
        if (list == null) {
            return;
        }
        for (String s : list) {
            System.out.println(s);
        }
    }
}
```

## mkdir() 方法

#### 小练习: 批量创建文件夹, 分别以数字 1-20 命名

使用 `java.io.File` 的 `public boolean mkdir()` 方法

```java
import java.io.File;
import java.util.ArrayList;
import java.util.Scanner;

public class Test {
    public static void main(String[] args) {
        File dir = new File(new Scanner(System.in).next());

        if (!dir.exists()) {
            System.out.println("文件或目录 \"" + dir.getPath() + "\" 不存在");
            return;
        }

        System.out.println("创建子文件夹中......");
        ArrayList<String> yStrings = new ArrayList<>();
        ArrayList<String> nStrings = new ArrayList<>();

        for (int i = 0; i < 20; i++) {
            File childDir = new File(dir.getPath() + "\\" + String.format("%02d", i + 1));
            if (childDir.mkdir()) {
                yStrings.add(childDir.getName());
            } else {
                nStrings.add(childDir.getName());
            }
        }

        System.out.println(yStrings);
        System.out.println(nStrings);
    }
}
```

## File 文件输入输出流

```java
public class test08 {
    public static void main(String[] args) {
        byte[] b = new byte[] { 1, -1, 25, -22, -5, 23 }; // 创建数组
        ByteArrayInputStream bais = new ByteArrayInputStream(b, 0, 6); // 创建字节数组输入流
        int i = bais.read(); // 从输入流中读取下一个字节，并转换成int型数据
        while (i != -1) { // 如果不返回-1，则表示没有到输入流的末尾
            System.out.println("原值=" + (byte) i + "\t\t\t转换为int类型=" + i);
            i = bais.read(); // 读取下一个
        }
    }
}
```

从键盘读入文件路径和文件内容，将读到的文件内容输出到文件

```java
import java.io.*;
import java.nio.charset.StandardCharsets;
import java.util.Scanner;

/*
来源: https://juejin.cn/post/7166085870720647198
 */

public class WriteToFilesAppMain {
    private static final Scanner SCANNER = new Scanner(System.in);

    public static void main(String[] args) throws IOException {
        File targetFile = createFile();
        writeToFile(targetFile);
        System.out.println("程序执行结束");
    }

    public static void writeToFile(File targetFile) throws IOException {
        try (
                // 创建一个 outputstream 建立一个从程序到文件的byte数据传输流
                FileOutputStream fileOutputStream = new FileOutputStream(targetFile);
                // 创建一个可以使用 outputstream 的 Writer，并制定字符集，这样程序就能一个一个字符地写入
                OutputStreamWriter outputStreamWriter = new OutputStreamWriter(fileOutputStream, StandardCharsets.UTF_8);
                // 使用 PrintWriter, 可以方便的写入一行字符
                PrintWriter printWriter = new PrintWriter(outputStreamWriter)) {
            System.out.println("输入的内容会实时写入文件，如果输入空行则结束");
            while (true) {
                String trim = SCANNER.nextLine().trim();
                System.out.println("输入内容为: " + trim);
                if (trim.trim().isBlank()) {
                    System.out.println("输入结束");
                    break;
                } else {
                    printWriter.println(trim);
                    // 真正用的时候不要写一行就flush() 这里只是演示
                    printWriter.flush();
                }
            }
            // 平时用的时候放在外面 flush
            // printWriter.flush();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static File createFile() throws IOException {
        System.out.println("请输入文件名: ");
        String fileName = SCANNER.nextLine().trim();
        File f = new File(fileName);
//        File f = new File("." + File.separator + fileName + ".txt");
        if (f.isFile()) {
            System.out.println("目标文件存在，删除：" + f.delete());
        }
        System.out.println(f.createNewFile());
        return f;
    }

}
```

读取计算机中的文件。将文件内容按行打印输出到控制台。如果存在小写字母，则应该将小写字母转为大写字母后再进行输出。

```java
import java.io.BufferedReader;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.nio.charset.StandardCharsets;

/*
来源:
https://juejin.cn/post/7166085870720647198
https://codeantenna.com/a/OBbR954kiJ
 */

public class ReadStringFromFileAppMain {
    private static final String SOURCE_FILE_NAME = "C:\\Users\\肖瑞\\11_Top_FF\\随时可删除\\Java File 类测试文件夹\\test\\a.txt";

    public static void main(String[] args) {
        File sourceFile = new File(SOURCE_FILE_NAME);
        readLineFromFile(sourceFile);
    }

    private static void readLineFromFile(File sourceFile) {
        try (
                // 建立从文件到程序的数据输入(input)流
                FileInputStream fileInputStream = new FileInputStream(sourceFile);
                // 用 InputStreamReader 包装 byte流，并指定字符编码，让它能够将读出的byte转为字符
                InputStreamReader inputStreamReader = new InputStreamReader(fileInputStream, StandardCharsets.UTF_8);
                //  增加缓冲功能，输入输出效率更高，并且可以一次读一行
                BufferedReader bufferedReader = new BufferedReader(inputStreamReader)) {
            String line = null;
            while ((line = bufferedReader.readLine()) != null) {
                System.out.println(line.trim().toUpperCase());
            }
            // 还可以从 bufferedReader 里获取 Stream，然后通过流操作+Lambda的形式完成
            // bufferedReader.lines().map(String::trim).map(String::toUpperCase).forEach(System.out::println);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

# java.io.FileOutputStream 类

#### 小示例: 向文本文件中写入内容, 再读取出来

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.util.Scanner;

public class Test2 {
    public static void main(String[] args) {
        File file = new File(new Scanner(System.in).next()); // 创建文件对象
        if (!file.exists() || !file.isFile()) {
            return;
        }
        try {
            FileOutputStream fileOutputStream = new FileOutputStream(file); // 创建输出流
            byte[] bytes = "你好。\nHello".getBytes(); // 写入内容的字节数组
            fileOutputStream.write(bytes); // 将字节写入文件中
            fileOutputStream.close(); // 关闭流

            FileInputStream fileInputStream = new FileInputStream(file); // 创建输入流
            byte[] bytes1 = new byte[1024]; // 缓存字节数组
            int len = fileInputStream.read(bytes1); // 将文件信息读入缓存数组中
            System.out.println(new String(bytes1, 0, len)); // 将字节转为字符串输出
            fileInputStream.close(); // 关闭流
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

# java.io.FileWriter 类

#### 小示例: 使用字符流读写文本文件

```java
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.util.Scanner;

/*
Java 从入门到精通（第6版）
15.3.2 FileReader 和 FileWriter 类
【例15.3】
 */

public class Test {
    public static void main(String[] args) {
        File file = new File(new Scanner(System.in).next()); // 创建文件对象

        try {
            FileWriter fileWriter = new FileWriter(file); // 创建字符输出流
            String word = "小姑娘多萝茜和她的小狗托托被龙卷风刮到了芒奇金国的故事。"; // 写入的字符串
            fileWriter.write(word); // 将字符串写入文件中
            fileWriter.close(); // 关闭流
        } catch (IOException e) {
            e.printStackTrace();
        }

        try {
            FileReader fileReader = new FileReader(file); // 创建字符输入流
            char[] chars = new char[1024]; // 缓存字符数组
            int len = fileReader.read(chars); // 将文件中的字符读入缓存数组中
            System.out.println(new String(chars, 0, len)); // 将字符转为字符串输出
            fileReader.close(); // 关闭流
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```

# java.io.PrintWriter 类

#### 小示例

```java
/*
Introduction to Java Programming and Data Structures_GE, 11e
Listing 12.13
WriteData.java

page 502
 */

public class WriteData {
    public static void main(String[] args) throws java.io.IOException {
        java.io.File file = new java.io.File("scores.txt");
        if (file.exists()) {
            System.out.println("File already exists");
            System.exit(1);
        }

        java.io.PrintWriter printWriter = new java.io.PrintWriter(file);

        printWriter.print("John T Smith ");
        printWriter.println(90);
        printWriter.print("Eric K Jones ");
        printWriter.println(85);

        /*
        The close() method must be used to close the file.
        If this method is not invoked, the data may not be saved properly in the file.
         */
        printWriter.close();
    }
}
```

# java.io.BufferedWriter 类

#### 小示例: 使用缓冲流读写文本文件

```java
/*
Java 从入门到精通（第6版） 明日科技
15.4.2 BufferedReader 和 BufferedWriter 类
【例 15.5】
使用缓冲流读写文本文件
 */

import java.io.*;
import java.util.Scanner;

public class BufferedDemo {
    public static void main(String[] args) {
        File file = new File(new Scanner(System.in).next()); // 创建文件对象

        try {
            FileWriter fileWriter = new FileWriter(file); // 文件字符输出流
            BufferedWriter bufferedWriter = new BufferedWriter(fileWriter); // 换成输出流
            for (String s : new String[]{"好久不见", "最近好吗", "常联系"}) {
                bufferedWriter.write(s); // 写入字符串
                bufferedWriter.newLine(); // 写入一个换行符
            }
            bufferedWriter.close(); // 关闭缓冲输出流
            fileWriter.close(); // 关闭文件字符输出流
        } catch (IOException e) {
            e.printStackTrace();
        }

        try {
            FileReader fileReader = new FileReader(file); // 文件字符输入流
            BufferedReader bufferedReader = new BufferedReader(fileReader); // 缓冲输入流
            String tmp = null; // 作为缓冲的临时字符串
            int i = 1; // 行数
            // 从文件中读出一行, 如果读出的内容不为 null, 则进入循环
            while ((tmp = bufferedReader.readLine()) != null) {
                System.out.println(String.format("第 %02d 行: ", i) + tmp); // 输出读取的内容
                i++; // 行数递增
            }
            bufferedReader.close(); // 关闭缓冲输入流
            fileReader.close(); // 关闭文件字符输入流
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```

# java.util.Scanner 类

#### 小示例 new Scanner(File source)

```java
import java.util.Scanner;

public class Test {
    public static void main(String[] args) throws Exception {
        java.io.File file = new java.io.File("scores.txt");
        if (file.exists()) {
            System.out.println("File already exists");
            System.exit(1);
        }

        try (java.io.PrintWriter printWriter = new java.io.PrintWriter(file)) {
            printWriter.print("John T Smith ");
            printWriter.println(90);
            printWriter.print("Eric K Jones ");
            printWriter.println(85);
        }

        Scanner input = new Scanner(file);

        while (input.hasNext()) {
            String firstName = input.next();
            String mi = input.next();
            String lastName = input.next();
            int score = input.nextInt();
            System.out.println(firstName + " " + mi + " " + lastName + " " + score);
        }

        input.close();

    }
}
```

# java.io.DataOutputStream 类

#### 小示例: 使用缓冲流读写文本文件

```java
/*
Java 从入门到精通（第6版） 明日科技
15.5 数据输入/输出流
【例 15.6】
使用数据流读写文本文件
 */

import java.io.*;
import java.util.Scanner;

public class JavaStreamReader {
    public static void main(String[] args) {
        File file = new File(new Scanner(System.in).next());
        try {
            // 创建 FileOutputStream 对象, 指定要向其中写入数据的文件
            FileOutputStream fileOutputStream = new FileOutputStream(file);
            // 创建 DataOutputStream 对象, 用来向文件中写入数据
            DataOutputStream dataOutputStream = new DataOutputStream(fileOutputStream);
            dataOutputStream.writeUTF("使用 writeUTF 方法写入数据"); // 将字符串写入文件
            dataOutputStream.writeDouble(12.345); // 将 double 数据写入文件
            dataOutputStream.writeInt(123); // 将 int 数据写入文件
            dataOutputStream.writeBoolean(true); // 将 boolean 数据写入文件
            dataOutputStream.close();
            fileOutputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

        try {
            // 创建 FileInputStream 对象, 指定要从中读取数的文件
            FileInputStream fileInputStream = new FileInputStream(file);
            // 创建 DataInputStream 对象, 用来从文件中读取文件
            DataInputStream dataInputStream = new DataInputStream(fileInputStream);
            System.out.println("readUTF() 方法读取数据: " + dataInputStream.readUTF()); // 读取字符串
            System.out.println("readDouble() 方法读取数据: " + dataInputStream.readDouble()); // 读取 double 数据
            System.out.println("readInt() 方法读取数据: " + dataInputStream.readInt()); // 读取 int 数据
            System.out.println("readBoolean() 方法读取数据: " + dataInputStream.readBoolean()); // 读取 boolean 数据
            dataInputStream.close();
            fileInputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```

# String 类

## 通过字节数组构造字符串

```java
public class Test {
    public static void main(String[] args) {
        byte[] bytes = "0123456789".getBytes();
        System.out.println(new String(bytes));
        System.out.println(new String(bytes, 0, bytes.length - 1));
        System.out.println(new String(bytes, 1, bytes.length - 1));
    }
}
```

## 增

`public String concat(String str)`

```java
public class Test {
    public static void main(String[] args) {
        String s1 = "hello, ";
        String s2 = "world";
        String s = s1.concat(s2);
        System.out.println(s);
    }
}
```


## 查

- `public char charAt(int index)` 获取指定位置的字符

```java
public class Test {
    public static void main(String[] args) {
        String s = "www.runoob.com";
        char result = s.charAt(6);
        System.out.println(result); // n
    }
}
```

- substring 方法

`public String substring(int beginIndex)`

> 将字符串从索引号为 `beginIndex` 开始截取，一直到字符串末尾。（注意索引值从 0 开始）
> 来源: <https://blog.csdn.net/zjx2016/article/details/74557301>

`public String substring(int beginIndex, int endIndex)`


> 从索引号 `beginIndex` 开始到索引号 `endIndex` 结束（返回结果包含索引为 `beginIndex` 的字符不包含索引为 `endIndex` 的字符）
> 来源: <https://blog.csdn.net/zjx2016/article/details/74557301>

```java
public class Test {
    public static void main(String[] args) {
        String s = "ABCDEFGHIJ";

        /*
        A - 0
        B - 1
        C - 2
        D - 3
        E - 4
        F - 5
        G - 6
        H - 7
        I - 8
        J - 9
         */

        String s1 = s.substring(4);
        System.out.println(s1); // EFGHIJ

        String s2 = s.substring(3, 6);
        System.out.println(s2); // DEF
    }
}
```

## valueOf 方法

- `public int length()` 获取 String 长度

`public static String valueOf(char c)`

```java
public class Test {
    public static void main(String[] args) {
        String s = String.valueOf('A');
        System.out.println(s);
    }
}
```

## format 方法

- `public static String format(String format, Object... args)`

```java
public class Test {
    public static void main(String[] args) {
        System.out.println(String.format("%3d:%03d", 21, 21).equals(" 21:021")); // true
    }
}
```

# StringBuffer 类

初始化:



## 增

`public synchronized StringBuffer insert(int offset, String str)`

```java
/*
更多请阅读：https://www.yiibai.com/java/stringbuffer_insert.html
 */

public class Test {

    public static void main(String[] args) {
        StringBuffer stringBuffer = new StringBuffer("Welcome to yiibai.com");
        stringBuffer.insert(3, "1234");
        System.out.println(stringBuffer);
    }
}
```

`public synchronized StringBuffer append(String str)`

```java
public class Test {
    public static void main(String[] args) {
        StringBuffer stringBuffer = new StringBuffer("hello");
        stringBuffer.append(", world!");
        System.out.println(stringBuffer);
    }
}
```

## 删

`public synchronized StringBuffer delete(int start, int end)`

```java
public class Test {
    public static void main(String[] args) {
        StringBuffer stringBuffer = new StringBuffer("0123456");
        stringBuffer.delete(2, 5);
        System.out.println(stringBuffer); // 0156
    }
}
```

## 查

`public synchronized char charAt(int index)`

```java
public class Test {
    public static void main(String[] args) {
        String s = "www.runoob.com";
        StringBuffer stringBuffer = new StringBuffer(s);
        char result = stringBuffer.charAt(6);
        System.out.println(result); // n
    }
}
```

# StringBuilder 类

初始化:

`public StringBuilder(String str)`

## 删

`public StringBuilder delete(int start, int end)`

| Parameters | 解释 |
|---|---|
| start | This is the beginning index, inclusive. |
| end | This is the ending index, exclusive. |

```java
public class Test {
    public static void main(String[] args) {
        StringBuilder stringBuilder = new StringBuilder("0123456");
        stringBuilder.delete(2, 5);
        System.out.println(stringBuilder); // 0156
    }
}
```

## 查

`public StringBuilder reverse()`  

```java
public class Test {
    public static void main(String[] args) {
        StringBuilder stringBuilder = new StringBuilder("ABC");
        stringBuilder.reverse();
        System.out.println(stringBuilder); // CBA
    }
}
```

# ArrayList 类

## 增

- `public boolean add(E e)`
- `public void add(int index, E element)`

```java
import java.util.ArrayList;

public class Test {
    public static void main(String[] args) {
        ArrayList<String> strings = new ArrayList<>();
        strings.add("00");
        strings.add("02");
        strings.add(1, "01");
        for (String string : strings) {
            System.out.println(string); // 00, 01, 02
        }
    }

}
```

## 查

- `public E get(int index)`

- `public int size()` 获取 ArrayList 长度

```java
import java.util.ArrayList;

public class Test3 {
    public static final ArrayList<String> stringArrayList = new ArrayList<String>();

    public static void main(String[] args) {
        stringArrayList.add("ABC");
        stringArrayList.add("BCD");
        stringArrayList.add("");
        stringArrayList.add("DEF");
        System.out.println(stringArrayList.size());
        for (int i = 0; i < 4; i++) {
            System.out.println("\"" + stringArrayList.get(i) + "\"");
        }
        System.out.println("===");
        for (int i = 0; i < 4; i++) {
            System.out.println(stringArrayList.get(i).length());
        }
    }

}
```

# java.util.Arrays 类

## fill

```java
import java.util.Arrays;

public class Test {
    public static void main(String[] args) {
        int[] ints = new int[3];
        Arrays.fill(ints, 9);
        System.out.println(Arrays.toString(ints)); // [9, 9, 9]
    }

}
```

