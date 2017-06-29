---

# required metadata
title: "Evaluate the load balancing capacity of your R Server configuration to operationalize - Microsoft R Server | Microsoft Docs"
description: "Load Balancing Capacity and simulation tests for latency and thread counts"
keywords: ""
author: "j-martens"
ms.author: "jmartens"
manager: "jhubbard"
ms.date: "6/21/2017"
ms.topic: "article"
ms.prod: "microsoft-r"

# optional metadata
#ROBOTS: ""
#audience: ""
#ms.devlang: ""
#ms.reviewer: ""
#ms.suite: ""
#ms.tgt_pltfrm: ""
ms.technology:
  - deployr
  - r-server
#ms.custom: ""
---

# Evaluate the load balancing capacity of your R Server configuration to operationalize

**Applies to:  Microsoft R Server 9.x**

The Evaluate Capacity tool allows you to test your own R code deployed as a web service in your own setup. The tool outputs an accurate evaluation of the latency/thread count for the simulation parameters you define and a break-down graph.

You can define the parameters for the traffic simulation for a given configuration or for a given web service. You can test for maximum latency or maximum thread count.

+ **Maximum Latency:** Define the maximum number of milliseconds for a web node request, the initial thread count, and the thread increments for the test. The test will increase the number of threads by the defined increment until the defined time limit is reached.

+ **Maximum Thread Count:** Define the number of threads against which you want to run, such as 10, 15, or 40.  The test will increase the number of parallel requests by the specified increment until the maximum number of threads is reached.

> [!NOTE]
> Web nodes are stateless, and therefore, session persistence ("stickiness") is not required.
<br>

## Configure Test Parameters

1. On the web node, [launch the administration utility](#launch) with administrator privileges (Windows) or `root`/ `sudo` privileges (Linux).

1. From the main menu, choose the option to **Evaluate Capacity** and review the current test parameters.

   ![Test Parameters](./media/configure-evaluate-capacity/admin-capacity-parameters.png)

1. To choose a different web service:

   1. From the sub-menu, choose the option for **Change the service for simulation**.
   1. Specify the new service:
      + To use an existing service, enter `Yes` and provide the service's name and version as `<name>/<version>`. For example, `my-service/1.1`.
      + To use the generated [default service], enter `No`.
   1. When prompted, enter the required input parameters for the service in a JSON format. <br>For example, for a vector/matrix, follow the JSON format such as `[1,2,3]` for vector, `[[…]]` for matrix. A data.frame is a map where each key is a column name, and each value is represented by a vector of the column values.

1. To test for the maximum latency:

   1. From the sub-menu, choose the option for **Change thread/latency limits**.
   1. When prompted, enter `Time` to define the number of threads against which you want to test.
   1. Specify the maximum latency in milliseconds after which the test will stop.
   1. Specify the minimum thread count at which the test will start.
   1. Specify the increment by which the number of threads will increase for each iteration until the maximum latency is reached.

1. To test for the maximum number of parallel requests that can be supported:

   1. From the sub-menu, choose the option for **Change thread/latency limits**.
   1. When prompted, enter `Threads` to define the maximal threshold for the duration of a web node request.
   1. Specify the maximum thread count after which the test will stop running.
   1. Specify the minimum thread count at which the test will start.
   1. Specify the increment by which the number of threads will increase for each iteration.

<br>

## Run Simulation Tests

1. On the web node, [launch the administration utility](#launch).
1. From the main menu, choose the option to **Evaluate Capacity**. The current test parameters appears.
1. From the sub menu, choose the option to **Run capacity simulation** to start the simulation.
1. Review the results onscreen.

   ![Onscreen results](./media/configure-evaluate-capacity/admin-capacity-results-cl.png)
1. Paste the URL printed onto the screen into your browser for a visual representation of the results (see below).

<br>

## Understanding the Results

It is important to understand the results of these simulations to determine whether any configuration changes are warranted, such as adding more web or compute nodes, increasing the pool size, and so on.

### Console Results

After the tool is run, the results are printed to the console. 

![Onscreen results](./media/configure-evaluate-capacity/admin-capacity-results-cl.png)

### Chart Report

The test results are divided into request processing stages to enable you to see if any configuration changes are warranted, such as adding more web or compute nodes, increase the pool size, and so on.



|Stage|Time Measured|
|------|-----------|
|Web Node Request|Time for the request from the web node's controller to go all the way to [`deployr-rserve`](https://github.com/Microsoft/deployr-rserve) and back|
|Create Shell|Time to create a shell or take it from the pool|
|Initialize Shell|Time to load the data (model or snapshot) into the shell prior to execution|
|Web Node to Compute Node|Time for a request from the web node to reach the compute node|
|Compute Node Request|Time for a request from the compute node to reach [`deployr-rserve`](https://github.com/Microsoft/deployr-rserve) and return to the node|

<br>
You can also explore the results visually in a break-down graph using the URL that is returned to the console. 

![URL results](./media/configure-evaluate-capacity/admin-capacity-results-url.png)

<a name="pool"></a>

### R Shell Pool 

When using R Server for operationalization, R code is executed in a session or as a service on a compute node. In order to optimize load-balancing performance, R Server is capable of establishing and maintaining a pool of R shells for R code execution.  This pool limits the maximum number of R shells can be used to execute in parallel.

There is a cost to creating an R shell both in time and memory. So having a pool of existing R shells awaiting R code execution requests means no time will be lost on shell creation at runtime thereby shortening the processing time. Instead, the time needed to create R shells for the pool occurs whenever the compute node is restarted. For this reason, the larger the defined initial pool size(`InitialSize`), the longer it will take to start up the compute node. 

New R shells can be added to the pool until the maximum pool size (`MaxSize`) is reached. Whenever the last R shell in the pool is called, a new R shell is automatically created for the next, future execution request until the maximum is reached. After the maximum is reached, the compute node will return a `503 - server busy` response. However, during simulation test, the test will continue until the test threshold is met (maximum threads or latency). If the number of R shells needed to run the test exceeds the number of shells in the pool, a new R shell will be created on-demand when the request is made and the time it takes to execute the code will be longer since time will be spent creating the shell itself. 

The size of this pool can be adjusted in the external configuration file, `appsettings.json`, found on each compute node.

```
"Pool": {
    "InitialSize": 5,
    "MaxSize": 80
  },
```

Since each compute node has its own thread pool for R shells, configuring multiple compute nodes means that more pooled R shells will be available to your users. 


**To update the thread pool:**

   1. On each compute nodes, [open the `appsettings.json` configuration file](configure-find-admin-configuration-file.md).

   1. Search for the section starting with `"Pool": {`

   1. Set the `InitialSize`. This is the number of R shells that will be pre-created for your users each time the compute node is restarted.

   1. Set the `MaxSize`. This is the maximum number of R shells that can be pre-created and held in memory for processing R code execution requests. 

   1. Save the file.

   1. [Restart](configure-use-admin-utility.md#startstop) the compute node services. 

   1. Repeat these changes on every compute node.


>[!Note]
>Each compute node should have the same `appsettings.json` properties.
