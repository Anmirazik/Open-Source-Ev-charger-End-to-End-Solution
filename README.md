# EV-Charger End to End Solutions

So you might be wondering how can I create end-to-end EV Charging Software using open source.

Good for us, there are genius developers out there who have published their solutions on Github, we just need to find and use it only. However, in this article, I will focus on integrating Everest Software In The Loop with Open E Mobility Backend and FrontEnd 

## What do you need? 

Everest 
1. You can refer to the Everest documentation on how to install Everest [here ](https://everest.github.io/nightly/)

Open E - Mobility 
1. You can refer how to install the Open E-Mobility Backend Server [here](https://github.com/sap-labs-france/ev-server)
2. You can refer how to install Open E-Mobility FrontEnd Server [here](https://github.com/sap-labs-france/ev-dashboard)

What you need to configure at Everest 

First of all SSH into your Linux system , as for me, I use Ubuntu 22.04 and then change directory into everest-core

As for me once I log in I need to run this 
~~~
cd everest-workspace/everest-core/build/dist/share/everest/modules/OCPP
sudo nano ocpp.json
~~~
my "ocpp.json" contents looks like this 
~~~
{
    "Internal": {
        "ChargePointId": "Everest-AC-Charger",
        "CentralSystemURI": "localhost:8001/OCPP16/63c937b82b1ed7174101f323/6569b51a34150fdfdd78cf1f/Everest-AC-Charger",
        "ChargeBoxSerialNumber": "Everest-AC-Charger",
        "ChargePointModel": "Yeti",
        "ChargePointVendor": "Pionix",
        "FirmwareVersion": "0.1",
        "AllowChargingProfileWithoutStartSchedule": false
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
        "MeterValuesSampledData": "Current.Import,Energy.Active.Import.Register,Energy.Active.Import.Interval,Power.Active.Import,SoC,Energy.Active.Import.Interval,Voltage,Energy.Active.Export.Register,Energy.Reactive.Export.Register,Power.Active.Export",
        "MeterValueSampleInterval": 10,
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
        "CpoName": "Pionix",
        "AuthorizationKey": "AABBCCDDEEFFGGHH",
        "SecurityProfile": 1
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
cd everest-workspace/everest-core/
~~~

and then I use Visual Studio Code server for editing the files in the server where i run this 
~~~
code .
~~~

and then changed your ChargePointConfigPath

as for me, my ChargePointConfigPath name is "ocpp.json" but yours might be different , it depends on you what you want to name it 
~~~
      ChargePointConfigPath: ocpp.json
~~~

