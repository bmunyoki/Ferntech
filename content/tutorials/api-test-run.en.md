---
title: "API Test Run"
date: 2019-05-14T11:02:05+06:00
type: "post"
author: "Somrat"
---


 
### Introduction
Welcome to our API!

This guide helps you navigate your way from logging in, all the way to extracting data for a particular parameter for a certain device.

### Login
Use the following URL with your username and password to login:

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/auth/login/
</div>

Once logged in, one should have view only access. Using any link in the remaining part of this guide on a browser should return data in a JSON format within the browser window.

### Getting Company List
This URL provides a straight list of companies to which you have access as a Ferntech user:

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/companies/
</div>

To get the company list in JSON format, follow the following URL: 

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/companies/hierarchy/
</div>

The JSON also shows how the companies are related to each other. This is how the hierarchy is represented in the portal:


![image example](/Ferntech/static/images/companies-list.png "image")

### System List for a Company
From the company list query above, each company has a company ID. For this test run, we shall be using company ID 5 (Asantys marked in Magenta). The query below provides a list of systems which belong to the Asantys company:

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/systems/for-company/5/?paginate=1
</div>

To get a single system, pass the system ID as show in the below query. The system we are interested in is has ID 4.

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/systems/4/
</div>

To have a better feel of what the Asantys system consists of, below is a single line communication diagram, where UMC = Universal Microgrid Controller.

![image example](/Ferntech/static/images/Asantys.png "image")

### Controller List for a System
Now that we know the company and the system, we can the system ID to the following endpoint to get the controllers assigned to the system.

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/systems/controller-instances/?system_id=4
</div>

The above produces a list of 3 controllers assigned to the system, each of which has specific subsystems assigned to them. The ID of the controllers which belong to this system are IDs 8, 9 and 10

<div class="alert rounded-0 alert-secondary">
	ID 8 (https://api-staging.infinitefingers.com/api/systems/controller-instances/8/)<br>
	ID 9 (https://api-staging.infinitefingers.com/api/systems/controller-instances/9/)<br>
	ID 10 (https://api-staging.infinitefingers.com/api/systems/controller-instances/10/)
</div> 

### Getting Details of a Subsystem

#### Driver Meta Files
Now we are going to look at the Studer subsystem which has ID 10 (ID = 10). This subsystem is represented by the Studer XCom-CAN which is a bit like a gateway into the Studer world. Each subsystem in the Ferntech ecosystem is represented by driver and each driver is defined by two files:

1. JSON Meta File - This file describes what can be contained within the config file.
2. JSON Config File - Contains the actual configuration and log parameter values for a particular device

<br>
For this particular system, the following number and type of Studer devices can be connected to a single XCom-CAN at once:

<li>9 x Xtenders</li>
<li>15 x Variostring</li>
<li>1 x BSP</li>
<li>15 x Variotracks</li>

The above information is defined in the meta file which is returned with the subsystem instance. For example, to get Studer with the ID 10 (ID = 10), use the following:

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/systems/subsystem-instances/10/
</div> 

With the result, you can search for the following text:

	"devices": {
		"bsp": 1,
		"xtender": 9,
		"variotrack": 15,
		"variostring": 15
	}

#### Get Devices Connected to the Subsystem
To get the number of devices connected to a given substem, browse the following URL,

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/systems/subsystem-instances/get-subsystem-info/
</div> 

Then enter the following JSON into the context textfield and hit <strong>POST</strong> button

	{
		"subsystem_instance": 10,
		"controller_instance": 9
	}

The produced result is as show below which has information about the devices connected to this Studer subsystem.

	{
		"mqtt_response": {
			"success": true,
			"message": {
				"device_models": {
					"variostring": {
						"1": "VS-120",
						"2": "VS-120"
					}
				},
				"device_numbers": {
					"xtender": 0,
					"variostring": 2,
					"bsp": 0,
					"variotrack": 0
				},
				"device_phase": {
					"xtender": {}
				},
				"device_serials": { 
					"variostring": {
						"1": "490613C5",
						"2": "48EA0245"
					}
				},
				"device_software": {
					"variostring": {
						"1": "1.6.24",
						"2": "1.6.24"
					}
				}
			}
		}
	}


#### Getting the Device Configuration
To find how the devices are configued, run the following query:

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/systems/subsystem-instances/get-config/
</div>

and enter the below JSON in the contest field:

	{
		"subsystem_instance": 10,
		"controller_instance": 9
	}

In among the response, there will be a config field which contains the actual config parameters as follows (Note this is just an extract)

	"config": {
		"variostring": {
			"min_delay_cycle": {
				"value": 1.0
			},
			"new_cycle_v1_delay": {
				"value": 30.0
			}, 
			"abs_duration": {
				"value": 120.0
			},
			"equ_current": {
				"value": 80.0
			},
			"equ_voltage": {
				"value": 62.4
			},
			"new_cycle_v2": {
				"value": 47.2
			},
			"abs_end_curr_trig": {
				"value": 0
			},
			"equ_before_abs": {
				"value": 1
			},
			"aux_2_relay_mode": {
				"value": 1.0
			},
			"new_cycle_v1": {
				"value": 48.8
			},
			"watchdog": {
				"value": 0
			}
		}
	}

#### Getting Logged Parameters for a Device
Browse the following endpoint to get the logged parameters for a device

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/systems/subsystem-instances/get-config/
</div>

and enter the same JSON in the context field

	{
		"subsystem_instance": 10,
		"controller_instance": 9
	}

The returned result is as follows which tells you what parameters are being logged in seconds and how often (Note the result below is an extract)

	{
		"mqtt_response": {
			"success": true,
			"message": {
				"variostring": {
					"pv_2_a": {
						"frequency": 60
					},
					"pv_a": {
						"frequency": 60
					},
					"pv_1_w": {
						"frequency": 60
					},
					"pv_1_v": {
						"frequency": 60
						},
						"batt_a": {
							"frequency": 60
						},
						"scc_op_state": {
							"frequency": 60
						},
						"batt_phase": {
							"frequency": 60
						},
						"pv_2_v": {
							"frequency": 60
						},
						"pv_2_w": {
							"frequency": 60
						}
					}
				}
			}
		}
	}

#### Extracting Historical Data
Browse the following URL to extract historical data 

<div class="alert rounded-0 alert-secondary">
	https://api-staging.infinitefingers.com/api/systems/subsystem-instances/historical-data/
</div>

and enter the below JSON in the context field

	{
		"subsystem_instance": 10,
		"controller_instance": 9, 
		"start_datetime": "2018-10-11 10:00:00.00", 
		"end_datetime": "2018-10-11 14:00:00.00", 
		"device": "variostring", 
		"field": "batt_a", 
		"time": "30m"
	}

The returned reponse looks like:

	[
		{
			"time": "2018-10-11T10:00:00Z",
			"mean": 6.0645259533895
		},
		{
			"time": "2018-10-11T10:30:00Z",
			"mean": 6.2857134975276665
		},
		{
			"time": "2018-10-11T11:00:00Z",
			"mean": 6.025993114406666},
		{
			"time": "2018-10-11T11:30:00Z",
			"mean": 6.249208818856167
			},
		{ 
			"time": "2018-10-11T12:00:00Z",
		    "mean": 6.042058395127333},
		{
			"time": "2018-10-11T12:30:00Z",
			"mean": 6.3448192531780006
		},
		{
			"time": "2018-10-11T13:00:00Z",
			"mean": 1.514127328301715
		},
		{
			"time": "2018-10-11T13:30:00Z",
			"mean": 4.700638903601667
		},
		{
			"time":"2018-10-11T14:00:00Z",
			"mean":5.2570842161
		}
	]