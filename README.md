

# System Designs 

- A few examples from Algoexpert/system design 
- Most other are private (ex. teamstream and magic) 

- [Global and Fast Code Deployment system - from Algoexpert](#1-code-deployment-system)  


# System Design 1 - News feed (facebook) 

## Initial questions related 

### Key concepts? 

- User-Generated Content (UGC): All these platforms rely heavily on content created and shared by their users.

- Algorithmic Content Delivery: They all use algorithms to decide what to show in a user's feed based on factors like past user behavior, network connections, user preferences, trending topics, etc.

- Social Interaction: Each of these platforms facilitates social interaction, allowing users to like, comment on, share, and interact with the content that they see.

- Real-time Updates: All of these platforms provide near-real-time updates, with new content appearing in the feed as it is posted.

- Customization: They all offer the ability for users to customize their feeds to some extent, based on who they follow or what topics they're interested in.

- Ad-based Revenue Model: They all generate revenue by showing targeted ads to users based on their behavior, interests, and demographics.

### Example platforms  

| Platform | Content Focus | Unique Features |
| --- | --- | --- |
| Spotify | Music and podcasts | User follows artists, playlists, or friends to get updates and recommendations; "Discover Weekly" playlist. |
| Facebook | Mixed (text, photos, videos, links) | Combines updates from friends, groups, pages; features for private messaging, groups, and events. |
| Instagram | Photo and video | Aesthetic appeal; photo filters; Instagram Stories, IGTV, and Shopping. |
| Twitter | Text (also supports images, videos, links) | Character limit encourages concise messaging; real-time updates on current events; trending topics and hashtags. |
| TikTok | Short-form video | Users can create and share videos up to 60 seconds long; viral dance challenges; educational content; powerful content recommendation algorithm. |


### Functionality needed? 

- Account Management
- User Profile
- Posts an Content Sharing
- Feed and content discovery
- Social interactions
- Messaging
- Groups and pages
- Privacy and security
- Accessibility and usability
- Ad display

### Main stakeholders 

- End-users
- Advertiseras
- Content createors
- Employees and developers
- Regulatory authorities
- Partners
- Community and society at large 
  
### Initial services 

- User Service: This handles user-related operations and stores user profiles, including the list of friends for each user.

- Feed Service: This is responsible for creating, storing, and retrieving feed data. Each user's feed is generated by pulling status updates from their friends and running them through a ranking algorithm.

- Real-Time Update Service: This service pushes real-time updates to users' feeds. When a user posts a status update, the Real-Time Update Service ensures that it's quickly propagated to their friends' feeds.

- Ranking Service: This service takes a list of relevant posts and ranks them to generate a user's news feed.

- Ads Service (optional): If ads are incorporated, this service will determine what ads to show to a user at any given time.

### Sequence diagram - ver 1.0 

![Feed sequence diagram](https://github.com/skirtapaieo/system-design-101/blob/main/feed-seq-diagram.png)

https://github.com/skirtapaieo/system-design-101/blob/main/feed-seq-diagram.png

´´´
@startuml
actor User
participant "User Service" as US
participant "Feed Service" as FS
participant "Ranking Service" as RS
participant "Real-Time Update Service" as RTUS

User -> US : Login
US -> FS : Request Feed
FS -> US : Get Friends
US -> FS : Return Friends
FS -> FS : Get Status Updates from Friends
FS -> RS : Rank Updates
RS -> FS : Return Ranked Updates
FS -> US : Return Feed
US -> User : Display Feed

User -> US : Post Status Update
US -> FS : Add Status Update
FS -> RTUS : Publish Update to Friends
RTUS -> FS : Update Friends' Feeds
@enduml
´´´

### Non-functional requirements 

- Scalability: Given the large number of users (100 million daily users, 1 million daily status updates), the system needs to be scalable. We could use a distributed database system and implement sharding and partitioning strategies to distribute data and load across multiple servers.

- Feed Generation/Refreshing: For feed generation, consider a pull model where feeds are generated when users log in or refresh their feeds. Given that we want new posts to show up quickly, it's important to optimize the feed refreshing process. Caching can also be used to improve feed load times.

- Data Persistence: To ensure that posts don't disappear, we need a robust data persistence strategy. This could involve database replication to protect against data loss if a single machine fails. Use of a distributed database with replication and failover mechanisms can help here.

- Real-Time Updates: Given the requirement for fast propagation of new posts, using a publish-subscribe model for real-time updates could be a good solution. When a user posts a status update, a message is published to a topic, and all of the user's friends who are subscribed to that topic receive the message.

- Global Audience: Given the global audience, latency and data locality should be considered. Users should be served from the nearest data center where possible. This can be achieved with a geo-distributed database and Content Delivery Network (CDN).

- Ads Integration (optional): If ads are incorporated, they could be treated as special types of posts that get inserted into the feed by the Ads Service and then ranked along with regular posts by the Ranking Service.


### Sequence diagram ver 2 - with notes related to non-functional ideas 

![Feed sequence diagram](https://github.com/skirtapaieo/system-design-101/blob/main/feed-seq-diagram-NFR.png)

https://github.com/skirtapaieo/system-design-101/blob/main/feed-seq-diagram-nfr.png

´´´
@startuml
actor User
participant "User Service" as US
participant "Feed Service" as FS
participant "Ranking Service" as RS
participant "Real-Time Update Service" as RTUS

User -> US : Login
US -> FS : Request Feed
note over FS : Scalability: Distributed database for user feeds
FS -> US : Get Friends
US -> FS : Return Friends
FS -> FS : Get Status Updates from Friends
FS -> RS : Rank Updates
RS -> FS : Return Ranked Updates
note over FS : Real-Time Updates: New posts propagate quickly
FS -> US : Return Feed
US -> User : Display Feed

User -> US : Post Status Update
note over US : Data Persistence: Status updates confirmed to user are reliably stored
US -> FS : Add Status Update
FS -> RTUS : Publish Update to Friends
RTUS -> FS : Update Friends' Feeds
@enduml

´´´

<br>

### Technical solution for each service 

ice	Recommended Technical Solution	Facebook	Twitter	Spotify
User Service	Distributed Database (e.g., Cassandra, DynamoDB)	MySQL, proprietary services (e.g., TAO)	MySQL, Manhattan (Twitter's distributed DB)	PostgreSQL, Google Cloud Bigtable
Feed Service	In-Memory Database (e.g., Redis), Message Brokers (e.g., Apache Kafka)	Memcached for caching, Apache Kafka for real-time streaming	Redis for caching, proprietary message bus system	Apache Cassandra, Google Pub/Sub
Ranking Service	ML Framework (e.g., TensorFlow, PyTorch)	Proprietary ML frameworks	Proprietary ML frameworks	TensorFlow, Google Cloud ML Engine
Real-Time Update Service	Real-time Message Broker (e.g., Apache Kafka, RabbitMQ)	Apache Kafka, proprietary pub/sub service	Proprietary message bus system	Apache Kafka, Google Pub/Sub




# Code-deployment system
design a code deployment system (part of exercise at Algoexpert/System design) 

# Index

- [System design](#system-design)
- [The initial questions](#the-initial-questions)
- [Detailed system design questions](#detailed-system-design-questions)
  - [Scope and Scale](#scope-and-scale)
  - [Target Environments](#target-environments)
  - [Security Requirements](#security-requirements)
  - [Versioning and Rollbacks](#versioning-and-rollbacks)
  - [Deployment Workflow](#deployment-workflow)
  - [Monitoring and Logging](#monitoring-and-logging)
  - [Network and Infrastructure](#network-and-infrastructure)
  - [Compliance and Governance](#compliance-and-governance)
  - [Integration and Compatibility](#integration-and-compatibility)
  - [Team and Roles](#team-and-roles)
- [First pass of the system design](#first-pass-of-the-system-design)
  - [Global Deployment](#global-deployment)
  - [Deployment Frequency and Rollbacks](#deployment-frequency-and-rollbacks)
  - [Uptime and Performance](#uptime-and-performance)
  - [Integration with Code Reviews and Build Systems](#integration-with-code-reviews-and-build-systems)
  - [Automation and Orchestration](#automation-and-orchestration)
  - [Monitoring and Alerting](#monitoring-and-alerting)
  - [Security and Access Control](#security-and-access-control)
  - [Training and Documentation](#training-and-documentation)
- [The system overview](#the-system-overview)


# System design (result of below) 

![Alt text of the image](https://github.com/skirtapaieo/codedeployment/blob/main/systemdesign.png)


# The initial questions 

- The first questions is of course buy or build, but for the point of the exercise we will ask the questions needed for a design, and a later implementation. 
- The second questions is whether this suits the company and the overall architecture and 
- whether the resources are in place to, after the design, actually be able to build the system. 

As shown later in the design phase, it is pretty complicated. 

# Detailed system design questions 

The following, and other questions will help us to understand the specific requirements and constraints of the code-deployment system, enabling you to design a solution the meets the needs of the organisation. 

## Scope and Scale

- How many locations or regions will the system need to support globally?
- What is the expected number of deployments per day or per hour?
- Are there any specific performance or latency requirements?

## Target Environments

- What types of environments will the code be deployed to (e.g., cloud-based, on-premises, hybrid)?
- Are there any specific platforms, operating systems, or architectures that need to be supported?

## Security Requirements

- What security measures need to be in place to protect the code during deployment?
- Are there any specific authentication or authorization requirements?
- Is there a need for encryption or secure communication channels?

## Versioning and Rollbacks

- How should the system handle versioning of the deployed code?
- Is there a need for the ability to rollback to a previous version in case of issues or failures?

## Deployment Workflow

- What is the desired workflow for code deployment (e.g., continuous integration/continuous deployment, manual approval process)?
- Are there any specific pre-deployment or post-deployment tasks that need to be performed?


## Monitoring and Logging

- What metrics and logging information should be captured during deployment?
- Are there any specific monitoring or alerting requirements in case of deployment failures?

## Network and Infrastructure

- What are the available network bandwidth and infrastructure capabilities across different regions?
- Are there any limitations or considerations related to firewalls, load balancers, or other networking components?

## Compliance and Governance

- Are there any compliance requirements or industry-specific regulations that need to be considered during code deployment?

## Integration and Compatibility

- Does the code-deployment system need to integrate with any existing tools or systems (e.g., version control, configuration management)?
- Are there any dependencies or restrictions regarding the technologies or programming languages used in the code?

## Team and Roles

- Who will be responsible for managing and operating the code-deployment system?
- What are the roles and responsibilities of the individuals involved in the deployment process?

# First pass of the system design 

- The system is supposed to exist in five different physical locations 
- where binaries have to be deployed reliably and 
- where the system have to be performance enought to do the deployment within a few minutes 

## Global Deployment

- With around 5 locations, you need to ensure that the system can efficiently distribute deployments across these locations while maintaining consistent performance.
- Consider using a distributed architecture with local deployment servers in each location to minimize latency and network bottlenecks.

## Deployment Frequency and Rollbacks

- Supporting hundreds of deployments per day requires an automated and streamlined process.
- Implement a robust versioning system that allows easy rollback to previous versions if issues occur.
- Define a clear rollback strategy to handle failures, including a mechanism to roll forward quickly once the issue is resolved.

## Uptime and Performance

- To achieve 99.9% uptime, consider implementing redundancy and failover mechanisms across the deployment system.
- Optimize the deployment process to minimize downtime during deployments, aiming for a maximum build and deploy time of 30 minutes.
- Utilize load balancing and resource scaling techniques to handle increased demand during peak deployment periods.

## Integration with Code Reviews and Build Systems:

- Leverage the existing code review and build systems to ensure the quality and integrity of the code being deployed.
- Integrate the code-deployment system with the code review system to enforce checks and balances before allowing deployment.

## Automation and Orchestration

- Automation is crucial for managing a high volume of deployments.
- Implement a robust deployment pipeline that automates the steps from code submission to production deployment, including testing and verification.

## Monitoring and Alerting

- Set up comprehensive monitoring and logging capabilities to track the performance, health, and success of deployments.
- Implement real-time alerts to notify relevant teams or individuals in case of deployment failures or anomalies.

## Security and Access Control

- Ensure proper authentication and authorization mechanisms are in place to prevent unauthorized access to the deployment system.
- Consider using secure communication protocols (e.g., HTTPS) to protect sensitive data during code deployment.

## Training and Documentation:

- Provide training and documentation to users and stakeholders to ensure smooth adoption and understanding of the code-deployment system.
- Document best practices, guidelines, and troubleshooting procedures to assist users in case of issues.

By addressing these considerations, it is possible to design a global and fast code-deployment system that meets the specified requirements and supports efficient and reliable deployments across your organization.

# The system overview 

Based on the description above we asked ChatGPT to generate a high-level sketch in PlantUML notation. *See Systemdesign.uml*

