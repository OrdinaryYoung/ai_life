<script lang="ts">
	import { get } from 'svelte/store';

	// Agent class with position and health properties
	class Agent {
		private pos_x = 0;
		private pos_y = 0;
		private health = 100;

		constructor(pos_x: number, pos_Y: number) {
			this.pos_x = pos_x;
			this.pos_y = pos_Y;
			this.health = 100;
		}

		getCoordinate() {
			return { x: this.pos_x, y: this.pos_y };
		}

		getHealth() {
			return this.health;
		}

		setHealth(type: 'Increase' | 'Decrease' | 'Set', value: number = 1) {
			try {
				switch (type) {
					case 'Increase':
						if (this.health < 100) this.health = Math.min(this.health + value, 100);
						break;
					case 'Decrease':
						if (this.health > 0) this.health = Math.max(this.health - value, 0);
						break;
					case 'Set':
						if (value < 0 || value > 100) throw Error('Value out of bounds');
						this.health = value;
						break;
					default:
						throw Error('Invalid health modification type');
				}
			} catch (error) {
				console.error(error);
			}
		}

		setX(newX: number) {
			this.pos_x = newX;
		}

		setY(newY: number) {
			this.pos_y = newY;
		}
	}

	// Simulation states
	let simulationState: 'init' | 'running' | 'over' = $state('init');

	let currentTime = $state(0);
	let simulationStartedTime = $state(0);
	let simulationCurrentTime = $state(0);
	let agent: Agent | null = $state(null);

	// Reactive time variables
	let timeInterval: number;
	let simulationTime = $derived(simulationCurrentTime - simulationStartedTime);

	let dom_data = $state({
		simulaion: {
			time: 0
		},
		agent: {
			x: 0,
			y: 0,
			health: 0
		}
	});

	// Starts the simulation
	const onStart = () => {
		agent = new Agent(50, 75);
		simulationStartedTime = Date.now();
		simulationState = 'running';

		clearInterval(timeInterval); // Clear any previous interval
		timeInterval = setInterval(() => {
			currentTime = Date.now();
		}, 100); // Update every 100 ms

		// Initiilize the DOM
		const { x: newX, y: newY } = agent.getCoordinate();
		dom_data.agent.x = newX;
		dom_data.agent.y = newY;
		dom_data.agent.health = agent.getHealth();
	};

	$effect(() => {
		if (simulationState === 'running' && agent) {
			simulationCurrentTime = currentTime;

			// Decrease health
			if (Math.floor(simulationTime) % 500 && agent?.getHealth() > 0) {
				agent.setHealth('Decrease');
				dom_data.agent.health = agent.getHealth();
			}
			// End the simulation if agent's health reaches 0
			if (agent?.getHealth() === 0) {
				clearInterval(timeInterval);
				simulationState = 'over';
			}
		}
	});
</script>

{#if simulationState === 'init'}
	<div class="modal">
		<button class="start-btn" onclick={onStart}> Start </button>
	</div>
{/if}

{#if simulationState === 'over'}
	<div class="modal">
		<h1>Simulation Time: {Math.floor(simulationTime / 1000)} seconds</h1>
		<button class="start-btn" onclick={onStart}> Restart </button>
	</div>
{/if}

{#if simulationState === 'running'}
	<div class="simulation-info">
		<p>TIME: {Math.floor(simulationTime / 1000)} sec</p>
		<p>♡ {dom_data.agent.health}</p>
	</div>
	<div class="environment">
		<canvas class="agent" style="left: {dom_data.agent.x}px; top: {dom_data.agent.y}px;"></canvas>
	</div>
{/if}

<style lang="postcss">
	.modal {
		@apply absolute start-1/2 top-1/2 z-40 flex size-64 translate-x-[-50%] translate-y-[-50%] flex-col items-center justify-center rounded-3xl py-4 shadow-md;
	}
	.start-btn {
		@apply h-12 w-full px-4 py-2 text-2xl hover:bg-gray-100;
	}
	.simulation-info {
		@apply flex h-16 w-full items-center justify-between gap-16 overflow-x-auto bg-gray-100 px-4;
	}

	.simulation-info > p {
		@apply text-xl text-gray-800;
	}

	.environment {
		@apply relative h-[calc(100vh-100px)] w-full;
	}
	.agent {
		@apply absolute size-2 rounded-full bg-green-700;
		position: absolute;
	}
</style>
