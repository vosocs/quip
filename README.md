QUIP
====

**QUality Information Protocol (QUIP) is a standard quality assurance data transfer format for radiation-producing medical equipment QA data.**

##Purpose

Though the data associated with medical radiation equipment tests is somewhat standard, it is expressed in many different formats, making it difficult to transfer between sites and devices. QUIP aims to be a standard format for this data, making the transfer of this data between information systems as easy as transferring images using the DICOM format. (In fact, we hope it is much easier!)


##Example

Here is a very basic example of the flatness measurement data taken by a Sun Nuclear DailyQA 3 on a Varian TrueBeam expressed in QUIP format. See JSON_data_example.json for a comprehensive example data item.

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
                        }
                    ] 			//.[data-values]
                }]			//.[tests]
        }]				//.[linacs]
}
```

##Structure

The basic structure of a QUIP data set looks like this:

```
linac{
	serial_number,
	configuration_data{
		energy{},
		fieldsize{},
		source-to-surface-distance{}
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

###Alternate Mapping Key-Value Pairs
An array of alternate mapping data can be sent in place of a serial number if necessary.

```
'alternate-mapping-key-value-pairs': [
	{'value': '123456', 'key': 'dqa3-id'},
	{'value': '1011', 'key': 'linac-serial'},
	{'value': 'My Truebeam', 'key': 'linac-name'}
	]
```

Any set of keys can be sent through a QUIP as long as the receiving system has the ability to link this mapping data to a device in the destination data system.


###configuration
The configuration data communicates the device configuration at the time the test data was recorded. The current configuration data options are appropriate for a linear accelerator.

####energy
* energy-type
  * name: (string)
    * "PHOTON"
    * "ELECTRON"
    * "PROTON"
* units
  * name: (string)
    * "kV" for kilo-voltage photons
    * "MV" for mega-voltage photons
    * "MeV" for mega-voltage electrons
    * "MV SRS" for sterotactic radiosurgery photons
    * "MV FFF" for flattening filter free mode
  * value: (float)

####gantry
* angle
  * units: (string)
    * almost always "DEGREES". Don't complicate everyones' lives with radians.
  * value: (float)

####field-size
* x
  * units (string)
    * name (string)
      * we recommend using "CENTIMETERS" as the standard unit of measure in all QA data being transferred
  * value (float)
* y
  * units (string)
    * name (string)
      * we recommend using "CENTIMETERS" as the standard unit of measure in all QA data being transferred
  * value (float)

####source-to-surface-distance
* units
	* name (string)
* value (float)

####dose
* units
	* name (string)
	  * should be "MONITOR_UNIT", "CENTIGRAY", or similar
* value (float)

####dose-rate
* units
  * name (string)
    * should be "MU_PER_MINUTE", "CGY_PER_MINUTE", or similar
* value (float)

###tests
The data for any test that corresponds to this machine and machine configuration can be added as an item in the "tests" array. Test data collected on different days can be included in the same array.

Each item in the tests array may have the following identifying information:

* device (hash)
  * type (string)
    * name of the collection device, e.g. "BEAMCHECKER", "DQA3", or similar
  * serial-number (string)
* performed-on-date (datetime)
  * e.g. "24 Nov 2009 16:45:49 -0400"
* is-baseline (boolean)
  * used to flag a data point as a baseline measurement, used in future comparisons
* temperature (hash)
  * units (hash)
    * name (string)
      * e.g. "CELSIUS"
  * value (float)
* atmospheric-pressure (hash)
  * units (hash)
    * name (string)
* data-values (array)

####The data-values Array
Each item in an item in the test array has an array of data values. Each individual test measurement will have its own item in the data-values array.

* test-type (string)
  * e.g. "FLATNESS", "SYMMETRY", "OUTPUT-FACTOR"
* unit (string)
  * e.g. "PERCENTAGE", "CENTIGRAY", "HU", or similar
* is-baseline (boolean)
* value (float, boolean, string)
* baseline-comparison (string)
  * some tests express their values as a percentage of a baseline value, a difference from baseline, or a difference from a nominal value. This field allows the data source to specify how the data value is to be used in comparison to a baseline.
* attribute-list (array of key-value pairs)
  * type (string)
    * e.g. "PLANE_DIRECTION"
  *  value (string)
    * e.g. "AXIAL"




















