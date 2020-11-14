---
title: "注解"
date: 2020-05-12T22:06:42+08:00
categories: "Java"
tags: [ "Java"]
---

## 定义注解

```java
import java.lang.annotation.*;
@Target(ElementType.METHOND)
@Retention(RetentionPolicy.RUNTIME)
public @interface UseCase {
    int id();
    String description() default "no description";
}
```

## 使用注解

```java
import java.util.*;
public class PasswordUtils {
    @UseCase(id = 47, description =
            "Passwords must contain at least one numeric")
    public boolean validatePassword(String passwd) {
        return (passwd.matches("\\w*\\d\\w*"));
    }
    @UseCase(id = 48)
    public String encryptPassword(String passwd) {
        return new StringBuilder(passwd)
                .reverse().toString();
    }
    @UseCase(id = 49, description =
            "New passwords can't equal previously used ones")
    public boolean checkForNewPassword(
            List<String> prevPasswords, String passwd) {
        return !prevPasswords.contains(passwd);
    }
}
```

## 注解处理器

```java
import java.util.*;
import java.util.stream.*;
import java.lang.reflect.*;
public class UseCaseTracker {
    public static void
    trackUseCases(List<Integer> useCases, Class<?> cl) {
        for(Method m : cl.getDeclaredMethods()) {
            UseCase uc = m.getAnnotation(UseCase.class);
            if(uc != null) {
                System.out.println("Found Use Case " +
                        uc.id() + "\n " + uc.description());
                useCases.remove(Integer.valueOf(uc.id()));
            }
        }
        useCases.forEach(i ->
                System.out.println("Missing use case " + i));
    }
    public static void main(String[] args) {
        List<Integer> useCases = IntStream.range(47, 51)
                .boxed().collect(Collectors.toList());
        trackUseCases(useCases, PasswordUtils.class);
    }
}
```

## java.lang包中的标准注解

- @Override：表示当前的方法定义将覆盖基类的方法。如果你不小心拼写错误，或者方法签名被错误拼写的时候，编译器就会发出错误提示。

- @Deprecated：如果使用该注解的元素被调用，编译器就会发出警告信息。

- @SuppressWarnings：关闭不当的编译器警告信息。

- @SafeVarargs：在 Java 7 中加入用于禁止对具有泛型varargs参数的方法或构造函数的调用方发出警告。

- @FunctionalInterface：Java 8 中加入用于表示类型声明为函数式接口

## 元注解

元注解用于创建新的注解

注解|解释
-|-
@Target|表示注解可以用于哪些地方。可能的ElementType参数包括：CONSTRUCTOR：构造器的声明 FIELD：字段声明（包括 enum 实例）LOCAL_VARIABLE：局部变量声 METHOD：方法声明 PACKAGE：包声明 PARAMETER：参数声明 TYPE：类、接口（包括注解类型）或者enum 声明
@Retention|表示注解信息保存的时长。可选的 RetentionPolicy 参数包括：SOURCE：注解将被编译器丢弃 CLASS：注解在 class 文件中可用，但是会被 VM 丢弃。RUNTIME：VM 将在运行期也保留注解，因此可以通过反射机制读取注解的信息。
@Documented|将此注解保存在 Javadoc 中
@Inherited|允许子类继承父类的注解
@Repeatable|允许一个注解可以被使用一次或者多次（Java 8）。
