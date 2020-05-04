### IOC前言

####   对象A调用对象B中方法的方式

1.  在A类的成员变量位置创建B对象，在A的方法中通过成员变量的方式调用方法
2.  在A的成员变量的位置声明B为成员变量，通过构造函数或者Set方法进行实例化，在创建对象的时候调用



如果不使用IOC的思想，那么在经典的MVC架构中，controller层调用Service层的方法要在controller层声明Service层的实例，这有两个缺点： 1，如果Service的成员变量很多那么在controller层声明Service的对象的时候要使用有参构造或者调用set方法进行大量参数的赋值 2，如果service后期增加一个成员变量的话，那么不仅要修改service层，controller层的构造方法或者set方法也要进行修改

IOC意味着控制反转，是将类的创建,类的装配交给Spring进行，而不用我们主动去new .IOC 的实现离不开DI（依赖注入），传统方式进行类的调用都是在高层类中new 一个低层类的对象进行调用，而依赖注入则是将低层的类注入到高层类（构造方法，set方法），在实例化高层类的时候，会根据@AutoWired ，  xml配置文件的方式，不断去寻找当前类的注入类，直到找到一个最低层类，它不需要任何注入，将这个类进行实例化，然后再不断将底层类注入到高层类



#### 传统方式类的装配

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/spring/classic.png)

#### IOC下使用DI进行的类的装配

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/spring/ioc1.png)



![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/spring/ioc2.png)



**其中进行config和注入对于用户来说都是透明的，都是Spring帮助我们做好的，而我们只需要将底层类使用@Component，加入到IOC容器中，哪个地方要使用该底层类就直接@AutoWired即可**



### Spring创建实例的方法

1. 基于Class构建(set方法)

   ```java
   <bean class="com.tuling.spring.HelloSpring"></bean>
       
    <bean id="person02" class="com.atguigu.bean.Person">
   		<property name="lastName" value="小花"></property>
   </bean>   
   ```

2. 构造方法构建

   ```java
   <bean class="com.tuling.spring.HelloSpring">
       <constructor-arg name="name" type="java.lang.String" value="luban"/>
       <constructor-arg index="1" type="java.lang.String" value="sex" />
   </bean>
   ```

3. 静态工厂

    ```java
   <bean id="airPlane01" class="com.atguigu.factory.AirPlaneStaticFactory"
   		factory-method="getAirPlane">
   		<!-- 可以为方法指定参数 -->
   		<constructor-arg value="李四"></constructor-arg>
   </bean>
       //指定一份
       
   ```

4. 实例工厂

    ```java
   <bean id="airPlaneInstanceFactory" 
   		class="com.atguigu.factory.AirPlaneInstanceFactory"></bean>
               
               
    <bean id="airPlane02" class="com.atguigu.bean.AirPlane"
   		factory-bean="airPlaneInstanceFactory" 
   		factory-method="getAirPlane">
   		<constructor-arg value="王五"></constructor-arg>
   	</bean>      
        //指定两份
   ```

5. FactoryBean

   ```java
   @Component  注册工厂本质上注册的是对象
   public class MyFactoryBeanImple implements FactoryBean<Book>{
   
   	/**
   	 * getObject：工厂方法；
   	 * 		返回创建的对象
   	 */
   	@Override
   	public Book getObject() throws Exception {
   		// TODO Auto-generated method stub
   		System.out.println("MyFactoryBeanImple。。帮你创建对象...");
   		Book book = new Book();
   		book.setBookName(UUID.randomUUID().toString());
   		return book;
   	}
   
   	/**
   	 * 返回创建的对象的类型；
   	 * Spring会自动调用这个方法来确认创建的对象是什么类型
   	 */
   	@Override
   	public Class<?> getObjectType() {
   		// TODO Auto-generated method stub
   		return Book.class;
   	}
   
   	/**
   	 * isSingleton：是单例？
   	 * false：不是单例
   	 * true：是单例
   	 */
   	@Override
   	public boolean isSingleton() {
   		// TODO Auto-generated method stub
   		return true;
   	}
   
   }
   ```

### Spring创建对象的单例和多例

不指定的话就是单例，指定为Prototype才为多例

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/spring/Spring1.png)

当bean的作用域为单例时，Spring会在IOC容器对象创建时就创建bean的对象实例。而当bean的作用域为prototype时，IOC容器在获取bean的实例时创建bean的实例对象

单例模式下如果处于成员变量位置的话会出现问题，一个好的解决方式就是将单例对象保存到ThreadLocal中

### Bean的生命周期

Spring IOC容器可以管理bean的生命周期，Spring允许在bean生命周期内特定的时间点执行指定的任务

    1. 通过构造器或工厂方法创建bean实例
       2. 为bean的**属性设置值**和对其他bean的引用(property,ref)
       3. 将bean实例传递给bean后置处理器的**postProcessBeforeInitialization()**方法(如果实现了后置处理器BeanPostProcessor)
       4. 调用bean的**初始化**方法
       5. 将bean实例传递给bean后置处理器的**postProcessAfterInitialization()**方法(如果实现了后置处理器BeanPostProcessor)
       6. bean可以使用了
       7. 当容器关闭时调用bean的**销毁方法**

单例bean，容器启动的时候就会创建好，容器关闭也会销毁创建的bean，多实例bean，获取的时候才创建

```xml
<bean id="book01" class="com.atguigu.bean.Book"
		destroy-method="myDestory" init-method="myInit" ></bean>
```

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/spring/Spring%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)

[initializingbean](https://www.cnblogs.com/study-everyday/p/6257127.html)

关注Aware接口

### Bean的组件装配

1. 手动装配：以value或ref的方式**明确指定属性值**都是手动装配
2. 自动装配：根据指定的装配规则，**不需要明确指定**，Spring**自动**将匹配的属性值**注入**bean中

#### 自动装配的方式

1. 根据**类型**自动装配：将类型匹配的bean作为属性注入到另一个bean中。若IOC容器中有多个与目标bean类型一致的bean，Spring将无法判定哪个bean最合适该属性，所以不能执行自动装配
2. 根据**名称**自动装配：必须将目标bean的名称和属性名设置的完全相同
3. 通过构造器自动装配：当bean中存在多个构造器时，此种自动装配方式将会很复杂。不推荐使用。

#### 自动装配的实现

    1. @Autowired注解：根据类型实现自动装配。
       2. 构造器、普通字段(即使是非public)、一切具有参数的方法都可以应用@Autowired注解（参数可以通过@AutoWired进行实例化）
       3. 默认情况下，所有使用@Autowired注解的属性都需要被设置。当Spring找不到匹配的bean装配属性时，会抛出异常
       4. 若某一属性允许不被设置，可以设置@Autowired注解的required属性为 false
       5. 默认情况下，当IOC容器里存在多个类型兼容的bean时，Spring会尝试匹配bean的id值是否与变量名相同，如果相同则进行装配。如果bean的id值不相同，通过类型的自动装配将无法工作。此时可以在@Qualifier注解里提供bean的名称。Spring甚至允许在方法的形参上标注@Qualifiter注解以指定注入bean的名称。
       6. @Autowired注解也可以应用在数组类型的属性上，此时Spring将会把所有匹配的bean进行自动装配。
       7. @Autowired注解也可以应用在集合属性上，此时Spring读取该集合的类型信息，然后自动装配所有与之兼容的bean
       8. @Autowired注解用在java.util.Map上时，若该Map的键值为String，那么 Spring将自动装配与值类型兼容的bean作为值，并以bean的id值作为键
       9. @Resource注解要求提供一个bean名称的属性，若该属性为空，则自动采用标注处的变量或方法名作为bean的名称
       10. @Inject和@Autowired注解一样也是按类型注入匹配的bean，但没有reqired属性。

#### 自动装配的实现原理

在指定要扫描的包时，<context:component-scan> 元素会自动注册一个bean的后置处理器：  AutowiredAnnotationBeanPostProcessor的实例。该后置处理器可以自动装配标记了**@Autowired**、@Resource或@Inject注解的属性。

#### Spring的事务传播机制

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/spring/%E4%BA%8B%E5%8A%A1%E4%BC%A0%E6%92%AD%E6%9C%BA%E5%88%B6.png)



### Spring源码

####    Bean的构建过程

​    ioc 实现中 我们在xml 中描述的Bean信息最后 都将保存至BeanDefinition （定义）对象中，其中xml bean 与BeanDefinition 程一对一的关系。

![](https://images-cdn.shimo.im/ZZPgXODJu6wxOqyS/image.png!thumbnail)

![](https://images-cdn.shimo.im/I4KQrj4LMMsDFoWF/image.png!thumbnail)

![](https://images-cdn.shimo.im/e95AoECmWtoGnXP1/image.png!thumbnail)

### AOP

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/spring/aop1.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/spring/aop1.png)

**基于JDK 动态代理要求类实现了接口**

**基于Cglib动态代理要求类不能使用final修饰，因为Cglib基于继承实现**

