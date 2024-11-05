# AI Agent Simulation Environment

A minimal simulation environment that creates and tracks an AI agent with health management, simulation timing, and a structured UI. The simulation runs until the agent’s health reaches zero, with options to start and restart the simulation.

## Features

### Core Simulation

- **Start Menu**: Displays a "Start" button to initiate the simulation.
- **Agent Initialization**: Sets up an AI agent with a health property, positioned within a defined area on the screen.
- **Health Management**: The agent’s health decreases over time, simulating gradual health depletion.
- **Simulation Time Tracking**: Monitors elapsed time from the start of the simulation.
- **Status Bar**: A header bar shows real-time information on elapsed simulation time (in seconds) and the agent's current health.

### Simulation End Modal

- **End Condition**: Simulation ends when the agent’s health reaches zero.
- **Simulation Over Display**: A modal appears showing total simulation time upon completion.
- **Restart Option**: Provides a "Restart" button to initiate a new simulation.

## Setup and Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/your-username/your-repo-name.git
   ```
