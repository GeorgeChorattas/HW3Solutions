# HW3 Submission
For this homework we had to firstly implement the Hotel Map Microservices <br />
Secondly we had to Evaluate Hotel Map Microservices <br />
For the second part of the assignement we utilize the wrk2 HTTP benchmarking tool <br />

**For this part i selected The CloudLab cluster at the University of Wisconsin** <br />
The 2 nodes i used was type **c220g1** with the following characteristics :
  * CPU : Two Intel E5-2630 v3 8-core CPUs at 2.40 GHz (Haswell w/ EM64T)
  * RAM : 128GB ECC Memory (8x 16 GB DDR4 1866 MHz dual rank RDIMMs)
  * Disk : Two 1.2 TB 10K RPM 6G SAS SFF HDDs
  * Disk : One Intel DC S3500 480 GB 6G SATA SSDs
  * NIC : Dual-port Intel X520-DA2 10Gb NIC (PCIe v3.0, 8 lanes) (both ports available for experiment use) NIC
  * NIC : Onboard Intel i350 1Gb

## Background 
For this part i created a script that executes the command of wrk2 with different parameters to find the limits of each 
implementantion . In the script i executed the wrk2 benchmarking tool with different threads, connection and workload number.
Then for each implementation i saved the best result and created the following graphs. The scripts are available under the script directory.


## Comments on the results 
![image](https://github.com/ucy-cs452-sp24/hw3-GeorgeChorattas/blob/main/Graphs/monolithic.png)
![image](https://github.com/ucy-cs452-sp24/hw3-GeorgeChorattas/blob/main/Graphs/Microservice.png)

### For the monolithic Version :
  1. Single Node :
     * With a single node, there's a clear upward trend as the number of instances increases from 1 to 2 (tripling the RPS), suggesting that a single instance may not fully utilize the node's resources. Further more, when moving from 2 to 4 instances, there is significant increase in RPS,doubling the RPS. This suggests that the node has sufficient resources to effectively run more instances, hence boosting the application’s ability to handle requests.
  2. 2 Nodes :
     * With two nodes, while there is an increase in RPS when the number of instances is elevated from 1 to 2 and from 2 to 4, the rate of improvement is relatively muted. This pattern suggests that inter-node communication introduces overhead, which affects the efficiency of request handling. As a result, the RPS figures are considerably lower than might be expected if the nodes were operating in isolation.
     
  3. 4 Nodes :
     *  As with the two-node configuration, deploying additional instances of the monolithic application across four nodes doesn't yield proportionally higher RPS values, which are notably lower than what we observed with the single-node setup. This reinforces the notion that the scalability of a monolithic architecture is not linear when spread across multiple nodes. The relatively modest increases in RPS with more instances across four nodes highlight the impact of inter-node communication. The overhead associated with coordinating and managing state across the nodes likely impedes performance gains that would otherwise be achieved by parallel processing.
     
  ### Conclusion of the monolithic Version :
  In conclusion, the isolated environment of a single node demonstrates that a monolithic application can scale effectively, with significant improvements in RPS when the number of instances is increased from 1 to 4. However, this is not the case when the application is deployed across multiple nodes. The monolithic nature of the architecture means that scaling affects the entire application, not just specific services. Consequently, when scaling out to 2 or 4 nodes and adding more instances, the RPS does not increase as expected. This can be attributed to the overhead of inter-node communication and the inefficiency of scaling services that may not necessitate scaling, inherent in the monolithic design. Thus, while individual service scaling is ideal, it is not feasible with a monolithic setup, leading to suboptimal utilization of resources across the nodes.

### For the Microservice Version :
**For this version of the hotelapp when i scaled up the microservices i tried multiple compinations and use the results of the best one for the RPS (for example in the 4 nodes i scaled up microservices of profile,frontend,search to 4 instances and the geo and rate microservices to 2 because we didn't need more)**
 1. Single Node :
    *  Starting with a single node, there's a clear upward trend as the number of instances increases from 1 to 2 (tripling the RPS), likely due to better resource utilization and parallel processing. Further more, when moving from 2 to 4 instances, there is significant increase in RPS,doubling the RPS. This suggests that the node has sufficient resources to effectively run more instances, hence boosting the application’s ability to handle requests. Comparing although the results from the monolithic version we didnt achieve better RPS numbers this is because of the overhead of the communication between the services of the hotelapp.
 2. 2 Nodes :
    * With two nodes, while there is an increase in RPS when the number of instances is elevated from 1 to 2 and from 2 to 4, the rate of improvement is relatively muted. This pattern suggests that inter-node communication introduces overhead, which affects the efficiency of request handling. As a result, the RPS figures are considerably lower than might be expected if the nodes were operating in isolation. Comparing although the results from the monolithic version again we didnt achieve better RPS number and again this is because of the overhead of the communication between the services of the hotelapp and the inter-node communication. 
 3. 4 Nodes :
    * As with the two-node configuration, deploying additional instances of the microservice application across four nodes doesn't yield proportionally higher RPS values, which are notably lower than what we observed with the single-node setup. As we can see from 1 to 2 instances we can see that the RPS are double up , further more for 4 instances the results are significantly lower than the implementantion in single node. This points to the overhead and complexities associated with scaling out in a microservices architecture, such as the latency from inter-node communication and potential bottlenecks in service-to-service interactions. Comparing again the results from the monolithic version we didnt achieve better RPS number. However if we compare the 4 instances in 4 nodes implementantion in the monolithic vesion we can see now that the implementantion of the microservice version with scaled up only profile,frontend,search to 4 instances and the geo and rate microservices to 2 gives us a slightly better result in the RPS pointing out that we utilize better the resources with only scaling specific microservices.
    
### Conclusion of the Microservice Version :
In summary, the performance analysis of the microservices-based hotel application reveals distinct scaling behaviors across various deployments. On a single node, the application's RPS improves significantly as instances increase, demonstrating the node's capacity to utilize resources efficiently and enhance request handling through parallel processing. However, this scalability does not linearly translate to multi-node environments. With two and four nodes, the expected linear increase in RPS is not observed as additional instances are introduced. Although the RPS doubles when the instance count is increased from one to two on both node configurations, further increments to four instances do not maintain this growth rate, highlighting a saturation point. This indicates that while some benefits are garnered from distributing the application across more nodes, they are mitigated by the communication overhead inherent to the microservices architecture, including both inter-service and inter-node communications. The scenario changes favorably when examining the microservices implementation in a four-node environment with selective scaling. Specifically, if we compare the monolithic version's performance with four instances across four nodes to the microservices architecture—where only the profile, frontend, and search services are scaled up to four instances, while the geo and rate services remain at two, we observe a slightly better RPS outcome. This improved metric suggests that microservices can better utilize resources by scaling only specific, demand-heavy services rather than the application as a whole.
The distributed nature of microservices introduces complexities not present in a monolithic architecture. These include network latency and the additional overhead of managing multiple instances of services across a distributed network. Consequently, when compared to a monolithic setup, the microservices architecture does not achieve higher RPS in multi-node configurations (exept the previous mention), reinforcing the need for a strategic approach to microservice design and deployment that carefully considers the interplay between service distribution and communication overhead. By tactically scaling individual components based on their load and performance requirements, microservices can offer more efficient resource utilization and potentially higher performance gains than a uniformly scaled monolithic counterpart, especially in distributed computing environments.

## Comments on the results tail latency 
![image](https://github.com/ucy-cs452-sp24/hw3-GeorgeChorattas/blob/main/Graphs/Latency%20Monolithic.png)
![image](https://github.com/ucy-cs452-sp24/hw3-GeorgeChorattas/blob/main/Graphs/Latency%20Microservice.png)


### For the monolithic Version :
In the monolithic architecture's tail latency observations, the latency decreases as the number of instances increases within each node configuration, which is visible in the experiments with a single node, 2 nodes, and 4 nodes. However, this trend does show a reversal in the 4 nodes configuration when we scale to 4 instances. Here, the latency experiences a slight uptick compared to the same instance count on 1 and 2 nodes. This increase is relatively small but noteworthy, as it hints at the additional latency introduced by the complexities of inter-node communication. In a four-node setup, even though the system has more resources, the overhead from managing network communications across nodes can lead to increased response times, particularly in the tail end where the worst-case scenarios lie.

### For the microservice Version :
* Single Node: With just a single node, the tail latency decreases as the number of instances increases from 1 to 2, then slightly increases when moving to 4 instances. The initial drop may indicate that the microservices are more efficiently utilizing the available resources without overloading the node, but the slight increase with 4 instances could suggest the onset of resource contention.
* 2 Nodes : For a microservices deployment on two nodes, the observed latency decreases somewhat when scaling from one to two instances, indicating a marginal enhancement in performance. However, it's when the deployment is scaled from two to four instances that a substantial drop in tail latency becomes apparent. This pronounced improvement signals that with four instances, the two-node setup is far more effective in leveraging the available computational resources, leading to a more efficient operation and yielding considerably better latency results.
* 4 Nodes : In the microservices setup across four nodes, a significant drop in tail latency was observed when scaling from one to two instances. This improvement was even more pronounced when increasing from two to four instances of the application, particularly when the profile, search, and frontend services were expanded to four instances, while geo and rate services remained at two.
### Overview addition : 
Notably, a consistent trend was detected across all deployments—whether 1, 2, or 4 nodes—with the same number of microservice instances (2 or 4) : as the number of nodes increased, tail latency decreased. For instance, the tail latency for four instances was higher on a single node compared to two nodes, and it further diminished on four nodes. This indicates that expanding the node count, while keeping the instance count constant, strategically benefits latency, showcasing the distributed capability of the microservices architecture to effectively manage and reduce latency. However, it is important to mention that for a single instance of the microservice application, tail latency was observed to increase as we increase the nodes. This rise can be attributed to the overhead associated with inter-node communication, where each microservice, requiring interaction with another, incurs additional network latency. This underlines one of the trade-offs of the microservices architecture—while it excels in a distributed system, the communication between different services over the network can introduce latency that may not be as prominent in a monolithic architecture.

### Overview for the tail latency :
When comparing tail latency times between the microservices and monolithic versions, it becomes evident that the microservices architecture generally achieves lower latency across various node configurations, particularly when comparing deployments with 2 or 4 instances. For instance, in a 4-node setup with 4 instances, the microservices version—with targeted scaling where profile, search, and frontend services are scaled to four instances while geo and rate services are kept at two—exhibits a significant latency reduction of approximately 0.5 seconds compared to the monolithic version. This highlights the efficiency of microservices in managing distributed loads. However, it's notable that the microservices architecture does not necessarily outperform the monolithic version in a single-node environment for 4 instances. The relatively higher latency observed could be attributed to the overhead from inter-service communication within the microservices architecture and the full utilization of the node's resources, which might lead to contention and reduced performance in this specific context.








