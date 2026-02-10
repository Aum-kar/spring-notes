# Bean Scopes

**Bean scope** in Spring defines **how many instances of a bean are created and how long they live within the Spring container**.

In simple terms:

> Bean scope controls the lifecycle and visibility of a bean—whether Spring creates one shared object or multiple objects and when they are destroyed.

The default bean scope in Spring is **singleton**, meaning one instance per Spring container.


- There are five basic scopes for bean:
1. Singleton (default)
2. Prototype
3. Request
4. Session
5. Application

## Example of Singleton

> In singleton scope, Spring creates one shared bean instance per container, whereas in prototype scope, a new instance is created every time the bean is requested.

- consider below program with Three beans:
- It has `App` as entrypoint.
- `Harrier`, `Compass` and `Engine` are beans.
- `Harrier` and `compass` are dependent on `Engine`.

### Entrypoint of the Program
```java
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        ApplicationContext context =
        SpringApplication.run(MidlightApplication.class, args);

        Compass car1 = context.getBean(Compass.class);
        Harrier car2 = context.getBean(Harrier.class);
        Engine engine = context.getBean(Engine.class);

        engine.setValues("2.0L Multijet turbo-diesel engine", "Fiat");
        engine.displayConfiguration();

        car1.displayConfiguration();
        car2.displayConfiguration();
    }
}
```

### Engine Bean
```java
@Component
//@Scope("prototype")
public class Engine {
    private String name;
    private String vendor;

    public void setValues(String name, String vendor) {
        this.name = name;
        this.vendor = vendor;
    }

    public void displayConfiguration() {
        System.out.println("Engine: "+name+"\nManufacturer: "+vendor+"\n---");
    }
}
```

### Dependent Beans
```java
@Component
class Compass {
    Engine engine;
    public Compass(Engine engine) {
        this.engine = engine;
    }

    public void displayConfiguration() {
        System.out.println("Vehicle Name: Jeep Compass");
        engine.displayConfiguration();
    }
}

@Component
class Harrier {
    Engine engine;
    public Harrier(Engine engine) {
        this.engine = engine;
    }

    public void displayConfiguration() {
        System.out.println("Vehicle Name: Tata Harrier");
        engine.displayConfiguration();
    }
}

```

- In this example, both `Harrier` and `Compass` uses same `Engine`.
- The default scope for any Spring bean is Singleton.
- That means, during the runtime, Spring will create only one instance of the bean.
- The public sources of bean will be shared with the other two beans.
- The properties we had set for `Engine` will be shared with both `Harrier` & `Compass` bean.
- Try making Engine scope to prototype!

## Singleton-scoped Bean vs Static Class

At first glance, a static class and a singleton-scoped Spring bean may look similar because both provide a single shared instance. However, they are fundamentally different. Spring beans provide much more control over lifecycle, configuration, and behavior than static classes.

> Static classes are JVM-managed globals, whereas Spring singleton beans are container-managed objects with lifecycle, dependency injection, and configurability.

| Aspect               | Static class | Spring Singleton Bean               |
| -------------------- | ---------------------- | ----------------------------------- |
| Creation             | When class is loaded   | Managed by Spring container         |
| Lifecycle            | Lives until JVM stops  | Controlled by Spring                |
| Dependency Injection | Not supported        | Supported                         |
| Testability          | Hard to mock           | Easy to mock/replace                |
| Scope control        | Always global          | Configurable via scopes             |
| AOP support          | Not possible         | Possible (transactions, logging)  |
| Container management | Outside Spring         | Fully managed by Spring             |


# Prototype-scope
Prototype bean in Spring is a bean scope where a new instance is created every time the bean is requested from the Spring container.

**Explanation:**
Unlike singleton beans, prototype beans are created lazily and not shared. Spring creates a prototype bean only when it is requested (through dependency injection or `getBean()`), and after creation, **Spring does not manage its lifecycle**. Each consumer gets its own independent object.

> In prototype scope, Spring creates a new bean instance on every request and does not manage it after creation.

Consider the example below, This project has three beans:
1. Harrier  – singleton scoped
2. Compass  – prototype scoped
3. Engine   – prototype scoped

### Entrypoint

```java
@SpringBootApplication
public class MidlightApplication {

    public static void main(String[] args) {
        SpringApplication.run(MidlightApplication.class, args);
    }
}
```

### Singleton-scoped Bean

- Spring eagerly creates singleton beans at application startup.
- Therefore, Harrier is created during startup.
- While creating Harrier, Spring also creates an Engine instance because Harrier depends on Engine.

```java
@Component
class Harrier {
    Engine engine;
    public Harrier(Engine engine) {
        this.engine = engine;
        System.out.println("Harrier object created");
    }

    public void displayConfiguration() {
        System.out.println("Vehicle Name: Tata Harrier");
        engine.displayConfiguration();
    }
}
```

### Prototype-scoped Bean

- Prototype beans (Compass and Engine) are created only when requested.
- Since Compass is never requested, its object is not created.
- Spring registers all beans, but instantiates prototype beans lazily.

```java
@Component
@Scope("prototype")
class Compass {
    Engine engine;
    public Compass(Engine engine) {
        this.engine = engine;
        System.out.println("Compass object created");
    }
}

// -------------------------------------------------

@Component
@Scope("prototype")
public class Engine {
    public Engine() {
        System.out.println("Engine object created");
    }
}
```
