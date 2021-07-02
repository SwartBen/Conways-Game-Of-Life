<script>
/*
Rules 
1. Any live cell with two or three live neighbours survives.
2. Any dead cell with three live neighbours becomes a live cell.
3. All other live cells die in the next generation. Similarly, all other dead cells stay dead. 
*/

import { onMount } from "svelte";
const gridSize = 40;

let playing = false

function createGrid() {
	let gridVar = []
	for(var i=0;i<gridSize;i++) {
		gridVar[i] = []
		for(var j=0;j<gridSize;j++) {
			gridVar[i][j] = 0
		}
	}
return gridVar
}

let grids = createGrid();

function reset() {
	grids=createGrid()
	playing = false;
}

function updateGrid(x, y) {
	if(grids[x][y] === 0) grids[x][y] = 1
	else grids[x][y] = 0
}

//Game Loop
async function loop() {
	let i = 0;
	i++;
	let neighbours = createGrid();

	//calculate neighbours
	for(let rows=5; rows<grids.length-1; rows++) {
		for(let cols=5; cols<grids[0].length-1; cols++) {
			if (grids[rows - 1][cols + 1] === 1) neighbours[rows][cols]++ //top left
			if (grids[rows-1][cols] === 1) neighbours[rows][cols]++ //mid left
			if (grids[rows - 1][cols - 1] === 1) neighbours[rows][cols]++ //bottom left
			if (grids[rows][cols+1] === 1) neighbours[rows][cols]++ //middle top
			if (grids[rows][cols-1] === 1) neighbours[rows][cols]++ // middle bottom
			if (grids[rows + 1][cols + 1] === 1) neighbours[rows][cols]++ //top right
			if (grids[rows+1][cols] === 1) neighbours[rows][cols]++ //mid right
			if (grids[rows + 1][cols - 1] === 1) neighbours[rows][cols]++ //mid bottom
		}
	}

	//kill and revive squares
	for(let rows=5; rows<grids.length; rows++) {
		for(let cols=5; cols<grids[0].length; cols++) {	
			if(((grids[rows][cols] === 1) && (neighbours[rows][cols] < 2 || neighbours[rows][cols] > 3))) {
				grids[rows][cols] = 0
			} else if (rows<=5 || cols<=5 || rows>=gridSize-5 || cols>=gridSize-5) {
				grids[rows][cols] = 0
			} else if (grids[rows][cols] === 0 && neighbours[rows][cols] === 3) {
				grids[rows][cols] = 1
			} 
		}
	}
}

onMount(async () => {
	while(true) {
		if(playing) await loop()
		await new Promise(resolve => setTimeout(resolve, 200))
	}
})
</script>

<div id=app> 
	<h1>Conways Game of Life</h1>
	<button on:click={() => playing = !playing}>
		{#if !playing}
			Start Game
		{:else}
			Pause Game
		{/if}
	</button>

	<button on:click={reset}>
		Reset Game
	</button>

	<div class='grid'>
		{#each grids as row, x}
			<div id="rows"> 
				{#each grids[x] as gridItem, y}
					{#if x >= 10} {#if x <= 30} {#if y >= 10} {#if y <= 30}
						<div 
						class="gridItem" 
						class:alive="{gridItem}" 
						on:click = {e => updateGrid(x, y)}
						/>
					{/if} {/if} {/if} {/if}
				{/each}
			</div>
		{/each}
	</div>
</div>

<style>
#app {
	text-align: center;
}

h1 {
	color: rgba(81, 203, 238, 0.5);
	text-transform: uppercase;
	font-size: 4em;
	font-weight: bold;
}

@media (min-width: 640px) {
	#app {
		max-width: none;
	}
}

.gridItem {
	border-style: solid;
	border-width: 0.5px;
	border-color: black;
	border-style:dotted;
	width: 20px;
	height: 20px;	
}

#rows {
	display: flex;
  	align-items: center;
  	justify-content: center;
}

.gridItem:hover {
  	transition: all 0s;
  	background-color: rgba(81, 203, 238, 0.5);
	cursor: pointer;
}
	
.alive {
	background-color: rgba(81, 203, 238, 0.5);
  }

button {
	background-color: rgba(81, 203, 238, 0.5);
	color: white;
	cursor: pointer;
	border: none;
}
button:focus {
	background-color: rgba(81, 203, 238, 0.5);
}
button:hover {
	background-color: rgba(81, 203, 238, 0.3);
}
</style>