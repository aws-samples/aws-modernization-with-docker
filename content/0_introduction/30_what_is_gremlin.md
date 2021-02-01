---
title: "What is Gremlin?"
date: 2018-10-03T10:14:46-07:00
draft: false
weight: 30
---


![image](/images/gremlin_mascot.png)
Gremlin provides you with the framework to safely, securely, and simply simulate real outages with an ever-growing library of attacks. Using Chaos Engineering to improve system resilience, Gremlin’s “Failure as a Service” makes it easy to find weaknesses in your system before they cause problems for your customers.

We offer several categories of attacks to inject faults into your system:

#### Resource Gremlins
Resource gremlins are a great starting point — simple to run and understand. They reveal how your service degrades when starved of CPU, memory, IO, or disk.

|Gremlin  | Impact                                                        |
|--------|----------------------------------------------------------------|
| CPU    |  Generates high load for one or more CPU cores.                |
| Memory |  Allocates a specific amount of RAM.                           |
| IO     |  Puts read/write pressure on I/O devices such as hard disks    |
| Disk   |  Writes files to disk to fill it to a specific percentage.     |

#### State Gremlins

State gremlins introduce chaos into your infrastructure so that you can observe how well your service handles it or fails.

|Gremlin         | Impact                                                         |
|----------------|----------------------------------------------------------------|
| Shutdown       |  Performs a shutdown (and an optional reboot) on the host operating system to test how your system behaves when losing one or more cluster machines.                                              |
| Time Travel    | Changes the host’s system time, which can be used to simulate adjusting to daylight saving time and other time-related events.                                                              |
| Process Killer |  Kills the specified process, which can be used to simulate application or dependency crashes. (Note: does not work for PID 1, consider a Shutdown attack instead)                                    |

#### Network Gremlins

Network gremlins allow you to see the impact of lost or delayed traffic to your application. Test how your service behaves when you are unable to reach one of your dependencies, internal or external. Limit the impact to only the traffic you want to test by specifying ports, hostnames, and IP addresses.

|Gremlin      | Impact                                                        |
|-------------|---------------------------------------------------------------|
| Blackhole   |  Drops all matching network traffic.                          |
| Latency     |  Injects latency into all matching egress network traffic     |
| Packet Loss |  Induces packet loss into all matching egress network traffic.|
| DNS         |  Blocks access to DNS servers.                                |

More information on what Gremlin is all about can be found on the official [Gremlin documentation page](insert link to Gremlin documentation page).