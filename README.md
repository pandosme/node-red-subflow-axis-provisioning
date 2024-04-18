# Axis Device Provisioning
This is a subflow published as an Node-RED node that is used to simplify multi-device provisioning/staging and mass-configuration when it is not possible to use [AXIS ADM](https://www.axis.com/support/tools/axis-device-manager) or [ADMx](https://www.axis.com/products/axis-device-manager-extend).

## Usage
You must import/update [node-red-contrib-axis-com](https://flows.nodered.org/node/node-red-contrib-axis-com) as this subflow depnds on it.  
<br/>
The subflow requires two input properties:
1. msg.config
2. msg.inventory

You can import a [Template Flow](https://github.com/pandosme/CamFlows/blob/master/flows/Provisioning%20Template.json) as a starting point, example and inspiration.

There are three outputs:
1. Status: A message when each configuration in a device is set
2. Complete: When a device is configured
3. Error: A message on every error that may occur


# Example of msg.inventory
A list of devices that will be provisioned.  If static is set it will set static IPv4 address.

```
msg.invenetory = [
    {
        "address":"2.3.4.5",
        "properties": {
            "name": "some-hostname",
[OPTIONAL: Set static IP address]
            "static": {  
                "address": "1.2.3.4",
                "router": "1.2.3.1"
            }
        }
    },
    {
        "address":"some.fqdn.org",
        "properties": {
            "name": "some-hostname",
[OPTIONAL: VAPIX Request only for this device]
            "vapix:" [
                {
                    "method": "post",
                    "cgi": "/axis-cgi/param.cgi",
                    "body": "action=update&root.ImageSource.I0.Rotation=180"
                }
            ]
        }
    }
]
``` 


# Example msg.config
The config holds all configuration. You can remove properties that you do not want or set them to false.  Values with empty data will not be commited to the provisioning nor will they produce an error.


## accounts
Up to three accounts can be set.  The first account is the main account that will be used for all configurations.  This means that the device must have this user/password. If the device is at factory default state, the device will be initiated with the main account.

``` 
msg.config.accounts = [
        {
            "name": "root",
            "password": "pass",
            "privileges": "Admin"
        },
[OPTIONAL: Add additional accounts]
        {
            "name": "account2",
            "password": "account2",
            "privileges": "Operatror"  
        },
        {
            "name": "account3",
            "password": "account3",
            "privileges": "Viewer"  
        }
]
``` 

## time
Set the device timezone and NTP server.  Lookup on https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

``` 
msg.config.time: {
    timezone: "Europe/Stockholm",
[OPTIONAL: Specific NTP servers]
    ntp: ["ntp.pool.org"]
}

``` 

## zipstream 
Set zipstream and minimum fps
Values: 
* Disable = "off"
* Low = 10
* Medium = 20
* High = 30
* Higher = 40
* Extreme = 50

``` 
msg.config.zipstream = {
    "strength": 20,
[OPTIONAL: Minimum FPS on low motion]
   "minfps": 15 
}
```

## tls
Set the cconnection policy
Values are "Http","Https" & "HttpAndHttps"
Connection poicy.  Values may be "Http","Https" & "HttpAndHttps"

```
msg.config.tls = {
        "policy": "HttpAndHttps"
}
```

## textOverlays
Up to two text overlay can be set

```
[
    {
        "text": "%F %X",
        "position": "topLeft",
        "textColor": "white",
        "textBGColor": "black",
        "fontSize": 48
    },
    {
        "text": "#n",
        "position": "topRight",
        "textColor": "white",
        "textBGColor": "transparent",
        "fontSize": 48
    }
]
```

## imageOverlay
The image file needs to stored under directory Provisioning/smiley.png.
Node-RED on Windows: C:\\Users\\user\\Provisioning\smiley.png
Node-RED on Linux: /home/user/Provisioning/smiley.png

```
msg.config.imageOverlay = {
    "filepath": "Provisioning/smiley.png",
    "position":"bottomRight",
    "scale": true
}
```
    
## onvif
Up to three ONVIF Accpunts can be set

```
[
    {
        "name": "user1",
        "password": "user1",
        "privileges": "Administrator"
    },
    {
        "name": "user2",
        "password": "user2",
        "privileges": "Administrator"
    }
]
```

## mqtt
Set the device MQTT client.  It is also possible to set mqtt publishing


```
msg.config.mqtt = {
    "address":"",
    "user": "",
    "password":"",
[OPTIONAL]	
    "publish": [
		{
			"qos": 0,
			"retain": "none",
			"topicFilter": "axis:CameraApplicationPlatform/ObjectAnalytics/Device1Scenario1"
		},
		{
			"qos": 0,
			"retain": "none",
			"topicFilter": "axis:CameraApplicationPlatform/ObjectAnalytics/Device1Scenario2"
		}
	]
}
```

## ssh
Enable or disable SSH

```
msg.config.ssh = {
    "name": "",
    "password":""
}
```

```
msg.config.ssh = false
```

## acaps
Installs and starts ACAPS. The eap-files needs to be store on local directory.  If the device already has the ACAP installed with the version defied in payload, it will not install, just start.

Configuration is planned to be supported

```
msg.config.acap = [
	{
        "package": "motionguard",  //Must be the correct package id
        "version": "2.3.2",
        "armv7hf": "Provisioning/MotionGuard/armv7hf.eap",
        "aarch64": "Provisioning/MotionGuard/aarch64.eap",
[OPTIONAL]        
        "config": null
    }
]
```
## Vapix
It is possible to add VAPIX API request for settings not supported in the config.
The VAPIX response is available in the status output wiht msg.response.

```
msg.config.vapix = [
  {
    "method": "get",
    "cgi": "/axis-cgi/param.cgi?action=update&ImageSource.I0.Color=50"
  },
  {
    "method": "post",
     "cgi": "/axis-cgi/basicdeviceinfo.cgi",
     "body": "{\"apiVersion\": \"1.0\",\"context\":\"Node-RED\",\"method\": \"getAllProperties\"}"
  }    
]
```

# Change log

### 1.1.5
- Added support for device specific VAPIX requests in inventory.properties.vapix[]

### 1.1.4
- Bug fixes

### 1.1.3
- Fixed clumsy mistake that prevented the node to run

### 1.1.2
- Fixed flaw in Overlay
- Added exemple flow to download ACAP
- Added example flow to upload a CSV into Node-RED and parse it.
  Useful for processing into msg.inventory

### 1.1.1
- Fixed a flaw in the example code

### 1.1.0
- Adjust config.time to support ntp server
- Added support for generic VAPIX request

### 1.0.0
- Initial commit
