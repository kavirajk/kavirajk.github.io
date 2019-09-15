---
layout: post
title: What I want to do at Recurse Center
summary: Scopes of the projects that I want to work on Recurse Center
comments: True
twitter: True
tags: [recurse-center, distributed-systems, rust]
---
I'll be joining [Recurse Center](https://www.recurse.com/) next week.

Recurse Center(RC) is where people spend 6 or 12 weeks working on getting better at programming.

This blog post I try to put some ideas of projects (and its scopes) that I would like to work on at RC. Hope this will help me keep on the track and also can be useful during retrospection.

I mostly work with Go and Web application in my daily Job. I will try to avoid that *comfort zone* as much as possible.

I will be using my project called **Heck(Distributed health checker)** to explore specific areas in distributed systems.
- Causal Consistency
- Co-ordination Avoidance
- Failure Detectors
- Load Balancing
- Replicated Data types
- Gossip

Here is the [design spec](https://paper.dropbox.com/doc/Heck-A-distributed-health-checker--Ak3aLZkVHqDLGRNLy~NHtxsFAg-WptgaNKpocdUVc1Ir8mr2) of the project for more details.

## Causal Consistency
I think I should probably start here. **Consistency Models** are core part of any distributed system. Consistency models defines formally what behaviors are possible in a distributed system. For example consider social media where people put comments on post. Strict consistency models like **Linearizability** guarantees the comments are seen in same "Order" to all the clients who are viewing it by introducing the "global order" in some way(used in etcd, Consul key values stores).

Then the other side is the weak consistency models like **Eventual Consistency** which just guarantees *at some point of time in future(eventually)* all the clients will see all the comments.

The consistency model that I really care about for the project is something called **Casual Consistency** which doesn't guarantee any global order like Linearlizability but guarantee order within events that are causally related. e.g: Comments that are reply to the other comments should appear in same order and doesn't matter the ordering of other comments that are not casually related.

Lots of concepts to understand here!. I will start with time and clocks. Physical clocks never works when it comes to distributed systems because each node can have their own physical clocks and cannot be assumed always be in sync. 
We need to use logical clocks to track causality in distributed systems (Does event A *happened-before* event B or are those concurrent?)

I should probably start playing with simple logical clocks like Lamport clocks, understand its down sides and why its not sufficient to capture causality. Then start playing with more modern logical clocks like **Version Vectors** and **Dotted Version Vectors** to capture causality relationship between events of different nodes.

## Co-ordination Avoidance
This is also related to Consistency models, If a system provides stronger consistency model say **Linearizability**, then every *state* change operations(say write operation in key value store) should be co-ordinated(Quorum, 2PC, etc). But this has additional cost and will be bottleneck when it comes to Scalability.

So many things to learn here as well, Understanding the **cost of co-ordination** in distributed systems(how bad is it?), **Universal Scalability Law** and **Blazes**([paper](https://arxiv.org/pdf/1309.3324.pdf)) to analysis co-ordination in Distributed programs.

## Failure Detectors
We got to have failure detectors in any systems where workload is shared between multiple nodes. We need a system in place to identify crashes and failures of nodes and potentially add or remove nodes dynamically to the system. I need one for cluster membership. **SWIM protocol** is popular one. Probably I should start with much simpler protocol and building all the way up to SWIM.

## Load Balancing
We need a way to assign workload to set of available nodes in a deterministic way. In Heck, additionally I also need Ownership assignment for Origin servers.

Suggested algorithm is **Rendezvous Hashing**, but still I don't know why can't we just use **Consistent Hashing** for deterministic ownership.

## Replicated Data Types
This is one of the important part of the distributed system I'm designing, Usually any state change in one node has to be replicated to other nodes. This is the problem of keeping all the nodes state in sync. No matter how much they diverge we make sure all the states will eventually be in sync 

Important to note here is we do provably correct way to reach "strong eventual consistent" state via something called **Conflict Free Replicated Data Types (CRDT)**. The basic idea is if we are able to make sure the operations on Distributed shared objects are **Associative, Commutative and Idempotent** then we can say no matter how much these shared objects diverge, they can always converge to same state irrespective of whether messages between the nodes are **delayed, dropped, delivered-out-of-order or duplicated** (Asynchrony of Networks)

I believe this will be the most interesting part of the project.

## Gossip
How does nodes communicate with each other?. They just **Gossip**. Important to note here is these nodes just have to communicate to exchange their states in a non-blocking way. Each node still serve its clients without co-ordination with other nodes.

When talking about Gossip communication, we have two options, Pull and Push based gossip. Push is kind of fire and forget way of communicating. I still need to understand much deep about exact pros and cons of each. 


I'm going to use **Rust** as implementation language. 

RC is also lot about **collaboration and discovering new things from fellow recursers!**. So probably some part of my time will be spent collaborating on other interesting projects with fellow recursers.

## Other Ideas!!

Other project ideas I might be able to work on if I happened to have more time!

* Simple QUIC protocol implementation
* Structured Regular expressions ('diff' could in principle report differences at the C function level instead of the line level) 
* A toy DNS system
* A toy line based editor like 'sed' in Rust
* Implement any modern consensus protocol like CASPaxos or Raft
* Learn to write formal specification to design, model and verify concurrent systems (like TLA+ or Isabella/HAL)

## References
* [Anatomy of Distributed systems](https://www.youtube.com/watch?v=1TIzPL4878Q)
* [Consensus without Consistency](https://www.youtube.com/watch?v=em9zLzM8O7c)
* [Co-ordination Avoidance in Database system](https://arxiv.org/pdf/1402.2237.pdf)
* [SWIM protocol](http://www.cs.cornell.edu/projects/Quicksilver/public_pdfs/SWIM.pdf)
* [Structured Regular Expression](http://doc.cat-v.org/bell_labs/structural_regexps/se.pdf)(1987)
* [Anna KV store](http://db.cs.berkeley.edu/jmh/papers/anna_ieee18.pdf)
* [Keeping CALM](https://arxiv.org/pdf/1901.01930.pdf)
* [Measuring Co-ordination](https://arxiv.org/pdf/1309.3324.pdf)
* [Version vectors](https://github.com/ricardobcl/Dotted-Version-Vectors)
* [Good resources on CRDT in general](https://github.com/ipfs/research-CRDT)
* [Distributed systems for practitioner](https://leanpub.com/distributed-systems-for-practitioners)
* [Distributed systems for Fun and Profit](http://book.mixu.net/distsys/single-page.html)
