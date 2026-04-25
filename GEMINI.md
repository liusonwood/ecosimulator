# EcoSim - Ecological Succession Simulator (v3.3.1)

EcoSim is a spatially explicit vegetation dynamic simulator that models the succession of plant functional types (Lichen, Moss, Herb, Shrub, Tree, and Crop) on a grid. It uses a Lotka-Volterra competition model combined with seed dispersal and environmental constraints (soil depth and climate) to simulate how ecosystems evolve over centuries.

## Project Overview

-   **Purpose:** Simulate and visualize ecological succession sequences (e.g., Lichen → Moss → Herb → Shrub → Tree).
-   **Core Tech Stack:**
    -   **Frontend:** Vue 3 (Global script, non-modular), Tailwind CSS (Local script with CDN fallback).
    -   **Styling:** External `style.css` for Glassmorphism effects and custom UI components.
    -   **Computation:** Hybrid engine supporting GPU acceleration (GPU.js), Multi-threaded CPU (Web Workers), and Serial CPU execution.
    -   **Visualization:** HTML5 Canvas (Emoji-based rendering with biomass-driven scaling) and Chart.js for population dynamics.
    -   **Icons:** Lucide Icons.

## Architecture

-   `index.html`: 16:9 Glassmorphism UI layout, Vue templates, and library includes.
-   `style.css`: Centralized styles for the UI, ensuring a consistent look and feel.
-   `main.js`: Core `EcoSimulator` class (simulation logic, multiple engines) and Vue application setup.
-   `design/`: Directory containing mathematical models (`mathmodel.md`), UI design specifications (`uidesign.md`), and updated parameter CSVs (`parameters-2.0.csv`).
-   `lib/`: Local copies of required libraries for `file://` protocol compatibility (Vue, GPU.js, Chart.js, Lucide).

## Core Simulation Logic

1.  **Species ($N=6$):** Includes Lichen, Moss, Herb, Shrub, Tree, and Crop.
2.  **Seed Dispersal:** Seeds are produced based on biomass and dispersed using a Gaussian kernel (either via GPU shaders or precomputed CPU kernels).
3.  **Succession Thresholds:** Higher-order species can only germinate once a certain total biomass (simulating soil organic matter) has accumulated from pioneer species.
4.  **Local Competition:** Species compete for space using a Lotka-Volterra model, modified by species-specific carrying capacities ($K$), soil depth multipliers, and climate factors.
5.  **Multi-Engine Execution:** Automatically selects the fastest available engine (GPU > Worker > Serial) with manual override via `window.setMode`.

## Development and Running

### Running Locally
The project is designed to run directly from the file system.
1.  Open `index.html` in any modern web browser.
2.  Ensure the `lib/` directory contains all necessary scripts.

### Key Commands (Console)
-   `setMode('GPU'|'Worker'|'Serial'|'Auto')`: Manually switch the computation engine.

## Development Conventions

-   **Non-Modular:** Avoid `import/export` or `type="module"`. Use global variables.
-   **Surgical Edits:** Use the `replace` tool for targeted updates to `main.js` and `index.html`.
-   **Centralized Configuration:** All adjustable parameters and the version number are managed in the `CONFIG` object within `main.js`.
-   **Comments:** Maintain detailed Chinese documentation within `main.js` to explain the mathematical models.

## Key Files and Directories

-   `index.html`: The entry point and UI structure.
-   `style.css`: UI styling and animations.
-   `main.js`: The simulation engine, state management, and version control.
-   `design/mathmodel.md`: Current mathematical foundation.
-   `design/parameters-2.0.csv`: Authoritative reference for biological parameters.
-   `lib/`: Essential JavaScript libraries for offline execution.
