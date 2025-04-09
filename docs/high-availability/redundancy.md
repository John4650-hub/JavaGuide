I don't...
type: redundancy design details\
Category: High availability\
icon: cluster\
I don't...

Redundancy design is the most common means of ensuring that systems and data are high.

For services, redundancy is the idea of multiple deployments of the same service, and if the service in use suddenly hangs up, the system can be quickly switched to a backup, significantly reducing the system's non-availability and increasing its availability.

For data, the idea of redundancy is the same data backup in multiple copies, so that data security can be improved very simply.

Indeed, there is a great deal of redundancy in daily life.

For my part, my way of saving important documents is to use redundant ideas. I'm going to use one of the important files on GitHub and my own cloud, so I can make sure that even if the hard drive is damaged, I can find my important file through GitHub or my own cloud.

High-availability clusters (High Availability Cluster, known as HA Cluster), co-city preparedness, off-site preparedness, and co-city and off-site situations are the most typical applications of redundancy in high-availability system design.

- **High-availability cluster**: Two or more copies of the same service are deployed, and when the service in use suddenly hangs up, the service can be switched to another service, thereby ensuring its high availability.
- **Same-city disaster**: An entire cluster could be deployed in the same data center, while the same services are deployed in different data centers in the same city. Furthermore, the standby service does not process requests. In this way, accidents such as power outages and fires could be avoided.
- **Off-site disaster preparedness**: Unlike similar urban disaster preparedness, the same services are deployed in different locations (usually far away, even in different cities or countries).
- **Living with the same city**: Similar to the same city, but the backup service can handle requests, thus making full use of system resources and improving the co-production of systems.
- **Lives in the diaspora**: Services are deployed in different data centers in the diaspora, and they can be provided externally at the same time.

High-availability clusters are simply redundancy of services and do not emphasize geography. The co-cities, the vagaries, the co-cities, and the vagaries have resulted in geographical redundancy.

The main difference between the city and the diaspora is the distance between the data centers. The distance is usually remote, even in different cities or countries.

Compared to the traditional disaster preparedness design, the most obvious change with the city's live and live is the “live,” that is, all stations provide services outside the country at the same time. Living in the off-sites is a response to sudden-onset events such as fires, earthquakes, and other natural or man-made disasters.

It's not enough to be redundant. We have to go with it. The so-called failure transfer is simply a quick and automatic switch from non-usable to available services, and no artificial interference is required throughout the process.

For example, in the Redis cluster of the sentinel model, if the sentinel detects a failure at the master node, it will help us to achieve a failure transfer, automatically upgrade a slave to the master, and ensure the availability of the entire Redis system. The process is fully automatic and does not require manual intervention. The technical interview section of my database section in [Java Interview Point North](https://www.yuque.com/docs/share/f37fc804-bfe6-4b0d-b373-9c462188fec7) details the knowledge points/interviews related to the Redis cluster, available to interested small partners.

One more example: Nginx can combine Keepalived to achieve high availability. If the main server of Nginx collapses, Keepalived can automatically perform a failure transfer, and the main server of Nginx is upgraded to the main service. And this switch is transparent because the virtual IPs used do not change. The "server" section of my "technical interview chapter" ([link](https://www.yuque.com/docs/share/f37fc804-bfe6-4b0d-b373-9c462188fec7)) provides details of Nginx's relevant knowledge points & interviews available to interested small partners.

The implementation of the offshore multi-active structure is very difficult and requires a great deal of consideration. I am not talented, and I do not have a living structure in practice in the actual project, and my knowledge of it remains in the books.

If you want to learn more about it, I'd like to recommend a few articles that I think are pretty good:

- [As long as you can read it -- drops and silver bullets -- 2021](https://mp.weixin.qq.com/s/T6mMDtTfBuiIiEowCpqu6Q)
- [Four-step construction of multiple alien life](https://mp.weixin.qq.com/s/hMD-IS__4JE5_nQhYPYSTg)
- \[A architecture for starting
