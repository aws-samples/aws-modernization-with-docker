+++
title = "How to do Chaos Engineering?"
chapter = false
weight = 70
+++

To implement Chaos Engineering, one should follow the scientific method to implement experiments: 

1. Observe Your System 
2. Baseline your metrics 
3. Form a Hypothesis with **Abort Conditions**
4. Define **Blast Radius**
5. Run Experiment 
6. Analyze Results
7. Expand Scope and Re-Test
8. Share Results


 **Blast Radius** is the number of hosts, containers or resources that are targeted in an experiment. This is also known as the subset of a system that can be impacted by an attack; the worst case impact of a failed experiment. 

+ For example, usually measured in customer impact (i.e. 10% of customers could be impacted), but may be expressed in hosts, services, or containers. 

 **Magnitude** is how the intensity of the attack you’re running is defined, and can also be defined as the impact that an experiment has. 

+ For example, a CPU attack would have a different magnitude if it targeted 10% of CPU versus 20% of CPU.

 **Abort Conditions** are the conditions that would cause to you to press the halt button. They are system conditions that indicate when we should stop a chaos experiment in order to avoid accidental damage

+ One should always define Abort Conditions. Examples of abort conditions include SLAs, Error Rates, Availability, Latency, Traffic, and any other KPI that matters to your organization.  

When running chaos experiments, it is recommended to start with a small blast radius and magnitude. As you run more experiments and build more confidence, you can increase the blast radius and magnitude. 

