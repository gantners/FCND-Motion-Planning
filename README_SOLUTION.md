# FCND - 3D Motion Planning

##[Rubric](https://review.udacity.com/#!/rubrics/1534/view)

## Explain the Starter Code

### Criteria 1 
Test that `motion_planning.py` is a modified version of `backyard_flyer_solution.py` for simple path planning. 
Verify that both scripts work. 
Then, compare them side by side and describe in words how each of the modifications implemented 
in `motion_planning.py` is functioning.

**Comparison**

The git diff viewer highlights the code differences between those files:

![Quad Image](./images/diff1.png)

Outlining the changes which have been done to `backyard_flyer_solution.py`:

- At the top some libraries have been added to `motion_planning.py`:
- import of planning helper functions from `planning_utils.py` a_star, heuristic,create_grid
- import of helper script method `frame_utils.global_to_local()` from udacidrone library

- The class `States` has one more State called `PLANNING` and the ordinals have changed from hard coded to `auto()` ordering

-  Variable `all_waypoints` has been renamed to just `waypoints`

- waypoints are not calculated anymore over the `calculate_box()` function, which mapped out a square path of 10m north, 10m east, 10m south and back 10m west 3m above the ground. Instead they shall be filled later as TODO

- The method `state_callback(self)` now handles the new State `PLANNING` which takes place after the State `ARMING` and is set at
the `plan_path()` function after successful arming


![Quad Image](./images/diff2.png)


- In general all States are now set before the actions taking place which is more appropriate. E.g. State `TAKEOFF` is set before
the function `self.takeoff()` is called, that means while takeoff we are in State `TAKEOFF` and not when we already took off as in
`backyard_flyer_solution.py`

- in `def arming_transition(self):` function `self.arm()` and `self.take_control()` have swapped lines but according to the udacidrone source code this should not have any sideeffects and home position is not anymore set on this function, instead is now part of the TODO

- in `def takeoff_transition(self):` function the first waypoint is used as a first target whereas in `backyard_flyer_solution.py` a hardcoded 3m altitude was used

![Quad Image](./images/diff3.png)

- in `def start(self):` function `self.connection.start()` is used directly instead of super class `Drone` function `start()` which both do the same thing 

- New function called `send_waypoints(self):` has been introduced which is used for sending planned waypoints to the established connection of either simulator or drone

- New function called `plan_path(self):` which contains all TODO`s for implementing the 3D motion planning.

## Implementing Your Path Planning Algorithm

### Criteria 1
In the starter code, we assume that the home position is where the drone first initializes, but in reality you need to be able to start planning from anywhere. Modify your code to read the global home location from the first line of the colliders.csv file and set that position as global home (self.set_home_position())
[See motion_planning.py](./motion_planning.py#134)

### Criteria 2 
In the starter code, we assume the drone takes off from map center, but you'll need to be able to takeoff from anywhere. Retrieve your current position in geodetic coordinates from self._latitude, self._longitude and self._altitude. Then use the utility function global_to_local() to convert to local position (using self.global_home as well, which you just set)
[See motion_planning.py](./motion_planning.py#138)

### Criteria 3
In the starter code, the start point for planning is hardcoded as map center. Change this to be your current local position.
[See motion_planning.py](./motion_planning.py#155)

### Criteria 4
In the starter code, the goal position is hardcoded as some location 10 m north and 10 m east of map center. Modify this to be set as some arbitrary position on the grid given any geodetic coordinates (latitude, longitude)
[See motion_planning.py](./motion_planning.py#162)

### Criteria 5
Write your search algorithm. Minimum requirement here is to add diagonal motions to the A* implementation provided, and assign them a cost of sqrt(2). However, you're encouraged to get creative and try other methods from the lessons and beyond!
[See planning_utils.py](./planning_utils.py#59) 4 diagonal actions have been added as well as code for checking off being off grid for those

### Criteria 6
Cull waypoints from the path you determine using search.
[See planning_utils.py](./planning_utils.py#217)   Bresenham algorithm was used for checking if we can remove unnecessary waypoints by testing if there is a path from point a to c without b which is not colliding with any obstacle on the grid.


### Criteria 7
This is simply a check on whether it all worked. Send the waypoints and the autopilot should fly you from start to goal!
[See motion_planning.py](./motion_planning.py#179) Planning works most of the time, except if A* does not find any valid path to goal.

[To check just change motion_planning.py coordinates lon,lat to your desired coordinates](./motion_planning.py#30)