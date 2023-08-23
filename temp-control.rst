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






Feedback control algorithms
---------------------------


In our setting, the "system" is the Peltier cell and the "sensor" is the temperature sensor. In principle, the microcontroller can computes the difference between the cold side measured temperature and the desired/reference temperature, and proportionally adjusts the duty cycle to reach the desired temperature. The coefficient of proportionality between the duty cycle of the Peltier cell and the error in temperature is called the gain, and plays a role in the effectiveness of the control system. It would be a good idea to make is a parameter that you can adjust easily in your code.


In the proportional controller, the duty cycle would be = kp*(ref-temperature)`` adjusts the duty cycle proportionally to the difference between the reference temperature ``ref`` and the actual measured temperature ``temperature``. The proportional gain ``kp`` can be adjusted by the user.

At this point, you should be ready to implement your overall solution for temperature control. You may want to consider what the difference is between small and large proportional gain kp.

The `proportional control <https://en.wikipedia.org/wiki/Proportional_control>`_ suggested above is non optimal for temperature control: it works but it is not precise. There is always a small error near the desired temperature. The best approach is the so called `proportional + integral control <https://en.wikipedia.org/wiki/PID_controller>`_. The role of integral control is to estimate the exact amount of energy that the system needs at steady state to keep the temperature at the exact desired value (no small error). If you have mastered this lecture so far and you want to explore a more challanging control algorithm, please read the linked wikipedia page and add an integral component to your proportional controller. A few additional resources can be found here:

`PID Cookbook Mbed <https://os.mbed.com/cookbook/PID>`_

`What is a PID Controller? <https://www.youtube.com/watch?v=sFqFrmMJ-sg>`_

`What are PID Tuning Parameters? <https://www.youtube.com/watch?v=1ImhKwpSmuc>`_


