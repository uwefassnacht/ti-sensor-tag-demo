## Overview

This is a demo showing how to connect a [Texas Instrument SimpleLink SensorTag] (http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/?INTC=SensorTag&HQS=sensortag) via the [IBM Internet of Things Platform] (https://internetofthings.ibmcloud.com/#/) to a [Node-RED](http://nodered.org/) flow running on [IBM Bluemix](http://www.bluemix.net).

## Architecture

The TI SensorTag connects via Bluetooth to an app on your smartphone. Your phone, in turn, is connected via Wifi or cellular data to the internet and transmits the SensorTag data into an application running on IBM Bluemix.

<img align="center" src="images/architecture-diagram.jpg">

## Prerequisites

<img align="right" src="images/SensorTag.jpg">

You will need a TI SimpleLink SensorTag, which can be bought in various online shops for about $25 or [directly from TI](http://www.ti.com/tool/cc2650stk?keyMatch=sensortag&tisearch=Search-EN-Everything).

Next, use [this tutorial](https://developer.ibm.com/recipes/tutorials/connect-a-cc2650-sensortag-to-the-iot-foundations-quickstart) to get your SensorTag ready to send data to the IBM IoT cloud, so that we can use it in a Bluemix application.

**Note :** If your SensorTag has an older firmware version, you might need to update it in order for the sensors to stream data correctly. This can be done via the smartphone app. Version 1.12 (or later) seems to work fine.

Make sure that you slide the "Sourcing" slider in the smartphone app to the "on" position. You can view the ID of your SensorTag bei either opening the link at the top of the "Cloud View" or using the "share" icon to send it in a mail to yourself. The device ID should an alphanumeric string of 12 characters. You will need it later, specifically in step 13 below.

If you **do not have a SensorTag**, you can use any modern smartphone as a "fake" sensor. Just navigate from your phones browser [to this page here](https://quickstart.internetofthings.ibmcloud.com/iotsensor/). The page you just opened will "pretend" to be a temperature and humidity sensor. Here is a [link to a short tutorial] (https://developer.ibm.com/recipes/tutorials/use-the-simulated-device-to-experience-the-iot-foundation/) that will give you more background. The ID of your fake sensor will be displayed on the upper right of the page you opened on your smartphone. It's an alphanumeric string of 12 characters, which you will need later, specifically in step 13 below.

## Steps to deploy Node-RED on Bluemix

**Step 1:** If you don't already have a Bluemix account, go to [http://www.bluemix.net] (http://www.bluemix.net) and sign up (it's free).

**Step 2:** Log into your bluemix account.

**Step 3:** Navigate to the Bluemix catalog.

**Step 4:** Click on the "Internet of Things Platform Starter" tile. It's in the "Boilerplate" section towards the top of the catalog.

**Step 5:** Enter a unique name for your application into the "Name:" field on the right hand side.

**Step 6:** Click "create" to deploy the application on Bluemix.

**Step 7:** After a minute or two, you should see a notice that your application is now running. Click on the blue link at the top, it should be named something like: http://TheNameThatYouChoseInStep5.mybluemix.net .

**Step 8:** The above should lead you to a page with the title "A visual tool for wiring the Internet of Things IBM Watson IoT Platform". It has a big red button "Go to your Node-RED flow editor" in the lower right. Click on it.

**Step 9:** You are now be in your Node-RED flow editor. It is already populated with a few nodes. Mark them all with Ctrl-a and then delete them (using the backspace key). We will start with a clean canvas.

## Steps to import the Node-RED flow

**Step 10:** Here is the flow that will connect your TI Sensor Tag with the IBM IoT Platform service. Select all of the JSON below and copy it into your clipboard.

```JSON
[{"id":"ed61d018.129e3","type":"ibmiot in","z":"ae9da6ed.516258","authentication":"quickstart","apiKey":"","inputType":"evt","deviceId":"yourDeviceIDgoesHere","applicationId":"","deviceType":"+","eventType":"+","commandType":"","format":"json","name":"TI Sensortag","service":"quickstart","allDevices":false,"allApplications":false,"allDeviceTypes":true,"allEvents":true,"allCommands":false,"allFormats":false,"x":129,"y":284,"wires":[["8296485d.7d69b8","f9750219.068b","411bd2b1.bee42c"]]},{"id":"8296485d.7d69b8","type":"function","z":"ae9da6ed.516258","name":"Extract G-Force","func":"if (msg.payload.d.accelX) {\n\n    var gForce = ({payload:msg.payload.d.accelX});\n\n} else {\n   \n   // If there is no accelX info, assume gForce of 0 \n    var gForce = { payload: '0' };\n}\n\n\nreturn gForce;","outputs":1,"noerr":0,"x":347.5000286102295,"y":141.0000033378601,"wires":[["35be0401.ca41fc","7f445870.80bba8"]]},{"id":"35be0401.ca41fc","type":"switch","z":"ae9da6ed.516258","name":"G-Force Threshold","property":"payload","propertyType":"msg","rules":[{"t":"btwn","v":"-1.2","vt":"num","v2":"0.8","v2t":"num"},{"t":"else"}],"checkall":"true","outputs":2,"x":600.5000286102295,"y":141.0000033378601,"wires":[["11abf765.ee5409"],["be1b3529.41e4c8"]]},{"id":"6c5a363c.93a5c8","type":"debug","z":"ae9da6ed.516258","name":"Status","active":true,"complete":"payload","x":1015.5000286102295,"y":130.0000033378601,"wires":[]},{"id":"f9750219.068b","type":"debug","z":"ae9da6ed.516258","name":"Raw Device Data","active":false,"console":"false","complete":"true","x":334.5,"y":284,"wires":[]},{"id":"11abf765.ee5409","type":"template","z":"ae9da6ed.516258","name":"No fall detected","field":"","template":"G-Force ({{payload}}) within safe limits","x":827.5000286102295,"y":79.00000333786011,"wires":[["6c5a363c.93a5c8"]]},{"id":"be1b3529.41e4c8","type":"template","z":"ae9da6ed.516258","name":"Fall detected","field":"","template":"G-Force ({{payload}}) critical","x":831.5000286102295,"y":186.0000033378601,"wires":[["6c5a363c.93a5c8"]]},{"id":"7f445870.80bba8","type":"debug","z":"ae9da6ed.516258","name":"G-Force","active":false,"console":"false","complete":"payload","x":571.9999980926514,"y":65.00000333786011,"wires":[]},{"id":"411bd2b1.bee42c","type":"function","z":"ae9da6ed.516258","name":"Extract Temperature","func":"if (msg.payload.d.objectTemp) {\n\n    var temp = ({payload:msg.payload.d.objectTemp});\n\n} else {\n   \n   // If there is no objectTemp info, assume temperature of 18 \n    var temp = {payload: '18.00'};\n}\n\n\nreturn temp;","outputs":1,"noerr":0,"x":346.0952434539795,"y":427.3378939628601,"wires":[["b0e6d8c0.4f1928","d9d3d209.262c3"]]},{"id":"1906a6c6.e6f959","type":"debug","z":"ae9da6ed.516258","name":"Status","active":true,"console":"false","complete":"payload","x":1039.3809623718262,"y":398.3379125595093,"wires":[]},{"id":"b0e6d8c0.4f1928","type":"switch","z":"ae9da6ed.516258","name":"Temperature Threshold","property":"payload","propertyType":"msg","rules":[{"t":"lt","v":"27","vt":"str"},{"t":"gte","v":"27","vt":"str"}],"checkall":"true","outputs":2,"x":581.3809623718262,"y":397.3379125595093,"wires":[["91846330.6e7ba"],["828914fc.7d76e8"]]},{"id":"828914fc.7d76e8","type":"template","z":"ae9da6ed.516258","name":"Temperature too high","field":"","template":"Temperature ({{payload}}) is too high!","x":862.3809623718262,"y":445.3379125595093,"wires":[["1906a6c6.e6f959"]]},{"id":"91846330.6e7ba","type":"template","z":"ae9da6ed.516258","name":"Temperature safe","field":"","template":"Temperature ({{payload}}) within safe limits","x":857.3809623718262,"y":341.3379125595093,"wires":[["1906a6c6.e6f959"]]},{"id":"d9d3d209.262c3","type":"debug","z":"ae9da6ed.516258","name":"Temperature","active":false,"console":"false","complete":"payload","x":559.3809623718262,"y":486.33790493011475,"wires":[]}]
```

**Step 11:** Next import the flow into the Node-RED instance on Bluemix.

- Click on the menu on the upper right of the Node-RED user interface

- Navigate to "Import" and then "Clipboard"

![Importing from clipboard](images/import-from-clipboard.jpg)

- Paste the content of your clipboard (which should contain the flow that you copied in step 10 above) into the window and click on "Ok"

![Paste flow into this window](images/import-window.jpg)

**Step 12:** You should now see the imported flow in your Node-RED editor.

![Imported NODE-RED Flow](images/screenshot-node-red-flow.jpg)

**Step 13:** Once you have imported the flow you will need to double click on the IBM IoT node (labelled "TI Sensortag" in the screenshot above) to open the configuration properties and replace the device ID with the device ID of your specific SensorTag (see the Prerequisites section above). Click "Ok" to finish the configuration of the node.

**Step 14:** Click on the red "Deploy" button (upper right) to deploy your flow to Bluemix. You will need to do this each time you change the flow.

**Step 15:** Activate any of the green debug nodes to see data (e.g. temperature or acceleration) flowing from your SensorTag. You will need to click on the "debug" tab on the upper right to see it.

**Done**

You can now play with the various debug nodes on your canvas. How about changing the threshold values in the yellow nodes? Or maybe adding new nodes to your flow by dragging them in from the palette on the left? Nodes are connected through simple clicking and dragging.

Don't forget to hit the "Deploy" button after every change you make in the flow!

Start playing and have fun!

## Next Steps

You might want to secure your Node-RED flow with a password or add more nodes to the palette. [This page](https://console.ng.bluemix.net/docs/starters/IoT/iot500.html#iot500) explains how it's done.

To search for new nodes to integrate, head over to the [Node-RED website](http://flows.nodered.org). The page also has a collection of interesting flows, which can easily be mixed and matched with your ideas.

Maybe you'd like to publish your sensor data via a REST API? Or display it on a beautiful dashboard? [Here is a good tutorial](https://www.hackster.io/adrianf/create-a-multi-tasking-iot-wi-fi-sensor-9d7fdf), showing you exactly how it's done (make sure to start at Step 4).

Or head on over to Github, where you'll find many interesting repos. How about [using your SensorTag in an alarm system](https://github.com/chrrel/bluemix-alarm-system)?


## Contribute
I'm more than happy to accept external contributions to this project, be it in the form of issues or pull requests.

If you find a bug, please report it via the [Issues section][issues_url] or even better, fork the project and submit a pull request with your fix!


## Things don't work? Here is where to get help

Maybe your internet connection does not allow websocket traffic (which I've seen when connected within some firewalls or when using hotel Wifi). To check whether websockets work for you, head over [here](http://websocketstest.com/).

Before asking questions, make sure to consult the documentation. Here is the [link to the general Bluemix docs] (https://www.ng.bluemix.net/docs), you'll find the [docs for the IBM IoT Platform service here] (https://docs.internetofthings.ibmcloud.com).

If you have technical questions about Bluemix or the IoT Platform service, head on over to [Stackoverflow] (http://stackoverflow.com/questions/tagged/bluemix) and make sure to tag your question with "Bluemix".

Another good resource is dW Answers, specifically the "Internet of Things" section [here](https://developer.ibm.com/answers/smartspace/internet-of-things).

The [SimpleLink Support Forum] (http://e2e.ti.com/support/wireless_connectivity/f/538) is a great place to get questions answered that pertain directly to the SensorTag itself.

For demo and debug purposes, the [quickstart page](https://quickstart.internetofthings.ibmcloud.com/iotsensor/) shows the values sent by the sensor, which allows you to verify quickly which data is being produced by the (fake or real) sensor. Just enter the sensor's id in the form field and you'll see a graph of the data being produced by the sensor. This is the same stream that is used by the Node RED flow.

# License

See [LICENSE](LICENSE) file.

[issues_url]: https://github.com/uwefassnacht/ti-sensor-tag-demo/issues
