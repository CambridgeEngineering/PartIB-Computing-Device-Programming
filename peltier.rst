Peltier cells
=============



Preparation of a cell
---------------------



`Peltier cells <https://en.wikipedia.org/wiki/Thermoelectric_cooling>`_ use the `Peltier effect <https://en.wikipedia.org/wiki/Thermoelectric_effect#Peltier_effect>`_ to pump heat from one plate to another of the device. The flux of heat is roughly proportional to the current passing through the peltier cell. The image below provides a schematic representation of a Peltier cell.


.. figure:: https://upload.wikimedia.org/wikipedia/commons/a/a2/Peltierelement.png
   :scale: 50 %
   :alt: Peltier cell

   Peltier cell. Source: Wikipedia


- One of the two plates of the Peltier cell is engineered to be hot. The other to be cold. Please attach the thermal adesive on both sides, then attach the heat sink to the cold side. To identify the hot side please refer to the [datasheet](./peltier_datasheet.pdf). For the particular model provided, place the cell on the bench/desk with the black cable on the right/down and red cable on the left/down, then the top plate is the hot plate. 

- Connect the Peltier cell to the MAX14870 Driver. The red cable to the **M1** pin and the black cable to the **M2** pin (but first be sure that your duty cycle is at zero!)

- Place the Peltier cell on your desk/bench with the heat sink in contact with the bench surface and the hot side exposed to the air. Then place the temperature sensor on the hot side (fix it with standard tape).


.. figure:: images/peltier_bench.jpg
   :scale: 50 %
   :alt: Peltier cell

   Peltier bench realization.




Constant heat transfer and sensor readings
------------------------------------------


The following code drives the Peltier cell with a small voltage and monitors the temperature of the cold side of the Peltier cell by cyclically reading the temperature sensor. Temperature sensor reading is made available to the user through serial.

The code to drive the Peltier cell is similar to the one for LED and bulb. 
Setting and reading of the temperature sensor is realized through minor adaptations of the code you have developed in Activity 2. In fact, you will need to connect the temperature sensor to the pins **D14** and **D15** as in Activity 2.

The code uses the `Ticker <https://os.mbed.com/docs/mbed-os/v5.13/apis/ticker.html>`_ interface to set up recurring interrupts. Recurrent interrupts allow to read sensors and send information on the serial port at precise time intervals. 



   .. code-block:: c

	#include "mbed.h"
	#include "stdint.h"

	// *** PWM driver: pins and variables 
	PwmOut peltierpwm(PA_7);
	DigitalOut DIR(D5);

	// *** Temperature sensor: pins and variables 
	#define LM75_REG_TEMP (0x00) // Temperature Register
	#define LM75_REG_CONF (0x01) // Configuration Register
	#define LM75_ADDR     (0x90) // LM75 address
	I2C i2c(I2C_SDA, I2C_SCL);  //D14 and D15
	Ticker dT_input;
	volatile int read_input = 0;  

	// *** Serial communication: variables 
	Serial pc(SERIAL_TX, SERIAL_RX);
	Ticker dT_serial;
	volatile int update_serial = 0;  

	// *** Interrupt functions 
	void sensing() {
		read_input = 1;
	}

	void serial_com() {
		update_serial = 1;
	}

	// *** General functions 
	float read_temperature() {
		// Read temperature register
		char data_write[2];
		char data_read[2];
		data_write[0] = LM75_REG_TEMP;
		i2c.write(LM75_ADDR, data_write, 1, 1); // no stop
		i2c.read(LM75_ADDR, data_read, 2, 0);

		// Calculate temperature value in Celcius
		int16_t i16 = (data_read[0] << 8) | data_read[1];
		// Read data as twos complement integer so sign is correct
		float temperature = i16 / 256.0;
		// Return temperature
		return temperature;   
	}


	int main() {

		//*** temperature sensing configuration 
		//Sensor configuration
		char data_write[2];
		data_write[0] = LM75_REG_CONF;
		data_write[1] = 0x02;
		i2c.write(LM75_ADDR, data_write, 2, 0);
		//variables
		float temperature = 0;
		
		//*** PWM drive configuration
		 DIR = 1;    
		 peltierpwm.period_us(1000);
		 peltierpwm.write(0.1f); // NEVER GO ABOVE 0.5f!
		 printf("pwm set to %.2f %%\n", peltierpwm.read());

		//***  Interrupt configuration   
		dT_input.attach(sensing, 0.01);
		dT_serial.attach(serial_com, 0.25);
		
		while(1) {
			if (read_input == 1) {
				read_input = 0;
				temperature = read_temperature();             
			}
			if (update_serial == 1) {
				update_serial = 0;
				pc.printf("Pwm set to %.2f, Temperature = %.3f\r\n ",peltierpwm.read() * 100, temperature); 
			}
		}   
	}






The code in detail
------------------



The initial part of the code is about setting pins and defining variables.


   .. code-block:: c

	// *** PWM driver: pins and variables 
	PwmOut peltierpwm(PA_7);
	DigitalOut DIR(D5);

This is about settings for the PWM driver. Please check that your MAX14870 Driver is connected to the right microcontroller pins.


   .. code-block:: c

	// *** Temperature sensor: pins and variables 
	#define LM75_REG_TEMP (0x00) // Temperature Register
	#define LM75_REG_CONF (0x01) // Configuration Register
	#define LM75_ADDR     (0x90) // LM75 address
	I2C i2c(I2C_SDA, I2C_SCL);  //D14 and D15
	Ticker dT_input;
	volatile int read_input = 0;  

This code is about settings for the temperature sensors (please refer to Activity 2). The ticker variable ``dT_input`` is used to trigger an interrupt at constant intervals of time. You will see that, as a consequence of the interrupt, the variable ``read_input`` will flip from $0$ to $1$ to inform the main routine that a sensor read must be performed. This variable is declared as ``volatile`` to inform the compiler that this is a sensitive variable whose state may change at any moment (therefore the compiler will not apply any optimization that could cause a delay in detecting its status).


   .. code-block:: c

	// *** Serial communication: variables 
	Serial pc(SERIAL_TX, SERIAL_RX);
	Ticker dT_serial;
	volatile int update_serial = 0;  

This code is about setting for the serial comunication. Please note that the ticker variable ``dT_serial`` is used to trigger an interrupt at constant intervals of time, to request serial comunication. When the volatile variable ``update_serial`` is set to 1, the main routine is informed that a serial comunication must be done.


   .. code-block:: c

	void sensing() {
		read_input = 1;
	}

The function ``sensing()`` is called when the ticker ``dT_input`` triggers an interrupt. 
The function flips the ``read_input`` variable to 1, informing the main
code that a sensor reading must be done as soon as possible.


   .. code-block:: c

	void serial_com() {
		update_serial = 1;
	}

The function ``serial_com()`` is called when the ticker ``dT_serial`` triggers an interrupt. The function flips the variable ``update_serial`` to 1, informing the main
code that a serial comunicatoon must be done as soon as possible.


   .. code-block:: c

	float read_temperature() {
		// Read temperature register
		char data_write[2];
		char data_read[2];
		data_write[0] = LM75_REG_TEMP;
		i2c.write(LM75_ADDR, data_write, 1, 1); // no stop
		i2c.read(LM75_ADDR, data_read, 2, 0);

		// Calculate temperature value in Celcius
		int16_t i16 = (data_read[0] << 8) | data_read[1];
		// Read data as twos complement integer so sign is correct
		float temperature = i16 / 256.0;
		// Return temperature
		return temperature;   
	}

The function ``read_temperature()`` returns a temperature read from the sensor in Celcius. Please refer to Activity 2 for details.

We now go into the details of the main routine.


   .. code-block:: c

    //*** temperature sensing configuration 
    //Sensor configuration
    char data_write[2];
    data_write[0] = LM75_REG_CONF;
    data_write[1] = 0x02;
    i2c.write(LM75_ADDR, data_write, 2, 0);
    //variables
    float temperature = 0;

This code initialize the temperature sensor and define the float variable
``temperature`` which will contain the sensor last read.


   .. code-block:: c

	//*** PWM drive configuration
	DIR = 1;    
	peltierpwm.period_us(1000);
	peltierpwm.write(0.1f); // NEVER GO ABOVE 0.5f!
	printf("pwm set to %.2f %%\n", peltierpwm.read());


This code set the Peltier PWM duty cycle at 10%. You are encouraged to
try different duty cycles but please never go above 50% to avoid
termal issues with the cell (the cell may break).


   .. code-block:: c

	//*** Interrupt configuration   
	dT_input.attach(sensing, 0.01);
	dT_serial.attach(serial_com, 0.25);


This code set the interval of the recurring interrupts. The first line sets a recurring interrupt every 0.01 seconds, which calls repeadetly the function ``sensing()`` to request a sensor reading. The second line sets a recurring interrupt every 0.25 seconds, which calls the function ``serial_com()`` to request serial comunication.

You will notice that serial comunication happens at much slower rate than sensor reading. The reason for these differences will be clear later, when we will design a more complex actuation mechanism. The idea is that sensing and comunication with the user can occur at different rates. Typically, sensing and actuation need a very fast rate to avoid issues but comunication with the user (serial) can be done at a slower rate to save computational resources.

Finally, the while loop constantly monitors the two variables
``read_input`` and ``update_serial``. A sensor read is performed when ``read_input`` is detected equal to 1. Consequently, ``read_input`` is set to $0$, in preparation for the next interrupt. Temperature and PWM status are comunicated to the user when ``update_serial`` is detected equal to 1. After that, ``update_serial`` is set to 0, in preparation for the next interrupt.


   .. code-block:: c

	while(1) {x
		 if (read_input == 1) {
			read_input = 0;
			temperature = read_temperature();             
		 }
		 if (update_serial == 1) {
			update_serial = 0;
			pc.printf("Pwm set to %.2f, Temperature = %.3f\r\n ",peltierpwm.read() * 100, temperature); 
		 }
	}   





Tasks
-----



- Why does the cold side become colder as the duty cycle increase?
- Can you set the temperature of the cold side to a desired value by a suitable selection of the duty cycle?


