- System Design
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
        - Some document stores like [MongoDB](https://www.mongodb.com/mongodb-architecture) and [CouchDB](https://blog.couchdb.org/2016/08/01/couchdb-2-0-architecture/) also provide a SQL-like language to perform complex queries.[DynamoDB](../undefined.md) supports both key-values and documents.
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
        - Google introduced [Bigtable](../undefined.md) as the first wide column store, which influenced the open-source [HBase](https://www.mapr.com/blog/in-depth-look-hbase-architecture) often-used in the Hadoop ecosystem, and [Cassandra](http://docs.datastax.com/en/archived/cassandra/2.0/cassandra/architecture/architectureIntro_c.html) from Facebook. Stores such as BigTable, HBase, and Cassandra maintain keys in lexicographic order, allowing efficient retrieval of selective key ranges.
        - Wide column stores offer high availability and high scalability. They are often used for very large data sets.
        - [**Link**](https://github.com/donnemartin/system-design-primer#sources-and-further-reading-wide-column-store)**Source(s) and further reading: wide column store**>>>
            - [SQL & NoSQL, a brief history](http://blog.grio.com/2015/11/sql-nosql-a-brief-history.html)
            - [Bigtable architecture](../undefined.md)
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
            - [Scaling memcache at Facebook](../undefined.md)
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
            - [Designs, lessons, and advice from building large distributed systems](../undefined.md)
            - [Software Engineering Advice from Building Large-Scale Distributed Systems](../undefined.md)
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
- OO Design
    - **Design a deck of cards**>>>
        - **Constraints and assumptions**>>>
            - Is this a generic deck of cards for games like poker and black jack?>>>
                - Yes, design a generic deck then extend it to black jack
            - Can we assume the deck has 52 cards (2-10, Jack, Queen, King, Ace) and 4 suits?>>>
                - Yes
            - Can we assume inputs are valid or do we have to validate them?>>>
                - Assume they're valid
        - **Solution**
        - %%writefile deck_of_cards.py
**from** **abc** **import** ABCMeta, abstractmethod
**from** **enum** **import** Enum
**import** **sys**


**class** **Suit**(Enum):

    HEART = 0
    DIAMOND = 1
    CLUBS = 2
    SPADE = 3


**class** **Card**(metaclass=ABCMeta):

    **def** __init__(self, value, suit):
        self.value = value
        self.suit = suit
        self.is_available = **True**

    @property
    @abstractmethod
    **def** value(self):
        **pass**

    @value.setter
    @abstractmethod
    **def** value(self, other):
        **pass**


**class** **BlackJackCard**(Card):

    **def** __init__(self, value, suit):
        super(BlackJackCard, self).__init__(value, suit)

    **def** is_ace(self):
        **return** **True** **if** self._value―1 **else** **False**

    **def** is_face_card(self):
        """Jack = 11, Queen = 12, King = 13"""
        **return** **True** **if** 10 < self._value <= 13 **else** **False**

    @property
    **def** value(self):
        **if** self.is_ace() ↔ 1:
            **return** 1
        **elif** self.is_face_card():
            **return** 10
        **else**:
            **return** self._value

    @value.setter
    **def** value(self, new_value):
        **if** 1 <= new_value <= 13:
            self._value = new_value
        **else**:
            **raise** **ValueError**('Invalid card value: **{}**'.format(new_value))


**class** **Hand**(object):

    **def** __init__(self, cards):
        self.cards = cards

    **def** add_card(self, card):
        self.cards.append(card)

    **def** score(self):
        total_value = 0
        **for** card **in** card:
            total_value += card.value
        **return** total_value


**class** **BlackJackHand**(Hand):

    BLACKJACK = 21

    **def** __init__(self, cards):
        super(BlackJackHand, self).__init__(cards)

    **def** score(self):
        min_over = sys.MAXSIZE
        max_under = -sys.MAXSIZE
        **for** score **in** self.possible_scores():
            **if** self.BLACKJACK < score < min_over:
                min_over = score
            **elif** max_under < score <= self.BLACKJACK:
                max_under = score
        **return** max_under **if** max_under != -sys.MAXSIZE **else** min_over

    **def** possible_scores(self):
        """Return a list of possible scores, taking Aces into account."""
        # ...


**class** **Deck**(object):

    **def** __init__(self, cards):
        self.cards = cards
        self.deal_index = 0

    **def** remaining_cards(self):
        **return** len(self.cards) - deal_index

    **def** deal_card():
        **try**:
            card = self.cards[self.deal_index]
            card.is_available = **False**
            self.deal_index += 1
        **except** **IndexError**:
            **return** **None**
        **return** card

    **def** shuffle(self):  # ...
    - **Design a call center**>>>
        - **Constraints and assumptions**>>>
            - What levels of employees are in the call center?>>>
                - Operator, supervisor, director
            - Can we assume operators always get the initial calls?>>>
                - Yes
            - If there is no free operators or the operator can't handle the call, does the call go to the supervisors?>>>
                - Yes
            - If there is no free supervisors or the supervisor can't handle the call, does the call go to the directors?>>>
                - Yes
            - Can we assume the directors can handle all calls?>>>
                - Yes
            - What happens if nobody can answer the call?>>>
                - It gets queued
            - Do we need to handle 'VIP' calls where we put someone to the front of the line?>>>
                - No
            - Can we assume inputs are valid or do we have to validate them?>>>
                - Assume they're valid
        - **Solution**
        - %%writefile call_center.py
**from** **abc** **import** ABCMeta, abstractmethod
**from** **collections** **import** deque
**from** **enum** **import** Enum


**class** **Rank**(Enum):

    OPERATOR = 0
    SUPERVISOR = 1
    DIRECTOR = 2


**class** **Employee**(metaclass=ABCMeta):

    **def** __init__(self, employee_id, name, rank, call_center):
        self.employee_id = employee_id
        self.name = name
        self.rank = rank
        self.call = **None**
        self.call_center = call_center

    **def** take_call(self, call):
        """Assume the employee will always successfully take the call."""
        self.call = call
        self.call.employee = self
        self.call.state = CallState.IN_PROGRESS

    **def** complete_call(self):
        self.call.state = CallState.COMPLETE
        self.call_center.notify_call_completed(self.call)

    @abstractmethod
    **def** escalate_call(self):
        **pass**

    **def** _escalate_call(self):
        self.call.state = CallState.READY
        call = self.call
        self.call = **None**
        self.call_center.notify_call_escalated(call)


**class** **Operator**(Employee):

    **def** __init__(self, employee_id, name):
        super(Operator, self).__init__(employee_id, name, Rank.OPERATOR)

    **def** escalate_call(self):
        self.call.level = Rank.SUPERVISOR
        self._escalate_call()


**class** **Supervisor**(Employee):

    **def** __init__(self, employee_id, name):
        super(Operator, self).__init__(employee_id, name, Rank.SUPERVISOR)

    **def** escalate_call(self):
        self.call.level = Rank.DIRECTOR
        self._escalate_call()


**class** **Director**(Employee):

    **def** __init__(self, employee_id, name):
        super(Operator, self).__init__(employee_id, name, Rank.DIRECTOR)

    **def** escalate_call(self):
        **raise** NotImplemented('Directors must be able to handle any call')


**class** **CallState**(Enum):

    READY = 0
    IN_PROGRESS = 1
    COMPLETE = 2


**class** **Call**(object):

    **def** __init__(self, rank):
        self.state = CallState.READY
        self.rank = rank
        self.employee = **None**


**class** **CallCenter**(object):

    **def** __init__(self, operators, supervisors, directors):
        self.operators = operators
        self.supervisors = supervisors
        self.directors = directors
        self.queued_calls = deque()

    **def** dispatch_call(self, call):
        **if** call.rank **not** **in** (Rank.OPERATOR, Rank.SUPERVISOR, Rank.DIRECTOR):
            **raise** **ValueError**('Invalid call rank: **{}**'.format(call.rank))
        employee = **None**
        **if** call.rank―Rank.OPERATOR:
            employee = self._dispatch_call(call, self.operators)
        **if** call.rank ↔ Rank.SUPERVISOR **or** employee **is** **None**:
            employee = self._dispatch_call(call, self.supervisors)
        **if** call.rank ↔ Rank.DIRECTOR **or** employee **is** **None**:
            employee = self._dispatch_call(call, self.directors)
        **if** employee **is** **None**:
            self.queued_calls.append(call)

    **def** _dispatch_call(self, call, employees):
        **for** employee **in** employees:
            **if** employee.call **is** **None**:
                employee.take_call(call)
                **return** employee
        **return** **None**

    **def** notify_call_escalated(self, call):  # ...
    **def** notify_call_completed(self, call):  # ...
    **def** dispatch_queued_call_to_newly_freed_employee(self, call, employee):  # ...
    - **Design a hash map**>>>
        - **Constraints and assumptions**>>>
            - For simplicity, are the keys integers only?>>>
                - Yes
            - For collision resolution, can we use chaining?>>>
                - Yes
            - Do we have to worry about load factors?>>>
                - No
            - Can we assume inputs are valid or do we have to validate them?>>>
                - Assume they're valid
            - Can we assume this fits memory?>>>
                - Yes
        - **Solution**
        - %%writefile hash_map.py
**class** **Item**(object):

    **def** __init__(self, key, value):
        self.key = key
        self.value = value


**class** **HashTable**(object):

    **def** __init__(self, size):
        self.size = size
        self.table = [[] _  _**for**_  _ _ **in** range(self.size)]

    **def** _hash_function(self, key):
        **return** key % self.size

    **def** set(self, key, value):
        hash_index = self._hash_function(key)
        **for** item **in** self.table[hash_index]:
            **if** item.key―key:
                item.value = value
                **return**
        self.table[hash_index].append(Item(key, value))

    **def** get(self, key):
        hash_index = self._hash_function(key)
        **for** item **in** self.table[hash_index]:
            **if** item.key ↔ key:
                **return** item.value
        **raise** **KeyError**('Key not found')

    **def** remove(self, key):
        hash_index = self._hash_function(key)
        **for** index, item **in** enumerate(self.table[hash_index]):
            **if** item.key ↔ key:
                **del** self.table[hash_index][index]
                **return**
        **raise** **KeyError**('Key not found')
    - **Design an LRU cache**>>>
        - **Constraints and assumptions**>>>
            - What are we caching?>>>
                - We are cahing the results of web queries
            - Can we assume inputs are valid or do we have to validate them?>>>
                - Assume they're valid
            - Can we assume this fits memory?>>>
                - Yes
        - **Solution**
        - %%writefile lru_cache.py
**class** **Node**(object):

    **def** __init__(self, results):
        self.results = results
        self.next = next


**class** **LinkedList**(object):

    **def** __init__(self):
        self.head = **None**
        self.tail = **None**

    **def** move_to_front(self, node):  # ...
    **def** append_to_front(self, node):  # ...
    **def** remove_from_tail(self):  # ...


**class** **Cache**(object):

    **def** __init__(self, MAX_SIZE):
        self.MAX_SIZE = MAX_SIZE
        self.size = 0
        self.lookup = {}  # key: query, value: node
        self.linked_list = LinkedList()

    **def** get(self, query)
        """Get the stored query result from the cache.
        
        Accessing a node updates its position to the front of the LRU list.
        """
        node = self.lookup[query]
        **if** node **is** **None**:
            **return** **None**
        self.linked_list.move_to_front(node)
        **return** node.results

    **def** set(self, results, query):
        """Set the result for the given query key in the cache.
        
        When updating an entry, updates its position to the front of the LRU list.
        If the entry is new and the cache is at capacity, removes the oldest entry
        before the new entry is added.
        """
        node = self.lookup[query]
        **if** node **is** **not** **None**:
            # Key exists in cache, update the value
            node.results = results
            self.linked_list.move_to_front(node)
        **else**:
            # Key does not exist in cache
            **if** self.size―self.MAX_SIZE:
                # Remove the oldest entry from the linked list and lookup
                self.lookup.pop(self.linked_list.tail.query, **None**)
                self.linked_list.remove_from_tail()
            **else**:
                self.size += 1
            # Add the new key and value
            new_node = Node(results)
            self.linked_list.append_to_front(new_node)
            self.lookup[query] = new_node
    - **Design an online chat**>>>
        - **Constraints and assumptions**>>>
            - Assume we'll focus on the following workflows:>>>
                - Text conversations only
                - Users>>>
                    - Add a user
                    - Remove a user
                    - Update a user
                    - Add to a user's friends list>>>
                        - Add friend request>>>
                            - Approve friend request
                            - Reject friend request
                    - Remove from a user's friends list
                - Create a group chat>>>
                    - Invite friends to a group chat
                    - Post a message to a group chat
                - Private 1-1 chat>>>
                    - Invite a friend to a private chat
                    - Post a meesage to a private chat
            - No need to worry about scaling initially
        - 
        - **Solution**
        - In [1]:
        - %%writefile online_chat.py
**from** **abc** **import** ABCMeta


**class** **UserService**(object):

    **def** __init__(self):
        self.users_by_id = {}  # key: user id, value: User

    **def** add_user(self, user_id, name, pass_hash):  # ...
    **def** remove_user(self, user_id):  # ...
    **def** add_friend_request(self, from_user_id, to_user_id):  # ...
    **def** approve_friend_request(self, from_user_id, to_user_id):  # ...
    **def** reject_friend_request(self, from_user_id, to_user_id):  # ...


**class** **User**(object):

    **def** __init__(self, user_id, name, pass_hash):
        self.user_id = user_id
        self.name = name
        self.pass_hash = pass_hash
        self.friends_by_id = {}  # key: friend id, value: User
        self.friend_ids_to_private_chats = {}  # key: friend id, value: private chats
        self.group_chats_by_id = {}  # key: chat id, value: GroupChat
        self.received_friend_requests_by_friend_id = {}  # key: friend id, value: AddRequest
        self.sent_friend_requests_by_friend_id = {}  # key: friend id, value: AddRequest

    **def** message_user(self, friend_id, message):  # ...
    **def** message_group(self, group_id, message):  # ...
    **def** send_friend_request(self, friend_id):  # ...
    **def** receive_friend_request(self, friend_id):  # ...
    **def** approve_friend_request(self, friend_id):  # ...
    **def** reject_friend_request(self, friend_id):  # ...


**class** **Chat**(metaclass=ABCMeta):

    **def** __init__(self, chat_id):
        self.chat_id = chat_id
        self.users = []
        self.messages = []


**class** **PrivateChat**(Chat):

    **def** __init__(self, first_user, second_user):
        super(PrivateChat, self).__init__()
        self.users.append(first_user)
        self.users.append(second_user)


**class** **GroupChat**(Chat):

    **def** add_user(self, user):  # ...
    **def** remove_user(self, user):  # ... 


**class** **Message**(object):

    **def** __init__(self, message_id, message, timestamp):
        self.message_id = message_id
        self.message = message
        self.timestamp = timestamp


**class** **AddRequest**(object):

    **def** __init__(self, from_user_id, to_user_id, request_status, timestamp):
        self.from_user_id = from_user_id
        self.to_user_id = to_user_id
        self.request_status = request_status
        self.timestamp = timestamp


**class** **RequestStatus**(Enum):

    UNREAD = 0
    READ = 1
    ACCEPTED = 2
    REJECTED = 3
    - **Design a parking lot**>>>
        - **Constraints and assumptions**>>>
            - What types of vehicles should we support?>>>
                - Motorcycle, Car, Bus
            - Does each vehicle type take up a different amount of parking spots?>>>
                - Yes
                - Motorcycle spot -> Motorcycle
                - Compact spot -> Motorcycle, Car
                - Large spot -> Motorcycle, Car
                - Bus can park if we have 5 consecutive "large" spots
            - Does the parking lot have multiple levels?>>>
                - Yes
        - **Solution**
        - %%writefile parking_lot.py
**from** **abc** **import** ABCMeta, abstractmethod


**class** **VehicleSize**(Enum):

    MOTORCYCLE = 0
    COMPACT = 1
    LARGE = 2


**class** **Vehicle**(metaclass=ABCMeta):

    **def** __init__(self, vehicle_size, license_plate, spot_size):
        self.vehicle_size = vehicle_size
        self.license_plate = license_plate
        self.spot_size
        self.spots_taken = []

    **def** clear_spots(self):
        **for** spot **in** self.spots_taken:
            spot.remove_vehicle(self)
        self.spots_taken = []

    **def** take_spot(self, spot):
        self.spots_taken.append(spot)

    @abstractmethod
    **def** can_fit_in_spot(self, spot):
        **pass**


**class** **Motorcycle**(Vehicle):

    **def** __init__(self, license_plate):
        super(Motorcycle, self).__init__(VehicleSize.MOTORCYCLE, license_plate, spot_size=1)

    **def** can_fit_in_spot(self, spot):
        **return** **True**


**class** **Car**(Vehicle):

    **def** __init__(self, license_plate):
        super(Car, self).__init__(VehicleSize.COMPACT, license_plate, spot_size=1)

    **def** can_fit_in_spot(self, spot):
        **return** **True** **if** (spot.size―LARGE **or** spot.size ↔ COMPACT) **else** **False**


**class** **Bus**(Vehicle):

    **def** __init__(self, license_plate):
        super(Bus, self).__init__(VehicleSize.LARGE, license_plate, spot_size=5)

    **def** can_fit_in_spot(self, spot):
        **return** **True** **if** spot.size ↔ LARGE **else** **False**


**class** **ParkingLot**(object):

    **def** __init__(self, num_levels):
        self.num_levels = num_levels
        self.levels = []

    **def** park_vehicle(self, vehicle):
        **for** level **in** levels:
            **if** level.park_vehicle(vehicle):
                **return** **True**
        **return** **False**


**class** **Level**(object):

    SPOTS_PER_ROW = 10

    **def** __init__(self, floor, total_spots):
        self.floor = floor
        self.num_spots = total_spots
        self.available_spots = 0
        self.parking_spots = []

    **def** spot_freed(self):
        self.available_spots += 1

    **def** park_vehicle(self, vehicle):
        spot = self._find_available_spot(vehicle)
        **if** spot **is** **None**:
            **return** **None**
        **else**:
            spot.park_vehicle(vehicle)
            **return** spot

    **def** _find_available_spot(self, vehicle):
        """Find an available spot where vehicle can fit, or return None"""
        # ...

    **def** _park_starting_at_spot(self, spot, vehicle):
        """Occupy starting at spot.spot_number to vehicle.spot_size."""
        # ...


**class** **ParkingSpot**(object):

    **def** __init__(self, level, row, spot_number, spot_size, vehicle_size):
        self.level = level
        self.row = row
        self.spot_number = spot_number
        self.spot_size = spot_size
        self.vehicle_size = vehicle_size
        self.vehicle = **None**

    **def** is_available(self):
        **return** **True** **if** self.vehicle **is** **None** **else** **False**

    **def** can_fit_vehicle(self, vehicle):
        **if** self.vehicle **is** **not** **None**:
            **return** **False**
        **return** vehicle.can_fit_in_spot(self)

    **def** park_vehicle(self, vehicle):  # ...
    **def** remove_vehicle(self):  # ...
- System Design Exercises
    - **Design Mint.com**>>>
        -  __Note: This document links directly to relevant areas found in the __ [ __system design topics__ ](https://github.com/donnemartin/system-design-primer-interview#index-of-system-design-topics-1) __ to avoid duplication. Refer to the linked content for general talking points, tradeoffs, and alternatives.__ 
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#step-1-outline-use-cases-and-constraints)**Step 1: Outline use cases and constraints**
        - Gather requirements and scope the problem. Ask questions to clarify use cases and constraints. Discuss assumptions.
        - Without an interviewer to address clarifying questions, we'll define some use cases and constraints.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#use-cases)**Use cases**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#well-scope-the-problem-to-handle-only-the-following-use-cases)**We'll scope the problem to handle only the following use cases**>>>
            - **User** connects to a financial account
            - **Service** extracts transactions from the account>>>
                - Updates daily
                - Categorizes transactions>>>
                    - Allows manual category override by the user
                    - No automatic re-categorization
                - Analyzes monthly spending, by category
            - **Service** recommends a budget>>>
                - Allows users to manually set a budget
                - Sends notifications when approaching or exceeding budget
            - **Service** has high availability
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#out-of-scope)**Out of scope**>>>
            - **Service** performs additional logging and analytics
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#constraints-and-assumptions)**Constraints and assumptions**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#state-assumptions)**State assumptions**>>>
            - Traffic is not evenly distributed
            - Automatic daily update of accounts applies only to users active in the past 30 days
            - Adding or removing financial accounts is relatively rare
            - Budget notifications don't need to be instant
            - 10 million users>>>
                - 10 budget categories per user = 100 million budget items
                - Example categories:>>>
                    - Housing = $1,000
                    - Food = $200
                    - Gas = $100
                - Sellers are used to determine transaction category>>>
                    - 50,000 sellers
            - 30 million financial accounts
            - 5 billion transactions per month
            - 500 million read requests per month
            - 10:1 write to read ratio>>>
                - Write-heavy, users make transactions daily, but few visit the site daily
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#calculate-usage)**Calculate usage**
        - **Clarify with your interviewer if you should run back-of-the-envelope usage calculations.**>>>
            - Size per transaction:>>>
                - `user_id` - 8 bytes
                - `created_at` - 5 bytes
                - `seller` - 32 bytes
                - `amount` - 5 bytes
                - Total: ~50 bytes
            - 250 GB of new transaction content per month>>>
                - 50 bytes per transaction * 5 billion transactions per month
                - 9 TB of new transaction content in 3 years
                - Assume most are new transactions instead of updates to existing ones
            - 2,000 transactions per second on average
            - 200 read requests per second on average
        - Handy conversion guide:>>>
            - 2.5 million seconds per month
            - 1 request per second = 2.5 million requests per month
            - 40 requests per second = 100 million requests per month
            - 400 requests per second = 1 billion requests per month
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#step-2-create-a-high-level-design)**Step 2: Create a high level design**
        - Outline a high level design with all important components.
        - [Link](https://camo.githubusercontent.com/a1129136225c67ce2a92f4b15a7a27653b6257b1/687474703a2f2f692e696d6775722e636f6d2f45386b6c7242682e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#step-3-design-core-components)**Step 3: Design core components**
        - Dive into details for each core component.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#use-case-user-connects-to-a-financial-account)**Use case: User connects to a financial account**
        - We could store info on the 10 million users in a [relational database](https://github.com/donnemartin/system-design-primer-interview#relational-database-management-system-rdbms). We should discuss the [use cases and tradeoffs between choosing SQL or NoSQL](https://github.com/donnemartin/system-design-primer-interview#sql-or-nosql).>>>
            - The **Client** sends a request to the **Web Server**, running as a [reverse proxy](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
            - The **Web Server** forwards the request to the **Accounts API** server
            - The **Accounts API** server updates the **SQL Database** `accounts` table with the newly entered account info
        - **Clarify with your interviewer how much code you are expected to write**.
        - The `accounts` table could have the following structure:
        - ```
id int NOT NULL AUTO_INCREMENT
created_at datetime NOT NULL
last_update datetime NOT NULL
account_url varchar(255) NOT NULL
account_login varchar(32) NOT NULL
account_password_hash char(64) NOT NULL
user_id int NOT NULL
PRIMARY KEY(id)
FOREIGN KEY(user_id) REFERENCES users(id)
```
        - We'll create an [index](https://github.com/donnemartin/system-design-primer-interview#use-good-indices) on `id`, `user_id`, and `created_at` to speed up lookups (log-time instead of scanning the entire table) and to keep the data in memory. Reading 1 MB sequentially from memory takes about 250 microseconds, while reading from SSD takes 4x and from disk takes 80x longer.[1](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know)
        - We'll use a public [**REST API**](https://github.com/donnemartin/system-design-primer-interview##representational-state-transfer-rest):
        - ```
$ curl -X POST --data '{ "user_id": "foo", "account_url": "bar", \
    "account_login": "baz", "account_password": "qux" }' \
    https://mint.com/api/v1/account
```
        - For internal communications, we could use [Remote Procedure Calls](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc).
        - Next, the service extracts transactions from the account.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#use-case-service-extracts-transactions-from-the-account)**Use case: Service extracts transactions from the account**
        - We'll want to extract information from an account in these cases:>>>
            - The user first links the account
            - The user manually refreshes the account
            - Automatically each day for users who have been active in the past 30 days
        - Data flow:>>>
            - The **Client** sends a request to the **Web Server**
            - The **Web Server** forwards the request to the **Accounts API** server
            - The **Accounts API** server places a job on a **Queue** such as Amazon SQS or [RabbitMQ](https://www.rabbitmq.com/)>>>
                - Extracting transactions could take awhile, we'd probably want to do this [asynchronously with a queue](https://github.com/donnemartin/system-design-primer-interview#asynchronism), although this introduces additional complexity
            - The **Transaction Extraction Service** does the following:>>>
                - Pulls from the **Queue** and extracts transactions for the given account from the financial institution, storing the results as raw log files in the **Object Store**
                - Uses the **Category Service** to categorize each transaction
                - Uses the **Budget Service** to calculate aggregate monthly spending by category>>>
                    - The **Budget Service** uses the **Notification Service** to let users know if they are nearing or have exceeded their budget
                - Updates the **SQL Database** `transactions` table with categorized transactions
                - Updates the **SQL Database** `monthly_spending` table with aggregate monthly spending by category
                - Notifies the user the transactions have completed through the **Notification Service**:>>>
                    - Uses a **Queue** (not pictured) to asynchronously send out notifications
        - The `transactions` table could have the following structure:
        - ```
id int NOT NULL AUTO_INCREMENT
created_at datetime NOT NULL
seller varchar(32) NOT NULL
amount decimal NOT NULL
user_id int NOT NULL
PRIMARY KEY(id)
FOREIGN KEY(user_id) REFERENCES users(id)
```
        - We'll create an [index](https://github.com/donnemartin/system-design-primer-interview#use-good-indices) on `id`, `user_id`, and `created_at`.
        - The `monthly_spending` table could have the following structure:
        - ```
id int NOT NULL AUTO_INCREMENT
month_year date NOT NULL
category varchar(32)
amount decimal NOT NULL
user_id int NOT NULL
PRIMARY KEY(id)
FOREIGN KEY(user_id) REFERENCES users(id)
```
        - We'll create an [index](https://github.com/donnemartin/system-design-primer-interview#use-good-indices) on `id` and `user_id`.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#category-service)**Category service**
        - For the **Category Service**, we can seed a seller-to-category dictionary with the most popular sellers. If we estimate 50,000 sellers and estimate each entry to take less than 255 bytes, the dictionary would only take about 12 MB of memory.
        - **Clarify with your interviewer how much code you are expected to write**.
        - ```
class DefaultCategories(Enum):

    HOUSING = 0
    FOOD = 1
    GAS = 2
    SHOPPING = 3
    ...

seller_category_map = {}
seller_category_map['Exxon'] = DefaultCategories.GAS
seller_category_map['Target'] = DefaultCategories.SHOPPING
...
```
        - For sellers not initially seeded in the map, we could use a crowdsourcing effort by evaluating the manual category overrides our users provide. We could use a heap to quickly lookup the top manual override per seller in O(1) time.
        - ```
class Categorizer(object):

    def __init__(self, seller_category_map, self.seller_category_crowd_overrides_map):
        self.seller_category_map = seller_category_map
        self.seller_category_crowd_overrides_map = \
            seller_category_crowd_overrides_map

    def categorize(self, transaction):
        if transaction.seller in self.seller_category_map:
            return self.seller_category_map[transaction.seller]
        elif transaction.seller in self.seller_category_crowd_overrides_map:
            self.seller_category_map[transaction.seller] = \
                self.seller_category_crowd_overrides_map[transaction.seller].peek_min()
            return self.seller_category_map[transaction.seller]
        return None
```
        - Transaction implementation:
        - ```
class Transaction(object):

    def __init__(self, created_at, seller, amount):
        self.timestamp = timestamp
        self.seller = seller
        self.amount = amount
```
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#use-case-service-recommends-a-budget)**Use case: Service recommends a budget**
        - To start, we could use a generic budget template that allocates category amounts based on income tiers. Using this approach, we would not have to store the 100 million budget items identified in the constraints, only those that the user overrides. If a user overrides a budget category, which we could store the override in the `TABLE budget_overrides`.
        - ```
class Budget(object):

    def __init__(self, income):
        self.income = income
        self.categories_to_budget_map = self.create_budget_template()

    def create_budget_template(self):
        return {
            'DefaultCategories.HOUSING': income * .4,
            'DefaultCategories.FOOD': income * .2
            'DefaultCategories.GAS': income * .1,
            'DefaultCategories.SHOPPING': income * .2
            ...
        }

    def override_category_budget(self, category, amount):
        self.categories_to_budget_map[category] = amount
```
        - For the **Budget Service**, we can potentially run SQL queries on the `transactions` table to generate the `monthly_spending` aggregate table. The `monthly_spending` table would likely have much fewer rows than the total 5 billion transactions, since users typically have many transactions per month.
        - As an alternative, we can run **MapReduce** jobs on the raw transaction files to:>>>
            - Categorize each transaction
            - Generate aggregate monthly spending by category
        - Running analyses on the transaction files could significantly reduce the load on the database.
        - We could call the **Budget Service** to re-run the analysis if the user updates a category.
        - **Clarify with your interviewer how much code you are expected to write**.
        - Sample log file format, tab delimited:
        - ```
user_id   timestamp   seller  amount
```
        - **MapReduce** implementation:
        - ```
class SpendingByCategory(MRJob):

    def __init__(self, categorizer):
        self.categorizer = categorizer
        self.current_year_month = calc_current_year_month()
        ...

    def calc_current_year_month(self):
        """Return the current year and month."""
        ...

    def extract_year_month(self, timestamp):
        """Return the year and month portions of the timestamp."""
        ...

    def handle_budget_notifications(self, key, total):
        """Call notification API if nearing or exceeded budget."""
        ...

    def mapper(self, _, line):
        """Parse each log line, extract and transform relevant lines.

        Argument line will be of the form:

        user_id   timestamp   seller  amount

        Using the categorizer to convert seller to category,
        emit key value pairs of the form:

        (user_id, 2016-01, shopping), 25
        (user_id, 2016-01, shopping), 100
        (user_id, 2016-01, gas), 50
        """
        user_id, timestamp, seller, amount = line.split('\t')
        category = self.categorizer.categorize(seller)
        period = self.extract_year_month(timestamp)
        if period == self.current_year_month:
            yield (user_id, period, category), amount

    def reducer(self, key, value):
        """Sum values for each key.

        (user_id, 2016-01, shopping), 125
        (user_id, 2016-01, gas), 50
        """
        total = sum(values)
        yield key, sum(values)
```
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#step-4-scale-the-design)**Step 4: Scale the design**
        - Identify and address bottlenecks, given the constraints.
        - [Link](https://camo.githubusercontent.com/12fea5f9324f74189a9cd983b02239c68615b67e/687474703a2f2f692e696d6775722e636f6d2f563571353776552e706e67)
        - **Important: Do not simply jump right into the final design from the initial design!**
        - State you would 1) **Benchmark/Load Test**, 2) **Profile** for bottlenecks 3) address bottlenecks while evaluating alternatives and trade-offs, and 4) repeat. See [Design a system that scales to millions of users on AWS](https://github.com/donnemartin/system-design-primer/blob/master/solutions/system_design/mint) as a sample on how to iteratively scale the initial design.
        - It's important to discuss what bottlenecks you might encounter with the initial design and how you might address each of them. For example, what issues are addressed by adding a **Load Balancer** with multiple **Web Servers**? **CDN**?**Master-Slave Replicas**? What are the alternatives and **Trade-Offs** for each?
        - We'll introduce some components to complete the design and to address scalability issues. Internal load balancers are not shown to reduce clutter.>>>
            -  __To avoid repeating discussions__ , refer to the following [system design topics](https://github.com/donnemartin/system-design-primer-interview#) for main talking points, tradeoffs, and alternatives:>>>
                - [DNS](https://github.com/donnemartin/system-design-primer-interview#domain-name-system)
                - [CDN](https://github.com/donnemartin/system-design-primer-interview#content-delivery-network)
                - [Load balancer](https://github.com/donnemartin/system-design-primer-interview#load-balancer)
                - [Horizontal scaling](https://github.com/donnemartin/system-design-primer-interview#horizontal-scaling)
                - [Web server (reverse proxy)](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
                - [API server (application layer)](https://github.com/donnemartin/system-design-primer-interview#application-layer)
                - [Cache](https://github.com/donnemartin/system-design-primer-interview#cache)
                - [Relational database management system (RDBMS)](https://github.com/donnemartin/system-design-primer-interview#relational-database-management-system-rdbms)
                - [SQL write master-slave failover](https://github.com/donnemartin/system-design-primer-interview#fail-over)
                - [Master-slave replication](https://github.com/donnemartin/system-design-primer-interview#master-slave-replication)
                - [Asynchronism](https://github.com/donnemartin/system-design-primer-interview#aysnchronism)
                - [Consistency patterns](https://github.com/donnemartin/system-design-primer-interview#consistency-patterns)
                - [Availability patterns](https://github.com/donnemartin/system-design-primer-interview#availability-patterns)
        - We'll add an additional use case: **User** accesses summaries and transactions.
        - User sessions, aggregate stats by category, and recent transactions could be placed in a **Memory Cache** such as Redis or Memcached.>>>
            - The **Client** sends a read request to the **Web Server**
            - The **Web Server** forwards the request to the **Read API** server>>>
                - Static content can be served from the **Object Store** such as S3, which is cached on the **CDN**
            - The **Read API** server does the following:>>>
                - Checks the **Memory Cache** for the content>>>
                    - If the url is in the **Memory Cache**, returns the cached contents
                    - Else>>>
                        - If the url is in the **SQL Database**, fetches the contents>>>
                            - Updates the **Memory Cache** with the contents
        - Refer to [When to update the cache](https://github.com/donnemartin/system-design-primer-interview#when-to-update-the-cache) for tradeoffs and alternatives. The approach above describes [cache-aside](https://github.com/donnemartin/system-design-primer-interview#cache-aside).
        - Instead of keeping the `monthly_spending` aggregate table in the **SQL Database**, we could create a separate **Analytics Database** using a data warehousing solution such as Amazon Redshift or Google BigQuery.
        - We might only want to store a month of `transactions` data in the database, while storing the rest in a data warehouse or in an **Object Store**. An **Object Store** such as Amazon S3 can comfortably handle the constraint of 250 GB of new content per month.
        - To address the 2,000  __average__  read requests per second (higher at peak), traffic for popular content should be handled by the **Memory Cache** instead of the database. The **Memory Cache** is also useful for handling the unevenly distributed traffic and traffic spikes. The **SQL Read Replicas** should be able to handle the cache misses, as long as the replicas are not bogged down with replicating writes.
        - 200  __average__  transaction writes per second (higher at peak) might be tough for a single **SQL Write Master-Slave**. We might need to employ additional SQL scaling patterns:>>>
            - [Federation](https://github.com/donnemartin/system-design-primer-interview#federation)
            - [Sharding](https://github.com/donnemartin/system-design-primer-interview#sharding)
            - [Denormalization](https://github.com/donnemartin/system-design-primer-interview#denormalization)
            - [SQL Tuning](https://github.com/donnemartin/system-design-primer-interview#sql-tuning)
        - We should also consider moving some data to a **NoSQL Database**.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#additional-talking-points)**Additional talking points**
        - Additional topics to dive into, depending on the problem scope and time remaining.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#nosql)**NoSQL**>>>
            - [Key-value store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Document store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Wide column store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Graph database](https://github.com/donnemartin/system-design-primer-interview#)
            - [SQL vs NoSQL](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#caching)**Caching**>>>
            - Where to cache>>>
                - [Client caching](https://github.com/donnemartin/system-design-primer-interview#client-caching)
                - [CDN caching](https://github.com/donnemartin/system-design-primer-interview#cdn-caching)
                - [Web server caching](https://github.com/donnemartin/system-design-primer-interview#web-server-caching)
                - [Database caching](https://github.com/donnemartin/system-design-primer-interview#database-caching)
                - [Application caching](https://github.com/donnemartin/system-design-primer-interview#application-caching)
            - What to cache>>>
                - [Caching at the database query level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-database-query-level)
                - [Caching at the object level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-object-level)
            - When to update the cache>>>
                - [Cache-aside](https://github.com/donnemartin/system-design-primer-interview#cache-aside)
                - [Write-through](https://github.com/donnemartin/system-design-primer-interview#write-through)
                - [Write-behind (write-back)](https://github.com/donnemartin/system-design-primer-interview#write-behind-write-back)
                - [Refresh ahead](https://github.com/donnemartin/system-design-primer-interview#refresh-ahead)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#asynchronism-and-microservices)**Asynchronism and microservices**>>>
            - [Message queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Task queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Back pressure](https://github.com/donnemartin/system-design-primer-interview#)
            - [Microservices](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#communications)**Communications**>>>
            - Discuss tradeoffs:>>>
                - External communication with clients - [HTTP APIs following REST](https://github.com/donnemartin/system-design-primer-interview#representational-state-transfer-rest)
                - Internal communications - [RPC](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc)
            - [Service discovery](https://github.com/donnemartin/system-design-primer-interview#service-discovery)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#security)**Security**
        - Refer to the [security section](https://github.com/donnemartin/system-design-primer-interview#security).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#latency-numbers)**Latency numbers**
        - See [Latency numbers every programmer should know](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/mint#ongoing)**Ongoing**>>>
            - Continue benchmarking and monitoring your system to address bottlenecks as they come up
            - Scaling is an iterative process
    - **Design Pastebin.com (or Bit.ly)**>>>
        -  __Note: This document links directly to relevant areas found in the __ [ __system design topics__ ](https://github.com/donnemartin/system-design-primer-interview#index-of-system-design-topics-1) __ to avoid duplication. Refer to the linked content for general talking points, tradeoffs, and alternatives.__ 
        - **Design Bit.ly** - is a similar question, except pastebin requires storing the paste contents instead of the original unshortened url.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#step-1-outline-use-cases-and-constraints)**Step 1: Outline use cases and constraints**
        - Gather requirements and scope the problem. Ask questions to clarify use cases and constraints. Discuss assumptions.
        - Without an interviewer to address clarifying questions, we'll define some use cases and constraints.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#use-cases)**Use cases**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#well-scope-the-problem-to-handle-only-the-following-use-cases)**We'll scope the problem to handle only the following use cases**>>>
            - **User** enters a block of text and gets a randomly generated link>>>
                - Expiration>>>
                    - Default setting does not expire
                    - Can optionally set a timed expiration
            - **User** enters a paste's url and views the contents
            - **User** is anonymous
            - **Service** tracks analytics of pages>>>
                - Monthly visit stats
            - **Service** deletes expired pastes
            - **Service** has high availability
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#out-of-scope)**Out of scope**>>>
            - **User** registers for an account>>>
                - **User** verifies email
            - **User** logs into a registered account>>>
                - **User** edits the document
            - **User** can set visibility
            - **User** can set the shortlink
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#constraints-and-assumptions)**Constraints and assumptions**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#state-assumptions)**State assumptions**>>>
            - Traffic is not evenly distributed
            - Following a short link should be fast
            - Pastes are text only
            - Page view analytics do not need to be realtime
            - 10 million users
            - 10 million paste writes per month
            - 100 million paste reads per month
            - 10:1 read to write ratio
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#calculate-usage)**Calculate usage**
        - **Clarify with your interviewer if you should run back-of-the-envelope usage calculations.**>>>
            - Size per paste>>>
                - 1 KB content per paste
                - `shortlink` - 7 bytes
                - `expiration_length_in_minutes` - 4 bytes
                - `created_at` - 5 bytes
                - `paste_path` - 255 bytes
                - total = ~1.27 KB
            - 12.7 GB of new paste content per month>>>
                - 1.27 KB per paste * 10 million pastes per month
                - ~450 GB of new paste content in 3 years
                - 360 million shortlinks in 3 years
                - Assume most are new pastes instead of updates to existing ones
            - 4 paste writes per second on average
            - 40 read requests per second on average
        - Handy conversion guide:>>>
            - 2.5 million seconds per month
            - 1 request per second = 2.5 million requests per month
            - 40 requests per second = 100 million requests per month
            - 400 requests per second = 1 billion requests per month
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#step-2-create-a-high-level-design)**Step 2: Create a high level design**
        - Outline a high level design with all important components.
        - [Link](https://camo.githubusercontent.com/2e0371e591b8311e36f5f5fa6ae18711f252b1f8/687474703a2f2f692e696d6775722e636f6d2f424b73426e6d472e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#step-3-design-core-components)**Step 3: Design core components**
        - Dive into details for each core component.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#use-case-user-enters-a-block-of-text-and-gets-a-randomly-generated-link)**Use case: User enters a block of text and gets a randomly generated link**
        - We could use a [relational database](https://github.com/donnemartin/system-design-primer-interview#relational-database-management-system-rdbms) as a large hash table, mapping the generated url to a file server and path containing the paste file.
        - Instead of managing a file server, we could use a managed **Object Store** such as Amazon S3 or a [NoSQL document store](https://github.com/donnemartin/system-design-primer-interview#document-store).
        - An alternative to a relational database acting as a large hash table, we could use a [NoSQL key-value store](https://github.com/donnemartin/system-design-primer-interview#key-value-store). We should discuss the [tradeoffs between choosing SQL or NoSQL](https://github.com/donnemartin/system-design-primer-interview#sql-or-nosql). The following discussion uses the relational database approach.>>>
            - The **Client** sends a create paste request to the **Web Server**, running as a [reverse proxy](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
            - The **Web Server** forwards the request to the **Write API** server
            - The **Write API** server does does the following:>>>
                - Generates a unique url>>>
                    - Checks if the url is unique by looking at the **SQL Database** for a duplicate
                    - If the url is not unique, it generates another url
                    - If we supported a custom url, we could use the user-supplied (also check for a duplicate)
                - Saves to the **SQL Database** `pastes` table
                - Saves the paste data to the **Object Store**
                - Returns the url
        - **Clarify with your interviewer how much code you are expected to write**.
        - The `pastes` table could have the following structure:
        - ```
shortlink char(7) NOT NULL
expiration_length_in_minutes int NOT NULL
created_at datetime NOT NULL
paste_path varchar(255) NOT NULL
PRIMARY KEY(shortlink)
```
        - We'll create an [index](https://github.com/donnemartin/system-design-primer-interview#use-good-indices) on `shortlink` and `created_at` to speed up lookups (log-time instead of scanning the entire table) and to keep the data in memory. Reading 1 MB sequentially from memory takes about 250 microseconds, while reading from SSD takes 4x and from disk takes 80x longer.[1](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know)
        - To generate the unique url, we could:>>>
            - Take the [**MD5**](https://en.wikipedia.org/wiki/MD5) hash of the user's ip_address + timestamp>>>
                - MD5 is a widely used hashing function that produces a 128-bit hash value
                - MD5 is uniformly distributed
                - Alternatively, we could also take the MD5 hash of randomly-generated data
            - [**Base 62**](https://www.kerstner.at/2012/07/shortening-strings-using-base-62-encoding/) encode the MD5 hash>>>
                - Base 62 encodes to `[a-zA-Z0-9]` which works well for urls, eliminating the need for escaping special characters
                - There is only one hash result for the original input and and Base 62 is deterministic (no randomness involved)
                - Base 64 is another popular encoding but provides issues for urls because of the additional `+` and `/`characters
                - The following [Base 62 pseudocode](http://stackoverflow.com/questions/742013/how-to-code-a-url-shortener) runs in O(k) time where k is the number of digits = 7:
        - ```
def base_encode(num, base=62):
    digits = []
    while num > 0
      remainder = modulo(num, base)
      digits.push(remainder)
      num = divide(num, base)
    digits = digits.reverse
```>>>
            - Take the first 7 characters of the output, which results in 62^7 possible values and should be sufficient to handle our constraint of 360 million shortlinks in 3 years:
        - ```
url = base_encode(md5(ip_address+timestamp))[:URL_LENGTH]
```
        - We'll use a public [**REST API**](https://github.com/donnemartin/system-design-primer-interview##representational-state-transfer-rest):
        - ```
$ curl -X POST --data '{ "expiration_length_in_minutes": "60", \
    "paste_contents": "Hello World!" }' https://pastebin.com/api/v1/paste
```
        - Response:
        - ```
{
    "shortlink": "foobar"
}
```
        - For internal communications, we could use [Remote Procedure Calls](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#use-case-user-enters-a-pastes-url-and-views-the-contents)**Use case: User enters a paste's url and views the contents**>>>
            - The **Client** sends a get paste request to the **Web Server**
            - The **Web Server** forwards the request to the **Read API** server
            - The **Read API** server does the following:>>>
                - Checks the **SQL Database** for the generated url>>>
                    - If the url is in the **SQL Database**, fetch the paste contents from the **Object Store**
                    - Else, return an error message for the user
        - REST API:
        - ```
$ curl https://pastebin.com/api/v1/paste?shortlink=foobar
```
        - Response:
        - ```
{
    "paste_contents": "Hello World"
    "created_at": "YYYY-MM-DD HH:MM:SS"
    "expiration_length_in_minutes": "60"
}
```
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#use-case-service-tracks-analytics-of-pages)**Use case: Service tracks analytics of pages**
        - Since realtime analytics are not a requirement, we could simply **MapReduce** the **Web Server** logs to generate hit counts.
        - **Clarify with your interviewer how much code you are expected to write**.
        - ```
class HitCounts(MRJob):

    def extract_url(self, line):
        """Extract the generated url from the log line."""
        ...

    def extract_year_month(self, line):
        """Return the year and month portions of the timestamp."""
        ...

    def mapper(self, _, line):
        """Parse each log line, extract and transform relevant lines.

        Emit key value pairs of the form:

        (2016-01, url0), 1
        (2016-01, url0), 1
        (2016-01, url1), 1
        """
        url = self.extract_url(line)
        period = self.extract_year_month(line)
        yield (period, url), 1

    def reducer(self, key, value):
        """Sum values for each key.

        (2016-01, url0), 2
        (2016-01, url1), 1
        """
        yield key, sum(values)
```
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#use-case-service-deletes-expired-pastes)**Use case: Service deletes expired pastes**
        - To delete expired pastes, we could just scan the **SQL Database** for all entries whose expiration timestamp are older than the current timestamp. All expired entries would then be deleted (or marked as expired) from the table.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#step-4-scale-the-design)**Step 4: Scale the design**
        - Identify and address bottlenecks, given the constraints.
        - [Link](https://camo.githubusercontent.com/4aee2d26ebedc20e7fa07a2c30780e332fa29f2c/687474703a2f2f692e696d6775722e636f6d2f346564584730542e706e67)
        - **Important: Do not simply jump right into the final design from the initial design!**
        - State you would do this iteratively: 1) **Benchmark/Load Test**, 2) **Profile** for bottlenecks 3) address bottlenecks while evaluating alternatives and trade-offs, and 4) repeat. See [Design a system that scales to millions of users on AWS](https://github.com/donnemartin/system-design-primer/blob/master/solutions/system_design/pastebin) as a sample on how to iteratively scale the initial design.
        - It's important to discuss what bottlenecks you might encounter with the initial design and how you might address each of them. For example, what issues are addressed by adding a **Load Balancer** with multiple **Web Servers**? **CDN**?**Master-Slave Replicas**? What are the alternatives and **Trade-Offs** for each?
        - We'll introduce some components to complete the design and to address scalability issues. Internal load balancers are not shown to reduce clutter.>>>
            -  __To avoid repeating discussions__ , refer to the following [system design topics](https://github.com/donnemartin/system-design-primer-interview#) for main talking points, tradeoffs, and alternatives:>>>
                - [DNS](https://github.com/donnemartin/system-design-primer-interview#domain-name-system)
                - [CDN](https://github.com/donnemartin/system-design-primer-interview#content-delivery-network)
                - [Load balancer](https://github.com/donnemartin/system-design-primer-interview#load-balancer)
                - [Horizontal scaling](https://github.com/donnemartin/system-design-primer-interview#horizontal-scaling)
                - [Web server (reverse proxy)](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
                - [API server (application layer)](https://github.com/donnemartin/system-design-primer-interview#application-layer)
                - [Cache](https://github.com/donnemartin/system-design-primer-interview#cache)
                - [Relational database management system (RDBMS)](https://github.com/donnemartin/system-design-primer-interview#relational-database-management-system-rdbms)
                - [SQL write master-slave failover](https://github.com/donnemartin/system-design-primer-interview#fail-over)
                - [Master-slave replication](https://github.com/donnemartin/system-design-primer-interview#master-slave-replication)
                - [Consistency patterns](https://github.com/donnemartin/system-design-primer-interview#consistency-patterns)
                - [Availability patterns](https://github.com/donnemartin/system-design-primer-interview#availability-patterns)
        - The **Analytics Database** could use a data warehousing solution such as Amazon Redshift or Google BigQuery.
        - An **Object Store** such as Amazon S3 can comfortably handle the constraint of 12.7 GB of new content per month.
        - To address the 40  __average__  read requests per second (higher at peak), traffic for popular content should be handled by the **Memory Cache** instead of the database. The **Memory Cache** is also useful for handling the unevenly distributed traffic and traffic spikes. The **SQL Read Replicas** should be able to handle the cache misses, as long as the replicas are not bogged down with replicating writes.
        - 4  __average__  paste writes per second (with higher at peak) should be do-able for a single **SQL Write Master-Slave**. Otherwise, we'll need to employ additional SQL scaling patterns:>>>
            - [Federation](https://github.com/donnemartin/system-design-primer-interview#federation)
            - [Sharding](https://github.com/donnemartin/system-design-primer-interview#sharding)
            - [Denormalization](https://github.com/donnemartin/system-design-primer-interview#denormalization)
            - [SQL Tuning](https://github.com/donnemartin/system-design-primer-interview#sql-tuning)
        - We should also consider moving some data to a **NoSQL Database**.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#additional-talking-points)**Additional talking points**
        - Additional topics to dive into, depending on the problem scope and time remaining.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#nosql)**NoSQL**>>>
            - [Key-value store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Document store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Wide column store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Graph database](https://github.com/donnemartin/system-design-primer-interview#)
            - [SQL vs NoSQL](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#caching)**Caching**>>>
            - Where to cache>>>
                - [Client caching](https://github.com/donnemartin/system-design-primer-interview#client-caching)
                - [CDN caching](https://github.com/donnemartin/system-design-primer-interview#cdn-caching)
                - [Web server caching](https://github.com/donnemartin/system-design-primer-interview#web-server-caching)
                - [Database caching](https://github.com/donnemartin/system-design-primer-interview#database-caching)
                - [Application caching](https://github.com/donnemartin/system-design-primer-interview#application-caching)
            - What to cache>>>
                - [Caching at the database query level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-database-query-level)
                - [Caching at the object level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-object-level)
            - When to update the cache>>>
                - [Cache-aside](https://github.com/donnemartin/system-design-primer-interview#cache-aside)
                - [Write-through](https://github.com/donnemartin/system-design-primer-interview#write-through)
                - [Write-behind (write-back)](https://github.com/donnemartin/system-design-primer-interview#write-behind-write-back)
                - [Refresh ahead](https://github.com/donnemartin/system-design-primer-interview#refresh-ahead)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#asynchronism-and-microservices)**Asynchronism and microservices**>>>
            - [Message queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Task queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Back pressure](https://github.com/donnemartin/system-design-primer-interview#)
            - [Microservices](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#communications)**Communications**>>>
            - Discuss tradeoffs:>>>
                - External communication with clients - [HTTP APIs following REST](https://github.com/donnemartin/system-design-primer-interview#representational-state-transfer-rest)
                - Internal communications - [RPC](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc)
            - [Service discovery](https://github.com/donnemartin/system-design-primer-interview#service-discovery)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#security)**Security**
        - Refer to the [security section](https://github.com/donnemartin/system-design-primer-interview#security).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#latency-numbers)**Latency numbers**
        - See [Latency numbers every programmer should know](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/pastebin#ongoing)**Ongoing**>>>
            - Continue benchmarking and monitoring your system to address bottlenecks as they come up
            - Scaling is an iterative process
    - **Design Amazon's sales rank by category feature**>>>
        -  __Note: This document links directly to relevant areas found in the __ [ __system design topics__ ](https://github.com/donnemartin/system-design-primer-interview#index-of-system-design-topics-1) __ to avoid duplication. Refer to the linked content for general talking points, tradeoffs, and alternatives.__ 
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#step-1-outline-use-cases-and-constraints)**Step 1: Outline use cases and constraints**
        - Gather requirements and scope the problem. Ask questions to clarify use cases and constraints. Discuss assumptions.
        - Without an interviewer to address clarifying questions, we'll define some use cases and constraints.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#use-cases)**Use cases**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#well-scope-the-problem-to-handle-only-the-following-use-case)**We'll scope the problem to handle only the following use case**>>>
            - **Service** calculates the past week's most popular products by category
            - **User** views the past week's most popular products by category
            - **Service** has high availability
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#out-of-scope)**Out of scope**>>>
            - The general e-commerce site>>>
                - Design components only for calculating sales rank
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#constraints-and-assumptions)**Constraints and assumptions**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#state-assumptions)**State assumptions**>>>
            - Traffic is not evenly distributed
            - Items can be in multiple categories
            - Items cannot change categories
            - There are no subcategories ie `foo/bar/baz`
            - Results must be updated hourly>>>
                - More popular products might need to be updated more frequently
            - 10 million products
            - 1000 categories
            - 1 billion transactions per month
            - 100 billion read requests per month
            - 100:1 read to write ratio
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#calculate-usage)**Calculate usage**
        - **Clarify with your interviewer if you should run back-of-the-envelope usage calculations.**>>>
            - Size per transaction:>>>
                - `created_at` - 5 bytes
                - `product_id` - 8 bytes
                - `category_id` - 4 bytes
                - `seller_id` - 8 bytes
                - `buyer_id` - 8 bytes
                - `quantity` - 4 bytes
                - `total_price` - 5 bytes
                - Total: ~40 bytes
            - 40 GB of new transaction content per month>>>
                - 40 bytes per transaction * 1 billion transactions per month
                - 1.44 TB of new transaction content in 3 years
                - Assume most are new transactions instead of updates to existing ones
            - 400 transactions per second on average
            - 40,000 read requests per second on average
        - Handy conversion guide:>>>
            - 2.5 million seconds per month
            - 1 request per second = 2.5 million requests per month
            - 40 requests per second = 100 million requests per month
            - 400 requests per second = 1 billion requests per month
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#step-2-create-a-high-level-design)**Step 2: Create a high level design**
        - Outline a high level design with all important components.
        - [Link](https://camo.githubusercontent.com/4dde26cbd75416ff93313d00ac6d7c1e4200064a/687474703a2f2f692e696d6775722e636f6d2f76774d613151752e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#step-3-design-core-components)**Step 3: Design core components**
        - Dive into details for each core component.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#use-case-service-calculates-the-past-weeks-most-popular-products-by-category)**Use case: Service calculates the past week's most popular products by category**
        - We could store the raw **Sales API** server log files on a managed **Object Store** such as Amazon S3, rather than managing our own distributed file system.
        - **Clarify with your interviewer how much code you are expected to write**.
        - We'll assume this is a sample log entry, tab delimited:
        - ```
timestamp   product_id  category_id    qty     total_price   seller_id    buyer_id
t1          product1    category1      2       20.00         1            1
t2          product1    category2      2       20.00         2            2
t2          product1    category2      1       10.00         2            3
t3          product2    category1      3        7.00         3            4
t4          product3    category2      7        2.00         4            5
t5          product4    category1      1        5.00         5            6
...
```
        - The **Sales Rank Service** could use **MapReduce**, using the **Sales API** server log files as input and writing the results to an aggregate table `sales_rank` in a **SQL Database**. We should discuss the [use cases and tradeoffs between choosing SQL or NoSQL](https://github.com/donnemartin/system-design-primer-interview#sql-or-nosql).
        - We'll use a multi-step **MapReduce**:>>>
            - **Step 1** - Transform the data to `(category, product_id), sum(quantity)`
            - **Step 2** - Perform a distributed sort
        - ```
class SalesRanker(MRJob):

    def within_past_week(self, timestamp):
        """Return True if timestamp is within past week, False otherwise."""
        ...

    def mapper(self, _ line):
        """Parse each log line, extract and transform relevant lines.

        Emit key value pairs of the form:

        (category1, product1), 2
        (category2, product1), 2
        (category2, product1), 1
        (category1, product2), 3
        (category2, product3), 7
        (category1, product4), 1
        """
        timestamp, product_id, category_id, quantity, total_price, seller_id, \
            buyer_id = line.split('\t')
        if self.within_past_week(timestamp):
            yield (category_id, product_id), quantity

    def reducer(self, key, value):
        """Sum values for each key.

        (category1, product1), 2
        (category2, product1), 3
        (category1, product2), 3
        (category2, product3), 7
        (category1, product4), 1
        """
        yield key, sum(values)

    def mapper_sort(self, key, value):
        """Construct key to ensure proper sorting.

        Transform key and value to the form:

        (category1, 2), product1
        (category2, 3), product1
        (category1, 3), product2
        (category2, 7), product3
        (category1, 1), product4

        The shuffle/sort step of MapReduce will then do a
        distributed sort on the keys, resulting in:

        (category1, 1), product4
        (category1, 2), product1
        (category1, 3), product2
        (category2, 3), product1
        (category2, 7), product3
        """
        category_id, product_id = key
        quantity = value
        yield (category_id, quantity), product_id

    def reducer_identity(self, key, value):
        yield key, value

    def steps(self):
        """Run the map and reduce steps."""
        return [
            self.mr(mapper=self.mapper,
                    reducer=self.reducer),
            self.mr(mapper=self.mapper_sort,
                    reducer=self.reducer_identity),
        ]
```
        - The result would be the following sorted list, which we could insert into the `sales_rank` table:
        - ```
(category1, 1), product4
(category1, 2), product1
(category1, 3), product2
(category2, 3), product1
(category2, 7), product3
```
        - The `sales_rank` table could have the following structure:
        - ```
id int NOT NULL AUTO_INCREMENT
category_id int NOT NULL
total_sold int NOT NULL
product_id int NOT NULL
PRIMARY KEY(id)
FOREIGN KEY(category_id) REFERENCES Categories(id)
FOREIGN KEY(product_id) REFERENCES Products(id)
```
        - We'll create an [index](https://github.com/donnemartin/system-design-primer-interview#use-good-indices) on `id`, `category_id`, and `product_id` to speed up lookups (log-time instead of scanning the entire table) and to keep the data in memory. Reading 1 MB sequentially from memory takes about 250 microseconds, while reading from SSD takes 4x and from disk takes 80x longer.[1](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#use-case-user-views-the-past-weeks-most-popular-products-by-category)**Use case: User views the past week's most popular products by category**>>>
            - The **Client** sends a request to the **Web Server**, running as a [reverse proxy](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
            - The **Web Server** forwards the request to the **Read API** server
            - The **Read API** server reads from the **SQL Database** `sales_rank` table
        - We'll use a public [**REST API**](https://github.com/donnemartin/system-design-primer-interview##representational-state-transfer-rest):
        - ```
$ curl https://amazon.com/api/v1/popular?category_id=1234
```
        - Response:
        - ```
{
    "id": "100",
    "category_id": "1234",
    "total_sold": "100000",
    "product_id": "50",
},
{
    "id": "53",
    "category_id": "1234",
    "total_sold": "90000",
    "product_id": "200",
},
{
    "id": "75",
    "category_id": "1234",
    "total_sold": "80000",
    "product_id": "3",
},
```
        - For internal communications, we could use [Remote Procedure Calls](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#step-4-scale-the-design)**Step 4: Scale the design**
        - Identify and address bottlenecks, given the constraints.
        - [Link](https://camo.githubusercontent.com/a56f5600f7ae29dc0c2e436b8e4e4b55c44d6894/687474703a2f2f692e696d6775722e636f6d2f4d7a45785030362e706e67)
        - **Important: Do not simply jump right into the final design from the initial design!**
        - State you would 1) **Benchmark/Load Test**, 2) **Profile** for bottlenecks 3) address bottlenecks while evaluating alternatives and trade-offs, and 4) repeat. See [Design a system that scales to millions of users on AWS](https://github.com/donnemartin/system-design-primer/blob/master/solutions/system_design/sales_rank) as a sample on how to iteratively scale the initial design.
        - It's important to discuss what bottlenecks you might encounter with the initial design and how you might address each of them. For example, what issues are addressed by adding a **Load Balancer** with multiple **Web Servers**? **CDN**?**Master-Slave Replicas**? What are the alternatives and **Trade-Offs** for each?
        - We'll introduce some components to complete the design and to address scalability issues. Internal load balancers are not shown to reduce clutter.>>>
            -  __To avoid repeating discussions__ , refer to the following [system design topics](https://github.com/donnemartin/system-design-primer-interview#) for main talking points, tradeoffs, and alternatives:>>>
                - [DNS](https://github.com/donnemartin/system-design-primer-interview#domain-name-system)
                - [CDN](https://github.com/donnemartin/system-design-primer-interview#content-delivery-network)
                - [Load balancer](https://github.com/donnemartin/system-design-primer-interview#load-balancer)
                - [Horizontal scaling](https://github.com/donnemartin/system-design-primer-interview#horizontal-scaling)
                - [Web server (reverse proxy)](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
                - [API server (application layer)](https://github.com/donnemartin/system-design-primer-interview#application-layer)
                - [Cache](https://github.com/donnemartin/system-design-primer-interview#cache)
                - [Relational database management system (RDBMS)](https://github.com/donnemartin/system-design-primer-interview#relational-database-management-system-rdbms)
                - [SQL write master-slave failover](https://github.com/donnemartin/system-design-primer-interview#fail-over)
                - [Master-slave replication](https://github.com/donnemartin/system-design-primer-interview#master-slave-replication)
                - [Consistency patterns](https://github.com/donnemartin/system-design-primer-interview#consistency-patterns)
                - [Availability patterns](https://github.com/donnemartin/system-design-primer-interview#availability-patterns)
        - The **Analytics Database** could use a data warehousing solution such as Amazon Redshift or Google BigQuery.
        - We might only want to store a limited time period of data in the database, while storing the rest in a data warehouse or in an **Object Store**. An **Object Store** such as Amazon S3 can comfortably handle the constraint of 40 GB of new content per month.
        - To address the 40,000  __average__  read requests per second (higher at peak), traffic for popular content (and their sales rank) should be handled by the **Memory Cache** instead of the database. The **Memory Cache** is also useful for handling the unevenly distributed traffic and traffic spikes. With the large volume of reads, the **SQL Read Replicas** might not be able to handle the cache misses. We'll probably need to employ additional SQL scaling patterns.
        - 400  __average__  writes per second (higher at peak) might be tough for a single **SQL Write Master-Slave**, also pointing to a need for additional scaling techniques.
        - SQL scaling patterns include:>>>
            - [Federation](https://github.com/donnemartin/system-design-primer-interview#federation)
            - [Sharding](https://github.com/donnemartin/system-design-primer-interview#sharding)
            - [Denormalization](https://github.com/donnemartin/system-design-primer-interview#denormalization)
            - [SQL Tuning](https://github.com/donnemartin/system-design-primer-interview#sql-tuning)
        - We should also consider moving some data to a **NoSQL Database**.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#additional-talking-points)**Additional talking points**
        - Additional topics to dive into, depending on the problem scope and time remaining.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#nosql)**NoSQL**>>>
            - [Key-value store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Document store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Wide column store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Graph database](https://github.com/donnemartin/system-design-primer-interview#)
            - [SQL vs NoSQL](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#caching)**Caching**>>>
            - Where to cache>>>
                - [Client caching](https://github.com/donnemartin/system-design-primer-interview#client-caching)
                - [CDN caching](https://github.com/donnemartin/system-design-primer-interview#cdn-caching)
                - [Web server caching](https://github.com/donnemartin/system-design-primer-interview#web-server-caching)
                - [Database caching](https://github.com/donnemartin/system-design-primer-interview#database-caching)
                - [Application caching](https://github.com/donnemartin/system-design-primer-interview#application-caching)
            - What to cache>>>
                - [Caching at the database query level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-database-query-level)
                - [Caching at the object level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-object-level)
            - When to update the cache>>>
                - [Cache-aside](https://github.com/donnemartin/system-design-primer-interview#cache-aside)
                - [Write-through](https://github.com/donnemartin/system-design-primer-interview#write-through)
                - [Write-behind (write-back)](https://github.com/donnemartin/system-design-primer-interview#write-behind-write-back)
                - [Refresh ahead](https://github.com/donnemartin/system-design-primer-interview#refresh-ahead)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#asynchronism-and-microservices)**Asynchronism and microservices**>>>
            - [Message queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Task queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Back pressure](https://github.com/donnemartin/system-design-primer-interview#)
            - [Microservices](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#communications)**Communications**>>>
            - Discuss tradeoffs:>>>
                - External communication with clients - [HTTP APIs following REST](https://github.com/donnemartin/system-design-primer-interview#representational-state-transfer-rest)
                - Internal communications - [RPC](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc)
            - [Service discovery](https://github.com/donnemartin/system-design-primer-interview#service-discovery)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#security)**Security**
        - Refer to the [security section](https://github.com/donnemartin/system-design-primer-interview#security).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#latency-numbers)**Latency numbers**
        - See [Latency numbers every programmer should know](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/sales_rank#ongoing)**Ongoing**>>>
            - Continue benchmarking and monitoring your system to address bottlenecks as they come up
            - Scaling is an iterative process
    - **Design a web crawler**>>>
        -  __Note: This document links directly to relevant areas found in the __ [ __system design topics__ ](https://github.com/donnemartin/system-design-primer-interview#index-of-system-design-topics-1) __ to avoid duplication. Refer to the linked content for general talking points, tradeoffs, and alternatives.__ 
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#step-1-outline-use-cases-and-constraints)**Step 1: Outline use cases and constraints**
        - Gather requirements and scope the problem. Ask questions to clarify use cases and constraints. Discuss assumptions.
        - Without an interviewer to address clarifying questions, we'll define some use cases and constraints.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#use-cases)**Use cases**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#well-scope-the-problem-to-handle-only-the-following-use-cases)**We'll scope the problem to handle only the following use cases**>>>
            - **Service** crawls a list of urls:>>>
                - Generates reverse index of words to pages containing the search terms
                - Generates titles and snippets for pages>>>
                    - Title and snippets are static, they do not change based on search query
            - **User** inputs a search term and sees a list of relevant pages with titles and snippets the crawler generated>>>
                - Only sketch high level components and interactions for this use case, no need to go into depth
            - **Service** has high availability
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#out-of-scope)**Out of scope**>>>
            - Search analytics
            - Personalized search results
            - Page rank
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#constraints-and-assumptions)**Constraints and assumptions**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#state-assumptions)**State assumptions**>>>
            - Traffic is not evenly distributed>>>
                - Some searches are very popular, while others are only executed once
            - Support only anonymous users
            - Generating search results should be fast
            - The web crawler should not get stuck in an infinite loop>>>
                - We get stuck in an infinite loop if the graph contains a cycle
            - 1 billion links to crawl>>>
                - Pages need to be crawled regularly to ensure freshness
                - Average refresh rate of about once per week, more frequent for popular sites>>>
                    - 4 billion links crawled each month
                - Average stored size per web page: 500 KB>>>
                    - For simplicity, count changes the same as new pages
            - 100 billion searches per month
        - Exercise the use of more traditional systems - don't use existing systems such as [solr](http://lucene.apache.org/solr/) or [nutch](http://nutch.apache.org/).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#calculate-usage)**Calculate usage**
        - **Clarify with your interviewer if you should run back-of-the-envelope usage calculations.**>>>
            - 2 PB of stored page content per month>>>
                - 500 KB per page * 4 billion links crawled per month
                - 72 PB of stored page content in 3 years
            - 1,600 write requests per second
            - 40,000 search requests per second
        - Handy conversion guide:>>>
            - 2.5 million seconds per month
            - 1 request per second = 2.5 million requests per month
            - 40 requests per second = 100 million requests per month
            - 400 requests per second = 1 billion requests per month
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#step-2-create-a-high-level-design)**Step 2: Create a high level design**
        - Outline a high level design with all important components.
        - [Link](https://camo.githubusercontent.com/379e7661ccfd4303985ca8b90f1ce7e9e664d3ab/687474703a2f2f692e696d6775722e636f6d2f786a64414155762e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#step-3-design-core-components)**Step 3: Design core components**
        - Dive into details for each core component.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#use-case-service-crawls-a-list-of-urls)**Use case: Service crawls a list of urls**
        - We'll assume we have an initial list of `links_to_crawl` ranked initially based on overall site popularity. If this is not a reasonable assumption, we can seed the crawler with popular sites that link to outside content such as [Yahoo](https://www.yahoo.com/), [DMOZ](http://www.dmoz.org/), etc
        - We'll use a table `crawled_links` to store processed links and their page signatures.
        - We could store `links_to_crawl` and `crawled_links` in a key-value **NoSQL Database**. For the ranked links in `links_to_crawl`, we could use [Redis](https://redis.io/) with sorted sets to maintain a ranking of page links. We should discuss the [use cases and tradeoffs between choosing SQL or NoSQL](https://github.com/donnemartin/system-design-primer-interview#sql-or-nosql).>>>
            - The **Crawler Service** processes each page link by doing the following in a loop:>>>
                - Takes the top ranked page link to crawl>>>
                    - Checks `crawled_links` in the **NoSQL Database** for an entry with a similar page signature>>>
                        - If we have a similar page, reduces the priority of the page link>>>
                            - This prevents us from getting into a cycle
                            - Continue
                        - Else, crawls the link>>>
                            - Adds a job to the **Reverse Index Service** queue to generate a [reverse index](https://en.wikipedia.org/wiki/Search_engine_indexing)
                            - Adds a job to the **Document Service** queue to generate a static title and snippet
                            - Generates the page signature
                            - Removes the link from `links_to_crawl` in the **NoSQL Database**
                            - Inserts the page link and signature to `crawled_links` in the **NoSQL Database**
        - **Clarify with your interviewer how much code you are expected to write**.
        - `PagesDataStore` is an abstraction within the **Crawler Service** that uses the **NoSQL Database**:
        - ```
class PagesDataStore(object):

    def __init__(self, db);
        self.db = db
        ...

    def add_link_to_crawl(self, url):
        """Add the given link to `links_to_crawl`."""
        ...

    def remove_link_to_crawl(self, url):
        """Remove the given link from `links_to_crawl`."""
        ...

    def reduce_priority_link_to_crawl(self, url)
        """Reduce the priority of a link in `links_to_crawl` to avoid cycles."""
        ...

    def extract_max_priority_page(self):
        """Return the highest priority link in `links_to_crawl`."""
        ...

    def insert_crawled_link(self, url, signature):
        """Add the given link to `crawled_links`."""
        ...

    def crawled_similar(self, signature):
        """Determine if we've already crawled a page matching the given signature"""
        ...
```
        - `Page` is an abstraction within the **Crawler Service** that encapsulates a page, its contents, child urls, and signature:
        - ```
class Page(object):

    def __init__(self, url, contents, child_urls, signature):
        self.url = url
        self.contents = contents
        self.child_urls = child_urls
        self.signature = signature
```
        - `Crawler` is the main class within **Crawler Service**, composed of `Page` and `PagesDataStore`.
        - ```
class Crawler(object):

    def __init__(self, data_store, reverse_index_queue, doc_index_queue):
        self.data_store = data_store
        self.reverse_index_queue = reverse_index_queue
        self.doc_index_queue = doc_index_queue

    def create_signature(self, page):
        """Create signature based on url and contents."""
        ...

    def crawl_page(self, page):
        for url in page.child_urls:
            self.data_store.add_link_to_crawl(url)
        page.signature = self.create_signature(page)
        self.data_store.remove_link_to_crawl(page.url)
        self.data_store.insert_crawled_link(page.url, page.signature)

    def crawl(self):
        while True:
            page = self.data_store.extract_max_priority_page()
            if page is None:
                break
            if self.data_store.crawled_similar(page.signature):
                self.data_store.reduce_priority_link_to_crawl(page.url)
            else:
                self.crawl_page(page)
```
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#handling-duplicates)**Handling duplicates**
        - We need to be careful the web crawler doesn't get stuck in an infinite loop, which happens when the graph contains a cycle.
        - **Clarify with your interviewer how much code you are expected to write**.
        - We'll want to remove duplicate urls:>>>
            - For smaller lists we could use something like `sort | unique`
            - With 1 billion links to crawl, we could use **MapReduce** to output only entries that have a frequency of 1
        - ```
class RemoveDuplicateUrls(MRJob):

    def mapper(self, _, line):
        yield line, 1

    def reducer(self, key, values):
        total = sum(values)
        if total == 1:
            yield key, total
```
        - Detecting duplicate content is more complex. We could generate a signature based on the contents of the page and compare those two signatures for similarity. Some potential algorithms are [Jaccard index](https://en.wikipedia.org/wiki/Jaccard_index) and [cosine similarity](https://en.wikipedia.org/wiki/Cosine_similarity).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#determining-when-to-update-the-crawl-results)**Determining when to update the crawl results**
        - Pages need to be crawled regularly to ensure freshness. Crawl results could have a `timestamp` field that indicates the last time a page was crawled. After a default time period, say one week, all pages should be refreshed. Frequently updated or more popular sites could be refreshed in shorter intervals.
        - Although we won't dive into details on analytics, we could do some data mining to determine the mean time before a particular page is updated, and use that statistic to determine how often to re-crawl the page.
        - We might also choose to support a `Robots.txt` file that gives webmasters control of crawl frequency.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#use-case-user-inputs-a-search-term-and-sees-a-list-of-relevant-pages-with-titles-and-snippets)**Use case: User inputs a search term and sees a list of relevant pages with titles and snippets**>>>
            - The **Client** sends a request to the **Web Server**, running as a [reverse proxy](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
            - The **Web Server** forwards the request to the **Query API** server
            - The **Query API** server does does the following:>>>
                - Parses the query>>>
                    - Removes markup
                    - Breaks up the text into terms
                    - Fixes typos
                    - Normalizes capitalization
                    - Converts the query to use boolean operations
                - Uses the **Reverse Index Service** to find documents matching the query>>>
                    - The **Reverse Index Service** ranks the matching results and returns the top ones
                - Uses the **Document Service** to return titles and snippets
        - We'll use a public [**REST API**](https://github.com/donnemartin/system-design-primer-interview##representational-state-transfer-rest):
        - ```
$ curl https://search.com/api/v1/search?query=hello+world
```
        - Response:
        - ```
{
    "title": "foo's title",
    "snippet": "foo's snippet",
    "link": "https://foo.com",
},
{
    "title": "bar's title",
    "snippet": "bar's snippet",
    "link": "https://bar.com",
},
{
    "title": "baz's title",
    "snippet": "baz's snippet",
    "link": "https://baz.com",
},
```
        - For internal communications, we could use [Remote Procedure Calls](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#step-4-scale-the-design)**Step 4: Scale the design**
        - Identify and address bottlenecks, given the constraints.
        - [Link](https://camo.githubusercontent.com/ba21a95852d1cf7bb64c8c4622a79d1d5a20d344/687474703a2f2f692e696d6775722e636f6d2f625778507451412e706e67)
        - **Important: Do not simply jump right into the final design from the initial design!**
        - State you would 1) **Benchmark/Load Test**, 2) **Profile** for bottlenecks 3) address bottlenecks while evaluating alternatives and trade-offs, and 4) repeat. See [Design a system that scales to millions of users on AWS](https://github.com/donnemartin/system-design-primer/blob/master/solutions/system_design/web_crawler) as a sample on how to iteratively scale the initial design.
        - It's important to discuss what bottlenecks you might encounter with the initial design and how you might address each of them. For example, what issues are addressed by adding a **Load Balancer** with multiple **Web Servers**? **CDN**?**Master-Slave Replicas**? What are the alternatives and **Trade-Offs** for each?
        - We'll introduce some components to complete the design and to address scalability issues. Internal load balancers are not shown to reduce clutter.>>>
            -  __To avoid repeating discussions__ , refer to the following [system design topics](https://github.com/donnemartin/system-design-primer-interview#) for main talking points, tradeoffs, and alternatives:>>>
                - [DNS](https://github.com/donnemartin/system-design-primer-interview#domain-name-system)
                - [Load balancer](https://github.com/donnemartin/system-design-primer-interview#load-balancer)
                - [Horizontal scaling](https://github.com/donnemartin/system-design-primer-interview#horizontal-scaling)
                - [Web server (reverse proxy)](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
                - [API server (application layer)](https://github.com/donnemartin/system-design-primer-interview#application-layer)
                - [Cache](https://github.com/donnemartin/system-design-primer-interview#cache)
                - [NoSQL](https://github.com/donnemartin/system-design-primer-interview#nosql)
                - [Consistency patterns](https://github.com/donnemartin/system-design-primer-interview#consistency-patterns)
                - [Availability patterns](https://github.com/donnemartin/system-design-primer-interview#availability-patterns)
        - Some searches are very popular, while others are only executed once. Popular queries can be served from a **Memory Cache** such as Redis or Memcached to reduce response times and to avoid overloading the **Reverse Index Service** and **Document Service**. The **Memory Cache** is also useful for handling the unevenly distributed traffic and traffic spikes. Reading 1 MB sequentially from memory takes about 250 microseconds, while reading from SSD takes 4x and from disk takes 80x longer.[1](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know)
        - Below are a few other optimizations to the **Crawling Service**:>>>
            - To handle the data size and request load, the **Reverse Index Service** and **Document Service** will likely need to make heavy use sharding and replication.
            - DNS lookup can be a bottleneck, the **Crawler Service** can keep its own DNS lookup that is refreshed periodically
            - The **Crawler Service** can improve performance and reduce memory usage by keeping many open connections at a time, referred to as [connection pooling](https://en.wikipedia.org/wiki/Connection_pool)>>>
                - Switching to [UDP](https://github.com/donnemartin/system-design-primer-interview#user-datagram-protocol-udp) could also boost performance
            - Web crawling is bandwidth intensive, ensure there is enough bandwidth to sustain high throughput
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#additional-talking-points)**Additional talking points**
        - Additional topics to dive into, depending on the problem scope and time remaining.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#sql-scaling-patterns)**SQL scaling patterns**>>>
            - [Read replicas](https://github.com/donnemartin/system-design-primer-interview#master-slave)
            - [Federation](https://github.com/donnemartin/system-design-primer-interview#federation)
            - [Sharding](https://github.com/donnemartin/system-design-primer-interview#sharding)
            - [Denormalization](https://github.com/donnemartin/system-design-primer-interview#denormalization)
            - [SQL Tuning](https://github.com/donnemartin/system-design-primer-interview#sql-tuning)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#nosql)**NoSQL**>>>
            - [Key-value store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Document store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Wide column store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Graph database](https://github.com/donnemartin/system-design-primer-interview#)
            - [SQL vs NoSQL](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#caching)**Caching**>>>
            - Where to cache>>>
                - [Client caching](https://github.com/donnemartin/system-design-primer-interview#client-caching)
                - [CDN caching](https://github.com/donnemartin/system-design-primer-interview#cdn-caching)
                - [Web server caching](https://github.com/donnemartin/system-design-primer-interview#web-server-caching)
                - [Database caching](https://github.com/donnemartin/system-design-primer-interview#database-caching)
                - [Application caching](https://github.com/donnemartin/system-design-primer-interview#application-caching)
            - What to cache>>>
                - [Caching at the database query level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-database-query-level)
                - [Caching at the object level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-object-level)
            - When to update the cache>>>
                - [Cache-aside](https://github.com/donnemartin/system-design-primer-interview#cache-aside)
                - [Write-through](https://github.com/donnemartin/system-design-primer-interview#write-through)
                - [Write-behind (write-back)](https://github.com/donnemartin/system-design-primer-interview#write-behind-write-back)
                - [Refresh ahead](https://github.com/donnemartin/system-design-primer-interview#refresh-ahead)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#asynchronism-and-microservices)**Asynchronism and microservices**>>>
            - [Message queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Task queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Back pressure](https://github.com/donnemartin/system-design-primer-interview#)
            - [Microservices](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#communications)**Communications**>>>
            - Discuss tradeoffs:>>>
                - External communication with clients - [HTTP APIs following REST](https://github.com/donnemartin/system-design-primer-interview#representational-state-transfer-rest)
                - Internal communications - [RPC](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc)
            - [Service discovery](https://github.com/donnemartin/system-design-primer-interview#service-discovery)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#security)**Security**
        - Refer to the [security section](https://github.com/donnemartin/system-design-primer-interview#security).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#latency-numbers)**Latency numbers**
        - See [Latency numbers every programmer should know](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler#ongoing)**Ongoing**>>>
            - Continue benchmarking and monitoring your system to address bottlenecks as they come up
            - Scaling is an iterative process
    - **Design the data structures for a social network**>>>
        -  __Note: This document links directly to relevant areas found in the __ [ __system design topics__ ](https://github.com/donnemartin/system-design-primer-interview#index-of-system-design-topics-1) __ to avoid duplication. Refer to the linked content for general talking points, tradeoffs, and alternatives.__ 
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#step-1-outline-use-cases-and-constraints)**Step 1: Outline use cases and constraints**
        - Gather requirements and scope the problem. Ask questions to clarify use cases and constraints. Discuss assumptions.
        - Without an interviewer to address clarifying questions, we'll define some use cases and constraints.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#use-cases)**Use cases**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#well-scope-the-problem-to-handle-only-the-following-use-cases)**We'll scope the problem to handle only the following use cases**>>>
            - **User** searches for someone and sees the shortest path to the searched person
            - **Service** has high availability
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#constraints-and-assumptions)**Constraints and assumptions**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#state-assumptions)**State assumptions**>>>
            - Traffic is not evenly distributed>>>
                - Some searches are more popular than others, while others are only executed once
            - Graph data won't fit on a single machine
            - Graph edges are unweighted
            - 100 million users
            - 50 friends per user average
            - 1 billion friend searches per month
        - Exercise the use of more traditional systems - don't use graph-specific solutions such as [GraphQL](http://graphql.org/) or a graph database like [Neo4j](https://neo4j.com/)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#calculate-usage)**Calculate usage**
        - **Clarify with your interviewer if you should run back-of-the-envelope usage calculations.**>>>
            - 5 billion friend relationships>>>
                - 100 million users * 50 friends per user average
            - 400 search requests per second
        - Handy conversion guide:>>>
            - 2.5 million seconds per month
            - 1 request per second = 2.5 million requests per month
            - 40 requests per second = 100 million requests per month
            - 400 requests per second = 1 billion requests per month
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#step-2-create-a-high-level-design)**Step 2: Create a high level design**
        - Outline a high level design with all important components.
        - [Link](https://camo.githubusercontent.com/75bd95a4871663004787cfb1737704918886121b/687474703a2f2f692e696d6775722e636f6d2f7778587971324a2e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#step-3-design-core-components)**Step 3: Design core components**
        - Dive into details for each core component.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#use-case-user-searches-for-someone-and-sees-the-shortest-path-to-the-searched-person)**Use case: User searches for someone and sees the shortest path to the searched person**
        - **Clarify with your interviewer how much code you are expected to write**.
        - Without the constraint of millions of users (vertices) and billions of friend relationships (edges), we could solve this unweighted shortest path task with a general BFS approach:
        - ```
class Graph(Graph):

    def shortest_path(self, source, dest):
        if source is None or dest is None:
            return None
        if source is dest:
            return [source.key]
        prev_node_keys = self._shortest_path(source, dest)
        if prev_node_keys is None:
            return None
        else:
            path_ids = [dest.key]
            prev_node_key = prev_node_keys[dest.key]
            while prev_node_key is not None:
                path_ids.append(prev_node_key)
                prev_node_key = prev_node_keys[prev_node_key]
            return path_ids[::-1]

    def _shortest_path(self, source, dest):
        queue = deque()
        queue.append(source)
        prev_node_keys = {source.key: None}
        source.visit_state = State.visited
        while queue:
            node = queue.popleft()
            if node is dest:
                return prev_node_keys
            prev_node = node
            for adj_node in node.adj_nodes.values():
                if adj_node.visit_state == State.unvisited:
                    queue.append(adj_node)
                    prev_node_keys[adj_node.key] = prev_node.key
                    adj_node.visit_state = State.visited
        return None
```
        - We won't be able to fit all users on the same machine, we'll need to [shard](https://github.com/donnemartin/system-design-primer-interview#sharding) users across **Person Servers** and access them with a **Lookup Service**.>>>
            - The **Client** sends a request to the **Web Server**, running as a [reverse proxy](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
            - The **Web Server** forwards the request to the **Search API** server
            - The **Search API** server forwards the request to the **User Graph Service**
            - The **User Graph Service** does does the following:>>>
                - Uses the **Lookup Service** to find the **Person Server** where the current user's info is stored
                - Finds the appropriate **Person Server** to retrieve the current user's list of `friend_ids`
                - Runs a BFS search using the current user as the `source` and the current user's `friend_ids` as the ids for each `adjacent_node`
                - To get the `adjacent_node` from a given id:>>>
                    - The **User Graph Service** will  __again__  need to communicate with the **Lookup Service** to determine which **Person Server** stores the`adjacent_node` matching the given id (potential for optimization)
        - **Clarify with your interviewer how much code you should be writing**.
        - **Note**: Error handling is excluded below for simplicity. Ask if you should code proper error handing.
        - **Lookup Service** implementation:
        - ```
class LookupService(object):

    def __init__(self):
        self.lookup = self._init_lookup()  # key: person_id, value: person_server

    def _init_lookup(self):
        ...

    def lookup_person_server(self, person_id):
        return self.lookup[person_id]
```
        - **Person Server** implementation:
        - ```
class PersonServer(object):

    def __init__(self):
        self.people = {}  # key: person_id, value: person

    def add_person(self, person):
        ...

    def people(self, ids):
        results = []
        for id in ids:
            if id in self.people:
                results.append(self.people[id])
        return results
```
        - **Person** implementation:
        - ```
class Person(object):

    def __init__(self, id, name, friend_ids):
        self.id = id
        self.name = name
        self.friend_ids = friend_ids
```
        - **User Graph Service** implementation:
        - ```
class UserGraphService(object):

    def __init__(self, lookup_service):
        self.lookup_service = lookup_service

    def person(self, person_id):
        person_server = self.lookup_service.lookup_person_server(person_id)
        return person_server.people([person_id])

    def shortest_path(self, source_key, dest_key):
        if source_key is None or dest_key is None:
            return None
        if source_key is dest_key:
            return [source_key]
        prev_node_keys = self._shortest_path(source_key, dest_key)
        if prev_node_keys is None:
            return None
        else:
            # Iterate through the path_ids backwards, starting at dest_key
            path_ids = [dest_key]
            prev_node_key = prev_node_keys[dest_key]
            while prev_node_key is not None:
                path_ids.append(prev_node_key)
                prev_node_key = prev_node_keys[prev_node_key]
            # Reverse the list since we iterated backwards
            return path_ids[::-1]

    def _shortest_path(self, source_key, dest_key, path):
        # Use the id to get the Person
        source = self.person(source_key)
        # Update our bfs queue
        queue = deque()
        queue.append(source)
        # prev_node_keys keeps track of each hop from
        # the source_key to the dest_key
        prev_node_keys = {source_key: None}
        # We'll use visited_ids to keep track of which nodes we've
        # visited, which can be different from a typical bfs where
        # this can be stored in the node itself
        visited_ids = set()
        visited_ids.add(source.id)
        while queue:
            node = queue.popleft()
            if node.key is dest_key:
                return prev_node_keys
            prev_node = node
            for friend_id in node.friend_ids:
                if friend_id not in visited_ids:
                    friend_node = self.person(friend_id)
                    queue.append(friend_node)
                    prev_node_keys[friend_id] = prev_node.key
                    visited_ids.add(friend_id)
        return None
```
        - We'll use a public [**REST API**](https://github.com/donnemartin/system-design-primer-interview##representational-state-transfer-rest):
        - ```
$ curl https://social.com/api/v1/friend_search?person_id=1234
```
        - Response:
        - ```
{
    "person_id": "100",
    "name": "foo",
    "link": "https://social.com/foo",
},
{
    "person_id": "53",
    "name": "bar",
    "link": "https://social.com/bar",
},
{
    "person_id": "1234",
    "name": "baz",
    "link": "https://social.com/baz",
},
```
        - For internal communications, we could use [Remote Procedure Calls](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#step-4-scale-the-design)**Step 4: Scale the design**
        - Identify and address bottlenecks, given the constraints.
        - [Link](https://camo.githubusercontent.com/16d78e51c2e2949e23122f4c26afe5886f82a96f/687474703a2f2f692e696d6775722e636f6d2f636443763567372e706e67)
        - **Important: Do not simply jump right into the final design from the initial design!**
        - State you would 1) **Benchmark/Load Test**, 2) **Profile** for bottlenecks 3) address bottlenecks while evaluating alternatives and trade-offs, and 4) repeat. See [Design a system that scales to millions of users on AWS](https://github.com/donnemartin/system-design-primer/blob/master/solutions/system_design/social_graph) as a sample on how to iteratively scale the initial design.
        - It's important to discuss what bottlenecks you might encounter with the initial design and how you might address each of them. For example, what issues are addressed by adding a **Load Balancer** with multiple **Web Servers**? **CDN**?**Master-Slave Replicas**? What are the alternatives and **Trade-Offs** for each?
        - We'll introduce some components to complete the design and to address scalability issues. Internal load balancers are not shown to reduce clutter.>>>
            -  __To avoid repeating discussions__ , refer to the following [system design topics](https://github.com/donnemartin/system-design-primer-interview#) for main talking points, tradeoffs, and alternatives:>>>
                - [DNS](https://github.com/donnemartin/system-design-primer-interview#domain-name-system)
                - [Load balancer](https://github.com/donnemartin/system-design-primer-interview#load-balancer)
                - [Horizontal scaling](https://github.com/donnemartin/system-design-primer-interview#horizontal-scaling)
                - [Web server (reverse proxy)](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
                - [API server (application layer)](https://github.com/donnemartin/system-design-primer-interview#application-layer)
                - [Cache](https://github.com/donnemartin/system-design-primer-interview#cache)
                - [Consistency patterns](https://github.com/donnemartin/system-design-primer-interview#consistency-patterns)
                - [Availability patterns](https://github.com/donnemartin/system-design-primer-interview#availability-patterns)
        - To address the constraint of 400  __average__  read requests per second (higher at peak), person data can be served from a **Memory Cache** such as Redis or Memcached to reduce response times and to reduce traffic to downstream services. This could be especially useful for people who do multiple searches in succession and for people who are well-connected. Reading 1 MB sequentially from memory takes about 250 microseconds, while reading from SSD takes 4x and from disk takes 80x longer.[1](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know)
        - Below are further optimizations:>>>
            - Store complete or partial BFS traversals to speed up subsequent lookups in the **Memory Cache**
            - Batch compute offline then store complete or partial BFS traversals to speed up subsequent lookups in a **NoSQL Database**
            - Reduce machine jumps by batching together friend lookups hosted on the same **Person Server**>>>
                - [Shard](https://github.com/donnemartin/system-design-primer-interview#Sharding) **Person Servers** by location to further improve this, as friends generally live closer to each other
            - Do two BFS searches at the same time, one starting from the source, and one from the destination, then merge the two paths
            - Start the BFS search from people with large numbers of friends, as they are more likely to reduce the number of [degrees of separation](https://en.wikipedia.org/wiki/Six_degrees_of_separation) between the current user and the search target
            - Set a limit based on time or number of hops before asking the user if they want to continue searching, as searching could take a considerable amount of time in some cases
            - Use a **Graph Database** such as [Neo4j](https://neo4j.com/) or a graph-specific query language such as [GraphQL](http://graphql.org/) (if there were no constraint preventing the use of **Graph Databases**)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#additional-talking-points)**Additional talking points**
        - Additional topics to dive into, depending on the problem scope and time remaining.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#sql-scaling-patterns)**SQL scaling patterns**>>>
            - [Read replicas](https://github.com/donnemartin/system-design-primer-interview#master-slave)
            - [Federation](https://github.com/donnemartin/system-design-primer-interview#federation)
            - [Sharding](https://github.com/donnemartin/system-design-primer-interview#sharding)
            - [Denormalization](https://github.com/donnemartin/system-design-primer-interview#denormalization)
            - [SQL Tuning](https://github.com/donnemartin/system-design-primer-interview#sql-tuning)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#nosql)**NoSQL**>>>
            - [Key-value store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Document store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Wide column store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Graph database](https://github.com/donnemartin/system-design-primer-interview#)
            - [SQL vs NoSQL](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#caching)**Caching**>>>
            - Where to cache>>>
                - [Client caching](https://github.com/donnemartin/system-design-primer-interview#client-caching)
                - [CDN caching](https://github.com/donnemartin/system-design-primer-interview#cdn-caching)
                - [Web server caching](https://github.com/donnemartin/system-design-primer-interview#web-server-caching)
                - [Database caching](https://github.com/donnemartin/system-design-primer-interview#database-caching)
                - [Application caching](https://github.com/donnemartin/system-design-primer-interview#application-caching)
            - What to cache>>>
                - [Caching at the database query level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-database-query-level)
                - [Caching at the object level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-object-level)
            - When to update the cache>>>
                - [Cache-aside](https://github.com/donnemartin/system-design-primer-interview#cache-aside)
                - [Write-through](https://github.com/donnemartin/system-design-primer-interview#write-through)
                - [Write-behind (write-back)](https://github.com/donnemartin/system-design-primer-interview#write-behind-write-back)
                - [Refresh ahead](https://github.com/donnemartin/system-design-primer-interview#refresh-ahead)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#asynchronism-and-microservices)**Asynchronism and microservices**>>>
            - [Message queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Task queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Back pressure](https://github.com/donnemartin/system-design-primer-interview#)
            - [Microservices](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#communications)**Communications**>>>
            - Discuss tradeoffs:>>>
                - External communication with clients - [HTTP APIs following REST](https://github.com/donnemartin/system-design-primer-interview#representational-state-transfer-rest)
                - Internal communications - [RPC](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc)
            - [Service discovery](https://github.com/donnemartin/system-design-primer-interview#service-discovery)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#security)**Security**
        - Refer to the [security section](https://github.com/donnemartin/system-design-primer-interview#security).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#latency-numbers)**Latency numbers**
        - See [Latency numbers every programmer should know](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/social_graph#ongoing)**Ongoing**>>>
            - Continue benchmarking and monitoring your system to address bottlenecks as they come up
            - Scaling is an iterative process
    - **Design the Twitter timeline and search**>>>
        -  __Note: This document links directly to relevant areas found in the __ [ __system design topics__ ](https://github.com/donnemartin/system-design-primer-interview#index-of-system-design-topics-1) __ to avoid duplication. Refer to the linked content for general talking points, tradeoffs, and alternatives.__ 
        - **Design the Facebook feed** and **Design Facebook search** are similar questions.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#step-1-outline-use-cases-and-constraints)**Step 1: Outline use cases and constraints**
        - Gather requirements and scope the problem. Ask questions to clarify use cases and constraints. Discuss assumptions.
        - Without an interviewer to address clarifying questions, we'll define some use cases and constraints.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#use-cases)**Use cases**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#well-scope-the-problem-to-handle-only-the-following-use-cases)**We'll scope the problem to handle only the following use cases**>>>
            - **User** posts a tweet>>>
                - **Service** pushes tweets to followers, sending push notifications and emails
            - **User** views the user timeline (activity from the user)
            - **User** views the home timeline (activity from people the user is following)
            - **User** searches keywords
            - **Service** has high availability
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#out-of-scope)**Out of scope**>>>
            - **Service** pushes tweets to the Twitter Firehose and other streams
            - **Service** strips out tweets based on user's visibility settings>>>
                - Hide @reply if the user is not also following the person being replied to
                - Respect 'hide retweets' setting
            - Analytics
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#constraints-and-assumptions)**Constraints and assumptions**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#state-assumptions)**State assumptions**
        - General>>>
            - Traffic is not evenly distributed
            - Posting a tweet should be fast>>>
                - Fanning out a tweet to all of your followers should be fast, unless you have millions of followers
            - 100 million active users
            - 500 million tweets per day or 15 billion tweets per month>>>
                - Each tweet averages a fanout of 10 deliveries
                - 5 billion total tweets delivered on fanout per day
                - 150 billion tweets delivered on fanout per month
            - 250 billion read requests per month
            - 10 billion searches per month
        - Timeline>>>
            - Viewing the timeline should be fast
            - Twitter is more read heavy than write heavy>>>
                - Optimize for fast reads of tweets
            - Ingesting tweets is write heavy
        - Search>>>
            - Searching should be fast
            - Search is read-heavy
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#calculate-usage)**Calculate usage**
        - **Clarify with your interviewer if you should run back-of-the-envelope usage calculations.**>>>
            - Size per tweet:>>>
                - `tweet_id` - 8 bytes
                - `user_id` - 32 bytes
                - `text` - 140 bytes
                - `media` - 10 KB average
                - Total: ~10 KB
            - 150 TB of new tweet content per month>>>
                - 10 KB per tweet * 500 million tweets per day * 30 days per month
                - 5.4 PB of new tweet content in 3 years
            - 100 thousand read requests per second>>>
                - 250 billion read requests per month * (400 requests per second / 1 billion requests per month)
            - 6,000 tweets per second>>>
                - 15 billion tweets delivered on fanout per month * (400 requests per second / 1 billion requests per month)
            - 60 thousand tweets delivered on fanout per second>>>
                - 150 billion tweets delivered on fanout per month * (400 requests per second / 1 billion requests per month)
            - 4,000 search requests per second
        - Handy conversion guide:>>>
            - 2.5 million seconds per month
            - 1 request per second = 2.5 million requests per month
            - 40 requests per second = 100 million requests per month
            - 400 requests per second = 1 billion requests per month
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#step-2-create-a-high-level-design)**Step 2: Create a high level design**
        - Outline a high level design with all important components.
        - [Link](https://camo.githubusercontent.com/f7f74718418c9b3d552d1f061cce920d2f50e81c/687474703a2f2f692e696d6775722e636f6d2f3438744541326a2e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#step-3-design-core-components)**Step 3: Design core components**
        - Dive into details for each core component.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#use-case-user-posts-a-tweet)**Use case: User posts a tweet**
        - We could store the user's own tweets to populate the user timeline (activity from the user) in a [relational database](https://github.com/donnemartin/system-design-primer-interview#relational-database-management-system-rdbms). We should discuss the [use cases and tradeoffs between choosing SQL or NoSQL](https://github.com/donnemartin/system-design-primer-interview#sql-or-nosql).
        - Delivering tweets and building the home timeline (activity from people the user is following) is trickier. Fanning out tweets to all followers (60 thousand tweets delivered on fanout per second) will overload a traditional [relational database](https://github.com/donnemartin/system-design-primer-interview#relational-database-management-system-rdbms). We'll probably want to choose a data store with fast writes such as a **NoSQL database** or **Memory Cache**. Reading 1 MB sequentially from memory takes about 250 microseconds, while reading from SSD takes 4x and from disk takes 80x longer.[1](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know)
        - We could store media such as photos or videos on an **Object Store**.>>>
            - The **Client** posts a tweet to the **Web Server**, running as a [reverse proxy](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
            - The **Web Server** forwards the request to the **Write API** server
            - The **Write API** stores the tweet in the user's timeline on a **SQL database**
            - The **Write API** contacts the **Fan Out Service**, which does the following:>>>
                - Queries the **User Graph Service** to find the user's followers stored in the **Memory Cache**
                - Stores the tweet in the  __home timeline of the user's followers__  in a **Memory Cache**>>>
                    - O(n) operation: 1,000 followers = 1,000 lookups and inserts
                - Stores the tweet in the **Search Index Service** to enable fast searching
                - Stores media in the **Object Store**
                - Uses the **Notification Service** to send out push notifications to followers:>>>
                    - Uses a **Queue** (not pictured) to asynchronously send out notifications
        - **Clarify with your interviewer how much code you are expected to write**.
        - If our **Memory Cache** is Redis, we could use a native Redis list with the following structure:
        - ```
tweet n+2                   tweet n+1                   tweet n
| 8 bytes   8 bytes  1 byte | 8 bytes   8 bytes  1 byte | 8 bytes   7 bytes  1 byte |
| tweet_id  user_id  meta   | tweet_id  user_id  meta   | tweet_id  user_id  meta   |
```
        - The new tweet would be placed in the **Memory Cache**, which populates user's home timeline (activity from people the user is following).
        - We'll use a public [**REST API**](https://github.com/donnemartin/system-design-primer-interview##representational-state-transfer-rest):
        - ```
$ curl -X POST --data '{ "user_id": "123", "auth_token": "ABC123", \
    "status": "hello world!", "media_ids": "ABC987" }' \
    https://twitter.com/api/v1/tweet
```
        - Response:
        - ```
{
    "created_at": "Wed Sep 05 00:37:15 +0000 2012",
    "status": "hello world!",
    "tweet_id": "987",
    "user_id": "123",
    ...
}
```
        - For internal communications, we could use [Remote Procedure Calls](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#use-case-user-views-the-home-timeline)**Use case: User views the home timeline**>>>
            - The **Client** posts a home timeline request to the **Web Server**
            - The **Web Server** forwards the request to the **Read API** server
            - The **Read API** server contacts the **Timeline Service**, which does the following:>>>
                - Gets the timeline data stored in the **Memory Cache**, containing tweet ids and user ids - O(1)
                - Queries the **Tweet Info Service** with a [multiget](http://redis.io/commands/mget) to obtain additional info about the tweet ids - O(n)
                - Queries the **User Info Service** with a multiget to obtain additional info about the user ids - O(n)
        - REST API:
        - ```
$ curl https://twitter.com/api/v1/home_timeline?user_id=123
```
        - Response:
        - ```
{
    "user_id": "456",
    "tweet_id": "123",
    "status": "foo"
},
{
    "user_id": "789",
    "tweet_id": "456",
    "status": "bar"
},
{
    "user_id": "789",
    "tweet_id": "579",
    "status": "baz"
},
```
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#use-case-user-views-the-user-timeline)**Use case: User views the user timeline**>>>
            - The **Client** posts a home timeline request to the **Web Server**
            - The **Web Server** forwards the request to the **Read API** server
            - The **Read API** retrieves the user timeline from the **SQL Database**
        - The REST API would be similar to the home timeline, except all tweets would come from the user as opposed to the people the user is following.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#use-case-user-searches-keywords)**Use case: User searches keywords**>>>
            - The **Client** sends a search request to the **Web Server**
            - The **Web Server** forwards the request to the **Search API** server
            - The **Search API** contacts the **Search Service**, which does the following:>>>
                - Parses/tokenizes the input query, determining what needs to be searched>>>
                    - Removes markup
                    - Breaks up the text into terms
                    - Fixes typos
                    - Normalizes capitalization
                    - Converts the query to use boolean operations
                - Queries the **Search Cluster** (ie [Lucene](https://lucene.apache.org/)) for the results:>>>
                    - [Scatter gathers](https://github.com/donnemartin/system-design-primer-interview#scatter-gather) each server in the cluster to determine if there are any results for the query
                    - Merges, ranks, sorts, and returns the results
        - REST API:
        - ```
$ curl https://twitter.com/api/v1/search?query=hello+world
```
        - The response would be similar to that of the home timeline, except for tweets matching the given query.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#step-4-scale-the-design)**Step 4: Scale the design**
        - Identify and address bottlenecks, given the constraints.
        - [Link](https://camo.githubusercontent.com/14f76dab28dfbfa12ea6b02c6bd0ec726fc17306/687474703a2f2f692e696d6775722e636f6d2f6a7255424146372e706e67)
        - **Important: Do not simply jump right into the final design from the initial design!**
        - State you would 1) **Benchmark/Load Test**, 2) **Profile** for bottlenecks 3) address bottlenecks while evaluating alternatives and trade-offs, and 4) repeat. See [Design a system that scales to millions of users on AWS](https://github.com/donnemartin/system-design-primer/blob/master/solutions/system_design/twitter) as a sample on how to iteratively scale the initial design.
        - It's important to discuss what bottlenecks you might encounter with the initial design and how you might address each of them. For example, what issues are addressed by adding a **Load Balancer** with multiple **Web Servers**? **CDN**?**Master-Slave Replicas**? What are the alternatives and **Trade-Offs** for each?
        - We'll introduce some components to complete the design and to address scalability issues. Internal load balancers are not shown to reduce clutter.>>>
            -  __To avoid repeating discussions__ , refer to the following [system design topics](https://github.com/donnemartin/system-design-primer-interview#) for main talking points, tradeoffs, and alternatives:>>>
                - [DNS](https://github.com/donnemartin/system-design-primer-interview#domain-name-system)
                - [CDN](https://github.com/donnemartin/system-design-primer-interview#content-delivery-network)
                - [Load balancer](https://github.com/donnemartin/system-design-primer-interview#load-balancer)
                - [Horizontal scaling](https://github.com/donnemartin/system-design-primer-interview#horizontal-scaling)
                - [Web server (reverse proxy)](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
                - [API server (application layer)](https://github.com/donnemartin/system-design-primer-interview#application-layer)
                - [Cache](https://github.com/donnemartin/system-design-primer-interview#cache)
                - [Relational database management system (RDBMS)](https://github.com/donnemartin/system-design-primer-interview#relational-database-management-system-rdbms)
                - [SQL write master-slave failover](https://github.com/donnemartin/system-design-primer-interview#fail-over)
                - [Master-slave replication](https://github.com/donnemartin/system-design-primer-interview#master-slave-replication)
                - [Consistency patterns](https://github.com/donnemartin/system-design-primer-interview#consistency-patterns)
                - [Availability patterns](https://github.com/donnemartin/system-design-primer-interview#availability-patterns)
        - The **Fanout Service** is a potential bottleneck. Twitter users with millions of followers could take several minutes to have their tweets go through the fanout process. This could lead to race conditions with @replies to the tweet, which we could mitigate by re-ordering the tweets at serve time.
        - We could also avoid fanning out tweets from highly-followed users. Instead, we could search to find tweets for high-followed users, merge the search results with the user's home timeline results, then re-order the tweets at serve time.
        - Additional optimizations include:>>>
            - Keep only several hundred tweets for each home timeline in the **Memory Cache**
            - Keep only active users' home timeline info in the **Memory Cache**>>>
                - If a user was not previously active in the past 30 days, we could rebuild the timeline from the **SQL Database**>>>
                    - Query the **User Graph Service** to determine who the user is following
                    - Get the tweets from the **SQL Database** and add them to the **Memory Cache**
            - Store only a month of tweets in the **Tweet Info Service**
            - Store only active users in the **User Info Service**
            - The **Search Cluster** would likely need to keep the tweets in memory to keep latency low
        - We'll also want to address the bottleneck with the **SQL Database**.
        - Although the **Memory Cache** should reduce the load on the database, it is unlikely the **SQL Read Replicas** alone would be enough to handle the cache misses. We'll probably need to employ additional SQL scaling patterns.
        - The high volume of writes would overwhelm a single **SQL Write Master-Slave**, also pointing to a need for additional scaling techniques.>>>
            - [Federation](https://github.com/donnemartin/system-design-primer-interview#federation)
            - [Sharding](https://github.com/donnemartin/system-design-primer-interview#sharding)
            - [Denormalization](https://github.com/donnemartin/system-design-primer-interview#denormalization)
            - [SQL Tuning](https://github.com/donnemartin/system-design-primer-interview#sql-tuning)
        - We should also consider moving some data to a **NoSQL Database**.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#additional-talking-points)**Additional talking points**
        - Additional topics to dive into, depending on the problem scope and time remaining.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#nosql)**NoSQL**>>>
            - [Key-value store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Document store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Wide column store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Graph database](https://github.com/donnemartin/system-design-primer-interview#)
            - [SQL vs NoSQL](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#caching)**Caching**>>>
            - Where to cache>>>
                - [Client caching](https://github.com/donnemartin/system-design-primer-interview#client-caching)
                - [CDN caching](https://github.com/donnemartin/system-design-primer-interview#cdn-caching)
                - [Web server caching](https://github.com/donnemartin/system-design-primer-interview#web-server-caching)
                - [Database caching](https://github.com/donnemartin/system-design-primer-interview#database-caching)
                - [Application caching](https://github.com/donnemartin/system-design-primer-interview#application-caching)
            - What to cache>>>
                - [Caching at the database query level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-database-query-level)
                - [Caching at the object level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-object-level)
            - When to update the cache>>>
                - [Cache-aside](https://github.com/donnemartin/system-design-primer-interview#cache-aside)
                - [Write-through](https://github.com/donnemartin/system-design-primer-interview#write-through)
                - [Write-behind (write-back)](https://github.com/donnemartin/system-design-primer-interview#write-behind-write-back)
                - [Refresh ahead](https://github.com/donnemartin/system-design-primer-interview#refresh-ahead)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#asynchronism-and-microservices)**Asynchronism and microservices**>>>
            - [Message queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Task queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Back pressure](https://github.com/donnemartin/system-design-primer-interview#)
            - [Microservices](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#communications)**Communications**>>>
            - Discuss tradeoffs:>>>
                - External communication with clients - [HTTP APIs following REST](https://github.com/donnemartin/system-design-primer-interview#representational-state-transfer-rest)
                - Internal communications - [RPC](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc)
            - [Service discovery](https://github.com/donnemartin/system-design-primer-interview#service-discovery)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#security)**Security**
        - Refer to the [security section](https://github.com/donnemartin/system-design-primer-interview#security).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#latency-numbers)**Latency numbers**
        - See [Latency numbers every programmer should know](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter#ongoing)**Ongoing**>>>
            - Continue benchmarking and monitoring your system to address bottlenecks as they come up
            - Scaling is an iterative process
    - **Design a key-value cache to save the results of the most recent web server queries**>>>
        -  __Note: This document links directly to relevant areas found in the __ [ __system design topics__ ](https://github.com/donnemartin/system-design-primer-interview#index-of-system-design-topics-1) __ to avoid duplication. Refer to the linked content for general talking points, tradeoffs, and alternatives.__ 
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#step-1-outline-use-cases-and-constraints)**Step 1: Outline use cases and constraints**
        - Gather requirements and scope the problem. Ask questions to clarify use cases and constraints. Discuss assumptions.
        - Without an interviewer to address clarifying questions, we'll define some use cases and constraints.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#use-cases)**Use cases**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#well-scope-the-problem-to-handle-only-the-following-use-cases)**We'll scope the problem to handle only the following use cases**>>>
            - **User** sends a search request resulting in a cache hit
            - **User** sends a search request resulting in a cache miss
            - **Service** has high availability
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#constraints-and-assumptions)**Constraints and assumptions**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#state-assumptions)**State assumptions**>>>
            - Traffic is not evenly distributed>>>
                - Popular queries should almost always be in the cache
                - Need to determine how to expire/refresh
            - Serving from cache requires fast lookups
            - Low latency between machines
            - Limited memory in cache>>>
                - Need to determine what to keep/remove
                - Need to cache millions of queries
            - 10 million users
            - 10 billion queries per month
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#calculate-usage)**Calculate usage**
        - **Clarify with your interviewer if you should run back-of-the-envelope usage calculations.**>>>
            - Cache stores ordered list of key: query, value: results>>>
                - `query` - 50 bytes
                - `title` - 20 bytes
                - `snippet` - 200 bytes
                - Total: 270 bytes
            - 2.7 TB of cache data per month if all 10 billion queries are unique and all are stored>>>
                - 270 bytes per search * 10 billion searches per month
                - Assumptions state limited memory, need to determine how to expire contents
            - 4,000 requests per second
        - Handy conversion guide:>>>
            - 2.5 million seconds per month
            - 1 request per second = 2.5 million requests per month
            - 40 requests per second = 100 million requests per month
            - 400 requests per second = 1 billion requests per month
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#step-2-create-a-high-level-design)**Step 2: Create a high level design**
        - Outline a high level design with all important components.
        - [Link](https://camo.githubusercontent.com/57223dafbceaf008d0fcec518ff40932f504b985/687474703a2f2f692e696d6775722e636f6d2f4b715a336453782e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#step-3-design-core-components)**Step 3: Design core components**
        - Dive into details for each core component.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#use-case-user-sends-a-request-resulting-in-a-cache-hit)**Use case: User sends a request resulting in a cache hit**
        - Popular queries can be served from a **Memory Cache** such as Redis or Memcached to reduce read latency and to avoid overloading the **Reverse Index Service** and **Document Service**. Reading 1 MB sequentially from memory takes about 250 microseconds, while reading from SSD takes 4x and from disk takes 80x longer.[1](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know)
        - Since the cache has limited capacity, we'll use a least recently used (LRU) approach to expire older entries.>>>
            - The **Client** sends a request to the **Web Server**, running as a [reverse proxy](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
            - The **Web Server** forwards the request to the **Query API** server
            - The **Query API** server does the following:>>>
                - Parses the query>>>
                    - Removes markup
                    - Breaks up the text into terms
                    - Fixes typos
                    - Normalizes capitalization
                    - Converts the query to use boolean operations
                - Checks the **Memory Cache** for the content matching the query>>>
                    - If there's a hit in the **Memory Cache**, the **Memory Cache** does the following:>>>
                        - Updates the cached entry's position to the front of the LRU list
                        - Returns the cached contents
                    - Else, the **Query API** does the following:>>>
                        - Uses the **Reverse Index Service** to find documents matching the query>>>
                            - The **Reverse Index Service** ranks the matching results and returns the top ones
                        - Uses the **Document Service** to return titles and snippets
                        - Updates the **Memory Cache** with the contents, placing the entry at the front of the LRU list
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#cache-implementation)**Cache implementation**
        - The cache can use a doubly-linked list: new items will be added to the head while items to expire will be removed from the tail. We'll use a hash table for fast lookups to each linked list node.
        - **Clarify with your interviewer how much code you are expected to write**.
        - **Query API Server** implementation:
        - ```
class QueryApi(object):

    def __init__(self, memory_cache, reverse_index_service):
        self.memory_cache = memory_cache
        self.reverse_index_service = reverse_index_service

    def parse_query(self, query):
        """Remove markup, break text into terms, deal with typos,
        normalize capitalization, convert to use boolean operations.
        """
        ...

    def process_query(self, query):
        query = self.parse_query(query)
        results = self.memory_cache.get(query)
        if results is None:
            results = self.reverse_index_service.process_search(query)
            self.memory_cache.set(query, results)
        return results
```
        - **Node** implementation:
        - ```
class Node(object):

    def __init__(self, query, results):
        self.query = query
        self.results = results
```
        - **LinkedList** implementation:
        - ```
class LinkedList(object):

    def __init__(self):
        self.head = None
        self.tail = None

    def move_to_front(self, node):
        ...

    def append_to_front(self, node):
        ...

    def remove_from_tail(self):
        ...
```
        - **Cache** implementation:
        - ```
class Cache(object):

    def __init__(self, MAX_SIZE):
        self.MAX_SIZE = MAX_SIZE
        self.size = 0
        self.lookup = {}  # key: query, value: node
        self.linked_list = LinkedList()

    def get(self, query)
        """Get the stored query result from the cache.

        Accessing a node updates its position to the front of the LRU list.
        """
        node = self.lookup[query]
        if node is None:
            return None
        self.linked_list.move_to_front(node)
        return node.results

    def set(self, results, query):
        """Set the result for the given query key in the cache.

        When updating an entry, updates its position to the front of the LRU list.
        If the entry is new and the cache is at capacity, removes the oldest entry
        before the new entry is added.
        """
        node = self.lookup[query]
        if node is not None:
            # Key exists in cache, update the value
            node.results = results
            self.linked_list.move_to_front(node)
        else:
            # Key does not exist in cache
            if self.size == self.MAX_SIZE:
                # Remove the oldest entry from the linked list and lookup
                self.lookup.pop(self.linked_list.tail.query, None)
                self.linked_list.remove_from_tail()
            else:
                self.size += 1
            # Add the new key and value
            new_node = Node(query, results)
            self.linked_list.append_to_front(new_node)
            self.lookup[query] = new_node
```
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#when-to-update-the-cache)**When to update the cache**
        - The cache should be updated when:>>>
            - The page contents change
            - The page is removed or a new page is added
            - The page rank changes
        - The most straightforward way to handle these cases is to simply set a max time that a cached entry can stay in the cache before it is updated, usually referred to as time to live (TTL).
        - Refer to [When to update the cache](https://github.com/donnemartin/system-design-primer-interview#when-to-update-the-cache) for tradeoffs and alternatives. The approach above describes [cache-aside](https://github.com/donnemartin/system-design-primer-interview#cache-aside).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#step-4-scale-the-design)**Step 4: Scale the design**
        - Identify and address bottlenecks, given the constraints.
        - [Link](https://camo.githubusercontent.com/b6439861687b9a0fc62d0149a364082643ebaf86/687474703a2f2f692e696d6775722e636f6d2f346a39396d68652e706e67)
        - **Important: Do not simply jump right into the final design from the initial design!**
        - State you would 1) **Benchmark/Load Test**, 2) **Profile** for bottlenecks 3) address bottlenecks while evaluating alternatives and trade-offs, and 4) repeat. See [Design a system that scales to millions of users on AWS](https://github.com/donnemartin/system-design-primer/blob/master/solutions/system_design/query_cache) as a sample on how to iteratively scale the initial design.
        - It's important to discuss what bottlenecks you might encounter with the initial design and how you might address each of them. For example, what issues are addressed by adding a **Load Balancer** with multiple **Web Servers**? **CDN**?**Master-Slave Replicas**? What are the alternatives and **Trade-Offs** for each?
        - We'll introduce some components to complete the design and to address scalability issues. Internal load balancers are not shown to reduce clutter.>>>
            -  __To avoid repeating discussions__ , refer to the following [system design topics](https://github.com/donnemartin/system-design-primer-interview#) for main talking points, tradeoffs, and alternatives:>>>
                - [DNS](https://github.com/donnemartin/system-design-primer-interview#domain-name-system)
                - [Load balancer](https://github.com/donnemartin/system-design-primer-interview#load-balancer)
                - [Horizontal scaling](https://github.com/donnemartin/system-design-primer-interview#horizontal-scaling)
                - [Web server (reverse proxy)](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy-web-server)
                - [API server (application layer)](https://github.com/donnemartin/system-design-primer-interview#application-layer)
                - [Cache](https://github.com/donnemartin/system-design-primer-interview#cache)
                - [Consistency patterns](https://github.com/donnemartin/system-design-primer-interview#consistency-patterns)
                - [Availability patterns](https://github.com/donnemartin/system-design-primer-interview#availability-patterns)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#expanding-the-memory-cache-to-many-machines)**Expanding the Memory Cache to many machines**
        - To handle the heavy request load and the large amount of memory needed, we'll scale horizontally. We have three main options on how to store the data on our **Memory Cache** cluster:>>>
            - **Each machine in the cache cluster has its own cache** - Simple, although it will likely result in a low cache hit rate.
            - **Each machine in the cache cluster has a copy of the cache** - Simple, although it is an inefficient use of memory.
            - **The cache is **[**sharded**](https://github.com/donnemartin/system-design-primer-interview#sharding)** across all machines in the cache cluster** - More complex, although it is likely the best option. We could use hashing to determine which machine could have the cached results of a query using `machine = hash(query)`. We'll likely want to use [consistent hashing](https://github.com/donnemartin/system-design-primer-interview#consistent-hashing).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#additional-talking-points)**Additional talking points**
        - Additional topics to dive into, depending on the problem scope and time remaining.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#sql-scaling-patterns)**SQL scaling patterns**>>>
            - [Read replicas](https://github.com/donnemartin/system-design-primer-interview#master-slave)
            - [Federation](https://github.com/donnemartin/system-design-primer-interview#federation)
            - [Sharding](https://github.com/donnemartin/system-design-primer-interview#sharding)
            - [Denormalization](https://github.com/donnemartin/system-design-primer-interview#denormalization)
            - [SQL Tuning](https://github.com/donnemartin/system-design-primer-interview#sql-tuning)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#nosql)**NoSQL**>>>
            - [Key-value store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Document store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Wide column store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Graph database](https://github.com/donnemartin/system-design-primer-interview#)
            - [SQL vs NoSQL](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#caching)**Caching**>>>
            - Where to cache>>>
                - [Client caching](https://github.com/donnemartin/system-design-primer-interview#client-caching)
                - [CDN caching](https://github.com/donnemartin/system-design-primer-interview#cdn-caching)
                - [Web server caching](https://github.com/donnemartin/system-design-primer-interview#web-server-caching)
                - [Database caching](https://github.com/donnemartin/system-design-primer-interview#database-caching)
                - [Application caching](https://github.com/donnemartin/system-design-primer-interview#application-caching)
            - What to cache>>>
                - [Caching at the database query level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-database-query-level)
                - [Caching at the object level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-object-level)
            - When to update the cache>>>
                - [Cache-aside](https://github.com/donnemartin/system-design-primer-interview#cache-aside)
                - [Write-through](https://github.com/donnemartin/system-design-primer-interview#write-through)
                - [Write-behind (write-back)](https://github.com/donnemartin/system-design-primer-interview#write-behind-write-back)
                - [Refresh ahead](https://github.com/donnemartin/system-design-primer-interview#refresh-ahead)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#asynchronism-and-microservices)**Asynchronism and microservices**>>>
            - [Message queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Task queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Back pressure](https://github.com/donnemartin/system-design-primer-interview#)
            - [Microservices](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#communications)**Communications**>>>
            - Discuss tradeoffs:>>>
                - External communication with clients - [HTTP APIs following REST](https://github.com/donnemartin/system-design-primer-interview#representational-state-transfer-rest)
                - Internal communications - [RPC](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc)
            - [Service discovery](https://github.com/donnemartin/system-design-primer-interview#service-discovery)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#security)**Security**
        - Refer to the [security section](https://github.com/donnemartin/system-design-primer-interview#security).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#latency-numbers)**Latency numbers**
        - See [Latency numbers every programmer should know](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/query_cache#ongoing)**Ongoing**>>>
            - Continue benchmarking and monitoring your system to address bottlenecks as they come up
            - Scaling is an iterative process
    - **Design a system that scales to millions of users on AWS**>>>
        -  __Note: This document links directly to relevant areas found in the __ [ __system design topics__ ](https://github.com/donnemartin/system-design-primer-interview#index-of-system-design-topics-1) __ to avoid duplication. Refer to the linked content for general talking points, tradeoffs, and alternatives.__ 
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#step-1-outline-use-cases-and-constraints)**Step 1: Outline use cases and constraints**
        - Gather requirements and scope the problem. Ask questions to clarify use cases and constraints. Discuss assumptions.
        - Without an interviewer to address clarifying questions, we'll define some use cases and constraints.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#use-cases)**Use cases**
        - Solving this problem takes an iterative approach of: 1) **Benchmark/Load Test**, 2) **Profile** for bottlenecks 3) address bottlenecks while evaluating alternatives and trade-offs, and 4) repeat, which is good pattern for evolving basic designs to scalable designs.
        - Unless you have a background in AWS or are applying for a position that requires AWS knowledge, AWS-specific details are not a requirement. However, **much of the principles discussed in this exercise can apply more generally outside of the AWS ecosystem.**
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#well-scope-the-problem-to-handle-only-the-following-use-cases)**We'll scope the problem to handle only the following use cases**>>>
            - **User** makes a read or write request>>>
                - **Service** does processing, stores user data, then returns the results
            - **Service** needs to evolve from serving a small amount of users to millions of users>>>
                - Discuss general scaling patterns as we evolve an architecture to handle a large number of users and requests
            - **Service** has high availability
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#constraints-and-assumptions)**Constraints and assumptions**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#state-assumptions)**State assumptions**>>>
            - Traffic is not evenly distributed
            - Need for relational data
            - Scale from 1 user to tens of millions of users>>>
                - Denote increase of users as:>>>
                    - Users+
                    - Users++
                    - Users+++
                    - ...
                - 10 million users
                - 1 billion writes per month
                - 100 billion reads per month
                - 100:1 read to write ratio
                - 1 KB content per write
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#calculate-usage)**Calculate usage**
        - **Clarify with your interviewer if you should run back-of-the-envelope usage calculations.**>>>
            - 1 TB of new content per month>>>
                - 1 KB per write * 1 billion writes per month
                - 36 TB of new content in 3 years
                - Assume most writes are from new content instead of updates to existing ones
            - 400 writes per second on average
            - 40,000 reads per second on average
        - Handy conversion guide:>>>
            - 2.5 million seconds per month
            - 1 request per second = 2.5 million requests per month
            - 40 requests per second = 100 million requests per month
            - 400 requests per second = 1 billion requests per month
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#step-2-create-a-high-level-design)**Step 2: Create a high level design**
        - Outline a high level design with all important components.
        - [Link](https://camo.githubusercontent.com/19e81d65d7ced13c58952f72fe1002e0f8c5798c/687474703a2f2f692e696d6775722e636f6d2f42384c444b44372e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#step-3-design-core-components)**Step 3: Design core components**
        - Dive into details for each core component.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#use-case-user-makes-a-read-or-write-request)**Use case: User makes a read or write request**[**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#goals)**Goals**>>>
            - With only 1-2 users, you only need a basic setup>>>
                - Single box for simplicity
                - Vertical scaling when needed
                - Monitor to determine bottlenecks
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#start-with-a-single-box)**Start with a single box**>>>
            - **Web server** on EC2>>>
                - Storage for user data
                - [**MySQL Database**](https://github.com/donnemartin/system-design-primer-interview#sql)
        - Use **Vertical Scaling**:>>>
            - Simply choose a bigger box
            - Keep an eye on metrics to determine how to scale up>>>
                - Use basic monitoring to determine bottlenecks: CPU, memory, IO, network, etc
                - CloudWatch, top, nagios, statsd, graphite, etc
            - Scaling vertically can get very expensive
            - No redundancy/failover
            -  __Trade-offs, alternatives, and additional details:__ >>>
                - The alternative to **Vertical Scaling** is [**Horizontal scaling**](https://github.com/donnemartin/system-design-primer-interview#horizontal-scaling)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#start-with-sql-consider-nosql)**Start with SQL, consider NoSQL**
        - The constraints assume there is a need for relational data. We can start off using a **MySQL Database** on the single box.>>>
            -  __Trade-offs, alternatives, and additional details:__ >>>
                - See the [Relational database management system (RDBMS)](https://github.com/donnemartin/system-design-primer-interview#relational-database-management-system-rdbms) section
                - Discuss reasons to use [SQL or NoSQL](https://github.com/donnemartin/system-design-primer-interview#sql-or-nosql)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#assign-a-public-static-ip)**Assign a public static IP**>>>
            - Elastic IPs provide a public endpoint whose IP doesn't change on reboot
            - Helps with failover, just point the domain to a new IP
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#use-a-dns)**Use a DNS**
        - Add a **DNS** such as Route 53 to map the domain to the instance's public IP.>>>
            -  __Trade-offs, alternatives, and additional details:__ >>>
                - See the [Domain name system](https://github.com/donnemartin/system-design-primer-interview#domain-name-system) section
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#secure-the-web-server)**Secure the web server**>>>
            - Open up only necessary ports>>>
                - Allow the web server to respond to incoming requests from:>>>
                    - 80 for HTTP
                    - 443 for HTTPS
                    - 22 for SSH to only whitelisted IPs
                - Prevent the web server from initiating outbound connections
            -  __Trade-offs, alternatives, and additional details:__ >>>
                - See the [Security](https://github.com/donnemartin/system-design-primer-interview#security) section
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#step-4-scale-the-design)**Step 4: Scale the design**
        - Identify and address bottlenecks, given the constraints.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#users)**Users+**
        - [Link](https://camo.githubusercontent.com/91df3093298a51bf3b70a2e24622f37d5ee19d0f/687474703a2f2f692e696d6775722e636f6d2f7272666a4d58422e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#assumptions)**Assumptions**
        - Our user count is starting to pick up and the load is increasing on our single box. Our **Benchmarks/Load Tests** and **Profiling** are pointing to the **MySQL Database** taking up more and more memory and CPU resources, while the user content is filling up disk space.
        - We've been able to address these issues with **Vertical Scaling** so far. Unfortunately, this has become quite expensive and it doesn't allow for independent scaling of the **MySQL Database** and **Web Server**.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#goals-1)**Goals**>>>
            - Lighten load on the single box and allow for independent scaling>>>
                - Store static content separately in an **Object Store**
                - Move the **MySQL Database** to a separate box
            - Disadvantages>>>
                - These changes would increase complexity and would require changes to the **Web Server** to point to the **Object Store** and the **MySQL Database**
                - Additional security measures must be taken to secure the new components
                - AWS costs could also increase, but should be weighed with the costs of managing similar systems on your own
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#store-static-content-separately)**Store static content separately**>>>
            - Consider using a managed **Object Store** like S3 to store static content>>>
                - Highly scalable and reliable
                - Server side encryption
            - Move static content to S3>>>
                - User files
                - JS
                - CSS
                - Images
                - Videos
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#move-the-mysql-database-to-a-separate-box)**Move the MySQL database to a separate box**>>>
            - Consider using a service like RDS to manage the **MySQL Database**>>>
                - Simple to administer, scale
                - Multiple availability zones
                - Encryption at rest
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#secure-the-system)**Secure the system**>>>
            - Encrypt data in transit and at rest
            - Use a Virtual Private Cloud>>>
                - Create a public subnet for the single **Web Server** so it can send and receive traffic from the internet
                - Create a private subnet for everything else, preventing outside access
                - Only open ports from whitelisted IPs for each component
            - These same patterns should be implemented for new components in the remainder of the exercise
            -  __Trade-offs, alternatives, and additional details:__ >>>
                - See the [Security](https://github.com/donnemartin/system-design-primer-interview#security) section
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#users-1)**Users++**
        - [Link](https://camo.githubusercontent.com/0a8044552aafe305f21804e136aa3d69e08e958a/687474703a2f2f692e696d6775722e636f6d2f72616f4654584d2e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#assumptions-1)**Assumptions**
        - Our **Benchmarks/Load Tests** and **Profiling** show that our single **Web Server** bottlenecks during peak hours, resulting in slow responses and in some cases, downtime. As the service matures, we'd also like to move towards higher availability and redundancy.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#goals-2)**Goals**>>>
            - The following goals attempt to address the scaling issues with the **Web Server**>>>
                - Based on the **Benchmarks/Load Tests** and **Profiling**, you might only need to implement one or two of these techniques
            - Use [**Horizontal Scaling**](https://github.com/donnemartin/system-design-primer-interview#horizontal-scaling) to handle increasing loads and to address single points of failure>>>
                - Add a [**Load Balancer**](https://github.com/donnemartin/system-design-primer-interview#load-balancer) such as Amazon's ELB or HAProxy>>>
                    - ELB is highly available
                    - If you are configuring your own **Load Balancer**, setting up multiple servers in [active-active](https://github.com/donnemartin/system-design-primer-interview#active-active) or [active-passive](https://github.com/donnemartin/system-design-primer-interview#active-passive) in multiple availability zones will improve availability
                    - Terminate SSL on the **Load Balancer** to reduce computational load on backend servers and to simplify certificate administration
                - Use multiple **Web Servers** spread out over multiple availability zones
                - Use multiple **MySQL** instances in [**Master-Slave Failover**](https://github.com/donnemartin/system-design-primer-interview#master-slave-replication) mode across multiple availability zones to improve redundancy
            - Separate out the **Web Servers** from the [**Application Servers**](https://github.com/donnemartin/system-design-primer-interview#application-layer)>>>
                - Scale and configure both layers independently
                - **Web Servers** can run as a [**Reverse Proxy**](https://github.com/donnemartin/system-design-primer-interview#reverse-proxy)
                - For example, you can add **Application Servers** handling **Read APIs** while others handle **Write APIs**
            - Move static (and some dynamic) content to a [**Content Delivery Network (CDN)**](https://github.com/donnemartin/system-design-primer-interview#content-delivery-network) such as CloudFront to reduce load and latency
            -  __Trade-offs, alternatives, and additional details:__ >>>
                - See the linked content above for details
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#users-2)**Users+++**
        - [Link](https://camo.githubusercontent.com/45ccce17b66f6089bc014a2c9ed538753df69ed9/687474703a2f2f692e696d6775722e636f6d2f4f5a43784a72302e706e67)
        - **Note:** **Internal Load Balancers** not shown to reduce clutter
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#assumptions-2)**Assumptions**
        - Our **Benchmarks/Load Tests** and **Profiling** show that we are read-heavy (100:1 with writes) and our database is suffering from poor performance from the high read requests.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#goals-3)**Goals**>>>
            - The following goals attempt to address the scaling issues with the **MySQL Database**>>>
                - Based on the **Benchmarks/Load Tests** and **Profiling**, you might only need to implement one or two of these techniques
            - Move the following data to a [**Memory Cache**](https://github.com/donnemartin/system-design-primer-interview#cache) such as Elasticache to reduce load and latency:>>>
                - Frequently accessed content from **MySQL**>>>
                    - First, try to configure the **MySQL Database** cache to see if that is sufficient to relieve the bottleneck before implementing a **Memory Cache**
                - Session data from the **Web Servers**>>>
                    - The **Web Servers** become stateless, allowing for **Autoscaling**
                - Reading 1 MB sequentially from memory takes about 250 microseconds, while reading from SSD takes 4x and from disk takes 80x longer.[1](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know)
            - Add [**MySQL Read Replicas**](https://github.com/donnemartin/system-design-primer-interview#master-slave-replication) to reduce load on the write master
            - Add more **Web Servers** and **Application Servers** to improve responsiveness
            -  __Trade-offs, alternatives, and additional details:__ >>>
                - See the linked content above for details
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#add-mysql-read-replicas)**Add MySQL read replicas**>>>
            - In addition to adding and scaling a **Memory Cache**, **MySQL Read Replicas** can also help relieve load on the **MySQL Write Master**
            - Add logic to **Web Server** to separate out writes and reads
            - Add **Load Balancers** in front of **MySQL Read Replicas** (not pictured to reduce clutter)
            - Most services are read-heavy vs write-heavy
            -  __Trade-offs, alternatives, and additional details:__ >>>
                - See the [Relational database management system (RDBMS)](https://github.com/donnemartin/system-design-primer-interview#relational-database-management-system-rdbms) section
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#users-3)**Users++++**
        - [Link](https://camo.githubusercontent.com/2b12de3a8a5c67a6bccadee0061bdb3d2c02c9d0/687474703a2f2f692e696d6775722e636f6d2f3358386e6d644c2e706e67)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#assumptions-3)**Assumptions**
        - Our **Benchmarks/Load Tests** and **Profiling** show that our traffic spikes during regular business hours in the U.S. and drop significantly when users leave the office. We think we can cut costs by automatically spinning up and down servers based on actual load. We're a small shop so we'd like to automate as much of the DevOps as possible for **Autoscaling** and for the general operations.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#goals-4)**Goals**>>>
            - Add **Autoscaling** to provision capacity as needed>>>
                - Keep up with traffic spikes
                - Reduce costs by powering down unused instances
            - Automate DevOps>>>
                - Chef, Puppet, Ansible, etc
            - Continue monitoring metrics to address bottlenecks>>>
                - **Host level** - Review a single EC2 instance
                - **Aggregate level** - Review load balancer stats
                - **Log analysis** - CloudWatch, CloudTrail, Loggly, Splunk, Sumo
                - **External site performance** - Pingdom or New Relic
                - **Handle notifications and incidents** - PagerDuty
                - **Error Reporting** - Sentry
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#add-autoscaling)**Add autoscaling**>>>
            - Consider a managed service such as AWS **Autoscaling**>>>
                - Create one group for each **Web Server** and one for each **Application Server** type, place each group in multiple availability zones
                - Set a min and max number of instances
                - Trigger to scale up and down through CloudWatch>>>
                    - Simple time of day metric for predictable loads or
                    - Metrics over a time period:>>>
                        - CPU load
                        - Latency
                        - Network traffic
                        - Custom metric
                - Disadvantages>>>
                    - Autoscaling can introduce complexity
                    - It could take some time before a system appropriately scales up to meet increased demand, or to scale down when demand drops
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#users-4)**Users+++++**
        - [Link](https://camo.githubusercontent.com/e45e39c36eebcc4c66e1aecd4e4145112d8e88e3/687474703a2f2f692e696d6775722e636f6d2f6a6a3341354e382e706e67)
        - **Note:** **Autoscaling** groups not shown to reduce clutter
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#assumptions-4)**Assumptions**
        - As the service continues to grow towards the figures outlined in the constraints, we iteratively run **Benchmarks/Load Tests** and **Profiling** to uncover and address new bottlenecks.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#goals-5)**Goals**
        - We'll continue to address scaling issues due to the problem's constraints:>>>
            - If our **MySQL Database** starts to grow too large, we might considering only storing a limited time period of data in the database, while storing the rest in a data warehouse such as Redshift>>>
                - A data warehouse such as Redshift can comfortably handle the constraint of 1 TB of new content per month
            - With 40,000 average read requests per second, read traffic for popular content can be addressed by scaling the **Memory Cache**, which is also useful for handling the unevenly distributed traffic and traffic spikes>>>
                - The **SQL Read Replicas** might have trouble handling the cache misses, we'll probably need to employ additional SQL scaling patterns
            - 400 average writes per second (with presumably significantly higher peaks) might be tough for a single **SQL Write Master-Slave**, also pointing to a need for additional scaling techniques
        - SQL scaling patterns include:>>>
            - [Federation](https://github.com/donnemartin/system-design-primer-interview#federation)
            - [Sharding](https://github.com/donnemartin/system-design-primer-interview#sharding)
            - [Denormalization](https://github.com/donnemartin/system-design-primer-interview#denormalization)
            - [SQL Tuning](https://github.com/donnemartin/system-design-primer-interview#sql-tuning)
        - To further address the high read and write requests, we should also consider moving appropriate data to a [**NoSQL Database**](https://github.com/donnemartin/system-design-primer-interview#nosql) such as DynamoDB.
        - We can further separate out our [**Application Servers**](https://github.com/donnemartin/system-design-primer-interview#application-layer) to allow for independent scaling. Batch processes or computations that do not need to be done in real-time can be done [**Asynchronously**](https://github.com/donnemartin/system-design-primer-interview#asynchronism) with **Queues** and **Workers**:>>>
            - For example, in a photo service, the photo upload and the thumbnail creation can be separated:>>>
                - **Client** uploads photo
                - **Application Server** puts a job in a **Queue** such as SQS
                - The **Worker Service** on EC2 or Lambda pulls work off the **Queue** then:>>>
                    - Creates a thumbnail
                    - Updates a **Database**
                    - Stores the thumbnail in the **Object Store**
            -  __Trade-offs, alternatives, and additional details:__ >>>
                - See the linked content above for details
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#additional-talking-points)**Additional talking points**
        - Additional topics to dive into, depending on the problem scope and time remaining.
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#sql-scaling-patterns)**SQL scaling patterns**>>>
            - [Read replicas](https://github.com/donnemartin/system-design-primer-interview#master-slave)
            - [Federation](https://github.com/donnemartin/system-design-primer-interview#federation)
            - [Sharding](https://github.com/donnemartin/system-design-primer-interview#sharding)
            - [Denormalization](https://github.com/donnemartin/system-design-primer-interview#denormalization)
            - [SQL Tuning](https://github.com/donnemartin/system-design-primer-interview#sql-tuning)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#nosql)**NoSQL**>>>
            - [Key-value store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Document store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Wide column store](https://github.com/donnemartin/system-design-primer-interview#)
            - [Graph database](https://github.com/donnemartin/system-design-primer-interview#)
            - [SQL vs NoSQL](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#caching)**Caching**>>>
            - Where to cache>>>
                - [Client caching](https://github.com/donnemartin/system-design-primer-interview#client-caching)
                - [CDN caching](https://github.com/donnemartin/system-design-primer-interview#cdn-caching)
                - [Web server caching](https://github.com/donnemartin/system-design-primer-interview#web-server-caching)
                - [Database caching](https://github.com/donnemartin/system-design-primer-interview#database-caching)
                - [Application caching](https://github.com/donnemartin/system-design-primer-interview#application-caching)
            - What to cache>>>
                - [Caching at the database query level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-database-query-level)
                - [Caching at the object level](https://github.com/donnemartin/system-design-primer-interview#caching-at-the-object-level)
            - When to update the cache>>>
                - [Cache-aside](https://github.com/donnemartin/system-design-primer-interview#cache-aside)
                - [Write-through](https://github.com/donnemartin/system-design-primer-interview#write-through)
                - [Write-behind (write-back)](https://github.com/donnemartin/system-design-primer-interview#write-behind-write-back)
                - [Refresh ahead](https://github.com/donnemartin/system-design-primer-interview#refresh-ahead)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#asynchronism-and-microservices)**Asynchronism and microservices**>>>
            - [Message queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Task queues](https://github.com/donnemartin/system-design-primer-interview#)
            - [Back pressure](https://github.com/donnemartin/system-design-primer-interview#)
            - [Microservices](https://github.com/donnemartin/system-design-primer-interview#)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#communications)**Communications**>>>
            - Discuss tradeoffs:>>>
                - External communication with clients - [HTTP APIs following REST](https://github.com/donnemartin/system-design-primer-interview#representational-state-transfer-rest)
                - Internal communications - [RPC](https://github.com/donnemartin/system-design-primer-interview#remote-procedure-call-rpc)
            - [Service discovery](https://github.com/donnemartin/system-design-primer-interview#service-discovery)
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#security)**Security**
        - Refer to the [security section](https://github.com/donnemartin/system-design-primer-interview#security).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#latency-numbers)**Latency numbers**
        - See [Latency numbers every programmer should know](https://github.com/donnemartin/system-design-primer-interview#latency-numbers-every-programmer-should-know).
        - [**Link**](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/scaling_aws#ongoing)**Ongoing**>>>
            - Continue benchmarking and monitoring your system to address bottlenecks as they come up
            - Scaling is an iterative process
