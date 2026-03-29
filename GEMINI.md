# EcoSim - Ecological Succession Simulator

EcoSim is a spatially explicit vegetation dynamic simulator that models the succession of plant functional types (Lichen, Moss, Herb, Shrub, and Tree) on a grid. It uses a Lotka-Volterra competition model combined with seed dispersal and environmental constraints (soil depth and climate) to simulate how ecosystems evolve over centuries.

## Project Overview

-   **Purpose:** Simulate and visualize ecological succession sequences (e.g., Lichen → Moss → Herb → Shrub → Tree).
-   **Core Tech Stack:**
    -   **Frontend:** Vue 3 (Global script, non-modular), Tailwind CSS (CDN).
    -   **Computation:** Custom CPU-based simulation engine with precomputed Gaussian kernels for dispersal.
    -   **Visualization:** HTML5 Canvas (ImageData for pixel manipulation) and Chart.js for population dynamics.
    -   **Icons:** Lucide Icons.
-   **Architecture:**
    -   `index.html`: 16:9 Glassmorphism UI layout, Vue templates, and library includes.
    -   `main.js`: Core `EcoSimulator` class (simulation logic) and Vue application setup.
    -   `design/`: Directory containing mathematical models (`mathmodel.md`), UI design specifications (`uidesign.md`), and parameter CSVs.
    -   `lib/`: Local copies of required libraries for `file://` protocol compatibility.

## Core Simulation Logic

1.  **Seed Dispersal:** Seeds are produced based on biomass and dispersed using a Gaussian kernel.
2.  **Succession Thresholds:** Higher-order species (e.g., Trees) can only germinate once a certain total biomass (simulating soil organic matter) has accumulated from pioneer species.
3.  **Local Competition:** Species compete for space using a Lotka-Volterra model, modified by species-specific carrying capacities ($K$) and soil depth multipliers.
4.  **Disturbances:** Manual events (Fire, Volcano, Drought) can be triggered to reset or alter the simulation state in specific areas.

## Development and Running

### Running Locally
The project is designed to run directly from the file system.
1.  Open `index.html` in any modern web browser.
2.  Ensure the `lib/` directory contains `vue.global.js`, `gpu-browser.min.js`, and `chart.js`.

### Key Commands (Inferred)
Since this is a client-side project without a build step:
-   **Build/Compile:** None required.
-   **Test:** Manual verification in the browser.

## Development Conventions

-   **Non-Modular:** Avoid `import/export` or `type="module"`. Use global variables.
-   **Surgical Edits:** Use the `replace` tool for targeted updates to `main.js` and `index.html`.
-   **Performance:** Prioritize efficient rendering (e.g., `ImageData`) and precomputed values for simulation steps.
-   **Parameters:** All adjustable simulation parameters (growth rates, competition coefficients, thresholds) are centralized in the `CONFIG` object in `main.js`.

## Key Files and Directories

-   `index.html`: The entry point and UI structure.
-   `main.js`: The simulation engine and state management.
-   `design/mathmodel.md`: The mathematical foundation of the simulation.
-   `design/parameters-2.0.csv`: Reference for species-specific biological parameters.
-   `lib/`: Essential JavaScript libraries for offline/local execution.
