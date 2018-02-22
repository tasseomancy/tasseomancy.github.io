---
title: Network services management
layout: default
---
A common problem in managing enterprise services is a failure in one or more services can cause the overall system to get wedged, due to many dependences between different services and server. After such a failure cascade, the network can only become fully functional by restarting the servers and services in the correct order. Network services management can coordinate the order of shutdown and restarts.

Using Protelis and the principles of aggregate programming, it is straightforward to write a daemon to monitor each service and communicate with other services to execute a coordinated shutdown and restart. We illustrate how dependency-directed coordination can be implemented with very little code.

<b>Code samples and illustrations from a simulation:</b>

![diagram of seven servers with service dependency arrows](/images/dependent-services-network.png)

This example scenario features an enterprise network for a small company, with dependencies between two key databases and the internal and external servers running web applications. The scenario was implemented on a network of EmuLab servers, with the services emulated as simple query-response networking programs in Java that entered a "hung" state either by being externally triggered to crash or after their queries began to consistently fail.

Each service was wrapped with an embedded Protelis execution engine, which was interfaced with the services by a small piece of monitoring glue code that inserted environment variables containing an indentifier for the <code>serviceID</code> running on that server, a tuple of identifier for <code>dependencies</code>, and the current <code>managedServiceStatus</code> of <code>stop</code>, <code>starting</code>, <code>run</code>, <code>stopping</code>, or <code>hung</code>. The glue code also provided <code>stopService</code> and <code>startService</code> methods to send signals to the service, track interactions between the services in order to maintain the set of neighbors for Protelis, and allowed an external monitoring applications to attach and receive status reports.

![diagram of seven servers with blue, red, and green status for run, hung, and stop](/images/restart.png)

In this screenshot of the emulated network of services on machines, we see that the supplies database has crashed, triggering a graceful shutdown cascade of dependent services. Upon restart of the supplies database, the rest of the services would automatically restart in the correct order, if each service was running the following Protelis network management code.

<pre>
<code style="color:purple">let</code><code style="color:green"> nbr_set </code><code>= </code><code style="color:purple">unionHood</code><code>(</code><code style="color:purple">nbr</code><code>([</code><code style="color:blue">serviceID</code><code>]));</code>
<code style="color:purple">let</code><code style="color:green"> nbr_missing </code><code>= </code><code style="color:blue">dependencies</code><code>.</code><code style="color:blue">subtract</code><code>(</code><code style="color:green">nbr_set</code><code>);</code>
<code style="color:purple">let</code><code style="color:green"> nbr_required </code><code>= </code><code style="color:blue">#contains</code><code>(</code><code style="color:blue">dependencies</code><code>, </code><code style="color:purple">nbr</code><code>(</code><code style="color:blue">serviceID</code><code>));</code>


<code style="color:purple">def</code><code style="color:blue"> dangerousDensity</code><code>(</code><code style="color:green">p</code><code>, </code><code style="color:green">r</code><code>) {</code>
<code style="color:purple">  let</code><code style="color:green"> mr</code><code> = </code><code style="color:blue"> managementRegions</code><code>(</code><code style="color:green">r</code><code>*2, () -> { </code><code style="color:purple">nbrRange</code><code> });</code>
<code style="color:purple">  let</code><code style="color:green"> danger</code><code> = </code><code style="color:blue"> average</code><code>(</code><code style="color:green">mr</code><code>, </code><code style="color:blue">densityEst</code><code>(</code><code style="color:green">p</code><code>, </code><code style="color:green">r</code><code>)) > 2.17</code><code style="color:purple"> &&</code><code style="color:blue"> summarize</code><code>(</code><code style="color:green">mr</code><code>, </code><code style="color:green">sum</code><code>, 1 / </code><code style="color:green">p</code><code>, 0 ) > 300;</code>
<code style="color:purple">  if</code><code>(</code><code style="color:green">danger</code><code>) { high } </code><code style="color:purple">else</code><code> { low }</code>
<code>}</code>
</pre>

The function crowdTracking checks for a dangerous density in crowded areas, where 1.08 people per square meter is used as the cut-off to define crowded.

<pre>
<code style="color:purple">def</code><code style="color:blue"> crowdTracking</code><code>(</code><code style="color:green">p</code><code>, </code><code style="color:green">r</code><code>, </code><code style="color:green">t</code><code>) {</code>
<code style="color:purple">  let</code><code style="color:green"> crowdRgn</code><code> = </code><code style="color:blue"> recentTrue</code><code>(</code><code style="color:blue">densityEst</code><code>(</code><code style="color:green">p</code><code>, </code><code style="color:green">r</code><code>) > 1.08, </code><code style="color:green">t</code><code>);</code>
<code style="color:purple">  if</code><code>(</code><code style="color:green">crowdRgn</code><code>) { </code><code style="color:blue">densityEst</code><code>(</code><code style="color:green">p</code><code>, </code><code style="color:green">r</code><code>) } </code><code style="color:purple">else</code><code> { none }</code>
<code>}</code>
</pre>

The function crowdWarning alerts individuals who are near dangerously crowded spots.

<pre>
<code style="color:purple">def</code><code style="color:blue"> crowdWarning</code><code>(</code><code style="color:green">p</code><code>, </code><code style="color:green">r</code><code>, </code><code style="color:green">warn</code><code>, </code><code style="color:green">t</code><code>) {</code>
<code style="color:blue">  distanceTo</code><code>(</code><code style="color:blue">crowdTracking</code><code>(</code><code style="color:green">p</code><code>, </code><code style="color:green">r</code><code>, </code><code style="color:green">t</code><code>) == high) < </code><code style="color:green">warn</code>
<code>}</code>
</pre>
