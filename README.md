


 

### robot-block-lib
robot block is a hardware abstraction layer for ROS that automatically creates ROS communication channels for supported sensors actuators and interfaces. This aims to replace low-level code and firmware, with an experience that's more similar to defining docker containers. 

This is a core repository of supported devices. We encourage everyone to submit a PR and add your own. 

## supported devices
| Name      | Type.  |Desc. 
| ----------- | ----------- | ----------- | 
| **ADS1015** | Interface | 4-channel 12-bit I2C ADC | 
| **pi-gpio** | Interface | Raspberry Pi GPIO Wrapper | 
| **VL53L1** | Range Sensor | ToF Sensor |
| **VL53L1_Array** | Range  Sensor | Configurable array of ToF Sensors |
| **ICM20948** | Motion Sensor | 9-Axis MEMS IMU  |
| **LSM9DS1** | Motion Sensor | 6-Axis MEMS IMU |
| **SparkfunTwist** | Sensor | Sparkfun Dual Encoder Reader |
| **INA219** | Power Sensor | Voltage/Current/Power Sensor |
| **4245-PSOC** | Motor Driver | Serial/I2C Motor Driver found in Sparkfun Auto pHat  |
| **pi-cam** | Camera | Official Raspberry Pi Camera (V4L2) |

### Planned

| LIBRARY      | Type.  |Desc. |
| ----------- | ----------- | ----------- | 
| **PCA9685** | Interface | 16-channel 12-bit I2C PWM | 
| **SRF04/05** | Range Sensor| Affordable Hobby Ultrasound Sensor |
| **pi-motor** | Motor Driver | Direction + PWM Motor Driver for Raspberry PI |
| **steering** | Motor Driver | DC Power Motor + Steering Servo |
| **arduino-firmata** | Interface | Arduino Support in Firmata Mode |
| **arduino-ros** | Interface | Native ROS support for Arduino  |


### How to write your own 
You can basically write any valid python code as long as some conventions are followed. Let's take ADS1015, a 4-channel Analog-Digital Converter as an example.

1) Package folder must contain a JSON configuration file that defines it's properties, dependencies, ros message types and callback functions. All fields in this example are mandatory. We also take care of dependencies for your as long as you add them to your config. (currently only pip packages are supported) 

```json
{
	"name":  "ADS1115",
	"info":  "4-channel 12-bit I2C ADC",
	"dependencies":  [

		{
		"type":  "pip3",
		"package":"adafruit-circuitpython-ads1x15"
		}

	],
	"callback":  ["read0","read1","read2","read3"],
	"ros_message":  ["std_msgs.msg",  "Int32"]
}
```
2) For the dynamic imports to work, folder name, config file, python file and class constructor must all share the same name: ADS1015, ADS1015.json, ADS1015.py, ``ADS1015(args) ``

3) devices with multiple channels  must expose ``read`` and ``update`` callbacks for each channel:  ``["read0","read1","read2","read3"] ``

4) You can make use of the utility functions in robot-block as they get imported at runtime. Take a look at [utils.py](https://github.com/cristidragomir97/robot-block/blob/master/robot/src/core/utils.py)


#### compatibility 
Due to particularities on how low level interfaces (I2C/CSI/etc) work in different SBCs, robot-block and it's packages are limited to the following boards
* Raspberry Pi 3/4/Zero2
* Jetson Nano
* x86 (with USB-to-I2C Bridge)