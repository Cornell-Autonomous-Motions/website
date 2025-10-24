<script lang="ts">
	import { onMount } from 'svelte';
	import * as PIXI from 'pixi.js';

	let canvas: HTMLCanvasElement;

	onMount(() => {
		// Read background color from CSS variable
		const bgColorStr = getComputedStyle(document.documentElement)
			.getPropertyValue('--color-bg-game')
			.trim();
		const BACKGROUND_COLOR = parseInt(bgColorStr.replace('#', ''), 16);
		
		// Configuration
		const CELL_SIZE = 15; // Larger cells for more visual presence
		const BIRTH_PROBABILITY = 0.1; // Low probability for subtle start
		const UPDATE_INTERVAL = 1250; // Slower updates (ms)
		const SLIDE_SPEED = 0.1; // Speed of slide transitions (higher = faster, closer to linear)
		const FADE_SPEED = 0.05; // Speed of fade out for dying cells
		const CORNER_RADIUS = 50; // Rounded corner radius
		const MOUSE_SPAWN_PROBABILITY = 0.5; // Probability of spawning cell near mouse
		const CELL_COLOR = 0xA2998B; // Cream/beige color
		const FIXED_CELL_COLOR = 0xe13737; // Red color for CAM text

		// Initialize PixiJS Application
		const app = new PIXI.Application();

		let cols: number;
		let rows: number;
		let currentGrid: boolean[][] = [];
		let nextGrid: boolean[][] = [];
		let fixedCells: boolean[][] = []; // Track which cells are fixed (CAM text)
		let cellGraphics: PIXI.Graphics[][] = [];
		let fixedCellGraphics: PIXI.Graphics[][] = []; // Graphics for fixed red cells
		let cellOffsetX: number[][] = []; // X offset from target position
		let cellOffsetY: number[][] = []; // Y offset from target position
		let targetOffsetX: number[][] = []; // Target X offset
		let targetOffsetY: number[][] = []; // Target Y offset
		let targetAlpha: number[][] = []; // Target alpha for fading
		let hoverHighlight: PIXI.Graphics; // Highlight for cell under mouse
		let isDragging = false; // Track if mouse is being dragged

		async function init() {
			const canvasHeight = window.innerHeight * 1.2;
			
			await app.init({
				canvas,
				backgroundColor: BACKGROUND_COLOR,
				width: window.innerWidth,
				height: canvasHeight,
				antialias: true,
				resolution: window.devicePixelRatio || 1
			});

			// Calculate grid dimensions based on extended canvas
			cols = Math.ceil(window.innerWidth / CELL_SIZE);
			rows = Math.ceil(canvasHeight / CELL_SIZE);

			// Initialize grids
			initializeGrids();
			
			// Run several iterations before displaying to avoid showing the ugly spawn frame
			for (let i = 0; i < 8; i++) {
				updateGameOfLife();
			}
			
			createCellGraphics();
			createCAMText();
			createArrow();
			
			// Create hover highlight (add it last so it's on top)
			hoverHighlight = new PIXI.Graphics();
			hoverHighlight.visible = false;
			hoverHighlight.zIndex = 1000; // Ensure it's on top
			app.stage.addChild(hoverHighlight);
			app.stage.sortableChildren = true;
			
			// Start the game loop
			let lastUpdate = Date.now();
			app.ticker.add(() => {
				// Always update slide transitions
				updateSlides();
				
				// Periodically update game state
				const now = Date.now();
				if (now - lastUpdate > UPDATE_INTERVAL) {
					updateGameOfLife();
					lastUpdate = now;
				}
			});
		}

		function initializeGrids() {
			currentGrid = [];
			nextGrid = [];
			fixedCells = [];
			cellOffsetX = [];
			cellOffsetY = [];
			targetOffsetX = [];
			targetOffsetY = [];
			targetAlpha = [];
			
			for (let i = 0; i < rows; i++) {
				currentGrid[i] = [];
				nextGrid[i] = [];
				fixedCells[i] = [];
				cellOffsetX[i] = [];
				cellOffsetY[i] = [];
				targetOffsetX[i] = [];
				targetOffsetY[i] = [];
				targetAlpha[i] = [];
				for (let j = 0; j < cols; j++) {
					// Random initialization with low probability for subtle effect
					currentGrid[i][j] = Math.random() < BIRTH_PROBABILITY;
					nextGrid[i][j] = false;
					fixedCells[i][j] = false;
					cellOffsetX[i][j] = 0;
					cellOffsetY[i][j] = 0;
					targetOffsetX[i][j] = 0;
					targetOffsetY[i][j] = 0;
					targetAlpha[i][j] = currentGrid[i][j] ? 1 : 0;
				}
			}
		}

		function createCellGraphics() {
			cellGraphics = [];
			fixedCellGraphics = [];
			
			for (let i = 0; i < rows; i++) {
				cellGraphics[i] = [];
				fixedCellGraphics[i] = [];
				for (let j = 0; j < cols; j++) {
					// Regular cell
					const cell = new PIXI.Graphics();
					const baseX = j * CELL_SIZE;
					const baseY = i * CELL_SIZE;
					cell.x = baseX;
					cell.y = baseY;
					cell.alpha = currentGrid[i][j] ? 1 : 0;
					cell.visible = currentGrid[i][j];
					cell.roundRect(0, 0, CELL_SIZE - 2, CELL_SIZE - 2, CORNER_RADIUS);
					cell.fill(CELL_COLOR);
					app.stage.addChild(cell);
					cellGraphics[i][j] = cell;
					
					// Fixed cell (for CAM text) - with glow/outline to make it pop
					const fixedCell = new PIXI.Graphics();
					fixedCell.x = baseX;
					fixedCell.y = baseY;
					
					// Draw a glow layer (larger, semi-transparent background)
					fixedCell.roundRect(-2, -2, CELL_SIZE + 2, CELL_SIZE + 2, CORNER_RADIUS + 1);
					fixedCell.fill({ color: FIXED_CELL_COLOR, alpha: 0.3 });
					
					// Draw the main cell on top
					fixedCell.roundRect(0, 0, CELL_SIZE - 2, CELL_SIZE - 2, CORNER_RADIUS);
					fixedCell.fill(FIXED_CELL_COLOR);
					
					fixedCell.visible = false;
					fixedCell.zIndex = 100; // Above regular cells
					
					app.stage.addChild(fixedCell);
					fixedCellGraphics[i][j] = fixedCell;
				}
			}
		}
		
		function createCAMText() {
			// Letter patterns (7 rows x 5 cols each)
			const letterC = [
				[0,1,1,1,0],
				[1,0,0,0,1],
				[1,0,0,0,0],
				[1,0,0,0,0],
				[1,0,0,0,0],
				[1,0,0,0,1],
				[0,1,1,1,0]
			];
			
			const letterA = [
				[0,1,1,1,0],
				[1,0,0,0,1],
				[1,0,0,0,1],
				[1,1,1,1,1],
				[1,0,0,0,1],
				[1,0,0,0,1],
				[1,0,0,0,1]
			];
			
			const letterM = [
				[1,0,0,0,1],
				[1,1,0,1,1],
				[1,0,1,0,1],
				[1,0,0,0,1],
				[1,0,0,0,1],
				[1,0,0,0,1],
				[1,0,0,0,1]
			];
			
			// Calculate center position based on viewport height, not total canvas height
			const viewportRows = Math.ceil(window.innerHeight / CELL_SIZE);
			const centerRow = Math.floor(viewportRows / 2) - 3; // Center vertically in viewport (7 tall / 2)
			const letterSpacing = 2; // Space between letters
			const totalWidth = 5 + letterSpacing + 5 + letterSpacing + 5; // 3 letters with spacing
			const centerCol = Math.floor(cols / 2) - Math.floor(totalWidth / 2);
			
			// Place letter C
			let currentCol = centerCol;
			for (let i = 0; i < 7; i++) {
				for (let j = 0; j < 5; j++) {
					if (letterC[i][j] === 1) {
						const row = centerRow + i;
						const col = currentCol + j;
						if (row >= 0 && row < rows && col >= 0 && col < cols) {
							fixedCells[row][col] = true;
							fixedCellGraphics[row][col].visible = true;
						}
					}
				}
			}
			
			// Place letter A
			currentCol += 5 + letterSpacing;
			for (let i = 0; i < 7; i++) {
				for (let j = 0; j < 5; j++) {
					if (letterA[i][j] === 1) {
						const row = centerRow + i;
						const col = currentCol + j;
						if (row >= 0 && row < rows && col >= 0 && col < cols) {
							fixedCells[row][col] = true;
							fixedCellGraphics[row][col].visible = true;
						}
					}
				}
			}
			
			// Place letter M
			currentCol += 5 + letterSpacing;
			for (let i = 0; i < 7; i++) {
				for (let j = 0; j < 5; j++) {
					if (letterM[i][j] === 1) {
						const row = centerRow + i;
						const col = currentCol + j;
						if (row >= 0 && row < rows && col >= 0 && col < cols) {
							fixedCells[row][col] = true;
							fixedCellGraphics[row][col].visible = true;
						}
					}
				}
			}
		}
		
		function createArrow() {
			// Down arrow pattern (9 rows x 7 cols)
			const downArrow = [
				[0,0,0,1,0,0,0],
				[0,0,0,1,0,0,0],
				[0,0,0,1,0,0,0],
				[0,1,0,1,0,1,0],
				[0,0,1,1,1,0,0],
				[0,0,0,1,0,0,0]
			];
			
			// Position arrow at bottom center of screen (based on viewport height)
			const viewportRows = Math.ceil(window.innerHeight / CELL_SIZE);
			const arrowRow = viewportRows - 12; // 12 cells from bottom of viewport
			const centerCol = Math.floor(cols / 2) - 3; // Center horizontally (7 wide / 2)
			
			// Place arrow
			for (let i = 0; i < downArrow.length; i++) {
				for (let j = 0; j < downArrow[i].length; j++) {
					if (downArrow[i][j] === 1) {
						const row = arrowRow + i;
						const col = centerCol + j;
						if (row >= 0 && row < rows && col >= 0 && col < cols) {
							fixedCells[row][col] = true;
							fixedCellGraphics[row][col].visible = true;
						}
					}
				}
			}
		}

		function countNeighbors(row: number, col: number): number {
			let count = 0;
			for (let i = -1; i <= 1; i++) {
				for (let j = -1; j <= 1; j++) {
					if (i === 0 && j === 0) continue;
					
					const newRow = (row + i + rows) % rows;
					const newCol = (col + j + cols) % cols;
					
					if (currentGrid[newRow][newCol]) {
						count++;
					}
				}
			}
			return count;
		}

		function getNeighborDirection(row: number, col: number): { dx: number; dy: number } {
			let sumX = 0;
			let sumY = 0;
			let count = 0;
			
			for (let i = -1; i <= 1; i++) {
				for (let j = -1; j <= 1; j++) {
					if (i === 0 && j === 0) continue;
					
					const newRow = (row + i + rows) % rows;
					const newCol = (col + j + cols) % cols;
					
					if (currentGrid[newRow][newCol]) {
						sumX += j;
						sumY += i;
						count++;
					}
				}
			}
			
			if (count > 0) {
				// Normalize the direction vector
				const magnitude = Math.sqrt(sumX * sumX + sumY * sumY);
				if (magnitude > 0) {
					return {
						dx: sumX / magnitude,
						dy: sumY / magnitude
					};
				}
			}
			return { dx: 0, dy: 0 };
		}

		function updateGameOfLife() {
			// Apply Conway's Game of Life rules
			for (let i = 0; i < rows; i++) {
				for (let j = 0; j < cols; j++) {
					// Skip fixed cells (CAM text)
					if (fixedCells[i][j]) {
						nextGrid[i][j] = false; // Fixed cells don't participate in Game of Life
						continue;
					}
					
					const neighbors = countNeighbors(i, j);
					const isAlive = currentGrid[i][j];
					
					if (isAlive) {
						// Cell survives with 2 or 3 neighbors
						nextGrid[i][j] = neighbors === 2 || neighbors === 3;
					} else {
						// Dead cell becomes alive with exactly 3 neighbors
						nextGrid[i][j] = neighbors === 3;
					}
					
					// Handle animations for state changes
					if (!isAlive && nextGrid[i][j]) {
						// Cell is being born - slide from neighbors
						const direction = getNeighborDirection(i, j);
						const slideDistance = CELL_SIZE * 0.8;
						cellOffsetX[i][j] = direction.dx * slideDistance;
						cellOffsetY[i][j] = direction.dy * slideDistance;
						targetOffsetX[i][j] = 0;
						targetOffsetY[i][j] = 0;
						targetAlpha[i][j] = 1;
					} else if (isAlive && !nextGrid[i][j]) {
						// Cell is dying - just fade out (no slide)
						targetAlpha[i][j] = 0;
					} else if (isAlive) {
						// Cell stays alive
						targetAlpha[i][j] = 1;
					}
				}
			}
			
			// Swap grids
			[currentGrid, nextGrid] = [nextGrid, currentGrid];
		}

		function updateSlides() {
			// Smoothly transition cell positions and alpha
			for (let i = 0; i < rows; i++) {
				for (let j = 0; j < cols; j++) {
					const cell = cellGraphics[i][j];
					const isAlive = currentGrid[i][j];
					const baseX = j * CELL_SIZE;
					const baseY = i * CELL_SIZE;
					
					// Update position if cell is sliding (birth animation) - exponential easing
					const targetX = targetOffsetX[i][j];
					const targetY = targetOffsetY[i][j];
					
					if (Math.abs(cellOffsetX[i][j] - targetX) > 0.5 || Math.abs(cellOffsetY[i][j] - targetY) > 0.5) {
						cellOffsetX[i][j] += (targetX - cellOffsetX[i][j]) * SLIDE_SPEED;
						cellOffsetY[i][j] += (targetY - cellOffsetY[i][j]) * SLIDE_SPEED;
					} else {
						cellOffsetX[i][j] = targetX;
						cellOffsetY[i][j] = targetY;
					}
					
					cell.x = baseX + cellOffsetX[i][j];
					cell.y = baseY + cellOffsetY[i][j];
					
					// Update alpha (for both birth and death)
					const targetAlphaVal = targetAlpha[i][j];
					if (Math.abs(cell.alpha - targetAlphaVal) > 0.01) {
						if (cell.alpha < targetAlphaVal) {
							cell.alpha = Math.min(cell.alpha + FADE_SPEED, targetAlphaVal);
						} else {
							cell.alpha = Math.max(cell.alpha - FADE_SPEED, targetAlphaVal);
						}
					} else {
						cell.alpha = targetAlphaVal;
					}
					
					// Update visibility
					cell.visible = cell.alpha > 0.01;
					
					// Clean up dead cells that have fully faded
					if (!isAlive && cell.alpha < 0.01) {
						cellOffsetX[i][j] = 0;
						cellOffsetY[i][j] = 0;
						targetOffsetX[i][j] = 0;
						targetOffsetY[i][j] = 0;
						cell.x = baseX;
						cell.y = baseY;
					}
				}
			}
		}

		// Handle window resize
		function handleResize() {
			const newCols = Math.ceil(app.screen.width / CELL_SIZE);
			const newRows = Math.ceil(app.screen.height / CELL_SIZE);
			
			if (newCols !== cols || newRows !== rows) {
				// Clear old graphics
				cellGraphics.flat().forEach(cell => cell.destroy());
				
				// Update dimensions
				cols = newCols;
				rows = newRows;
				
				// Reinitialize
				initializeGrids();
				createCellGraphics();
			}
		}

		// Handle mouse movement for interactive cell spawning and highlighting
		function handleMouseMove(event: MouseEvent) {
			const rect = canvas.getBoundingClientRect();
			const mouseX = event.clientX - rect.left;
			const mouseY = event.clientY - rect.top;
			
			const col = Math.floor(mouseX / CELL_SIZE);
			const row = Math.floor(mouseY / CELL_SIZE);
			
			if (row >= 0 && row < rows && col >= 0 && col < cols) {
				// Update hover highlight position with more visible outline
				hoverHighlight.clear();
				hoverHighlight.roundRect(0, 0, CELL_SIZE - 2, CELL_SIZE - 2, CORNER_RADIUS);
				hoverHighlight.stroke({ width: 2, color: CELL_COLOR, alpha: 0.8 });
				hoverHighlight.x = col * CELL_SIZE;
				hoverHighlight.y = row * CELL_SIZE;
				hoverHighlight.visible = true;
				hoverHighlight.zIndex = 1000;
				
				// If dragging, spawn circles continuously
				if (isDragging) {
					spawnCircleAt(col, row);
				}
				// Otherwise spawn cells with probability (but not on fixed cells)
				else if (Math.random() < MOUSE_SPAWN_PROBABILITY && !currentGrid[row][col] && !fixedCells[row][col]) {
					// Spawn a new cell with birth animation
					currentGrid[row][col] = true;
					const direction = getNeighborDirection(row, col);
					const slideDistance = CELL_SIZE * 0.8;
					cellOffsetX[row][col] = direction.dx * slideDistance;
					cellOffsetY[row][col] = direction.dy * slideDistance;
					targetOffsetX[row][col] = 0;
					targetOffsetY[row][col] = 0;
					targetAlpha[row][col] = 1;
					
					// Make cell visible immediately
					cellGraphics[row][col].visible = true;
				}
			} else {
				hoverHighlight.visible = false;
			}
		}
		
		// Spawn circle of cells at given position
		function spawnCircleAt(col: number, row: number) {
			if (row < 0 || row >= rows || col < 0 || col >= cols) return;
			
			const radius = 2; // Circle radius in cells
			
			// Spawn cells in a circular pattern
			for (let i = -radius; i <= radius; i++) {
				for (let j = -radius; j <= radius; j++) {
					const distance = Math.sqrt(i * i + j * j);
					
					// Only spawn if within circle radius
					if (distance <= radius) {
						const targetRow = (row + i + rows) % rows;
						const targetCol = (col + j + cols) % cols;
						
						// Don't spawn on fixed cells or cells that are already alive
						if (!fixedCells[targetRow][targetCol] && !currentGrid[targetRow][targetCol]) {
							currentGrid[targetRow][targetCol] = true;
							
							// Calculate direction from center of circle for animation
							const magnitude = Math.sqrt(i * i + j * j);
							const dx = magnitude > 0 ? i / magnitude : 0;
							const dy = magnitude > 0 ? j / magnitude : 0;
							
							const slideDistance = CELL_SIZE * 0.8;
							cellOffsetX[targetRow][targetCol] = -dx * slideDistance;
							cellOffsetY[targetRow][targetCol] = -dy * slideDistance;
							targetOffsetX[targetRow][targetCol] = 0;
							targetOffsetY[targetRow][targetCol] = 0;
							targetAlpha[targetRow][targetCol] = 1;
							
							cellGraphics[targetRow][targetCol].visible = true;
						}
					}
				}
			}
		}
		
		// Handle mouse down to start dragging
		function handleMouseDown(event: MouseEvent) {
			isDragging = true;
			const rect = canvas.getBoundingClientRect();
			const mouseX = event.clientX - rect.left;
			const mouseY = event.clientY - rect.top;
			
			const col = Math.floor(mouseX / CELL_SIZE);
			const row = Math.floor(mouseY / CELL_SIZE);
			
			spawnCircleAt(col, row);
		}
		
		// Handle mouse up to stop dragging
		function handleMouseUp() {
			isDragging = false;
		}
		
		// Handle mouse click to spawn a circle of cells
		function handleMouseClick(event: MouseEvent) {
			const rect = canvas.getBoundingClientRect();
			const mouseX = event.clientX - rect.left;
			const mouseY = event.clientY - rect.top;
			
			const col = Math.floor(mouseX / CELL_SIZE);
			const row = Math.floor(mouseY / CELL_SIZE);
			
			if (row >= 0 && row < rows && col >= 0 && col < cols) {
				const radius = 2; // Circle radius in cells
				
				// Spawn cells in a circular pattern
				for (let i = -radius; i <= radius; i++) {
					for (let j = -radius; j <= radius; j++) {
						const distance = Math.sqrt(i * i + j * j);
						
						// Only spawn if within circle radius
						if (distance <= radius) {
							const targetRow = (row + i + rows) % rows;
							const targetCol = (col + j + cols) % cols;
							
							// Don't spawn on fixed cells or cells that are already alive
							if (!fixedCells[targetRow][targetCol] && !currentGrid[targetRow][targetCol]) {
								currentGrid[targetRow][targetCol] = true;
								
								// Calculate direction from center of circle for animation
								const magnitude = Math.sqrt(i * i + j * j);
								const dx = magnitude > 0 ? i / magnitude : 0;
								const dy = magnitude > 0 ? j / magnitude : 0;
								
								const slideDistance = CELL_SIZE * 0.8;
								cellOffsetX[targetRow][targetCol] = -dx * slideDistance;
								cellOffsetY[targetRow][targetCol] = -dy * slideDistance;
								targetOffsetX[targetRow][targetCol] = 0;
								targetOffsetY[targetRow][targetCol] = 0;
								targetAlpha[targetRow][targetCol] = 1;
								
								cellGraphics[targetRow][targetCol].visible = true;
							}
						}
					}
				}
			}
		}
		
		function handleMouseLeave() {
			hoverHighlight.visible = false;
		}

		window.addEventListener('resize', handleResize);
		canvas.addEventListener('mousedown', handleMouseDown);
		canvas.addEventListener('mouseup', handleMouseUp);
		canvas.addEventListener('mousemove', handleMouseMove);
		canvas.addEventListener('click', handleMouseClick);
		canvas.addEventListener('mouseleave', handleMouseLeave);

		init();

		return () => {
			window.removeEventListener('resize', handleResize);
			canvas.removeEventListener('mousedown', handleMouseDown);
			canvas.removeEventListener('mouseup', handleMouseUp);
			canvas.removeEventListener('mousemove', handleMouseMove);
			canvas.removeEventListener('click', handleMouseClick);
			canvas.removeEventListener('mouseleave', handleMouseLeave);
			app.destroy(true, { children: true });
		};
	});
</script>

<div class="canvas-container">
	<canvas bind:this={canvas} class="game-of-life-canvas"></canvas>
	<div class="fade-overlay"></div>
</div>

<style>
	.canvas-container {
		position: absolute;
		top: 0;
		left: 0;
		width: 100vw;
		height: 120vh;
		z-index: 0;
	}

	.game-of-life-canvas {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		pointer-events: all;
	}

	.fade-overlay {
		position: absolute;
		bottom: 0;
		left: 0;
		width: 100%;
		height: 50vh;
		background: linear-gradient(to bottom, 
			transparent 0%, 
			rgb(from var(--color-bg-game) r g b / 0.3) 40%,
			rgb(from var(--color-bg-game) r g b / 0.7) 70%,
			var(--color-bg-game) 100%);
		pointer-events: none;
		z-index: 1;
	}
</style>
