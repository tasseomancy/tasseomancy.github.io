---
title: Peer-to-peer route planning
layout: default
---

When two people want to meet up at a large event, they may be hampered when a pre-arranged rendezvous point turns out to be inconvenient, while any centralized services for real-time coordination are overwhelmed by demand. Peer-to-peer calculations between participating devices could guide the two friends to meet at an intermediate location, regardless of the continued movement of the two friends and participating devices in the area.

Code samples and illustrations from a simulation:

Using some of Protelis’s special operators (purple), a small number of functions (blue) and be defined and applied to variables (green), “Alice” and “Bob” can be directed toward each other (red dots) through the peer-to-peer calculations performed on participating devices (blue dots). 

``` distanceTo ```

``` descend ```
``` rendezvous ```

![map with blue dots](/images/peer-to-peer-1.png)

Imagine an initial configuration where a number of individuals carrying participating devices (blue dots) are attending a large event, and two of them (Alice and Bob) wish to meet up.

![map with blue and a few red dots](/images/peer-to-peer-2.png)

Alice and Bob activate their rendezvous application, and nearby devices that are on the shortest path between them start to be selected as part of their rendezvous path (red dots).

![map with blue and more red dots](/images/peer-to-peer-3.png)

No central information service is needed for peer-to-peer calculation of the shortest path.

![map with blue and even more red dots](/images/peer-to-peer-4.png)

The complete path is calculated, and will update automatically as Alice and Bob walk toward each other, and as intermediate devices move around.


