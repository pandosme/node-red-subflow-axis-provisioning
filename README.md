# Input
msg.config
msg.inventory


# Example of msg.inventory
A list of devices that will be provisioned.  If static is set it will set static IPv4 address.
```
msg.invenetory = [
    {
        "address":"2.3.4.5",
        "properties": {
            "name": "some-hostname",
[OPTIONAL]
            "static": {  
                "address": "1.2.3.4",
                "router": "1.2.3.1"
            }
        }
    },
    {
        "address":"some.device.com",
        "properties": {
            "name": "some-hostname",
[OPTIONAL]
            "static": {
                "address": "1.2.3.5",
                "router": "1.2.3.1"
            }
        }
    }
]
``` 


# Example msg.config
The config holds all configuration. You can remove properties that you do not want or set them false.  Values with empty data will not be commited to the provisioning.


## accounts
Up to three accounts can be set.  The first is the main account that will be used for all configurations.
If camera is factory default, the device will be initiated with the main account.

``` 
msg.config.accounts = [
        {
            "name": "root",
            "password": "pass",
            "privileges": "Admin"
        },
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

## timezone
Set the device timezone.  Lookup on https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

``` 
msg.config.timezone = "Europe/Stockholm"
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
[OPTIONAL]
   "minfps": 15 
}
```

## tls
Set the cconnection policy
Values are "Http","Https" & "HttpAndHttps"
Connection poicy.  Values may be "Http","Https" & "HttpAndHttps"

msg.config.tls = {
        "policy": "HttpAndHttps"
}

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
