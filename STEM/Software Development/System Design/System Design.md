- **Performance vs scalability**>>>
    - A service is **scalable** if it results in increased **performance** in a manner proportional to resources added. Generally, increasing performance means serving more units of work, but it can also be to handle larger units of work, such as when datasets grow.[1](http://www.allthingsdistributed.com/2006/03/a_word_on_scalability.html)
    - Another way to look at performance vs scalability:>>>
        - If you have a **performance** problem, your system is slow for a single user.
        - If you have a **scalability** problem, your system is fast for a single user but slow under heavy load.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-1)**Source(s) and further reading**>>>
        - [A word on scalability](http://www.allthingsdistributed.com/2006/03/a_word_on_scalability.html)
        - [Scalability, availability, stability, patterns](http://www.slideshare.net/jboner/scalability-availability-stability-patterns/)
- **Latency vs throughput**>>>
    - **Latency** is the time to perform some action or to produce some result.
    - **Throughput** is the number of such actions or results per unit of time.
    - Generally, you should aim for **maximal throughput** with **acceptable latency**.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-2)**Source(s) and further reading**>>>
        - [Understanding latency vs throughput](https://community.cadence.com/cadence_blogs_8/b/sd/archive/2010/09/13/understanding-latency-vs-throughput)
- **Consistency patterns**>>>
    - With multiple copies of the same data, we are faced with options on how to synchronize them so clients have a consistent view of the data. Recall the definition of consistency from the [CAP theorem](https://github.com/donnemartin/system-design-primer#cap-theorem) - Every read receives the most recent write or an error.
    - [**Link**](https://github.com/donnemartin/system-design-primer#weak-consistency)**Weak consistency**
    - After a write, reads may or may not see it. A best effort approach is taken.
    - This approach is seen in systems such as memcached. Weak consistency works well in real time use cases such as VoIP, video chat, and realtime multiplayer games. For example, if you are on a phone call and lose reception for a few seconds, when you regain connection you do not hear what was spoken during connection loss.
    - [**Link**](https://github.com/donnemartin/system-design-primer#eventual-consistency)**Eventual consistency**
    - After a write, reads will eventually see it (typically within milliseconds). Data is replicated asynchronously.
    - This approach is seen in systems such as DNS and email. Eventual consistency works well in highly available systems.
    - [**Link**](https://github.com/donnemartin/system-design-primer#strong-consistency)**Strong consistency**
    - After a write, reads will see it. Data is replicated synchronously.
    - This approach is seen in file systems and RDBMSes. Strong consistency works well in systems that need transactions.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-4)**Source(s) and further reading**>>>
        - [Transactions across data centers](http://snarfed.org/transactions_across_datacenters_io.html)
- **Availability patterns**>>>
    - There are two main patterns to support high availability: **fail-over** and **replication**.
    - [**Link**](https://github.com/donnemartin/system-design-primer#fail-over)**Fail-over**[**Link**](https://github.com/donnemartin/system-design-primer#active-passive)**Active-passive**
    - With active-passive fail-over, heartbeats are sent between the active and the passive server on standby. If the heartbeat is interrupted, the passive server takes over the active's IP address and resumes service.
    - The length of downtime is determined by whether the passive server is already running in 'hot' standy or whether it needs to start up from 'cold' standby. Only the active server handles traffic.
    - Active-passive failover can also be referred to as master-slave failover.
    - [**Link**](https://github.com/donnemartin/system-design-primer#active-active)**Active-active**
    - In active-active, both servers are managing traffic, spreading the load between them.
    - If the servers are public-facing, the DNS would need to know about the public IPs of both servers. If the servers are internal-facing, application logic would need to know about both servers.
    - Active-active failover can also be referred to as master-master failover.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-failover)**Disadvantage(s): failover**>>>
        - Fail-over adds more hardware and additional complexity.
        - There is a potential for loss of data if the active system fails before any newly written data can be replicated to the passive.
    - [**Link**](https://github.com/donnemartin/system-design-primer#replication)**Replication****Master-slave replication**[**Link**](https://github.com/donnemartin/system-design-primer#master-slave-and-master-master)
    - The master serves reads and writes, replicating writes to one or more slaves, which serve only reads. Slaves can also replicate to additional slaves in a tree-like fashion. If the master goes offline, the system can continue to operate in read-only mode until a slave is promoted to a master or a new master is provisioned.
    - [Link](https://camo.githubusercontent.com/6a097809b9690236258747d969b1d3e0d93bb8ca/687474703a2f2f692e696d6775722e636f6d2f4339696f47746e2e706e67)
[ __Source: Scalability, availability, stability, patterns__ ](http://www.slideshare.net/jboner/scalability-availability-stability-patterns/)
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-master-slave-replication)**Disadvantage(s): master-slave replication**>>>
        - Additional logic is needed to promote a slave to a master.
        - See [Disadvantage(s): replication](https://github.com/donnemartin/system-design-primer#disadvantages-replication) for points related to **both** master-slave and master-master.
    - [**Link**](https://github.com/donnemartin/system-design-primer#master-master-replication)**Master-master replication****Both masters serve reads and writes and coordinate with each other on writes. If either master goes down, the system can continue to operate with both reads and writes.**
    - [Link](https://camo.githubusercontent.com/5862604b102ee97d85f86f89edda44bde85a5b7f/687474703a2f2f692e696d6775722e636f6d2f6b7241484c47672e706e67)
[ __Source: Scalability, availability, stability, patterns__ ](http://www.slideshare.net/jboner/scalability-availability-stability-patterns/)
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-master-master-replication)**Disadvantage(s): master-master replication**>>>
        - You'll need a load balancer or you'll need to make changes to your application logic to determine where to write.
        - Most master-master systems are either loosely consistent (violating ACID) or have increased write latency due to synchronization.
        - Conflict resolution comes more into play as more write nodes are added and as latency increases.
        - See [Disadvantage(s): replication](https://github.com/donnemartin/system-design-primer#disadvantages-replication) for points related to **both** master-slave and master-master.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-replication)**Disadvantage(s): replication**>>>
        - There is a potential for loss of data if the master fails before any newly written data can be replicated to other nodes.
        - Writes are replayed to the read replicas. If there are a lot of writes, the read replicas can get bogged down with replaying writes and can't do as many reads.
        - The more read slaves, the more you have to replicate, which leads to greater replication lag.
        - On some systems, writing to the master can spawn multiple threads to write in parallel, whereas read replicas only support writing sequentially with a single thread.
        - Replication adds more hardware and additional complexity.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-replication)**Source(s) and further reading: replication**>>>
        - [Scalability, availability, stability, patterns](http://www.slideshare.net/jboner/scalability-availability-stability-patterns/)
        - [Multi-master replication](https://en.wikipedia.org/wiki/Multi-master_replication)
- **Availability vs consistency**>>>
    - **CAP theorem**
    - [Link](https://camo.githubusercontent.com/13719354da7dcd34cd79ff5f8b6306a67bc18261/687474703a2f2f692e696d6775722e636f6d2f62674c4d4932752e706e67) 
[ __Source: CAP theorem revisited__ ](http://robertgreiner.com/2014/08/cap-theorem-revisited)
    - In a distributed computer system, you can only support two of the following guarantees:>>>
        - **Consistency** - Every read receives the most recent write or an error
        - **Availability** - Every request receives a response, without guarantee that it contains the most recent version of the information
        - **Partition Tolerance** - The system continues to operate despite arbitrary partitioning due to network failures
        -  __Networks aren't reliable, so you'll need to support partition tolerance. You'll need to make a software tradeoff between consistency and availability.__ 
    - [**Link**](https://github.com/donnemartin/system-design-primer#cp---consistency-and-partition-tolerance)**CP - consistency and partition tolerance**
    - Waiting for a response from the partitioned node might result in a timeout error. CP is a good choice if your business needs require atomic reads and writes.
    - [**Link**](https://github.com/donnemartin/system-design-primer#ap---availability-and-partition-tolerance)**AP - availability and partition tolerance**
    - Responses return the most recent version of the data, which might not be the latest. Writes might take some time to propagate when the partition is resolved.
    - AP is a good choice if the business needs allow for [eventual consistency](https://github.com/donnemartin/system-design-primer#eventual-consistency) or when the system needs to continue working despite external errors.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-3)**Source(s) and further reading**>>>
        - [CAP theorem revisited](http://robertgreiner.com/2014/08/cap-theorem-revisited/)
        - [A plain english introduction to CAP theorem](http://ksat.me/a-plain-english-introduction-to-cap-theorem/)
        - [CAP FAQ](https://github.com/henryr/cap-faq)
- **Domain name system**>>>
    - [Link](https://camo.githubusercontent.com/fae27d1291ed38dd120595d692eacd2505cd3a9c/687474703a2f2f692e696d6775722e636f6d2f494f794c6a34692e6a7067) 
[ __Source: DNS security presentation__ ](http://www.slideshare.net/srikrupa5/dns-security-presentation-issa)
    - A Domain Name System (DNS) translates a domain name such as [www.example.com](http://www.example.com/) to an IP address.
    - DNS is hierarchical, with a few authoritative servers at the top level. Your router or ISP provides information about which DNS server(s) to contact when doing a lookup. Lower level DNS servers cache mappings, which could become stale due to DNS propagation delays. DNS results can also be cached by your browser or OS for a certain period of time, determined by the [time to live (TTL)](https://en.wikipedia.org/wiki/Time_to_live).>>>
        - **NS record (name server)** - Specifies the DNS servers for your domain/subdomain.
        - **MX record (mail exchange)** - Specifies the mail servers for accepting messages.
        - **A record (address)** - Points a name to an IP address.
        - **CNAME (canonical)** - Points a name to another name or `CNAME` (example.com to [www.example.com](http://www.example.com/)) or to an `A`record.
    - Services such as [CloudFlare](https://www.cloudflare.com/dns/) and [Route 53](https://aws.amazon.com/route53/) provide managed DNS services. Some DNS services can route traffic through various methods:>>>
        - [Weighted round robin](http://g33kinfo.com/info/archives/2657)>>>
            - Prevent traffic from going to servers under maintenance
            - Balance between varying cluster sizes
            - A/B testing
        - Latency-based
        - Geolocation-based
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-dns)**Disadvantage(s): DNS**>>>
        - Accessing a DNS server introduces a slight delay, although mitigated by caching described above.
        - DNS server management could be complex, although they are generally managed by [governments, ISPs, and large companies](http://superuser.com/questions/472695/who-controls-the-dns-servers/472729).
        - DNS services have recently come under DDoS attack, preventing users from accessing websites such as Twitter without knowing Twitter's IP address(es).
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-5)**Source(s) and further reading**>>>
        - [DNS architecture](https://technet.microsoft.com/en-us/library/dd197427(v=ws.10).aspx)
        - [Wikipedia](https://en.wikipedia.org/wiki/Domain_Name_System)
        - [DNS articles](https://support.dnsimple.com/categories/dns/)
- **Content delivery network**>>>
    - [Link](https://camo.githubusercontent.com/853a8603651149c686bf3c504769fc594ff08849/687474703a2f2f692e696d6775722e636f6d2f683954417547492e6a7067)
[ __Source: Why use a CDN__ ](https://www.creative-artworks.eu/why-use-a-content-delivery-network-cdn/)
    - A content delivery network (CDN) is a globally distributed network of proxy servers, serving content from locations closer to the user. Generally, static files such as HTML/CSS/JSS, photos, and videos are served from CDN, although some CDNs such as Amazon's CloudFront support dynamic content. The site's DNS resolution will tell clients which server to contact.
    - Serving content from CDNs can significantly improve performance in two ways:>>>
        - Users receive content at data centers close to them
        - Your servers do not have to serve requests that the CDN fulfills
    - [**Link**](https://github.com/donnemartin/system-design-primer#push-cdns)**Push CDNs**
    - Push CDNs receive new content whenever changes occur on your server. You take full responsibility for providing content, uploading directly to the CDN and rewriting URLs to point to the CDN. You can configure when content expires and when it is updated. Content is uploaded only when it is new or changed, minimizing traffic, but maximizing storage.
    - Sites with a small amount of traffic or sites with content that isn't often updated work well with push CDNs. Content is placed on the CDNs once, instead of being re-pulled at regular intervals.
    - [**Link**](https://github.com/donnemartin/system-design-primer#pull-cdns)**Pull CDNs**
    - Pull CDNs grab new content from your server when the first user requests the content. You leave the content on your server and rewrite URLs to point to the CDN. This results in a slower request until the content is cached on the server.
    - A [time-to-live (TTL)](https://en.wikipedia.org/wiki/Time_to_live) determines how long content is cached. Pull CDNs minimize storage space on the CDN, but can create redundant traffic if files expire and are pulled before they have actually changed.
    - Sites with heavy traffic work well with pull CDNs, as traffic is spread out more evenly with only recently-requested content remaining on the CDN.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-cdn)**Disadvantage(s): CDN**>>>
        - CDN costs could be significant depending on traffic, although this should be weighed with additional costs you would incur not using a CDN.
        - Content might be stale if it is updated before the TTL expires it.
        - CDNs require changing URLs for static content to point to the CDN.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-6)**Source(s) and further reading**>>>
        - [Globally distributed content delivery](http://repository.cmu.edu/cgi/viewcontent.cgi?article=2112&context=compsci)
        - [The differences between push and pull CDNs](http://www.travelblogadvice.com/technical/the-differences-between-push-and-pull-cdns/)
        - [Wikipedia](https://en.wikipedia.org/wiki/Content_delivery_network)
- **Load balancer**>>>
    - [Link](https://camo.githubusercontent.com/21caea3d7f67f451630012f657ae59a56709365c/687474703a2f2f692e696d6775722e636f6d2f6838316e39694b2e706e67)
[ __Source: Scalable system design patterns__ ](http://horicky.blogspot.com/2010/10/scalable-system-design-patterns.html)
    - Load balancers distribute incoming client requests to computing resources such as application servers and databases. In each case, the load balancer returns the response from the computing resource to the appropriate client. Load balancers are effective at:>>>
        - Preventing requests from going to unhealthy servers
        - Preventing overloading resources
        - Helping eliminate single points of failure
    - Load balancers can be implemented with hardware (expensive) or with software such as HAProxy.
    - Additional benefits include:>>>
        - **SSL termination** - Decrypt incoming requests and encrypt server responses so backend servers do not have to perform these potentially expensive operations>>>
            - Removes the need to install [X.509 certificates](https://en.wikipedia.org/wiki/X.509) on each server
        - **Session persistence** - Issue cookies and route a specific client's requests to same instance if the web apps do not keep track of sessions
    - To protect against failures, it's common to set up multiple load balancers, either in [active-passive](https://github.com/donnemartin/system-design-primer#active-passive) or [active-active](https://github.com/donnemartin/system-design-primer#active-active) mode.
    - Load balancers can route traffic based on various metrics, including:>>>
        - Random
        - Least loaded
        - Seesion/cookies
        - [Round robin or weighted round robin](http://g33kinfo.com/info/archives/2657)
        - [Layer 4](https://github.com/donnemartin/system-design-primer#layer-4-load-balancing)
        - [Layer 7](https://github.com/donnemartin/system-design-primer#layer-7-load-balancing)
    - [**Link**](https://github.com/donnemartin/system-design-primer#layer-4-load-balancing)**Layer 4 load balancing**
    - Layer 4 load balancers look at info at the [transport layer](https://github.com/donnemartin/system-design-primer#communication) to decide how to distribute requests. Generally, this involves the source, destination IP addresses, and ports in the header, but not the contents of the packet. Layer 4 load balancers forward network packets to and from the upstream server, performing [Network Address Translation (NAT)](https://www.nginx.com/resources/glossary/layer-4-load-balancing/).
    - [**Link**](https://github.com/donnemartin/system-design-primer#layer-7-load-balancing)**layer 7 load balancing**
    - Layer 7 load balancers look at the [application layer](https://github.com/donnemartin/system-design-primer#communication) to decide how to distribute requests. This can involve contents of the header, message, and cookies. Layer 7 load balancers terminates network traffic, reads the message, makes a load-balancing decision, then opens a connection to the selected server. For example, a layer 7 load balancer can direct video traffic to servers that host videos while directing more sensitive user billing traffic to security-hardened servers.
    - At the cost of flexibility, layer 4 load balancing requires less time and computing resources than Layer 7, although the performance impact can be minimal on modern commodity hardware.
    - [**Link**](https://github.com/donnemartin/system-design-primer#horizontal-scaling)**Horizontal scaling**
    - Load balancers can also help with horizontal scaling, improving performance and availability. Scaling out using commodity machines is more cost efficient and results in higher availability than scaling up a single server on more expensive hardware, called **Vertical Scaling**. It is also easier to hire for talent working on commodity hardware than it is for specialized enterprise systems.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-horizontal-scaling)**Disadvantage(s): horizontal scaling**>>>
        - Scaling horizontally introduces complexity and involves cloning servers>>>
            - Servers should be stateless: they should not contain any user-related data like sessions or profile pictures
            - Sessions can be stored in a centralized data store such as a [database](https://github.com/donnemartin/system-design-primer#database) (SQL, NoSQL) or a persistent [cache](https://github.com/donnemartin/system-design-primer#cache) (Redis, Memcached)
        - Downstream servers such as caches and databases need to handle more simultaneous connections as upstream servers scale out
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-load-balancer)**Disadvantage(s): load balancer**>>>
        - The load balancer can become a performance bottleneck if it does not have enough resources or if it is not configured properly.
        - Introducing a load balancer to help eliminate single points of failure results in increased complexity.
        - A single load balancer is a single point of failure, configuring multiple load balancers further increases complexity.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-7)**Source(s) and further reading**>>>
        - [NGINX architecture](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/)
        - [HAProxy architecture guide](http://www.haproxy.org/download/1.2/doc/architecture.txt)
        - [Scalability](http://www.lecloud.net/post/7295452622/scalability-for-dummies-part-1-clones)
        - [Wikipedia](https://en.wikipedia.org/wiki/Load_balancing_(computing))
        - [Layer 4 load balancing](https://www.nginx.com/resources/glossary/layer-4-load-balancing/)
        - [Layer 7 load balancing](https://www.nginx.com/resources/glossary/layer-7-load-balancing/)
        - [ELB listener config](http://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-listener-config.html)
- **Reverse proxy (web server)**>>>
    - [Link](https://camo.githubusercontent.com/a66e9f885b04db69638825c6a98f42e5570a83f3/687474703a2f2f692e696d6775722e636f6d2f7037784853345a2e706e67)
[ __Source: Wikipedia__ ](https://commons.wikimedia.org/wiki/File:Proxy_concept_en.svg)
    - A reverse proxy is a web server that centralizes internal services and provides unified interfaces to the public. Requests from clients are forwarded to a server that can fulfill it before the reverse proxy returns the server's response to the client.
    - Additional benefits include:>>>
        - **Increased security** - Hide information about backend servers, blacklist IPs, limit number of connections per client
        - **Increased scalability and flexibility** - Clients only see the reverse proxy's IP, allowing you to scale servers or change their configuration
        - **SSL termination** - Decrypt incoming requests and encrypt server responses so backend servers do not have to perform these potentially expensive operations>>>
            - Removes the need to install [X.509 certificates](https://en.wikipedia.org/wiki/X.509) on each server
        - **Compression** - Compress server responses
        - **Caching** - Return the response for cached requests
        - **Static content** - Serve static content directly>>>
            - HTML/CSS/JS
            - Photos
            - Videos
            - Etc
    - [**Link**](https://github.com/donnemartin/system-design-primer#load-balancer-vs-reverse-proxy)**Load balancer vs reverse proxy**>>>
        - Deploying a load balancer is useful when you have multiple servers. Often, load balancers route traffic to a set of servers serving the same function.
        - Reverse proxies can be useful even with just one web server or application server, opening up the benefits described in the previous section.
        - Solutions such as NGINX and HAProxy can support both layer 7 reverse proxying and load balancing.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-reverse-proxy)**Disadvantage(s): reverse proxy**>>>
        - Introducing a reverse proxy results in increased complexity.
        - A single reverse proxy is a single point of failure, configuring multiple reverse proxies (ie a [failover](https://en.wikipedia.org/wiki/Failover)) further increases complexity.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-8)**Source(s) and further reading**>>>
        - [Reverse proxy vs load balancer](https://www.nginx.com/resources/glossary/reverse-proxy-vs-load-balancer/)
        - [NGINX architecture](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/)
        - [HAProxy architecture guide](http://www.haproxy.org/download/1.2/doc/architecture.txt)
        - [Wikipedia](https://en.wikipedia.org/wiki/Reverse_proxy)
- **Application layer**>>>
    - [Link](https://camo.githubusercontent.com/feeb549c5b6e94f65c613635f7166dc26e0c7de7/687474703a2f2f692e696d6775722e636f6d2f7942355359776d2e706e67)
[ __Source: Intro to architecting systems for scale__ ](http://lethain.com/introduction-to-architecting-systems-for-scale/#platform_layer)
    - Separating out the web layer from the application layer (also known as platform layer) allows you to scale and configure both layers independently. Adding a new API results in adding application servers without necessarily adding additional web servers.
    - The **single responsibility principle** advocates for small and autonomous services that work together. Small teams with small services can plan more aggressively for rapid growth.
    - Workers in the application layer also help enable [asynchronism](https://github.com/donnemartin/system-design-primer#asynchronism).
    - [**Link**](https://github.com/donnemartin/system-design-primer#microservices)**Microservices**
    - Related to this discussion are [microservices](https://en.wikipedia.org/wiki/Microservices), which can be described as a suite of independently deployable, small, modular services. Each service runs a unique process and communicates through a well-definied, lightweight mechanism to serve a business goal. [1](https://smartbear.com/learn/api-design/what-are-microservices)
    - Pinterest, for example, could have the following microservices: user profile, follower, feed, search, photo upload, etc.
    - [**Link**](https://github.com/donnemartin/system-design-primer#service-discovery)**Service Discovery**
    - Systems such as [Zookeeper](http://www.slideshare.net/sauravhaloi/introduction-to-apache-zookeeper) can help services find each other by keeping track of registered names, addresses, ports, etc.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-application-layer)**Disadvantage(s): application layer**>>>
        - Adding an application layer with loosely coupled services requires a different approach from an architectural, operations, and process viewpoint (vs a monolithic system).
        - Microservices can add complexity in terms of deployments and operations.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-9)**Source(s) and further reading**>>>
        - [Intro to architecting systems for scale](http://lethain.com/introduction-to-architecting-systems-for-scale)
        - [Crack the system design interview](http://www.puncsky.com/blog/2016/02/14/crack-the-system-design-interview/)
        - [Service oriented architecture](https://en.wikipedia.org/wiki/Service-oriented_architecture)
        - [Introduction to Zookeeper](http://www.slideshare.net/sauravhaloi/introduction-to-apache-zookeeper)
        - [Here's what you need to know about building microservices](https://cloudncode.wordpress.com/2016/07/22/msa-getting-started/)
- **Database**>>>
    - **Database**
    - [Link](https://camo.githubusercontent.com/15a7553727e6da98d0de5e9ca3792f6d2b5e92d4/687474703a2f2f692e696d6775722e636f6d2f586b6d3543587a2e706e67)
[ __Source: Scaling up to your first 10 million users__ ](https://www.youtube.com/watch?v=vg5onp8TU6Q)
    - [**Link**](https://github.com/donnemartin/system-design-primer#relational-database-management-system-rdbms)**Relational database management system (RDBMS)**
    - A relational database like SQL is a collection of data items organized in tables.
    - **ACID** is a set of properties of relational database [transactions](https://en.wikipedia.org/wiki/Database_transaction).>>>
        - **Atomicity** - Each transaction is all or nothing
        - **Consistency** - Any tranaction will bring the database from one valid state to another
        - **Isolation** - Excuting transactions concurrently has the same results as if the transactions were executed serially
        - **Durability** - Once a transaction has been committed, it will remain so
    - There are many techniques to scale a relational database: **master-slave replication**, **master-master replication**, **federation**, **sharding**, **denormalization**, and **SQL tuning**.
- **Federation**>>>
    - [Link](https://camo.githubusercontent.com/6eb6570a8b6b4e1d52e3d7cc07e7959ea5dac75f/687474703a2f2f692e696d6775722e636f6d2f553371563333652e706e67) 
[ __Source: Scaling up to your first 10 million users__ ](https://www.youtube.com/watch?v=vg5onp8TU6Q)
    - Federation (or functional partitioning) splits up databases by function. For example, instead of a single, monolithic database, you could have three databases: **forums**, **users**, and **products**, resulting in less read and write traffic to each database and therefore less replication lag. Smaller databases result in more data that can fit in memory, which in turn results in more cache hits due to improved cache locality. With no single central master serializing writes you can write in parallel, increasing throughput.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-federation)**Disadvantage(s): federation**>>>
        - Federation is not effective if your schema requires huge functions or tables.
        - You'll need to update your application logic to determine which database to read and write.
        - Joining data from two databases is more complex with a [server link](http://stackoverflow.com/questions/5145637/querying-data-by-joining-two-tables-in-two-database-on-different-servers).
        - Federation adds more hardware and additional complexity.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-federation)**Source(s) and further reading: federation**>>>
        - [Scaling up to your first 10 million users](https://www.youtube.com/watch?v=vg5onp8TU6Q)
- **Sharding**>>>
    - [Link](https://camo.githubusercontent.com/1df78be67b749171569a0e11a51aa76b3b678d4f/687474703a2f2f692e696d6775722e636f6d2f775538783549642e706e67) 
[ __Source: Scalability, availability, stability, patterns__ ](http://www.slideshare.net/jboner/scalability-availability-stability-patterns/)
    - Sharding distributes data across different databases such that each database can only manage a subset of the data. Taking a users database as an example, as the number of users increases, more shards are added to the cluster.
    - Similar to the advantages of [federation](https://github.com/donnemartin/system-design-primer#federation), sharding results in less read and write traffic, less replication, and more cache hits. Index size is also reduced, which generally improves performance with faster queries. If one shard goes down, the other shards are still operational, although you'll want to add some form of replication to avoid data loss. Like federation, there is no single central master serializing writes, allowing you to write in parallel with increased throughput.
    - Common ways to shard a table of users is either through the user's last name initial or the user's geographic location.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-sharding)**Disadvantage(s): sharding**>>>
        - You'll need to update your application logic to work with shards, which could result in complex SQL queries.
        - Data distribution can become lobsided in a shard. For example, a set of power users on a shard could result in increased load to that shard compared to others.>>>
            - Rebalancing adds additional complexity. A sharding function based on [consistent hashing](http://www.paperplanes.de/2011/12/9/the-magic-of-consistent-hashing.html) can reduce the amount of transferred data.
        - Joining data from multiple shards is more complex.
        - Sharding adds more hardware and additional complexity.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-sharding)**Source(s) and further reading: sharding**>>>
        - [The coming of the shard](http://highscalability.com/blog/2009/8/6/an-unorthodox-approach-to-database-design-the-coming-of-the.html)
        - [Shard database architecture](https://en.wikipedia.org/wiki/Shard_(database_architecture))
        - [Consistent hashing](http://www.paperplanes.de/2011/12/9/the-magic-of-consistent-hashing.html)
- **Denormalization**>>>
    - Denormalization attemps to improve read performance at the expense of some write performance. Redundant copies of the data are written in multiple tables to avoid expensive joins. Some RDBMS such as [PostgreSQL](https://en.wikipedia.org/wiki/PostgreSQL) and Oracle support [materialized views](https://en.wikipedia.org/wiki/Materialized_view) which handle the work of storing redudant information and keeping redundant copies consistent.
    - Once data becomes distributed with techniques such as [federation](https://github.com/donnemartin/system-design-primer#federation) and [sharding](https://github.com/donnemartin/system-design-primer#sharding), managing joins across data centers further increases complexity. Denormalization might circumvent the need for such complex joins.
    - In most systems, reads can heavily number writes 100:1 or even 1000:1. A read resulting in a complex database join can be very expensive, spending a significant amount of time on disk operations.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-denormalization)**Disadvantage(s): denormalization**>>>
        - Data is duplicated.
        - Constraints can help redundant copies of information stay in sync, which increases complexity of the database design.
        - A denormalized database under heavy write load might perform worse than its normalized counterpart.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-denormalization)**Source(s) and further reading: denormalization**>>>
        - [Denormalization](https://en.wikipedia.org/wiki/Denormalization)
- **SQL tuning**>>>
    - SQL tuning is a broad topic and many [books](https://www.amazon.com/s/ref=nb_sb_noss_2?url=search-alias%3Daps&field-keywords=sql+tuning) have been written as reference.
    - It's important to **benchmark** and **profile** to simulate and uncover bottlenecks.>>>
        - **Benchmark** - Simulate high-load situations with tools such as [ab](http://httpd.apache.org/docs/2.2/programs/ab.html).
        - **Profile** - Enable tools such as the [slow query log](http://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) to help track performance issues.
    - Benchmarking and profiling might point you to the following optimizations.
    - [**Link**](https://github.com/donnemartin/system-design-primer#tighten-up-the-schema)**Tighten up the schema**>>>
        - MySQL dumps to disk in contiguous blocks for fast access.
        - Use `CHAR` instead of `VARCHAR` for fixed-length fields.>>>
            - `CHAR` effectively allows for fast, random access, whereas with `VARCHAR`, you must find the end of a string before moving onto the next one.
        - Use `TEXT` for large blocks of text such as blog posts. `TEXT` also allows for boolean searches. Using a `TEXT` field results in storing a pointer on disk that is used to locate the text block.
        - Use `INT` for larger numbers up to 2^32 or 4 billion.
        - Use `DECIMAL` for currency to avoid floating point representation errors.
        - Avoid storing large `BLOBS`, store the location of where to get the object instead.
        - `VARCHAR(255)` is the largest number of characters that can be counted in an 8 bit number, often maximizing the use of a byte in some RDBMS.
        - Set the `NOT NULL` constraint where applicable to [improve search performance](http://stackoverflow.com/questions/1017239/how-do-null-values-affect-performance-in-a-database-search).
    - [**Link**](https://github.com/donnemartin/system-design-primer#use-good-indices)**Use good indices**>>>
        - Columns that you are querying (`SELECT`, `GROUP BY`, `ORDER BY`, `JOIN`) could be faster with indices.
        - Indices are usually represented as self-balancing [B-tree](https://en.wikipedia.org/wiki/B-tree) that keeps data sorted and allows searches, sequential access, insertions, and deletions in logarithmic time.
        - Placing an index can keep the data in memory, requiring more space.
        - Writes could also be slower since the index also needs to be updated.
        - When loading large amounts of data, it might be faster to disable indices, load the data, then rebuild the indices.
    - [**Link**](https://github.com/donnemartin/system-design-primer#avoid-expensive-joins)**Avoid expensive joins**>>>
        - [Denormalize](https://github.com/donnemartin/system-design-primer#denormalization) where performance demands it.
    - [**Link**](https://github.com/donnemartin/system-design-primer#partition-tables)**Partition tables**>>>
        - Break up a table by putting hot spots in a separate table to help keep it in memory.
    - [**Link**](https://github.com/donnemartin/system-design-primer#tune-the-query-cache)**Tune the query cache**>>>
        - In some cases, the [query cache](http://dev.mysql.com/doc/refman/5.7/en/query-cache) could lead to [performance issues](https://www.percona.com/blog/2014/01/28/10-mysql-performance-tuning-settings-after-installation/).
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-sql-tuning)**Source(s) and further reading: SQL tuning**>>>
        - [Tips for optimizing MySQL queries](http://20bits.com/article/10-tips-for-optimizing-mysql-queries-that-dont-suck)
        - [Is there a good reason i see VARCHAR(255) used so often?](http://stackoverflow.com/questions/1217466/is-there-a-good-reason-i-see-varchar255-used-so-often-as-opposed-to-another-l)
        - [How do null values affect performance?](http://stackoverflow.com/questions/1017239/how-do-null-values-affect-performance-in-a-database-search)
        - [Slow query log](http://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)
- **NoSQL**>>>
    - NoSQL is a collection of data items represented in a **key-value store**, **document-store**, **wide column store**, or a **graph database**. Data is denormalized, and joins are generally done in the application code. Most NoSQL stores lack true ACID transactions and favor [eventual consistency](https://github.com/donnemartin/system-design-primer#eventual-consistency).
    - **BASE** is often used to describe the properties of NoSQL databases. In comparison with the [CAP Theorem](https://github.com/donnemartin/system-design-primer#cap-theorem), BASE chooses availability over consistency.>>>
        - **Basically available** - the system guarantees availability.
        - **Soft state** - the state of the system may change over time, even without input.
        - **Eventual consistency** - the system will become consistent over a period of time, given that the system doesn't receive input during that period.
    - In addition to choosing between [SQL or NoSQL](https://github.com/donnemartin/system-design-primer#sql-or-nosql), it is helpful to understand which type of NoSQL database best fits your use case(s). We'll review **key-value stores**, **document-stores**, **wide column stores**, and **graph databases** in the next section.
    - **Source(s) and further reading: NoSQL**>>>
        - [Explanation of base terminology](http://stackoverflow.com/questions/3342497/explanation-of-base-terminology)
        - [NoSQL databases a survey and decision guidance](https://medium.com/baqend-blog/nosql-databases-a-survey-and-decision-guidance-ea7823a822d#.wskogqenq)
        - [Scalability](http://www.lecloud.net/post/7994751381/scalability-for-dummies-part-2-database)
        - [Introduction to NoSQL](https://www.youtube.com/watch?v=qI_g07C_Q5I)
        - [NoSQL patterns](http://horicky.blogspot.com/2009/11/nosql-patterns.html)
- **Key-value store**>>>
    - Abstraction: hash table
    - A key-value store generally allows for O(1) reads and writes and is often backed by memory or SSD. Data stores can maintain keys in [lexicographic order](https://en.wikipedia.org/wiki/Lexicographical_order), allowing efficient retrieval of key ranges. Key-value stores can allow for storing of metadata with a value.
    - Key-value stores provide high performance and are often used for simple data models or for rapidly-changing data, such as an in-memory cache layer. Since they offer only a limited set of operations, complexity is shifted to the application layer if additional operations are needed.
    - A key-value store is the basis for more complex system systems such as a document store, and in some cases, a graph database.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-key-value-store)**Source(s) and further reading: key-value store**>>>
        - [Key-value database](https://en.wikipedia.org/wiki/Key-value_database)
        - [Disadvantages of key-value stores](http://stackoverflow.com/questions/4056093/what-are-the-disadvantages-of-using-a-key-value-table-over-nullable-columns-or)
        - [Redis architecture](http://qnimate.com/overview-of-redis-architecture/)
        - [Memcached architecture](https://www.adayinthelifeof.nl/2011/02/06/memcache-internals/)
- Document store>>>
    - Abstraction: key-value store with documents stored as values
    - A document store is centered around documents (XML, JSON, binary, etc), where a document stores all information for a given object. Document stores provide APIs or a query language to query based on the internal structure of the document itself.  __Note, many key-value stores include features for working with a value's metadata, blurring the lines between these two storage types.__ 
    - Based on the underlying implementation, documents are organized in either collections, tags, metadata, or directories. Although documents can be organized or grouped together, documents may have fields that are completely different from each other.
    - Some document stores like [MongoDB](https://www.mongodb.com/mongodb-architecture) and [CouchDB](https://blog.couchdb.org/2016/08/01/couchdb-2-0-architecture/) also provide a SQL-like language to perform complex queries.[DynamoDB](../../undefined.md) supports both key-values and documents.
    - Document stores provide high flexibility and are often used for working with occasionally changing data.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-document-store)**Source(s) and further reading: document store**>>>
        - [Document-oriented database](https://en.wikipedia.org/wiki/Document-oriented_database)
        - [MongoDB architecture](https://www.mongodb.com/mongodb-architecture)
        - [CouchDB architecture](https://blog.couchdb.org/2016/08/01/couchdb-2-0-architecture/)
        - [Elasticsearch architecture](https://www.elastic.co/blog/found-elasticsearch-from-the-bottom-up)
- **Wide column store**>>>
    - [Link](https://camo.githubusercontent.com/823668b07b4bff50574e934273c9244e4e5017d6/687474703a2f2f692e696d6775722e636f6d2f6e3136694f476b2e706e67) 
[ __Source: SQL & NoSQL, a brief history__ ](http://blog.grio.com/2015/11/sql-nosql-a-brief-history.html)
    - Abstraction: nested map `ColumnFamily<RowKey, Columns<ColKey, Value, Timestamp>>`
    - A wide column store's basic unit of data is a column (name/value pair). A column can be grouped in column families (analogous to a SQL table). Super column families further group column families. You can access each column independently with a row key, and columns with the same row key form a row. Each value contains a timestamp for versioning and for conflict resolution.
    - Google introduced [Bigtable](../../undefined.md) as the first wide column store, which influenced the open-source [HBase](https://www.mapr.com/blog/in-depth-look-hbase-architecture) often-used in the Hadoop ecosystem, and [Cassandra](http://docs.datastax.com/en/archived/cassandra/2.0/cassandra/architecture/architectureIntro_c.html) from Facebook. Stores such as BigTable, HBase, and Cassandra maintain keys in lexicographic order, allowing efficient retrieval of selective key ranges.
    - Wide column stores offer high availability and high scalability. They are often used for very large data sets.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-wide-column-store)**Source(s) and further reading: wide column store**>>>
        - [SQL & NoSQL, a brief history](http://blog.grio.com/2015/11/sql-nosql-a-brief-history.html)
        - [Bigtable architecture](../../undefined.md)
        - [HBase architecture](https://www.mapr.com/blog/in-depth-look-hbase-architecture)
        - [Cassandra architecture](http://docs.datastax.com/en/archived/cassandra/2.0/cassandra/architecture/architectureIntro_c.html)
- Graph database>>>
    - **Graph database**
    - [Link](https://camo.githubusercontent.com/bf6508b65e98a7210d9861515833afa0d9434436/687474703a2f2f692e696d6775722e636f6d2f664e636c3635672e706e67) 
[ __Source: Graph database__ ](https://en.wikipedia.org/wiki/File:GraphDatabase_PropertyGraph.png)
    - Abstraction: graph
    - In a graph database, each node is a record and each arc is a relationship between two nodes. Graph databases are optimized to represent complex relationships with many foreign keys or many-to-many relationships.
    - Graphs databases offer high performance for data models with complex relationships, such as a social network. They are relatively new and are not yet widely-used; it might be more difficult to find development tools and resources. Many graphs can only be accessed with [REST APIs](https://github.com/donnemartin/system-design-primer#representational-state-transfer-rest).
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-graph)**Source(s) and further reading: graph**>>>
        - [Graph database](https://en.wikipedia.org/wiki/Graph_database)
        - [Neo4j](https://neo4j.com/)
        - [FlockDB](https://blog.twitter.com/2010/introducing-flockdb)
- **SQL or NoSQL**>>>
    - [Link](https://camo.githubusercontent.com/a6e2e844765c9d5382d9c9b64ef7693977981646/687474703a2f2f692e696d6775722e636f6d2f775847714735662e706e67) 
[ __Source: Transitioning from RDBMS to NoSQL__ ](https://www.infoq.com/articles/Transition-RDBMS-NoSQL/)
    - Reasons for **SQL**:>>>
        - Structured data
        - Strict schema
        - Relational data
        - Need for complex joins
        - Transactions
        - Clear patterns for scaling
        - More established: developers, community, code, tools, etc
        - Lookups by index are very fast
    - Reasons for **NoSQL**:>>>
        - Semi-structured data
        - Dynamic or flexible schema
        - Non relational data
        - No need for complex joins
        - Store many TB (or PB) of data
        - Very data intensive workload
        - Very high throughput for IOPS
    - Sample data well-suited for NoSQL:>>>
        - Rapid ingest of clickstream and log data
        - Leaderboard or scoring data
        - Temporary data, such as a shopping cart
        - Frequently accessed ('hot') tables
        - Metadata/lookup tables
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-sql-or-nosql)**Source(s) and further reading: SQL or NoSQL**>>>
        - [Scaling up to your first 10 million users](https://www.youtube.com/watch?v=vg5onp8TU6Q)
        - [SQL vs NoSQL differences](https://www.sitepoint.com/sql-vs-nosql-differences/)
- **Cache**>>>
    - [Link](https://camo.githubusercontent.com/7acedde6aa7853baf2eb4a53f88e2595ebe43756/687474703a2f2f692e696d6775722e636f6d2f51367a32344c612e706e67)
[ __Source: Scalable system design patterns__ ](http://horicky.blogspot.com/2010/10/scalable-system-design-patterns.html)
    - Caching improves page load times and can reduce the load on your servers and databases. In this model, the dispatcher will first lookup if the request has been made before and try to find the previous result to return, in order to save the actual execution.
    - Databases often benefit from a uniform distribution of reads and writes across its partitions. Popular items can skew the distribution, causing bottlenecks. Putting a cache in front of a database can help absorb uneven loads and spikes in traffic.
    - **Disadvantage(s): cache**>>>
        - Need to maintain consistency between caches and the source of truth such as the database through [cache invalidation](https://en.wikipedia.org/wiki/Cache_algorithms).
        - Need to make application changes such as adding Redis or memcached.
        - Cache invalidation is a difficult problem, there is additional complexity associated with when to update the cache.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-10)**Source(s) and further reading**>>>
        - [From cache to in-memory data grid](http://www.slideshare.net/tmatyashovsky/from-cache-to-in-memory-data-grid-introduction-to-hazelcast)
        - [Scalable system design patterns](http://horicky.blogspot.com/2010/10/scalable-system-design-patterns.html)
        - [Introduction to architecting systems for scale](http://lethain.com/introduction-to-architecting-systems-for-scale/)
        - [Scalability, availability, stability, patterns](http://www.slideshare.net/jboner/scalability-availability-stability-patterns/)
        - [Scalability](http://www.lecloud.net/post/9246290032/scalability-for-dummies-part-3-cache)
        - [AWS ElastiCache strategies](http://docs.aws.amazon.com/AmazonElastiCache/latest/UserGuide/Strategies.html)
        - [Wikipedia](https://en.wikipedia.org/wiki/Cache_(computing))
- Cache locations>>>
    - **Client caching**
    - Caches can be located on the client side (OS or browser), [server side](https://github.com/donnemartin/system-design-primer#reverse-proxy), or in a distinct cache layer.
    - [**Link**](https://github.com/donnemartin/system-design-primer#cdn-caching)**CDN caching**
    - [CDNs](https://github.com/donnemartin/system-design-primer#content-delivery-network) are considered a type of cache.
    - [**Link**](https://github.com/donnemartin/system-design-primer#web-server-caching)**Web server caching**
    - [Reverse proxies](https://github.com/donnemartin/system-design-primer#reverse-proxy-web-server) and caches such as [Varnish](https://www.varnish-cache.org/) can serve static and dynamic content directly. Web servers can also cache requests, returning responses without having to contact application servers.
    - [**Link**](https://github.com/donnemartin/system-design-primer#database-caching)**Database caching**
    - Your database usually includes some level of caching in a default configuration, optimized for a generic use case. Tweaking these settings for specific usage patterns can further boost performance.
    - [**Link**](https://github.com/donnemartin/system-design-primer#application-caching)**Application caching**
    - In-memory caches such as Memcached and Redis are key-value stores between your application and your data storage. Since the data is held in RAM, it is much faster than typical databases where data is stored on disk. RAM is more limited than disk, so [cache invalidation](https://en.wikipedia.org/wiki/Cache_algorithms) algorithms such as [least recently used (LRU)](https://en.wikipedia.org/wiki/Cache_algorithms#Least_Recently_Used) can help invalidate 'cold' entries and keep 'hot' data in RAM.
    - Redis has the following additional features:>>>
        - Persistence option
        - Built-in data structures such as sorted sets and lists
    - There are multiple levels you can cache that fall into two general categories: **database queries** and **objects**:>>>
        - Row level
        - Query-level
        - Fully-formed serializable objects
        - Fully-rendered HTML
    - Generaly, you should try to avoid file-based caching, as it makes cloning and auto-scaling more difficult.
- Database caching, what to cache>>>
    - There are multiple levels you can cache that fall into two general categories: **database queries** and **objects**:>>>
        - Row level
        - Query-level
        - Fully-formed serializable objects
        - Fully-rendered HTML
    - Generaly, you should try to avoid file-based caching, as it makes cloning and auto-scaling more difficult.
    - [**Link**](https://github.com/donnemartin/system-design-primer#caching-at-the-database-query-level)**Caching at the database query level**
    - Whenever you query the database, hash the query as a key and store the result to the cache. This approach suffers from expiration issues:>>>
        - Hard to delete a cached result with complex queries
        - If one piece of data changes such as a table cell, you need to delete all cached queries that might include the changed cell
    - [**Link**](https://github.com/donnemartin/system-design-primer#caching-at-the-object-level)**Caching at the object level**
    - See your data as an object, similar to what you do with your application code. Have your application assemble the dataset from the database into a class instance or a data structure(s):>>>
        - Remove the object from cache if its underlying data has changed
        - Allows for asynchronous processing: workers assemble objects by consuming the latest cached object
    - Suggestions of what to cache:>>>
        - User sessions
        - Fully rendered web pages
        - Activity streams
        - User graph data
- **Cache-aside**>>>
    - [Link](https://camo.githubusercontent.com/7f5934e49a678b67f65e5ed53134bc258b007ebb/687474703a2f2f692e696d6775722e636f6d2f4f4e6a4f52716b2e706e67)
[ __Source: From cache to in-memory data grid__ ](http://www.slideshare.net/tmatyashovsky/from-cache-to-in-memory-data-grid-introduction-to-hazelcast)
    - The application is responsible for reading and writing from storage. The cache does not interact with storage directly. The application does the following:>>>
        - Look for entry in cache, resulting in a cache miss
        - Load entry from the database
        - Add entry to cache
        - Return entry
    - ```
def get_user(self, user_id):
    user = cache.get("user.{0}", user_id)
    if user is None:
        user = db.query("SELECT * FROM users WHERE user_id = {0}", user_id)
        if user is not None:
            cache.set(key, json.dumps(user))
    return user
```
    - [Memcached](https://memcached.org/) is generally used in this manner.
    - Subsequent reads of data added to cache are fast. Cache-aside is also referred to as lazy loading. Only requested data is cached, which avoids filling up the cache with data that isn't requested.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-cache-aside)**Disadvantage(s): cache-aside**>>>
        - Each cache miss results in three trips, which can cause a noticeable delay.
        - Data can become stale if it is updated in the database. This issue is mitigated by setting a time-to-live (TTL) which forces an update of the cache entry, or by using write-through.
        - When a node fails, it is replaced by a new, empty node, increasing latency.
- **Write-through**>>>
    - [Link](https://camo.githubusercontent.com/56b870f4d199335ccdbc98b989ef6511ed14f0e2/687474703a2f2f692e696d6775722e636f6d2f3076426330684e2e706e67) 
[ __Source: Scalability, availability, stability, patterns__ ](http://www.slideshare.net/jboner/scalability-availability-stability-patterns/)
    - The application uses the cache as the main data store, reading and writing data to it, while the cache is responsible for reading and writing to the database:>>>
        - Application adds/updates entry in cache
        - Cache synchronously writes entry to data store
        - Return
    - Application code:
    - ```
set_user(12345, {"foo":"bar"})
```
    - Cache code:
    - ```
def set_user(user_id, values):
    user = db.query("UPDATE Users WHERE id = {0}", user_id, values)
    cache.set(user_id, user)
```
    - Write-through is a slow overall operation due to the write operation, but subsequent reads of just written data are fast. Users are generally more tolerant of latency when updating data than reading data. Data in the cache is not stale.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-write-through)**Disadvantage(s): write through**>>>
        - When a new node is created due to failure or scaling, the new node will not cache entries until the entry is updated in the database. Cache-aside in conjunction with write through can mitigate this issue.
        - Most data written might never read, which can be minimized with a TTL.
- **Write-behind (write-back)**>>>
    - [Link](https://camo.githubusercontent.com/8aa9f1a2f050c1422898bb5e82f1f01773334e22/687474703a2f2f692e696d6775722e636f6d2f72675372766a472e706e67) 
[ __Source: Scalability, availability, stability, patterns__ ](http://www.slideshare.net/jboner/scalability-availability-stability-patterns/)
    - In write-behind, tha application does the following:>>>
        - Add/update entry in cache
        - Asynchronously write entry to the data store, improving write performance
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-write-behind)**Disadvantage(s): write-behind**>>>
        - There could be data loss if the cache goes down prior to its contents hitting the data store.
        - It is more complex to implement write-behind than it is to implement cache-aside or write-through.
- Refresh-ahead>>>
    - [Link](https://camo.githubusercontent.com/49dcb54307763b4f56d61a4a1369826e2e7d52e4/687474703a2f2f692e696d6775722e636f6d2f6b78746a7167452e706e67)
[ __Source: From cache to in-memory data grid__ ](http://www.slideshare.net/tmatyashovsky/from-cache-to-in-memory-data-grid-introduction-to-hazelcast)
    - You can configure the cache to automatically refresh any recently accessed cache entry prior to its expiration.
    - Refresh-ahead can result in reduced latency vs read-through if the cache can accurately predict which items are likely to be needed in the future.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-refresh-ahead)**Disadvantage(s): refresh-ahead**>>>
        - Not accurately predicting which items are likely to be needed in the future can result in reduced performance than without refresh-ahead.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-cache)
- **Asynchronism**>>>
    - [Link](https://camo.githubusercontent.com/c01ec137453216bbc188e3a8f16da39ec9131234/687474703a2f2f692e696d6775722e636f6d2f353447597353782e706e67)
[ __Source: Intro to architecting systems for scale__ ](http://lethain.com/introduction-to-architecting-systems-for-scale/#platform_layer)
    - Asynchronous workflows help reduce request times for expensive operations that would otherwise be performed in-line. They can also help by doing time-consuming work in advance, such as periodic aggregation of data.
    - [**Link**](https://github.com/donnemartin/system-design-primer#message-queues)**Message queues**
    - Message queues receive, hold, and deliver messages. If an operation is too slow to perform inline, you can use a message queue with the following workflow:>>>
        - An application publishes a job to the queue, then notifies the user of job status
        - A worker picks up the job from the queue, processes it, then signals the job is complete
    - The user is not blocked and the job is processed in the background. During this time, the client might optionally do a small amount of processing to make it seem like the task has completed. For example, if posting a tweet, the tweet could be instantly posted to your timeline, but it could take some time before your tweet is actually delivered to all of your followers.
    - **Redis** is useful as a simple message broker but messages can be lost.
    - **RabbitMQ** is popular but requires you to adapt to the 'AMQP' protocol and manage your own nodes.
    - **Amazon SQS**, is hosted but can have high latency and has the possibility of messages being delivered twice.
    - [**Link**](https://github.com/donnemartin/system-design-primer#task-queues)**Task queues**
    - Tasks queues receive tasks and their related data, runs them, then delivers their results. They can support scheduling and can be used to run computationally-intensive jobs in the background.
    - **Celery** has support for scheduling and primarily has python support.
    - [**Link**](https://github.com/donnemartin/system-design-primer#back-pressure)**Back pressure**
    - If queues start to grow significantly, the queue size can become larger than memory, resulting in cache misses, disk reads, and even slower performance. [Back pressure](http://mechanical-sympathy.blogspot.com/2012/05/apply-back-pressure-when-overloaded.html) can help by limiting the queue size, thereby maintaining a high throughput rate and good response times for jobs already in the queue. Once the queue fills up, clients get a server busy or HTTP 503 status code to try again later. Clients can retry the request at a later time, perhaps with [exponential backoff](https://en.wikipedia.org/wiki/Exponential_backoff).
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-asynchronism)**Disadvantage(s): asynchronism**>>>
        - Use cases such as inexpensive calculations and realtime workflows might be better suited for synchronous operations, as introducing queues can add delays and complexity.
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-11)**Source(s) and further reading**>>>
        - [It's all a numbers game](https://www.youtube.com/watch?v=1KRYH75wgy4)
        - [Applying back pressure when overloaded](http://mechanical-sympathy.blogspot.com/2012/05/apply-back-pressure-when-overloaded.html)
        - [Little's law](https://en.wikipedia.org/wiki/Little%27s_law)
        - [What is the difference between a message queue and a task queue?](https://www.quora.com/What-is-the-difference-between-a-message-queue-and-a-task-queue-Why-would-a-task-queue-require-a-message-broker-like-RabbitMQ-Redis-Celery-or-IronMQ-to-function)
    - [**Link**](https://github.com/donnemartin/system-design-primer#communication)
- **Communication**―[Link](https://camo.githubusercontent.com/1d761d5688d28ce1fb12a0f1c8191bca96eece4c/687474703a2f2f692e696d6775722e636f6d2f354b656f6351732e6a7067) 
[ __Source: OSI 7 layer model__ ](http://www.escotal.com/osilayer.html)
- **Hypertext transfer protocol (HTTP)**>>>
    - HTTP is a method for encoding and transporting data between a client and a server. It is a request/response protocol: clients issue requests and servers issue responses with relevant content and completion status info about the request. HTTP is self-contained, allowing requests and responses to flow through many intermediate routers and servers that perform load balancing, caching, encryption, and compression.
    - A basic HTTP request consists of a verb (method) and a resource (endpoint). Below are common HTTP verbs:
    - Verb>>>
        - Description
        - Idempotent*
        - Safe
        - Cacheable
    - GET>>>
        - Reads a resource
        - Yes
        - Yes
        - Yes
    - POST>>>
        - Creates a resource or trigger a process that handles data
        - No
        - No
        - Yes if response contains freshness info
    - PUT>>>
        - Creates or replace a resource
        - Yes
        - No
        - No
    - PATCH>>>
        - Partially updates a resource
        - No
        - No
        - Yes if response contains freshness info
    - DELETE>>>
        - Deletes a resource
        - Yes
        - No
        - No
    - *Can be called many times without different outcomes.
    - HTTP is an application layer protocol relying on lower-level protocols such as **TCP** and **UDP**.>>>
        - [HTTP](https://www.nginx.com/resources/glossary/http/)
        - [README](https://www.quora.com/What-is-the-difference-between-HTTP-protocol-and-TCP-protocol)
- **Transmission control protocol (TCP)**>>>
    - [Link](https://camo.githubusercontent.com/821620cf6aa83566f4def561e754e5991480ca8d/687474703a2f2f692e696d6775722e636f6d2f4a6441736476472e6a7067) 
[ __Source: How to make a multiplayer game__ ](http://www.wildbunny.co.uk/blog/2012/10/09/how-to-make-a-multi-player-game-part-1/)
    - TCP is a connection-oriented protocol over an [IP network](https://en.wikipedia.org/wiki/Internet_Protocol). Connection is established and terminated using a [handshake](https://en.wikipedia.org/wiki/Handshaking). All packets sent are guaranteed to reach the destination in the original order and without corruption through:>>>
        - Sequence numbers and [checksum fields](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#Checksum_computation) for each packet
        - [Acknowledgement](https://en.wikipedia.org/wiki/Acknowledgement_(data_networks)) packets and automatic retransmission
    - If the sender does not receive a correct response, it will resend the packets. If there are multiple timeouts, the connection is dropped. TCP also implements [flow control](https://en.wikipedia.org/wiki/Flow_control_(data)) and [congestion control](https://en.wikipedia.org/wiki/Network_congestion#Congestion_control). These guarantees cause delays and generally results in less efficient transmission than UDP.
    - To ensure high throughput, web servers can keep a large number of TCP connections open, resulting in high memory usage. It can be expensive to have a large number of open connections between web server threads and say, a [memcached](https://github.com/donnemartin/system-design-primer#memcached) server. [Connection pooling](https://en.wikipedia.org/wiki/Connection_pool) can help in addition to switching to UDP where applicable.
    - TCP is useful for applications that require high reliability but are less time critical. Some examples include web servers, database info, SMTP, FTP, and SSH.
    - Use TCP over UDP when:>>>
        - You need all of the data to arrive in tact
        - You want to automatically make a best estimate use of the network throughput
- **User datagram protocol (UDP)**>>>
    - [Link](https://camo.githubusercontent.com/47eb14c0a2dff2166f8781a6ce8c7f33d4c33da8/687474703a2f2f692e696d6775722e636f6d2f797a44724a74412e6a7067) 
[ __Source: How to make a multiplayer game__ ](http://www.wildbunny.co.uk/blog/2012/10/09/how-to-make-a-multi-player-game-part-1/)
    - UDP is connectionless. Datagrams (analogous to packets) are guaranteed only at the datagram level. Datagrams might reach their destination out of order or not at all. UDP does not support congestion control. Without the guarantees that TCP support, UDP is generally more efficient.
    - UDP can broadcast, sending datagrams to all devices on the subnet. This is useful with [DHCP](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) because the client has not yet received an IP address, thus preventing a way for TCP to stream without the IP address.
    - UDP is less reliable but works well in real time use cases such as VoIP, video chat, streaming, and realtime multiplayer games.
    - Use UDP over TCP when:>>>
        - You need the lowest latency
        - Late data is worse than loss of data
        - You want to implement your own error correction
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-tcp-and-udp)**Source(s) and further reading: TCP and UDP**>>>
        - [Networking for game programming](http://gafferongames.com/networking-for-game-programmers/udp-vs-tcp/)
        - [Key differences between TCP and UDP protocols](http://www.cyberciti.biz/faq/key-differences-between-tcp-and-udp-protocols/)
        - [Difference between TCP and UDP](http://stackoverflow.com/questions/5970383/difference-between-tcp-and-udp)
        - [Transmission control protocol](https://en.wikipedia.org/wiki/Transmission_Control_Protocol)
        - [User datagram protocol](https://en.wikipedia.org/wiki/User_Datagram_Protocol)
        - [Scaling memcache at Facebook](../../undefined.md)
- **Remote procedure call (RPC)**>>>
    - [Link](https://camo.githubusercontent.com/1a3d7771c0b0a7816d0533fffeb6eeeb442d9945/687474703a2f2f692e696d6775722e636f6d2f6946344d6b62352e706e67) 
[ __Source: Crack the system design interview__ ](http://www.puncsky.com/blog/2016/02/14/crack-the-system-design-interview/)
    - In an RPC, a client causes a procedure to execute on a different address space, usually a remote server. The procedure is coded as if it were a local procedure call, abstracting away the details of how to communicate with the server from the client program. Remote calls are usually slower and less reliable than local calls so it is helpful to distinguish RPC calls from local calls. Popular RPC frameworks include [Protobuf](https://developers.google.com/protocol-buffers/), [Thrift](https://thrift.apache.org/), and [Avro](https://avro.apache.org/docs/current/).
    - RPC is a request-response protocol:>>>
        - **Client program** - Calls the client stub procedure. The parameters are pushed onto the stack like a local procedure call.
        - **Client stub procedure** - Marshals (packs) procedure id and arguments into a request message.
        - **Client communication module** - OS sends the message from the client to the server.
        - **Server communication module** - OS passes the incoming packets to the server stub procedure.
        - **Server stub procedure** - Unmarshalls the results, calls the server procedure matching the procedure id and passes the given arguments.
        - The server response repeats the steps above in reverse order.
    - Sample RPC calls:
    - ```
GET /someoperation?data=anId

POST /anotheroperation
{
  "data":"anId";
  "anotherdata": "another value"
}
```
    - RPC is focused on exposing behaviors. RPCs are often used for performance reasons with internal communications, as you can hand-craft native calls to better fit your use cases.
    - Choose a Native Library aka SDK when:>>>
        - You know your target platform.
        - You want to control how your "logic" is accessed
        - You want to control how error control happens off your library
        - Performance and end user experience is your primary concern
    - HTTP APIs following **REST** tend to be used more often for public APIs.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-rpc)**Disadvantage(s): RPC**>>>
        - RPC clients become tightly coupled to the service implementation.
        - A new API must be definied for every new operation or use case.
        - It can be difficult to debug RPC.
        - You might not be able to leverage existing technologies out of the box. For example, it might require additional effort to ensure [RPC calls are properly cached](http://etherealbits.com/2012/12/debunking-the-myths-of-rpc-rest/) on caching servers such as [Squid](http://www.squid-cache.org/).
- **Representational state transfer (REST)**>>>
    - REST is an architectural style enforcing a client/server model where the client acts on a set of resources managed by the server. The server provides a representation of resources and actions that can either manipulate or get a new representation of resources. All communication must be stateless and cacheable.
    - There are four qualities of a RESTful interface:>>>
        - **Identify resources (URI in HTTP)** - use the same URI regardless of any operation.
        - **Change with representations (Verbs in HTTP)** - use verbs, headers, and body.
        - **Self-descriptive error message (status response in HTTP)** - Use status codes, don't reinvent the wheel.
        - [**HATEOAS**](http://restcookbook.com/Basics/hateoas/)** (HTML interface for HTTP)** - your web service should be fully accessible in a browser.
    - Sample REST calls:
    - ```
GET /someresources/anId

PUT /someresources/anId
{"anotherdata": "another value"}
```
    - REST is focused on exposing data. It minimizes the coupling between client/server and is often used for public HTTP APIs. REST uses a more generic and uniform method of exposing resources through URIs, [representation through headers](https://github.com/for-GET/know-your-http-well/blob/master/headers.md), and actions through verbs such as GET, POST, PUT, DELETE, and PATCH. Being stateless, REST is great for horizontal scaling and partitioning.
    - [**Link**](https://github.com/donnemartin/system-design-primer#disadvantages-rest)**Disadvantage(s): REST**>>>
        - With REST being focused on exposing data, it might not be a good fit if resources are not naturally organized or accessed in a simple hierarchy. For example, returning all updated records from the past hour matching a particular set of events is not easily expressed as a path. With REST, it is likely to be implemented with a combination of URI path, query parameters, and possibly the request body.
        - REST typically relies on a few verbs (GET, POST, PUT, DELETE, and PATCH) which sometimes doesn't fit your use case. For example, moving expired documents to the archive folder might not cleanly fit within these verbs.
- **Security**>>>
    - This section could use some updates. Consider [contributing](https://github.com/donnemartin/system-design-primer#contributing)!
    - Security is a broad topic. Unless you have considerable experience, a security background, or are applying for a position that requires knowledge of security, you probably won't need to know more than the basics:>>>
        - Encrypt in transit and at rest.
        - Sanitize all user inputs or any input parameters exposed to user to prevent [XSS](https://en.wikipedia.org/wiki/Cross-site_scripting) and [SQL injection](https://en.wikipedia.org/wiki/SQL_injection).
        - Use parameterized queries to prevent SQL injection.
        - Use the principle of [least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege).
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-12)**Source(s) and further reading**>>>
        - [Security guide for developers](https://github.com/FallibleInc/security-guide-for-developers)
        - [OWASP top ten](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet)
- **Powers of two table**>>>
    - **Powers of two table**
    - ```
Power           Exact Value         Approx Value        Bytes
---------------------------------------------------------------
7                             128
8                             256
10                           1024   1 thousand           1 KB
16                         65,536                       64 KB
20                      1,048,576   1 million            1 MB
30                  1,073,741,824   1 billion            1 GB
32                  4,294,967,296                        4 GB
40              1,099,511,627,776   1 trillion           1 TB
```
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-13)**Source(s) and further reading**>>>
        - [Powers of two](https://en.wikipedia.org/wiki/Power_of_two)
- **Latency numbers every programmer should know**>>>
    - ```
Latency Comparison Numbers
--------------------------
L1 cache reference                           0.5 ns
Branch mispredict                            5   ns
L2 cache reference                           7   ns                      14x L1 cache
Mutex lock/unlock                          100   ns
Main memory reference                      100   ns                      20x L2 cache, 200x L1 cache
Compress 1K bytes with Zippy            10,000   ns       10 us
Send 1 KB bytes over 1 Gbps network     10,000   ns       10 us
Read 4 KB randomly from SSD*           150,000   ns      150 us          ~1GB/sec SSD
Read 1 MB sequentially from memory     250,000   ns      250 us
Round trip within same datacenter      500,000   ns      500 us
Read 1 MB sequentially from SSD*     1,000,000   ns    1,000 us    1 ms  ~1GB/sec SSD, 4X memory
Disk seek                           10,000,000   ns   10,000 us   10 ms  20x datacenter roundtrip
Read 1 MB sequentially from 1 Gbps  10,000,000   ns   10,000 us   10 ms  40x memory, 10X SSD
Read 1 MB sequentially from disk    30,000,000   ns   30,000 us   30 ms 120x memory, 30X SSD
Send packet CA->Netherlands->CA    150,000,000   ns  150,000 us  150 ms

Notes
-----
1 ns = 10^-9 seconds
1 us = 10^-6 seconds = 1,000 ns
1 ms = 10^-3 seconds = 1,000 us = 1,000,000 ns
```
    - Handy metrics based on numbers above:>>>
        - Read sequentially from disk at 30 MB/s
        - Read sequentially from 1 Gbps Ethernet at 100 MB/s
        - Read sequentially from SSD at 1 GB/s
        - Read sequentially from main memory at 4 GB/s
        - 6-7 world-wide round trips per second
        - 2,000 round trips per second within a data center
    - [**Link**](https://github.com/donnemartin/system-design-primer#latency-numbers-visualized)**Latency numbers visualized**
    - [Link](https://camo.githubusercontent.com/77f72259e1eb58596b564d1ad823af1853bc60a3/687474703a2f2f692e696d6775722e636f6d2f6b307431652e706e67)
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-14)**Source(s) and further reading**>>>
        - [Latency numbers every programmer should know - 1](https://gist.github.com/jboner/2841832)
        - [Latency numbers every programmer should know - 2](https://gist.github.com/hellerbarde/2843375)
        - [Designs, lessons, and advice from building large distributed systems](../../undefined.md)
        - [Software Engineering Advice from Building Large-Scale Distributed Systems](../../undefined.md)
- MD5>>>
    - Widely used hashing function that produces a 128-bit hash value
    - Uniformly distributed
- Base 62>>>
    - Encodes to `[a-zA-Z0-9]` which works well for urls, eliminating the need for escaping special characters
    - Only one hash result for the original input and and the operation is deterministic (no randomness involved)
    - Base 64 is another popular encoding but provides issues for urls because of the additional `+` and `/` characters
- What is HATEOAS?>>>
    - What is HATEOAS and why is it important for my REST API?
    - HATEOAS stands for **Hypertext As The Engine Of Application State**. It means that hypertext should be used to find your way through the API. An example:
    - ```
GET /account/12345 HTTP/1.1  HTTP/1.1 200 OK <?xml version="1.0"?> <account>     <account_number>12345</account_number>     <balance currency="usd">100.00</balance>     <link rel="deposit" href="/account/12345/deposit" />     <link rel="withdraw" href="/account/12345/withdraw" />     <link rel="transfer" href="/account/12345/transfer" />     <link rel="close" href="/account/12345/close" /> </account>
```
    - Apart from the fact that we have 100 dollars (US) in our account, we can see 4 options: deposit more money, withdraw money, transfer money to another account, or close our account. The "link"-tags allows us to find out the URLs that are needed for the specified actions. Now, let's suppose we didn't have 100 usd in the bank, but we actually are in the red:
    - ```
GET /account/12345 HTTP/1.1  HTTP/1.1 200 OK <?xml version="1.0"?> <account>     <account_number>12345</account_number>     <balance currency="usd">-25.00</balance>     <link rel="deposit" href="/account/12345/deposit" /> </account>
```
    - Now we are 25 dollars in the red. Do you see that right now we have lost many of our options, and only depositing money is valid? As long as we are in the red, we cannot close our account, nor transfer or withdraw any money from the account. The hypertext is actually telling us what is allowed and what not: HATEOAS
- **RPC and REST calls comparison**>>>
    - Operation>>>
        - RPC
        - REST
    - Signup>>>
        - **POST** /signup
        - **POST** /persons
    - Resign>>>
        - **POST** /resign
{
"personid": "1234"
}
        - **DELETE** /persons/1234
    - Read a person>>>
        - **GET** /readPerson?personid=1234
        - **GET** /persons/1234
    - Read a person’s items list>>>
        - **GET** /readUsersItemsList?personid=1234
        - **GET** /persons/1234/items
    - Add an item to a person’s items>>>
        - **POST** /addItemToUsersItemsList
{
"personid": "1234";
"itemid": "456"
}
        - **POST** /persons/1234/items
{
"itemid": "456"
}
    - Update an item>>>
        - **POST** /modifyItem
{
"itemid": "456";
"key": "value"
}
        - **PUT** /items/456
{
"key": "value"
}
    - Delete an item>>>
        - **POST** /removeItem
{
"itemid": "456"
}
        - **DELETE** /items/456
        - [ __Source: Do you really know why you prefer REST over RPC__ ](https://apihandyman.io/do-you-really-know-why-you-prefer-rest-over-rpc/)
    - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-rest-and-rpc)**Source(s) and further reading: REST and RPC**>>>
        - [Do you really know why you prefer REST over RPC](https://apihandyman.io/do-you-really-know-why-you-prefer-rest-over-rpc/)
        - [When are RPC-ish approaches more appropriate than REST?](http://programmers.stackexchange.com/a/181186)
        - [REST vs JSON-RPC](http://stackoverflow.com/questions/15056878/rest-vs-json-rpc)
        - [Debunking the myths of RPC and REST](http://etherealbits.com/2012/12/debunking-the-myths-of-rpc-rest/)
        - [What are the drawbacks of using REST](https://www.quora.com/What-are-the-drawbacks-of-using-RESTful-APIs)
        - [Crack the system design interview](http://www.puncsky.com/blog/2016/02/14/crack-the-system-design-interview/)
        - [Thrift](https://code.facebook.com/posts/1468950976659943/)
        - [Why REST for internal use and not RPC](http://arstechnica.com/civis/viewtopic.php?t=1190508)
