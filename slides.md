---
marp: true
_class: invert
paginate: true
backgroundColor: #161616
color: #f1f1f1
theme: default
transition: fade 0.3s
footer: "Optimisation and Planning Summer School 2025"
---

<style>
    @import "base";
    @import url("https://fonts.googleapis.com/css2?family=Geist&display=swap");
    :root {
        font-size: 22px;
        font-family: "Geist", sans-serif;
        --color-fg-default: white;
        --color-canvas-default: white;
        padding: 64px;
    }
    footer, header { 
        padding: 32px 32px;
    }
    h1 > strong {
        color: #f1f1f1;
    }
</style>

![width:160px](logo-wide.png)

# **Introduction to Multi-agent Path Planning**

In this tutorial, you'll learn to develop a multi-agent path planning algorithm.

---

# Set up your environment

Open up a terminal and run the following command:

### MacOS

```bash
curl -fsSL https://pathfinding.ai/opss25-setup/install | bash
```

### Linux

```bash
wget -qO- https://pathfinding.ai/opss25-setup/install | bash
```

### Windows

```ps
powershell -c "irm https://pathfinding.ai/opss25-setup/install.ps1 | iex"
```

---

# Exercise 1: 
## The A* Algorithm

In this exercise, we are going to implement the basic Lego blocks of search:
![width:500px](logo-wide.png)

----

## The Expander

During search, an expander generates valid successors of the current state.
This means that expanders are *domain-dependent*, and must be modified to match the `rules' of the world. 

In this exercise, your task is to implement an expander for the RobotRunners competition. Robots are able to perform three actions:
1) Move forward one step,
2) Rotate 90 degrees either clockwise (CW) or counter-clockwise (CCW),
3) Wait at their current location.

In ```ex1_robotrunners_expander.py```, read through ```expand()``` and modify the helper functions ```get_actions()``` and ```__move()``` to generate nodes based on the above actions each robot can perform.

*HINT: in get_actions(), you need to make sure that moves are valid within the environment.*
<!-- How do expanders work? Let's write a grid-expander together.

```py
if (self.domain_.get_tile((x, y + 1))):
        retval.append(grid_action())
        retval[-1].move_ = Move_Actions.MOVE_RIGHT
        retval[-1].cost_ = 1

if (self.domain_.get_tile((x - 1, y))):
    retval.append(grid_action())
    retval[-1].move_ = Move_Actions.MOVE_UP
    retval[-1].cost_ = 1
``` -->
----
##

Okay, so we now have a way to generate new states, but we also have to evaluate which looks the most promising! 

This brings us to the idea of domain-dependent hueristics. Let's have a quick look at implementing a couple.
Write the function for the Manhattan distance heuristic together (one liner)

Manhattan does reasonably well, but what are we actually looking for? What do we *want* from a heuristic? (greatest value LESS than or equal to the true distance.)

Say things to queue up the idea that we really want (perfect heuristic).
When we have the time, we often want to precompute heuristics and re-use these for search! This can essentially eliminate the need for real-time search, but is still extremely useful for dynamic environments as a basline cost (lower bound).

Let's have a look at how we do do this. Get the idea for Dijkstras from the crowd. Then work on showing the template for what we are having a look at. Here are the barebones, you need to run this yourself and collect a database of the results. 

Let's have a look at how much faster search is now.
Run a series of problems. 

Should we create a script to report summary table in terminal for comparison purposes. 

Now, we have solved grid-based pathfinding problems. We can generate new states, and we can search over them efficiently. 
But oftentimes, problems introduce a new 

----
# Excercise 2

We now have a working low-level search! Agents can get where they need to go.

But agents are not alone. Agents must plan paths in a *shared* environment.

```Switch to the 'E2' branch``` to continue. We have already filled out the expander from E1 for you. 

Let's see what happens when we plan paths for *two* agents.
Run ```MAPFplanner.py``` and observe the output.

 **What do you see?** 

----
## Dealing With Agent Collisions - Part A

Evidently, we have an issue now. Agents must learn to avoid each other!

In this Exercise, we will work on implementing a way of tracking locations that agents occupy in order to avoid collisions. We call this data strcture a *reservation table*, and it has two main methods:

1) ```add_reservation(state)``` (reserves a specific location for an agent),
2) ```is_reserved(state)``` (which checks if a location is reserved)

In your code, you will see template functions for these two functions. It is your task to complete the implementation. You are safe to assume that the class has an inbuilt ```reservation_table```, which is a 2D array indexed by [x][y].

----
## Dealing With Agent Collisions - Part B

You will also need to complete this implementation---agents need to reserve their location in our high-level planner! To do so, you will need to:

3) modify your ```expander``` so that it does not generate new states if there is a collision!
4) modify the ```high-level planner``` to make agents reserve their path.

To test your implementation, run ```MAPFplanner.py```. 
**What looks different?**