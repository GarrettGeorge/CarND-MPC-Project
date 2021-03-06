[formulas]: vehicle_model_formulas.png "Logo Title Text 2"


## Model Predictive Control

---

### The Model

The model used to represent the state of vehicle at time _t_ was a kinematic model which models an _x_ and _y_ coordinate relative to some _(0, 0)_ origin as well as _psi_, the heading angle and velocity vector _v_. As with many control algorithms,a cost function is introducing in order to be minimized. The two major errors tracked are cross track error and psi error. 

The actuators used are accelaration between 1.0 and -1.0, where negative accelartion corresponds to breaking, and steering angle with range [-25 degrees, 25 degrees]. 

Below are the formulas generated when all the variables come together to create the state _{x<sub>t+1</sub>, y<sub>t+1</sub>, ψ<sub>t+1</sub>, v<sub>t+1</sub>, cte<sub>t+1</sub>, eψ<sub>t+1</sub>}_ below:

![vehicle model formulas][formulas]

### Timestep (_N_) and elapsed duration (_dt_)

The values selected were based on those used in the guided study for the project. Originally I had thought to use _n = 25_ and _dt = 0.05_ but upon seeing the values from the guided study I thought about to change my chosen _N_ and _dt_. The primary reason being that my values could potentially hamper the performance of the MPC especially considering a 100ms latency.

### Polynomial Fitting and MPC Preprocessing

The preprocessing for my MPC implementation consisted of transforming the state as it was received from a map-based orientation to a vehicle-based orientation. This allowed the calculations for coefficients, _cte_, and the _psi_ error.

### Model Predictive Control with Latency

To combat the latency problem as directly as possible I have a check in MPC.cpp to check for values of T larger than 1. In this event it means we're "behind" so to speak. Instead of using values that may put the car's eventual trajectory over the yellow line and cause oscillation, we can simply use the previous actuator values at _t - 2_ and eliminate the oscillation. The other way to help with the negative results of latency on the path of the car is to add cost to the car when it's having to use actuators often and with large values between sequential actions. Minimizing this cost will allow the potential effects of latency at higher speeds and around corners to be handled safely. 