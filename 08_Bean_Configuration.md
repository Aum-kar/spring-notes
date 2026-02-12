# Bean Configuration

Bean configuration is the process of defining how Spring should create, configure, and manage objects (beans).
It tells Spring:

- What objects to create
- How to create them
- What dependencies to inject
- What scope they should have

Three Main Ways to Configure Beans in Spring

- Annotation-Based Configuration
- Java-Based Configuration
- XML Configuration

## Java Based Configuration

When we configure bean inside @Configuration class, we don't need our bean to steriotype using bean annotations.

To configure in java based configuration, we have to create a @configuration class, then create a method that has Bean return type, annotate it with @Bean. Create a new Bean object inside it using required Bean parameterized constructor and return it.

### Example

Below is the simple example of Bean configuration:

1. It has an entrypoint `TwilightApplication`
2. A bean `SimpleComponent`
3. A Configuration class `SimpleComponentConfiguration`

#### Entrypoint

Don't worry, variable names are longer not the concept!

```java
@SpringBootApplication
public class TwilightApplication {

    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(TwilightApplication.class, args);
        SimpleComponent simpleComponent = context.getBean(SimpleComponent.class);
        System.out.println(simpleComponent.getName());
    }
}
```

#### Bean

Look at the `SimpleComponent` bean, it is not steriotyped using bean annotations.

```java
public class SimpleComponent {
    private String name;

    public SimpleComponent(String name) {
        this.name = name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

#### Configuration class

Below is how we have configured our required bean using Java based configuration and injecting them using Qualifier.

- We have used @Configuration annotation to make it config class.
- We have used @Bean annotation to configure our bean.
- `simpleComponent()` is called as **bean factory method.**

```java
@Configuration
class SimpleComponentConfiguration {
    @Bean
    public SimpleComponent simpleComponent() {
        return new SimpleComponent("Saurabh");
    }
}
```

How we are configuring bean?

- Inside configuration class, we create a method.
- Annotate the method with @Bean.
- Sets its return type to our bean class.
- We create an object of our bean and return it.

What if we have multiple bean configurations inside @configuration class?

- If we have multiple **bean factory methods** (i.e., methods annoted as @Bean inside @Configuration class), spring will throw .`NoUniqueBeanDefinitionException`.
- There are two ways to solve this issue.

1. Either set the `@Primary` annotion to required bean inside Configuration class.
2. Or define `@Qualifier("configClassMethod")` while injecting bean in constructors

##### How to setup Qualifier?

Below is the example of bean qualifier.

`Engine` - A class using this we will configure two beans and inject them as per business requirements.

```java
class Engine {
    int power;
    int volume;

    public Engine(int power, int volume) {
        this.power = power;
        this.volume = volume;
    }

    @Override
    public String toString() {
        return "Engine Configuration: " + "power=" + power + ", volume=" + volume + "\n---";
    }
}
```

`EngineConfiguration` - We are configuring two beans - `compassEngine` and `harrierEngine`

```java
@Configuration
class EngineConfiguration {
    @Bean
    public Engine compassEngine() {
        return new Engine(300, 4);
    }

    @Bean
    public Engine harrierEngine() {
        return new Engine(200, 2);
    }
}
```

Consuming the beans in compass and harrier.

```java
@Component
class Compass {
    public Compass(@Qualifier("compassEngine") Engine engine) {
        System.out.println("Vehicle name: Compass"+"\n"+engine);
    }
}
```

```java
@Component
class Harrier {
    public Harrier(@Qualifier("harrierEngine") Engine engine) {
        System.out.println("Vehicle name: Harrier"+"\n"+engine);
    }
}
```

## Annotation Based Configuration

In annotation-based configuration, we can have multiple beans of the same class by defining multiple @Bean methods with different names. @Component alone creates only one bean per class

```java
@Component
class EngineProvider {

    @Bean("compassEngine")
    public Engine compassEngine() {
        return new Engine(300, 4);
    }

    @Bean("harrierEngine")
    public Engine harrierEngine() {
        return new Engine(200, 2);
    }
}
```

Above annotation based configuration is equivalent to

```java
// Java based configuration
@Configuration
class EngineConfiguration {
    @Bean
    public Engine compassEngine() {
        return new Engine(300, 4);
    }

    @Bean
    public Engine harrierEngine() {
        return new Engine(200, 2);
    }
}
```
