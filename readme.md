# Modifiable version of the LunarLander environment.
Environment used for the paper **Uncertainty-Based Out-of-Distribution Classification in Deep Reinforcement Learning**.   
https://arxiv.org/abs/2001.00496

While the landing pad position is fixed in the original LunarLander environment, this version enabled configurable, varying positions.   
This way, the environment can be used for out-of-distribution or generalization research in RL.

## HELIPAD-POSITION:
Position (x and y) of the landing pad is determined new on every reset.   
The x and y position is determined via the function self.helipad_fn .   
This function can be specified by passing it as a parameter to the set_random_helipad_on_reset function.   
When called, the function receives a single parameter 'np_random', which is the environments seeded random generator.   
It must return two floats, specifying the x and y coordinate of the helipad.   
Note that the x coordinate specifies the center of the helipad in terms of 'chunks' (see update_helipad function on how these chunks are defined).   
Maximum values are dependent on the VIEWPORT_H/W and SCALE settings (range checks are enforced).   
E.g. pass the following function to vary the position of the helipad in the specified ranges.   
```python
  def helipad_fn(np_random):
      x = np_random.uniform(2, 5),
      y = np_random.uniform(1, 12),
      return x, y
```
Then set it on the unwrapped environment e.g.:
```python
  env.unwrapped.set_random_helipad_on_reset(True, helipad_fn)
```
This way, it is possible to e.g. train only with helipads on the left side of the environment,
then evaluate with helipads on the right side of the environment by setting different ranges.
See main at the bottom of lunar_lander.py for an example.

## STATE-SHAPE:
State shape is extended by 3 values: helipad_x1, helipad_x2, helipad_y coordinates.   
(The original LunarLander does not include the helipad's coordinates in the state.)   
The helipad_x1 / helipad_x2 values specify the left / right coordinate of the helipad, which are calculated   
based on the width of the helipad (3 chunks) and the center coordinate output by helipad_x_fn.   

## REWARD FUNCTION
Note that the reward function was modified and differs from the original LunarLander.   

---

Based on https://github.com/openai/gym/blob/master/gym/envs/box2d/lunar_lander.py   
Modifications by Andreas Sedlmeier (andreas.sedlmeier@ifi.lmu.de).   
Licensed on the same terms as the rest of OpenAI Gym.