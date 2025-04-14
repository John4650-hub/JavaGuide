---
title: Analysis of the Principle of @Async Annotation
category: Framework
tag:
  - Spring
---

The `@Async` annotation is provided by the Spring framework, and classes or methods marked with this annotation will be executed in **asynchronous threads**. This means that when the method is called, the caller will not wait for the method to complete execution but can continue executing subsequent code.

Using the `@Async` annotation is very simple and requires two steps:

1. Add the `@EnableAsync` annotation to the startup class to enable asynchronous tasks.
2. Add the `@Async` annotation to the methods or classes that need to be executed asynchronously.

```java
@SpringBootApplication
// Enable asynchronous tasks
@EnableAsync
public class YourApplication {

    public static void main(String[] args) {
        SpringApplication.run(YourApplication.class, args);
    }
}

// Asynchronous service class
@Service
public class MyService {

    // It is recommended to use a custom thread pool; this is just a demonstration of basic usage
    @Async
    public CompletableFuture<String> doSomethingAsync() {

        // There will be some time-consuming business operations here
        // ...
        // Using CompletableFuture makes it easier to handle the results of asynchronous tasks, avoiding blocking the main thread
        return CompletableFuture.completedFuture("Async Task Completed");
    }

}
```

Next, let's take a look at the underlying principles of `@Async`.

## Analysis of @Async Principles

The `@Async` annotation can execute tasks asynchronously, which is essentially implemented using **dynamic proxies**. Through Spring's post-processor `BeanPostProcessor`, a dynamic proxy is created for classes using the `@Async` annotation. Subsequently, calls to methods annotated with `@Async` will be intercepted by the dynamic proxy, and the execution of the method will be encapsulated as an asynchronous task submitted to a thread pool for processing.

Next, let's analyze this in detail.

### Enabling Asynchronous Execution

Before using `@Async`, you need to add `@EnableAsync` to the startup class to enable asynchronous execution. The `@EnableAsync` annotation is as follows:

```JAVA
// Other annotations omitted ...
@Import(AsyncConfigurationSelector.class)
public @interface EnableAsync { /* ... */ }
```

The `@EnableAsync` annotation imports `AsyncConfigurationSelector` through the `@Import` annotation, so Spring will load the classes introduced by the `@Import` annotation.

The `AsyncConfigurationSelector` class implements the `ImportSelector` interface, so it overrides the `selectImports()` method to customize the logic for loading Beans, as follows:

```JAVA
public class AsyncConfigurationSelector extends AdviceModeImportSelector<EnableAsync> {
	@Override
	@Nullable
	public String[] selectImports(AdviceMode adviceMode) {
		switch (adviceMode) {
   // Notifications woven based on JDK proxies
			case PROXY:
				return new String[] {ProxyAsyncConfiguration.class.getName()};
   // Notifications woven based on AspectJ
			case ASPECTJ:
				return new String[] {ASYNC_EXECUTION_ASPECT_CONFIGURATION_CLASS_NAME};
			default:
				return null;
		}
	}
}
```

In the `selectImports()` method, different classes are selected for loading based on the type of advice, where the default value of `adviceMode` is `PROXY`.

Taking the notification based on JDK proxies as an example, the `ProxyAsyncConfiguration` class will be loaded, as follows:

```JAVA
@Configuration
@Role(BeanDefinition.ROLE_INFRASTRUCTURE)
public class ProxyAsyncConfiguration extends AbstractAsyncConfiguration {
	@Bean(name = TaskManagementConfigUtils.ASYNC_ANNOTATION_PROCESSOR_BEAN_NAME)
	@Role(BeanDefinition.ROLE_INFRASTRUCTURE)
	public AsyncAnnotationBeanPostProcessor asyncAdvisor() {
		 // ...
  // Load the post-processor
		AsyncAnnotationBeanPostProcessor bpp = new AsyncAnnotationBeanPostProcessor();

  // ...
		return bpp;
	}
}
```

### Post-Processor

In the `ProxyAsyncConfiguration` class, a post-processor `AsyncAnnotationBeanPostProcessor` is loaded through the `@Bean` annotation. This post-processor is key to making the `@Async` annotation effective.

If a class or method uses the `@Async` annotation, the `AsyncAnnotationBeanPostProcessor` processor will create a dynamic proxy for that class.

When the method of that class is executed, it will be intercepted by the interceptor of the proxy object, and the method marked with the `@Async` annotation will be executed asynchronously.

The code for `AsyncAnnotationBeanPostProcessor` is as follows:

```JAVA
public class AsyncAnnotationBeanPostProcessor extends AbstractBeanFactoryAwareAdvisingPostProcessor {
	@Override
	public void setBeanFactory(BeanFactory beanFactory) {
		super.setBeanFactory(beanFactory);
  // Create AsyncAnnotationAdvisor, which is an Advisor
  // Used to