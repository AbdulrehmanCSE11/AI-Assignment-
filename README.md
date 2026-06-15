# AI-Assignment-
# PEAS Framework and Task Environment Analysis

Course: Artificial Intelligence

Assignment: PEAS Framework and Task Environment Analysis

Reference: Russell & Norvig – Artificial Intelligence: A Modern Approach, Chapter 2

Student Name: Abdul Rehman
Roll No: 2k23/CSE/11
Date: 15-June-2026

## Table of Contents

1. [PEAS Framework](#task-1-peas-framework)
2. [Environment Classification](#task-2-environment-classification)
3. [Critical Analysis](#task-3-critical-analysis)
4. [Structured JSON Representation](#task-4-structured-json-representation)
5. [References](#references)

## Introduction

For this assignment I picked the Automated Warehouse Sorting Robot, basically the kind of robot used by companies like Amazon Robotics. Whenever someone places an online order, a robot like this moves around the warehouse, finds the right item on a shelf, picks it up, and brings it to a packing station so it can be shipped out, all without a human handling each item directly.

I chose this topic because I think it's one of the coolest real-world examples of an AI agent working in a physical environment. I've seen videos of Amazon warehouses where hundreds of robots move around at once without bumping into each other, and I wanted to actually understand how that works.

The robot has to:
- Move around a busy space full of other robots and human workers
- Find and pick items of all different shapes and sizes
- Do this fast enough and accurately enough to keep up with thousands of orders every day

So basically it's a good example of an agent with clear goals, inputs, outputs, and ways to measure performance, which is what this assignment is about.

## Task 1: PEAS Framework

The system I'm analyzing is the warehouse sorting robot used inside fulfilment centres to find, pick, and move items from storage shelves to packing/dispatch stations in real time.

### Performance Measures

1. Sorting and Picking Accuracy: how often the robot picks the correct item and puts it in the correct bin. Should be above 99% because at the scale of millions of picks a day, even a small error rate adds up to a lot of wrong orders.

2. Throughput (Items Processed Per Hour): how many items it can handle per shift. During big sales events, the warehouse gets way more orders, so the robot needs to keep up or things start backing up.

3. Damage and Mis-sort Rate: how often items get dropped, crushed, or put in the wrong bin. Damaged stuff means losses, and wrong bins mean wrong orders to customers, which leads to refunds and unhappy customers.

4. Energy and Uptime Efficiency: how much battery it uses per item, and how much time it actually spends working vs sitting idle or charging. Since there are thousands of these robots, even small efficiency improvements save a lot overall.

### Environment

The robot works inside huge warehouses that run 24/7. The environment has:

- Storage racks/pods with thousands of products, conveyor belts, packing stations, charging docks, and safe zones for workers
- Hundreds of other robots and human workers around at the same time
- A central computer that manages the whole fleet
- Orders coming in constantly and changing
- Shelves/pods that get rearranged based on demand, so the robot can't rely on a fixed map
- Different floor conditions (texture, clearance, etc.)
- A need for constant wireless connection, if it drops, the robot loses coordination with the rest of the fleet

### Actuators

- Robotic Arm and End-Effector: uses suction cups, grippers, or soft fingers to pick up and place items of different shapes, sizes, and weights
- Mobile Drive Base: omnidirectional wheels to move around, line up with shelves, and dock at conveyors/charging stations
- Vertical Lift / Telescoping Mechanism: lets the arm reach items at any shelf height
- Indicator/Signalling System: LEDs, sounds, and screens to let nearby humans know what it's about to do, reducing collision risk

### Sensors

- Barcode/RFID Scanners: confirm it's grabbing the right item
- Force/Torque/Load-Cell Sensors: in the gripper, detect if the item is slipping so it can fix its grip before dropping it
- RGB-D Cameras: colour + depth, used for planning grasps and spotting obstacles
- LIDAR + Ultrasonic Sensors: build a live map of the aisle, detect other robots/people moving around
- Wheel Encoders + IMU: track position, direction, and speed for accurate movement
- Fleet Management Signals: get task assignments, congestion updates, battery/charging schedules, and order priorities from the central system

## Task 2: Environment Classification

| Dimension | Classification | Justification |
|---|---|---|
| Fully vs Partially Observable | Partially Observable | The robot can only "see" the area immediately around it, shelves and other robots block the view of the rest of the warehouse. |
| Single-Agent vs Multi-Agent | Multi-Agent | Hundreds of robots and people share the same space, and the fleet system has to manage everyone competing for the same aisles. |
| Deterministic vs Stochastic | Stochastic | Even if a grasp worked before on the exact same item, it might fail this time because the item shifted slightly in its packaging, so it's not 100% predictable. |
| Episodic vs Sequential | Sequential | Every pick/move affects battery level and how crowded the aisles are, which then affects what the robot can do next. |
| Static vs Dynamic | Dynamic | Other robots and people keep moving and new orders keep coming in while the robot is still planning/acting, nothing waits for it. |
| Discrete vs Continuous | Continuous | Things like arm angles, gripper force, wheel speed, and distances to obstacles are all real numbers, not fixed steps. |

## Task 3: Critical Analysis

### 3.1 Most Technically Challenging Dimension: Dynamic Environment

I think the hardest part of this whole system is dealing with the fact that the warehouse never stops changing. Other robots are constantly moving through the same aisles, workers walk around unpredictably, and new orders keep coming in and changing what needs to be picked next.

The problem is that any plan the robot makes is based on what the warehouse looks like at that exact moment. By the time it actually starts moving, something might have already changed, another robot enters the aisle, or a person steps into the path. So the robot can't just make one plan and stick to it, it has to keep re-planning while it's moving, which needs really fast processing and constant communication with the rest of the fleet.

Another big issue is deadlocks, when two or more robots block each other's only way through and nobody can move. Figuring out how to detect and fix these before they shut down a whole section of the warehouse is apparently still an active area of research in warehouse robotics.

So overall, I think handling a constantly changing, shared physical space is the toughest engineering challenge here.

### 3.2 Utility Function and Trade-Off Analysis

For this robot, there's a clear trade-off between doing as many picks as possible, as fast as possible, vs not damaging items or causing accidents with other robots or people.

I came up with this utility function:

```
U = α × Throughput_Score − β × Damage_Risk − γ × Collision_Risk
```

Throughput_Score = items picked and moved per hour
Damage_Risk = chance of dropping/crushing the item during the current pick/move
Collision_Risk = chance of hitting (or almost hitting) another robot or person, based on speed and proximity

If we increase the weight on Throughput_Score (α), the robot starts moving way more aggressively, faster speeds, sharper turns, quicker grabs. This means more items per hour in the short term, but also a much higher chance of dropping fragile items and getting too close to other robots or workers.

This shows that warehouse robots can't just be optimized for speed, they need a balance between productivity, item safety, and workplace safety. Pushing one factor too hard ends up hurting the others and the overall system performs worse, not better.

## Task 4: Structured JSON Representation

```json
{
  "system_name": "Automated Warehouse Sorting Robot",
  "peas": {
    "performance_measures": [
      "Sorting and Picking Accuracy: percentage of pick attempts where the correct item is grasped and placed in the correct destination",
      "Throughput: number of items successfully processed per hour",
      "Damage and Mis-sort Rate: share of items dropped, crushed, or placed in the wrong destination bin",
      "Energy and Uptime Efficiency: battery consumption per item moved and proportion of time spent actively completing tasks"
    ],
    "environment": "Large physical warehouse fulfilment centre containing storage racks, conveyor belts, packing stations, charging docks, hundreds of co-located robots, human workers, and a continuously changing queue of customer orders.",
    "actuators": [
      "Robotic Arm and End-Effector Assembly: grasps and places items using suction cups, pincer grippers, or soft robotic fingers",
      "Mobile Drive Base: navigates the warehouse floor using omnidirectional wheels",
      "Vertical Lift and Telescoping Mechanism: raises or lowers the arm to access items at any shelf height",
      "Indicator and Signalling System: communicates intended movements to nearby human workers using LED lights and audible tones"
    ],
    "sensors": [
      "Barcode and RFID Scanners: confirm correct item identity before picking",
      "Force, Torque, and Load-Cell Sensors: detect grip security and weight changes indicating a slipping item",
      "High-Resolution RGB-D Cameras: capture colour and depth for grasp planning and obstacle identification",
      "LIDAR and Ultrasonic Proximity Sensors: build real-time maps of the surrounding aisle and detect nearby robots and people",
      "Wheel Encoders and IMU: track the robot's position, orientation, and velocity for precise navigation",
      "Fleet-Management and Warehouse-System Signals: receive live task assignments and congestion data from the central system"
    ]
  },
  "environment_classification": {
    "fully_observable_vs_partially_observable": {
      "choice": "Partially Observable",
      "justification": "Onboard sensors cover only the local area around the robot. Shelves and other robots block the wider warehouse view."
    },
    "single_agent_vs_multi_agent": {
      "choice": "Multi-Agent",
      "justification": "Hundreds of robots and human workers share the same aisles and infrastructure simultaneously."
    },
    "deterministic_vs_stochastic": {
      "choice": "Stochastic",
      "justification": "Grasp outcomes depend on unpredictable item position and packaging variation that the robot cannot observe."
    },
    "episodic_vs_sequential": {
      "choice": "Sequential",
      "justification": "Each pick and route decision changes battery level and aisle congestion, affecting all future task options."
    },
    "static_vs_dynamic": {
      "choice": "Dynamic",
      "justification": "Robots, orders, and workers continuously change the warehouse state while the robot is operating."
    },
    "discrete_vs_continuous": {
      "choice": "Continuous",
      "justification": "Arm joint angles, gripper force, velocities, and obstacle distances are all continuous numerical values."
    }
  },
  "utility_function": "U = alpha * Throughput_Score - beta * Damage_Risk - gamma * Collision_Risk"
}
```

## References

Russell, S., & Norvig, P. (2022). Artificial Intelligence: A Modern Approach (4th ed.). Pearson.
Guizzo, E. (2008). Three Engineers, Hundreds of Robots, One Warehouse. IEEE Spectrum, 45(7), 26–34.
Wurman, P. R., D'Andrea, R., & Mountz, M. (2008). Coordinating Hundreds of Cooperative, Autonomous Vehicles in Warehouses. AI Magazine, 29(1), 9–20.
