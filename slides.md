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

# Cloning OPSS25-StartKit

Go to ```https://github.com/ShortestPathLab/opss25-startkit/```

Navigate to a chosen directory in Terminal. Run the following command (using your preferred of https or SSH):

```bash
git clone https://github.com/ShortestPathLab/opss25-startkit.git
```

*Support*: for those unfamiliar with Git, we recommened visiting the *Github Docs* for cloning a repository.

Navigate to the newly created directory and explore by running:

```bash
cd opss25-startkit
ls
```

We recommend opening the Start Kit in your favourite IDE.

----

# Test your Setup

Navigate to the OPSS25 top directory. Switch to a bash shell by running ```bash```. 
To test the setup, run the following command:

```bash
opss25-lifelong --inputFile example_problems/random/random_1.json
```

If you see a search trace, you are good to go!

Otherwise, please raise your hand and we will come over to help.

----

# League of Robot Runners Competition

![alt text](lorr-screenshot.png)

https://www.leagueofrobotrunners.org/

---

![bg left:40%](astar.png)

# Arc 1: Pathfinding Search

In this exercise, we are going to implement the basic Lego blocks of search:

##### ⏭️ Expander

This is the expander that generates valid successors of the current state.

##### 💡 Heuristic

This is a function that estimates the cost of reaching the goal from the current state.
<br/>

**Switch to the A1 branch to continue: `git checkout a1`**

---

# A\* Visualised

TODO

---

# The Expander

During search, an expander generates valid successors of the current state.
This means that expanders are *domain-dependent*, and must be modified to match the `rules' of the world. 

In this exercise, your task is to implement an expander for the RobotRunners competition. Robots are able to perform three actions:
1) Move forward one step,
2) Rotate 90 degrees either clockwise (CW) or counter-clockwise (CCW),
3) Wait at their current location.

In ```ex1_robotrunners_expander.py```, read through ```expand()``` and modify the helper functions ```get_actions()``` and ```__move()``` to generate nodes based on the above actions each robot can perform.

*HINT: in get_actions(), you need to make sure that moves are valid within the environment.*

---


# RobotRunners Expander - SOLUTION

![w:500](get_actions.png) ![w:500](move.png)

---

# Domain-Dependent Heuristics

We now have a way to generate new states, but we also have to evaluate which looks the most promising!

Nodes in A* are ranked by $f(n) = g(n) + h(n)$.

Unlike what we have seen of heuristics in PDDL, in pathfinding we aim to implement domain-dependent hueristics. Let's have a quick look at implementing a couple.

Your task is to implement the:
1. ```manhattan_distance_heuristic()```,
2. ```octile_distance_heuristic()```, and
3. ```straight_line_distance()```

Manhattan distance returns the optimisitic grid-distance between any two locations (ignoring obstacles).
The Octile distance returns a similar estimate, but it allows diagonal movements (cost = $\sqrt(2)$).
The Straight line distance is just the direct Euclidean distance bewtween two points (length of a straight line).

----

# Heuristics - SOLUTION

![w:500](manhattan.png) 

![w:900](octile.png)

![w:900](straight.png)

----

# Creating the Search

TODO

---

# Run your Code

Run the start kit with one agent:

```bash
opss25-lifelong --inputFile example_problems/random/random_1.json
```

Then, visualise your solution:

```bash
opss25-planviz --plan=output.json --map=example_problems/random/maps/random-32-32-20.map
```

---

# How to Choose a Heuristic?

Not all heuristics are equal. What do we *want* from a heuristic?

----

# Improving our Heuristic

Task: ```Modify the manhattan distance heuristic to be direction-aware.```

*Hint: agents may require some number of turns to face the correct direction, and then may need to bend their path.*

We have provided a template for ```get_init_turns``` to help calculate the number of turns that may be required for an agent to face in one of the heuristic-recommended directions.

## Improving our Heuristic - SOLUTION

Task: ```Modify the manhattan distance heuristic to be direction-aware.```

![w:400](turns.png) ![w:700](direction_aware.png)


# Congrats on completing the first exercise!

You should now have a working A\* search.

### What's next?

- 💡 Check out the `ex4_basic_planner.py` file. It contains the structural code that runs your search engine in the context of the robot runner start kit.

- 💡 Experiment with various heuristics.

- 💡 Try creating a visualisation using Posthoc.

---

TODO replace picture

![bg left:30%](./astar.png)

# Arc 2: Dealing with Agent Collisions

We now have a working low-level search! Agents can get where they need to go.

But agents are not alone. Agents must plan paths in a _shared_ environment.

`Switch to the 'a2' branch` to continue. We have already filled out the expander from a1 for you.

Let's see what happens when we plan paths for _two_ agents.
Run `MAPFplanner.py` and observe the output.

**What do you see?**

---

## Dealing With Agent Collisions - Part A

Evidently, we have an issue now. Agents must learn to avoid each other!

In this Exercise, we will work on implementing a way of tracking locations that agents occupy in order to avoid collisions. We call this data strcture a _reservation table_, and it has two main methods:

1. `add_reservation(state)` (reserves a specific location for an agent),
2. `is_reserved(state)` (which checks if a location is reserved)

In your code, you will see template functions for these two functions. It is your task to complete the implementation. You are safe to assume that the class has an inbuilt `reservation_table`, which is a 2D array indexed by [x][y].

---

## Dealing With Agent Collisions - Part B

You will also need to complete this implementation---agents need to reserve their location in our high-level planner! To do so, you will need to:

3. modify your `expander` so that it does not generate new states if there is a collision!
4. modify the `high-level planner` to make agents reserve their path.

To test your implementation, run `MAPFplanner.py`.
**What looks different?**
