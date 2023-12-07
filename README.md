![shutterstock_1864450102-scaled](https://github.com/Anmirazik/EV-Charger-Solution/assets/113960675/5fd4a4ee-98e4-40c5-8212-6c9550766597)

# EV-Charger End to End Solutions
So you might be wondering how can you create end-to-end EV Charging Software using open source.

Good for us, there are genius developers out there who have published their solutions on Github, we just need to find and use it only. However, in this article, I will focus on integrating Everest Software In The Loop with steve Backend

## What do you need? 
### Everest 
1. You can refer to the Everest documentation on how to install Everest [here ](https://everest.github.io/nightly/)

### Steve
1. You can refer how to install the steve Backend Server [here](https://github.com/steve-community/steve)

## What you need to configure at steve
steve looks like this the first time you access the browser
![image](https://github.com/Anmirazik/Open-Source-Ev-charger-End-to-End-Solution/assets/113960675/75029101-9acc-46c2-868a-97c0280dd109)

you need to create a charge point bly click on the number of charge point - add new

and it should pop up like this 

please remember to add the ChargeBox ID same as in the Everest configurations , here the default ChargeBox ID is cp001

![image](https://github.com/Anmirazik/Open-Source-Ev-charger-End-to-End-Solution/assets/113960675/8922479e-748f-4db8-8b35-43c81486cda8)

The output should look something like this 
~~~
/OCPP16/63c937b82b1ed7174101f323/6569e25a32a12adfd538d383
~~~
## What you need to configure at Everest 
First of all SSH into your Linux system, as for me, I use Ubuntu 22.04 and then change the directory into everest-core, As for me once I log in I need to run this 
~~~
sudo nano ~/checkout/everest-workspace/everest-core/build/dist/share/everest/modules/OCPP/config-docker.json
~~~
my "config-docker.json" contents looks like this 
~~~
{
    "Internal": {
        "ChargePointId": "cp001",
        "CentralSystemURI": "127.0.0.1:8180/steve/websocket/CentralSystemService/cp001",
        "ChargeBoxSerialNumber": "cp001",
        "ChargePointModel": "Yeti",
        "ChargePointVendor": "Pionix",
        "FirmwareVersion": "0.1",
        "AllowChargingProfileWithoutStartSchedule": true
    },
    "Core": {
        "AuthorizeRemoteTxRequests": false,
        "ClockAlignedDataInterval": 900,
        "ConnectionTimeOut": 30,
        "ConnectorPhaseRotation": "0.RST,1.RST",
        "GetConfigurationMaxKeys": 100,
        "HeartbeatInterval": 86400,
        "LocalAuthorizeOffline": false,
        "LocalPreAuthorize": false,
        "MeterValuesAlignedData": "Energy.Active.Import.Register",
        "MeterValuesSampledData": "Energy.Active.Import.Register",
        "MeterValueSampleInterval": 60,
        "NumberOfConnectors": 1,
        "ResetRetries": 1,
        "StopTransactionOnEVSideDisconnect": true,
        "StopTransactionOnInvalidId": true,
        "StopTxnAlignedData": "Energy.Active.Import.Register",
        "StopTxnSampledData": "Energy.Active.Import.Register",
        "SupportedFeatureProfiles": "Core,FirmwareManagement,RemoteTrigger,Reservation,LocalAuthListManagement,SmartCharging",
        "TransactionMessageAttempts": 1,
        "TransactionMessageRetryInterval": 10,
        "UnlockConnectorOnEVSideDisconnect": true,
        "WebsocketPingInterval": 0
    },
    "FirmwareManagement": {
        "SupportedFileTransferProtocols": "FTP"
    },
    "Security": {
        "SecurityProfile": 0,
        "CpoName": "Pionix"
    },
    "LocalAuthListManagement": {
        "LocalAuthListEnabled": true,
        "LocalAuthListMaxLength": 42,
        "SendLocalListMaxLength": 42
    },
    "SmartCharging": {
        "ChargeProfileMaxStackLevel": 42,
        "ChargingScheduleAllowedChargingRateUnit": "Current,Power",
        "ChargingScheduleMaxPeriods": 42,
        "MaxChargingProfilesInstalled": 42
    },
    "PnC": {
        "ISO15118PnCEnabled": true,
        "ContractValidationOffline": true
    },
    "Custom": {
        "ExampleConfigurationKey": "example"
    }
}
~~~

~~~
sudo nano ~/checkout/everest-workspace/everest-core/config/config-sil-ocpp.yaml
~~~

and then changed your ChargePointConfigPath
as for me, I changed my ChargePointConfigPath name is "ocpp.json" but yours might be different , it depends on you what you want to name it 
~~~
ChargePointConfigPath: ocpp.json
~~~

After That run 
~~~
cmake ..
make install
~~~

and then run the SIL using 

~~~
sh run-sil-ocpp.sh
~~~

## What will happen after you run Everest
Charger should pop up on your dashboard like this 

<img width="1275" alt="image" src="https://github.com/Anmirazik/EV-Charger-Solution/assets/113960675/fe457ce6-4282-4e72-b2dc-379bbbd8421c">

You need to configure your charger , mine looks like this 

<img width="725" alt="image" src="https://github.com/Anmirazik/EV-Charger-Solution/assets/113960675/8115ffe1-79f9-4160-9de2-5d95064ad301">

This is My Node-Red dashboard for the Everest SIL

<img width="613" alt="image" src="https://github.com/Anmirazik/EV-Charger-Solution/assets/113960675/6b5b8abe-704a-4db5-9f33-be259df30d71">

and then plug in the charger and then you will see on Open E-Mobility dashboard shows preparing status in yellow color like this 

<img width="1279" alt="image" src="https://github.com/Anmirazik/EV-Charger-Solution/assets/113960675/f3c83cd4-fb5e-4f3d-a924-4933e7fb9839">

You need to add the RFID tags in the Open E-Mobility

<img width="211" alt="image" src="https://github.com/Anmirazik/EV-Charger-Solution/assets/113960675/35f1c1e3-c6aa-4192-89b8-b50afe57c17b">

like this 

<img width="1280" alt="image" src="https://github.com/Anmirazik/EV-Charger-Solution/assets/113960675/30640572-a12e-4650-bb1b-44a72bf8d2f8">

If you don't add the RFID tags on Open E-Mobility then the RFID tags will be rejected by the charger, this is because we don't want unregistered RFID tags to authorized for any sessions :)

After the sessions is authorized, the charging session should start and you will see the data displayed like this 

<img width="1279" alt="image" src="https://github.com/Anmirazik/EV-Charger-Solution/assets/113960675/2bd82f16-1167-4a87-ba70-5f53fe45c5ea">

Now you have an End to End EV charging solution with only Open Source ! Congratulations












