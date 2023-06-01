

## Definitions

### What is Bean in spring ?
In Spring, a "bean" is an object that is instantiated, assembled, and managed by the Spring IoC (Inversion of Control) container. A bean is essentially an instance of a class that is stored in the Spring application context, and it can be accessed by other parts of the application through dependency injection.

A Spring bean can be defined in a variety of ways, including:

1. Using XML configuration: Beans can be defined in an XML file and then loaded into the Spring application context.
2. Using Java configuration: Beans can be defined in a Java class annotated with @Configuration.
3. Using component scanning: Beans can be automatically detected and registered by the Spring container using annotations like @Component, @Service, @Controller, etc.

## Annotations

### @Controller

### @Repository

### @Service
In Spring, the @Service annotation is used to indicate that a particular class is a **service component** in the application. A service component is a class that performs some specific business logic, such as retrieving data from a database, performing calculations, or calling external APIs.

**When a class is marked with the @Service annotation, Spring will automatically detect it and create an instance of it as a bean in the application context.** This allows other parts of the application to easily access and use the service component through **dependency injection**.

The @Service annotation is one of several annotations in the Spring framework that are used to manage components in a Spring application. Other common annotations include @Controller, @Repository, and @Component.

### @Component
Components are used to define a class as a **Spring-managed bean**. These are typically used to create objects that provide some functionality or behavior that can be injected into other parts of the application. The @Component annotation is the base annotation for all Spring-managed components, and other annotations such as @Controller, @Repository, and @Service are specialized variants of @Component.

components are Spring-managed beans that provide specific functionality or behavior, while services are higher-level constructs that encapsulate business logic and may use one or more components to provide their functionality.


### @AutoWired
In Spring, the @Autowired annotation is used to automatically wire (inject) dependencies into a Spring-managed bean. Dependencies refer to other Spring-managed beans that a bean depends on to perform its functionality.

When a class is marked with the @Autowired annotation, Spring will automatically identify the dependencies of the class and inject them into the class at runtime. This eliminates the need for manual configuration of dependencies in XML or Java configuration files.

There are several ways to use the @Autowired annotation, such as:

Constructor Injection: The @Autowired annotation can be used on a constructor to inject dependencies.
Setter Injection: The @Autowired annotation can be used on a setter method to inject dependencies.
Field Injection: The @Autowired annotation can be used on a class field to inject dependencies.
The @Autowired annotation is a key feature of Spring's dependency injection mechanism and is widely used in Spring applications to manage dependencies between components.

### @InjectMocks
In Mockito, the @InjectMocks annotation is used to inject mock objects into a class being tested, and is typically used in conjunction with the @Mock annotation.

When a class is marked with the @InjectMocks annotation, Mockito will attempt to inject any objects annotated with @Mock or @Spy into the class being tested. This can help simplify the process of setting up test cases by automatically creating and injecting mock objects where needed.

@InjectMocks only works with fields, not constructor or setter injection. 

### @ExtendWith / @SpringBootTest
If you are using JUnit 4, do not forget to also add @RunWith(SpringRunner.class) to your test, otherwise the annotations will be ignored. If you are using JUnit 5, there is no need to add the equivalent @ExtendWith(SpringExtension.class) as @SpringBootTest and the other @…​Test annotations are already annotated with it.

