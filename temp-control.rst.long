Temperature controller
======================



Feedback control
----------------



With a constant duty cycle, the Peltier cell moves heat from the cold side to the hot side. Heat is then slowly removed from the hot side by the heat-sink, and dissipated through `convection <https://en.wikipedia.org/wiki/Convection>`_. For this reason, the cold side temperature of the Peltier cell becomes colder than the room average temperature. Increasing the duty cycle further reduces the cold side temperature. However, you do not have much control on the actual temperature of the cold side. That depends on a number of factors. Even if you spend time to estimate the relationship between duty cycle and cold side temperature (at steady state), that relationship will dependent on the room average temperature, airflow within the heat sink, humidity, and many other factors.  

To overcome these limitations and control precisely the cold side temperature you need feedback. Feedback is effective against uncertainties. The idea is simple: if the cold side temperature is above the desidred temperature, then the PWM duty cycle must be increased to remove more heat. If the cold side temperature is below the desired temperature, then the PWM duty cycle must be set to zero to reduce the extraction of heat. This basic `feedback <https://en.wikipedia.org/wiki/Control_theory>`_ approach, summarized in the figure below, is extensively used in industry. 

.. figure:: https://upload.wikimedia.org/wikipedia/commons/2/24/Feedback_loop_with_descriptions.svg
   :scale: 50 %
   :alt: Feedback loop

   Feedback loop. Source: Wikipedia




In our setting, the "system" is the Peltier cell and the "sensor" is the temperature sensor. In principle, the microcontroller can computes the difference between the cold side measured temperature and the desired/reference temperature, and proportionally adjusts the duty cycle to reach the desired temperature. 




Feedback control algorithm
--------------------------




The code below implements a feedback control algorithm. Sensor reading and serial comunication are as in the previous code. A new Ticker variable is introduce to trigger updates of the PWM duty cycle at constant interval of times. The function ``update_PWM()`` adapts the PWM duty cycle accordingly to the difference between current and desired cold side temperatures. The function implement the simplest form of feedback control: proportional feedback.


   .. code-block:: c

	#include "mbed.h"
	#include "stdint.h"

	// *** PWM driver: pins and variables 
	PwmOut peltierpwm(PA_7);
	DigitalOut DIR(D5);
	Ticker dT_output;
	volatile int write_output = 0;  

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

	void actuation() {
		write_output = 1;
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

	void update_PWM(float temperature) {
		// Read temperature register
		float ref = 30;
		float kp = 0.3;
		float duty_cycle;
		
		duty_cycle = kp*(ref-temperature);
		if (duty_cycle <= 0) {
			peltierpwm.write(0.0f);        
		}
		if (duty_cycle >= 0.50) {
				peltierpwm.write(0.50f);   
		}
		if (duty_cycle >= 0 && duty_cycle <= 0.50) {
				peltierpwm.write(duty_cycle);       
		}   
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
		 peltierpwm.write(0.0f); 
		 printf("pwm set to %.2f %%\n", peltierpwm.read());

		//***  Interrupt configuration   
		dT_input.attach(sensing, 0.01);
		dT_output.attach(actuation, 0.01);
		dT_serial.attach(serial_com, 0.25);
		
		while(1) {
			if (read_input == 1) {
				read_input = 0;
				temperature = read_temperature();             
			}
			if (write_output == 1) {
				write_output = 0;
				update_PWM(temperature); 
			}        
			if (update_serial == 1) {
				update_serial = 0;
				pc.printf("Pwm set to %.2f, Temperature = %.3f\r\n ",peltierpwm.read() * 100, temperature, ref); 
			}
		}   
	}






Feedback control algorithm in detail
------------------------------------




Let's discuss only the new elements.


   .. code-block:: c

	// *** PWM driver: pins and variables 
	PwmOut peltierpwm(PA_7);
	DigitalOut DIR(D5);
	Ticker dT_output;
	volatile int write_output = 0;  


In this code, the ticker variable ``dT_output`` is used to trigger an interrupt at constant intervals of time. You will see that, as a consequence of the interrupt, the variable ``write_output`` is set to 1. This will trigger an update of the duty cycle driving the Peltier cell. 


   .. code-block:: c

	void actuation() {
		write_output = 1;
	}

The function ``actuation()`` is called when the ``dT_output`` ticker triggers an interrupt. The function changes the variable ``write_output`` to $1$.


   .. code-block:: c

	void update_PWM(float temperature) {
		// Read temperature register
		float ref = 30;
		float kp = 0.3;
		float duty_cycle;
		
		duty_cycle = kp*(ref-temperature);
		if (duty_cycle <= 0) {
			peltierpwm.write(0.0f);        
		}
		if (duty_cycle >= 0.50) {
				peltierpwm.write(0.50f);   
		}
		if (duty_cycle >= 0 && duty_cycle <= 0.50) {
				peltierpwm.write(duty_cycle);       
		}   
	}


The function ``update_PWM()`` adapts the duty cycle driving the Peltier cell:

- ``ref`` is the desired temperature. This is set by the user.

- ``duty_cycle = kp*(ref-temperature)`` adjusts the duty cycle proportionally to the difference between the reference temperature ``ref`` and the actual measured temperature ``temperature``. The proportional gain ``kp`` can be adjusted by the user.

The rest of the code normalizes the duty_cycle within safety bounds, for compatibility with the physical limits of Peltier cell and MAX14870 driver: 

- if ``duty_cycle <= 0``, the measured temperature is already below the reference temperature and the best action is to turn off the Peltier cell by setting ``peltierpwm.write(0.0f)``;

- if ``duty_cycle >= 0.50`` then the actual duty cycle is normalized to the maximum value ``peltierpwm.write(0.50f)`` to avoid large currents within the Peltier cell.


The initial part of the main code is used for initialization of the controller. For instance, 


   .. code-block:: c

	//*** PWM drive configuration
	EN = 1;    
	peltierpwm.period_us(1000);
	peltierpwm.write(0.0f); 
	printf("pwm set to %.2f %%\n", peltierpwm.read());

sets the initial PWM duty cycle at 0. Also


   .. code-block:: c
	dT_output.attach(actuation, 0.01);


defines a recurrent interrupt every 0.01 seconds, which call the function ``actuation()``. 

Finally, within the main while loop, the code


   .. code-block:: c

	if (write_output == 1) {
		write_output = 0;
		update_PWM(temperature); 
	}  

triggers an update of the PWM duty cycle whenever the variable ``write_output`` is detected equal to 1. After that, ``write_output`` is set to 0, in preparation for the next interrupt.

Actuation and sensing are updated every 0.01 seconds, a very fast rate. Serial updates to the user are just four each second, a much slower rate (enough for monitor reading).




Task
----



- what is the difference between small and large proportional gain kp?

- Set the reference temperature through buttons. 

- The `proportional control <https://en.wikipedia.org/wiki/Proportional_control>`_ you have implemented in the code above is non optimal for temperature control: it works but it is not precise. There is always a small error near the desired temperature. The best approach is the so called `proportional + integral control <https://en.wikipedia.org/wiki/PID_controller>`_. The role of integral control is to estimate the exact amount of energy that the system needs at steady state to keep the temperature at the exact desired value (no small error). If you have mastered this lecture so far and you want to explore a more challanging control algorithm, please read the linked wikipedia page and add an integral component to your proportional controller. A few additional resources can be found here:

`PID Cookbook Mbed <https://os.mbed.com/cookbook/PID>`_

`What is a PID Controller? <https://www.youtube.com/watch?v=sFqFrmMJ-sg>`_

`What are PID Tuning Parameters? <https://www.youtube.com/watch?v=1ImhKwpSmuc>`_


