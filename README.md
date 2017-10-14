# 一天一个设计模式 - Adapter

## Adapter 模式

现实生活中，我们经常遇到这样的问题，比如笔记本需要 12V 的直流电压，可是墙电是 220V 的，显示器接口是 VGA 的，显卡只给了 DVI 接口。遇到这样的情况，通常会使用适配器、转接口之类的工具。

在程序世界中，也会出现现有程序无法直接使用的情况。而 **Adapter 模式 **就是在程序中起到适配器的作用，简单来说就是“使...相互适应”，用于填补“现有的程序”和“所需的程序”之间的差异。

Adapter 模式有两种：

- 使用继承的适配器
- 使用委托的适配器

### 1. 使用继承的适配器

举个栗子，打印横幅 `Banner` 类之前有两种打印样式分别是在输入字符串左右添加 () 和 **。

```java
public class Banner {
  private String str;
  public Banner(String str) {
      this.str = str;
  }
  public void showWithParen() {
      System.out.println("(" + str +")");
  }
  public void showWithAster() {
      System.out.println("*" + str + "*");
  }
}
```

上面这个类就相当于我们原先交流电 220V 的情况。

而现在有个 Print 接口中 `printWeak()` 和 `printStrong()` 两个方法来打印。

```java
public interface Print {
  public abstract void printWeak();
  public abstract void pirntStrong();
}
```

这接口就相当于我们笔记本需要的 10V 直流电。

这种情况我们就需要一个适配器将 `Banner` 类的 `showWithParen()` 和 `showWithAster()` 适配起来。

```java
public class PrintBanner extends Banner implements Print {
  public PirntBanner(String str) {
    super(str);
  }
  public void printWeak() {
    showWithParen();
  }
  public void printStrong(){
    showWithAster();
  }
}
```

好了现在我们适配了这两种打印方法，试试看。

```java
public class Main {
  Print p = new PrintBanner("Hello,World.");
  p.printWeak();
  p.printStrong();
}
```

**总结**：对于 `Main` 类来说，它看不到 `Banner` 类中的 `showWithParen()` 方法和 `showWithAster()` 方法，就好像笔记本只要在 10V 直流电下工作，并不知道需要一个适配器将 220V 交流电转换成 10V 直流电一样。

### 2. 使用委托的适配器

> 委托通俗来说就是"交给其他人",在程序中委托的意思就是将某个方法的实际处理交给其他实例的意思

 `Banner` 类和 `Main` 类与上面相同，不同之处在于现在 `Print` 不再是接口而是类，同时由于 Java 的限制，我们现在没有办法让 `PrintBanner` 类同时集成 `Banner` 和 `Print` 两个类。那我们怎么实现这个适配器呢？

首先我们继承 `Print` 类中的 `printWeak()` 和 `printStrong()` 两个方法，

然后我们需要一个 `Banner` 类的实例来具体实现 `printWeak()` 和 `printStrong()` 两个方法。

```java
public abstract class Print {
  public abstract void printWeak();
  public abstract void printStrong();
}
```

```java
public class PirntBanner extends Print {
  private Banner banner;
  public PrintBanner(String str) {
    this.banner = new Banner(str);
  }
  public void printWeak() {
    banner.printWithParen();
  }
  public void printStrong() {
    banner.printWithAster();
  }
}
```

## 解惑

### 如果某个方法就是我们所需要的方法，直接在程序中使用不就可以了吗？

很多时候，我们并非从零开始编程，你也不是一个人在战斗，经常会用到现有的类或者接口。特别是当有的类已经进行过充分的测试，Bug 少，我们更愿意将这些类作为组件进行复用。这样使用 **Adapter 模式** 生成新的类之后，出现 Bug 时，我们基本可以排除现有的类，在代码排查时更加简单。

我们常常会有新 API 出来需要适配，我们常常会产生“在这里改改就好了”，很容易就修改了现有的代码。需要注意的是，如果我们修改的是已经测试过的代码，我们就要再修改过后重新测试。

### 版本升级和兼容

软件经常需要版本升级，在升级过程中会出现和之前版本兼容的问题，现实生活中，我们不是可以完全摒弃旧版本的，使用 Adapter 模式可以很好的同时维护旧版本和新版本。

### 无法或者很难适配功能不一致的类

举个栗子，你想让 VGA 接口输出到你的音响，你在逗我？
