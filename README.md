<p align="center">
  <img src="https://github.com/user-attachments/assets/948c5a6d-f450-400f-8e13-0d6026796d99" alt="Forest Fire Simulation Logo" width="600" />
</p>

**Forest Fire Simulation** is an agent-based forest fire propagation simulator built with **Julia**, **Agents.jl**, and a web-based frontend.

The project models how fire spreads through a forest grid depending on tree density, spread probability, wind direction, wind strength, and optional long-distance spark behavior.

> [!NOTE]
> This project combines a Julia backend simulation with a React-based frontend interface for visualizing forest fire behavior over time.

---

## Overview

**Forest Fire Simulation** represents a forest as a 2D grid where each tree is modeled as an individual agent.

Each tree can be in one of three states:

- Green
- Burning
- Burnt

The simulation starts with a group of burning trees near the center of the forest. During each step, burning trees may spread fire to nearby green trees depending on the configured probability and wind values.

The frontend allows users to configure simulation parameters, start or stop the simulation, and visualize how the fire spreads through the forest.

> [!IMPORTANT]
> This project is a simulation and educational model. It is not intended for real wildfire prediction, emergency planning, or environmental risk assessment.

---

## Features

- Agent-based forest fire simulation.
- Julia backend using `Agents.jl`.
- REST API using `Genie.jl`.
- React frontend for visualization.
- Grid-based forest model.
- Configurable forest density.
- Configurable grid size.
- Configurable fire spread probability.
- Configurable simulation speed.
- Wind influence on fire propagation.
- Optional long-distance spark behavior.
- Tree state visualization with SVG assets.
- Burnt tree percentage tracking.
- Iteration counter.
- Plotly graph for burnt tree progression.
- Local API communication between frontend and backend.

---

## Project Structure

```text
Forest-Simulator_Case2/
│
├── forest.jl
├── webapi.jl
├── LICENSE
├── README.md
│
├── fe/
│   ├── public/
│   │   ├── greentree.svg
│   │   ├── burningtree.svg
│   │   └── burnttree.svg
│   │
│   ├── src/
│   │   ├── App.jsx
│   │   ├── App.css
│   │   ├── index.css
│   │   └── main.jsx
│   │
│   ├── package.json
│   ├── package-lock.json
│   ├── vite.config.js
│   └── index.html
│
└── frontend/
    ├── app/
    │   ├── page.js
    │   ├── page.module.css
    │   ├── layout.js
    │   └── globals.css
    │
    ├── package.json
    ├── package-lock.json
    └── next.config.mjs
```

---

## Main Files

| File | Description |
|---|---|
| `forest.jl` | Main agent-based forest fire simulation model. |
| `webapi.jl` | Genie web API used to create and advance simulations. |
| `fe/src/App.jsx` | Main Vite React frontend with controls, visualization, and plotting. |
| `fe/public/greentree.svg` | SVG used to display healthy trees. |
| `fe/public/burningtree.svg` | SVG used to display burning trees. |
| `fe/public/burnttree.svg` | SVG used to display burnt trees. |
| `frontend/app/page.js` | Older or alternative Next.js frontend prototype. |
| `LICENSE` | Project license file. The current license is CC0 1.0 Universal. |

> [!NOTE]
> The `fe/` folder appears to be the more complete frontend because it includes controls for density, spread probability, wind speed, simulation speed, big jumps, burnt percentage, and Plotly graph output.

---

## Technologies Used

### Backend

| Technology | Purpose |
|---|---|
| `Julia` | Main simulation language. |
| `Agents.jl` | Agent-based modeling framework. |
| `Genie.jl` | Web API server. |
| `Distributions.jl` | Random probability distribution support. |
| `HTTP.jl` | HTTP server support. |
| `UUIDs` | Unique simulation ID generation. |

### Frontend

| Technology | Purpose |
|---|---|
| `React` | User interface. |
| `Vite` | Frontend development server and build tool. |
| `AWS Amplify UI React` | UI components such as buttons, sliders, and switches. |
| `Plotly.js` | Burnt tree percentage chart. |
| `React Plotly.js` | Plotly integration for React. |
| `SVG` | Tree visualization assets. |

---

## Requirements

To run the full project, you need:

- Julia
- Node.js
- npm
- A modern web browser

Recommended versions:

```text
Julia 1.x
Node.js 18+
npm 9+
```

> [!TIP]
> Use current stable versions of Julia and Node.js unless your class or instructor requires a specific version.

---

## Julia Backend Setup

Open a terminal in the project root:

```bash
cd Forest-Simulator_Case2
```

Start Julia:

```bash
julia
```

Install the required Julia packages:

```julia
using Pkg

Pkg.add("Agents")
Pkg.add("Distributions")
Pkg.add("Genie")
Pkg.add("HTTP")
```

Exit Julia or continue from the same session.

Run the API server:

```bash
julia webapi.jl
```

The backend will start a Genie server locally.

By default, the frontend expects the backend to run at:

```text
http://localhost:8000
```

> [!IMPORTANT]
> Start the Julia backend before using the frontend. The frontend sends requests to `http://localhost:8000/simulations`.

---

## Frontend Setup

The main frontend is inside the `fe/` folder.

Open a second terminal:

```bash
cd Forest-Simulator_Case2/fe
```

Install dependencies:

```bash
npm install
```

Run the development server:

```bash
npm run dev
```

Open the local URL shown in the terminal, usually:

```text
http://localhost:5173
```

> [!TIP]
> Keep both servers running at the same time: the Julia backend and the Vite frontend.

---

## How to Run the Full Project

1. Start the backend:

```bash
cd Forest-Simulator_Case2
julia webapi.jl
```

2. Start the frontend in another terminal:

```bash
cd Forest-Simulator_Case2/fe
npm install
npm run dev
```

3. Open the frontend in your browser.

4. Configure the simulation parameters.

5. Click:

```text
Setup
```

6. Click:

```text
Start
```

7. Watch the fire spread through the forest grid.

8. Click:

```text
Stop
```

to stop the simulation and display the burnt tree progression graph.

---

## Simulation Model

The simulation uses an agent-based model where each tree is an agent placed on a 2D grid.

The tree states are defined as:

```julia
@enum TreeStatus green burning burnt
```

Each tree agent stores its current status:

```julia
@agent struct TreeAgent(GridAgent{2})
    status::TreeStatus = green
end
```

---

## Tree States

| State | Meaning | Visualization |
|---|---|---|
| `green` | Healthy tree that has not caught fire. | `greentree.svg` |
| `burning` | Tree currently on fire and able to spread fire. | `burningtree.svg` |
| `burnt` | Tree that has already burned and can no longer spread fire. | `burnttree.svg` |

> [!NOTE]
> A burning tree spreads fire to nearby green trees, then becomes burnt.

---

## Fire Spread Logic

During each simulation step:

1. The model checks each tree.
2. If a tree is burning, it looks for nearby tree agents.
3. Each nearby green tree has a chance of catching fire.
4. The spread probability is adjusted by wind direction and wind strength.
5. The original burning tree becomes burnt.

The main spread probability is controlled by:

```text
probability_of_spread
```

The frontend allows this value to be adjusted from:

```text
0% to 100%
```

---

## Wind System

The simulation includes two wind controls:

| Parameter | Description |
|---|---|
| `south_wind_speed` | Controls north-south wind influence. |
| `west_wind_speed` | Controls west-east wind influence. |

Wind modifies the chance that fire spreads in certain directions.

In the frontend, these are shown as:

```text
South-North Wind
West-East Wind
```

> [!IMPORTANT]
> Wind values can increase or decrease the probability of fire spreading depending on the relative direction between the burning tree and its neighbor.

---

## Big Jump Mode

The simulation includes an optional setting called:

```text
bigJumps
```

When enabled, strong wind values can cause sparks to jump farther away instead of only spreading to nearby trees.

This allows the fire to ignite trees farther from the original burning area.

> [!WARNING]
> Big Jump mode can make the fire spread less predictably because new burning trees may appear farther from the main fire front.

---

## Initial Fire Placement

When a simulation is created, trees are randomly placed across the grid based on the selected forest density.

The initial fire starts near the center of the grid.

The backend checks whether a tree position is near the center and sets that tree to:

```text
burning
```

This creates a starting fire zone from which the simulation spreads outward.

---

## Frontend Controls

The Vite frontend includes the following controls:

| Control | Description |
|---|---|
| `Setup` | Creates a new simulation with the selected parameters. |
| `Start` | Starts advancing the simulation automatically. |
| `Stop` | Stops the simulation and displays the burnt tree graph. |
| `Grid size` | Sets the dimensions of the square forest grid. |
| `Simulation speed` | Controls how quickly simulation steps are requested. |
| `Spread Probability` | Controls the base probability that fire spreads to nearby trees. |
| `Density` | Controls how many grid positions contain trees. |
| `South-North Wind` | Controls vertical wind influence. |
| `West-East Wind` | Controls horizontal wind influence. |
| `Big Jump` | Enables long-distance spark behavior. |

---

## Frontend Parameter Ranges

| Parameter | Range |
|---|---|
| Grid size | `10` to `40` |
| Simulation speed | `1` to `30` |
| Spread probability | `0%` to `100%` |
| Density | `10%` to `100%` |
| South-North wind | `-50` to `50` |
| West-East wind | `-50` to `50` |
| Big Jump | `true` or `false` |

---

## API Endpoints

The backend exposes two main endpoints.

### Create Simulation

```http
POST /simulations
```

Creates a new simulation instance.

Example request body:

```json
{
  "dim": [40, 40],
  "probability_of_spread": 100,
  "density": 0.8,
  "south_wind_speed": 0,
  "west_wind_speed": 0,
  "bigJumps": false
}
```

Example response structure:

```json
{
  "msg": "Hola",
  "Location": "/simulations/{id}",
  "trees": []
}
```

---

### Advance Simulation

```http
GET /simulations/{id}
```

Advances the selected simulation by one step and returns the updated tree states.

Example response structure:

```json
{
  "msg": "Adios",
  "trees": []
}
```

> [!NOTE]
> Each simulation is stored in memory using a generated UUID.

---

## Visualization

The frontend displays the simulation using an SVG grid.

Each tree is rendered as an image depending on its state:

```jsx
tree["status"] === "green" ? "./greentree.svg" :
tree["status"] === "burning" ? "./burningtree.svg" :
"./burnttree.svg"
```

The simulation also tracks:

- Current iteration count.
- Burnt tree percentage.
- Burnt tree progression over time.

When the simulation is stopped, a Plotly graph is generated using the recorded burnt tree percentage values.

---

## Example Workflow

1. Set grid size to:

```text
40
```

2. Set spread probability to:

```text
70
```

3. Set density to:

```text
80
```

4. Set wind values:

```text
South-North Wind: 10
West-East Wind: -5
```

5. Enable or disable:

```text
Big Jump
```

6. Click:

```text
Setup
```

7. Click:

```text
Start
```

8. Watch the fire spread.

9. Click:

```text
Stop
```

10. Review the burnt tree percentage graph.

---

## Alternative Frontend

The repository also includes a `frontend/` folder using **Next.js** and **Material UI**.

This version appears to be an earlier or alternate frontend implementation. It includes:

- Setup button.
- Start button.
- Stop button.
- Grid size slider.
- SVG tree visualization.

However, the `fe/` frontend contains more complete controls and visualization features.

> [!TIP]
> Use the `fe/` folder as the main frontend unless you specifically want to continue developing the Next.js version.

---

## Running the Next.js Frontend

To run the alternative frontend:

```bash
cd Forest-Simulator_Case2/frontend
npm install
npm run dev
```

Then open the local URL shown in the terminal, usually:

```text
http://localhost:3000
```

> [!WARNING]
> The Next.js frontend sends a simplified request body to the backend. Because the backend expects additional parameters, this version may require updates before working correctly with the current `webapi.jl`.

---

## Preview

### Screenshots
<img width="400" height="500" alt="imagen" src="https://github.com/user-attachments/assets/070f4954-0ea4-4c41-ab9e-fbb18c1b7853" />

---

## Recommended Development Workflow

For the current project version:

```text
Backend:  webapi.jl
Frontend: fe/
```

Use two terminals:

```text
Terminal 1: Julia backend
Terminal 2: Vite React frontend
```

Recommended commands:

```bash
# Terminal 1
cd Forest-Simulator_Case2
julia webapi.jl
```

```bash
# Terminal 2
cd Forest-Simulator_Case2/fe
npm install
npm run dev
```

---

## Educational Purpose

This project is useful for learning:

- Agent-based modeling.
- Forest fire spread simulation.
- Julia programming.
- Agents.jl.
- Web APIs with Genie.jl.
- React frontend development.
- Vite development workflow.
- SVG-based visualization.
- Simulation parameters.
- Probabilistic modeling.
- State-based systems.
- Client-server architecture.
- Basic environmental modeling.

> [!NOTE]
> The project connects scientific simulation logic with web visualization, making it useful for both programming and modeling practice.

---

## Troubleshooting

### The frontend does not show any trees

Make sure the backend is running:

```bash
julia webapi.jl
```

The frontend expects the API at:

```text
http://localhost:8000
```

---

### The browser console shows a connection error

Check that:

- The Julia server is running.
- The backend is running on port `8000`.
- You clicked `Setup` before clicking `Start`.
- CORS is enabled in `webapi.jl`.

---

### The simulation does not start

Click:

```text
Setup
```

before clicking:

```text
Start
```

The `Setup` button creates a simulation instance and stores its location.

---

### The simulation stops immediately

This can happen if there are no burning trees left.

Try increasing:

- Forest density
- Spread probability
- Grid size

---

### Julia cannot find a package

Install the required Julia packages:

```julia
using Pkg

Pkg.add("Agents")
Pkg.add("Distributions")
Pkg.add("Genie")
Pkg.add("HTTP")
```

---

### npm install fails

Make sure Node.js and npm are installed:

```bash
node --version
npm --version
```

Then try again:

```bash
npm install
```

---

### Plotly graph does not update

The graph is generated when the simulation is stopped.

Click:

```text
Stop
```

after the simulation has been running for a few iterations.

---

## Possible Improvements

Future versions could include:

- Add a Julia `Project.toml` file for reproducible backend dependencies.
- Improve frontend styling.
- Add labels or colors for different tree states.
- Add downloadable simulation results.
- Add CSV export.
- Add multiple ignition points.
- Add humidity as a parameter.
- Add terrain slope as a parameter.
- Add firebreaks or water zones.
- Add deterministic random seeds.
- Add pause and reset controls.
- Add better error handling in the API.
- Add persistent simulation storage.
- Improve wind model clarity.
- Add legends for tree states.
- Add charts for green, burning, and burnt trees.
- Merge or remove the older Next.js frontend if it is no longer needed.

> [!TIP]
> A strong next improvement would be adding `Project.toml` and `Manifest.toml` for the Julia backend so other users can install the exact required package versions.

---

## License

This project is licensed under:

```text
CC0 1.0 Universal
```

This means the project has been dedicated to the public domain as much as legally possible.

> [!IMPORTANT]
> Even with CC0, verify whether any external assets, libraries, or dependencies used by the project have their own license requirements.

---

## Disclaimer

**Forest Fire Simulation** is an educational programming and modeling project.

It simplifies forest fire behavior into an agent-based grid model and does not account for many real-world wildfire factors.

Real wildfire behavior depends on many variables, including:

- Vegetation type
- Humidity
- Temperature
- Terrain slope
- Fuel moisture
- Wind turbulence
- Weather conditions
- Fire suppression activity

> [!CAUTION]
> Do not use this project for real emergency planning, evacuation decisions, wildfire forecasting, or environmental risk assessment.
