## Car Yaw prediction
##### In this kernel, a NN is built to, given a car image (cropped from the original, using YOLO, for example), predict its yaw. Hope this kernel/model will serve those of you who are using a separated stages to solve the problem.

### 1. Intuition
##### To describe the rotation of an object we need three things: Yaw, Pitch and Roll.


##### As shown by the data analysis, roll is 0 always. This makes sense as there aren't any cars dumped (in this dataset), so we can suppose roll always 0

.

With respect to Pitch (elevation of the nose of the car), the distribution of this datasets shows a gaussian with μ≈0.1
and a low σ. The intuition tells us that because of a car is parallel to the ground, this should be 0. The deviation of this dataset seems to be due to the annotation/generation failures. Notwithstanding, we can suppose that Pitch will be always μ=0.15

.

So, only Yaw is unknown. This will be our regression target.

It is expressed as a rotation with range (+π,−π)
(in radians), taking 0

as facing frontwards. The cars are, in most of the cases, facing frontwards or backwards (they are on our lane or on the opposite).

This way of expressing the target has a problem: wrapping arround edges. That is, if we predict a car is rotated 3.13

radians and the real target is 0, in reality, we are very close to the target, but the loss would be huge. We need a way to transform the target so we can eliminate the wrapping.

There is a paper [https://arxiv.org/pdf/1612.00496.pdf](https://arxiv.org/pdf/1612.00496.pdf) which solution I found clever and very simple. I will explain it here.

For fixing the problem of wrapping, we first have to convert the rotation to "global angle", that is +π=180,−π=180
(the negative angles are 360−a^). Once done that, we divide the space in N bins shifted a little bit (as shown in the image) so that we can exploit the advantage that most of the cars have 0 and 180 degrees.



Here's an example of a definition:
