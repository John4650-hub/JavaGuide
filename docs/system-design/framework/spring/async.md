I don't...
Title: Async
Category: Frame
Tag:

- Spring.
  I don't...

`@Async` is provided by the Spring framework, and the class or method indicated by that note will be implemented in **the walk-through**. This means that when the method is called, the caller will not wait for its implementation to be completed but will continue to implement the subsequent code.

The use of `@Async` notes is simple and requires two steps:

1. Add a note `@EnableAsync` to the start class to start a different task.
1. Add a note `@Async` to the method or category that requires step-by-step execution.

```Java
@SpringBootApplication
// Start a different task
@EnableAsync
public class Main {
    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }
}

// Institutional Services
@Service
public class MyService {
    // Recommended use of a custom thread pool, here only to demonstrate basic usage
    @Async
    public CompletableFuture<String> asyncMethod() {
        // There will be some time-consuming operations here
        // ...
        // Use CompletableFuture to handle more easily the results of the different missions and avoid blocking the main route
        return CompletableFuture.completedFuture("Async Task Completed");
    }
}
```

Next, let's look at the bottom theory of `@Async`.

`@Async`

`@Async`, which is capable of evaporating, is essentially accomplished by using **Dynamic Agent**. The call to the `@Async` description method will be intercepted by the dynamic agent, who will then submit the method's execution in the interceptor to the silo for processing.

Next, let's take a detailed analysis.

# Turn on the walk

Before using `@Async`, you need to add `@EnableAsync` to the start class to open a walk, `@EnableAsync`, as follows:

```Java
// omit other notes ...
@Import
public @interface EnableAsync {
}
```

`AsyncConfigurationSelector` was introduced by `@Import` note on `@EnableAsync`, so Spring will load the class introduced by `@Import` note.

The `ImportSelector` interface has been achieved in the `AsyncConfigurationSelector` class, so the `selectImports()` method is rewritten in this class from the logic of defining the loading of Bean as follows:

```Java
@Override
@Nullable
public String[] selectImports(AnnotationMetadata importingClassMetadata) {
    switch (adviceMode) {
        // Notification based on JDK proxy weave
        case PROXY:
            return new String[]{ProxyAsyncConfiguration.class.getName()};
        // Notification based on AspectJ
        case ASYNC_EXECUTION_ASPECT_CONFIGURATION_CLASS_NAME:
            return new String[]{ASYNC_EXECUTION_ASPECT_CONFIGURATION_CLASS_NAME};
        default:
            return null;
    }
}
```

In the `selectImports()` method, different classes are selected for loading depending on the type of notice, where the default value `adviceMode` is `PROXY`.

This is an example of a notice based on JDK agents, at which point the category `ProxyAsyncConfiguration` is loaded, as follows:

```Java
@Configuration
@Role
public class ProxyAsyncConfiguration extends AbstractAsyncConfiguration {
    @Bean(name = TaskManagementConfigUtils.SYNC_ANNOTATION_PROCESSOR_BEAN_NAME)
    @Role
    public AsyncAnnotationBeanPostProcessor asyncAnnotationBeanPostProcessor() {
        // Loading back-processor
        AsyncAnnotationBeanPostProcessor bpp = new AsyncAnnotationBeanPostProcessor();
        return bpp;
    }
}
```

# Backup processor

In the category `ProxyAsyncConfiguration`, a rear processor `AsyncAnnotationBeanPostProcessor`, which is the key to making `@Async` note work, is loaded with `AsyncAnnotationBeanPostProcessor`.

`@Async` notes that the `AsyncAnnotationBeanPostProcessor` processor creates a dynamic agent for this category.

The method in this category will be intercepted by the interceptor of the agent, where the method of denoting the mark will be performed at a different pace.

`AsyncAnnotationBeanPostProcessor` code as follows:

```Java
public class AsyncAnnotationBeanPostProcessor extends AbstractBeanFactoryAwareAdvisingPostProcessor {
    @Override
    public void setBeanFactory(BeanFactory beanFactory) {
        super.setBeanFactory(beanFactory);
        // Create AsyncAnnotationAdvisor, an Advisor
        // For intercepting methods with @Async note and implementing them differently.
        AsyncAnnotationAdvisor advisor = new AsyncAnnotationAdvisor();
        // If a custom annotation type is set, set it to an advisor.
        // asyncAnnotationType specifies a custom step note, for example @MyAsync.
        if (this.asyncAnnotationType != null) {
            advisor.setAsyncAnnotationType(this.asyncAnnotationType);
        }
        this.advisor = advisor;
    }
}
```

The `AsyncAnnotationBeanPostProcessor` parent(s) achieved the `BeanFactoryAware` interface, and the \`set
