[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/IAASVEAZ)
# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: LI, Mengxiao
### Student Id: 21112698
### Email: mlieo@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

    > I use SysBench to measure cpu performance.  
    >  **The command I use to test the CPU is:**  
    > `sysbench cpu --cpu-max-prime=20000 run`  
    > This parameter specifies the maximum prime value that the CPU will calculate during the test. Setting it to 20000 means that the CPU will perform the prime number test and calculate until 20000. As the prime number increases, the complexity of the calculation task will also increase. This will affect the CPU load and execution efficiency during the test. This setting is to increase the calculation difficulty of the CPU, so that higher calculation pressure can be simulated, and the performance of the CPU in large-scale calculations can be better tested.  
    > **The command I use to test the memory is:**  
    > `sysbench memory --memory-block-size=1M --memory-total-size=10G run`  
    > --memory-block-size=1M：Set the size of each memory block to 1MB. --memory-total-size=10G：Specifies the total memory size used during the test. Here it is 10GB. This command simulates large-scale memory read and write operations to help evaluate the performance of memory bandwidth and latency.
    > This First column shows the CPU performance of each VM type in events/sec. This is a measure of the CPU's ability to handle computing tasks, indicating the number of events that can be processed per second. The higher the value, the more efficient the CPU is in performing tasks.  
    > This Second column shows the memory performance of each VM type in MiB/sec (megabytes transferred per second). This is a measure of memory bandwidth, which indicates the amount of data transferred through the memory per second. A higher value means that the system is able to perform memory operations at a higher rate, which is suitable for large data transfers or memory-intensive tasks.
    
2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

    In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

    | Size        | CPU performance | Memory performance |
    | ----------- | --------------- | ------------------ |
    | `t2.micro` |    889.80 events/sec      |       3570.36 MiB/sec     |
    | `t2.medium`  |      885.97 events/sec           |        3589.40 MiB/sec            |
    | `c5d.large` |        449.49 events/sec        |       14735.28   MiB/sec          |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.  
    > **Answer and analyze:**  
    > From the CPU performance perspective, increasing the number of vCPUs does not seem to lead to a proportional increase in performance. This suggests that while t2.medium has more vCPUs, performance does not scale linearly. This could be due to factors like architecture, burstable performance modes (as seen in T2 instances), or CPU allocation policies, which limit the potential increase in CPU performance.  
    > In terms of memory performance, increasing memory resources clearly results in a substantial improvement. The memory performance of c5d.large is far superior to the other two instances, demonstrating that more memory resources lead to higher efficiency in memory operations.  


## Question 2: Measure the EC2 Network performance

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    | Type                      | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | `t3.medium` - `t3.medium` |       4100       |    min/avg/max/mdev = 0.178/0.207/0.284/0.023      |
    | `m5.large` - `m5.large`   |      3940     |    min/avg/max/mdev = 0.120/0.138/0.171/0.013 |
    | `c5n.large` - `c5n.large` |        4960        |     min/avg/max/mdev = 0.154/0.165/0.176/0.007      |
    | `t3.medium` - `c5n.large` |     2200           |    min/avg/max/mdev = 0.781/0.791/0.797/0.005      |
    | `m5.large` - `c5n.large`  |      2960          |     min/avg/max/mdev = 0.414/0.435/0.471/0.017       |
    | `m5.large` - `t3.medium`  |        1380        |    min/avg/max/mdev = 1.063/1.100/1.148/0.025     |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. Note: Use private IP address when using iPerf within the same region. You'll need iPerf for measuring TCP bandwidth and Ping for measuring Round-Trip time.  
    > **Answer and analyze:**  
    > For the same type instances, the network performance generally shows a higher TCP bandwidth and lower RTT. The c5n.large - c5n.large configuration offers the best TCP bandwidth at 4960 Mbps with a very low RTT. According to the different type instances, there is a noticeable drop in both TCP bandwidth and RTT. TCP bandwidth drops significantly when mixing instance types (e.g., t3.medium - c5n.large shows a decrease to 2200 Mbps from 4100 Mbps in the same type comparison).RTT increases when different types are involved, with the highest RTT observed in the m5.large - t3.medium combination at 1.100 ms, compared to the very low RTT values within the same type configurations.  

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

    | Connection                | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | N. Virginia - Oregon      |         33       |     min/avg/max/mdev = 59.803/59.817/59.843/0.010       |
    | N. Virginia - N. Virginia |          9520      |     min/avg/max/mdev = 0.090/0.096/0.118/0.007      |
    | Oregon - Oregon           |        9160        |     min/avg/max/mdev = 0.098/0.107/0.116/0.006     |
 
    > Region: US East (N. Virginia), US West (Oregon). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. All instances are `c5.large`. Note: Use public IP address when using iPerf within the same region.  
    > **Answer and analyze:**  
    > Cross-region connection (e.g., N. Virginia - Oregon) has much worse network performance, with a TCP bandwidth of only 33 Mbps and a high RTT of around 59.8 ms. This indicates that data transmission between different regions is limited in bandwidth and experiences higher latency.  
    > Same-region connections (e.g., N. Virginia - N. Virginia and Oregon - Oregon) offer much higher TCP bandwidth and lower RTT, indicating very efficient network communication within the same region.  
