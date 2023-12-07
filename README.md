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
![image](https://github.com/Anmirazik/Open-Source-Ev-charger-End-to-End-Solution/assets/113960675/9b5f725b-d26c-4764-9ff5-260b81b43448)

## What you need to configure at Everest 
First of all SSH into your Linux system, as for me, I use Ubuntu 22.04 and then change the directory into everest-core, As for me once I log in I need to run this 
~~~
sudo nano ~/checkout/everest-workspace/everest-core/build/dist/share/everest/modules/OCPP/config-docker.json
~~~
my "config-docker.json" contents looks like this , btw this is the default config Everest provide to us
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

Go to config file using this

~~~
sudo nano ~/checkout/everest-workspace/everest-core/config/config-sil-ocpp.yaml
~~~
and then ensure your ChargePointConfigPath are as below
~~~
ChargePointConfigPath: config-docker.json
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
Connector Charger should pop up on "Number Of Connected JSON Charge Point"

![image](https://github.com/Anmirazik/Open-Source-Ev-charger-End-to-End-Solution/assets/113960675/782a528e-0b62-4f42-8699-35c4753b4964)

You can execeute OCPP commands by going to Operations - OCPP V1.6

![image](https://github.com/Anmirazik/Open-Source-Ev-charger-End-to-End-Solution/assets/113960675/28e078af-ce73-44fe-b9b0-582f87bb203f)


This is My Node-Red dashboard for the Everest SIL

<img width="613" alt="image" src="https://github.com/Anmirazik/EV-Charger-Solution/assets/113960675/6b5b8abe-704a-4db5-9f33-be259df30d71">

You need to add the RFID tags in the steve server

<img width="211" alt="image" src="https://github.com/Anmirazik/EV-Charger-Solution/assets/113960675/35f1c1e3-c6aa-4192-89b8-b50afe57c17b">

like this 

![image](https://github.com/Anmirazik/Open-Source-Ev-charger-End-to-End-Solution/assets/113960675/25effaf8-74cc-4655-b077-55e3fe2c31ac)


If you don't add the RFID tags on steve then the RFID tags will be rejected by the charger, this is because we don't want unregistered RFID tags to authorized for any sessions :)

After the sessions is authorized, the charging session should start

Now you have an End to End EV charging solution with only Open Source ! Congratulations












