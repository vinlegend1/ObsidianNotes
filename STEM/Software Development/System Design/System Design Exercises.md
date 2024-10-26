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
