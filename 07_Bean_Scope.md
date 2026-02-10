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
