---
title: Peer-to-peer route planning
layout: default
---
When two people want to meet up at a large event, they may be hampered when a pre-arranged rendezvous point turns out to be inconvenient, while any centralized services for real-time coordination are overwhelmed by demand. Peer-to-peer calculations between participating devices could guide the two friends to meet at an intermediate location, regardless of the continued movement of the two friends and participating devices in the area.

Code samples and illustrations from a simulation:

Using some of Protelis’s special operators (purple), a small number of functions (blue) and be defined and applied to variables (green), “Alice” and “Bob” can be directed toward each other (red dots) through the peer-to-peer calculations performed on participating devices (blue dots). 

The function distanceTo computes the minimum distance from any device to the nearest "source device" (a device where source is true). The field d is initially Infinity everywhere, but is set to 0 on sources and set to the minimum across neighbors of the sum of d and the estimated distance to the current device.

<pre>
<code style="color:red">def</code><code style="color:blue"> distanceTo</code><code>(</code><code style="color:green">source</code><code>) {</code>
<code style="color:red">  rep</code><code>(</code><code style="color:green">d</code><code> <- Infinity) {</code>
<code style="color:purple">    mux</code><code> (</code><code style="color:green">source</code><code>) { 0 }</code>
<code style="color:purple">    else</code><code> { minHood(</code><code style="color:purple">nbr</code><code>{</code><code style="color:green">d</code><code>} + nbrRange) }</code>
<code>  }
}</code>
</pre>

The function descend follows the gradient of a potential field down from a source. Given an original device (self) and a potential potential field, this function builds a path of intermediate devices connecting the original device with the source of the potential field. The original device is marked as part of the path. Other devices are identified as being on the path if one of their neighbors is already on the path, and they are the closest of that neighbor's neighbors to the destination.

<pre>
<code style="color:red">def</code><code style="color:blue"> descend</code><code>(</code><code style="color:green">source</code><code>, </code><code style="color:green">potential</code><code>) {</code>
<code style="color:purple">  rep</code><code>(</code><code style="color:green">path</code><code> <- </code><code style="color:green">source</code><code>) {</code>
<code style="color:red">    let</code><code style="color:green"> nextStep</code><code> = minHood(</code><code style="color:purple">nbr</code><code>([</code><code style="color:green">potential</code><code>, </code><code style="color:purple">self</code><code style="color:blue">.getId</code><code>()]));</code>
<code style="color:purple">    if</code><code>(</code><code style="color:green">nextStep</code><code style="color:blue">.size</code><code>() > 1) {</code>
<code style="color:red">    let</code><code style="color:green"> candidates</code><code> = (</code><code style="color:purple">nbr</code><code>([</code><code style="color:green">nextStep</code><code style="color:blue">.get</code><code>(1), </code><code style="color:green">path</code><code>]);</code>
<code style="color:green">    source</code><code> || </code><code style="color:purple">anyHood</code><code>([</code><code style="color:purple">self</code><code style="color:blue">.getId</code><code>(), true] == </code><code style="color:green">candidates</code><code>)</code>
<code>    } </code><code style="color:purple">else</code><code> {</code>
</code><code style="color:green">      source</code>
<code>   {</code>
<code> {</code>
<code>{</code>

<pre>
<code>
def descend(source,potential) {
  rep(path <- source) {
    let nextStep = minHood(nbr([potential, self.getId()]));
    if (nextStep.size() > 1) {
      let candidates = nbr([nextStep.get(1), path]);
      source || anyHood([self.getId(), true] == candidates)
    } else {
      source
    }
  }
}
</code>
</pre>

The rendezvous function uses the descend function to identify the path between two people.

``` 
def rendezvous (person1, person2) {
  descend (person1 == owner, distanceTo(person2 == owner))
}
```

![map with blue dots](/images/peer-to-peer-1.png)

Imagine an initial configuration where a number of individuals carrying participating devices (blue dots) are attending a large event, and two of them (Alice and Bob, as two yellow squares) wish to meet up.

![map with blue and a few red dots](/images/peer-to-peer-2.png)

Alice and Bob activate their rendezvous application, and nearby devices that are on the shortest path between them start to be selected as part of their rendezvous path (red dots).

![map with blue and more red dots](/images/peer-to-peer-3.png)

No central information service is needed for peer-to-peer calculation of the shortest path.

![map with blue and even more red dots](/images/peer-to-peer-4.png)

The complete path is calculated, and will update automatically as Alice and Bob walk toward each other, and as intermediate devices move around.


