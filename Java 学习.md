# 常用

```java
public class ClassNameTest {
    public static void main(String[] args) {
        ClassNameTest classNameTest = new ClassNameTest();

        System.out.println(Thread.currentThread().getStackTrace()[1]);
        System.out.println(classNameTest.getClass());
        System.out.println("获取类名");
        System.out.println(Thread.currentThread().getStackTrace()[1].getClassName());
        System.out.println(classNameTest.getClass().getName());
        System.out.println("获取方法名");
        System.out.println(Thread.currentThread().getStackTrace()[1].getMethodName());
        System.out.println("获取文件名");
        System.out.println(Thread.currentThread().getStackTrace()[1].getFileName());
        System.out.println("获取行号");
        System.out.println(Thread.currentThread().getStackTrace()[1].getLineNumber());
        classNameTest.func();
    }

    public void func() {
        System.out.println("=== func 函数开始");
        System.out.println(this.getClass());
        System.out.println("获取类名");
        System.out.println(this.getClass().getName());
        System.out.println("=== func 函数结束");
    }

}
```

# 资料参考

Java API 文档: <https://tool.oschina.net/apidocs/apidoc?api=jdk-zh>

Java API 文档: <https://www.apiref.com/java11-zh/>

# 面向对象

## 封装

## 继承

## 多态

### Override

覆写: 子类中的方法（或属性）覆写父类中的方法（或属性）。  
static 定义方法  
static 关键字也可以进行方法的定义, static 方法的主要特点在于,
static 方法和非 static 方法在调用上的限制：  
- static 方法只允许调用 static 属性或 static 方法；  
- 非 static 方法允许调用 static 属性或 static 方法；  

## IO

