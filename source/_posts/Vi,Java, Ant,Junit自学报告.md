---
layout: post
title:  Vi, java, Ant, Junit自学报告
date: 2018-4-25
categories: java
tags: [java,ant,junit,vi/vim]
---



实训报告。简单的记录自己所学，然而实训之后就忘光了。

<!--more--> 

## Vi/Vim

Vi是Linux系统自带的命令行编辑器，其功能强大，且无需GUI即可运行。而Vim是Vi的进阶版本，其功能更强大，且有丰富的插件，使其深受程序员喜爱。 

Vi分为三种模式，命令行模式（command mode），插入模式（insert mode），底行模式（Last Line Mode，有些地方称为command-line mode）。

### 使用

进入时只需在命令行输入`vi file`即可打开file，并进入命令行模式。

#### 命令模式

输入`i`可直接进入插入模式（还有其他指令），按下Esc可从插入模式切换回来。
在命令行模式中可移动光标，删除字符等操作，下面列出部分操作。

| 指令           | 作用                                        |
| -------------- | ------------------------------------------- |
| **移动光标**   |
| h或左箭头键(←) | 光标左移一格                                |
| j或下箭头键(↓) | 光标下移一格                                |
| k或上箭头键(↑) | 光标上移一格                                |
| l或右箭头键(→) | 光标右移一格                                |
| 0              | 移到此行开头                                |
| $              | 移到此行末尾                                |
| G              | 移到最后                                    |
| gg             | 移到开头                                    |
| ^              | 移到此行第一个非空白字符                    |
| w              | 移到下一个字的开头                          |
| e              | 移到这一个字的字尾                          |
| b              | 移到上一个字的开头                          |
| #l             | 移到此行的第#个位置，如：5l                 |
| **删除**       |
| x              | 删除下一个字符                              |
| X（大写）      | 删除前一个字符                              |
| dd             | 删除此行                                    |
| #x，#X         | 类比#l                                      |
| #dd            | 从此行开始删除#行                           |
| **复制**       |
| yw             | 将光标所在之处到字尾的字符复制到缓冲区中    |
| yy             | 复制该行到缓冲区                            |
| #yw，#yy       | 类比以上                                    |
| p              | 将缓冲区内的字符贴到光标所在位置            |
| **替换**       |
| r              | 替换光标所在之处字符                        |
| R              | 替换光标所到之处的字符，直到按下「ESC」键为止 |
| **恢复**       |
| u              | 撤销                                        |
| **更改**       |
| cw             | 更改光标所在处的字到字尾处                  |
| c#w            | 更改#个**字**（不是字符）                   |
| 跳行           |
| [ctrl] + g     | 列出光标所在行的行号                        |
| #G             | 表示移动光标至文章的第#行行首               |

### 插入模式

无需多言，有三种进入方式。只需记住其功能是**插入**即可

1. 按i进入插入模式后，是从光标当前位置开始输入文件。
2. 按a进入插入模式后，是从目前光标所在位置的下一个位置开始输入文字。
3. 按o进入插入模式后，是插入新的一行，从行首开始输入文字。

### 底行模式

在命令行模式下按下冒号（[:]）即可进入底行模式。

| 指令     | 作用                        |
| -------- | --------------------------- |
| set nu   | 每一行都会显示行号          |
| set nonu | 取消显示行号                |
| w        | 保存                        |
| w!       | 强制保存                    |
| q        | 退出Vi                      |
| q!       | 不保存强制退出              |
| wq       | 保存并退出                  |
| /word    | 查找word，可按n往后继续寻找 |
| ?word    | 查找word，可按n往前继续寻找 |
| !command | 暂时离开Vi并执行command命令 |

## Vi/Vim总结

Vi/Vim功能强大，并非几日便能速成，需要长期使用才能熟能生巧。其在ssh中显得尤为重要。

另外就是，实训给出的文档有几个错误，比如[0]的作用。

参考：

[鳥哥的 Linux 私房菜](http://linux.vbird.org/linux_basic/0310vi.php#vim_v)

## Java

Java的基本语法大部分与C/C++并无不同，但是在设计上比C/C++更“亲民”，下面通过简单的学习总结其特点（充满主观性）。

* 变量引用。不同于C/C++，Java没有指针，取而代之的是对象的引用。对于一个基本类型的变量，变量即是其本身，但对于一个对象变量，变量只是这个对象的引用，也就是说，变量存在栈中，而对象存在堆中，变量指向这个对象。

* GC（*Garbage Collection*）。Java无需手动释放内存，它的垃圾回收机制会将无用的内存回收，基本原则便是回收不可再达到的对象内存，即该对象已无任何引用。

* 完全的面向对象。OOP三大特性：继承，封装，多态，Java都支持的很好，没有C/C++的多重继承，Java使用了单继承与接口，实现了多重继承的功能。

* 丰富的高级特性和api。反射，容器，多线程，泛型，网络编程，包（*package*），Java的高级特性方便了程序员的使用，也封装了底层的许多细节。

* 独特的编译和执行行程。不同于类似C/C++的编译性语言，也不是类似JavaScript的解释性语言，Java被称为半解释语言。源代码首先会被编译器编译成字节码（.class），字节码再由JVM解释运行，具有高性能，当然还比不上C/C++。

* 生态繁荣。Java应用广泛，JVM为其跨平台提供了有力的支持，Android开发为其注入了新鲜血液，Java的生态繁荣，许多学习资料随处可得，丰富的源码库也让使用者使用起来更轻松。

因为之前接触过C#，所以学起Java大概会轻松一丢丢，但是要将其运用到开发中还是不容易的，需要实际动手才能领略其特点。

## Ant

Ant,是一个将软件编译、测试、部署等步骤联系在一起加以自动化的一个工具，有点类似于C/C++的make，其没有单独的语法，而是通过xml来构建，免去了重新学习的时间。下面是一个简单的`build.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="Caculator" default="compile" basedir=".">
  <!-- set global properties for this build -->
  <property name="src" location="src"/>
  <property name="build" location="build"/>
  <property name="lib" location="lib"/>
  <property name="entry" value="com.vegchic.junittest.HelloWorld"/>
  <target name="init">
    <!-- Create the time stamp -->
    <tstamp/>
    <!-- Create the build directory structure used by compile -->
    <mkdir dir="${build}"/>
  </target>
  <target name="compile" depends="init" description="compile the source">
    <!-- Compile the java code from ${src} into ${build} -->
    <javac srcdir="${src}" destdir="${build}"/>
  </target>
  <target name="run" depends="compile">
    <java classname="${entry}" fork="yes" classpath="${build}"/>
  </target>
  <target name="test" depends="compile">
    <junit>
      <classpath location="${build}"/>
      <formatter type="brief" usefile="false"/>
      <test name="com.vegchic.test.HelloWorldTest"/>
    </junit>
  </target>
  <target name="clean" description="clean up">
    <!-- Delete the ${build} and ${dist} directory trees -->
    <delete dir="${build}"/>
  </target>
</project>
```

由xml简洁易懂的语法容易看出，这个`build.xml`有5个命令，分别为`init`，`compile`，`run`，`test`，`run`。

* `<project>`即使项目的信息描述，`default`为默认命令，如果不执行默认命令则需输入`ant <commandName>`。
* `<property>`是一些变量的定义。
* `<target>`是需要运行的命令，其子元素的标签名即是各种命令，可以使用Ant的内建命令，也可以自己自定义，其中的属性相当于命令行中的参数，如`mkdir ${dist}/lib`。其中`depends=`则是依赖关系的体现，如果依赖命令还未执行，会先执行依赖命令。

下面是使用结果：

```bash
unddd@unddd-virtual-machine:~/helloWorld$ ant run
Buildfile: /home/unddd/helloWorld/build.xml

init:

compile:
    [javac] /home/unddd/helloWorld/build.xml:16: warning: 'includeantruntime' was not set, defaulting to build.sysclasspath=last; set to false for repeatable builds
    [javac] Compiling 2 source files to /home/unddd/helloWorld/build

run:
     [java] Done!!!

BUILD SUCCESSFUL
Total time: 0 seconds
unddd@unddd-virtual-machine:~/helloWorld$
```

ant的简单使用不难，但是要深入学习还需要下功夫才行。

## Junit

Junit是一款Java的单元测试框架。在我看来，它通过测试类来测试目的方法是否能正常执行其功能。

### 简单使用

junit通过注解和断言来进行测试，只要写一个测试类，并通过`import`Juint的api即可测试，下面是一个简单的测试类：

```java
package com.vegchic.test;
import junit.framework.TestCase;
import org.junit.Test;
import static org.junit.Assert.assertEquals;
import com.vegchic.junittest.HelloWorld;

public class HelloWorldTest {
  @Test
  public void testSayHello() {
    String res = HelloWorld.sayHello(); //should return "Hello world"
    assertEquals(res, "Hello world");
  }

  @Test
  public void testGetInt() {
    int res = HelloWorld.getInt(); //should return 5;
    assertEquals(5, res);
  }
}
```

然后输入`java org.junit.runner.JunitCore <testClassName>`即可运行测试。下面会介绍如何与ant搭配使用。

### 注解

需要引入`org.junit.*`（Junit5中为`org.junit.jupiter.api.*`）。

* `@Test`，即实际的测试方法。

* `@Before`，在每个测试方法执行前执行。在Junit5中改为`@BeforeEach`。

* `@After`，在每个测试方法后执行。在Junit5中改为`@AfterEach`。

* `@BeforeClass`，静态测试方法，会在所有测试方法开始前测试，且只有一次。在Junit5中改为`@BeforeAll`。

* `@AfterClass`，类似`@BeforeClass`。在Junit5中改为`@AfterAll`。

* `@Ignore`，加了这个注解的`@Test`不会执行。在Junit5中改为`@Disabled`。

### 断言

断言在`org.junit.Assert.*`中，为静态方法。junit5为`org.junit.jupiter.Assertions`。

| 方法                                                                                       | 作用                               |
| ------------------------------------------------------------------------------------------ | ---------------------------------- |
| `void assertEquals(boolean expected, boolean actual)`                                      | 检查两个变量或者等式是否相等       |
| `void assertArrayEquals​(boolean[] expected, boolean[] actual)`                            | 检查两个数组是否相等               |
| `void assertTrue​(boolean condition)`                                                      | 检查条件是真的                     |
| `void assertNotNull(Object object)`                                                        | 检查对象不是空的                   |
| `void assertSame​(java.lang.Object expected, java.lang.Object actual)`                     | 检查两个对象引用是否指向同一个对象 |

其中对于基本类型比较支持byte，char，float，boolean等类型（junit4中只支持Double），详情可见[org.junit.Assert](https://junit.org/junit4/javadoc/latest/org/junit/Assert.html)。

### 与Ant搭配使用

前面说到，Ant支持测试，自然也为Junit提供了支持。下面是一个简单的test target。

```xml
  <target name="test" depends="compile">
    <junit>
      <classpath location="${build}"/>
      <formatter type="brief" usefile="false"/>
      <test name="${testClass}"/>
    </junit>
  </target>
```

`<classpath>`顾名思义，而`<formatter>`是测试的信息格式，`<test>`是测试类的名字。运行结果如下：

```bash
unddd@unddd-virtual-machine:~/helloWorld$ ant test
Buildfile: /home/unddd/helloWorld/build.xml

init:

compile:
    [javac] /home/unddd/helloWorld/build.xml:16: warning: 'includeantruntime' was not set, defaulting to build.sysclasspath=last; set to false for repeatable builds
    [javac] Compiling 2 source files to /home/unddd/helloWorld/build

test:
    [junit] Testsuite: com.vegchic.test.HelloWorldTest
    [junit] Tests run: 2, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.025 sec
    [junit] 

BUILD SUCCESSFUL
Total time: 0 seconds
unddd@unddd-virtual-machine:~/helloWorld$
```

### Junit总结

虽然看上去语法并不难而且也接触不到深入使用，但是我被它的配置和各种包弄得生不如死。但也让我对java的寻包有了更深的理解（只能这么安慰自己了）。