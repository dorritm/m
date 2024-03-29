# spring boot

约定大于配置原则。

1. 创建一个maven项目
2. 在pom.xml中加入spring boot依赖
3. 创建引导类 Demo 

## spring ioc容器

容器创建对象（spring Beans）
通过阅读配置元数据（XML、JAVA注释或JAVA代码），知道对哪些对象实例化、定位、配置应用程序中的对象

### BeanFactory 容器

主要功能是DI（依赖注入），常用类 XmlBeanFactory类，该类会从XML文件中读取配置元数据。
定义一个HelloWorld类和MainApp类，在src文件下创建Bean的配置文件Beans.xml。

MainApp ClassPathResource()接口加载路径CLASSPATH下的bean配置文件。XmlBeanFactory接口创建和初始化bean.xml中对象。利用第一步生成的工厂对象的getBean()方法得到所需要的bean，通过获取bean的id来返回bean。

	 XmlBeanFactory factory = new XmlBeanFactory(new ClassPathResource("Beans.xml"));
	 HelloWorld obj = (HelloWorld) factory.getBean("helloWorld");



### ApplicationContext 容器

Application Context 是 BeanFactory 的子接口，也被称为 Spring 上下文，原理和BeanFactory类似。
**常用接口：**

FileSystemXmlApplicationContext：该容器从 XML 文件中加载已被定义的 bean。在这里，你需要提供给构造器 XML 文件的完整路径。
ClassPathXmlApplicationContext：该容器从 XML 文件中加载已被定义的 bean。在这里，你不需要提供 XML 文件的完整路径，只需正确配置 CLASSPATH 环境变量即可，因为，容器会从 CLASSPATH 中搜索 bean 配置文件。
WebXmlApplicationContext：该容器会在一个 web 应用程序的范围内加载在 XML 文件中已被定义的 bean。
MainApp.java的代码为：

<center>ApplicationContext context = new FileSystemXmlApplicationContext("C:/Users/ZARA/workspace/HelloSpring/src/Beans.xml");	</center>

<center>HelloWorld obj = (HelloWorld) context.getBean("helloWorld");</center>

### Bean定义了配置元数据的信息

**bean的属性：**
class					具有强制性，用来指定创建bean的bean类，如com.springboot.HelloWorld
name					指定唯一bean的标识符，在基于xml的配置元数据中，可以使用name或id作为标识符
scope					作用域
constructor-arg 依赖注入
properties			依赖注入
autowiring mode依赖注入
lazy-initialization mode	延迟初始化，在第一次请求时创建Bean实例。

**Bean与Spring容器的关系**

Bean的配置信息通过xml 、java类注解 、注解获取，在spring容器中形成Bean定义注册表，根据注册表实例化bean。将Bean实例放到spring容器的Bean缓存池中，最后由应用程序调用。

### Bean的作用域

**singleton**	是默认作用域，创建容器时自动创建一个bean对象，每次获取的对象是同一对象。
（对于所有的bean请求，只要id与bean定义相匹配，就会返回同一实例）
**prototype**	应用多个对象实例，在创建容器时没有实例化，当我们获取bean的时候才会去创建一个对象，而且每次获取的都不是同一对象。当调用容器的getBean（）方法都会创建一个新的bean。

### Bean的生命周期

bean的定义-->bean的初始化-->bean的使用-->bean的销毁
在bean的定义中加入init-method（在实例化bean时调用该方法）和destroy-method（从容器中移除bean时调用）参数。

```<bean id="helloWorld" 
<bean id="helloWorld" 
       class="com.tutorialspoint.HelloWorld"
       init-method="init" destroy-method="destroy">
       <property name="message" value="Hello World!"/>
</bean>
```



## 依赖注入DI

作用：java应用程序中有很多对象，依赖注入有助于在对象保持独立的情况下，把这些类粘合在一起。
如写文本编辑器组件，提供拼写检查功能，提供两个类TextEditor和spellChecker。

**第一种方法--基于构造函数的依赖注入：**引用的类spellChecker通过类构造函数注入到类TextEditor中。
MainApp.java  

	<center>ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");</center>
	  	<center>TextEditor te = (TextEditor) context.getBean("textEditor");</center>
Beans.xml

```
<bean id="textEditor" class="com.tutorialspoint.TextEditor">
	  		<constructor-arg ref="spellChecker"/>
	 </bean>
	<bean id="spellChecker" class="com.tutorialspoint.SpellChecker">
	 </bean>
```

**第二种方法--基于设定函数的依赖注入：**在TextEditor类中设置变量spellChecker，使用set和get方法。
MainApp.java与基于构造函数的依赖注入相同
Beans.xml 

```<bean id="textEditor" class="com.tutorialspoint.TextEditor">
<bean id="textEditor" class="com.tutorialspoint.TextEditor">
      <property name="spellChecker" ref="spellChecker"/>
</bean>
```

**Spring注入内部的Beans**

<property/>或<constructor-arg/>元素中的<bean/>元素称为内部bean。

### 注入集合

list				允许重复

set				无序，不可重复

map			键值對，可以是任意类型

props			键值對，都是字符串类型	

```
 <bean id="..." class="...">

      <property name="addressList">
         <list>
            <ref bean="address1"/>
            <ref bean="address2"/>
            <value>Pakistan</value>
         </list>
      </property>

      <property name="addressSet">
         <set>
            <ref bean="address1"/>
            <ref bean="address2"/>
            <value>Pakistan</value>
         </set>
      </property>

      <property name="addressMap">
         <map>
            <entry key="one" value="INDIA"/>
            <entry key ="two" value-ref="address1"/>
            <entry key ="three" value-ref="address2"/>
         </map>
      </property>
      
		<property name="addressProp">
         <props>
            <prop key="one">INDIA</prop>
            <prop key="two">Pakistan</prop>
            <prop key="three">USA</prop>
            <prop key="four">USA</prop>
         </props>
      </property>
      

   </bean>
```



## Beans 自动装配

自动装配情况下，可以不使用<constructor-arg>`和`<property>

### Spring 自动装配 byName

byName是根据属性名自动装配。继续上面的*TextEditor*，*SpellChecker*例子，如果一个bean设置*auto-wire*为byName，并且包含*spellChecker*属性，spring会查找名为*spellChecker*的bean。

*TextEditor*类定义了两个变量：*private SpellChecker spellChecker;*

​													*private String name;*

```
package com.tutorialspoint;
public class TextEditor {
   private SpellChecker spellChecker;
   private String name;
   public void setSpellChecker( SpellChecker spellChecker ){
      this.spellChecker = spellChecker;
   }
   public SpellChecker getSpellChecker() {
      return spellChecker;
   }
   public void setName(String name) {
      this.name = name;
   }
   public String getName() {
      return name;
   }
   public void spellCheck() {
      spellChecker.checkSpelling();
   }
}
```

另一个依赖类文件 *SpellChecker.java* 的内容：

```
package com.tutorialspoint;
public class SpellChecker {
   public SpellChecker() {
      System.out.println("Inside SpellChecker constructor." );
   }
   public void checkSpelling() {
      System.out.println("Inside checkSpelling." );
   }   
}
```

在基于设值函数的依赖注入中，bean.xml文件为

```
<bean id="textEditor" class="com.tutorialspoint.TextEditor">
       <property name="spellChecker" ref="spellChecker" />
       <property name="name" value="Generic Text Editor" />
   </bean>

   <bean id="spellChecker" class="com.tutorialspoint.SpellChecker">
</bean>
```

自动装配*byName*，xml配置文件为：

```
<bean id="textEditor" class="com.tutorialspoint.TextEditor" 
      autowire="byName">
      <property name="name" value="Generic Text Editor" />
   </bean>

   <bean id="spellChecker" class="com.tutorialspoint.SpellChecker">
   </bean>
   <!--根据属性名装配，private SpellChecker spellChecker;是后面的spellChecker；根据属性类型个装配是前面的SpellChecker-->
```

### Spring 自动装配 byType

byType是根据属性类型自动装配。在*TextEditor*，*SpellChecker*的例子中，如果一个bean（*TextEditor*）包含*SpellChecker* 类型的 *spellChecker* 属性，那么spring就会查找名为*spellChecker*的bean。

*TextEditor*，*SpellChecker*和 *MainApp*与byName相同。

TextEditor和自动装配byType,xml配置文件为：

```
<bean id="textEditor" class="com.tutorialspoint.TextEditor" 
      autowire="byType">
      <property name="name" value="Generic Text Editor" />
   </bean>

   <bean id="SpellChecker" class="com.tutorialspoint.SpellChecker">
   </bean>
```

### 构造函数自动装配

<center>HelloWorld obj = (HelloWorld) context.getBean("helloWorld");</center>

与byType类似。如果一个bean定义设置通过构造函数自动装配，并且带有*SpellChecker* 类型的构造函数，那么spring就会查找名为*spellChecker*的bean。

*TextEditor.java*生成带有*SpellChecker*类型的构造函数。

在基于构造函数的依赖注入，xml配置文件为：

```
 <bean id="textEditor" class="com.tutorialspoint.TextEditor">
      <constructor-arg  ref="spellChecker" />
      <constructor-arg  value="Generic Text Editor"/>
   </bean>

   <bean id="spellChecker" class="com.tutorialspoint.SpellChecker">
   </bean>
```

自动装配*by constructor*，xml配置文件为：

```
<bean id="textEditor" class="com.tutorialspoint.TextEditor" 
      autowire="constructor">
      <constructor-arg value="Generic Text Editor"/>
   </bean>

   <bean id="SpellChecker" class="com.tutorialspoint.SpellChecker">
   </bean>
```

## Spring 基于注解的配置

为什么要使用注解？用xml文件对bean进行注入或者配置aop、事务，会有两个缺点：1.所有内容在xml文件中，xml文件会很大，如果把需求分开，xml文件会很多，可读性和可维护性会很低。

java文件和xml文件相互切换，会造成思维不连贯降低开发效率。所以要引入注解，使用@XXX，让注解和Java Bean更紧密。

### Spring @Required 注释

用于注释bean属性的setter方法

定义一个实体类Student

```
public class Student {
   private Integer age;
   private String name;
   @Required
   public void setAge(Integer age) {
      this.age = age;
   }
   public Integer getAge() {
      return age;
   }
   @Required
   public void setName(String name) {
      this.name = name;
   }
   public String getName() {
      return name;
   }
}
```

MainApp.java内容如下：

```
public class MainApp {
   public static void main(String[] args) {
      ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
      Student student = (Student) context.getBean("student");
      System.out.println("Name : " + student.getName() );
      System.out.println("Age : " + student.getAge() );
   }
}
```

配置文件 Beans.xml文件的内容：

```
<bean id="student" class="com..Student">
      <property name="name"  value="Zara" />

      <!-- property name="age"  value="11"-->
   </bean>
```

当注释age属性时运行，会出现报错信息，说明@Required注释的属性，必须要在bean中配置。

### Spring @Autowired 注释

意思是自动装配，可以自动帮你把bean里面引用的对象的setter/getter方法省略，由它来setter/getter。引入@Autowired注解，spring配置文件不需要使用<property name="" ref=""/>

```
定义一个学生
public class Student{
		private String stu_name="lihua";
		public String toString(){
			return "StudentName:"+stu_name;
		}
}

定义一个老师
public class Teacher{
		private String course="math";
		public String toString(){
			return "course:"+course;
		}
}

定义一个学校，声明student变量。
public class School{
		@Autowired
        private Student student;
        @Autowired
        private Teacher teacher;
        public String toString(){
            return student+"\n"+teacher;
        }
    }

spring配置文件应该是：
	<context:component-scan base-package="com.xyz" />
	<!--在base-package指明了一个包，表明在com.tutorialspoint保以及自包中，如果某个类的头上带有特定注解@Component,@Repository,@Service,@Controller就会将这个对象作为Bean注入spring容器。存在context:component-scan,context:annotation-config可以不使用，它被包括在context:component-scan中-->

     <bean id="school"  class="com.xyz.School" />
     <bean id="Teacher"  class="com.xyz.Teacher" />
    <bean id="student" class="com.xyz.Student" />
```

当Spring发现@Autowired注解时，将自动在代码上下文找到匹配的Bean，并注入到相应的地方。

如果.xml文件里有<property name="" ref=""/>，School.Java中去掉了*getter/setter*方法，并使用*@Autowired*标注属性将会出现的结果是什么？Spring会按照xml优先原则去School.Java中寻找两个属性*getter/setter*，导致初始化bean报错。

如果去掉xml文件对于Student和Teacher的bean的配置，再运行会抛出异常。如果属性找不到又不想抛出异常，就是将*@Autowired*注解的required属性设置为false，这样Spring容器就会认为这两个属性为null，就像下面这样：

```
public class School{
		@Autowired(required=false)
        private Student student;
        @Autowired(required=false)
        private Teacher teacher;
        public String toString(){
            return student+"\n"+teacher;
        }
    }
```

#### @Autowired接口注入

写一个Person接口

```
public interface Person{
	public String personName();
}
```

写两个实现类Doctor和Police:

```
@Service
public class Doctor implements Person{
	public String doctorName(){
		return "Person Doctor";
	}
}

@Service 
public class Police implements Person{
	public String policeName(){
		return "Person Police";
	}
}
```

写一个PersonFactory，引用Person: 

```
@Service
public class PersonFactory{
	@Autowired
	private Person person;
	public String toString(){
		return person.policeName();
	}
}
```

Person接口有两个实现类，Spring不知道引用哪个实现类，会导致代码报错。 

要么删除一个实现类，Spring会自动去base-package下寻找Person接口的实现类，发现只有一个实现类，会直接引用这个实现类。

要么使用@Qualifier注解。

### Spring @Qualifier 注释

在创建多个具有相同类型的bean时，想要选择其中一个进行装配，可以用 **@Qualifier** 注释和 **@Autowired** 注释指定。

```
@Service
    public class PersonFactory{
        @Autowired
        @Qualifier("Doctor")
        private Person person;
        public String toString(){
            return person.personName();
        }
 }
```

@Qualifier注解括号里面必须是Person接口实现类的类名。

### Spring @Resource 注释

@Resource 注释使用一个 ‘name’ 属性，该属性以一个 bean 名称的形式被注入。你可以说，它遵循 **by-name** 自动连接语义

```
public class TextEditor {
   private SpellChecker spellChecker;
   @Resource(name= "spellChecker")
   public void setSpellChecker( SpellChecker spellChecker ){
      this.spellChecker = spellChecker;
   }
   public SpellChecker getSpellChecker(){
      return spellChecker;
   }
   public void spellCheck(){
      spellChecker.checkSpelling();
   }
}
```

@Resource的装配顺序：

@Resource后面没有任何内容，默认通过name属性去匹配bean,如果找不到再按type去匹配。 

指定了name或者type则根据指定的类型去匹配bean。

指定了name或者type则根据指定的name和type去匹配bean，任何一个匹配都不会有错。

####  @Autowired和@Resource两个注解的区别：

1. @Autowired默认按照byType方式进行bean匹配，@Resource默认按照byName方式进行bean匹配。
2. @Autowired是Spring的注解，@Resource是J2EE的注解，根据导入的注解和包名就能看出来。Spring属于第三方，J2EE是Java自己的东西，因此建议使用@Resource注解，减少代码和Spring之间的耦合。

放1. @A

### @Service注解

用于标注业务层组建。@Service可以简化xml文件配置，加入自动扫描标签，可以去掉bean的配置，增加java代码的内聚性。

配置文件：

```
<context:component-scan base-package="com.xyz" />
```

Student.java和Teacher.java保持不变，改变School.java

```
@Service 
  public class School{ 
       @Autowired 
       private Teacher teacher; 
      @Autowired 
       private Student student; 
       public String toString(){ 
             return teacher + "\n" + student; 
       } 
} 
```

这样School.java在Spring容器中的存在形式就是“school”，即可以通过ApplicationContext的getBean("school")方法得到School.java。

在这个过程里，@Service做了两件事： 声明School.java是一个bean。因为School.java是一个bean，其他的类才可以使用@Autowired将school作为成员变量自动注入。 School.java在bean中的id是“school”，即类名且首字母小写。

如果想让School.java在bean中的id是“School”，就不能通过上面的方法，而是加入@Scope注解。

```
@Service
    @Scope("prototype")
    public class School{
        @Autowired
        private Teacher teacher;
        @Autowired
        private Student student;
        public String toString(){
            return "TeacherCourse:" + teacher + "\nStudentName:" + student;
        }
    }
```

这样，就可以通过ApplicationContext的getBean(“School”)方法来得到School.java了。@Scope注解的“prototype“每次都会new一个新的原型出来。

### @Controller注解

用于标注控制层组件。

### @Repository注解

用于标注数据访问组件，即DAO组件。

### @Component注解

泛指组件，当组件不好归类的时候，用这个注解标注。

@Configuration 和 @Bean 注解



### Spring基于Java的配置

#### @Configuration 和 @Bean 注解

```
@Configuration
public class HelloWorldConfig {
   @Bean 
   public HelloWorld helloWorld(){
      return new HelloWorld();
   }
}
```

相当于下面的xml配置

```
<beans>
   <bean id="helloWorld" class="com.tutorialspoint.HelloWorld" />
</beans>
```

使用@Bean注释相当于告诉spring会返回一个对象（在这里是helloWorld对象，id是被注释的方法名，class是id标识的类的路径）。

配置类（存在@Configuration的类）可以声明多个@Bean，一旦定义了配置类，要使用*AnnotationConfigApplicationContext* 来加载并把他们提供给 Spring 容器。

```
public class HelloWorld {
   private String message;

   public void setMessage(String message){
      this.message  = message;
   }

   public void getMessage(){
      System.out.println("Your Message : " + message);
   }
}


public class MainApp {
   public static void main(String[] args) {
      ApplicationContext ctx = 
      new AnnotationConfigApplicationContext(HelloWorldConfig.class);

      HelloWorld helloWorld = ctx.getBean(HelloWorld.class);

      helloWorld.setMessage("Hello World!");
      helloWorld.getMessage();
   }
}
```

有@Bean时，表达对对方的依赖，可以直接调用，如下：

举*TextEditor*，*SpellChecker*的例子：

使用@Configuration和@Bean，写一个配置类*TextEditorConfig*，声明两个bean分别返回*TextEditor*和 *spellChecker*对象，其中*textEditor*调用*spellChecker*对象。

```
@Configuration
public class TextEditorConfig {
   @Bean 
   public TextEditor textEditor(){
      return new TextEditor( spellChecker() );
   }
   @Bean 
   public SpellChecker spellChecker(){
      return new SpellChecker( );
   }
}
```

TextEditor.java 文件的内容：

```
public class TextEditor {
   private SpellChecker spellChecker;
   public TextEditor(SpellChecker spellChecker){
      System.out.println("Inside TextEditor constructor." );
      this.spellChecker = spellChecker;
   }
   public void spellCheck(){
      spellChecker.checkSpelling();
   }
}
```

依赖的类文件 SpellChecker.java

```
public class SpellChecker {
   public SpellChecker(){
      System.out.println("Inside SpellChecker constructor." );
   }
   public void checkSpelling(){
      System.out.println("Inside checkSpelling." );
   }

}
```

 MainApp.java文件的内容：

```
public class MainApp {
   public static void main(String[] args) {
      ApplicationContext ctx = 
      new AnnotationConfigApplicationContext(TextEditorConfig.class);
      TextEditor te = ctx.getBean(TextEditor.class);
      te.spellCheck();
   }
}
```

#### @Import 注解:

是配置类之间的连接。

```
@Configuration
public class ConfigA {
   @Bean
   public A a() {
      return new A(); 
   }
}

@Configuration
@Import(ConfigA.class)
public class ConfigB {
   @Bean
   public B b() {
      return new B(); 
   }
}

public static void main(String[] args) {
   ApplicationContext ctx = 
   new AnnotationConfigApplicationContext(ConfigB.class);
   // now both beans A and B will be available...
   A a = ctx.getBean(A.class);
   B b = ctx.getBean(B.class);
}
```

在配置类B中导入配置类A声明的bean，在mainApp中实例化，不需要同时指定config.A和config.B，只要config.B。

#### 生命周期回调

初始化和销毁在@Bean中完成：@Bean(initMethod = "init", destroyMethod = "cleanup" )

```
public class Foo {
   public void init() {
      // initialization logic
   }
   public void cleanup() {
      // destruction logic
   }
}

@Configuration
public class AppConfig {
	@Bean(initMethod = "init", destroyMethod = "cleanup" )
   public Foo foo() {
      return new Foo();
   }
}
```

指定Bean的范围：在配置类中重写带有@Scope的方法。

```
@Configuration
public class AppConfig {
   @Bean
   @Scope("prototype")
   public Foo foo() {
      return new Foo();
   }
}
```



## Spring 框架的 AOP

AOP是面向方面的编程框架，面向方面要把程序逻辑分为不同的部分即关注点。跨一个应用程序的多个点的功能叫做横切关注点。

理解切面的含义：web层级设计中，web层-->网关层-->服务层-->数据层，每一层都是一个切面。编程中，对象与对象之间，方法与方法之间，模块与模块之间都是切面。

为什么要用到切面？举个例子，做活动对每个接口都做活动的有效验证（是否开始是否结束），每个接口都要写重复的验证开始和结束代码，会比较麻烦。因此把它作为公共方法，让接口都调用它。在接口调用时，引入切面的概念，把方法注入到接口调用的某个地方（切点）。

### AOP 术语

Aspect						切面。Aspect声明类似Java类声明。

Join point					连接点。具有明确定义的点。

Advice						增强。Advice 定义了在 Pointcut 里面定义的程序点具体要做的操作。

Pointcut					切点。表示一组Join point，连接点通过逻辑、正则表达式等方式集中起来，定义了									Advice将要发生的地方。

Target 						目标对象。织入Advice的目标对象。

Weaving						织入。 将Aspect和其他对象连接起来, 并创建 Advice d object 的过程。

对术语做个简单的理解  

在A城找一个身高175cm的男性，如果符合条件的都抓起来。

Joint point：A城人民。因为Joint point是所有可能被织入advice的候选点。在 Spring AOP中, 则可以认为所有方法执行点都是 Joint point。在例子中查找的全范围就是A城。

Pointcut：男性，身高175cm。Pointcut的作用是提供一组规则筛选符合条件的 Joint point。

Advice：抓起来。Advice是一个动作即一段java代码，这段代码作用在Pointcut筛选出的对象上。

Aspect：找到身高175cm的男性抓起来。它是Pointcut和Advice的组合。

### 通知类型

前置通知**@Before**								在一个方法执行之前，执行通知。

后置通知**@After**									在一个方法执行之后，不考虑结果，执行通知。

返回后通知**@AfterReturning**				在一个方法执行之后，只有方法成功完成时，才执行通知。

抛出异常后通知**@AfterThrowing**		在一个方法执行之后，只有方法退出抛出异常时，才执行通知。

环绕通知**@Around**	 							在建议方法调用之前和之后，执行通知。

### Spring 中基于 AOP 的 XML架构

需要使用到aop命名空间，导入spring-aop j架构

```
 xmlns:aop="http://www.springframework.org/schema/aop"
 xsi:schemaLocation="http://www.springframework.org/schema/aop 
 http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
```

在应用程序的CLASSPATH中使用AspectJ库文件。

- aspectjrt.jar
- aspectjweaver.jar
- aspectj.jar
- aopalliance.jar

#### 声明一个 aspect

```
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">
   ...
   </aop:aspect>
</aop:config>
<bean id="aBean" class="...">
...
</bean>
```

#### 声明一个切入点

```
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">
   <aop:pointcut id="businessService"
      expression="execution(* com.xyz.myapp.service.*.*(..))"/>
   ...
   </aop:aspect>
</aop:config>
<bean id="aBean" class="...">
...
</bean>
```

##### spring aop中pointcut表达式

```
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">
   <aop:pointcut id="businessService"
      expression="execution(* com.xyz.myapp.service.*.*(..))"/>
   ...
   </aop:aspect>
</aop:config>
<bean id="aBean" class="...">
...
</bean>
```

**pointcut表达式**

- 用execute表达

1. 拦截任意公共方法		              execution(public * *(..))*

2. 拦截以set开头的任意方法 		execution(* set*(..))

3. 拦截类或者接口中的方法           execution(* com.xyz.service.AccountService.*(..))  （拦截AccountService(类、接口)中定义的所有方法）

4. 拦截包中定义的方法，不包含子包中的方法     execution(* com.xyz.service.*.*(..))  （拦截com.xyz.service包中所有类中任意方法，不包含子包中的类）

5. 拦截包或者子包中定义的方法     execution(* com.xyz.service..*.*(..)) （拦截com.xyz.service包或者子包中定义的所有方法）

   



### @SpringBootApplication注解

这是SpringBoot的启动注解。@SpringBootConfiguration,@EnableAutoConfiguration,@ComponentScan三个注解就等于@SpringBootApplication注解的作用。

### @RestController 和@RequestMapping

@RestController 和@RequestMapping是springMVC的注解。

@RestController=@Controller+@ResponseBody ，作用是以json格式返回结果。

SpringBoot Controller接收参数

第一种：请求路径参数

*@PathVariable*

获取路径参数，采用url/{id}形式。

*@RequestParam*

获取查询参数，采用url?name=形式。

GET请求
http://localhost:8080/demo/123?name=admin

```
@GetMapping("/demo/{id}")
public void demo(@PathVariable(name = "id") String id, @RequestParam(name = "name") String name) {
    System.out.println("id="+id);
    System.out.println("name="+name);
}
```

输出结果：
id=123
name=admin

```
@GetMapping("/demo/{id}")
public void demo(@PathVariable(name="id") String id,@RequestParam(name="name") String name){
	System.out.println("id="+id);
	System.out.println("name="+name);
}
```

第二种：Body参数 

Body中的json格式为

{	"name":admin,

​		"age":18

}

```
@PostMapping(path = "/demo1")
public void demo1(@RequestBody Person person) {
    System.out.println(person.toString());
}
```



























