# Java 读写文件辅助功能类

## FileUtilFF 类

```java
package ff;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.nio.charset.StandardCharsets;
import java.util.ArrayList;

/*
来源:
https://juejin.cn/post/7166085870720647198
https://codeantenna.com/a/OBbR954kiJ
 */

/**
 * 方便的提供 File 读写的类
 */
public class FileUtilFF {

    /**
     * 文件读取后按行取出, 放入 ArrayList<String>
     *
     * @param sourceFile 传入的 File 类对象
     * @return 一个 ArrayList<String> 类对象. 一个元素是一行内容.
     */
    public static ArrayList<String> readLineFromFile(File sourceFile) {
        return readLineFromFile(sourceFile, new ArrayList<>());
    }

    /**
     * 文件读取后按行取出, 放入 ArrayList<String>
     *
     * @param sourceFile      传入的 File 类对象
     * @param line2StringArrayList 传入的 ArrayList 对象
     * @return 一个 ArrayList<String> 类对象. 一个元素是一行内容.
     */
    public static ArrayList<String> readLineFromFile(File sourceFile, ArrayList<String> line2StringArrayList) {
        try (
                // 建立从文件到程序的数据输入(input)流
                FileInputStream fileInputStream = new FileInputStream(sourceFile);
                // 用 InputStreamReader 包装 byte流，并指定字符编码，让它能够将读出的byte转为字符
                InputStreamReader inputStreamReader = new InputStreamReader(fileInputStream, StandardCharsets.UTF_8);
                //  增加缓冲功能，输入输出效率更高，并且可以一次读一行
                BufferedReader bufferedReader = new BufferedReader(inputStreamReader)) {
            String line = null;
            while ((line = bufferedReader.readLine()) != null) {
                line2StringArrayList.add(line);
            }
            // 还可以从 bufferedReader 里获取 Stream，然后通过流操作+Lambda的形式完成
            // bufferedReader.lines().map(String::trim).map(String::toUpperCase).forEach(System.out::println);
            bufferedReader.close();
            inputStreamReader.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return line2StringArrayList;
    }

}
```

## Sha256Parser 类

```java
package ff;

import java.io.File;
import java.util.ArrayList;

/**
 * 解析由 7-zip 生成的 .sha256 文件
 */
public class Sha256Parser {

    /**
     * 重复扫描到的 sha256 码保存至此. 已去重
     */
    public final ArrayList<String> duplicateSha256ArrayList = new ArrayList<>();

    /**
     * 解析文件得到的所有的 sha256 码保存至此. 为去重后保存
     */
    public final ArrayList<String> sha256StringArrayList = new ArrayList<>();

    public Sha256Parser() {

    }

    public Sha256Parser(File sourceFile) {
        parse(sourceFile);
    }

    public void parse(File sourceFile) {
        ArrayList<String> readLineFromFile = FileUtilFF.readLineFromFile(sourceFile);
        for (String line : readLineFromFile) {
            this.parse(line);
        }
    }

    public void parse(String line) {
        if (line.length() < 67) {
            return;
        }
        // 行的字符长度合理则到达该处
        String sha256String = line.substring(0, 64);
        for (int i = 0; i < sha256String.length(); i++) {
            char charAt = sha256String.charAt(i);
            boolean charOK = Character.isDigit(charAt) || Character.isLowerCase(charAt) || Character.isUpperCase(charAt);
            if (!charOK) {
                return;
            }
        }
        // sha256 的字符合理则到达该处
        if (this.sha256StringArrayList.contains(sha256String)) {
            // 判断是否存在, 存在则到达该处
            if (!this.duplicateSha256ArrayList.contains(sha256String)) {
                // 如果没有记录该重复项, 则到达该处
                this.duplicateSha256ArrayList.add(sha256String);
            }
        } else {
            this.sha256StringArrayList.add(sha256String);
        }
    }

}
```

# 通过 .sha256 文件查重 sha256 码

## 使用 ArrayList 类

ArrayList 元素为 String 时, indexOf 似乎能准确判断 ArrayList 是否含有这个字符串

```java
import java.util.ArrayList;

public class Test {
    public static void main(String[] args) {
        ArrayList<String> stringArrayList = new ArrayList<>();
        String s1 = "s1";
        String s2 = new String("s2");
        stringArrayList.add(s1);
        stringArrayList.add(s2);
        System.out.println(stringArrayList.contains("s0")); // false
        System.out.println(stringArrayList.contains("s1")); // true
        System.out.println(stringArrayList.contains("s2")); // true
        System.out.println(s1 == "s1"); // true
        System.out.println(s2 == "s2"); // false
    }
}
```

## 结果代码

```java
import ff.FileUtilFF;

import java.io.File;
import java.util.ArrayList;

/**
 * 解析由 7-zip 生成的 .sha256 文件, 控制台输出重复的 sha256 码
 */
public class Test {

    /**
     * 解析文件得到的所有的 sha256 码保存至此. 为去重后保存
     */
    public static final ArrayList<String> sha256ArrayList = new ArrayList<>();

    /**
     * 重复扫描到的 sha256 码保存至此. 已去重
     */
    public static final ArrayList<String> duplicateSha256ArrayList = new ArrayList<>();

    /**
     * 源文件位置
     */
    private static final String SOURCE_FILE_NAME = "C:\\Users\\肖瑞\\11_Top_FF\\随时可删除\\temp\\2\\2\\电子书库_by-FoliageForest.sha256";

    /**
     * 源文件位置
     */
    private static final String SOURCE_FILE_NAME2 = "C:\\Users\\肖瑞\\11_Top_FF\\随时可删除\\temp\\2\\2\\学习资料_by-FoliageForest.sha256";

    public static void main(String[] args) {
        // 读取文件
        ArrayList<String> stringArrayList = FileUtilFF.readLineFromFile(new File(SOURCE_FILE_NAME));
        ArrayList<String> stringArrayList2 = FileUtilFF.readLineFromFile(new File(SOURCE_FILE_NAME2));
        for (String line : stringArrayList) {
            addSha256Value(line);
        }
        for (String line : stringArrayList2) {
            addSha256Value(line);
        }
        // 向控制台输出重复的 sha256 码
        for (String s : duplicateSha256ArrayList) {
            System.out.println(s);
        }
    }

    /**
     * 通过一行文件中的一行 String, 读取出 sha256 值, 查重后保存到 sha256ArrayList 对象
     *
     * @param line 文件中的一行 String
     */
    public static void addSha256Value(String line) {
        if (line.length() > 66) {
            // 判断长度, 长度合适则到达该处
            String s = line.substring(0, 64);
            if (sha256ArrayList.contains(s)) {
                // 判断是否存在, 存在则到达该处
                if (!duplicateSha256ArrayList.contains(s)) {
                    duplicateSha256ArrayList.add(s);
                }
            } else {
                sha256ArrayList.add(s);
            }
        }
    }

}
```

# 通过文件树获取文件名列表

## 读取文件

略

## 使用 ArrayList 类

`public boolean add(E e)`

public E get(int index)

public int size() 获取 ArrayList 长度

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

## 结果代码:

```java
import java.io.BufferedReader;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.nio.charset.StandardCharsets;
import java.util.ArrayList;

/**
 * 读取和分析文件树
 */

public class Test2 {
    public static final ArrayList<String> fileNameArrayList = new ArrayList<String>();
    private static final String SOURCE_FILE_NAME = "C:\\Users\\肖瑞\\11_Top_FF\\随时可删除\\temp\\2\\2\\tree1.txt";

    public static void main(String[] args) {
        File sourceFile = new File(SOURCE_FILE_NAME);
        readLineFromFile(sourceFile);
        ArrayList<String> stringArrayList = new ArrayList<String>();
        System.out.println("=============================");
        for (int i = 0; i < fileNameArrayList.size(); i++) {
            System.out.println(fileNameArrayList.get(i));
            File bookFile = new File("C:\\Users\\肖瑞\\11_Top_FF\\随时可删除\\temp\\2\\1\\心理学187本\\" + fileNameArrayList.get(i));
            if (!bookFile.isFile()) {
                stringArrayList.add(fileNameArrayList.get(i));
            }
        }
        System.out.println("=============================");
        for (int i = 0; i < stringArrayList.size(); i++) {
            System.out.println(stringArrayList.get(i));
        }
//        System.out.println(stringArrayList.size());
//        System.out.println(stringArrayList.indexOf("└── 生命中最简单又最困难的事：日常生活就是我们的本身，既绝望又禅意 - [美]大卫·福斯特·华莱士.azw3"));
//        System.out.println(stringArrayList.indexOf("0 directories, 187 files"));
//        System.out.println(stringArrayList.get(0));
    }

    public static void readLineFromFile(File sourceFile) {
        try (
                // 建立从文件到程序的数据输入(input)流
                FileInputStream fileInputStream = new FileInputStream(sourceFile);
                // 用 InputStreamReader 包装 byte流，并指定字符编码，让它能够将读出的byte转为字符
                InputStreamReader inputStreamReader = new InputStreamReader(fileInputStream, StandardCharsets.UTF_8);
                //  增加缓冲功能，输入输出效率更高，并且可以一次读一行
                BufferedReader bufferedReader = new BufferedReader(inputStreamReader)) {
            String line = null;
            while ((line = bufferedReader.readLine()) != null) {
                addFileName(line);
            }
            // 还可以从 bufferedReader 里获取 Stream，然后通过流操作+Lambda的形式完成
            // bufferedReader.lines().map(String::trim).map(String::toUpperCase).forEach(System.out::println);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void addFileName(String line) {
        if (line.length() > 3) {
            if (line.charAt(1) == '─' && line.charAt(2) == '─') {
                fileNameArrayList.add(line.substring(4)); // 向 stringArrayList 加入数据
            }
        }
    }

}
```
