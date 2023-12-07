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

After the sessions is authorized, the charging session should start as below

![image](https://github.com/Anmirazik/Open-Source-Ev-charger-End-to-End-Solution/assets/113960675/d5a56567-c521-46dd-964e-3b378b512dcb)

~~~

2023-12-07 11:58:48.319931 [INFO] manager          ::   ________      __                _
2023-12-07 11:58:48.320093 [INFO] manager          ::  |  ____\ \    / /               | |
2023-12-07 11:58:48.320125 [INFO] manager          ::  | |__   \ \  / /__ _ __ ___  ___| |_
2023-12-07 11:58:48.320147 [INFO] manager          ::  |  __|   \ \/ / _ \ '__/ _ \/ __| __|
2023-12-07 11:58:48.320168 [INFO] manager          ::  | |____   \  /  __/ | |  __/\__ \ |_
2023-12-07 11:58:48.320189 [INFO] manager          ::  |______|   \/ \___|_|  \___||___/\__|
2023-12-07 11:58:48.320209 [INFO] manager          ::
2023-12-07 11:58:48.320226 [INFO] manager          :: Using MQTT broker localhost:1883
2023-12-07 11:58:48.339442 [INFO] manager          :: Loading config file at: /home/anmirazik/checkout/everest-workspace-no-dirty/everest-core/config/config-sil-ocpp.yaml
2023-12-07 11:58:49.013845 [INFO] manager          :: - Types loaded in [39ms]
2023-12-07 11:58:49.013884 [INFO] manager          :: - Types validated [619ms]
2023-12-07 11:58:49.015152 [INFO] manager          :: - Errors loaded in [1ms]
2023-12-07 11:58:49.015175 [INFO] manager          :: - Errors validated [0ms]
2023-12-07 11:58:49.311434 [INFO] manager          :: Config loading completed in 991ms
2023-12-07 11:58:49.317507 [INFO] everest_ctrl     :: Launching controller service on port 8849
2023-12-07 11:58:50.410617 [INFO] auth:Auth        :: Module auth initialized [1072ms]
2023-12-07 11:58:50.565760 [INFO] energy_manager:  :: Module energy_manager initialized [1222ms]
2023-12-07 11:58:50.567366 [INFO] grid_connection  :: Module grid_connection_point initialized [1220ms]
2023-12-07 11:58:50.670580 [INFO] evse_manager_2:  :: Module evse_manager_2 initialized [1325ms]
2023-12-07 11:58:50.705543 [INFO] api:API          :: Module api initialized [1352ms]
2023-12-07 11:58:50.753110 [INFO] token_provider_  :: Module token_provider_1 initialized [1283ms]
2023-12-07 11:58:50.762459 [INFO] evse_security:E  :: Module evse_security initialized [1415ms]
2023-12-07 11:58:50.767294 [INFO] ocpp:OCPP        :: OCPP config: /home/anmirazik/checkout/everest-workspace-no-dirty/everest-core/build/dist/share/everest/modules/OCPP/config-docker.json
2023-12-07 11:58:50.767459 [INFO] ocpp:OCPP        :: OCPP user config: /home/anmirazik/checkout/everest-workspace-no-dirty/everest-core/build/dist/share/everest/modules/OCPP/user_config.json
2023-12-07 11:58:50.768272 [INFO] ocpp:OCPP        :: Augmenting chargepoint config with user_config entries

2023-12-07 11:58:50.788823 [INFO] iso15118_charge  :: TCP server on eno1 is listening on port [fe80::84f7:4071:be8d:1cbc%2]:61341

2023-12-07 11:58:50.789161 [INFO] iso15118_charge  :: TLS server on eno1 is listening on port [fe80::84f7:4071:be8d:1cbc%2]:64109

2023-12-07 11:58:50.789341 [INFO] iso15118_charge  :: SDP socket setup succeeded
2023-12-07 11:58:50.789737 [INFO] iso15118_charge  :: Module iso15118_charger initialized [1399ms]
2023-12-07 11:58:50.798158 [INFO] evse_manager_1:  :: Module evse_manager_1 initialized [1454ms]
2023-12-07 11:58:50.805982 [INFO] ocpp:OCPP        :: Logging OCPP messages to log file: /tmp/everest_ocpp_logs/2023-12-07T03:58:50.799Z.log
2023-12-07 11:58:50.806206 [INFO] ocpp:OCPP        :: Logging OCPP messages to html file: /tmp/everest_ocpp_logs/2023-12-07T03:58:50.799Z.html
2023-12-07 11:58:50.806896 [INFO] ocpp:OCPP        :: Module ocpp initialized [1387ms]
2023-12-07 11:58:50.810911 [INFO] system:System    :: Module system initialized [1342ms]
2023-12-07 11:58:51.411190 [INFO] manager          :: 🚙🚙🚙 All modules are initialized. EVerest up and running [3097ms] 🚙🚙🚙
2023-12-07 11:58:51.761656 [INFO] evse_manager_2:  :: Max AC hardware capabilities: 32A/3ph
2023-12-07 11:58:51.776658 [INFO] evse_manager_1:  :: Max AC hardware capabilities: 32A/3ph
2023-12-07 11:58:51.893626 [INFO] evse_manager_2:  :: 🌀🌀🌀 Ready to start charging 🌀🌀🌀
2023-12-07 11:58:51.898028 [INFO] evse_manager_1:  :: 🌀🌀🌀 Ready to start charging 🌀🌀🌀
2023-12-07 11:58:51.948366 [INFO] ocpp:OCPP        :: All EVSE ready. Starting OCPP1.6 service
2023-12-07 11:58:52.303608 [INFO] ocpp:OCPP        :: Connecting to plain websocket at uri: ws://127.0.0.1:8180/steve/websocket/CentralSystemService/cp001 with profile: 0
2023-12-07 11:58:52.304489 [INFO] ocpp:OCPP        :: Found 0 charging profile(s) in the database
2023-12-07 11:58:52.310373 [INFO] ocpp:OCPP        :: OCPP client successfully connected to plain websocket server
2023-12-07 11:59:02.767901 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 11:59:04.019620 [INFO] car_simulator_1  :: { cmd: 'iec_wait_pwr_ready', args: [], exec: [Function (anonymous)] }
2023-12-07 11:59:04.488285 [INFO] evse_manager_1:  :: SYS  Session logging started.
2023-12-07 11:59:04.488592 [INFO] evse_manager_1:  :: EVSE IEC Session Started: EVConnected
2023-12-07 11:59:04.577488 [INFO] ocpp:OCPP        :: Logging OCPP messages to html file: /tmp/everest-logs/2023-12-07T03:59:04.487Z-e393851f-0cee-4284-973e-177f23331489/incomplete-ocpp.html
2023-12-07 11:59:05.336228 [INFO] token_provider_  :: Publishing new dummy token: {"id_token":"DEADBEEF","authorization_type":"RFID","prevalidated":false,"connectors":[1]}
2023-12-07 11:59:05.366126 [INFO] auth:Auth        :: Received new token: {
    "authorization_type": "RFID",
    "connectors": [
        1
    ],
    "id_token": "DEADBEEF",
    "prevalidated": false
}
2023-12-07 11:59:05.457291 [INFO] auth:Auth        :: Providing authorization to connector#1
2023-12-07 11:59:05.541196 [INFO] evse_manager_1:  :: EVSE IEC EIM Authorization received
2023-12-07 11:59:05.541609 [INFO] evse_manager_1:  :: EVSE IEC Transaction Started (0 kWh)
2023-12-07 11:59:05.542510 [INFO] evse_manager_1:  :: EVSE IEC AC mode, HLC disabled. We are in X1 so we can go directly to nominal PWM.
2023-12-07 11:59:05.578594 [INFO] auth:Auth        :: Result for token: DEADBEEF: ACCEPTED
2023-12-07 11:59:05.592851 [INFO] evse_manager_1:  :: EVSE IEC Charger state: Wait for Auth->PrepareCharging
2023-12-07 11:59:05.593403 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (100%)
2023-12-07 11:59:06.022047 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 11:59:06.269650 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 11:59:07.520683 [INFO] car_simulator_1  :: {
  cmd: 'draw_power_regulated',
  args: [ 16, 3 ],
  exec: [Function (anonymous)]
}
2023-12-07 11:59:07.521162 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 36000 ], exec: [Function (anonymous)] }
2023-12-07 11:59:07.753139 [INFO] evse_manager_1:  ::                                     CAR IEC Event CarRequestedPower
2023-12-07 11:59:07.769730 [INFO] evse_manager_1:  :: EVSE IEC Charger state: PrepareCharging->Charging
2023-12-07 11:59:07.835115 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 11:59:08.021987 [INFO] evse_manager_1:  :: EVSE IEC Event PowerOn
2023-12-07 11:59:24.380310 [INFO] car_simulator_1  :: { cmd: 'unplug', args: [], exec: [Function (anonymous)] }
2023-12-07 11:59:24.808945 [INFO] evse_manager_1:  ::                                     CAR IEC Event BCDtoEF
2023-12-07 11:59:24.829236 [INFO] evse_manager_1:  ::                                     CAR IEC Event CarRequestedStopPower
2023-12-07 11:59:24.829413 [INFO] evse_manager_1:  :: EVSE IEC Charger state: Charging->Car Paused
2023-12-07 11:59:24.921547 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 11:59:25.043912 [INFO] evse_manager_1:  :: EVSE IEC Event PowerOff
2023-12-07 11:59:25.044431 [INFO] evse_manager_1:  ::                                     CAR IEC Event CarUnplugged
2023-12-07 11:59:25.044725 [INFO] evse_manager_1:  :: EVSE IEC Charger state: Car Paused->StoppingCharging
2023-12-07 11:59:25.045414 [INFO] evse_manager_1:  :: EVSE IEC Set PWM Off
2023-12-07 11:59:25.145626 [INFO] evse_manager_1:  :: EVSE IEC Charger state: StoppingCharging->Finished
2023-12-07 11:59:25.146219 [INFO] evse_manager_1:  :: EVSE IEC Transaction Finished: EVDisconnected (0.048 kWh)
2023-12-07 11:59:25.147096 [INFO] evse_manager_1:  :: EVSE IEC Session Finished
2023-12-07 11:59:25.147299 [INFO] evse_manager_1:  :: SYS  Session logging stopped.
2023-12-07 11:59:25.197996 [INFO] ocpp:OCPP        :: Executing unlock connector callback
2023-12-07 11:59:29.773855 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 11:59:31.040020 [INFO] car_simulator_1  :: { cmd: 'iec_wait_pwr_ready', args: [], exec: [Function (anonymous)] }
2023-12-07 11:59:31.544526 [INFO] evse_manager_1:  :: SYS  Session logging started.
2023-12-07 11:59:31.544827 [INFO] evse_manager_1:  :: EVSE IEC Session Started: EVConnected
2023-12-07 11:59:31.594380 [INFO] ocpp:OCPP        :: Logging OCPP messages to html file: /tmp/everest-logs/2023-12-07T03:59:31.544Z-84c082f5-20fd-49a3-b4db-6a653cd3958f/incomplete-ocpp.html
2023-12-07 11:59:33.100298 [INFO] token_provider_  :: Publishing new dummy token: {"id_token":"DEADBEEF","authorization_type":"RFID","prevalidated":false,"connectors":[1]}
2023-12-07 11:59:33.121071 [INFO] auth:Auth        :: Received new token: {
    "authorization_type": "RFID",
    "connectors": [
        1
    ],
    "id_token": "DEADBEEF",
    "prevalidated": false
}
2023-12-07 11:59:33.212373 [INFO] auth:Auth        :: Providing authorization to connector#1
2023-12-07 11:59:33.298683 [INFO] evse_manager_1:  :: EVSE IEC EIM Authorization received
2023-12-07 11:59:33.299022 [INFO] evse_manager_1:  :: EVSE IEC Transaction Started (0 kWh)
2023-12-07 11:59:33.299894 [INFO] evse_manager_1:  :: EVSE IEC AC mode, HLC disabled. We are in X1 so we can go directly to nominal PWM.
2023-12-07 11:59:33.333812 [INFO] auth:Auth        :: Result for token: DEADBEEF: ACCEPTED
2023-12-07 11:59:33.350210 [INFO] evse_manager_1:  :: EVSE IEC Charger state: Wait for Auth->PrepareCharging
2023-12-07 11:59:33.350728 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (100%)
2023-12-07 11:59:34.400950 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 11:59:34.792764 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 11:59:36.043177 [INFO] car_simulator_1  :: {
  cmd: 'draw_power_regulated',
  args: [ 16, 3 ],
  exec: [Function (anonymous)]
}
2023-12-07 11:59:36.043466 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 36000 ], exec: [Function (anonymous)] }
2023-12-07 11:59:36.281843 [INFO] evse_manager_1:  ::                                     CAR IEC Event CarRequestedPower
2023-12-07 11:59:36.325712 [INFO] evse_manager_1:  :: EVSE IEC Charger state: PrepareCharging->Charging
2023-12-07 11:59:36.475392 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 11:59:36.586932 [INFO] evse_manager_1:  :: EVSE IEC Event PowerOn
2023-12-07 11:59:39.399457 [INFO] car_simulator_1  :: { cmd: 'unplug', args: [], exec: [Function (anonymous)] }
2023-12-07 11:59:39.826082 [INFO] evse_manager_1:  ::                                     CAR IEC Event BCDtoEF
2023-12-07 11:59:39.846572 [INFO] evse_manager_1:  ::                                     CAR IEC Event CarRequestedStopPower
2023-12-07 11:59:39.846854 [INFO] evse_manager_1:  :: EVSE IEC Charger state: Charging->Car Paused
2023-12-07 11:59:39.949000 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 11:59:40.121609 [INFO] evse_manager_1:  :: EVSE IEC Event PowerOff
2023-12-07 11:59:40.122106 [INFO] evse_manager_1:  ::                                     CAR IEC Event CarUnplugged
2023-12-07 11:59:40.122333 [INFO] evse_manager_1:  :: EVSE IEC Charger state: Car Paused->StoppingCharging
2023-12-07 11:59:40.122809 [INFO] evse_manager_1:  :: EVSE IEC Set PWM Off
2023-12-07 11:59:40.233470 [INFO] evse_manager_1:  :: EVSE IEC Charger state: StoppingCharging->Finished
2023-12-07 11:59:40.234062 [INFO] evse_manager_1:  :: EVSE IEC Transaction Finished: EVDisconnected (0.008 kWh)
2023-12-07 11:59:40.234989 [INFO] evse_manager_1:  :: EVSE IEC Session Finished
2023-12-07 11:59:40.235187 [INFO] evse_manager_1:  :: SYS  Session logging stopped.
2023-12-07 11:59:40.288466 [INFO] ocpp:OCPP        :: Executing unlock connector callback
2023-12-07 11:59:44.119519 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 11:59:45.370810 [INFO] car_simulator_1  :: { cmd: 'iec_wait_pwr_ready', args: [], exec: [Function (anonymous)] }
2023-12-07 11:59:45.797206 [INFO] evse_manager_1:  :: SYS  Session logging started.
2023-12-07 11:59:45.797532 [INFO] evse_manager_1:  :: EVSE IEC Session Started: EVConnected
2023-12-07 11:59:45.848807 [INFO] ocpp:OCPP        :: Logging OCPP messages to html file: /tmp/everest-logs/2023-12-07T03:59:45.796Z-f59e81aa-74f5-42ea-abc7-90b4f99556c5/incomplete-ocpp.html
2023-12-07 11:59:46.828707 [INFO] token_provider_  :: Publishing new dummy token: {"id_token":"DEADBEEF","authorization_type":"RFID","prevalidated":false,"connectors":[1]}
2023-12-07 11:59:46.859371 [INFO] auth:Auth        :: Received new token: {
    "authorization_type": "RFID",
    "connectors": [
        1
    ],
    "id_token": "DEADBEEF",
    "prevalidated": false
}
2023-12-07 11:59:46.940850 [INFO] auth:Auth        :: Providing authorization to connector#1
2023-12-07 11:59:47.050583 [INFO] evse_manager_1:  :: EVSE IEC EIM Authorization received
2023-12-07 11:59:47.050928 [INFO] evse_manager_1:  :: EVSE IEC Transaction Started (0 kWh)
2023-12-07 11:59:47.051781 [INFO] evse_manager_1:  :: EVSE IEC AC mode, HLC disabled. We are in X1 so we can go directly to nominal PWM.
2023-12-07 11:59:47.072677 [INFO] auth:Auth        :: Result for token: DEADBEEF: ACCEPTED
2023-12-07 11:59:47.102076 [INFO] evse_manager_1:  :: EVSE IEC Charger state: Wait for Auth->PrepareCharging
2023-12-07 11:59:47.102560 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (100%)
2023-12-07 11:59:47.903882 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 11:59:48.120862 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 11:59:49.370925 [INFO] car_simulator_1  :: {
  cmd: 'draw_power_regulated',
  args: [ 32, 1 ],
  exec: [Function (anonymous)]
}
2023-12-07 11:59:49.371212 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 36000 ], exec: [Function (anonymous)] }
2023-12-07 11:59:49.553762 [INFO] evse_manager_1:  ::                                     CAR IEC Event CarRequestedPower
2023-12-07 11:59:49.592658 [INFO] evse_manager_1:  :: EVSE IEC Charger state: PrepareCharging->Charging
2023-12-07 11:59:49.705830 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 11:59:49.796921 [INFO] evse_manager_1:  :: EVSE IEC Event PowerOn
2023-12-07 11:59:52.323652 [INFO] ocpp:OCPP        :: Checking if V2GCertificate has expired
2023-12-07 11:59:52.390467 [INFO] evse_security:E  :: Requesting certificate expiry: V2G
2023-12-07 11:59:52.390564 [INFO] evse_security:E  :: Requesting key/pair: V2G
2023-12-07 11:59:52.397706 [WARN] evse_security:E void evse_security::X509CertificateBundle::add_certifcates(const string&, evse_security::EncodingFormat, const std::optional<std::filesystem::__cxx11::path>&) :: Failed to read single certificate from directory file, ignoring entry!
2023-12-07 11:59:52.403242 [INFO] evse_security:E  :: Key found for certificate at path: "/home/anmirazik/checkout/everest-workspace-no-dirty/everest-core/build/dist/etc/everest/certs/client/cso/SECC_LEAF.key"
2023-12-07 11:59:52.451185 [INFO] ocpp:OCPP        :: V2GCertificate is still valid.
2023-12-07 11:59:52.451295 [INFO] ocpp:OCPP        :: Checking if OCSP cache should be updated
2023-12-07 11:59:52.451582 [INFO] ocpp:OCPP        :: OCSP Cache is up-to-date enough
2023-12-07 12:00:00.994582 [INFO] car_simulator_2  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 12:00:02.263485 [INFO] car_simulator_2  :: { cmd: 'iec_wait_pwr_ready', args: [], exec: [Function (anonymous)] }
2023-12-07 12:00:02.859556 [INFO] evse_manager_2:  :: SYS  Session logging started.
2023-12-07 12:00:02.859879 [INFO] evse_manager_2:  :: EVSE IEC Session Started: EVConnected
2023-12-07 12:00:02.944353 [INFO] ocpp:OCPP        :: Logging OCPP messages to html file: /tmp/2023-12-07T04:00:02.859Z-cd02e950-4cd3-43f2-b591-bc7b649f0cef/incomplete-ocpp.html
2023-12-07 12:00:03.181668 [INFO] token_provider_  :: Publishing new dummy token: {"id_token":"DEADBEEF","authorization_type":"RFID","prevalidated":false,"connectors":[2]}
2023-12-07 12:00:03.204228 [INFO] auth:Auth        :: Received new token: {
    "authorization_type": "RFID",
    "connectors": [
        2
    ],
    "id_token": "DEADBEEF",
    "prevalidated": false
}
2023-12-07 12:00:03.295686 [INFO] auth:Auth        :: Providing authorization to connector#2
2023-12-07 12:00:03.411621 [INFO] evse_manager_2:  :: EVSE IEC EIM Authorization received
2023-12-07 12:00:03.412026 [INFO] evse_manager_2:  :: EVSE IEC Transaction Started (0 kWh)
2023-12-07 12:00:03.412903 [INFO] evse_manager_2:  :: EVSE IEC AC mode, HLC disabled. We are in X1 so we can go directly to nominal PWM.
2023-12-07 12:00:03.427324 [INFO] auth:Auth        :: Result for token: DEADBEEF: ACCEPTED
2023-12-07 12:00:03.463246 [INFO] evse_manager_2:  :: EVSE IEC Charger state: Wait for Auth->PrepareCharging
2023-12-07 12:00:03.463788 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (100%)
2023-12-07 12:00:03.759066 [INFO] evse_manager_2:  :: EVSE IEC Transaction Finished: DeAuthorized (0 kWh)
2023-12-07 12:00:03.787698 [INFO] evse_manager_2:  :: EVSE IEC Charger state: PrepareCharging->EVSE Paused
2023-12-07 12:00:03.788108 [INFO] evse_manager_2:  :: EVSE IEC Set PWM Off
2023-12-07 12:00:11.679765 [INFO] token_provider_  :: Publishing new dummy token: {"id_token":"ABC12345","authorization_type":"RFID","prevalidated":false,"connectors":[2]}
2023-12-07 12:00:11.709559 [INFO] auth:Auth        :: Received new token: {
    "authorization_type": "RFID",
    "connectors": [
        2
    ],
    "id_token": "ABC12345",
    "prevalidated": false
}
2023-12-07 12:00:11.801034 [INFO] auth:Auth        :: Result for token: ABC12345: NO_CONNECTOR_AVAILABLE
2023-12-07 12:00:17.714257 [INFO] car_simulator_2  :: { cmd: 'unplug', args: [], exec: [Function (anonymous)] }
2023-12-07 12:00:17.868847 [INFO] evse_manager_2:  ::                                     CAR IEC Event BCDtoEF
2023-12-07 12:00:17.889140 [INFO] evse_manager_2:  ::                                     CAR IEC Event CarRequestedStopPower
2023-12-07 12:00:17.889574 [INFO] evse_manager_2:  ::                                     CAR IEC Event CarUnplugged
2023-12-07 12:00:17.889844 [INFO] evse_manager_2:  :: EVSE IEC Charger state: EVSE Paused->StoppingCharging
2023-12-07 12:00:17.890397 [INFO] evse_manager_2:  :: EVSE IEC Set PWM Off
2023-12-07 12:00:17.980725 [INFO] evse_manager_2:  :: EVSE IEC Charger state: StoppingCharging->Finished
2023-12-07 12:00:17.981050 [INFO] evse_manager_2:  :: EVSE IEC Session Finished
2023-12-07 12:00:17.981210 [INFO] evse_manager_2:  :: SYS  Session logging stopped.
2023-12-07 12:00:21.751787 [INFO] car_simulator_2  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 12:00:23.030280 [INFO] car_simulator_2  :: { cmd: 'iec_wait_pwr_ready', args: [], exec: [Function (anonymous)] }
2023-12-07 12:00:23.330363 [INFO] evse_manager_2:  :: SYS  Session logging started.
2023-12-07 12:00:23.330642 [INFO] evse_manager_2:  :: EVSE IEC Session Started: EVConnected
2023-12-07 12:00:23.401183 [INFO] ocpp:OCPP        :: Logging OCPP messages to html file: /tmp/2023-12-07T04:00:23.330Z-2826b81b-3179-4954-8ab7-6466b3fb2f30/incomplete-ocpp.html
2023-12-07 12:00:24.206962 [INFO] token_provider_  :: Publishing new dummy token: {"id_token":"ABC12345","authorization_type":"RFID","prevalidated":false,"connectors":[2]}
2023-12-07 12:00:24.230436 [INFO] auth:Auth        :: Received new token: {
    "authorization_type": "RFID",
    "connectors": [
        2
    ],
    "id_token": "ABC12345",
    "prevalidated": false
}
2023-12-07 12:00:24.321814 [INFO] auth:Auth        :: Providing authorization to connector#2
2023-12-07 12:00:24.433405 [INFO] evse_manager_2:  :: EVSE IEC EIM Authorization received
2023-12-07 12:00:24.433792 [INFO] evse_manager_2:  :: EVSE IEC Transaction Started (0 kWh)
2023-12-07 12:00:24.434743 [INFO] evse_manager_2:  :: EVSE IEC AC mode, HLC disabled. We are in X1 so we can go directly to nominal PWM.
2023-12-07 12:00:24.453507 [INFO] auth:Auth        :: Result for token: ABC12345: ACCEPTED
2023-12-07 12:00:24.485133 [INFO] evse_manager_2:  :: EVSE IEC Charger state: Wait for Auth->PrepareCharging
2023-12-07 12:00:24.485759 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (100%)
2023-12-07 12:00:25.767409 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (33.33333432674408%)
2023-12-07 12:00:26.073685 [INFO] evse_manager_1:  :: EVSE IEC Soft overcurrent event (L1:31.719103, L2:31.719103, L3:0, limit 22.55), starting timer.
2023-12-07 12:00:26.074050 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (33.33333432674408%)
2023-12-07 12:00:26.282817 [INFO] car_simulator_2  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 12:00:27.533911 [INFO] car_simulator_2  :: {
  cmd: 'draw_power_regulated',
  args: [ 32, 1 ],
  exec: [Function (anonymous)]
}
2023-12-07 12:00:27.534408 [INFO] car_simulator_2  :: { cmd: 'sleep', args: [ 36000 ], exec: [Function (anonymous)] }
2023-12-07 12:00:27.619068 [INFO] evse_manager_2:  ::                                     CAR IEC Event CarRequestedPower
2023-12-07 12:00:27.633505 [INFO] evse_manager_2:  :: EVSE IEC Charger state: PrepareCharging->Charging
2023-12-07 12:00:27.751839 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (33.33333432674408%)
2023-12-07 12:00:27.873178 [INFO] evse_manager_2:  :: EVSE IEC Event PowerOn


~~~




Now you have an End to End EV charging solution with only Open Source ! Congratulations












