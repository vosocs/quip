QUIP
====

**QUality Information Protocol (QUIP) is a standard quality assurance data transfer format for radiation-producing medical equipment QA data.**

###Purpose

Though the data associated with medical radiation equipment tests is somewhat standard, it is expressed in many different formats, making it difficult to transfer between sites and devices. QUIP aims to be a standard format for this data, making the transfer of this data between information systems as easy as transferring images using the DICOM format. (In fact, we hope it is much easier!)


###Example

Here is an example of the flatness and symmetry measurement data taken by a Sun Nuclear DailyQA 3 on a Varian TrueBeam expressed in QUIP format.

```
{
    "linacs": [
        {
            "serial-number": "TB1234",
            "configuration": {
                "energy": {
                    "energy-type": {
                        "name": "PHOTON"
                    },
                    "units": {
                        "name": "MV"
                    },
                    "value": 6.0
                },
                "field-size": {
                    "x": {
                        "units": {
                            "name": "CENTIMETERS"
                        },
                        "value": 20.0
                    },
                    "y": {
                        "units": {
                            "name": "CENTIMETERS"
                        },
                        "value": 20.0
                    }
                },
                "source-to-surface-distance": {
                    "units": {
                        "name": "CENTIMETERS"
                    },
                    "value": 100.0
                }
            },
            "tests": [
                {
                    "device": {
                        "type": "DQA3",
                        "serial-number": "31419"
                    },
                    "performed-on-date": "22 Jul 2014 16:45:49 -0800",
                    "is-basline": False,
                    "temperature": {
                        "units": {
                            "name": "CELSIUS"
                        },
                        "value": 21.5
                    },
                    "data-values": [
                        {
                            "test-type": "FLATNESS",
                            "unit": "PERCENTAGE",
                            "is-baseline": True,
                            "value": "1.03",
                            "baseline-comparison": "DIFFERENCE_FROM_BASELINE",
                            "attribute-list": [
                                {
                                    "type": "PLANE_DIRECTION",
                                    "value": "COMBINED"
                                }
                            ]
                        },
                        {
                            "test-type": "SYMMETRY",
                            "unit": "PERCENTAGE",
                            "is-baseline": True,
                            "value": "-0.11",
                            "baseline-comparison": "DIFFERENCE_FROM_BASELINE",
                            "attribute-list": [
                                {
                                    "type": "PLANE_DIRECTION",
                                    "value": "TRANSVERSE"
                                }
                            ]
                        }
                    ] //.[data-values]
                }]//.[tests]
        }]//.[linacs]
}
```

###Structure

The basic structure of a QUIP data set looks like this:

```
linac{
	serial_number,
	configuration_data{
		energy,
		fieldsize,
		source-to-surface-distance
	},
	tests{
		device{},
		performed-on-date,
		data-values{
			test-type,
			unit,
			value,
			attribute-list[]
		}
	}
}
```

A QUIP needs to have enough identifying information for a data system to link a data set to a particular medical device, and this is best done with a serial number. 

####Alternate Mapping Key-Value Pairs
An array of alternate mapping data can be sent in place of a serial number if necessary.

```
'alternate-mapping-key-value-pairs': [
	{'value': '77030074', 'key': 'dqa3-id'},
	{'value': '1157', 'key': 'linac-serial'},
	{'value': 'LA5 Truebeam', 'key': 'linac-name'}
	]
```

Any set of keys can be sent through a QUIP as long as the receiving system has the ability to link this mapping data to a device in the destination data system.


####Configuration Data
The configuration data communicates the device configuration at the time the test data was recorded. The current configuration data options are appropriate for a linear accelerator.

#####Energy
* energy-type
  * name: (string)
* units
  * name: (string)
    * kV for kilo-voltage photons
    * MV for mega-voltage photons
    * MeV for mega-voltage electrons
    * MV SRS for sterotactic radiosurgery photons
    * MV FFF for flattening filter free mode
  * value: (float)

#####Gantry
* angle
  * units: (string)
  * value: (float)



















