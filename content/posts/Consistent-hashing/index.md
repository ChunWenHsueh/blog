---
title: "Consistent Hashing"
date: 2025-02-05T00:00:00-07:00
draft: false
showToc: true
TocOpen: true
categories: [Distributed Systems]
---

This is a note of this [paragraph](https://xiaolincoding.com/os/8_network_system/hash.html).

# Problem Statement

Given a set of servers, we need to distribute the load among them. The load can be anything like requests, data, etc. The goal is to minimize the number of reassignments when a server is added or removed. Also notice that the data on each server might not be the same.

# First intuition

One way to distribute the load is to use a hash function to map the load to a server.

```
request send to server[hash(request) % (number of servers)]
```
This approach works well when the number of servers is fixed.

For example, if we have 3 servers numbered from 0 to 2, and 3 requests with hash value 1, 2, and 3, we can assign the requests to server 1 (1 % 3), 2 (2 % 3), and 0 (3 % 3) respectively.

However, when a server is added or removed, the data needs to be reassigned to different servers. This is not ideal since it can lead to a lot of reassignments.

For example, if we add a server, now the same 3 requests will be assigned to servers 1 (1 % 4), 2 (2 % 4), and 3 (3 % 4) respectively. The first two requests are assigned to the same servers as before, but the third request is assigned to a different server. This means the data needs to be moved from server 0 to server 3.

# What is Consistent Hashing?

In our first intuition, we used a hash function to map the load to a server. Consistent hashing is a technique that uses a hash function to map the load and the server to a circle. The circle is represented by a ring where the servers are placed at different points on the ring. The load is then mapped to the server that is closest to it in the clockwise direction.

![Ring](/ring01.svg)

There are $2^{32}$ points on the ring. We map servers and requests to points on the ring using a hash function. In the above example, we have 3 servers and 3 requests. The requests `r0` and `r1` will we mapped to server `s1`, and request `r2` will be mapped to server `s0`.

However, this design still has problems.
1. The servers are not uniformly distributed on the ring. This can lead to hotspots where a lot of requests are assigned to the same server.

In the above example, the requests that are mapped between `s2` and `s1` will all go to `s2`. Which means server `s1` will have more requests than `s0` and `s2`, since there are more points between `s2` and `s1`.

2. Even if the servers are uniformly distributed, when we add or remove a server, it still might cause some servers to handle significantly more requests than others.

Let's say servers are uniformly distributed clockwise on the ring, when we remove one of the server, the next server that is closest to the removed server will take all the requests that were assigned to the removed server. Making it accept twice as many requests as the other servers.

# Virtual Nodes

We can fix this with virtula nodes. Create multiple virtual nodes for each server and place them on the ring. This way, the servers are more uniformly distributed on the ring.

![Ring](/ring02.svg)

In the above example, we create three virtual nodes for each server. The request that mapped to `s0-0`, `s0-1`, or `s0-2` will we remapped to the true server `s0`. This way, the requests are more evenly distributed among the servers, and even if we add or remove a server, the load will be evenly distributed to other servers.