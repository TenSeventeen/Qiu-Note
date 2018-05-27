# Spring-framework-reference---读书笔记7

## 7. The IoC container

1.org.springframework.beans包和org.springframework.context包是spring框架IOC容器的基础。

2.基于java的配置：从spring3.0开始，许多spring JavaConfig工程中的特性已经加入了spring框架的核心包。以方便你在应用中通过java来定义bean而不是使用xml文件。如果需要使用这些新特性，见@Configuration、@Bean、@Import和@DependsOn注解。

3.可以使用AspectJ来控制没有被IOC容器包含的外部bean。

4.在name中使用$符号用于区分内部类和外部类。com.example包下有个类叫Foo，并且Foo有个内部类叫Bar，那么class属性的值应该是com.example.Foo$Bar。

### 7.2 容器概览

#### 7.2.1 Configuration metadata 

​        spring IOC容器处理一系列配置的元数据，这些元数据代表了你的应用是如何告诉spring容器来初始化实例、配置和集合应用的object。

​        基于java的配置：从spring3.0开始，许多spring JavaConfig工程中的特性已经加入了spring框架的核心包。以方便你在应用中通过java来定义bean而不是使用xml文件。  

​        spring的配置中至少有一个或多个bean的定义需要容器来管理。java配置通常使用在@Configuration的类中定义@Bean修饰的方法。

​       字符串类型的id属性是你用来唯一定义bean的标识。class属性定义了bean的类型并且需要使用全限定的类名。value属性定义了bean包含的object。

### 7.4 Dependencies 

#### 7.4.1 Dependency Injection 

依赖注入有两种主要的方式，构造注入和基于setter方法的注入。

​        基于构造器注入是通过容器调用带有参数的构造方法实现的，每个参数代表一个依赖。构造器参数解析产生在使用参数的类型。如果一个构造器参数的bean定义没有潜在的歧义存在，那么构造器中参数的位置和提供给构造的参数依赖必须是相同的顺序。

​        1.可以在构造器的参数声明中指定type属性。

​        2.使用index来声明构造器参数的位置。

​        3.可以使用构造器参数的name来消除value的歧义。

​        基于setter方法的依赖注入，是容器首先调用无参数的构造方法或无参数的静态工厂方法实例bean之后调用setter方法来最终完成bean的初始化。

​       ApplicationContext对于他管理的bean支持构造器注入和setter方法注入。基于setter的依赖注入也支持和构造器注入一同使用。

​       大部分spring的用户不会直接这样操作这些类，而是使用xml定义bean、或使用注解修饰组件、或使用Java的注解类@Bean。这些资源会在内部转换为BeanDefinition的实体被spring的IOC容器全部加载。

​       混合使用构造器和setter注入是一个不错的习惯，如果将构造器注入作为主、setter方法或配置方法作为可选项依赖。在set方法上使用@Required注解会使得这个属性成为必要的依赖。

​      spring的小组成员建议使用构造器注入，因为这样可以使得组件成为不可变object，并且可以确保依赖不为null。而且构造器注入返回给客户端代码的总是一个已经初始化好的状态。如果构造器的参数过多也不是很好，这样一个class的压力会很大，建议将属性拆分重构。

​       如果可以提供默认值的话可以考虑使用setter方法的注入。另外，在使用依赖的时候必要的非空检查是需要的。使用setter方法注入的好处就是可以在晚些的时候注入属性。

​       使用依赖注入的方式依赖于特殊的类。处理第三方的类时，如何处理由你来选择。例如，一个第三方类并未提供setter方法，那么构造器注入就是唯一可用的依赖注入方法。

#### 7.4.2 Dependencies and configuration in detail 

​        property/>元素定义的内容或构造器参数定义的内容都是方便阅读的string形式。spring的转换服务将这些值转化为实际中需要的实际类型。

### 7.9 Annotation-based container configuration 

​        通常取决于开发者决定哪个策略更适合。注解提供了很多他们定义中的上下文，来实现简单方便的配置。然而，xml文件可以在不修改源代码的情况下惊醒配置。一些开发者倾向于接近源代码，另外一些则认为注解会破坏pojo并且使得配置难以被控制。

​       不管哪一种选择，spring都可以使用甚至可以混合使用。可以通过java配置选项，spring也允许非侵入的方式，不触及目标源代码，所有的配置风格在spring tool suite中都支持。

​       注解的注入在xml注入之前。

**@Required**

​        这个注解指明bean的属性必须在配置时被赋予，在bean定义中注入或自动注入。如果没有被注入会抛出一个bean属性的异常，这样可以避免后续的空指针异常。并且强烈建议你断言类本身在初始化方法中。这样可以强制在引用即便是这个类在容器外部使用。

**@Autowired**

​        在JSR330中@Inject注解可以用于替代spring的@Autowired注解。

​       你可以在构造器上使用@Autowired注解。

​       在spirng4.3中，@Autowired可以不在修饰构造器如果目标bean只有一个构造器时。如果有多个构造器，则至少有一个需要被修饰来告诉容器使用哪个构造器。

​       @Autowired注解也可以用于传统的setter方法。也可以用在具有多个参数的方法上，也可以在属性和构造器上混合使用，每个类中只有一个被注解修饰的构造器可以是必须的，多个非必须的构造器时允许的。在这种情况下，在每个符合条件的构造器中，spring会选择参数最多那个构造器。

​        advice use @Autowired注解来替换@Required注解。required属性可以指定属性不是自动注入为目的，如果不被自动注入的话会被忽略。@Required，在另一方面，强制属性以任何容器支持的方法设置。如果没有可以设置的值会抛出一个异常。

​        @Autowired、@Inject、@Resource和@Value注解通过spring的BeanPostProcessor实现来处理，也就意味着你不能使用这些注解在你自己的BeanPostProcessor或BeanFactoryPostProcessor类型中。这些类型必须通过xml或用@Bean来修饰。

**@Qualifier**

​        @Primary在有多个实例时确定一个主要的类型来注入是有效的方法。当需要在进一步控制选择过程时可以使用spring的@Qualifier注解。你可以使用qualifier的值与特定的参数连接起来，缩小类型的匹配范围以便于每个参数选择特定的bean。在最简单的例子中，可以指定描述的值。

​       @Autowired可以应用于属性、构造器和多参数的方法，允许缩小范围通过qualifier注解在参数级别。作为区别，@Resource只能在属性或bean的set方法上修饰单个参数。作为一个结论，如果你需要通过构造器注入或多参数方法注入时可以考虑qualifier注解。

**@Resource**

​       @Resource注解有一个name属性，默认使用bean的名字来实现注入。

​       @Repository注解用于描述存储的角色（常见的就是DAO）。

​      spinrg提供了很多模板注解：@Component、@Service和@Controller。@Component是用于spring管理组件的泛型模板。@Repository、@Service和@Controller是@Component的特例用于更清晰的定义，例如，在持久化、服务和表现层。你可以将你的组件类都声明为@Component，但是通过@Repository、@Service和@Controller会更加的方便出来并与切面相互结合。例如，这些策略可以成为目标的切入点。而且这些在spirng框架的后续版本中会有特殊的功能。如果你在服务层纠结使用@Component还是@Service，那很明显@Service是更好的选择。同样，在开始的时候，@Repository支持持久层的异常translation。

​        所有spring提供的注解都可以作为你代码中的元注解。一个元注解可以很简单的运用在另一个注解上。例如，@Service注解就在元注解@Component上。

​       元注解也可以合并用于创建组合注解。例如，spring mvc中的@RestController注解就是@Controller和@ResponseBody的组合。

#### 7.10.3 Automatically detecting classes and registering bean definitions 

​      spring可以自动探测模板类并且使用ApplicationContext来注册相应的bean定义。

​       需要被自动探测的类需要注册相应的bean，你需要在@Configuration类上添加@ComponentScan注解，然后基本包路径就是为两个类的通用父包。

​      使用<context:component-scan>暗示着允许<context:annotation-config>的功能。所以当使用<context:component-scan>时可以忽略<context:annotation-config>。

​      用于扫描的包路径必须要以相对路径来表示。

​       AutowiredAnnotationBeanPostProcessor和CommonAnnotationBeanPostProcessor都隐含的包含了当你使用组件扫描元素时。这就意味着这两个组件已经被扫描注入了，不需要在xml中提供bean的配置。

​       正常spring组件中的@Bean方法和用@Configuration修饰的spring类处理方式不同。区别在于@Component修饰的类不会使用CGLIB来加强以实现对方法和属性的调用。cglib代理意味着在@Configuration的类中调用方法和属性，创建的bean元数据指向组合object。这样的方法不再被java声明调用，而是通过容器用于提供通常的生命周期管理和spring的bean的代理通过调用@Bean方法执行其他的bean。相对的，使用普通的@Component类可以调用@Bean方法，而不再使用特殊的cglbi处理或其他假设的应用。

​       像@Autowired注解一样，@Inject也可以修饰field级别、方法级别和构造器参数级别。更进一步，你可以定义你自己的注入点作为Provider，允许依赖访问小范围或延迟访问其他bean通过Provider.get()方法。  

#### 7.11.1 Dependency Injection with @Inject and @Named 

​       像@Autowired注解一样，@Inject也可以修饰field级别、方法级别和构造器参数级别。更进一步，你可以定义你自己的注入点作为Provider，允许依赖访问小范围或延迟访问其他bean通过Provider.get()方法。 

### 7.12 Java-based container configuration

​           @Bean注解用于指定配置和初始化由spring的ioc容器管理的实例。@Bean注解和beans元素中的bean元素有相同的作用。你可以在@Component中使用@Bean注解方法，然而，最常用的是@Configuration修饰bean。

​        当一个@Bean的方法定义在一个没有@Configuration修饰的类中时，他们将会以简化的模式被处理。例如，一个定义在@Component中的bean方法或一个普通的类将会被简化处理。

​       只有@Configuration修饰的类中的@Bean方法才会以full模式的形式被处理。这样可以避免相同的@Bean方法偶尔的被调用多次，也可以减少在lite模式下问题的追踪。

​       spring的AnnotationConfigApplicationContext是在spring3.0中加入的。这个ApplicationContext的实现允许只有@Configuration修饰的类作为输入，也允许普通的@Component类和使用JSR330元数据的注解类。

​       JSR 330 ，提供了一种可重用的、可维护、可测试的方式来获取Java对象。也称为Dependency Injection 。

​       当@Configuration修饰的类作为输入，@Configuration类本身会注册为一个bean的定义，并且所有该类定义的@Bean方法也会注册为bean的定义。

​        默认情况下，使用java配置的bean定义有一个公共的关闭方法在销毁时被回调。如果有公共的销毁回调方法，但是你不希望在容器关闭时调用，那么可以使用@Bean(destroyMethod="")来使得你的bean不调用默认关闭方法。

```
@Configuration
public class AppConfig {
    
    @Bean
    public Foo foo() {
        return new Foo(bar());
    }

    @Bean
    public Bar bar() {
        return new Bar();
    }

}
```

​       这种方法定义内部bean依赖只使用与@Configuration类中的@Bean方法。你不可以在普通的@Component类中定义内部bean依赖。