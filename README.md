# TurtleSim-in-ROS2

# ROS 2 Turtle Square Movement Project Report

## Project Overview
Programming a turtle to autonomously draw a square pattern using velocity commands in ROS 2.

---

## 1. Introduction

### Robot Motion Control in ROS 2

Robot motion control enables autonomous movement through **velocity commands** rather than position control, providing real-time responsiveness and hardware abstraction.

### Importance of `/cmd_vel` Topic

The `/cmd_vel` (command velocity) topic is the standard interface for robot motion:

- **Message Type:** `geometry_msgs/msg/Twist`
- **Purpose:** Publishes linear and angular velocity commands
- **Universality:** Works with turtlesim, TurtleBot, mobile robots, and drones
- **Real-time Control:** Continuous publishing maintains motion; stopping publishing stops the robot
- **Standardization:** Industry-standard across ROS ecosystem

---

## 2. Materials & Tools Used

| Component | Details |
|-----------|---------|
| **ROS 2 Distribution** | Humble Hawksbill |
| **OS** | Ubuntu 22.04 LTS |
| **Language** | Python 3.10 |
| **Simulator** | Turtlesim |
| **Message Type** | `geometry_msgs/msg/Twist` |
| **Build Tool** | colcon |
| **Testing Tools** | `ros2 topic`, `rqt_graph` |

---

## 3. Methodology

### 3.1 Understanding Twist Message

```python
geometry_msgs/Vector3 linear
  float64 x    # Forward/backward velocity (m/s)
  float64 y    # Left/right (usually 0 for ground robots)
  float64 z    # Up/down (usually 0 for ground robots)

geometry_msgs/Vector3 angular
  float64 x    # Roll (usually 0)
  float64 y    # Pitch (usually 0)
  float64 z    # Yaw - turning left/right (rad/s)
```

**For Ground Robots:**
- `linear.x`: Forward (+) or backward (-) speed
- `angular.z`: Turning speed (+ = counter-clockwise, - = clockwise)

### 3.2 Square Movement Logic

**Strategy:**
1. Move Forward → Set `linear.x = 1.0`, `angular.z = 0.0` (1 second)
2. Turn 90° → Set `linear.x = 0.0`, `angular.z = 1.57` (1 second)
3. Repeat 4 times
4. Stop after completing square


## 4. Problem-Solving Approach

### Issue 1: Incorrect Shebang Line

**Error:**
```python
#!usr/bin/env python3  # ❌ Missing leading slash
```

**Error Message:**
```
bad interpreter: No such file or directory
```

**Problem:** Missing `/` at the beginning prevents script execution

**Solution:**
```python
#!/usr/bin/env python3  # ✅ Correct - note the leading /
```

### Issue 2: Comparison Instead of Assignment

**Error:**
```python
if self.state == 'move':
    self.state == 'turn'  # ❌ WRONG - This compares, doesn't assign!
elif self.state == 'turn':
    self.side_count += 1
    self.state == 'move'  # ❌ WRONG - Also just comparing
```

**Problem:** Using `==` (comparison operator) instead of `=` (assignment operator)

**Result:** State never changes, turtle stuck in infinite loop doing the same action

**Solution:**
```python
if self.state == 'move':
    self.state = 'turn'   # ✅ CORRECT - Assigns new value
elif self.state == 'turn':
    self.side_count += 1
    self.state = 'move'   # ✅ CORRECT - Assigns new value
```

**Key Difference:**
- `==` compares two values and returns True/False
- `=` assigns a value to a variable

---

## 5. Testing & Results

### Terminal Output

```bash
# Start turtlesim
$ ros2 run turtlesim turtlesim_node

# Run square movement
$ ros2 run simple_pkg turtle_square.py
```

<img width="2880" height="1800" alt="image" src="https://github.com/user-attachments/assets/b9cdfad0-5135-442b-a089-40505692de9e" />

### Results Summary

| Test | Expected | Result | Status |
|------|----------|--------|--------|
| Sides drawn | 4 | 4 | ✅ |
| Corner angles | 90° | ~90° | ✅ |
| Side lengths | 1.0m | ~1.0m | ✅ |
| Auto-stop | Yes | Yes | ✅ |

<img width="2880" height="1800" alt="image" src="https://github.com/user-attachments/assets/8418810c-d08f-4c7e-8e5f-a6d298f2a89d" />

<img width="2880" height="1800" alt="image" src="https://github.com/user-attachments/assets/f3f1d28d-1c99-4032-9e4e-b21b1bd7cdb6" />

---

## 7. Conclusion

### Key Learnings

1. **ROS 2 Motion Control**
   - Velocity-based control through `/cmd_vel`
   - Twist message for commanding velocities
   - Timer-based periodic execution

2. **Robot Kinematics**
   - Linear velocity for forward motion
   - Angular velocity for rotation
   - Relationship: distance = velocity × time

3. **State Machine Design**
   - State-based sequential control
   - Transition logic between states
   - Counter-based termination

4. **Common Errors**
   - Shebang must start with `#!/` not `#!`
   - Assignment `=` vs comparison `==`
   - Object instantiation requires parentheses
   - Avoid reserved attribute names

### Skills Acquired

- ✅ ROS 2 publisher implementation
- ✅ Timer-based control loops
- ✅ State machine design
- ✅ Geometric calculations
- ✅ Debugging robotic systems
---

**Author:** Akhileshwar Pratap Singh 
**Date:** December 2025
**License:** MIT
