---
layout: post
title:  "Cycloidal Drive"
date:   2024-11-25 17:24:20 +0000
categories: jekyll update
---
<script type="text/javascript" async
    src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
  </script>

I'm going to use a cycloidal drive at each joint in my new robot arm. The benefits of a cycloidal drive are:

1. Gear Reduction - Gear reduction allows the robot arm to be smoother, stronger and more precise.

2. Backlash Immunity - The robot arm will stay put (even under load) without any effort from the stepper motors.

3. Easy to make - Compared to some other compact drives, this one is really easy to design, which is good for me.

### The Math
Okay, I've sold you on the cycloidal drive. But what is a cycloid?

![Image of a cycloid.](/assets/media/cycloidal-drive/cycloid.png "Cycloid")

A cycloid is the path traced by a single point on a circle as it rolls, without slipping, along a straight line. For the purposes of the cycloidal drive, we are going to use a very similar function, the epicycloid.

The epicycloid is the path traced by a single point on a circle as it rolls, without slipping, along the outside of another, larger circle. 

![Image of a General Epicycloid.](/assets/media/cycloidal-drive/epicycloid.png "General Epicycloid")

The parametric functions are defined below.

$$
x(t) = (R + r) \cos(t) - r \cos\left(\frac{R + r}{r} t\right)
$$

$$
y(t) = (R + r) \sin(t) - r \sin\left(\frac{R + r}{r} t\right)
$$

where $$R$$ represents the radius of the large circle, $$r$$ represents the radius of the rolling circle, and $$t$$ is an angle, ranging from $$0 < t < 2\pi$$.
Note that only certain values of $$R$$ and $$r$$ create a closed shape.

#### Finding values that close the shape
To find $$R$$ and $$r$$ values that close the shape, we need values such that 
$$(x(0),y(0)) = (x(2\pi),y(2\pi))$$

The set of numbers that can be used as inputs for the equation is defined as $$r(m-1) = R$$

$$R$$: Radius of larger circle.

$$r$$: Radius of smaller circle.

$$m-1$$: An integer that represents the number of lobes on the epicycloid.

A more complete derivation is provided at the end.

#### Removing the Cusps
Now, these equations have an issue. Each lobe on the circle meets the next at a cusp, forming a sharp corner. This is a problem for two reasons.
1. Autodesk Inventor's *Equation Curve* tool doesn't handle cusps.
2. 3D printed parts with sharp corners are weaker than parts with rounded corners.

To smooth the curve, we need to adjust our equation a little, by mutiplying the second trig function in each parameter by some value $$s$$. From a geometric point of view, $$s$$ determines how much the rolling circle "slips" as it rolls. This slippage stretches the path traced by the point on the smaller circle, removing the cusps.

The new functions are given below.

$$
x(t) = (R + r) \cos(t) - s\cdot r \cos\left(\frac{R + r}{r} t\right)
$$

$$
y(t) = (R + r) \sin(t) - s\cdot r \sin\left(\frac{R + r}{r} t\right)
$$

Notes:
1. $$s$$ is only effective if $$ 0< s < 1$$. 
2. As $$s$$ approaches $$0$$, the curve becomes more like a circle.

### Modelling the Cycloidal Disc
Now that we have our equations, let's decide on some values.

One fact that I've failed to mention is that the gear reduction obtained from a cycloidal drive is determined by $$m$$, or the number of lobes plus 1.

To decide on our values, let's first determine a gear ratio. This will vary by application depending on the speed and lifting requirements of the drive.

I'm going to use a 10:1 gear reduction, so my $$m$$ value is going to be $$10$$.

The next value of importance is the radius of the gear. I want my gear to have a radius of 20mm. The radius of an epicycloid is determined by $$R + r$$, so $$r = 20 - R$$.

Plugging that into the relationship between $$R$$, $$r$$, and $$m$$, we get:

$$(20-R)\cdot(10-1) = R$$

$$R = 180/10 = 18$$

$$r = 20 - 18 = 2$$

Now let's graph our epicycloid using the values we found. For now, we'll leave $$s$$ equal to 1.

$$
x(t) = (18+2) \cos(t) - s\cdot 2 \cos\left(\frac{18+2}{2} \cdot t\right)
$$

$$
y(t) = (18+2) \sin(t) - s\cdot 2 \sin\left(\frac{18+2}{2} \cdot t\right)
$$

Simplified:

$$
x(t) = (20) \cos(t) - s\cdot 2 \cos\left(10 \cdot t\right)
$$

$$
y(t) = (20) \sin(t) - s\cdot 2 \sin\left(10 \cdot t\right)
$$


![Image of an Epicycloid using the values just defined.](/assets/media/cycloidal-drive/epicycloid2.png "Epicycloid 2")

To remove the cusps, let's use $$s = .65$$

$$
x(t) = (20) \cos(t) - .65\cdot 2 \cos\left(10 \cdot t\right)
$$

$$
y(t) = (20) \sin(t) - .65\cdot 2 \sin\left(10 \cdot t\right)
$$

Simplified:

$$
x(t) = (20) \cos(t) - 1.3 \cdot \cos\left(10 \cdot t\right)
$$

$$
y(t) = (20) \sin(t) - 1.3 \cdot \sin\left(10 \cdot t\right)
$$

![Image of a smooth epicycloid.](/assets/media/cycloidal-drive/epicycloid2_smooth.png "Smooth Epicycloid")

### Modelling the Ring Pins
Next, we need to make something for the cycloidal disc to rotate within.

In manufactured cycloidal drives, they use metal for the components, 
and they use cylinders inside a circle to make the track for 
the disc to ride on.

Because I am 3D printing this, I think that one solid piece (rather than 
many loose pins) will make a better track for the disc.

To model this, first we need to start from our cycloidal disc formula.

We want the new Epicycloid to:
1. Have the same sized lobes as the cycloidal disc.
2. Have one more lobe than the cycloidal disc (this will allow the disc to rotate).
3. Be large enough for the cycloidal disc to move inside it.

Without getting too deep into the math, it's important to realize what each part 
of the Epicycloid function does.

I'll use the $$x(t)$$ term in this example, but the example works on either.

$$
x(t) = (R + r) \cos(t) - s \cdot r \cos\left(\frac{R + r}{r} t\right)
$$

The first term, $$(R + r) \cos(t)$$, draws a circle size $$R+r$$.
The second term, $$- s \cdot r \cos\left(\frac{R + r}{r} t\right)$$, causes
the lobes on the epicycloid. 

To make the ring pins, we need to roll our $$r$$ circle around 
a circle larger than $$R+r$$. In order to preserve the same lobe size with
the new, larger circle, the $$r$$ circle tracing the lobes needs to rotate faster.

These facts tell us that we need to scale the first part of the
equation, and we need to scale the term inside the trig function of 
the second part. These changes will make a large circle with an additional lobe 
while still preserving the same lobe size.

To make these changes, let's first consider the size of the big circle.
we need the cycloidal disc to be able to move within the new epicycloid.
In order to figure out how much bigger the new epicycloid needs to be,
we need to first figure out how big of a circle the first term makes.
When graphed on top of our cycloidal disc equation, the first term looks
like this:
![Image of a epicycloid and a circle of radius R+r.](/assets/media/cycloidal-drive/epicycloid_and_circle.png "Epicycloid and Circle")

The orange circle can be represented by the equations:

$$
x(t) = (R + r) \cos(t)
$$

$$
y(t) = (R + r) \sin(t)
$$

We see that the circle (orange) traces the midline between the peaks and 
troughs of the epicycloid. That means that the new epicycloid must be at
least $$r/2$$ bigger than the cycloidal disc. We also need to consider that 
the new epicycloid will have smoothed cusps that exented into the $$R+r$$ 
circle. To accomodate for both halves, the new epicycloid needs to be $$r$$ 
bigger than the cycloidal disc.

Thus, the first half of the equations for the ring pins will be:

$$
x(t) = (R + 2r) \cos(t)
$$

$$
y(t) = (R + 2r) \sin(t)
$$

To finish the equation, remember that $$\frac{R + r}{r} = m$$, and $$m$$ represents the number of lobes, 
so to add a lobe, simply add 1.

$$
x(t) = (R + 2r) \cos(t) - s\cdot r \cos\left((m+1) t\right)
$$

$$
y(t) = (R + 2r) \sin(t) - s\cdot r \sin\left((m+1)) t\right)
$$

Our new epicycloid, when graphed on top of the old one, looks like this:
![Image of cycloidal disc and Ring Pins Cycloid.](/assets/media/cycloidal-drive/both_epicycloids.png "Cycloidal Disc and Ring Pins Cycloid")

This graph is to scale, so we can visually determine that the inner (blue) 
disc will be able to move inside the outer (orange) disc.

Given all this information, we are ready to create a 3D model of the components.


## Full Derivation of Cycloidal Formula
Note: this portion is not polished. Read at your own risk.

To find a general solution to our problem, lets solve each parameter symbolically at $$ 0 $$ and $$ 2\pi $$.

To find the beginning of the curve, let's use $$t=0$$:

$$x(0) = (R + r) \cos(0) - r \cos\left(\frac{R + r}{r} \cdot 0\right) = (R + r) - r = R$$

$$y(0) = (R + r) \sin(0) - r \sin\left(\frac{R + r}{r} \cdot 0\right) = 0 - 0 = 0$$

Thus, $$(x(0),y(0)) = (R, 0)$$

Now to find the end of the curve, let's use $$t = 2\pi$$:


$$x(2\pi) = (R + r) \cos(2\pi) - r \cos\left(\frac{R + r}{r} \cdot 2\pi\right) = (R + r) - r \cos\left(\frac{R + r}{r} \cdot 2\pi\right)$$

We are looking for $$x(0) = x(2\pi)$$. Let's set the solutions for $$x(0)$$ and $$x(2\pi)$$ equal to eachother and simplify.

$$R = R+r-r \cos\left(\frac{R + r}{r} \cdot 2\pi\right)$$

$$0 = r(1-\cos\left(\frac{R + r}{r} \cdot 2\pi\right))$$

since $$r \not= 0$$, we can divide by $$r$$.

$$\cos\left(\frac{R + r}{r} \cdot 2\pi\right)=1$$

Cosine equals $$1$$ at every multiple of $$2\pi$$, so as long as $$\frac{R + r}{r}$$ is an integer, the equation above holds true. We can represent this mathematically as:

$$\frac{R + r}{r} = m$$

where $$m$$ is any integer.

Now to solve $$y(2\pi)$$:

$$y(2\pi) = (R + r) \sin(2\pi) - r \sin\left(\frac{R + r}{r} \cdot 2\pi\right) = 0 - r \sin\left(\frac{R + r}{r} \cdot 2\pi\right)$$

Let's set the solutions for $$y(0)$$ and $$y(2\pi)$$ equal to eachother and simplify.

$$0 = - r \sin\left(\frac{R + r}{r} \cdot 2\pi\right)$$

Divide by $$-r$$.

$$0 = \sin\left(\frac{R + r}{r} \cdot 2\pi\right)$$

Sine equals $$0$$ at every multiple of $$\pi$$, so the equation is as follows. 

$$2 \cdot\frac{R + r}{r} = n$$

where $$k$$ is an integer.

Now, let's combine the two solutions.

$$m = \frac{R + r}{r}$$

and

$$n = 2 \cdot \frac{R + r}{r}$$

After dividing $$n$$ by two, we can set them equal to eachother, forming the following relationship. 

$$\frac{n}{2} = m$$

Given this relationship, we can solve for $$n$$, and plug that into the sine equation to find a general solution for the system.

$$r(m-1) = R$$

where $$m$$ is any integer.