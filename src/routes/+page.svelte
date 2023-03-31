<script lang="ts">
	const rows: number = 6;
	const cols: number = 7;
	$: status = `${
		gameOver
			? currentPlayer === true
				? 'Red WINS! 🎉'
				: 'Yellow WINS! 🎉'
			: currentPlayer === true
			? "Red's turn"
			: "Yellow's turn"
	}`;
	let gameOver: boolean = false;
	let currentPlayer = true;
	let grid: any[][] = Array(rows)
		.fill(null)
		.map(() => Array(cols).fill(null));

	function dropDisk(column: number): void {
		if (!gameOver) {
			const emptyCell = findEmptyCellInColumn(grid, column);
			if (emptyCell !== null) {
				const { row, column } = emptyCell;
				grid[row][column] = currentPlayer;
				if (isGameOver()) {
					gameOver = true;
				} else {
					changePlayer();
				}
			}
		}
	}

	function findEmptyCellInColumn(
		grid: any[][],
		column: number
	): { row: number; column: number } | null {
		for (let row = grid.length - 1; row >= 0; row--) {
			if (grid[row][column] === null) {
				return { row, column };
			}
		}
		return null;
	}

	function changePlayer(): void {
		currentPlayer = !currentPlayer;
	}

	function isGameOver(): boolean {
		let gameOver = false;
		grid.forEach((row, rowIndex) => {
			row.forEach((col, colIndex) => {
				if (
					// horizontal
					(grid?.[rowIndex]?.[colIndex + 0] === currentPlayer &&
						grid?.[rowIndex]?.[colIndex + 1] === currentPlayer &&
						grid?.[rowIndex]?.[colIndex + 2] === currentPlayer &&
						grid?.[rowIndex]?.[colIndex + 3] === currentPlayer) ||
					// vertical
					(grid?.[rowIndex + 0]?.[colIndex] === currentPlayer &&
						grid?.[rowIndex + 1]?.[colIndex] === currentPlayer &&
						grid?.[rowIndex + 2]?.[colIndex] === currentPlayer &&
						grid?.[rowIndex + 3]?.[colIndex] === currentPlayer) ||
					// diagonal ascending
					(grid?.[rowIndex]?.[colIndex] === currentPlayer &&
						grid?.[rowIndex - 1]?.[colIndex + 1] === currentPlayer &&
						grid?.[rowIndex - 2]?.[colIndex + 2] === currentPlayer &&
						grid?.[rowIndex - 3]?.[colIndex + 3] === currentPlayer) ||
					// diagonal descending
					(grid?.[rowIndex]?.[colIndex] === currentPlayer &&
						grid?.[rowIndex + 1]?.[colIndex + 1] === currentPlayer &&
						grid?.[rowIndex + 2]?.[colIndex + 2] === currentPlayer &&
						grid?.[rowIndex + 3]?.[colIndex + 3] === currentPlayer)
				) {
					gameOver = true;
				}
			});
		});
		return gameOver;
	}

	function startNewGame(): void {
		gameOver = false;
		grid = Array(rows)
			.fill(null)
			.map(() => Array(cols).fill(null));
		currentPlayer = true;
	}
</script>

<main class="w-full p-6">
	<section class="flex flex-row justify-between items-center mb-6">
		<h1>{status}</h1>
		<button on:click={() => startNewGame()} class="border border-black rounded px-4 py-2">
			New game
		</button>
	</section>
	<section class="bg-blue-400 p-4">
		<div class="grid grid-cols-7 grid-rows-6 gap-4">
			{#each grid as row}
				{#each row as col, index}
					<button
						class="rounded-full bg-white w-8 h-8 {col === true
							? 'bg-red-500'
							: col === false
							? 'bg-yellow-400'
							: 'bg-white'}"
						on:click={() => dropDisk(index)}
					/>
				{/each}
			{/each}
		</div>
	</section>
</main>
