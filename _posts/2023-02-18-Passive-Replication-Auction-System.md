---
title: Passive Replication Auction System
date: 2023-02-18
categories: [University Projects, 3rd year]
tags: [java, replication, distributed systems]  # TAG names should always be lowercase
---

**Project overview:**

This project is an auction system consisting of a stateless front-end, passive replication with multiple server replicas, and clients interacting with the front-end. It was made in Java using a RMI Registry

## Overall structure

Below is a detailed architecture diagram the only thing that it doesn't cover is each replica on startup looks through the RMI Registry to find the primary and then calls the primary replica to update all replica's state and if there is no primary replica it makes itself primary:

![](https://michael-perdue.github.io/assets/Auction-Structure.png)

## Passive Replication in the system

- **Replicas**: The system includes a bash file which sets up 3 replicas. It allows users to add new replicas with ease, ensuring scalability and redundancy.

- **Primary Replica**: Any replica can function as the primary replica. The front-end program forwards client requests to the primary replica. The primary replica ensures that all replicas maintain a consistent view of auction data before responding to requests.

- **Replica Process:** Each replica runs in a separate process. Command-line parameters allow the assignment of unique identifiers (IDs) to each replica for easy management.


## Front end program

- **Front-End**: The front-end is stateless and communicates with clients. It selects a primary replica by getting and searching through the list of objects in the rmiregistry for objects named `ReplicaID` and selecting the first match. It will then on subsequent requests check the object is marked as a primary replica. It then forwards client requests to it.

- **Auction Interface**: The front-end implements the `Auction` interface, providing methods for user registration, authentication, auction item management, bidding, and more. The `getPrimaryID()` method returns the identifier of the current primary replica to prove that passive replication is working. This is how the client program knows what function it can call.

## Client program

**The Client program lets the clients do the following:**     
- Create new auction items specifying the name, description and reserve price.
- List the current auction items including current bid, ID, name and description.
- Close their auction stating the price it was sold for and who to given the final bid was greater than the reserve price.
- Ensures only the creator of an auction can close it.
- Bid for a specific auction item given the auction items ID.
- See the ID of the current primary replica.

## Fault Tolerance

**No differences in states between replicas:** No replicas ever have any different states as upon starting it gets the primary replicas state which ensures even if no requests are processed before the primary replica dies the new replica still has the same state.

**Replica Recovery:** Replicas that fail can recover and rejoin the system, ensuring system robustness. This is due to each replica on start needing to request the state of the primary replica

**Complete Replica Turnover:** The system is capable of functioning even with a complete turnover of replicas. It can start with a set of replicas, add new ones, and handle the removal of original replicas while maintaining proper functionality. 

## Demonstration of Auction system

Here you can see the starting up of the auction system, 2 clients using the system, 2 replicas, shows the handling of primary replica failure and complete replica turnover.

![](https://michael-perdue.github.io/assets/Auction-Demo.gif)

**Closing thoughts:**     
This project was great at getting me to understand the basics of a distributed system and how replication works specifically passive replication.