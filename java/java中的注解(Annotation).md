## 简介
> 注解，java中提供了一种原程序中的元素关联任何信息、任何元素的途径的途径和方法。

注解是那些插入到源代码中使用其他工具可以对其进行处理的标签。注解不会改变程序的编译方式。java编译器会对包含注解与不包含注解的代码生成相同的虚拟机指令。在java中，注解是被当做修饰符（如public/static之类）来使用的。

## 注解与注释
注释是供人看的，注解是供程序调用的。一种是程序员写给另一个程序员的，一种是程序员写给计算机解析的。

## 常用注解
- @Override: 修饰重载父类方法;
- @Deprecated: 修饰未来将被抛弃的类/方法/属性等等;
- @SuppressWarnings: 关闭不当编译器警告信息。

## 注解运行机制分类
- 源码注解: 注解只在源码中存在，编译成.class文件中不存在;
- 编译时注解: 注解存在于源码与.class中，运行时不存在;
- 运行时注解: 在所有阶段都存在，可以影响程序运行逻辑。

## 注解来源分类
- 来自jdk的注解：如@Override/@Deprecated等等;
- 三方的注解: 如junit中的@Test/@Before等等;
- 自定义注解: 通过元注解自定义的注解。

## 元注解
给注解进行注解，用于自定义注解。
- @Target: 指明定义的注解的作用域，其值包括：
    + ElementType.CONSTRUCTOR:  构造方法声明；
    + ElementType.FIELD: 属性/字段声明;
    + ElementType.LOCAL_VARIABLE: 局部变量声明;
    + ElementType.METHOD: 方法声明;
    + ElementType.PACKAGE: 包声明;
    + ElementType.PARAMETER: 参数声明;
    + ElementType.TYPE: 类接口声明;
    + ElementType.ANNOTATION_TYPE: 注解类型声明;
    + ElementType.TYPE_PARAMETER: 类型参数声明(@since 1.8);
    + ElementType.TYPE_USE: 类型使用(@since 1.8)
- @Retention: 自定义注解的生命周期，其值包括：
    + RetentionPolicy.SOURCE: 只在源码显示，编译时丢弃;
    + RetentionPolicy.CLASS: 编译时记录到.class中，运行时忽略;
    + RetentionPolicy.RUNTIME: 运行时存在，可通过反射来读取。
- @Inherited: 一个标记注解，阐述了某个被标注的类型是被继承的，只能继承与类。
- @Documented: 用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被例如javadoc此类的工具文档化。Documented是一个标记注解，没有成员。

## 自定义注解
```java
//@Target(ElementType.METHOD)
@Target({ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Description {
    String desc() default "";
    String author() default "";
    int age() default 18;
}
```

- 使用关键字@interface定义注解;
- 成员变量以无参无异常的方式声明;
- 可以通过default关键字给成员变量指定默认值；
- 成员变量的类型是受限制的，包括`原始类型、String、Class、Annotation、Enumeration`;
- 如果注解只有一个成员变量，则成员名称必须取名value(),在使用中可以忽略成员名称与赋值号(=);
- 注解可以没有成员，没有成员的注解称为标示注解。

## 使用注解
```java
// @<注解名称>(<成员名1>=<成员值1>,<成员名2>=<成员值2> ...)

@Description(desc = "I'm class annotation")
public class AnnotationApp {

    @Description(desc = "I'm method annotation",author = "rudy")
    public String eyeColor(){
        return "red";
    }
}
```

## 解析注解
通过反射获取类、方法、成员上的`运行时`注解信息，从而实现动态控制程序运行的逻辑。
```java
import Annotation.Description;
import org.junit.Test;

public class AnnotationTest {

    @Test
    public void testParse() throws ClassNotFoundException {
        // 取出注解
        Class cls = Class.forName("Annotation.AnnotationApp");
        boolean isExit =  cls.isAnnotationPresent(Description.class);
        if(isExit){
            // 做逻辑处理
            Description annotation = (Description) cls.getAnnotation(Description.class);
            System.out.println("get annotation:" + annotation.desc());
        }else{
            System.out.println("no annotation!");
        }
    }
}
```
- 使用类加载器加载类，获取类对象;
- 判断是否存在类注解;
- 取出类注解，做逻辑处理;
- 方法注解类似。

## 他山之石
- 深入理解Java：注解（Annotation）--注解处理器: [http://blog.csdn.net/z69183787/article/details/40378309](http://blog.csdn.net/z69183787/article/details/40378309)
- 全面解析Java注解(视频):[http://www.imooc.com/learn/456](http://www.imooc.com/learn/456)