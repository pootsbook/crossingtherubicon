---
title: "Setting and Clearing Intervals in the browser"
layout: post
---

When setting up two-way communication over websockets with Phoenix Channels recently I wanted to poll the server until a given event (on the server) had occurred, and then stop. I’ll understand if you start scratching your head and asking why on earth I’d want to poll when a websocket allows me to send notification of said event from the server directly. In this case the polling (via the websocket/channel) is a safety net for instances where the server-side event has happened before, or during, the page request meaning that the server-side event would have been broadcast to no-one. In other cases an exception might prevent the broadcast from being called.

The options for doing this from the Web API off the top of my head were `setTimeout` and `setInterval`. The latter seemed the best option for this case, however there were two constraints which eventually expanded my knowledge.

The first constraint was that I wanted to ensure the polling would stop once the event had been received. I was always working in the ignorance that a `setInterval` would continue until the browser browsed to another page or the tab/window was closed. It turns out that you can store a reference to the interval by assigning the result of the call to a variable:

```js
var intervalId = setInterval(messageServerFn, 5000)
```

And I learned that that interval reference comes in handy when you want to stop it. There is a related function in the Web API named `clearInterval`. This function takes an interval reference as a parameter:

```js
clearInterval(intervalId)
```

And voilà, the function passed to `setInterval` will no longer be called. Neat.

The trouble with `setInterval` is that it first runs only _after_ the interval length. In our case above, with a value of five thousand milliseconds, the function will be called for the first time only after five seconds. I wanted the function to be called immediately. This was my second constraint.

It turns out there a few ways of doing this. The most straightforward was not immediately obvious to me given it was so simple, but just calling the function independently of the call to `setInterval` does the trick.

```js
messageServerFn()
var intervalId = setInterval(messageServerFn, 5000)
```

No point in making it more complicated than it is.

—*Monday, 20th June 2022.*


<div class="references">
  <h3>References</h3>
  <ul>
    <li><a href="https://developer.mozilla.org/en-US/docs/Web/API/setInterval"><code>setInterval</code></a></li>
    <li><a href="https://developer.mozilla.org/en-US/docs/Web/API/clearInterval"><code>clearInterval</code></a></li>
    <li><a href="https://stackoverflow.com/questions/10563344/how-to-start-setinterval-loop-immediately">How to start the setInterval loop immediately?</a></li>
    <li><a href="https://stackoverflow.com/questions/6685396/execute-the-setinterval-function-without-delay-the-first-time">Execute the setInterval function without delay the first time</a></li>
  </ul>
</div>
