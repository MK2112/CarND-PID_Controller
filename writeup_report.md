## Writeup - PID Controller
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

The goal was to build a PID controller and to tune the Controller's hyperparameters by applying the general processing flow as described in the previous lessons. To do that, I had to test my solution on the provided simulator. This write-up includes the rubric points as well as my detailed description of how I addressed each point.


### [Rubric Points](https://review.udacity.com/#!/rubrics/1972/view)

### 1. Compilation
#### The code should compile
I successfully compiled the code with the provided steps:
-   `mkdir build && cd build` to create and enter the build directory
-   `cmake .. && make` to compile the project
-   `./pid` to run the code

The code compiles without errors or warnings.


### 2. Implementation
#### The PID procedure follows what was taught in the lessons
The PID Controller's implementation is directly inspired by the prior work on P-, PD- and PID-Controllers in Python. The algorithm follows the contents of the lessons. I achieved sticking to the contents by utilizing the repository's starter code aswell as moving expressions like

```
diff_cte = cte - prev_cte
int_cte += cte
prev_cte = cte
```

from the lesson's Python code over into C++. With this the PID's UpdateError method calculates the integral, derivative and proportional errors like so:

```
d_error = cte - p_error;
i_error += cte;
p_error = cte;
```
*(PID.cpp, lines 27-29)*

### 3. Reflection
#### Description of the effect each of the P, I, D components had in my implementation

In the controller the **proportional component** aims to move the car toward the road's center. It therefore directly reduces the cross-track error. With this component on its own (all the others being zero), the car would overshoot the center line very easily, repeatedly and increasingly. This quickly makes the car go off the road entirely.

The **integral component** aims to reduce possible systematic bias on the controlled system (e.g. mis-aligned steering axis) that could prevent the cross-track error to be continuously reduced. With this component on its own, car ... goes in circles. This is the case as in the simulator, no bias is present.
    
The **differential component** is a countermeasure for the proportional components tendency to overshoot the center line easily. This is achieved by smoothing out the approach to the center.

#### Description of how the hyperparameters were chosen
The choosing process turned out to be a process of trial and error. I approached this by
- finding a p value that would make the car go reasonably far on the road on its own before overshooting too much
- add smoothing to focus more on keeping close track of the center line by increasing the d value.
- as there was no systematic bias present in the simulator, setting the i value made the car go off the road more easily again. After tuning the other parameters I decided to simply keep it at 0

The values (with which the car completed an entire lap) are P: 0.14, I: 0.0, D: 1.0