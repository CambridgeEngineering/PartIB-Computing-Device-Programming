Activity 3: actuation and feedback
==================================

Learning objectives
-------------------



In this third activity you will learn about actuation, which transforms the logical/mathematical capabilities of your micro-controller into actions that have effect on the real world. The activity relies on the skills that you have learned in the first and second activities - you will have to use interrupts, read sensors, and solder again!

You will implement a temperature controller. The controller will acquire data from the temperature sensor and will drive a Peltier cell to achieve the desired temperature.  





Task to complete
----------------




- Adjust the brightness of a LED and of a halogen bulb.

- Use a Peltier cell to transfer a constant amount of heat.
 
- Temperature regulation: use temperature sensor and Peltier cell to regulate the temperature of a given test-surface to a desired temperature.




What you may need to learn
--------------------------



- Learn about Pulse-Width-Modulation and how to use it to adjust the brightness of a LED.

- Learn what an H-Bridge is and how to use it to adjust the brightness of a halogen bulb.

- Learn what a Peltier cell is and how to drive it to transfer heat.

- Learn how to implement a feedback control loop: read sensor, compute the right control action, drive the actuator, and back.

These activities are progressive, and each relies on the former one. Please take them in the right order. 






.. toctree::
   :maxdepth: 2
   :caption: Contents:
   
   i2c-intro
   soldering
   i2c-testing
   i2c-com-lm75
   i2c-interrupt-lm75












## Pulse-Width-Modulation and LED brightness

### What is Pulse-Width-Modulation (PWM)?

Pulse-width-modulation is probably the most common way to produce an average analog voltage in electrical circuits via fast switching. 
The idea is simple: if you switch the voltage of a circuit between ON ($V_{ref}$ volts) and OFF ($0$ volts) sufficiently fast, you will generate an average voltage in the circuit proportional to the ratio between the length of time the circuit was ON and the length of time the circuit was OFF. 

Let's make a few examples. Take a PWM frequency of $10$kH; this means that the ON/OFF cycle repeats $10000$ times a second).

- A 50% duty cycle means that the circuit is ON for half of the cycle and OFF otherwise (first row in the image below, from [Wikipedia](https://en.wikipedia.org/wiki/Pulse-width_modulation)). Thus, the generated averaged voltage is about $0.5V_{ref}$.

- A 25% duty cycle means that the circuit is ON for a fourth of each cycle and and OFF otherwise (last row in the image below). The generated voltage is about $0.25 V_{ref}$.

![Wikipedia PWM](https://upload.wikimedia.org/wikipedia/commons/b/b8/Duty_Cycle_Examples.png)

This technique is so popular that you can find a full [Wikipedia](https://en.wikipedia.org/wiki/Pulse-width_modulation) page dedicated to it and several tutorials available on internet like [Arduino](https://www.arduino.cc/en/tutorial/PWM) and [Sparkfun](https://learn.sparkfun.com/tutorials/pulse-width-modulation/all). You may find this video useful:

*Embed this:* <https://www.youtube.com/watch?v=GQLED3gmONg>


### The PWM library

The [MbedOS library](https://os.mbed.com/docs/mbed-os/v5.13/apis/pwmout.html) makes easy the use PWM on the microcontroller. 

First, you need to declare your PWM output

```
PwmOut pwmled(LED2);
```
The instruction above sets LED2 as pwm output. Any other digital output compatible with PWM would work. Check your microcontroller manual to know which pins are compatible with PWM.

Then, you need to set the PWM period

```
pwmled.period_us(1000);
```
The instruction above sets a ON/OFF cycle every $1000$ microseconds, that is, $1000$ cycles each second.

Finally, the duty cycle is defined by

```
pwmled.write(0.1f); 
```
The argument of the write function must be a float between $0$ and $1$. The instruction above sets the duty cycle at $10$%. 

You can also read the current PWM duty cycle through the instruction

```
pwmled.read(); 
```
which returns a floating-point value. 
For more details, please refer to the [PwmOut API](https://os.mbed.com/docs/mbed-os/v5.13/apis/pwmout.html#pwmout-class-reference).


### LED brightness through PWM.

By now the following code should be quite readable.

```
#include "mbed.h"

PwmOut pwmled(LED2);

int main() {
    
	pwmled.period_us(1000);
	pwmled.write(0.1f); 
	printf("pwm set to %.2f %%\n", pwmled.read());    
}
```

The code switches ON and OFF the LED $10000$ times a second. Within each cycle the LED is ON only for 10% of the time. Your eyes cannot see such fast frequencies and you will perceive the overal switching pattern as low brightness.

Try different duty cycles to adjust the brightness of the LED. Do you see a linear relation between duty cycle and brightness?


### Tasks

- Modify the code to change brightness levels by pressing the button.

- Modify the code to make brightness slowly pulsating from low brightness to high brightness and back.













## H-bridge and power

###What is an H-bridge?

Microcontrollers are logical devices that function with very modest power. They are not very good at driving loads, like actuators, which typically require power at their inputs. The problem can be solved through an interface capable of injecting power to the actuator as a function of the logical signals of the microcontroller. This is the role of a H-bridge. 

An H-bridge is a simple device. It is just a switching circuit connected to a large power generator, as shown in the figure below (from [Wikipedia](https://en.wikipedia.org/wiki/H_bridge)). The four switches S1-S4 route power to the load, represented in the figure by an encircled M (a motor, in this example). 

![H Bridge Wikipedia](https://upload.wikimedia.org/wikipedia/commons/d/d4/H_bridge.svg)

The switches are typically controlled by the microcontroller.

- If S1 and S4 are closed, and S2 and S3 are open, a positive voltage is applied to the left side of the load (the motor spins in one direction).
- If S2 and S3 are closed, and S1 and S4 are open, a positive voltage is applied to the right side of the load (the motor spins in the other direction).
- If S1 and S3 are closed, and S2 and S4 are open, the same voltage is applied to both sides of the load (the motor does not want to move). The same happens if S2 and S4 are closed, and S1 and S3 are open.
- If either S1 and S2 are closed, or S3 and S4 are closed, the generator is short circuited and terrible things will happen...

So, by using a H-bridge, you can apply a voltage to your load without strong restrictions on the amount of power. Selecting the configuration of the four switches S1-S4 (ON/OFF) you can also decide the direction of the driving voltage to the load. Finally, to modulate the intensity of the voltage, just use PWM, opening and closing S1-S4 at high frequency to generate a suitable average voltage. 

###MAX14870 Driver 

The MAX14870 Driver is a H-bridge that can be used with voltages between 4.5 V to 36 V and can supply up to about 1.7 A continuously, and  2.5 A peak. Documentation and a several additional resources can be found on [Pololu website](https://www.pololu.com/product/2961).

The image below, from the website, show control connections and power connections on different sides of the board. The driver is shipped with 
two 1x5 pin breakaway that you will have to solder.

![Pololu image](https://a.pololu-files.com/picture/0J6505.1200.jpg)

- Connect the 9-volts power supply to the pins **VIN** and **GND**, paying attention to NOT reverse polarity (see figure).
- The load will be connected to the pins **M1** and **M2**.
- Connect the two **GND** pins and the **EN** pin, to a common ground.
- The MAX14870 resolves internally the configuration of the switches S1-S4 of previous section. You will need just two signals to control your load: a *direction signal* for the **DIR** pin (ON / 3.3V =  M1 > M2; OFF / 0V = M1 < M2), and a *PWM signal* for the **PWM** pin (ON = voltage supplied, OFF = load is at ground). 

Typically, the microcontroller sets the **DIR** pin low or high, depending on the direction of the desired voltage supplied to the load. The microcontroller also sends a  PWM signal to the **PWM** pin with a given duty cycle. As a consequence, the voltage between **M1**/**M2** pins will be an average value between 0V and 9V proportional to the PWM duty cycle. Overall, the MAX14870 Driver essentially operates as an amplifier.

###Regulation of the brightness of a halogen bulb 

Connect **PWM** and **DIR** pins of MAX14870 to the microcontroller pins **PA_7/D11** and **D5**, respectively. Connect the two pins of the halogen bulb to pins **M1** and **M2**.

The following code regulates the brightness of the halogen bulb. 
Edit the duty cycle to change the brightness. 

```
#include "mbed.h"

PwmOut pwmbulb(PA_7);
DigitalOut DIR(D5);

int main() {

	DIR = 1;    
	pwmbulb.period_us(1000);
	pwmbulb.write(0.1f); //NEVER go above 0.5f.
	printf("pwm set to %.2f %%\n", pwmbulb.read());
	
	wait(2);
	pwmbulb.write(0.0f);    
}
```
The code is very similar to the LED one. In fact, we have just added an amplifier to drive more consistent loads. The last two lines of the code turn off the driver after two seconds, to avoid termal issues (the bulb drains a lot of current, which makes both bulb and driver becoming hot very fast). 



















## Peltier cells


### Preparation of a cell

[Peltier cells](https://en.wikipedia.org/wiki/Thermoelectric_cooling) use the [Peltier effect](https://en.wikipedia.org/wiki/Thermoelectric_effect#Peltier_effect) to pump heat from one plate to another of the device. The flux of heat is roughly proportional to the current passing through the peltier cell. The image below, from [Wikipedia](https://en.wikipedia.org/wiki/Thermoelectric_cooling), provides a schematic representation of a Peltier cell.

![Peltier cell, Wikipedia](https://upload.wikimedia.org/wikipedia/commons/a/a2/Peltierelement.png)

- One of the two plates of the Peltier cell is engineered to be hot. The other to be cold. Please attach the thermal adesive on both sides, then attach the heat sink to the cold side. To identify the hot side please refer to the [datasheet](./peltier_datasheet.pdf). For the particular model provided, place the cell on the bench/desk with the black cable on the right/down and red cable on the left/down, then the top plate is the hot plate. 

- Connect the Peltier cell to the MAX14870 Driver. The red cable to the **M1** pin and the black cable to the **M2** pin (but first be sure that your duty cycle is at zero!)

- Place the Peltier cell on your desk/bench with the heat sink in contact with the bench surface and the hot side exposed to the air. Then place the temperature sensor on the hot side (fix it with standard tape).

![Peltier bench realization](./peltier_bench.jpg)


### Constant heat transfer and sensor readings

The following code drives the Peltier cell with a small voltage and monitors the temperature of the cold side of the Peltier cell by cyclically reading the temperature sensor. Temperature sensor reading is made available to the user through serial.

The code to drive the Peltier cell is similar to the one for LED and bulb. 
Setting and reading of the temperature sensor is realized through minor adaptations of the code you have developed in Activity 2. In fact, you will need to connect the temperature sensor to the pins **D14** and **D15** as in Activity 2.

The code uses the [Ticker](https://os.mbed.com/docs/mbed-os/v5.13/apis/ticker.html) interface to set up recurring interrupts. Recurrent interrupts allow to read sensors and send information on the serial port at precise time intervals. 


```
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
```

###The code in detail

The initial part of the code is about setting pins and defining variables.

```
// *** PWM driver: pins and variables 
PwmOut peltierpwm(PA_7);
DigitalOut DIR(D5);
```
This is about settings for the PWM driver. Please check that your MAX14870 Driver is connected to the right microcontroller pins.

```
// *** Temperature sensor: pins and variables 
#define LM75_REG_TEMP (0x00) // Temperature Register
#define LM75_REG_CONF (0x01) // Configuration Register
#define LM75_ADDR     (0x90) // LM75 address
I2C i2c(I2C_SDA, I2C_SCL);  //D14 and D15
Ticker dT_input;
volatile int read_input = 0;  
```
This code is about settings for the temperature sensors (please refer to Activity 2). The ticker variable ```dT_input``` is used to trigger an interrupt at constant intervals of time. You will see that, as a consequence of the interrupt, the variable ```read_input``` will flip from $0$ to $1$ to inform the main routine that a sensor read must be performed. This variable is declared as ```volatile``` to inform the compiler that this is a sensitive variable whose state may change at any moment (therefore the compiler will not apply any optimization that could cause a delay in detecting its status).

```
// *** Serial communication: variables 
Serial pc(SERIAL_TX, SERIAL_RX);
Ticker dT_serial;
volatile int update_serial = 0;  
```
This code is about setting for the serial comunication. Please note that the ticker variable ```dT_serial``` is used to trigger an interrupt at constant intervals of time, to request serial comunication. When the volatile variable ```update_serial``` is set to $1$, the main routine is informed that a serial comunication must be done.

```
void sensing() {
    read_input = 1;
}
```
The function ```sensing()``` is called when the ticker ```dT_input``` triggers an interrupt. 
The function flips the ```read_input``` variable to $1$, informing the main
code that a sensor reading must be done as soon as possible.

```
void serial_com() {
    update_serial = 1;
}
```
The function ```serial_com()``` is called when the ticker ```dT_serial``` triggers an interrupt. The function flips the variable ```update_serial``` to $1$, informing the main
code that a serial comunicatoon must be done as soon as possible.

```
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
```
The function ```read_temperature()``` returns a temperature read from the sensor in Celcius. Please refer to Activity 2 for details.

We now go into the details of the main routine.

```    
    //*** temperature sensing configuration 
    //Sensor configuration
    char data_write[2];
    data_write[0] = LM75_REG_CONF;
    data_write[1] = 0x02;
    i2c.write(LM75_ADDR, data_write, 2, 0);
    //variables
    float temperature = 0;
```  
This code initialize the temperature sensor and define the float variable
```temperature``` which will contain the sensor last read.

```   
//*** PWM drive configuration
DIR = 1;    
peltierpwm.period_us(1000);
peltierpwm.write(0.1f); // NEVER GO ABOVE 0.5f!
printf("pwm set to %.2f %%\n", peltierpwm.read());
```
This code set the Peltier PWM duty cycle at $10$%. You are encouraged to
try different duty cycles but please never go above $50$% to avoid
termal issues with the cell (the cell may break).

```
//*** Interrupt configuration   
dT_input.attach(sensing, 0.01);
dT_serial.attach(serial_com, 0.25);
```
This code set the interval of the recurring interrupts. The first line sets a recurring interrupt every $0.01$ seconds, which calls repeadetly the function ```sensing()``` to request a sensor reading. The second line sets a recurring interrupt every $0.25$ seconds, which calls the function ```serial_com()``` to request serial comunication.

You will notice that serial comunication happens at much slower rate than sensor reading. The reason for these differences will be clear later, when we will design a more complex actuation mechanism. The idea is that sensing and comunication with the user can occur at different rates. Typically, sensing and actuation need a very fast rate to avoid issues but comunication with the user (serial) can be done at a slower rate to save computational resources.

Finally, the while loop constantly monitors the two variables
```read_input``` and ```update_serial```. A sensor read is performed when ```read_input``` is detected equal to $1$. Consequently, ```read_input``` is set to $0$, in preparation for the next interrupt. Temperature and PWM status are comunicated to the user when ```update_serial``` is detected equal to $1$. After that, ```update_serial``` is set to $0$, in preparation for the next interrupt.

```    
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
```

### Tasks

- Why does the cold side become colder as the duty cycle increase?
- Can you set the temperature of the cold side to a desired value by a suitable selection of the duty cycle?





















## Temperature controller

###Feedback control

With a constant duty cycle, the Peltier cell moves heat from the cold side to the hot side. Heat is then slowly removed from the hot side by the heat-sink, and dissipated through [convection](https://en.wikipedia.org/wiki/Convection). For this reason, the cold side temperature of the Peltier cell becomes colder than the room average temperature. Increasing the duty cycle further reduces the cold side temperature. However, you do not have much control on the actual temperature of the cold side. That depends on a number of factors. Even if you spend time to estimate the relationship between duty cycle and cold side temperature (at steady state), that relationship will dependent on the room average temperature, airflow within the heat sink, humidity, and many other factors.  

To overcome these limitations and control precisely the cold side temperature you need feedback. Feedback is effective against uncertainties. The idea is simple: if the cold side temperature is above the desidred temperature, then the PWM duty cycle must be increased to remove more heat. If the cold side temperature is below the desired temperature, then the PWM duty cycle must be set to zero to reduce the extraction of heat. This basic [feedback](https://en.wikipedia.org/wiki/Control_theory) approach, summarized in the figure below (from [Wikipedia](https://en.wikipedia.org/wiki/Control_theory)), is extensively used in industry. 

![Feedback](https://upload.wikimedia.org/wikipedia/commons/2/24/Feedback_loop_with_descriptions.svg)

In our setting, the "system" is the Peltier cell and the "sensor" is the temperature sensor. In principle, the microcontroller can computes the difference between the cold side measured temperature and the desired/reference temperature, and proportionally adjusts the duty cycle to reach the desired temperature. 

###Feedback control algorithm

The code below implements a feedback control algorithm. Sensor reading and serial comunication are as in the previous code. A new Ticker variable is introduce to trigger updates of the PWM duty cycle at constant interval of times. The function ```update_PWM()``` adapts the PWM duty cycle accordingly to the difference between current and desired cold side temperatures. The function implement the simplest form of feedback control: proportional feedback.

```
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
```

###Feedback control algorithm in detail

Let's discuss only the new elements.

```
// *** PWM driver: pins and variables 
PwmOut peltierpwm(PA_7);
DigitalOut DIR(D5);
Ticker dT_output;
volatile int write_output = 0;  
```

In this code, the ticker variable ```dT_output``` is used to trigger an interrupt at constant intervals of time. You will see that, as a consequence of the interrupt, the variable ```write_output``` is set to 1. This will trigger an update of the duty cycle driving the Peltier cell. 

```
void actuation() {
    write_output = 1;
}
```
The function ```actuation()``` is called when the ```dT_output``` ticker triggers an interrupt. The function changes the variable ```write_output``` to $1$.

```
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
```
The function ```update_PWM()``` adapts the duty cycle driving the Peltier cell:

- ```ref``` is the desired temperature. This is set by the user.

- ```duty_cycle = kp*(ref-temperature)``` adjusts the duty cycle proportionally to the difference between the reference temperature ```ref``` and the actual measured temperature ```temperature```. The proportional gain ```kp``` can be adjusted by the user.

The rest of the code normalizes the duty_cycle within safety bounds, for compatibility with the physical limits of Peltier cell and MAX14870 driver: 

- if ```duty_cycle <= 0```, the measured temperature is already below the reference temperature and the best action is to turn off the Peltier cell by setting ```peltierpwm.write(0.0f)```;

- if ```duty_cycle >= 0.50``` then the actual duty cycle is normalized to the maximum value ```peltierpwm.write(0.50f)``` to avoid large currents within the Peltier cell.


The initial part of the main code is used for initialization of the controller. For instance, 

```
//*** PWM drive configuration
EN = 1;    
peltierpwm.period_us(1000);
peltierpwm.write(0.0f); 
printf("pwm set to %.2f %%\n", peltierpwm.read());
```
sets the initial PWM duty cycle at 0. Also

```
dT_output.attach(actuation, 0.01);
```
defines a recurrent interrupt every 0.01 seconds, which call the function ```actuation()```. 

Finally, within the main while loop, the code

```
if (write_output == 1) {
	write_output = 0;
	update_PWM(temperature); 
}  
```
triggers an update of the PWM duty cycle whenever the variable ```write_output``` is detected equal to $1$. After that, ```write_output``` is set to $0$, in preparation for the next interrupt.

Actuation and sensing are updated every 0.01 seconds, a very fast rate. Serial updates to the user are just four each second, a much slower rate (enoigj for monitor reading).

###Task

- what is the difference between small and large proportional gain kp?

- Set the reference temperature through buttons. 

- The [proportional control](https://en.wikipedia.org/wiki/Proportional_control) you have implemented in the code above is non optimal for temperature control: it works but it is not precise. There is always a small error near the desired temperature. The best approach is the so called [proportional + integral control](https://en.wikipedia.org/wiki/PID_controller). The role of integral control is to estimate the exact amount of energy that the system needs at steady state to keep the temperature at the exact desired value (no small error). If you have mastered this lecture so far and you want to explore a more challanging control algorithm, please read the linked wikipedia page and add an integral component to your proportional controller. A few additional resources can be found here: [PID Cookbook Mbed](https://os.mbed.com/cookbook/PID),
[What is a PID Controller?](https://www.youtube.com/watch?v=sFqFrmMJ-sg),
[What are PID Tuning Parameters?](https://www.youtube.com/watch?v=1ImhKwpSmuc).



