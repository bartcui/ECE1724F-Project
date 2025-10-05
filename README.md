# ECE1724F-Project-Project-Proposal
# Simple Game Engine
Bart Cui - 1011827908
Oliver Chen - 1003045518
## Motivation

Our motivation for picking this project comes from both personal interest and curiosity about how modern game engines are built. We heard that more gaming companies are starting to adapt existing large-scale game engines such as Unity and Unreal. These engines are powerful, but also very complex with hidden tools and APIs. Building even a small engine of our own gives us the chance to gain a deeper understanding of how engines operate and how these core components such as scene management, ECS design, and real-time rendering cooperate with each other.

Also, since Rust’s emphasis is on memory safety and concurrency without a garbage collector makes it particularly appealing for systems-level development. Having used C++ in past projects, we’re hoping to explore how Rust’s ownership model can improve both developer productivity and runtime safety in a game engine context. Given the scope of a course project, we plan to focus on implementing a minimal prototype featuring a basic rendering pipeline, level loading, and a simplified ECS framework. 

## Novelty

Since we are new to the Rust ecosystem, we are not fully aware of all the gaps that currently exist or which ones need the most attention. However, based on our research on game engine design and Bevy, we found that Bevy is good at real-time, parallel systems but is not directly aimed at turn-based, grid-based games. In such games, reproducibility is crucial and identical input sequences should always lead to the same outcomes. Bevy does not guarantee this behaviour by default. To address this, we will try to implement a custom turn scheduler that ensures consistent and reproducible state transitions.

Although the new Bevy 0.17 recently introduced initial tilemap rendering support, it still lacks native grid utilities such as coordinate-to-world mapping, occupancy management, and pathfinding. We will try to build the engine to fill this space by adding a grid-aware foundation and reusable utilities designed for the needs of turn-based puzzle design, and hopefully can represent a small but meaningful contribution to the Rust game development ecosystem.

## Objective

Our goal is to design a small 2D game engine in Rust, built on top of Bevy, purpose-built for deterministic, turn-based, grid-based puzzle games. We will use Bevy’s ECS, scheduling, and plugin model, and extend it with a deterministic turn scheduler and a grid-native utility layer. The engine will load grid levels from data files and provide reproducible gameplay using seeded random numbers and replays. We will ship a demo that showcases level loading, scene transitions (Menu → Game → Ending), and deterministic enemy chase behaviour.

## Key Features

### Deterministic Turn Scheduler
Implements a fixed pipeline (Input → Intent → Resolve → Commit) with explicit system ordering, a single-threaded commit step, and seeded RNG.  
Deliverables: replay files (inputs + seed) and golden tests that reproduce identical end states.

### Grid-Native Abstractions
Defines a GridCoord type and provides grid↔world mapping (tile size/origin), occupancy and layers (terrain/unit/item), blocking and collision flags, neighbour iterators (4- or 8-connected), and region queries (flood-fill).

### Map Loader
Supports level formats such as JSON, TOML, or simple ASCII layouts.  Validates bounds, layers, and blocking flags, and builds grid + occupancy indices on load.

### ECS for Game Objects
Implements minimal, composable components such as Position(GridCoord), Player, Blocking, Goal, Trap, and AI.  
Example: a wall = Position + Blocking; an exit = Position + Goal; a ghost = Position + Actor + AI + Blocking. Systems follow the turn pipeline and only mutate state during the commit phase.

### Scene Management
Organizes the game into scenes such as Loading, Main Menu, Game, and Game Over. Each scene has its own setup and cleanup, ensuring smooth transitions and predictable behaviour.

### Movement Rules
Controls how characters move on the grid. Movement will be turn-based, where the player acts and then enemies respond, but the rules will remain customizable. Examples include explicit tie-breaks when multiple actors target the same cell, or configurable win/lose triggers.

### Pathfinding Algorithm
Uses A* as the default shortest-path solver. A* provides optimal routes like Dijkstra’s algorithm while exploring fewer nodes when guided by a heuristic. Passability and step costs are defined through a pluggable policy (a Rust trait), so different movers (player, ghost), door/key mechanics, or terrain costs can be swapped without altering the solver. The default heuristic is Manhattan distance (|dx| + |dy|), which is admissible and consistent on a 4-connected grid — fast to compute and ideal for deterministic turn logic.

## Tentative Plan

We aim to finish the project within the timeframe of five weeks and have enough time to polish and work on the report and demo in late November. First, we both need to get familiar with Bevy and game engine design concepts, focusing on ECS, plugins, states, assets, 2D rendering aspects. After the ramp-up, Bart will focus on developing the core systems and logic of the engine. Components need to be defined which represent the basic elements of the game world, such as the grid positions, characters, boundaries, and exits. Bart will also design the movement and rule systems, which control how players and enemies move on the grid and how turns are resolved. This includes implementing the conditions for collisions and win/lose states, such as what happens when the player reaches an exit or when an enemy catches the player. Another key part will be input mapping that makes sure that keybindings from a configuration file translate correctly into actions inside the game. By the start of week 4, all these features should be developed and ready for integration.

Oliver’s role will focus on the content layer of the engine which includes building the grid and level loader that reads from configuration files and spawns the game world. He will set up the rendering system so that tiles and boundaries are displayed correctly. Oliver will also implement the scene and state management, ensuring smooth transitions between menus, the game, and ending screens. To make the engine more usable, he will add simple UI elements such as score board. Another key part will be ensuring that much of the gameplay is driven by configuration files, so that anyone can edit a text file to change the level layout, inputs, or even basic rules.

After the integration of our components, we will work together on the pathfinding for the enemies which will combine logic systems with the grid. Many rounds of testing are needed to make sure the movement rules work smoothly within the grid and rendering system. By week 5, we will work on the simple game demo, showcase the engine’s features and the final proof that the framework works as intended.

