---
title: Peer-to-peer route planning
layout: default
---
When two people want to meet up at a large event, they may be hampered when a pre-arranged rendezvous point turns out to be inconvenient, while any centralized services for real-time coordination are overwhelmed by demand. Peer-to-peer calculations between participating devices could guide the two friends to meet at an intermediate location, regardless of the continued movement of the two friends and participating devices in the area.

<b>Code samples and illustrations from a simulation:</b>

Using some of Protelis’s special operators and syntax (purple), a small number of functions (blue) can be defined and applied to variables (green). “Alice” and “Bob” can be directed toward each other (red dots) through the peer-to-peer calculations performed on participating devices (blue dots). 

The function <code>distanceTo</code> computes the minimum distance from any device to the nearest "source device" (a device where source is true). The field d is initially Infinity everywhere, but is set to 0 on sources and set to the minimum across neighbors of the sum of d and the estimated distance to the current device.

<pre>
<code style="color:purple">def</code><code style="color:blue"> distanceTo</code><code>(</code><code style="color:green">source</code><code>) {</code>
<code style="color:purple">  rep</code><code>(</code><code style="color:green">d</code><code> <- </code><code style="color:purple">Infinity</code><code>) {</code>
<code style="color:purple">    mux</code><code> (</code><code style="color:green">source</code><code>) { 0 }</code>
<code style="color:purple">    else</code><code> { </code><code style="color:purple">minHood</code><code>(</code><code style="color:purple">nbr</code><code>(</code><code style="color:green">d</code><code>) + </code><code style="color:purple">nbrRange</code><code>) }</code>
<code>  }
}</code>
</pre>

The function <code>descend</code> follows the gradient of a potential field down from a source. Given an original device (self) and a potential potential field, this function builds a path of intermediate devices connecting the original device with the source of the potential field. The original device is marked as part of the path. Other devices are identified as being on the path if one of their neighbors is already on the path, and they are the closest of that neighbor's neighbors to the destination.

<pre>
<code style="color:purple">def</code><code style="color:blue"> descend</code><code>(</code><code style="color:green">source</code><code>, </code><code style="color:green">potential</code><code>) {</code>
<code style="color:purple">  rep</code><code>(</code><code style="color:green">path</code><code> <- </code><code style="color:green">source</code><code>) {</code>
<code style="color:purple">    let</code><code style="color:green"> nextStep</code><code> = </code><code style="color:purple">minHood</code><code>(</code><code style="color:purple">nbr</code><code>([</code><code style="color:green">potential</code><code>, </code><code style="color:purple">self</code><code style="color:blue">.getId</code><code>()]));</code>
<code style="color:purple">    if</code><code>(</code><code style="color:green">nextStep</code><code style="color:blue">.size</code><code>() > 1) {</code>
<code style="color:purple">    let</code><code style="color:green"> candidates</code><code> = (</code><code style="color:purple">nbr</code><code>([</code><code style="color:green">nextStep</code><code style="color:blue">.get</code><code>(1), </code><code style="color:green">path</code><code>]));</code>
<code style="color:green">    source</code><code> || </code><code style="color:purple">anyHood</code><code>([</code><code style="color:purple">self</code><code style="color:blue">.getId</code><code>(), </code><code style="color:purple">true</code><code>] == </code><code style="color:green">candidates</code><code>)</code>
<code>    } </code><code style="color:purple">else</code><code> {</code>
<code style="color:green">      source</code>
<code>    }
  }
}</code>
</pre>

The <code>rendezvous</code> function uses the descend function to identify the path between two people, whose devices have been marked with the owner property.

<pre>
<code style="color:purple">def</code><code style="color:blue"> rendezvous</code><code>(</code><code style="color:green">person1</code><code>, </code><code style="color:green">person2</code><code>) {</code>
<code style="color:blue">  descend</code><code> (</code><code style="color:green">person1</code><code> == </code><code style="color:green">owner</code><code>, </code><code style="color:blue">distanceTo</code><code>(</code><code style="color:green">person2</code><code>) == </code><code style="color:green">owner</code><code>))</code>
<code>}</code>
</pre>

<img src="/images/peer-to-peer-1.png" alt="map with blue dots" width="500">

Imagine an initial configuration where a number of individuals carrying participating devices (blue dots) are attending a large event, and two of them (Alice and Bob, as two yellow squares) wish to meet up.

<img src="/images/peer-to-peer-2.png" alt="map with blue and a few red dots" width="500">

Alice and Bob activate their rendezvous application, and nearby devices that are on the shortest path between them start to be selected as part of their rendezvous path (red dots).

<img src="/images/peer-to-peer-3.png" alt="map with blue and more red dots" width="500">

No central information service is needed for peer-to-peer calculation of the shortest path.

<img src="/images/peer-to-peer-4.png" alt="map with blue and even more red dots" width="500">

The complete path is calculated, and will update automatically as Alice and Bob walk toward each other, and as intermediate devices move around.

For more information, see <a href="http://jakebeal.com/Publications/SAC2015-Protelis.pdf">Protelis: Practical Aggregate Programming</a>, Danilo Pianini, Mirko Viroli, Jacob Beal, ACM Symposium on Applied Computing 2015, April 2015.

<!--
Accelerated video of the Alchemist simulation of an upgradable crowd tracking application written in Protelis. Each dot is a handheld device. Devices move according to real world traces of the 2013 Vienna City Marathon (more information on this paper: https://link.springer.com/chapter/10.1007/978-3-319-03260-3_18).
All devices begin with the Protelis process scheduler installed, but with no actual application running. Black dots are devices in such status: they are not running the application. Then, a first version of the crowd tracking application is injected in a handful of randomly chosen devices. The aggregate scheduler spreads it on neighbouring nodes. Nodes running the first version of the application turn blue. Red shadow indicate overcrowding detected by the distributed application.
After some time, a second version of the crowd detection app, with a less conservative estimation of crowd danger is injected in randomly chosen devices. The aggregate scheduler spreads it around, and devices reached can upgrade their application. The red shadows shrink as a consequence of the new version of the app taking over the old, too conservative version.
More information about the application and its implementation can be found in this paper: http://ieeexplore.ieee.org/document/7274429/
Details on the Alchemist simulator are available at https://alchemistsimulator.github.io/
Details on the Protelis programming language are available at http://www.protelis.org
<video src="/images/mapehd-small-h264.mp4" width="320" height="200" controls preload></video>
-->
