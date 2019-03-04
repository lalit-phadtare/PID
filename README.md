# CarND-Controls-PID
Self-Driving Car Engineer Nanodegree Program

---

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `./install-mac.sh` or `./install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.
* Simulator. You can download these from the [project intro page](https://github.com/udacity/self-driving-car-sim/releases) in the classroom.

Fellow students have put together a guide to Windows set-up for the project [here](https://s3-us-west-1.amazonaws.com/udacity-selfdrivingcar/files/Kidnapped_Vehicle_Windows_Setup.pdf) if the environment you have set up for the Sensor Fusion projects does not work for this project. There's also an experimental patch for windows in this [PR](https://github.com/udacity/CarND-PID-Control-Project/pull/3).

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid`. 

Tips for setting up your environment can be found [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)

## Reflection

### Effect of P, I and D parameters

* P as it stands for proportional param. has a correcting effect w.r.t. to the error or diff. from the baseline. However this it soon has an oscillating effect and the car starts to swerve a lot in either direction leading to getting off-track soon.
This is because there is no factor to control the multiplication by P. Following video will demonstrate it:

[car controlled using only P](https://github.com/lx-px/PID/blob/master/P.mp4)


* D (derivative constant) has a very strong effect to avoid the oscillating (ringing) effect caused by just having the P constant. Following video shows the car recovers from the wide angle caused by P after some steps and this is taken care in both direction.

[car controlled using only PD](https://github.com/lx-px/PID/blob/master/PID.mp4)


* I (integral constant) helps with some drift. There is not a lot of drift seen from the simulator behavior but overall it looks like it helps center the car more quickly than just having the PD constants. 
Here is a short clip of actual implementation:

[car controlled using PID](https://github.com/lx-px/PID/blob/master/PID.mp4)


### Tuning the parameters

Here is the process I followed to tune the params.:

* I used twiddle algorithm to tune the steer and speed PID parameters.
* Start with PD tuning for the steer angle but keep the speed constant at 0.3.
* P initial value = 0.0,0.1, D initial value = 0.0,0.5,1.0,2.0.,3.0 (since a large D was needed to control the P).
* Min. steps to settle/ Steps where error is measured = 100/100, 50/200, 20/40. Finally tuned with 20/40.
* Error (dp from class) = 1, 0.1, 0.01, 0.001. The error tolerance correspondingly was 0.2, 0.02, 0.002, 0.0002. Finally chose dp = 0.01 and tolerance = 0.002.
* Tune for I using the PD values obtained above. I initial value were chose from 0.1,0.01,0.001. I needed to be small to have required effect.
* Keeping the twiddle for steer on, train for speed PID in the same process described above. I was found zero for this speed tuning. 

Final values for were:
* steer PID = [0.397614, 0.0001, 3.19852]
* speed PID =  [0.01, 0, 0.0190584]

* I used a bias for speed in the main.cpp to push the car to max limit. The max bias (in steps of 0.1) was found to be 0.4. The car averages around 40mph at this bias.
