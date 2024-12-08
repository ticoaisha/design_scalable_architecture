# Design Scalable Architecture

This project is about designing an architecture for a scalable web application. For the purposes of this task we will use an e-commerce platform. Before diving in technical details of each of the architectural models, let's understand why it is important for the cloud engineers to make a comparison between traditional and serverless acrhitecture.

1. Traditional and serverless architectures offer different benefits and challenges, and understanding these differences helps cloud engineers make informed decisions based on the application’s requirements. For example, a traditional architecture might offer more control and consistent performance for long-running processes, while a serverless architecture simplifies infrastructure management and scales automatically.

2. Cost implications vary between architectures. Serverless architecture typically follows a pay-as-you-go model, charging only for the actual usage, which is highly cost-effective for the applications with variable or unpredictable traffic. Traditional server architectures, on the other hand, require provisioning and maintaining servers even during periods of low usage, which can be costlier in some scenarios. Comparing both approaches helps cloud engineers understand the potential cost savings or increases associated with each model, guiding them toward the most cost-effective solution.

3. Serverless architectures reduce operational overhead since developers don't need to manage servers, operating systems, or scaling configurations. Traditional architectures, however, provide more direct control over infrastructure, which can be important for some applications. Cloud engineers must understand when it makes sense to leverage serverless for simplicity and agility versus traditional setups where fine-grained control may be necessary.

4. Application performance and responsiveness. Certain applications may perform better on one architecture over the other. For instance, serverless functions may suffer from cold-start latency, while traditional servers offer consistent performance but require more scaling complexity. Comparing the architectures allows engineers to evaluate performance trade-offs, latency issues, and the user experience for different scenarios.

5. Scalability and flexibility. The ability to scale resources effectively is crucial for modern applications that may experience fluctuating or rapid increases in demand. Serverless architectures automatically scale with the number of requests without manual intervention, making them ideal for highly variable workloads. Traditional architectures rely on vertical and horizontal scaling techniques, which require careful configuration and management but offer predictable performance.

Cloud engineers focus on scalability because it ensures applications remain performant, cost-effective, and available under varying loads, which is essential for maintaining a good user experience and meeting business needs. Cloud engineers often build applications with future growth in mind. Scalability ensures that the application can accommodate growth in user base and functionality without needing a complete architectural overhaul.

## Traditional server scaling design

Let's identify the core functionalities of the e-commerce platform:
* User management will incorporate user registration, login, and profile management.
* Product catalog will manage products, search, and filter by categories.
* Shopping cart will contain add / remove items, update quantities.
* Order management will handle place orders, track order status, and view past orders.
* Payment processing will take care of the secure payment integration.

![Traditional scalable architecture](/design_scalable_architecture/images/1_traditional_server_scaling.png)

**Traditional server scaling on a high-level will include the following components:**
* **Client (web / mobile app)** represents the front-end interface, through which users interact with the application. Client sends requests and receives responses from the server. The client handles user input, display of data, and navigation.
* **Load balancer** distributes incoming network requests across multiple web servers to ensure no single server is overwhelmed. This improves system availability, reliability, and performance. Common examples is an AWS Elastic Load Balancer.
* **Web servers** handle incoming HTTP requests from clients, including rendering static pages and passing requests to the application servers. Web servers are scaled horizontally using an Auto Scaling Group to accommodate changes in the traffic load.
* **App servers** process business logic, interact with databases, and manage application-specific operations. These servers execute core application functionalities like user authentication, cart management, and order processing. They are also part of the Auto Scaling Group for efficient scaling.
* **Database** stores and manages persistent data such as user profiles, product details, orders, and more. In this setup, the database layer can be scaled vertically by increasing its resources (CPU, memory) or horizontally using replication and sharding techniques.

**Vertical and horizontal scaling strategies** can be applied to various components of this diagram. Let's consider them in more details with identifying benefits and limitations for each of the approaches. 

**1. Vertical scaling for the database layer** will be implemented by the increasing the server’s resources (e.g., CPU, memory) to handle higher loads and more complex queries.

**Benefits:** simple to implement and no architectural changes required. It is good for short-term improvements.

**Limitations:** limited by physical hardware constraints and offers diminishing returns as resources increase. It can lead to a single point of failure.

**2. Horizontal scaling for the web and application server layers** will be implemented by using an Auto Scaling Group to add or remove instances of web and application servers accordingly based on the demand. A load balancer distributes incoming traffic among available instances.

**Benefits:** provides high availability, redundancy, and supports dynamic scaling to accommodate traffic spikes.

**Limitations:** more complex to manage and requires state management (e.g., user sessions) using external stores, like Redis.

## Serverless architecture design



![Serverless architecture](/design_scalable_architecture/images/2_serverless_architecture.png)

**Serverless architecture on a high-level will include the following key components:**

* **Client (web / mobile app)**: similar to the traditional approach, the client is the user-facing interface. It interacts with serverless components through API Gateway, sending requests and receiving responses.
* **API Gateway** acts as the single entry point for client requests to the back-end. It routes incoming requests to appropriate AWS Lambda functions. It also handles request validation, caching, rate limiting, and security (authentication and authorization).
* **AWS Lambda functions** perform back-end processing for specific tasks on-demand, triggered by client requests or events. Each function is responsible for executing a distinct task, such as user authentication, managing cart operations, processing orders, or interacting with other services. For example, user management function, cart management function, order processing function.
* **Amazon DynamoDB** represents a NoSQL database used for storing and retrieving data quickly and reliably. It is ideal for serverless applications due to its seamless scalability and performance capabilities. It can store user data, product details, cart contents, and orders.
* **Amazon S3** stores static assets such as product images, CSS files, and other media files. It offers high availability, durability, and scalability, making it suitable for the content delivery in the serverless applications.
* **AWS Cognito** provides user authentication, authorization, and user management capabilities. It handles user registration, login, and access control for the application.
* **Amazon SNS/SQS (Optional)** used for messaging and event-driven architectures. SNS (Simple Notification Service) sends notifications or messages, while SQS (Simple Queue Service) queues messages for asynchronous processing.

As the picture above shows, DynamoDB, S3, SNS and SQS are shown as data stores. In this context, it is worth to note the following:
* DynamoDB and S3 are primarily used for data storage and retrieval.
* SNS and SQS serve as messaging systems for event-driven architectures and temporary data storage for messages, but their main focus is enabling asynchronous communication between components rather than long-term data persistence.

Serverless architecture provides simplicity of the scaling and management processes. As such AWS Lambda automatically scales based on incoming requests, with no need for manual intervention. With serverless architecture there is no server management, no infrastructure maintenance with sole focus on a code. With this type of architecture we can achieve a cost efficiency as pay-per-execution model ensures you only pay for the actual usage. This leads to highlighting key differences between the serverless and traditional server-based scaling in terms of:
* Cost: serverless architecture often provides significant cost savings, especially for applications with variable or unpredictable loads.
* Operational overhead: reduced need for server maintenance, load balancing, and infrastructure management.
* Scalability limits: limited mainly by account-level concurrency limits, which can be increased as needed.

## Comparison between traditional server scaling and serverless architecture

After looking into the specific characteritics of the architectural design for the traditional servers scaling and serverless architecure, let's now compare two models from the perspective of performance, cost, and operational complexity. 
* **Traditional server scaling:**
    * Good for consistent, predictable loads; horizontal scaling ensures high availability.
    * Higher costs due to maintaining idle resources during low traffic periods.
    * Requires manual scaling configurations and infrastructure maintenance.
    * **Use cases:** 
        * Application has predictable traffic and high throughput.
        * Tight control over the underlying infrastructure is needed.
        * Long-running processes require consistent availability.
* **Serverless architecture:**
    * Excellent for variable and unpredictable loads due to the automatic scaling capabilities.
    * Cost-efficient due to the pay-per-execution model; no idle costs.
    * Significantly reduced; no need to manage servers or manually scale.
    * **Use cases:**
        * Application experiences highly variable or has unpredictable traffic.
        * Development speed and reduced operational overhead are priorities.
        * Cost savings are critical due to the low-to-moderate usage patterns.

Comparing traditional and serverless architectures and focusing on scalability helps cloud engineers understand the best approach for different application scenarios, optimize costs, ensure reliable performance, and create flexible solutions that adapt to changing needs. This knowledge is essential for building resilient, efficient, and scalable cloud-based systems in a fast-paced digital environment.