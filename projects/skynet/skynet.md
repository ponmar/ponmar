# Skynet (2013-2015)

## About

Skynet is a home security and home automation system. It can be used for collecting sensor data, visualizing system events, to control home devices and to communicate with third party services. The Skynet software is developed to be easily extended with new functionality. It was initially developed for Raspberry Pi, but has then been adapted for general purpose use.

## Security & Surveillance

Skynet supports many security and surveillance related features, such as video monitoring, camera capturing, motion detection and other intrusion detection and notification features.

The system is easily extendible with, for example, monitors visualizing system events or live camera images. Such terminals can also be used for controlling the system.

See [Details](#details) for a complete list of features.

## Home Automation

Skynet can be used to control power outlets to, for example, light up an area when a motion is detected, manually controlling lights or start a radiator when the temperature gets too low.

The following home automation devices are supported:

* Power outlets can be used to toggle any equipment on/off, but are mostly used for controlling lights
* Electronics connected via Raspberry Pi GPIO interface
* Simulated devices for testing and demo purposes

Home automation devices can be controlled via the user interfaces and by actions connected to any sensor.

Weather reports are collected from different providers. This data can also be used for monitoring or home automation.

See [Details](#details) for a complete list of features.

## User Interfaces

Each user interface presents events, images, live camera streaming, weather reports and advanced system details. Home automation devices can also be controlled.

These are the Skynet user interfaces:

* A web service which presents a user friendly graphical user interface (GUI) that can be used from computers, phones and other mobile devices
* A command line interface (CLI) for using the system specific shell. Secure Shell (SSH) is used to get secure access to Skynet via untrusted networks such as the Internet. Telnet can be used on a trusted network (if SSH can not be used)
* Example applications for monitoring and controlling the system, written and Java and Python, can be modified for your needs. See API for documentation and source code.
* Email (scheduled or triggered by a sensor)
* SMS (scheduled or triggered by a sensor)

By using the API it is possible to create new user interfaces and services.

## Privacy

This system is developed with your privacy in mind. No information is sent to other parties except your own. Collected information is stored locally (not in the cloud).

The most secure way to spread system information is to use the API (instead of sending emails, SMS text messages, etc). However, there are plenty of alternatives to choose from.

## Use Case Examples

Here are a few use case examples to get a basic understanding of the system features:

* Motion detection, that generates events when the system is armed, and turns on a light for a few minutes
* Generate an event with major severity when a door is opened if the system is armed
* Send an email/SMS (to your family, neightbors, etc.), turn on lights and activate a siren when a major event is detected
* Turn on lights at sunset and turn off lights at 22:30 (no need to reconfigure due to season)
* Send system information to a remote server
* Notify users, and control the system, via Skynet Remote or custom applications

See [Details](#details) for a complete list of features.

## Details

An event can be generated when a system notification is needed. The event can be shown for the user in several ways (see user interfaces). It includes a timestamp, description, severity and attached images from cameras. An event may cause a number of actions to be performed, depending on the system configuration.

A sensor is a concept of something that can be triggered (hardware or software). When it is triggered, the framework will call all actions connected to this sensor class. Sensor listing:


* Arm/disarm sensor
* Linux webcam image capturer via v4l2
* Windows webcam image capturer via OpenCV
* Network camera image capturer (HTTP)
* Raspberry Pi GPIO input (from buttons, switches or custom electronics)
* Network sensor for receiving GPIO input from another Raspberry Pi
* Camera based motion detection
* Image pattern sensor
* Wireless Tellstick attached devices (triggers on input from Nexa remote controls, buttons, motion detectors, temperature and humidity sensors, etc.)
* Home automation device state sensor (is triggered when the device is turned on/off)
* Scheduled tasks (for example triggered 12:00 every day or every 30th seconds)
* Event detector (detects new events with given severity)
* Event severity changed (happens when an event is accepted)
* Weather sensors for temperature, precipitation, wind speed and pressure
* Sunrise and sunset sensor
* Ping sensor for checking if any kind of network equipment is alive
* SMS balance sensor for notifying when the user needs to transfer money to the cloud service (46elks)
* HTTP server result code for a given URL
* Hostname resolver for testing DNS
* IP address changed for host (useful with a dynamic DNS service to inform about that a new IP address was registered)
* External program/script exit code
* File changed sensor
* Directory content changed sensor
* Logged in users sensor
* CPU load sensor
* CPU temperature sensor
* System restart sensor to detect power outage and measuring uptime
* Sensor group sensor that is triggered when a group of sensors has been triggered
* Sensor watchdog that is triggered when another sensor has not triggered for a given time period (can, for example, be used to make sure that a camera is capturing images or that a motion detection sensor is not out of battery)
* Service job queue length (check for all or a specific service)

An action is functionality that is executed when a sensor has been triggered. The same action can be added to several sensors. Action listing:

* Generate events with text, severity and optional live camera images or images from a motion detection sensor
* Email senders (triggered sensor info, system health check, complete overview, or customized emails)
* SMS sender via modem or cloud service (46elks)
* Turn on/off home automation devices (such as power outlets and Raspberry Pi GPIO connected electronics)
* Turn on home automation devices for an amount of time (useful in combination with a motion detection sensor)
* Raspberry Pi GPIO output control (PIR-sensors, sirens, LEDs or custom electronics)
* Normal/delayed arm and disarm
* Send system information (such as XML data, live camera images or event images) to a remote FTP server, with optional TLS authentication
* Run external command
* Play local sound
* Remote VLC media playback (sound, video or Skynet live/event images) via the RC (Remote Control) VLC interface
* Sensor history logger
* Mute sensors (skip sensor triggering while muted)
* Network data sender (can for example be used to forward Raspberry Pi GPIO input to another system)
* HTTP URL access to trigger actions on a remote HTTP server
* Telnet notifier action (sends a message to all connected telnet clients)
* Show overlay message on set-top box
* Broadcast UDP data to trigger network attached equipment on the local network
* Message logger (logs to internal cyclic log that is accessible from the user interfaces)

Some tasks require to run in the background (in their own thread of execution). In Skynet such tasks are called services. Each service has its own job queue to work with. They will run in parallell with the main thread, and will therefore not delay, for example, motion detection. Many of the actions above will just add a task to a service job queue.

## API

The Skynet API can be used by software developers to create applications that uses information from Skynet (XML/JSON formatted data) or to control the system from a remote host.

The API uses SSH to access the system shell, and can therefore be used securely via untrusted networks such as the Internet.

There are helper classes, for Java and Python, that provides an easy-to-use interface for the Skynet shell API. There are some example applications for Python and Java for how to use the API helper classes (see downloads).

This API can be used to build adapters between Skynet and third party systems. It can also be used to provide custom user interfaces.

A network discovery service (UDP signalling via broadcast address) can be used to find Skynet devices on the local network.

Python API example usage:

```python
import skynetapi
api = skynetapi.SkynetAPI(host='example.com',
                          port=22,
                          protocol=skynetapi.SSH)
eventsJson = api.getEventsJson()
...

api.close()
```

Java API example usage:
```java
import se.markstrom.skynet.api.SkynetAPI;

public class Example {
	public static void main(String[] arg) {
		SkynetAPI api = null;
		
		try {
			String host = "example.com";
			int port = 22;
			String password = "password";
			SkynetAPI.Protocol protocol = SkynetAPI.Protocol.SSH;
			
			api = new SkynetAPI(host,
			                    port,
			                    protocol,
			                    password,
			                    false);

			String eventsXml = api.getEventsXml();
			
			...
			
		}
		catch (SkynetAPI.SkynetAPIError e) {
			e.printStackTrace();
		}

		if (api != null) {
			api.close();
		}
	}
}
```

## Downloads

The Skynet system is not available for download, but API helper classes and example software that uses the API can be downloaded here:

* The Skynet API helper classes and example applications (Python and Java) can be downloaded here: [SkynetAPI-9.0.tar.gz](SkynetAPI-9.0.tar.gz)
* Skynet Remote is a GUI for remote supervision and control of a Skynet system (via the API). Source code and releases are available for download at the [github project page](https://github.com/ponmar/skynetremote).


