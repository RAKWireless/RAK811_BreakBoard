*THIS IS THE DRAFT OF THE TRACKING PROTOCOL. IT IS NOT IMPLEMENTED YET*
# Protocol description LoRaWAN GPS tracker
## Position information
Position information are sent on fport 1.

### Payload format
The payload consists of 12 bytes and looks like this: `LLLlllAAHCS`. The length is maximum of 11 bytes which complies with the recomondation of The Things Network to stay under 12 bytes.  
`LLL` Latitude  
`lll` Longitude  
`AA` Altitude  
`H` HDOP  
`C` course  
`S` Speed  
HDOP is optional, but you have to send it (or `0x00` if you don't have HDOP info) if you want to send direction and speed. Direction and speed ar optional, but only as a group. Eighter you send both or none of them.

### Coding of the latitude `LLL`
The latitude can reach from -90° to 90°. As we use 3 bytes for the latitude, we map this values to 0x000000 to 0xFFFFFF. `lat` is the double value taken from the gps receiver, `latitude` will be our encoded value:  
```latitude = (lat+90)/180 * 0xFFFFFF```  
This results in a resolution of 0.00001 degrees, which is about 1.2m (or about 4 ft in retarded units). This should be enough, as most (affordable) GPS won't reach this precision.

### Coding of the longitude `lll`
The longitude can reach from -180° to 180°. As we use 3 bytes for the longitude, we map this values to 0x000000 to 0xFFFFFF. `lon` is the double value taken from the gps receiver, `longitude` will be our encoded value:  
```longitude = (lon+180)/360 * 0xFFFFFF```  
This results in a resolution of 0.00002 degrees. Now the distance resolution depends on which latitude we are. On the equator, it's about 2.4m (8 ft). In central europe (Lake Constance area) it results in a resolution of 1.6m (5 ft), on the polar circle just about one meter (3 ft). This should be enough, as most (affordable) GPS won't reach this precision.

### Coding of altitude `AA`
A realistic range for the altitude of a LoRaWAN-tracker, I assume the Dead Sea as the lowest point in question. It is about 428m below main sea level. So we set the lower boundary to -500m (as you know, they got a dry out problem there and the level decreases constantly...). As for the maximum, there is Mount Everest. At the time of writing, the closest TTN-gateway is in New Delhi but who knows, the network develops fast. The hight of Mount Everest is 8848m, so we set the maximum to 9000m. This means that we got to map a range of 9500m with an ofset of 500m to 0x0000..0xFFFF. `alt` is the double value taken from the gps receiver, `altitude` will be our encoded value:  
```altitude = (alt+500)/9500 *0xFFFF```  
I recomend to set the value to 0x0000 if the altitude is below -500m and to 0xFFFF if it is above 9000m. The altitude resolution is ~14.4cm or ~6".  
If you use the tracker for a balloon-project, you might want to alter the mapping. For example, if you map 0...50'000m to 0x0000..0xFFFF the resolution would still be about 70cm, enough for a balloon project I suppose. *A future version of the protocol might implement the mapping from -500..50000m.*

### Coding of HDOP `H`
Minimum HDOP per Definition is 1. A value below 4 usualy is regarded as good, a value above 8 is regarded bad. So we just multiply the value by 10 and map 0..25.5 to 0x00 to 0xFF. `hdop_float` is the value from the gps receiver, `hdop` will be our encoded value:  
```hdop=hdop_float*10```  
If you got no HDOP from the GPS, just set the value to 0x00, as values below 0xA0 should not be possible according to the HDOP-definition. Any hdop above 25.5 should be set to 0xFF. For a tracker, normaly the HDOP is not too interesting, but I included it because ttnmapper uses this value.

### Coding of direction `C`
Course and speed are not too interesting in a LoRaWAN-tracer-project, as usually the interval between messages is long. So course and speed are optional and very coarse. Course will be encoded into one byte, so we map 0°..360° into 0x00..0xFF. `cou` is the direction from the gps receiver that will be encoded into `course`:  
```course=cou/360*0xFF```  
The resolution of the course is about 1.4°.

### Coding of Speed `S`
The speed (in m/s) will be encoded from 0..100m/s to 0x00..0xFF. This should be enough even for the german Autobahn as I think there are very few cars will go faster than 360km/h. `spe` from the GPS receiver will be encoded into `speed`:  
```speed=spe/100*0xFF```  
The resolution of the speed is 0.4m/s, which is about 1.4km/h, 0.9mph or 0.8knots. Speeds above 100m/s should be encoded into 0xFF.

## Sample TTN payload decoder
```function Decoder(bytes, port) {
  // Decode an uplink message from a buffer
  // (array) of bytes to an object of fields.
  var decoded = {};
    
  // Chek if we got position information (port 1)
  if (port == 1) {
    decoded.latitude = (bytes[0] << 16 | bytes[1] << 8 | bytes[2]) * 180 / 0xFFFFFF - 90;
    decoded.longitude = (bytes[3] << 16 | bytes[4] << 8 | bytes[5]) * 360 / 0xFFFFFF - 180;
    decoded.altitude = (bytes[6] << 8 | bytes[7]) * 9500 / 0xFFFF - 500;

    //check if we got a valid HDOP field
    if (bytes.length > 8 && bytes[8] > 0) {
      decoded.hdop = bytes[8] / 10;
    }

    //check if we got course and speed
    if (bytes.length == 11) {
      decoded.course = (bytes[9] * 360 / 255);
      decoded.speed = (bytes[10] * 100 / 255);
    }
  }
  return decoded;
}
```
