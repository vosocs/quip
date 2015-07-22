QUIP
====

**QUality Information Protocol (QUIP) is a standard quality assurance data transfer format for radiation-producing medical equipment QA data.**

##Purpose

Though the data associated with medical radiation equipment tests is somewhat standard, it is expressed in many different formats, making it difficult to transfer between sites and devices. QUIP aims to be a standard format for this data, making the transfer of this data between information systems as easy as transferring images using the DICOM format. (In fact, we hope it is much easier!)


##Example

Here is a very basic example of the flatness measurement data taken by a Sun Nuclear DailyQA 3 on a Varian TrueBeam expressed in QUIP format. See JSON_data_example.json for a comprehensive example data item.

```
{
  "machines": [
    {
      "id": null,
      "serial-number": "H199999",
      "configuration": {
        "machine-configuration-value-groups": [
          {
            "machine-configuration-values": [
              {
                "field-code": "FIELDSIZE_X",
                "unit-code": "cm",
                "value": "20"
              },
              {
                "field-code": "FIELDSIZE_Y",
                "unit-code": "cm",
                "value": "20"
              }
            ]
          }
        ],
        "machine-configuration-values": [
          {
            "field-code": "ENERGY",
            "unit-code": "MeV",
            "alt": "MV",
            "value": "6"
          }
        ]
      },
      "tests": [
        {
          "device": {
            "type": "DQA3",
            "serial-number": "99999999"
          },
          "performed-on-date": "12 Jul 2015 10:01:53 -0700",
          "is-basline": false,
          "data-values": [
            {
              "test-raw-data-value-code": "DOSE",
              "unit": "CENTIGRAY",
              "is-baseline": false,
              "value": 99.0037868714
            }
          ]
        }
      ]
    }
  ]
}

```

##Structure

The basic structure of a QUIP data set looks like this:

```
linac{
	serial_number,
  machine-configuration-value-groups{
    {
      fieldsize_x{},
      fieldsize_y{},
    }
  },
	machine-configuration-values{
		energy{},
		source-to-surface-distance{}
	},
	tests{
		device{},
		performed-on-date,
		data-values[{
			test-value-code,
			unit,
			value,
			is-baseline
		}]
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
* angle (hash)
  * units: (string)
    * almost always "DEGREES". Don't complicate everyones' lives with radians.
  * value: (float)

####field-size
* x
  * units (hash)
    * name (string)
      * we recommend using "CENTIMETERS" as the standard unit of measure in all QA data being transferred
  * value (float)
* y
  * units (hash)
    * name (string)
      * we recommend using "CENTIMETERS" as the standard unit of measure in all QA data being transferred
  * value (float)

####source-to-surface-distance
* units (hash)
	* name (string)
* value (float)

####dose
* units (hash)
	* name (string)
	  * should be "MONITOR_UNIT", "CENTIGRAY", or similar
* value (float)

####dose-rate
* units (hash)
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

* test-value-code (string)
  * e.g. "FLATNESS", "SYMMETRY", "OUTPUT-FACTOR"
  * test-value-codes should contain deviations from a desired value. If a calculation is required to compute a deviation, that data should go in a test-raw-data-value-code.
* test-raw-data-value-code (string)
  * Data points used to calculate a deviation from desired value should be sent in test-raw-data-value-code. The receiving system can be configured to process these values in a specific manner.
* unit (string)
  * e.g. "PERCENTAGE", "CENTIGRAY", "HU", or similar
* is-baseline (boolean)
* value (float, boolean, string)
* attribute-list (array of key-value pairs)
  * type (string)
    * e.g. "PLANE_DIRECTION"
  *  value (string)
    * e.g. "AXIAL"
