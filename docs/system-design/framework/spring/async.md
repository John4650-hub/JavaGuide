---
title: Analysis of Async Annotation Principles
category: Framework
tag:
  - Spring
---

The `@Async` annotation is provided by the Spring framework, and classes or methods marked with this annotation will be executed in **asynchronous threads**. This means that when the method is called, the caller will not wait for the method to complete; instead, it can continue executing subsequent code.

Using the `@Async` annotation is very simple and requires two steps:

1. Add the `@EnableAsync` annotation to the startup class to enable asynchronous tasks.
1. Add the `@Async` annotation to the methods or classes that need to be executed asynchronously.

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

    // It's recommended to use a custom thread pool; here it's just demonstrating basic usage
    @Async
    public CompletableFuture<String> doSomethingAsync() {

        // Here will be some time-consuming business operations
        // ...
        // Using CompletableFuture makes it easier to handle the results of asynchronous tasks and avoids blocking the main thread
        return CompletableFuture.completedFuture("Async Task Completed");
    }

}
```

Next, let's take a look at the underlying principles of `@Async`.

## Analysis of @Async Principles

The `@Async` can execute tasks asynchronously and is essentially implemented using **dynamic proxies**. Through Spring's `BeanPostProcessor`, a dynamic proxy is created for classes using the `@Async` annotation. Afterwards, the invocation of `@Async` annotated methods will be intercepted by the dynamic proxy, and the execution of the methods will be encapsulated as asynchronous tasks submitted to the thread pool for processing.

Now, let's analyze this in detail.

### Enabling Asynchronous Processing

Before using `@Async`, you need to add `@EnableAsync` to the startup class to enable asynchronous processing. The `@EnableAsync` annotation is defined as follows:

```java
// Omitting other annotations ...
@Import(AsyncConfigurationSelector.class)
public @interface EnableAsync { /* ... */ }
```

The `@EnableAsync` annotation imports `AsyncConfigurationSelector` through the `@Import` annotation, so Spring will load the classes introduced by the `@Import` annotation.

The `AsyncConfigurationSelector` class implements the `ImportSelector` interface, and therefore overrides the `selectImports()` method to define the logic for loading beans, as follows:

```java
public class AsyncConfigurationSelector extends AdviceModeImportSelector<EnableAsync> {
	@Override
	@Nullable
	public String[] selectImports(AdviceMode adviceMode) {
		switch (adviceMode) {
   // Notifications woven based on JDK proxy
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

In the `selectImports()` method, different classes are chosen to be loaded based on the type of advice, where the default value of `adviceMode` is `PROXY`.

Taking the notification based on JDK proxy as an example, the `ProxyAsyncConfiguration` class will be loaded as follows:

```java
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

In the `ProxyAsyncConfiguration` class, a post-processor `AsyncAnnotationBeanPostProcessor` is loaded via the `@Bean` annotation. This post-processor is key to making the `@Async` annotation work.

If a class or method uses the `@Async` annotation, the `AsyncAnnotationBeanPostProcessor` processor will create a dynamic proxy for that class.

When the method of that class is executed, it will be intercepted by the interceptor of the proxy object, and methods marked with the `@Async` annotation will be executed asynchronously.

Here's the code for `AsyncAnnotationBeanPostProcessor`:

```java
public class AsyncAnnotationBeanPostProcessor extends AbstractBeanFactoryAwareAdvisingPostProcessor {
	@Override
	public void setBeanFactory(BeanFactory beanFactory) {
		super.setBeanFactory(beanFactory);
  // Create AsyncAnnotationAdvisor, which is an Advisor
  // For intercepting methods marked with @Async and executing them asynchronously.
		AsyncAnnotationAdvisor advisor = new AsyncAnnotationAdvisor(this.executor, this.exceptionHandler);
  // If a custom asyncAnnotationType is set, it will be set to the advisor.
  // asyncAnnotationType is used to specify a custom asynchronous annotation, such as @MyAsync.
		if (this.asyncAnnotationType != null) {
			advisor.setAsyncAnnotationType(this.asyncAnnotationType);
		}
		advisor.setBeanFactory(beanFactory);
		this.advisor = advisor;
	}
}
```

The `AsyncAnnotationBeanPostProcessor` class's parent class implements the `BeanFactoryAware` interface, so the `setBeanFactory()` method is overridden as an extension point to load `AsyncAnnotationAdvisor`.

#### Creating an Advisor

An `Advisor` is an abstraction of `Advice` and `Pointcut` in `Spring AOP`. `Advice` is the notification logic executed, while `Pointcut` is the join point at which the advice is applied.

In the post-processor `AsyncAnnotationBeanPostProcessor`, `AsyncAnnotationAdvisor` will be created. In its constructor, the corresponding `Advice` and `Pointcut` will be constructed as follows:

```java
public class AsyncAnnotationAdvisor extends AbstractPointcutAdvisor implements BeanFactoryAware {

    private Advice advice; // Advice for asynchronous execution
    private Pointcut pointcut; // Pointcut for matching methods annotated with @Async

    // Constructor
    public AsyncAnnotationAdvisor(/* parameters omitted */) {
        // 1. Create Advice responsible for asynchronous execution logic
        this.advice = buildAdvice(executor, exceptionHandler);
        // 2. Create Pointcut, selecting target methods to be enhanced
        this.pointcut = buildPointcut(asyncAnnotationTypes);
    }

    // Create Advice
    protected Advice buildAdvice(/* parameters omitted */) {
        // Create interceptor for handling asynchronous execution
        AnnotationAsyncExecutionInterceptor interceptor = new AnnotationAsyncExecutionInterceptor(null);
        // Configure interceptor using executor and exception handler
        interceptor.configure(executor, exceptionHandler);
        return interceptor;
    }

    // Create Pointcut
    protected Pointcut buildPointcut(Set<Class<? extends Annotation>> asyncAnnotationTypes) {
        ComposablePointcut result = null;
        for (Class<? extends Annotation> asyncAnnotationType : asyncAnnotationTypes) {
            // 1. Class-level pointcut: matches if there is an annotation on the class
            Pointcut cpc = new AnnotationMatchingPointcut(asyncAnnotationType, true);
            // 2. Method-level pointcut: matches if there is an annotation on the method
            Pointcut mpc = new AnnotationMatchingPointcut(null, asyncAnnotationType, true);

            if (result == null) {
                result = new ComposablePointcut(cpc);
            } else {
                // Use union to merge previous pointcuts
                result.union(cpc);
            }
            // Add method-level pointcut to the composite pointcut
            result = result.union(mpc);
        }
        // Return composite pointcut; if no annotation type is provided, return Pointcut.TRUE
        return (result != null ? result : Pointcut.TRUE);
    }
}
```

The core of `AsyncAnnotationAdvisor` lies in building the `Advice` and `Pointcut`:

- Building `Advice`: A `AnnotationAsyncExecutionInterceptor` interceptor is created, and the notification logic will be executed in the interceptor's `invoke()` method.
- Building `Pointcut`: Composed of `ClassFilter` and `MethodMatcher` to determine which methods need to execute the notification (`Advice`) logic.

#### Post-Processing Logic

The `postProcessAfterInitialization()` method implemented in the `AsyncAnnotationBeanPostProcessor` post-processor is in its parent class `AbstractAdvisingBeanPostProcessor`. After the bean initialization, it will enter the `postProcessAfterInitialization()` method for post-processing.

In the post-processing method, it will determine whether the bean meets the conditions for advisory notifications. If they match, a proxy object will be created. As follows:

```java
// AbstractAdvisingBeanPostProcessor
public Object postProcessAfterInitialization(Object bean, String beanName) {
	if (this.advisor == null || bean instanceof AopInfrastructureBean) {
		return bean;
	}
	if (bean instanceof Advised) {
		Advised advised = (Advised) bean;
		if (!advised.isFrozen() && isEligible(AopUtils.getTargetClass(bean))) {
			if (this.beforeExistingAdvisors) {
				advised.addAdvisor(0, this.advisor);
			}
			else {
				advised.addAdvisor(this.advisor);
			}
			return bean;
		}
	}
 // Check if the given bean meets the conditions in the post-processor for advisory notifications. If so, create a proxy object.
	if (isEligible(bean, beanName)) {
		ProxyFactory proxyFactory = prepareProxyFactory(bean, beanName);
		if (!proxyFactory.isProxyTargetClass()) {
			evaluateProxyInterfaces(bean.getClass(), proxyFactory);
		}
  // Add Advisor.
		proxyFactory.addAdvisor(this.advisor);
		customizeProxyFactory(proxyFactory);
  // Return proxy object.
		return proxyFactory.getProxy(getProxyClassLoader());
	}
	return bean;
}
```

### Interception of Methods with @Async Annotation

The execution of methods with the `@Async` annotation will be intercepted in the `AnnotationAsyncExecutionInterceptor`, where the interceptor's logic will be executed in the `invoke()` method. At this point, the methods annotated with `@Async` will be encapsulated as asynchronous tasks and submitted to the executor for execution.

The `invoke()` method is defined in the parent class `AsyncExecutionInterceptor`, as follows:

```java
public class AsyncExecutionInterceptor extends AsyncExecutionAspectSupport implements MethodInterceptor, Ordered {
	@Override
	@Nullable
	public Object invoke(final MethodInvocation invocation) throws Throwable {
		Class<?> targetClass = (invocation.getThis() != null ? AopUtils.getTargetClass(invocation.getThis()) : null);
		Method specificMethod = ClassUtils.getMostSpecificMethod(invocation.getMethod(), targetClass);
		final Method userDeclaredMethod = BridgeMethodResolver.findBridgedMethod(specificMethod);

  // 1. Determine the asynchronous task executor
		AsyncTaskExecutor executor = determineAsyncExecutor(userDeclaredMethod);

  // 2. Encapsulate the method to be executed as a Callable asynchronous task
		Callable<Object> task = () -> {
			try {
    // 2.1 Execute the method
				Object result = invocation.proceed();
    // 2.2 If the method's return value is of Future type, block and wait for the result
				if (result instanceof Future) {
					return ((Future<?>) result).get();
				}
			}
			catch (ExecutionException ex) {
				handleError(ex.getCause(), userDeclaredMethod, invocation.getArguments());
			}
			catch (Throwable ex) {
				handleError(ex, userDeclaredMethod, invocation.getArguments());
			}
			return null;
		};
		// 3. Submit the task
		return doSubmit(task, executor, invocation.getMethod().getReturnType());
	}
}
```

In the `invoke()` method, there are mainly 3 steps:

1. Determine the executor for executing the asynchronous task.
1. Package the method marked with `@Async` as a `Callable` asynchronous task.
1. Submit the task to the executor for execution.

#### 1. Getting the Asynchronous Task Executor

In the `determineAsyncExecutor()` method, the executor for the asynchronous task (i.e., the thread pool) will be obtained. The code is as follows:

```java
// Determine the executor for the asynchronous task
protected AsyncTaskExecutor determineAsyncExecutor(Method method) {
 // 1. First attempt to get it from the cache.
	AsyncTaskExecutor executor = this.executors.get(method);
	if (executor == null) {
		Executor targetExecutor;
  // 2. Get the qualifier of the executor.
		String qualifier = getExecutorQualifier(method);
		if (StringUtils.hasLength(qualifier)) {
   // 3. Obtain the corresponding executor based on the qualifier.
			targetExecutor = findQualifiedExecutor(this.beanFactory, qualifier);
		}
		else {
   // 4. If there is no qualifier, use the default executor provided by Spring: SimpleAsyncTaskExecutor.
			targetExecutor = this.defaultExecutor.get();
		}
		if (targetExecutor == null) {
			return null;
		}
  // 5. Wrap the executor as a TaskExecutorAdapter adapter.
  // TaskExecutorAdapter is an abstraction for JDK thread pools provided by Spring, still inheriting from JDK's Executor thread pool. You don't need to worry too much about this; just know it's a thread pool.
		executor = (targetExecutor instanceof AsyncListenableTaskExecutor ?
				(AsyncListenableTaskExecutor) targetExecutor : new TaskExecutorAdapter(targetExecutor));
		this.executors.put(method, executor);
	}
	return executor;
}
```

In the `determineAsyncExecutor()` method, the executor for the asynchronous task (thread pool) is determined mainly through the `value` of the `@Async` annotation to get the qualifier of the executor and then find the corresponding executor in the `BeanFactory.`

If no thread pool is specified in the `@Async` annotation, it will get the default thread pool through `this.defaultExecutor.get()`, which is assigned in the following method:

```java
// AsyncExecutionInterceptor
protected Executor getDefaultExecutor(@Nullable BeanFactory beanFactory) {
 // 1. Try to get the thread pool from the beanFactory.
	Executor defaultExecutor = super.getDefaultExecutor(beanFactory);
 // 2. If there's none in the beanFactory, create a SimpleAsyncTaskExecutor thread pool.
	return (defaultExecutor != null ? defaultExecutor : new SimpleAsyncTaskExecutor());
}
```

In which, `super.getDefaultExecutor()` will try to obtain the `Executor` type thread pool from the `beanFactory`. The code is as follows:

```java
protected Executor getDefaultExecutor(@Nullable BeanFactory beanFactory) {
	if (beanFactory != null) {
		try {
   // 1. Get the TaskExecutor type thread pool from the beanFactory.
			return beanFactory.getBean(TaskExecutor.class);
		}
		catch (NoUniqueBeanDefinitionException ex) {
			try {
				// 2. If there are multiple, attempt to get the executor thread pool by execution name from the beanFactory.
				return beanFactory.getBean(DEFAULT_TASK_EXECUTOR_BEAN_NAME, Executor.class);
			}
			catch (NoSuchBeanDefinitionException ex2) {
				if (logger.isInfoEnabled()) {
					// ...
				}
			}
		}
		catch (NoSuchBeanDefinitionException ex) {
			try {
    // 3. If none exist, attempt to get the executor thread pool by execution name from the beanFactory.
				return beanFactory.getBean(DEFAULT_TASK_EXECUTOR_BEAN_NAME, Executor.class);
			}
			catch (NoSuchBeanDefinitionException ex2) {
				// ...
			}
		}
	}
	return null;
}
```

In `getDefaultExecutor()`, if it fails to get the thread pool from the `beanFactory`, it will create a `SimpleAsyncTaskExecutor` thread pool.

This thread pool will create a new thread for each asynchronous task execution, without reusing threads, leading to substantial overhead for executing asynchronous tasks. If the concurrency spikes at any moment in methods marked with `@Async`, the application will create a vast number of threads, thereby affecting service quality or even causing service outages.

If 10,000 tasks are submitted to the `SimpleAsyncTaskExecutor` thread pool at the same time, the thread pool will create 10,000 threads. Its `execute()` method is as follows:

```java
// SimpleAsyncTaskExecutor: The execute() method internally calls doExecute()
protected void doExecute(Runnable task) {
    // Create a new thread
    Thread thread = (this.threadFactory != null ? this.threadFactory.newThread(task) : createThread(task));
    thread.start();
}
```

**Recommendation: When using `@Async`, be sure to specify your own thread pool to avoid the risks of Spring's default thread pool.**

The `value` in the `@Async` annotation specifies the qualifier of the thread pool, and based on the qualifier, a **custom thread pool** can be obtained. The code to obtain the qualifier is as follows:

```java
// AnnotationAsyncExecutionInterceptor
protected String getExecutorQualifier(Method method) {
	// 1. Get the Async annotation from the method.
	Async async = AnnotatedElementUtils.findMergedAnnotation(method, Async.class);
 // 2. If no @Async annotation is found on the method, try to get the @Async annotation from the class where the method is defined.
	if (async == null) {
		async = AnnotatedElementUtils.findMergedAnnotation(method.getDeclaringClass(), Async.class);
	}
 // 3. If the @Async annotation is found, get its value and return it as the thread pool's qualifier.
 //    If the value attribute is an empty string, it will use the default thread pool.
 //    If the @Async annotation is not found, null will be returned, hence using the default thread pool.
	return (async != null ? async.value() : null);
}
```

#### 2. Encapsulating the Method as an Asynchronous Task

After obtaining the executor in the `invoke()` method, the method is encapsulated as an asynchronous task, as follows:

```java
// Encapsulate the method to be executed as a Callable asynchronous task
Callable<Object> task = () -> {
    try {
        // 2.1 Execute the intercepted method (the proceed() method is the core method in AOP for executing the target method)
        Object result = invocation.proceed();

        // 2.2 If the intercepted method's return value is of Future type, it needs to block and wait for the result,
        //     and return the result of Future as the result of the asynchronous task. This is to handle nested calls of asynchronous methods.
        //     For instance, if an asynchronous method calls another asynchronous method internally, it should wait for the inner asynchronous method to complete
        //     before returning the final result.
        if (result instanceof Future) {
            return ((Future<?>) result).get(); // Blocking wait for the Future result
        }
    }
    catch (ExecutionException ex) {
        // 2.3 Handle ExecutionException. ExecutionException is an exception thrown by Future.get().
        handleError(ex.getCause(), userDeclaredMethod, invocation.getArguments()); // Handle the original exception
    }
    catch (Throwable ex) {
        // 2.4 Handle other types of exceptions. Pass the exception, intercepted method, and method parameters to the handleError() method for handling.
        handleError(ex, userDeclaredMethod, invocation.getArguments());
    }
    // 2.5 If the method return value is not of Future type or an exception occurs, return null.
    return null;
};
```

Compared to `Runnable`, `Callable` can return results and throw exceptions.

The execution of `invocation.proceed()` (the execution of the original method) is encapsulated as a `Callable` asynchronous task. Here, it only returns when `result` (the method return value) is of `Future` type; otherwise, it returns `null`.

Therefore, if the return value of the method annotated with `@Async` is defined as something other than `Future`, it will not be able to obtain the execution result of the method.

#### 3. Submitting the Asynchronous Task

After the method is encapsulated as a `Callable` task in the `AsyncExecutionInterceptor # invoke()`, the task is submitted to the executor for execution. The related code for submission is as follows:

```java
protected Object doSubmit(Callable<Object> task, AsyncTaskExecutor executor, Class<?> returnType) {
    // Depending on the return type of the method, choose different asynchronous execution methods and return results.
    // 1. If the method return value is of CompletableFuture type
    if (CompletableFuture.class.isAssignableFrom(returnType)) {
        // Use CompletableFuture.supplyAsync() method to execute the task asynchronously.
        return CompletableFuture.supplyAsync(() -> {
            try {
                return task.call();
            }
            catch (Throwable ex) {
                throw new CompletionException(ex); // Wrap the exception in a CompletionException, so it throws during future.get()
            }
        }, executor);
    }
    // 2. If the method return value is of ListenableFuture type
    else if (ListenableFuture.class.isAssignableFrom(returnType)) {
        // Cast the AsyncTaskExecutor to AsyncListenableTaskExecutor,
        // and call the submitListenable() method to submit the task.
        // AsyncListenableTaskExecutor is a specialized asynchronous executor for ListenableFuture,
        // allowing callbacks to be added to listen for task completion.
        return ((AsyncListenableTaskExecutor) executor).submitListenable(task);
    }
    // 3. If the method return value is of Future type
    else if (Future.class.isAssignableFrom(returnType)) {
        // Call the submit() method of AsyncTaskExecutor directly to submit the task and return a Future object.
        return executor.submit(task);
    }
    // 4. If the method return value is void or another type
    else {
        // Submit the task directly through the AsyncTaskExecutor.
        // Since the return value of the method is void, there's no need to return any result, just return null.
        executor.submit(task);
        return null;
    }
}
```

In the `doSubmit()` method, it selects different task submission methods based on the return value of the method marked with the `@Async` annotation, and finally the task is executed by the executor (thread pool).

### Summary

![Async Principle Summary](./images/async/async.png)

Understanding the core of the `@Async` principle focuses on understanding the `@EnableAsync` annotation, which enables asynchronous task functionality.

The main process is shown in the figure above: a post-processor is used to create a proxy object, after which the execution of methods in the proxy object marked with `@Async` will go through the interceptor in the `Advice`, encapsulating the methods as asynchronous tasks, and submitting them to the thread pool for processing.

## Suggestions for Using @Async

### Custom Thread Pool

If a thread pool is not explicitly configured, the underlying implementation of `@Async` will first attempt to get the thread pool from the `BeanFactory`. If it fails, it will create a `SimpleAsyncTaskExecutor` implementation. The `SimpleAsyncTaskExecutor` is not a true thread pool because it creates a new thread for each request without reusing existing threads, which can lead to potential issues, such as excessive resource consumption.

For specific details on obtaining a thread pool, you can refer to this article: [An Analysis of the Underlying Asynchronous Thread Pool Principle of the Async Annotation in Spring｜DeWu Technology](https://mp.weixin.qq.com/s/FySv5L0bCdrlb5MoSfQtAA).

Always explicitly configure a thread pool, and `ThreadPoolTaskExecutor` is recommended. Additionally, you can specify different thread pools for different asynchronous methods based on the nature and requirements of the tasks.

```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean(name = "executor1")
    public Executor executor1() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(3);
        executor.setMaxPoolSize(5);
        executor.setQueueCapacity(50);
        executor.setThreadNamePrefix("AsyncExecutor1-");
        executor.initialize();
        return executor;
    }

    @Bean(name = "executor2")
    public Executor executor2() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(2);
        executor.setMaxPoolSize(4);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("AsyncExecutor2-");
        executor.initialize();
        return executor;
    }
}
```

Specify the bean name of the thread pool in the `@Async` annotation:

```java
@Service
public class AsyncService {

    @Async("executor1")
    public void performTask1() {
        // Logic for Task 1
        System.out.println("Executing Task1 with Executor1");
    }

    @Async("executor2")
    public void performTask2() {
        // Logic for Task 2
        System.out.println("Executing Task2 with Executor2");
    }
}
```

### Avoiding @Async Annotation Failure

The `@Async` annotation can fail to function in the following situations, so please be cautious:

**1. Calling Asynchronous Methods from Within the Same Class**

If you call a method annotated with `@Async` directly through `this` reference in the same class, that method will not execute asynchronously.

```java
@Service
public class MyService {

    public void myMethod() {
        // Calling via this reference bypasses the Spring proxy mechanism, causing async execution to fail
        asyncMethod();
    }

    @Async
    public void asyncMethod() {
        // Logic for asynchronous execution
    }
}
```

This is because Spring's asynchronous mechanism is implemented through **proxies**, and method calls within the same class bypass the Spring proxy mechanism, meaning they go directly through the `this` reference. Since it doesn't go through the proxy, all proxy-related processes (i.e., task submission to the thread pool for asynchronous execution) won't occur.

To avoid this issue, it's recommended to move the asynchronous method to another Spring Bean.

```java
@Service
public class AsyncService {
    @Async
    public void asyncMethod() {
        // Logic for asynchronous execution
    }
}

@Service
public class MyService {
    @Autowired
    private AsyncService asyncService;

    public void myMethod() {
        asyncService.asyncMethod();
    }
}
```

**2. Using the static Keyword to Modify Asynchronous Methods**

If a method annotated with `@Async` is modified with the `static` keyword, that method will not execute asynchronously.

This is because Spring's asynchronous mechanism relies on proxying, and static methods do not belong to instances but to classes and do not participate in inheritance. Therefore, Spring's proxy mechanism (either based on JDK or CGLIB) cannot intercept static methods for enhancements like asynchronous execution.

Due to space limitations, further elaboration is not provided here. Friends who do not understand the proxy mechanism may refer to my article [Java Proxy Pattern Explained in Detail](https://javaguide.cn/java/basis/proxy.html).

If you need to asynchronously execute logic of a static method, you might consider designing a non-static wrapper method, which uses the `@Async` annotation and calls the static method inside it.

```java
@Service
public class AsyncService {

    @Async
    public void asyncWrapper() {
        // Call the static method
        SClass.staticMethod();
    }
}

public class SClass {
    public static void staticMethod() {
        // Execute some operation
    }
}
```

**3. Forgetting to Enable Asynchronous Support**

By default, Spring Boot does not enable asynchronous support. Ensure to add the `@EnableAsync` annotation on the main configuration class `Application` to enable asynchronous functionality.

```java
@SpringBootApplication
@EnableAsync
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

**4. @Async Annotated Methods Must Be in Spring Beans**

Methods annotated with `@Async` must be located within Spring-managed beans. Only then can Spring apply proxies during bean creation, allowing the proxy to intercept method calls and implement asynchronous execution logic. If the method is not within a Spring-managed bean, Spring cannot create the necessary proxy, and the `@Async` annotation will have no effect.

### Return Value Type

It is recommended to define the return value type of methods annotated with `@Async` as `void` or `Future`.

- If you don't need to get the results returned by the asynchronous method, define the return type as `void`.
- If you need to get the result returned by the asynchronous method, define the return type as `Future` (e.g., `CompletableFuture`, `ListenableFuture`).

If you define the return value of a method with the `@Async` annotation as any type other than these (like `Object`, `String`, etc.), you won't be able to retrieve the method's return value.

This design conforms to the basic principle of asynchronous programming, where the caller should not expect an immediate result but should be able to obtain it at a later point in time. If the return type is `Future`, the caller can use this returned `Future` object to query the task's status, cancel the task, or retrieve the result when the task completes.

### Handling Exceptions in Asynchronous Methods

Exceptions thrown in asynchronous methods are not captured by the caller by default. To manage these exceptions, it's advisable to use the exception-handling features of `CompletableFuture` or to configure a global `AsyncUncaughtExceptionHandler` to handle uncaught exceptions.

```java
@Configuration
@EnableAsync
public class AsyncConfig implements AsyncConfigurer{

    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return new CustomAsyncExceptionHandler();
    }

}

// Custom exception handler
class CustomAsyncExceptionHandler implements AsyncUncaughtExceptionHandler {

    @Override
    public void handleUncaughtException(Throwable ex, Method method, Object... params) {
        // Logging or other handling logic
    }
}
```

### Not Considering Transaction Management

If methods annotated with `@Async` require transaction support, make sure to use the `@Transactional` annotation independently on that asynchronous method.

```java
@Service
public class AsyncTransactionalService {

    @Async
    // Propagation.REQUIRES_NEW indicates that Spring starts a new transaction, unrelated to the current transaction, when executing the asynchronous method
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void asyncTransactionalMethod() {
        // The operations here will be executed in a new transaction
        // Execute some database operations
    }
}
```

### Not Specifying the Execution Order of Asynchronous Methods

Methods annotated with `@Async` execute in a non-blocking manner, and they may complete in any order. If you need to process results in a specific order, you can set the return value of the method to `Future` or `CompletableFuture`, achieving execution of one method after another's completion.

```java
@Async
public CompletableFuture<String> fetchDataAsync() {
    return CompletableFuture.completedFuture("Data");
}

@Async
public CompletableFuture<String> processDataAsync(String data) {
    return CompletableFuture.supplyAsync(() -> "Processed " + data);
}
```

The `processDataAsync` method executes after `fetchDataAsync`:

```java
CompletableFuture<String> dataFuture = asyncService.fetchDataAsync();
dataFuture.thenCompose(data -> asyncService.processDataAsync(data))
          .thenAccept(result -> System.out.println(result));
```

## 
