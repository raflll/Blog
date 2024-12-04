import pygame
import numpy as np
from typing import Tuple, List
import random


class GameOfLife:
    def __init__(self, width: int = 800, height: int = 600, cell_size: int = 10):
        """
        Initialize the Game of Life simulation.

        Args:
            width: Screen width in pixels
            height: Screen height in pixels
            cell_size: Size of each cell in pixels
        """
        # Initialize Pygame
        pygame.init()

        # Calculate grid dimensions
        self.cell_size = cell_size
        self.grid_width = width // cell_size
        self.grid_height = height // cell_size

        # Create the display surface
        self.screen = pygame.display.set_mode((width, height))
        pygame.display.set_caption("Conway's Game of Life")

        # Create the grid
        self.grid = np.random.choice([0, 1], size=(self.grid_height, self.grid_width), p=[0.85, 0.15])

        # Colors
        self.colors = {
            'background': (10, 10, 10),
            'grid': (40, 40, 40),
            'cell': (50, 255, 50),
            'text': (255, 255, 255)
        }

        # Initialize font for displaying statistics
        self.font = pygame.font.Font(None, 36)

        # Statistics
        self.generation = 0
        self.population = np.sum(self.grid)

        # Simulation state
        self.running = True
        self.paused = False

    def count_neighbors(self, row: int, col: int) -> int:
        """Count the number of live neighbors for a cell."""
        # Get the surrounding 3x3 region, handling wrap-around
        region = self.grid[
                 (row - 1) % self.grid_height:(row + 2) % self.grid_height,
                 (col - 1) % self.grid_width:(col + 2) % self.grid_width
                 ]
        # Sum all neighbors (subtract the cell itself if it's alive)
        return np.sum(region) - self.grid[row, col]

    def update(self) -> None:
        """Update the grid according to Conway's Game of Life rules."""
        if not self.paused:
            new_grid = self.grid.copy()

            # Calculate the number of neighbors for each cell
            for row in range(self.grid_height):
                for col in range(self.grid_width):
                    neighbors = self.count_neighbors(row, col)

                    # Apply Conway's rules
                    if self.grid[row, col] == 1:  # Live cell
                        if neighbors < 2 or neighbors > 3:
                            new_grid[row, col] = 0  # Cell dies
                    else:  # Dead cell
                        if neighbors == 3:
                            new_grid[row, col] = 1  # Cell becomes alive

            self.grid = new_grid
            self.generation += 1
            self.population = np.sum(self.grid)

    def draw(self) -> None:
        """Draw the current state of the grid."""
        self.screen.fill(self.colors['background'])

        # Draw cells
        for row in range(self.grid_height):
            for col in range(self.grid_width):
                if self.grid[row, col] == 1:
                    pygame.draw.rect(
                        self.screen,
                        self.colors['cell'],
                        (col * self.cell_size, row * self.cell_size, self.cell_size - 1, self.cell_size - 1)
                    )

        # Draw statistics
        stats_text = f"Generation: {self.generation} | Population: {self.population}"
        text_surface = self.font.render(stats_text, True, self.colors['text'])
        self.screen.blit(text_surface, (10, 10))

        # Draw pause indicator if paused
        if self.paused:
            pause_text = self.font.render("PAUSED", True, self.colors['text'])
            self.screen.blit(pause_text, (10, 50))

        pygame.display.flip()

    def handle_input(self) -> None:
        """Handle user input events."""
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                self.running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    self.paused = not self.paused
                elif event.key == pygame.K_r:
                    # Reset with random state
                    self.grid = np.random.choice([0, 1], size=(self.grid_height, self.grid_width), p=[0.85, 0.15])
                    self.generation = 0
            elif event.type == pygame.MOUSEBUTTONDOWN:
                # Toggle cells with mouse click
                x, y = pygame.mouse.get_pos()
                col = x // self.cell_size
                row = y // self.cell_size
                self.grid[row, col] = 1 - self.grid[row, col]

    def run(self) -> None:
        """Main game loop."""
        clock = pygame.time.Clock()

        while self.running:
            self.handle_input()
            self.update()
            self.draw()
            clock.tick(10)  # Limit to 10 frames per second

        pygame.quit()


if __name__ == "__main__":
    game = GameOfLife()
    game.run()
