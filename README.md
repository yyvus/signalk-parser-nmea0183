# signalk-parser-nmea0183

[![Greenkeeper badge](https://badges.greenkeeper.io/SignalK/signalk-parser-nmea0183.svg)](https://greenkeeper.io/)

[![Build Status](https://travis-ci.org/SignalK/signalk-parser-nmea0183.svg?branch=development)](https://travis-ci.org/SignalK/signalk-parser-nmea0183)

> A node.js/javascript parser of NMEA0183 sentences. Sentences are parsed to [Signal K delta](http://signalk.org/specification/master/data_model.html#delta-format) format.

## Supported sentences

- [ALK - Seatalk](https://en.wikipedia.org/wiki/Seatalk)
- [APB - Autopilot Sentence "B"](http://www.catb.org/gpsd/NMEA.html#_apb_autopilot_sentence_b)
- [BVE - CruzPro Proprietary Sentence (currently only OP30/60 supported)](http://www.cruzpro.com/op60.html)
- [DBT - Depth Below Transducer](http://www.catb.org/gpsd/NMEA.html#_dbt_depth_below_transducer)
- [DBS - Depth Below Surface](http://www.catb.org/gpsd/NMEA.html#_dbs_depth_below_surface)
- [DBK - Depth Below Keel](http://www.catb.org/gpsd/NMEA.html#_dbk_depth_below_keel)
- [DPT - Depth of Water](http://www.catb.org/gpsd/NMEA.html#_dpt_depth_of_water)
- [DSC - Digital Selective Calling Class-D Radios](http://continuouswave.com/whaler/reference/DSC_Datagrams.html)
- [GGA - Global Positioning System Fix Data](http://www.catb.org/gpsd/NMEA.html#_gga_global_positioning_system_fix_data)
- [GLL - Geographic Position - Latitude/Longitude](http://www.catb.org/gpsd/NMEA.html#_gll_geographic_position_latitude_longitude)
- [HDG - Heading - Deviation & Variation](http://www.catb.org/gpsd/NMEA.html#_hdg_heading_deviation_amp_variation)
- [HDM - Heading - Magnetic](http://www.catb.org/gpsd/NMEA.html#_hdm_heading_magnetic)
- [HDT - Heading - True](http://www.catb.org/gpsd/NMEA.html#_hdt_heading_true)
- KEP - NKE Performance data
- [MDA - Meteorological Composite](http://catb.org/gpsd/NMEA.html#_mda_meteorilogical_composite)
- [MTW - Mean Temperature of Water](http://catb.org/gpsd/NMEA.html#_mtw_mean_temperature_of_water)
- [MWV - Wind Speed and Angle](http://www.catb.org/gpsd/NMEA.html#_mwv_wind_speed_and_angle)
- [RMB - Recommended Minimum Navigation Information](http://www.catb.org/gpsd/NMEA.html#_rmb_recommended_minimum_navigation_information)
- [RMC - Recommended Minimum Navigation Information](http://www.catb.org/gpsd/NMEA.html#_rmc_recommended_minimum_navigation_information)
- [ROT - Rate of Turn](http://www.catb.org/gpsd/NMEA.html#_rot_rate_of_turn)
- [RPM - Revolutions](http://www.catb.org/gpsd/NMEA.html#_rpm_revolutions)
- [RSA - Rudder Sensor Angle](https://gpsd.gitlab.io/gpsd/NMEA.html#_rsa_rudder_sensor_angle)
- [VDM - AIS Other Vessel Data](http://catb.org/gpsd/AIVDM.html)
- [VDO - AIS Own Vessel Data](http://catb.org/gpsd/AIVDM.html)
- [VDR - Set and Drift](http://www.catb.org/gpsd/NMEA.html#_vdr_set_and_drift)
- [VHW - Water Speed and Heading](http://www.catb.org/gpsd/NMEA.html#_vhw_water_speed_and_heading)
- [VLW - Distance Traveled through Water](http://www.catb.org/gpsd/NMEA.html#_vlw_distance_traveled_through_water)
- [VPW - Speed - Measured Parallel to Wind](http://www.catb.org/gpsd/NMEA.html#_vpw_speed_measured_parallel_to_wind)
- [VTG - Track Made Good and Ground Speed](http://www.catb.org/gpsd/NMEA.html#_vtg_track_made_good_and_ground_speed)
- [VWR - Relative Wind Speed and Angle](http://www.catb.org/gpsd/NMEA.html#_vwr_relative_wind_speed_and_angle)
- [ZDA - UTC day, month, and year, and local time zone offset](http://www.trimble.com/oem_receiverhelp/v4.44/en/NMEA-0183messages_ZDA.html)

**Note:** *at this time, unknown sentences will be silently discarded.*

## Usage

### JavaScript API

```javascript
const Parser = require('@signalk/nmea0183-signalk')
const parser = new Parser()

try {
  const delta = parser.parse('$SDDBT,17.0,f,5.1,M,2.8,F*3E')
  if (delta !== null) {
    console.log(`[delta] ${JSON.stringify(delta, null, 2)}`)
  }
}
catch (e) {
  console.error(`[error] ${e.message}`)
}
```

### Command line

In addition to usage in your code, the parser can be used on the command-line if installed globally (`npm install --global`). This allows you to pipe data from one program into the parser directly, without using a Signal K server. The parser holds no Signal K tree in memory (a big change vs. 1.x), so the output will be stringified [Signal K delta](http://signalk.org/specification/master/data_model.html#delta-format) messages.

```bash
$ echo '$SDDBT,17.0,f,5.1,M,2.8,F*3E' | nmea0183-signalk
```

## NMEA0183v4 tag blocks

This parser has (limited) support of [NMEA0183v4 tag blocks](http://www.nmea.org/Assets/may%2009%20rtcm%200183_v400.pdf) (e.g. `\s:airmar dst800,c:1438489697*13\$SDDBT,17.0,f,5.1,M,2.8,F*3E`).
Keep in mind that, since NMEA uses the backslash `\` as the start and end character of the tag block, you need to escape these characters *before* parsing them.
This is necessary because javascript treats the backslash as the escape character causing it not to be included in the resulting string (unless escaped).

Example:

```javascript
const Parser = require('@signalk/nmea0183-signalk')
const parser = new Parser()

try {
  const delta = parser.parse('\\s:airmar dst800,c:1438489697*13\\$SDDBT,17.0,f,5.1,M,2.8,F*3E')
  if (delta !== null) {
    console.log(`[delta] ${JSON.stringify(delta, null, 2)}`)
  }
}
catch (e) {
  console.error(`[error] ${e.message}`)
}
```

Output:
```json
[delta] {
  "updates": [
    {
      "source": {
        "sentence": "DBT",
        "talker": "airmar dst800",
        "type": "NMEA0183"
      },
      "timestamp": "2015-08-02T04:28:17.000Z",
      "values": [
        {
          "path": "environment.depth.belowTransducer",
          "value": 5.1
        }
      ]
    }
  ]
}
```

**Note:** *at this time, the checksum of the tag block (`c:1438489697*13`) is not validated.*

## License

```
Copyright 2016/2017 Signal K and Fabian Tollenaar <fabian@signalk.org>.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
