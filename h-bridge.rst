H-bridge and power
==================



What is a H-bridge?
--------------------


Microcontrollers are logical devices that function with very modest power. They are not very good at driving loads, such as actuators, which typically require more power at their inputs. The problem can be solved through an interface capable of injecting power to the actuator as a function of the logical signals of the microcontroller. This is the role of a H-bridge. 

A H-bridge is a simple device. It is just a switching circuit connected to a large power generator, as shown in the figure below. The circuit is called an H-bridge because its schematic resembles the letter 'H'. The four switches S1-S4 route power to the load, represented in the figure by an encircled M (a motor, in this example). 


.. figure:: https://upload.wikimedia.org/wikipedia/commons/d/d4/H_bridge.svg
   :scale: 50 %
   :alt: H bridge

   H bridge. Source: Wikipedia



The switches are typically controlled by the microcontroller.

- If S1 and S4 are closed, and S2 and S3 are open, a positive voltage is applied to the left side of the load (the motor spins in one direction).
- If S2 and S3 are closed, and S1 and S4 are open, a positive voltage is applied to the right side of the load (the motor spins in the other direction).
- If S1 and S3 are closed, and S2 and S4 are open, the same voltage is applied to both sides of the load (the motor does not want to move). The same happens if S2 and S4 are closed, and S1 and S3 are open.
- If either S1 and S2 are closed, or S3 and S4 are closed, the generator is short circuited and terrible things will happen...

So, by using a H-bridge, you can apply a voltage to your load without strong restrictions on the amount of power. Selecting the configuration of the four switches S1-S4 (ON/OFF) you can also decide the direction of the driving voltage to the load. Finally, to modulate the intensity of the voltage, just use PWM, opening and closing S1-S4 at high frequency to generate a suitable average voltage. 



L298N Driver
---------------


The L298N driver is a dual H-bridge that can be used with voltages between 3.2 V to 40 V and can supply up to 2 A per bridge. To know more about this driver, consult the relevant data-sheet from the manufacturer of the :download:`driver integrated circuit <docs/L298N.pdf>` and the manufacturer of the :download:`breakout board <docs/L298N-Motor-Driver-Datasheet.pdf>`. 

The image below, from the breakout board datasheet, shows the different inputs and outputs of the circuit. 

.. figure:: images/L298N-Module-Pinout.jpg
   :scale: 80 %
   :alt: L298N

   L298N H bridge connections. 

- Connect an external DC power supply to the ground and +12V Power pins terminal. The power voltage doesn't need to be 12 V. Any value from 3.2 V to 40 V would work. This power must not be taken from the microcontroller itself. Pay attention to NOT reverse polarity. Always double-check your connections before powering on the circuit to avoid damaging your components.


- Here, we will use only one of the two H-bridges. The load will be connected to the Output A terminals. 

- The pins IN1 and IN2 control the switches of the bridge and should be connected to digital outputs of your microcontroller. They would work with both 3.3 V and 5 V logic.
    - if IN1 and IN2 are at the same voltage (0 or 3.3 V), then there is no current through the load;
    - if IN1 = 1 and IN2 = 0, the current will flow in a certain direction through the load; 
    - if IN1 = 0 and IN2 = 1, the current will flow in the opposite direction through the load;

- The pin "A Enable" could be either set to 1 permanently using the jumper, or connected to a PWM input to modulate the load. In the example below, we will use PWM so the jumper needs to be removed and safely stored.

- the +5V Power pin is not required here. It may be used to actually power the microcontroller, but we use here the USB connection to the computer to deliver power.

- However, the microcontroller and the L298N H-bridge need to have the same ground. It is therefore important to connect the Power GND terminal of the H-bridge to a GND pin of the microcontroller.




Regulation of the speed and rotation direction of a DC motor
------------------------------------------------------------

- Connect a small DC motor to the "Output A" terminals.

- Configure the power supply to deliver 5 V and up to 1 A. Connect the (-) voltage output of the power supply to the ground of the H-bridge, and the (+) voltage to the "+12V Power" terminal of the bridge.

- Connect the GND of the microcontroller to the "Power GND" terminal of the bridge. (You can insert two cables if the terminal if needed.)

- Connect IN1 and IN2 to two digital outputs of the microcontroller. The code below uses the pins D8 and D9.

- Connect "A enable" to a PWM enabled pin. The code below uses the pin D11.


   .. code-block:: c

    #include "mbed.h"


    // Pins used for the H-bridge control
    PwmOut pwmload(D11);
    DigitalOut in_A(D8);
    DigitalOut in_B(D9);

    // We use the LEDs to communicate the state of the H-bridge
    // LED1 (green) when not enabled
    // LED2 when in the positive direction
    // LED3 when in the negative direction
    DigitalOut ledgreen(LED1);
    PwmOut pwmblue(LED2);
    PwmOut pwmred(LED3);


    void setload(float x)
    {
      if (x>0)
        { in_A = 1;
        in_B = 0;
        pwmload.write(x);
        pwmred.write(x);
        pwmblue.write(0.0);
        ledgreen = 0;
        }
      else if (x<0)
        { in_A = 0;
        in_B = 1;
        pwmload.write(-x);
        pwmred.write(0.0);
        pwmblue.write(-x);
        ledgreen = 0;
        }
      else
        { in_A = 0;
        in_B = 0;
        pwmload.write(0.0);
        pwmred.write(0.0);
        pwmblue.write(0.0);
        ledgreen = 1;
        }
    }



    int main() 
    {
      float load = 0.0;
      for (load = 0; load <=1; load += 0.025)
      {
        setload(load);
        wait(0.1);
      }
      while(true)
      {
        for (load = 1; load >=-1; load -= 0.025)
        {
          setload(load);
          wait(0.1);
        }
        for (load = -1; load <=1; load += 0.025)
        {
          setload(load);
          wait(0.1);
        }
      }

    }


.. admonition:: Task

   **Connect properly the bridge to your microcontroller and motor, and test the code above. Modify the code so that the button can be used to alternate between different speeds and directions of the motor.**




