---
title: "Exception Handling in the Concurrency Runtime"
ms.custom: na
ms.date: "10/14/2016"
ms.prod: "visual-studio-dev14"
ms.reviewer: na
ms.suite: na
ms.technology: 
  - "devlang-cpp"
ms.tgt_pltfrm: na
ms.topic: "article"
dev_langs: 
  - "C++"
helpviewer_keywords: 
  - "lightweight tasks, exception handling [Concurrency Runtime]"
  - "exception handling [Concurrency Runtime]"
  - "structured task groups, exception handling [Concurrency Runtime]"
  - "agents, exception handling [Concurrency Runtime]"
  - "task groups, exception handling [Concurrency Runtime]"
ms.assetid: 4d1494fb-3089-4f4b-8cfb-712aa67d7a7a
caps.latest.revision: 26
ms.author: "mithom"
manager: "ghogen"
translation.priority.ht: 
  - "de-de"
  - "es-es"
  - "fr-fr"
  - "it-it"
  - "ja-jp"
  - "ko-kr"
  - "ru-ru"
  - "zh-cn"
  - "zh-tw"
translation.priority.mt: 
  - "cs-cz"
  - "pl-pl"
  - "pt-br"
  - "tr-tr"
---
# Exception Handling in the Concurrency Runtime
The Concurrency Runtime uses C++ exception handling to communicate many kinds of errors. These errors include invalid use of the runtime, runtime errors such as failure to acquire a resource, and errors that occur in work functions that you provide to tasks and task groups. When a task or task group throws an exception, the runtime holds that exception and marshals it to the context that waits for the task or task group to finish. For components such as lightweight tasks and agents, the runtime does not manage exceptions for you. In these cases, you must implement your own exception-handling mechanism. This topic describes how the runtime handles exceptions that are thrown by tasks, task groups, lightweight tasks, and asynchronous agents, and how to respond to exceptions in your applications.  
  
## Key Points  
  
-   When a task or task group throws an exception, the runtime holds that exception and marshals it to the context that waits for the task or task group to finish.  
  
-   When possible, surround every call to [concurrency::task::get](../Topic/task::get%20Method.md) and [concurrency::task::wait](../Topic/task::wait%20Method.md) with a `try`/`catch` block to handle errors that you can recover from. The runtime terminates the app if a task throws an exception and that exception is not caught by the task, one of its continuations, or the main app.  
  
-   A task-based continuation always runs; it does not matter whether the antecedent task completed successfully, threw an exception, or was canceled. A value-based continuation does not run if the antecedent task throws or cancels.  
  
-   Because task-based continuations always run, consider whether to add a task-based continuation at the end of your continuation chain. This can help guarantee that your code observes all exceptions.  
  
-   The runtime throws [concurrency::task_canceled](../parallel/task_canceled-class.md) when you call [concurrency::task::get](../Topic/task::get%20Method.md) and that task is canceled.  
  
-   The runtime does not manage exceptions for lightweight tasks and agents.  
  
##  <a name="top"></a> In this Document  
  
-   [Tasks and Continuations](#tasks)  
  
-   [Task Groups and Parallel Algorithms](#task_groups)  
  
-   [Exceptions Thrown by the Runtime](#runtime)  
  
-   [Multiple Exceptions](#multiple)  
  
-   [Cancellation](#cancellation)  
  
-   [Lightweight Tasks](#lwts)  
  
-   [Asynchronous Agents](#agents)  
  
##  <a name="tasks"></a> Tasks and Continuations  
 This section describes how the runtime handles exceptions that are thrown by [concurrency::task](../parallel/task-class--concurrency-runtime-.md) objects and their continuations. For more information about the task and continuation model, see [Task Parallelism](../parallel/task-parallelism--concurrency-runtime-.md).  
  
 When you throw an exception in the body of a work function that you pass to a `task` object, the runtime stores that exception and marshals it to the context that calls [concurrency::task::get](../Topic/task::get%20Method.md) or [concurrency::task::wait](../Topic/task::wait%20Method.md). The document [Task Parallelism](../parallel/task-parallelism--concurrency-runtime-.md) describes task-based versus value-based continuations, but to summarize, a value-based continuation takes a parameter of type `T` and a task-based continuation takes a parameter of type `task<T>`. If a task that throws has one or more value-based continuations, those continuations are not scheduled to run. The following example illustrates this behavior:  
  
 [!code[concrt-eh-task#1](../parallel/codesnippet/CPP/exception-handling-in-the-concurrency-runtime_1.cpp)]  
  
 A task-based continuation enables you to handle any exception that is thrown by the antecedent task. A task-based continuation always runs; it does not matter whether the task completed successfully, threw an exception, or was canceled. When a task throws an exception, its task-based continuations are scheduled to run. The following example shows a task that always throws. The task has two continuations; one is value-based and the other is task-based. The task-based exception always runs, and therefore can catch the exception that is thrown by the antecedent task. When the example waits for both continuations to finish, the exception is thrown again because the task exception is always thrown when `task::get` or `task::wait` is called.  
  
 [!code[concrt-eh-continuations#1](../parallel/codesnippet/CPP/exception-handling-in-the-concurrency-runtime_2.cpp)]  
  
 We recommend that you use task-based continuations to catch exceptions that you are able to handle. Because task-based continuations always run, consider whether to add a task-based continuation at the end of your continuation chain. This can help guarantee that your code observes all exceptions. The following example shows a basic value-based continuation chain. The third task in the chain throws, and therefore any value-based continuations that follow it are not run. However, the final continuation is task-based, and therefore always runs. This final continuation handles the exception that is thrown by the third task.  
  
 We recommend that you catch the most specific exceptions that you can. You can omit this final task-based continuation if you don’t have specific exceptions to catch. Any exception will remain unhandled and can terminate the app.  
  
 [!code[concrt-eh-task-chain#1](../parallel/codesnippet/CPP/exception-handling-in-the-concurrency-runtime_3.cpp)]  
  
> [!TIP]
>  You can use the [concurrency::task_completion_event::set_exception](../parallel/task_completion_event-class.md) method to associate an exception with a task completion event. The document [Task Parallelism](../parallel/task-parallelism--concurrency-runtime-.md) describes the [concurrency::task_completion_event](../parallel/task_completion_event-class.md) class in greater detail.  
  
 [concurrency::task_canceled](../parallel/task_canceled-class.md) is an important runtime exception type that relates to `task`. The runtime throws `task_canceled` when you call `task::get` and that task is canceled. (Conversely, `task::wait` returns [task_status::canceled](../Topic/task_group_status%20Enumeration.md) and does not throw.) You can catch and handle this exception from a task-based continuation or when you call `task::get`. For more information about task cancellation, see [Cancellation](../parallel/cancellation-in-the-ppl.md).  
  
> [!CAUTION]
>  Never throw `task_canceled` from your code. Call [concurrency::cancel_current_task](../Topic/cancel_current_task%20Function.md) instead.  
  
 The runtime terminates the app if a task throws an exception and that exception is not caught by the task, one of its continuations, or the main app. If your application crashes, you can configure Visual Studio to break when C++ exceptions are thrown. After you diagnose the location of the unhandled exception, use a task-based continuation to handle it.  
  
 The section [Exceptions Thrown by the Runtime](#runtime) in this document describes how to work with runtime exceptions in greater detail.  
  
 [[Top](#top)]  
  
##  <a name="task_groups"></a> Task Groups and Parallel Algorithms  
 This section describes how the runtime handles exceptions that are thrown by task groups. This section also applies to parallel algorithms such as [concurrency::parallel_for](../Topic/parallel_for%20Function.md), because these algorithms build on task groups.  
  
> [!CAUTION]
>  Make sure that you understand the effects that exceptions have on dependent tasks. For recommended practices about how to use exception handling with tasks or parallel algorithms, see the [Understand how Cancellation and Exception Handling Affect Object Destruction](../parallel/best-practices-in-the-parallel-patterns-library.md#object-destruction) section in the Best Practices in the Parallel Patterns Library topic.  
  
 For more information about task groups, see [Task Parallelism](../parallel/task-parallelism--concurrency-runtime-.md). For more information about parallel algorithms, see [Parallel Algorithms](../parallel/parallel-algorithms.md).  
  
 When you throw an exception in the body of a work function that you pass to a [concurrency::task_group](../Topic/task_group%20Class.md) or [concurrency::structured_task_group](../parallel/structured_task_group-class.md) object, the runtime stores that exception and marshals it to the context that calls [concurrency::task_group::wait](../Topic/task_group::wait%20Method.md), [concurrency::structured_task_group::wait](../Topic/structured_task_group::wait%20Method.md), [concurrency::task_group::run_and_wait](../Topic/task_group::run_and_wait%20Method.md), or [concurrency::structured_task_group::run_and_wait](../Topic/structured_task_group::run_and_wait%20Method.md). The runtime also stops all active tasks that are in the task group (including those in child task groups) and discards any tasks that have not yet started.  
  
 The following example shows the basic structure of a work function that throws an exception. The example uses a `task_group` object to print the values of two `point` objects in parallel. The `print_point` work function prints the values of a `point` object to the console. The work function throws an exception if the input value is `NULL`. The runtime stores this exception and marshals it to the context that calls `task_group::wait`.  
  
 [!code[concrt-eh-task-group#1](../parallel/codesnippet/CPP/exception-handling-in-the-concurrency-runtime_4.cpp)]  
  
 This example produces the following output.  
  
 **X = 15, Y = 30Caught exception: point is NULL.** For a complete example that uses exception handling in a task group, see [How to: Use Exception Handling to Break from a Parallel Loop](../parallel/how-to--use-exception-handling-to-break-from-a-parallel-loop.md).  
  
 [[Top](#top)]  
  
##  <a name="runtime"></a> Exceptions Thrown by the Runtime  
 An exception can result from a call to the runtime. Most exception types, except for [concurrency::task_canceled](../parallel/task_canceled-class.md) and [concurrency::operation_timed_out](../parallel/operation_timed_out-class.md), indicate a programming error. These errors are typically unrecoverable, and therefore should not be caught or handled by application code. We suggest that you only catch or handle unrecoverable errors in your application code when you need to diagnose programming errors. However, understanding the exception types that are defined by the runtime can help you diagnose programming errors.  
  
 The exception handling mechanism is the same for exceptions that are thrown by the runtime as exceptions that are thrown by work functions. For example, the [concurrency::receive](../Topic/receive%20Function.md) function throws `operation_timed_out` when it does not receive a message in the specified time period. If `receive` throws an exception in a work function that you pass to a task group, the runtime stores that exception and marshals it to the context that calls `task_group::wait`, `structured_task_group::wait`, `task_group::run_and_wait`, or `structured_task_group::run_and_wait`.  
  
 The following example uses the [concurrency::parallel_invoke](../Topic/parallel_invoke%20Function.md) algorithm to run two tasks in parallel. The first task waits five seconds and then sends a message to a message buffer. The second task uses the `receive` function to wait three seconds to receive a message from the same message buffer. The `receive` function throws `operation_timed_out` if it does not receive the message in the time period.  
  
 [!code[concrt-eh-time-out#1](../parallel/codesnippet/CPP/exception-handling-in-the-concurrency-runtime_5.cpp)]  
  
 This example produces the following output.  
  
 **The operation timed out.** To prevent abnormal termination of your application, make sure that your code handles exceptions when it calls into the runtime. Also handle exceptions when you call into external code that uses the Concurrency Runtime, for example, a third-party library.  
  
 [[Top](#top)]  
  
##  <a name="multiple"></a> Multiple Exceptions  
 If a task or parallel algorithm receives multiple exceptions, the runtime marshals only one of those exceptions to the calling context. The runtime does not guarantee which exception it marshals.  
  
 The following example uses the `parallel_for` algorithm to print numbers to the console. It throws an exception if the input value is less than some minimum value or greater than some maximum value. In this example, multiple work functions can throw an exception.  
  
 [!code[concrt-eh-multiple#1](../parallel/codesnippet/CPP/exception-handling-in-the-concurrency-runtime_6.cpp)]  
  
 The following shows sample output for this example.  
  
 **8293104567Caught exception: -5: the value is less than the minimum.** [[Top](#top)]  
  
##  <a name="cancellation"></a> Cancellation  
 Not all exceptions indicate an error. For example, a search algorithm might use exception handling to stop its associated task when it finds the result. For more information about how to use cancellation mechanisms in your code, see [Cancellation](../parallel/cancellation-in-the-ppl.md).  
  
 [[Top](#top)]  
  
##  <a name="lwts"></a> Lightweight Tasks  
 A lightweight task is a task that you schedule directly from a [concurrency::Scheduler](../parallel/scheduler-class.md) object. Lightweight tasks carry less overhead than ordinary tasks. However, the runtime does not catch exceptions that are thrown by lightweight tasks. Instead, the exception is caught by the unhandled exception handler, which by default terminates the process. Therefore, use an appropriate error-handling mechanism in your application. For more information about lightweight tasks, see [Task Scheduler](../parallel/task-scheduler--concurrency-runtime-.md).  
  
 [[Top](#top)]  
  
##  <a name="agents"></a> Asynchronous Agents  
 Like lightweight tasks, the runtime does not manage exceptions that are thrown by asynchronous agents.  
  
 The following example shows one way to handle exceptions in a class that derives from [concurrency::agent](../parallel/agent-class.md). This example defines the `points_agent` class. The `points_agent::run` method reads `point` objects from the message buffer and prints them to the console. The `run` method throws an exception if it receives a `NULL` pointer.  
  
 The `run` method surrounds all work in a `try`-`catch` block. The `catch` block stores the exception in a message buffer. The application checks whether the agent encountered an error by reading from this buffer after the agent finishes.  
  
 [!code[concrt-eh-agents#1](../parallel/codesnippet/CPP/exception-handling-in-the-concurrency-runtime_7.cpp)]  
  
 This example produces the following output.  
  
 **X: 10 Y: 20**  
**X: 20 Y: 30**  
**error occurred in agent: point must not be NULL**  
**the status of the agent is: done** Because the `try`-`catch` block exists outside the `while` loop, the agent ends processing when it encounters the first error. If the `try`-`catch` block was inside the `while` loop, the agent would continue after an error occurs.  
  
 This example stores exceptions in a message buffer so that another component can monitor the agent for errors as it runs. This example uses a [concurrency::single_assignment](../parallel/single_assignment-class.md) object to store the error. In the case where an agent handles multiple exceptions, the `single_assignment` class stores only the first message that is passed to it. To store only the last exception, use the [concurrency::overwrite_buffer](../parallel/overwrite_buffer-class.md) class. To store all exceptions, use the [concurrency::unbounded_buffer](../Topic/unbounded_buffer%20Class.md) class. For more information about these message blocks, see [Asynchronous Message Blocks](../parallel/asynchronous-message-blocks.md).  
  
 For more information about asynchronous agents, see [Asynchronous Agents](../parallel/asynchronous-agents.md).  
  
 [[Top](#top)]  
  
##  <a name="summary"></a> Summary  
 [[Top](#top)]  
  
## See Also  
 [Concurrency Runtime](../parallel/concurrency-runtime.md)   
 [Task Parallelism](../parallel/task-parallelism--concurrency-runtime-.md)   
 [Parallel Algorithms](../parallel/parallel-algorithms.md)   
 [Cancellation](../parallel/cancellation-in-the-ppl.md)   
 [Task Scheduler](../parallel/task-scheduler--concurrency-runtime-.md)   
 [Asynchronous Agents](../parallel/asynchronous-agents.md)