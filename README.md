# Overview

This is a demo showing how to connect a Texas Instrument (TI) Sensor Tag to a node red flow running on IBM Bluemix.


## Steps to deploy the code to Bluemix

1. If you don't already have a Bluemix account, go to [http://www.bluemix.net] (http://www.bluemix.net) and sign up (it's free).

2. Log into your bluemix account

3. Navigate to the Bluemix catalog

4. Click on the "Internet of Things Foundation Starter" tile

5. Enter a unique name for your application into the "Name:" field on the right hand side

6. Click create to deploy the application on Bluemix

7. After about a minute, you should see a notice that your application is now running. Click on the blue link at the top, it should be named something like: http://TheNameThatYouChoseInStep5.mybluemix.net

8. ...


Here is the flow that will connect your TI Sensor Tag with the IoT Foundation service.

```
[{"id":"bb8da4.ff44726","type":"ibmiot in","authentication":"quickstart","apiKey":"","inputType":"evt","deviceId":"yourDeviceIDgoesHere","applicationId":"","deviceType":"+","eventType":"+","commandType":"","format":"json","name":"TI Sensortag","service":"quickstart","allDevices":false,"allApplications":false,"allDeviceTypes":true,"allEvents":true,"allCommands":false,"allFormats":false,"x":81,"y":266.9999966621399,"z":"835b45a1.7ca4b8","wires":[["8379964f.7c8668","7943faac.86bc04","c5ba0a32.3a45f8"]]},{"id":"8379964f.7c8668","type":"function","name":"Extract G-Force","func":"return {payload:msg.payload.d.gyroY};","outputs":1,"noerr":0,"x":299.5000286102295,"y":124,"z":"835b45a1.7ca4b8","wires":[["9431664d.6bce98","db50b388.24af5"]]},{"id":"9431664d.6bce98","type":"switch","name":"G-Force Threshold","property":"payload","rules":[{"t":"btwn","v":"-30","v2":"30"},{"t":"else"}],"checkall":"true","outputs":2,"x":552.5000286102295,"y":124,"z":"835b45a1.7ca4b8","wires":[["7bfbe7a0.840418"],["9368e07.f6c972"]]},{"id":"37399a5f.c8c666","type":"debug","name":"Status","active":false,"complete":"payload","x":967.5000286102295,"y":113,"z":"835b45a1.7ca4b8","wires":[]},{"id":"7943faac.86bc04","type":"debug","name":"Raw Device Data","active":false,"console":"false","complete":"true","x":286.5,"y":266.9999966621399,"z":"835b45a1.7ca4b8","wires":[]},{"id":"7bfbe7a0.840418","type":"template","name":"No fall detected","field":"","template":"G-Force ({{payload}}) within safe limits","x":779.5000286102295,"y":62,"z":"835b45a1.7ca4b8","wires":[["37399a5f.c8c666"]]},{"id":"9368e07.f6c972","type":"template","name":"Fall detected","field":"","template":"G-Force ({{payload}}) critical","x":783.5000286102295,"y":169,"z":"835b45a1.7ca4b8","wires":[["37399a5f.c8c666"]]},{"id":"db50b388.24af5","type":"debug","name":"G-Force","active":false,"console":"false","complete":"payload","x":523.9999980926514,"y":48,"z":"835b45a1.7ca4b8","wires":[]},{"id":"c5ba0a32.3a45f8","type":"function","name":"Extract Temperature","func":"return {payload:msg.payload.d.IRTemp};","outputs":1,"noerr":0,"x":298.0952434539795,"y":410.337890625,"z":"835b45a1.7ca4b8","wires":[["1f6b51d8.e094ae","57595a77.a8a6a4"]]},{"id":"f7156858.08ea98","type":"debug","name":"Status","active":false,"console":"false","complete":"payload","x":991.3809623718262,"y":381.33790922164917,"z":"835b45a1.7ca4b8","wires":[]},{"id":"1f6b51d8.e094ae","type":"switch","name":"Temperature Threshold","property":"payload","rules":[{"t":"lt","v":"27"},{"t":"else"}],"checkall":"true","outputs":2,"x":533.3809623718262,"y":380.33790922164917,"z":"835b45a1.7ca4b8","wires":[["4f7dc9bf.b08238"],["5f44c74.fa0bb38"]]},{"id":"5f44c74.fa0bb38","type":"template","name":"Temperature too high","field":"","template":"Temperature ({{payload}}) is too high!","x":814.3809623718262,"y":428.33790922164917,"z":"835b45a1.7ca4b8","wires":[["f7156858.08ea98"]]},{"id":"4f7dc9bf.b08238","type":"template","name":"Temperature safe","field":"","template":"Temperature ({{payload}}) within safe limits","x":809.3809623718262,"y":324.33790922164917,"z":"835b45a1.7ca4b8","wires":[["f7156858.08ea98"]]},{"id":"57595a77.a8a6a4","type":"debug","name":"Temperature","active":true,"console":"false","complete":"payload","x":511.3809623718262,"y":469.33790159225464,"z":"835b45a1.7ca4b8","wires":[]}]
```

You can copy the above JSON and import it into the Node-RED instance on Bluemix. (Make sure you have the IoT service you created bound to your Node-RED instance.)  Once you have imported the flow you will need to double click on the IBM IoT node to open the configuration properties and replace the device ID with the device ID you registered your Sensor Tag with in Bluemix.
