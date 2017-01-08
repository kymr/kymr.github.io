---
layout: post
title: 'RetryUtils with Java 8. How to retry block of codes when exception occurs with functional way?'
author: yongmaroo.kim
date: 2017-01-09 02:00:00
tags: [functional,java]
---

### Basic

There are already many ways exist to retry logics when exception occurs. For example, you can use framework like @retry with Spring.
I just want to make a simple util to retry logic with java 8 like below.

#### RetryUtils

```java
@Slf4j
public class RetryUtils {
	private static final int RETRY = 3;
	private static final long DELAY = 1000l;

	@FunctionalInterface
	public interface RunnableWithException {
		void run() throws Exception;
	}

	public static <V> V retry(Callable<V> callable, Throwable throwable, String message) {
		return retryLogics(callable, throwable, message);
	}

	public static void retry(RunnableWithException runnable, Throwable throwable, String message) {
		retryLogics(() -> {
			runnable.run();
			return null;
		}, throwable, message);
	}

	private static <T> T retryLogics(Callable<T> callable, Throwable throwable, String message) {
		int counter = 0;

		while (counter < RETRY) {
			try {
				return callable.call();
			} catch (Exception e) {
				counter++;
				log.error("retry {} / {}, {}", counter, RETRY, message, e);

				try {
					Thread.sleep(DELAY);
				} catch (InterruptedException e1) {
					e1.printStackTrace();
				}
			}
		}

		throw new RuntimeException(throwable);
	}
}
```

The logic can have a return value or not. When the logic has a return value, callable interface is good for it.
But when the logic doesn't have a return value, then runnable can be used. Because retry will be executed when exception occurs, so i declared new interface that throws exception.
I want to print the error message at every retry, so i added it to paramter.
Also i want to throw the specific exception if all retries also are failed.
If you want to control retry count and delay time, then customize it :D

Below is the use case.

#### Test Case

```java
public class RetryUtilsTest {

	@Test
	public void testRunnable() throws Exception {
		RetryUtils.retry(() -> System.out.println("hi"), new IOException(), "error occurs");
	}

	@Test(expectedExceptions = Exception.class)
	public void testRunnableWithException() throws Exception {
		RetryUtils.retry(() -> {
			throw new Exception();
		}, new IOException(), "error occurs");
	}

	@Test
	public void testCallable() throws Exception {
		String result = RetryUtils.retry(() -> "hi", new IOException(), "error occurs");
		System.out.println(result);

		List<String> results = RetryUtils.retry(() -> Arrays.asList("hi1", "hi2", "hi3"), new IOException(), "error occurs");
		results.forEach(str -> System.out.println("List : " + str));
	}

	@Test(expectedExceptions = Exception.class)
	public void testCallableException() throws Exception {
		String result = RetryUtils.retry(() -> {
			throw new Exception();
		}, new IOException(), "error occurs");
	}
}
```