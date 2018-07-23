## RAK811 BreakBoard (For RAK811 TrackerBoard and RAK811 SensorNodeBoard)
### chip： RAK811 + GPS + MEMS + charge battery
#### Support CoIDE/Keil5
#### Base on semtech LoRaWAN1.0.2

The document please go to : http://www.rakwireless.com/en/download

### Software version V1.1.3
### Update record:
1.  Add the AT command, the command list is shown below;
2.  Modify the time of sending data, and modify it to send a packet of data for 60s;
3.  Integrate all frequency bands, support band switching, and switch frequency bands by AT command;

     AT command list: \
     at+region=EU868/US915/AS923/AU915/IN865/KR920    // Set device region
     
     at+dev_eui=xxxxxxxxxxxxxxxx                      //  Set device dev_eui  \
     at+app_eui=xxxxxxxxxxxxxxxx                      // Set device app_eui  \
     at+app_key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx      //  Set device app_key 

     at+dev_addr=xxxxxxxx                             // Set device dev_addr \
     at+nwks_key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx     //  Set device nwks_key  \
     at+apps_key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx     // Set device apps_key  

     at+join_mode=otaa/abp                            // Set the device to join otaa or abp \

     (Note:Please set the region first, then set the join method.)  \
     After the parameter setting is completed, reset the device enable parameter.
