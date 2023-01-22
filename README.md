This was my winning project for [State Machine Hackathon by Stately and ODevs](https://www.eventbrite.com/e/state-machine-hackathon-by-stately-and-odevs-tickets-471547420027?aff=ebdssbcitybrowse)

# DensityMeterHelper
A tool that uses a state machine that helps people properly use density meters.

You can demo it on my website [here](https://jaked.co/demo).

---

#### Tools Used

[Stately.ai](https://stately.ai) was used to create the logic behind the 
state machine.

[Vue 2](https://v2.vuejs.org/v2/guide/) was used for the frontend.

#### Real Life Use Cases

There can be a backend added to this project, with some logic changes 
depending on use case, to help integrate this frontend to a PLC system.

This way, it can prevent improper use of industrial equipment.

#### Logic

Here is the logic from Stately:

![Logic Screenshot](./logic.png)


#### Notes for future development

In the future, it would be nice if we could dynamically move around devices and choose which devices first need to be on before turning on another device, and the same for turning off devices.

Also, creating some backend to control devices would be nice, to integrate into a PLC. A good example for this would be if we click "Turn on X Device", it would either (1) turn on that device if it can be turned on, OR, with a more advanced state machine, turn on the needed devices in order to turn on the requested device.
