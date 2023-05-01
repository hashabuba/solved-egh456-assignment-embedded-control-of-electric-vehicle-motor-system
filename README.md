Download Link: https://assignmentchef.com/product/solved-egh456-assignment-embedded-control-of-electric-vehicle-motor-system
<br>
Battery-Electric and Hybrid-Electric vehicles are becoming more and more economical, efficient and environmentally friendly resulting in an increase in adoption rates. In addition, the recent advancements in autonomous electric vehicles are also resulting in the automotive industry starting to transition from petrol to electric vehicle design. As an embedded system engineer you have been contracted by an automotive manufacture who is transitioning from petrol to Battery-Electric technology. Your task is to design the real time controller and hardware interface of the electric motor for the vehicle.

Your challenge is to handle the complete sensing and actuation of a 3-phase Brushless DC (BLDC) motor common in electric vehicles. This will include monitoring the state of the motor such as velocity, power and temperature and handle the start-up, braking and emergency procedures for the system. Your system design will require real-time handling of multiple tasks for sensor acquisition and filtering, motor fault handling and system display of critical information. Your setup includes a motor testing station including the motor driver and sensor board and a development kit for the Tiva TM4C1294NCPDT microcontroller. Figure 1b illustrates the setup interface of the testing station. Inputs and outputs of sensors and per-phase connections are compatible with the requirements of the microcontroller ports, with an electrical interface to achieve this.

The mapping of the microcontroller pins (GPIO, I2C, ADC, UART) and the motor driver inputs and outputs is provided in a separate document. Additional documentation that will be provided includes a description of the operation of the motor.

(b) (a)

Figure 1: (a) Simulated Electric Vehicle Sensor and Motor Testing Station. The system includes a microcontroller development kit, a motor driver and sensor board, brushless motor with hall effect sensors for position and velocity feedback and temperature and current sensors for fault monitoring. (b) BOOSTXL-DRV8323RH Motor Control Launchpad Boosterpack

<h1>2.     Requirements</h1>

The assignment can be separated into three main sections: Motor Control, Sensing and User Interface. Each section has different requirements and are described below. You are required to design the solution using a software driver model for each subsystem. The user interface system will then use the driver functionalities to integrate each subsystem into one coherent solution for demonstration. Your team should coordinate and design the software system together in order to manage events, shared resources and CPU utilisation, taking into account the priority of each subsystem and their tasks.

<h2>2.1         Motor Control</h2>

You are to write a device driver for the DRV832x motor driver board (including the additional custom motor adapter board) to provide an application programming interface that

will:

<ol>

 <li>Start and control the motor safely, handling any fault conditions using the provided state transition diagram (see supporting documents for how to control the phases of the motor)</li>

 <li>Control a user given desired speed of the motor in revs/min</li>

 <li>Safely start and accelerate the motor to the desired speed (See below for acceleration</li>

</ol>

specifications)

<ol start="4">

 <li>Safely decelerate the motor (See below for deceleration specifications)</li>

</ol>

The motor must be controlled by specifying the rpm. No marks will be awarded if the motor is controlled by specifying the PWM duty cycle. In order to safely start and brake the vehicle (motor) the system should control the speed of the motor following acceleration and deceleration limits (See separate reference document for supporting speed control diagram). You will also need to investigate how to effectively get a 3 phase brushless motor to start correctly. The system will also require different deceleration limits depending on whether an emergency condition has occurred or a user has input a slower desired speed. The acceleration and deceleration limits are defined as:

<ol>

 <li>Acceleration ≤ 500<em>RPM/s</em></li>

 <li>Deceleration (setting slower speed) ≥ 500<em>RPM/s</em></li>

 <li>Deceleration (emergency stop aka e-stop) = 1000<em>RPM/s</em></li>

</ol>

Please ensure you handle units correctly as acceleration and deceleration values will be tested. Note, the deceleration for e-stop should be equal to (not great or less than) the desired value as we would like the vehicle to come to a complete stop quickly but safely.

<h3>2.1.1         E-stop Conditions</h3>

The following conditions should cause the motor control to enter into an e-stop condition causing the motor to safely brake using the previously indicated deceleration limit.

<ol>

 <li>Total motor current has exceeded a user defined threshold</li>

 <li>Motor temperature has exceeded a user defined threshold</li>

 <li>Absolute acceleration has exceeded a user defined threshold (an impact has occurred) measured via the accelerometer sensor (see section 2.3)</li>

</ol>

<h2>2.2         Motor Library and Motor Kits</h2>

A custom motor library will be supplied to your group in order for you to easily and safely commutate the phases of the motor. This will be available via Blackboard and separate instructions will be supplied describing how to install and use the library. You must use this motor library to minimise the risk of damaging the motor kits supplied with this assignment. However, this does not guarantee that the motors will not be damaged and it is recommended that the motor kits are monitored during operation and if debugging software while operating the motors that either the motor kits are powered down or monitored such that the do not heat up significantly. The teaching team have added some protection mechanisms (motor adapter board and current limited power supplies) to minimise this risk but with all electrical and mechanical hardware it is almost impossible to protect against all possible failures.

<h2>2.3         Sensing</h2>

In order to monitor critical information on the state of the vehicle you will need to write a device driver for the various sensors available on the vehicle. You will need to write the sensor drivers in order to provide an application programming interface (api) to perform the following functionalities:

<ol>

 <li>Read and filter the light level from the ambient light sensor over an I2C connection.</li>

 <li>Read and filter the ambient board temperature and the motor temperature from the the two temperature sensors over a UART connection</li>

 <li>Read and filter two motor phase currents via the analogue signals provided by the three current sensors</li>

 <li>Read and Filter the acceleration on all three axis and calculate average absolute acceleration (sum of absolute acceleration in each axis)</li>

 <li>Setup the accelerometer to detect a user-definable crash threshold (in <em>m/s</em><sup>2</sup>) using the chips output interrupt pin</li>

 <li>Measure and filter the current speed of the motor in revs/min (rpm)</li>

</ol>

<h3>2.3.1         Data Filtering</h3>

All received data is noisy in some fashion such as temperature, current and speed. In order to correctly identify faults in a real-time fashion and ensure noise does not trigger false alarms you must filter this data before displaying it. You must filter the data using the

following specifications:

<ul>

 <li>Temperature – sample window size greater than 3 at ≥ 2 Hz.</li>

 <li>Current – sample window size greater than 5 at ≥ 250 Hz</li>

 <li>Ambient Humidity – sample window size greater than 5 at≥ 1 Hz</li>

 <li>Magnitude (sum of absolute x,y,z values) of acceleration – sample window size (of each axis) greater than 5 at 200 Hz</li>

 <li>Speed – sample window size greater than 5 at ≥ 100 Hz</li>

 <li>Light – sample window size greater than 5 at ≥ 2 Hz</li>

</ul>

It is recommended that this filtering should not be done in a hardware interrupt. For example you should capture 1 sample of the current every 2ms (250Hz) and keep a buffer (window size) of 5 samples. You may use your own filtering technique such as a sliding window technique (see <a href="https://au.mathworks.com/help/dsp/ug/sliding-window-method-and-exponential-weighting-method.html">sliding window</a> for examples). It is recommended that once a set of samples have filled the buffer then a software interrupt should be used to perform the follow up filtering update step. If you believe more samples at higher frequency would improve the performance of the filter then it is okay to increase these specifications.

<h2>2.4         User Interface</h2>

You are required to develop a graphical user interface to control the operation of the electric vehicle. This includes displaying critical information and including the ability to change the status of the vehicle. You should consider the layout of the interface for easy control and display of the information.

Write an application program that uses the device driver and other drivers and allows:

<ol>

 <li>Starting and stopping the motor using buttons</li>

 <li>Classify and display whether it is currently day or night (night time is classified as the ambient light being less than 5 lux)</li>

 <li>Displaying start and stopped status using an LED</li>

 <li>Turn on an LED when the time of day is classified as night time</li>

 <li>Setting the desired speed of the motor using user input</li>

 <li>Keeping track of time and displaying calendar time</li>

 <li>Setting an upper limit of allowable current drawn by the motor. If this condition is met a fault has occurred and the motor should be stopped safely</li>

 <li>Setting an upper limit of allowable temperature of the motor. If this condition is met a fault has occurred and the motor should be stopped safely</li>

 <li>Setting an upper limit of allowable acceleration of the vehicle (from accelerometer sensor). If this condition is met a fault has occurred and the motor should be stopped</li>

</ol>

safely.

On a second page/tab within the user interface you will be required to plot the filtered sensor information from the vehicle in a real-time, user friendly manner. Use your own design/structure for displaying the sensor information, but it must display the following information as a moving plot over time:

<ol>

 <li>approximate power usage of the motor using the phase current measurements (in watts)</li>

 <li>ambient light level (in Lux)</li>

 <li>ambient and motor temperatures (in Degrees Celsius)</li>

 <li>Average absolute acceleration from the accelerometer (<em>m/s</em><sup>2</sup>)</li>

 <li>motor speed (rpm)</li>

</ol>

For displaying the sensor data, units can be of different magnitudes (e.g. Kilowatts), scaled or adjusted for display purposes.

<h1>3.     Video Demonstration</h1>

You are required to present and demonstrate your work to the teaching team via a video presentation. Your video should include a speaking roll by each member of your group. For the video demonstration, you should prepare to show and talk about what you have done. Additional advanced features will also improve your mark only if the design requirements for that system have been met. Your video should step through and demonstrate each design requirement. For the video presentation you should aim to present your designed system to your client which is the automotive manufacture (teaching team). Your presentation should include an overall description of your final design, how you have met specifications and showing all relevant details for the teaching team to assess the quality of your solution.

The duration limit is up to 10 minutes for the total video demonstration.




<h2>4.4         Requirements that could not be demonstrated</h2>

If you describe your work well enough – and your demonstration marks were low – you could be given partial credit to compensate for an inability to demonstrate when it was required. You can add an explanation for each failure if you have been able to figure out how you might proceed if you could do it again.

<h1>5.     Suggested Format of the Report</h1>

The report should contain the following items. The content and style are flexible if these are separately identifiable. Approximate page guidelines are given in parentheses.

<ul>

 <li><em>Cover page </em>– names, student ID numbers, course, and unit information. (1 page)</li>

 <li><em>Table of Contents </em>– Section headings, list of figures, list of tables. (1 to 2 pages)</li>

 <li><em>Introduction </em>– Problem statement, context, requirements, and statements on the individual contributions by each team member. (2 pages)</li>

 <li><em>Design and Implementation </em>– Approach to design, important issues and choices and their relationships to theoretical concepts and the hardware and software platforms. Check if you have addressed:

  <ul>

   <li>Provided a graphical representation for your design?</li>

   <li>Provided a Project Folders and Files diagram?</li>

   <li>Did you use the GPIO module? How? Did you use the graphics library? How?</li>

  </ul></li>

</ul>

Did you use Tasks, Hwi and Swi? How?

<ul>

 <li>Did you use multiple threads/handlers? How? Did you use the ADC? How?</li>

</ul>

<ul>

 <li><em>Results </em>– Summary of evidence of functional requirements that were demonstrated and explanation of failures as learning outcomes in terms of what could have been done differently. (2 pages)</li>

 <li><em>References </em>– Including vendor supplied technical documents with a table that links each document to sub-sections of your report where they are relevant with entries: The reference number, the sub-section number, topic keyword or keywords, the pages that were found useful. (2 pages)</li>

 <li><em>Appendix </em>– Mention the name of the hardware development platform and versions of the tools used such as Code Composer Studio, Tivaware, TI-RTOS, etc. (1 page)</li>

</ul>

<h1>Reference Documents</h1>

<ul>

 <li><a href="https://www.ti.com/lit/ds/slvsdj3c/slvsdj3c.pdf">DRV832x 6 to 60-V Three-Phase Smart Gate Driver Datasheet</a></li>

 <li><a href="https://www.ti.com/lit/ug/slvub13/slvub13.pdf">DRV832XX EVM Sensored Software User’s Guide</a></li>

 <li><a href="https://www.ti.com/lit/ug/slvub01c/slvub01c.pdf">BOOSTXL-DRV8323Rx EVM User’s Guide</a></li>

 <li><a href="https://www.ti.com/lit/ug/spruhd4m/spruhd4m.pdf">TI-RTOS User’s Guide</a></li>

 <li><a href="http://processors.wiki.ti.com/index.php/SYS/BIOS_Getting_Started_Guide">TI-RTOS SYS/BIOS User’s Guide</a></li>

 <li><a href="https://www.ti.com/lit/ug/spmu365c/spmu365c.pdf">EK-TM4C1294XL User’s Guide</a></li>

 <li><a href="https://www.ti.com/lit/ds/symlink/tm4c1294ncpdt.pdf">TM4C1294NCPDT Datasheet</a></li>

 <li><a href="https://www.ti.com/lit/ds/symlink/tmp107.pdf">TMP107 Datasheet</a></li>

 <li><a href="https://www.ti.com/lit/ds/symlink/opt3001.pdf">OPT3001 Ambient Light Sensor Datasheet</a></li>

 <li><a href="https://www.ti.com/lit/ug/slau666b/slau666b.pdf">BOOSTXL-SENSORS Sensors BoosterPack User’s Guide</a></li>

</ul>