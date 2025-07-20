# Perlin Noise World Generation

This Go program generates a 3D world using Perlin noise for terrain generation and simulates mineral vein distribution.

## Table of Contents

- [Perlin Noise World Generation](#perlin-noise-world-generation)
  - [Table of Contents](#table-of-contents)
  - [Features](#features)
  - [How it Works](#how-it-works)
    - [Perlin Noise Generation](#perlin-noise-generation)
    - [World Generation](#world-generation)
    - [Mineral Vein Generation](#mineral-vein-generation)
  - [Getting Started](#getting-started)
    - [Prerequisites](#prerequisites)
    - [Running the Program](#running-the-program)
  - [Program Usage](#program-usage)
  - [Code Structure](#code-structure)
  - [Constants](#constants)
  - [Material Legend](#material-legend)

## Features

* **Perlin Noise Terrain:** Generates a 2D Perlin noise map to define the height of the terrain.
* **3D World Generation:** Creates a 3D block-based world with different materials like bedrock, dirt, stone, and various ores.
* **Mineral Vein Simulation:** Simulates the generation of mineral veins (coal, iron, gold, diamond) with probabilities and vein sizes.
* **Interactive Output:** Allows users to visualize the Perlin noise map, terrain height graphs, and different cross-sections of the generated world.

## How it Works

### Perlin Noise Generation

The Perlin noise generation process involves several steps:

1.  **Gradient Vector Creation:** Random gradient vectors are created at the vertices of a grid (`n_slot` x `n_slot`).
2.  **Dot Product Calculation:** For each pixel within a grid slot, dot products are calculated between the pixel's distance vectors to the four surrounding grid vertices and the gradient vectors at those vertices. This results in four intermediate Perlin maps.
3.  **Interpolation:** The four intermediate Perlin maps are combined using bilinear interpolation (or cosine interpolation, as an alternative commented out) to create a smooth Perlin noise map.
4.  **Value Resizing:** The Perlin noise values are scaled and clamped to a range (e.g., -9 to 9) for easier interpretation and use in world generation.
5.  **World Map Integration:** The 2D Perlin noise map is then adapted to serve as a height map for the 3D world.

### World Generation

The 3D world is represented by a `[h_blocks][lenght_blocks][deep_blocks]int` array.
* **Bedrock Layer:** The lowest level (height 0) is always bedrock.
* **Surface Generation:** Above a certain height (`stone_level[1]`), dirt blocks are generated based on the Perlin noise map, simulating the ground level. Air blocks are generated above the dirt or if outside the Perlin noise-defined surface.
* **Subsurface Generation:** Below the surface, the program attempts to generate stone or mineral blocks.

### Mineral Vein Generation

Mineral generation is influenced by:
* **Height Levels:** Each mineral (coal, iron, gold, diamond) has a defined height range where it can appear.
* **Probabilities:** Each mineral has a percentage probability of being generated at a given location if the height conditions are met.
* **Vein Continuity:** The `mineral_recognition` and `vein_recognition` functions check for existing mineral blocks nearby. If a mineral is found, there's an increased chance of generating the same type of mineral, simulating veins.
* **Vein Size Limits:** Each mineral type has a maximum number of blocks allowed in a single vein (`coal_vein`, `iron_vein`, etc.). The `total_veins` map keeps track of active veins and their sizes.

## Getting Started

### Prerequisites

You need to have Go installed on your system. You can download it from [https://golang.org/dl/](https://golang.org/dl/).

### Running the Program

1.  Save the provided code as `perlin_noise.go`.
2.  Open a terminal or command prompt in the directory where you saved the file.
3.  Run the program using the Go command:

    ```bash
    go run perlin_noise.go
    ```

## Program Usage

After running the program, you will be prompted with options:

* **Press 1:** To visualize the Perlin noise map and a graph of height differences.
    * You will then be asked to choose between viewing height differences per row or per column.
* **Press 2:** To view the number of generated minerals and different layers of the world.
    * You will be asked to enter the name of a mineral (coal, iron, gold, diamond) to highlight in the world view.
    * Then, you can choose to display different cross-sections of the world:
        * **1:** Section parallel to the ground plane (horizontal layers).
        * **2:** Section perpendicular to the ground plane, viewed from the left (front view).
        * **3:** Section perpendicular to the ground plane, viewed from the front (side view).
        * Any other number to terminate the world view.

## Code Structure

* `main()`: The entry point of the program, orchestrating the Perlin noise generation, world generation, and user interaction.
* `create_total_vector()`: Initializes random gradient vectors for Perlin noise.
* `create_perlin_maps()`: Calculates the four intermediate Perlin noise maps using dot products.
* `dot_product()`: Performs a dot product calculation between two 2D vectors.
* `perlin_map_interpolation()`: Combines the four intermediate maps using bilinear interpolation.
* `bilinearInterpolation()`: Implements bilinear interpolation.
* `cosineInterpolation()`: Implements cosine interpolation (currently not used in `perlin_map_interpolation`).
* `resizing_values()`: Scales and clamps the Perlin noise values.
* `create_perlin_map_for_world()`: Consolidates the Perlin noise map into a single 2D array for world height.
* `draw_perlin_map()`: Prints the Perlin noise map to the console.
* `graphic_of_hight()`: Prints a text-based graphic representing height differences.
* `assignment_of_blocks()`: Populates the 3D world array with blocks.
* `casual_generation_world()`: Determines the material for a specific block based on its position and surrounding blocks.
* `veins_nearby()`: Checks if there are any mineral veins in the vicinity of a given block.
* `mineral_recognition()`: Identifies if a neighboring block is a mineral and returns its details.
* `create_new_vein()`: Registers a new mineral vein.
* `vein_recognition()`: Determines if a block belongs to an existing mineral vein.
* `zone_is_empty()`: Checks if a given zone around a block is free of minerals.
* `resising_limit()`: Adjusts search limits to prevent going out of world bounds.
* `vein_update()`: Updates the `total_veins` map when a new block is added to an existing vein.
* `data()`: Prints the total count of each mineral type.
* `print_levels()`: Allows the user to print different cross-sections of the generated world, highlighting a chosen mineral.

## Constants

* `n_slot`: Number of grid slots along one dimension for Perlin noise (total slots: `n_slot * n_slot`).
* `n_pixel_in_slot`: Number of pixels along one side within each slot (each slot has `n_pixel_in_slot * n_pixel_in_slot` pixels).
* `lenght_blocks`: Length dimension of the world.
* `deep_blocks`: Depth dimension of the world.
* `h_blocks`: Height dimension of the world.

## Material Legend

* `air = 0`
* `bedrock = 10`
* `dirt = 1`
* `stone = 2`
* `coal = 3`
* `iron = 4`
* `gold = 5`
* `diamond = 6`
