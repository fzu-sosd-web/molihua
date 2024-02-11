---
authors:
  - luke 
date: 2024-02-02
tags:
  - CGlib
categories:
  - Java
  - Reflection
---

# CGLIB——简单使用

在Java中，CGLIB（Code Generation Library）是一个功能强大的代码生成库，它可以在运行时生成字节码，常被用于实现动态代理。

<!-- more -->

## MethodInterceptor

`MethodInterceptor`是CGLIB中的一个接口，用于拦截方法的调用。通过实现这个接口，可以在目标方法执行前后进行一些自定义的处理。

下面是一个简单的示例，演示了如何使用CGLIB的`MethodInterceptor`：

首先，您需要添加相关的依赖。如果使用Maven，可以在项目的`pom.xml`文件中添加：

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.3.0</version> <!-- 请使用最新版本 -->
</dependency>
```

然后，可以使用以下代码创建一个动态代理类，并在`MethodInterceptor`中实现拦截逻辑：

```java
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class SampleInterceptor implements MethodInterceptor {

    public static void main(String[] args) {
        // 创建Enhancer对象，类似于JDK动态代理的Proxy类
        Enhancer enhancer = new Enhancer();
        // 设置目标类
        enhancer.setSuperclass(SampleClass.class);
        // 设置回调（拦截器）
        enhancer.setCallback(new SampleInterceptor());

        // 创建代理对象
        SampleClass proxy = (SampleClass) enhancer.create();

        // 调用代理对象的方法
        proxy.sampleMethod();
    }

    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        System.out.println("Before method: " + method.getName());

        // 调用原始方法
        Object result = proxy.invokeSuper(obj, args);

        System.out.println("After method: " + method.getName());

        return result;
    }
}

class SampleClass {
    public void sampleMethod() {
        System.out.println("Inside sampleMethod");
    }
}
```

在上述示例中，`SampleInterceptor`实现了`MethodInterceptor`接口，并在`intercept`方法中添加了在目标方法执行前后打印日志的逻辑。`Enhancer`类用于创建代理对象，并设置了目标类和拦截器。

请注意，CGLIB通过生成字节码来创建代理类，因此与JDK动态代理相比，它对于代理的类没有接口限制。

## InterfaceMaker

`InterfaceMaker`是CGLIB库中的一个类，用于在运行时创建接口。通常，它与`Enhancer`一起使用，通过`InterfaceMaker`可以生成一个接口，然后使用该接口的实现类作为代理。

以下是一个简单的示例，演示了如何使用`InterfaceMaker`创建接口：

```java
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.InterfaceMaker;

import java.lang.reflect.Method;

public class InterfaceMakerExample {

    public static void main(String[] args) {
        // 创建InterfaceMaker对象
        InterfaceMaker interfaceMaker = new InterfaceMaker();

        // 添加l方法到接口
        interfaceMaker.add(SampleClass.class);

        // 通过生成的接口创建代理对象
        Class<?> proxyInterface = interfaceMaker.create();
        Enhancer enhancer = new Enhancer();
        enhancer.setInterfaces(new Class[]{proxyInterface});
        enhancer.setCallback((obj, method, args1, proxy) -> {
            System.out.println("Before method: " + method.getName());

            // 调用原始方法
            Object result = proxy.invokeSuper(obj, args1);

            System.out.println("After method: " + method.getName());

            return result;
        });

        // 创建代理对象
        SampleInterface proxy = (SampleInterface) enhancer.create();

        // 调用代理对象的方法
        proxy.sampleMethod();
    }
}

class SampleClass {
    public void sampleMethod() {
        System.out.println("Inside sampleMethod");
    }
}

interface SampleInterface {
    void sampleMethod();
}
```

在上述示例中，`InterfaceMaker`用于从`SampleClass`中提取方法，然后创建了一个新的接口`SampleInterface`。通过`Enhancer`，我们创建了一个实现`SampleInterface`接口的代理对象，实现了方法拦截。

请注意，由于CGLIB是基于字节码生成的，所以在运行时创建接口和代理对象时，性能可能会相对较低，而且生成的代码可能比较复杂。在一般情况下，使用`Enhancer`和`MethodInterceptor`直接创建代理类可能更为常见和简便。