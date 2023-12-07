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

Below shows the charger transaction logs 
~~~
2023-12-07 12:03:28.485970 [INFO] manager          ::   ________      __                _
2023-12-07 12:03:28.486127 [INFO] manager          ::  |  ____\ \    / /               | |
2023-12-07 12:03:28.486156 [INFO] manager          ::  | |__   \ \  / /__ _ __ ___  ___| |_
2023-12-07 12:03:28.486177 [INFO] manager          ::  |  __|   \ \/ / _ \ '__/ _ \/ __| __|
2023-12-07 12:03:28.486197 [INFO] manager          ::  | |____   \  /  __/ | |  __/\__ \ |_
2023-12-07 12:03:28.486217 [INFO] manager          ::  |______|   \/ \___|_|  \___||___/\__|
2023-12-07 12:03:28.486236 [INFO] manager          ::
2023-12-07 12:03:28.486253 [INFO] manager          :: Using MQTT broker localhost:1883
2023-12-07 12:03:28.505165 [INFO] manager          :: Loading config file at: /home/anmirazik/checkout/everest-workspace-no-dirty/everest-core/config/config-sil-ocpp.yaml
2023-12-07 12:03:29.185574 [INFO] manager          :: - Types loaded in [38ms]
2023-12-07 12:03:29.185614 [INFO] manager          :: - Types validated [627ms]
2023-12-07 12:03:29.186846 [INFO] manager          :: - Errors loaded in [1ms]
2023-12-07 12:03:29.186869 [INFO] manager          :: - Errors validated [0ms]
2023-12-07 12:03:29.479161 [INFO] manager          :: Config loading completed in 992ms
2023-12-07 12:03:29.485401 [INFO] everest_ctrl     :: Launching controller service on port 8849
2023-12-07 12:03:30.730244 [INFO] api:API          :: Module api initialized [1226ms]2023-12-07 12:03:30.736377 [INFO] evse_manager_2:  :: Module evse_manager_2 initialized [1224ms]

2023-12-07 12:03:30.773845 [INFO] auth:Auth        :: Module auth initialized [1268ms]
2023-12-07 12:03:30.795467 [INFO] grid_connection  :: Module grid_connection_point initialized [1280ms]
2023-12-07 12:03:30.828788 [INFO] system:System    :: Module system initialized [1215ms]
2023-12-07 12:03:30.873965 [INFO] energy_manager:  :: Module energy_manager initialized [1362ms]
2023-12-07 12:03:30.921234 [INFO] evse_security:E  :: Module evse_security initialized [1385ms]
2023-12-07 12:03:30.927231 [INFO] ocpp:OCPP        :: OCPP config: /home/anmirazik/checkout/everest-workspace-no-dirty/everest-core/build/dist/share/everest/modules/OCPP/config-docker.json

2023-12-07 12:03:30.927400 [INFO] ocpp:OCPP        :: OCPP user config: /home/anmirazik/checkout/everest-workspace-no-dirty/everest-core/build/dist/share/everest/modules/OCPP/user_config.json
2023-12-07 12:03:30.927433 [INFO] iso15118_charge  :: TCP server on eno1 is listening on port [fe80::84f7:4071:be8d:1cbc%2]:61341

2023-12-07 12:03:30.927631 [INFO] iso15118_charge  :: TLS server on eno1 is listening on port [fe80::84f7:4071:be8d:1cbc%2]:64109

2023-12-07 12:03:30.927708 [INFO] iso15118_charge  :: SDP socket setup succeeded
2023-12-07 12:03:30.928085 [INFO] iso15118_charge  :: Module iso15118_charger initialized [1330ms]
2023-12-07 12:03:30.928249 [INFO] ocpp:OCPP        :: Augmenting chargepoint config with user_config entries
2023-12-07 12:03:30.930640 [INFO] token_provider_  :: Module token_provider_1 initialized [1304ms]
2023-12-07 12:03:30.958070 [INFO] ocpp:OCPP        :: Logging OCPP messages to log file: /tmp/everest_ocpp_logs/2023-12-07T04:03:30.950Z.log
2023-12-07 12:03:30.958315 [INFO] ocpp:OCPP        :: Logging OCPP messages to html file: /tmp/everest_ocpp_logs/2023-12-07T04:03:30.950Z.html
2023-12-07 12:03:30.959045 [INFO] ocpp:OCPP        :: Module ocpp initialized [1346ms]
2023-12-07 12:03:30.963021 [INFO] evse_manager_1:  :: Module evse_manager_1 initialized [1450ms]
2023-12-07 12:03:31.508140 [INFO] manager          :: 🚙🚙🚙 All modules are initialized. EVerest up and running [3028ms] 🚙🚙🚙
2023-12-07 12:03:31.837166 [INFO] evse_manager_2:  :: Max AC hardware capabilities: 32A/3ph
2023-12-07 12:03:31.892363 [INFO] evse_manager_1:  :: Max AC hardware capabilities: 32A/3ph
2023-12-07 12:03:32.009273 [INFO] evse_manager_2:  :: 🌀🌀🌀 Ready to start charging 🌀🌀🌀
2023-12-07 12:03:32.013529 [INFO] evse_manager_1:  :: 🌀🌀🌀 Ready to start charging 🌀🌀🌀
2023-12-07 12:03:32.070139 [INFO] ocpp:OCPP        :: All EVSE ready. Starting OCPP1.6 service
2023-12-07 12:03:32.445601 [INFO] ocpp:OCPP        :: Connecting to plain websocket at uri: ws://127.0.0.1:8180/steve/websocket/CentralSystemService/cp001 with profile: 0
2023-12-07 12:03:32.446397 [INFO] ocpp:OCPP        :: Found 0 charging profile(s) in the database
2023-12-07 12:03:32.454843 [INFO] ocpp:OCPP        :: OCPP client successfully connected to plain websocket server
2023-12-07 12:03:43.337137 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 12:03:44.589858 [INFO] car_simulator_1  :: { cmd: 'iec_wait_pwr_ready', args: [], exec: [Function (anonymous)] }
2023-12-07 12:03:45.097738 [INFO] evse_manager_1:  :: SYS  Session logging started.
2023-12-07 12:03:45.098036 [INFO] evse_manager_1:  :: EVSE IEC Session Started: EVConnected
2023-12-07 12:03:45.142125 [INFO] ocpp:OCPP        :: Logging OCPP messages to html file: /tmp/everest-logs/2023-12-07T04:03:45.097Z-7955151a-b348-4082-9ad3-dabb597e7ce5/incomplete-ocpp.html
2023-12-07 12:03:47.089085 [INFO] token_provider_  :: Publishing new dummy token: {"id_token":"ABC12345","authorization_type":"RFID","prevalidated":false,"connectors":[1]}
2023-12-07 12:03:47.113994 [INFO] auth:Auth        :: Received new token: {
    "authorization_type": "RFID",
    "connectors": [
        1
    ],
    "id_token": "ABC12345",
    "prevalidated": false
}
2023-12-07 12:03:47.205071 [INFO] auth:Auth        :: Providing authorization to connector#1
2023-12-07 12:03:47.302677 [INFO] evse_manager_1:  :: EVSE IEC EIM Authorization received
2023-12-07 12:03:47.303053 [INFO] evse_manager_1:  :: EVSE IEC Transaction Started (0 kWh)
2023-12-07 12:03:47.303967 [INFO] evse_manager_1:  :: EVSE IEC AC mode, HLC disabled. We are in X1 so we can go directly to nominal PWM.
2023-12-07 12:03:47.326457 [INFO] auth:Auth        :: Result for token: ABC12345: ACCEPTED
2023-12-07 12:03:47.354295 [INFO] evse_manager_1:  :: EVSE IEC Charger state: Wait for Auth->PrepareCharging
2023-12-07 12:03:47.354887 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (100%)
2023-12-07 12:03:47.483831 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 12:03:47.841760 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 12:03:49.093042 [INFO] car_simulator_1  :: {
  cmd: 'draw_power_regulated',
  args: [ 32, 1 ],
  exec: [Function (anonymous)]
}
2023-12-07 12:03:49.093536 [INFO] car_simulator_1  :: { cmd: 'sleep', args: [ 36000 ], exec: [Function (anonymous)] }
2023-12-07 12:03:49.364709 [INFO] evse_manager_1:  ::                                     CAR IEC Event CarRequestedPower
2023-12-07 12:03:49.408448 [INFO] evse_manager_1:  :: EVSE IEC Charger state: PrepareCharging->Charging
2023-12-07 12:03:49.527059 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 12:03:49.618210 [INFO] evse_manager_1:  :: EVSE IEC Event PowerOn
2023-12-07 12:03:50.011657 [INFO] car_simulator_2  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 12:03:51.262229 [INFO] car_simulator_2  :: { cmd: 'iec_wait_pwr_ready', args: [], exec: [Function (anonymous)] }
2023-12-07 12:03:51.917415 [INFO] evse_manager_2:  :: SYS  Session logging started.
2023-12-07 12:03:51.917729 [INFO] evse_manager_2:  :: EVSE IEC Session Started: EVConnected
2023-12-07 12:03:51.963805 [INFO] ocpp:OCPP        :: Logging OCPP messages to html file: /tmp/2023-12-07T04:03:51.916Z-bb88019e-38cb-4613-ad35-e0f1b92f65f1/incomplete-ocpp.html
2023-12-07 12:03:53.495706 [INFO] evse_manager_1:  :: EVSE IEC Soft overcurrent event (L1:31.776558, L2:31.776558, L3:0, limit 22.55), starting timer.
2023-12-07 12:03:54.648088 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (33.33333432674408%)
2023-12-07 12:03:54.838943 [INFO] token_provider_  :: Publishing new dummy token: {"id_token":"DEADBEEF","authorization_type":"RFID","prevalidated":false,"connectors":[2]}
2023-12-07 12:03:54.864552 [INFO] auth:Auth        :: Received new token: {
    "authorization_type": "RFID",
    "connectors": [
        2
    ],
    "id_token": "DEADBEEF",
    "prevalidated": false
}
2023-12-07 12:03:54.946596 [INFO] auth:Auth        :: Providing authorization to connector#2
2023-12-07 12:03:55.074159 [INFO] evse_manager_2:  :: EVSE IEC EIM Authorization received
2023-12-07 12:03:55.074557 [INFO] evse_manager_2:  :: EVSE IEC Transaction Started (0 kWh)
2023-12-07 12:03:55.075436 [INFO] evse_manager_2:  :: EVSE IEC AC mode, HLC disabled. We are in X1 so we can go directly to nominal PWM.
2023-12-07 12:03:55.107645 [INFO] auth:Auth        :: Result for token: DEADBEEF: ACCEPTED
2023-12-07 12:03:55.125773 [INFO] evse_manager_2:  :: EVSE IEC Charger state: Wait for Auth->PrepareCharging
2023-12-07 12:03:55.126123 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (100%)
2023-12-07 12:03:55.294671 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (33.33333432674408%)
2023-12-07 12:03:55.767664 [INFO] car_simulator_2  :: { cmd: 'sleep', args: [ 1 ], exec: [Function (anonymous)] }
2023-12-07 12:03:57.017732 [INFO] car_simulator_2  :: {
  cmd: 'draw_power_regulated',
  args: [ 32, 1 ],
  exec: [Function (anonymous)]
}
2023-12-07 12:03:57.018068 [INFO] car_simulator_2  :: { cmd: 'sleep', args: [ 36000 ], exec: [Function (anonymous)] }
2023-12-07 12:03:57.145801 [INFO] evse_manager_2:  ::                                     CAR IEC Event CarRequestedPower
2023-12-07 12:03:57.169310 [INFO] evse_manager_2:  :: EVSE IEC Charger state: PrepareCharging->Charging
2023-12-07 12:03:57.328594 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (33.33333432674408%)
2023-12-07 12:03:57.389184 [INFO] evse_manager_2:  :: EVSE IEC Event PowerOn
2023-12-07 12:04:10.402694 [INFO] evse_manager_1:  :: EVSE IEC Soft overcurrent event (L1:20.036688, L2:20.036688, L3:0, limit 8.8), starting timer.
2023-12-07 12:04:10.403039 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (12.5%)
2023-12-07 12:04:10.985741 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 12:04:21.775762 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (24.49999898672104%)
2023-12-07 12:04:22.312820 [INFO] evse_manager_2:  :: EVSE IEC Soft overcurrent event (L1:32.28976, L2:32.28976, L3:0, limit 28.38), starting timer.
2023-12-07 12:04:22.313120 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (42.16666519641876%)
2023-12-07 12:04:27.513445 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (32.83333480358124%)
2023-12-07 12:04:28.090378 [INFO] evse_manager_2:  :: EVSE IEC Soft overcurrent event (L1:25.181944, L2:25.181944, L3:0, limit 22.88), starting timer.
2023-12-07 12:04:28.090702 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (33.83333086967468%)
2023-12-07 12:04:32.468486 [INFO] ocpp:OCPP        :: Checking if V2GCertificate has expired
2023-12-07 12:04:32.490065 [INFO] evse_security:E  :: Requesting certificate expiry: V2G
2023-12-07 12:04:32.490196 [INFO] evse_security:E  :: Requesting key/pair: V2G
2023-12-07 12:04:32.497553 [WARN] evse_security:E void evse_security::X509CertificateBundle::add_certifcates(const string&, evse_security::EncodingFormat, const std::optional<std::filesystem::__cxx11::path>&) :: Failed to read single certificate from directory file, ignoring entry!
2023-12-07 12:04:32.503412 [INFO] evse_security:E  :: Key found for certificate at path: "/home/anmirazik/checkout/everest-workspace-no-dirty/everest-core/build/dist/etc/everest/certs/client/cso/SECC_LEAF.key"
2023-12-07 12:04:32.551351 [INFO] ocpp:OCPP        :: V2GCertificate is still valid.
2023-12-07 12:04:32.551452 [INFO] ocpp:OCPP        :: Checking if OCSP cache should be updated
2023-12-07 12:04:32.551726 [INFO] ocpp:OCPP        :: OCSP Cache is up-to-date enough
2023-12-07 12:04:32.654585 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (33.166664838790894%)
2023-12-07 12:04:33.230827 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (33.49999785423279%)
2023-12-07 12:04:37.776305 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (33.33333432674408%)
2023-12-07 12:04:38.362670 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (33.33333432674408%)
2023-12-07 12:04:38.793811 [INFO] car_simulator_1  :: { cmd: 'unplug', args: [], exec: [Function (anonymous)] }
2023-12-07 12:04:39.194265 [INFO] evse_manager_1:  ::                                     CAR IEC Event BCDtoEF
2023-12-07 12:04:39.214607 [INFO] evse_manager_1:  ::                                     CAR IEC Event CarRequestedStopPower
2023-12-07 12:04:39.214908 [INFO] evse_manager_1:  :: EVSE IEC Charger state: Charging->Car Paused
2023-12-07 12:04:39.326666 [INFO] evse_manager_1:  :: EVSE IEC Set PWM On (33.33333432674408%)
2023-12-07 12:04:39.448373 [INFO] evse_manager_1:  :: EVSE IEC Event PowerOff
2023-12-07 12:04:39.448754 [INFO] evse_manager_1:  ::                                     CAR IEC Event CarUnplugged
2023-12-07 12:04:39.448960 [INFO] evse_manager_1:  :: EVSE IEC Charger state: Car Paused->StoppingCharging
2023-12-07 12:04:39.449456 [INFO] evse_manager_1:  :: EVSE IEC Set PWM Off
2023-12-07 12:04:39.559686 [INFO] evse_manager_1:  :: EVSE IEC Charger state: StoppingCharging->Finished
2023-12-07 12:04:39.560245 [INFO] evse_manager_1:  :: EVSE IEC Transaction Finished: EVDisconnected (0.053 kWh)
2023-12-07 12:04:39.561104 [INFO] evse_manager_1:  :: EVSE IEC Session Finished
2023-12-07 12:04:39.561293 [INFO] evse_manager_1:  :: SYS  Session logging stopped.
2023-12-07 12:04:39.617045 [INFO] ocpp:OCPP        :: Executing unlock connector callback
2023-12-07 12:04:42.226495 [INFO] car_simulator_2  :: { cmd: 'unplug', args: [], exec: [Function (anonymous)] }
2023-12-07 12:04:42.627945 [INFO] evse_manager_2:  ::                                     CAR IEC Event BCDtoEF
2023-12-07 12:04:42.648241 [INFO] evse_manager_2:  ::                                     CAR IEC Event CarRequestedStopPower
2023-12-07 12:04:42.648494 [INFO] evse_manager_2:  :: EVSE IEC Charger state: Charging->Car Paused
2023-12-07 12:04:42.810535 [INFO] evse_manager_2:  :: EVSE IEC Set PWM On (53.33333611488342%)
2023-12-07 12:04:42.942621 [INFO] evse_manager_2:  :: EVSE IEC Event PowerOff
2023-12-07 12:04:42.943136 [INFO] evse_manager_2:  ::                                     CAR IEC Event CarUnplugged
2023-12-07 12:04:42.943426 [INFO] evse_manager_2:  :: EVSE IEC Charger state: Car Paused->StoppingCharging
2023-12-07 12:04:42.944075 [INFO] evse_manager_2:  :: EVSE IEC Set PWM Off
2023-12-07 12:04:43.053823 [INFO] evse_manager_2:  :: EVSE IEC Charger state: StoppingCharging->Finished
2023-12-07 12:04:43.054371 [INFO] evse_manager_2:  :: EVSE IEC Transaction Finished: EVDisconnected (0 kWh)
2023-12-07 12:04:43.055212 [INFO] evse_manager_2:  :: EVSE IEC Session Finished
2023-12-07 12:04:43.055405 [INFO] evse_manager_2:  :: SYS  Session logging stopped.
2023-12-07 12:04:43.114566 [INFO] ocpp:OCPP        :: Executing unlock connector callback
~~~

Below shows the charger OCPP commands logs

~~~
2023-12-07T04:03:30.958Z: SYS> Session logging started. 


2023-12-07T04:03:32.445Z: SYS> Connecting 


2023-12-07T04:03:32.454Z: SYS> Connected 


2023-12-07T04:03:32.455Z: ChargePoint>CentralSystem BootNotification 
[
  2,
  "7e348825-d154-4b38-a5ba-c3624b8ea09f",
  "BootNotification",
  {
    "chargeBoxSerialNumber": "cp001",
    "chargePointModel": "Yeti",
    "chargePointVendor": "Pionix",
    "firmwareVersion": "0.1"
  }
]

2023-12-07T04:03:32.467Z: CentralSystem>ChargePoint  BootNotificationResponse
[
  3,
  "7e348825-d154-4b38-a5ba-c3624b8ea09f",
  {
    "currentTime": "2023-12-07T04:03:32.459Z",
    "interval": 14400,
    "status": "Accepted"
  }
]

2023-12-07T04:03:32.467Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "c44da50b-0a2e-4ff7-9bea-2a93f1024d05",
  "StatusNotification",
  {
    "connectorId": 0,
    "errorCode": "NoError",
    "status": "Available"
  }
]

2023-12-07T04:03:32.476Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "c44da50b-0a2e-4ff7-9bea-2a93f1024d05",
  {}
]

2023-12-07T04:03:32.476Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "a40801eb-2e2b-41d8-902d-7cf6bde240c7",
  "StatusNotification",
  {
    "connectorId": 1,
    "errorCode": "NoError",
    "status": "Available"
  }
]

2023-12-07T04:03:32.503Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "a40801eb-2e2b-41d8-902d-7cf6bde240c7",
  {}
]

2023-12-07T04:03:32.503Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "bdce1b13-1bea-4c72-9195-b5d8790a4c91",
  "StatusNotification",
  {
    "connectorId": 2,
    "errorCode": "NoError",
    "status": "Available"
  }
]

2023-12-07T04:03:32.512Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "bdce1b13-1bea-4c72-9195-b5d8790a4c91",
  {}
]

2023-12-07T04:03:45.143Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "6345f38f-e7dc-4312-a294-eae504f02953",
  "StatusNotification",
  {
    "connectorId": 1,
    "errorCode": "NoError",
    "status": "Preparing"
  }
]

2023-12-07T04:03:45.160Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "6345f38f-e7dc-4312-a294-eae504f02953",
  {}
]

2023-12-07T04:03:47.144Z: ChargePoint>CentralSystem Authorize 
[
  2,
  "07353fa6-5f51-47e0-92fe-88bc18b0555c",
  "Authorize",
  {
    "idTag": "ABC12345"
  }
]

2023-12-07T04:03:47.153Z: CentralSystem>ChargePoint  AuthorizeResponse
[
  3,
  "07353fa6-5f51-47e0-92fe-88bc18b0555c",
  {
    "idTagInfo": {
      "expiryDate": "2023-12-07T05:03:47.151Z",
      "status": "Accepted"
    }
  }
]

2023-12-07T04:03:47.362Z: ChargePoint>CentralSystem StartTransaction 
[
  2,
  "28ea58ff-441c-4e8a-8096-1a8f9ed7a145",
  "StartTransaction",
  {
    "connectorId": 1,
    "idTag": "ABC12345",
    "meterStart": 0,
    "timestamp": "2023-12-07T04:03:47.302Z"
  }
]

2023-12-07T04:03:47.399Z: CentralSystem>ChargePoint  StartTransactionResponse
[
  3,
  "28ea58ff-441c-4e8a-8096-1a8f9ed7a145",
  {
    "idTagInfo": {
      "expiryDate": "2023-12-07T05:03:47.367Z",
      "status": "Accepted"
    },
    "transactionId": 10
  }
]

2023-12-07T04:03:49.427Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "465ca390-cf6f-4f91-9a93-fc656ad0f1ac",
  "StatusNotification",
  {
    "connectorId": 1,
    "errorCode": "NoError",
    "status": "Charging"
  }
]

2023-12-07T04:03:49.446Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "465ca390-cf6f-4f91-9a93-fc656ad0f1ac",
  {}
]

2023-12-07T04:03:51.964Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "0539b9fb-85dc-419c-ac7a-5d07fbc8804f",
  "StatusNotification",
  {
    "connectorId": 2,
    "errorCode": "NoError",
    "status": "Preparing"
  }
]

2023-12-07T04:03:51.976Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "0539b9fb-85dc-419c-ac7a-5d07fbc8804f",
  {}
]

2023-12-07T04:03:54.895Z: ChargePoint>CentralSystem Authorize 
[
  2,
  "f8369101-f4ee-4187-8210-e47f6617fb76",
  "Authorize",
  {
    "idTag": "DEADBEEF"
  }
]

2023-12-07T04:03:54.902Z: CentralSystem>ChargePoint  AuthorizeResponse
[
  3,
  "f8369101-f4ee-4187-8210-e47f6617fb76",
  {
    "idTagInfo": {
      "expiryDate": "2023-12-07T05:03:54.901Z",
      "status": "Accepted"
    }
  }
]

2023-12-07T04:03:55.137Z: ChargePoint>CentralSystem StartTransaction 
[
  2,
  "a383c0a2-6ac9-4666-a6de-478c5fc04ba5",
  "StartTransaction",
  {
    "connectorId": 2,
    "idTag": "DEADBEEF",
    "meterStart": 0,
    "timestamp": "2023-12-07T04:03:55.074Z"
  }
]

2023-12-07T04:03:55.193Z: CentralSystem>ChargePoint  StartTransactionResponse
[
  3,
  "a383c0a2-6ac9-4666-a6de-478c5fc04ba5",
  {
    "idTagInfo": {
      "expiryDate": "2023-12-07T05:03:55.142Z",
      "status": "Accepted"
    },
    "transactionId": 11
  }
]

2023-12-07T04:03:57.209Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "2e435856-8a90-474f-ad5a-815ec4cdcba5",
  "StatusNotification",
  {
    "connectorId": 2,
    "errorCode": "NoError",
    "status": "Charging"
  }
]

2023-12-07T04:03:57.221Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "2e435856-8a90-474f-ad5a-815ec4cdcba5",
  {}
]

2023-12-07T04:04:39.252Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "247e729c-6e7b-4b30-be53-b8c82c4f9052",
  "StatusNotification",
  {
    "connectorId": 1,
    "errorCode": "NoError",
    "status": "SuspendedEV"
  }
]

2023-12-07T04:04:39.274Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "247e729c-6e7b-4b30-be53-b8c82c4f9052",
  {}
]

2023-12-07T04:04:39.617Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "69cdddb5-5ebb-45d1-aa69-0160e55ba001",
  "StatusNotification",
  {
    "connectorId": 1,
    "errorCode": "NoError",
    "status": "Finishing"
  }
]

2023-12-07T04:04:39.629Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "69cdddb5-5ebb-45d1-aa69-0160e55ba001",
  {}
]

2023-12-07T04:04:39.797Z: ChargePoint>CentralSystem StopTransaction 
[
  2,
  "33d09192-f722-4bb7-bcc0-23907fe11028",
  "StopTransaction",
  {
    "meterStop": 53,
    "reason": "EVDisconnected",
    "timestamp": "2023-12-07T04:04:39.560Z",
    "transactionId": 10
  }
]

2023-12-07T04:04:39.818Z: CentralSystem>ChargePoint  StopTransactionResponse
[
  3,
  "33d09192-f722-4bb7-bcc0-23907fe11028",
  {}
]

2023-12-07T04:04:39.818Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "22f9dd4f-0c29-4438-bffe-72fbe84d3674",
  "StatusNotification",
  {
    "connectorId": 1,
    "errorCode": "NoError",
    "status": "Available"
  }
]

2023-12-07T04:04:39.843Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "22f9dd4f-0c29-4438-bffe-72fbe84d3674",
  {}
]

2023-12-07T04:04:42.719Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "7b681215-1098-4233-8c22-e2c5b5be918e",
  "StatusNotification",
  {
    "connectorId": 2,
    "errorCode": "NoError",
    "status": "SuspendedEV"
  }
]

2023-12-07T04:04:42.730Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "7b681215-1098-4233-8c22-e2c5b5be918e",
  {}
]

2023-12-07T04:04:43.115Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "de07613b-51f1-400c-80f9-53f88603bb6b",
  "StatusNotification",
  {
    "connectorId": 2,
    "errorCode": "NoError",
    "status": "Finishing"
  }
]

2023-12-07T04:04:43.125Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "de07613b-51f1-400c-80f9-53f88603bb6b",
  {}
]

2023-12-07T04:04:43.313Z: ChargePoint>CentralSystem StopTransaction 
[
  2,
  "50fe6bf7-1d7e-4daa-a56e-dc65443fd797",
  "StopTransaction",
  {
    "meterStop": 0,
    "reason": "EVDisconnected",
    "timestamp": "2023-12-07T04:04:43.054Z",
    "transactionId": 11
  }
]

2023-12-07T04:04:43.335Z: CentralSystem>ChargePoint  StopTransactionResponse
[
  3,
  "50fe6bf7-1d7e-4daa-a56e-dc65443fd797",
  {}
]

2023-12-07T04:04:43.335Z: ChargePoint>CentralSystem StatusNotification 
[
  2,
  "9c9f162b-e8e8-41e9-85d5-b7a781dfabb9",
  "StatusNotification",
  {
    "connectorId": 2,
    "errorCode": "NoError",
    "status": "Available"
  }
]

2023-12-07T04:04:43.381Z: CentralSystem>ChargePoint  StatusNotificationResponse
[
  3,
  "9c9f162b-e8e8-41e9-85d5-b7a781dfabb9",
  {}
]


~~~


Now you have an End to End EV charging solution with only Open Source ! Congratulations












