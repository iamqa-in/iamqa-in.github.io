title: The IoT Testing Atlas
date: 2015-10-04 22:39:58
categories:
- IoT
tags:
- IoT
- Testing
- Atlas
- Frameworks
---
I recently worked on a product, where a mobile app talks to a connected machine. The various parameters and states that these two devices can be in made it particularly challenging while coming up with test scenarios.

I would like to present a framework which I found useful for managing the permutations of parameters while testing an IoT (Internet of Things) based product. I call it, *The IoT Testing Atlas*.

# IoT Testing Parameters

Let us consider some of the common set of states/variants that a tester would be testing in a simple web application:
* Server Down
* Http Timeouts
* Slow Networks
* Authorization & Authentication Errors
* ...

So any application on the internet, needs to be tested for these 4+ states.

Now, consider a mobile application. The mobile device brings its own additional set of states/variants into picture, like the following:

* Offline mode
* Online mode
* Activity Kill
* Background behavior
* Languages
* Location
* ...

Let us consider some of the simple parameters that a “connected machine” would bring in.

* Machine Wifi Off
* Machine Wifi on
* Machine is Busy
* Machine is Sleeping
* ...

This means that, even with the given set of example parameters, there are about `4*6*4` states the entire system can be at any point in time! Each of these states cannot be treated as a stand-alone entity, as the state transition within a system brings some more additional constraints. For example, state change from *offline* to *online* probably needs to trigger a set of events.

The above set of parameters are only the tip of the proverbial iceberg. As we get deeper into the specifics, we may get overwhelmed while trying to connect these different states into logical scenarios. Thus, arose the need for the IoT Testing Atlas.

# The IoT Testing Atlas

Most of us would have played with an Atlas to keep up with our Geography classes :).  Atlas as a concept, in this context, mainly helps to view all of the `n*n*n` parameters at a glance, and hence derive more meaningful scenarios for testing a particular feature.

Each system in the product is captured as a circle with its n states, and the logical next states are placed close to each other. These circles are to be considered as rotatable entities. As you can see, I have a separate circle for the **NFRs** too, as these may slip out of our minds easily while testing such complex integrations.

The following image shows my idea of the IoT Testing Atlas

{% asset_img iot_testing_atlas.png [IoT Testing Atlas] %}

Let us use the Atlas and derive logical scenarios for some features which involve only mobile and machine interaction. This means our focus circles are the *Device*, *Machine* and *Network* circles.

1. Fix the mobile device and machine at *Wifi On* state. On rotating the Network circle, we get the following scenarios:
  - *Unauthorized* user tries to access the machine. Hence show “Access Denied” error message on app.
  - *Server down and server errors* should show appropriate business error messages say “Something went wrong. Try again later.”
  - *Response Timeout* could either re-trigger the same request with the app showing prolonged loading icons or show similar error message as above.
  - *Invalid request* should trigger messages like “Your app needs to be updated”.
2. Continue to keep the mobile device on *Wifi On*, and move the machine circle one by one.
  - When machine is in *offline* mode, app needs to indicate the user to “Check the machine for network connections”
  - When machine is *Busy*, the required message “Unable to complete your request as the machine is busy” needs to popup.
  - When machine is *sleeping* or in *some other network*, then “Machine not found” or similar messages should be shown.
  - Now, switching the machine to the right network should enable the connection between mobile and machine again.
3. Now, fix the machine circle to *Wifi On* and rotate the mobile circle to find more scenarios
  - Mobile goes *offline*, then appropriate messages/behavior (like disabling the button etc) should come up.
  - Mobile comes *online*, appropriate calls should go from the app to make connection with the machine
  - Mobile *switches network* Wifi – 3G. What should happen?
  - User gets a call and puts app in *background* while performing a task. Should they still see the completed request, or back to square one?
  - Android *kills* the app which is in background for a while. Do we save the user's last screen status?
  - Apps with *Localization* scope needs verification at every scenario level.

This can be extended to many more scenarios as we perform multiple *rotations* of these circles. Some may not fit the current feature, and some may not even be relevant to the business needs. At the end of the day, we know we have not missed any state as we finish the circles!

This framework can be extended to many more systems in a product. Moreover, when there are more than one QA on a team, this framework provides a common understanding and approach for testing the product.
