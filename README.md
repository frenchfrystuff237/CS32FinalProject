# CS32FinalProject
#Import Necessary Items
import pygame
import sys
import random
from pygame.locals import *

# Loading in Pygame
pygame.init()

# Establishing Constants
CELLS_SIZE = 25
GRID_WIDTH = 25
GRID_HEIGHT = 25
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
BLUE = (0, 0, 255)
RED = (255, 0, 0)
PURPLE = (138,43, 226)
FPS = 15

# Setting up Display
screen = pygame.display.set_mode((CELLS_SIZE * GRID_WIDTH, CELLS_SIZE * GRID_HEIGHT))
pygame.display.set_caption('Snake')
Time = pygame.time.Clock()
FONT = pygame.font.Font(None, 50)  # Add this line to set the font for displaying score

# Draw Score on Screen
def create_score(score):
    score_surface = FONT.render(f"Score: {score}", True, BLACK)
    score_rect = score_surface.get_rect()
    score_rect.topleft = (10, 10)
    screen.blit(score_surface, score_rect)
    
#Setting up Snake
def create_snake(snake_coords):
    for coord in snake_coords:
        pygame.draw.rect(screen, BLUE, (coord['x'] * CELLS_SIZE, coord['y'] * CELLS_SIZE, CELLS_SIZE, CELLS_SIZE))

#Setting up Apple
def create_apple(coord):
    pygame.draw.rect(screen, RED, (coord['x'] * CELLS_SIZE, coord['y'] * CELLS_SIZE, CELLS_SIZE, CELLS_SIZE))

#Setting up Grid
def create_grid():
    for x in range(0, GRID_WIDTH * CELLS_SIZE, CELLS_SIZE):
        pygame.draw.line(screen, WHITE, (x, 0), (x, GRID_HEIGHT * CELLS_SIZE))
    for y in range(0, GRID_HEIGHT * CELLS_SIZE, CELLS_SIZE):
        pygame.draw.line(screen, WHITE, (0, y), (GRID_WIDTH * CELLS_SIZE, y))

#Setting up Randomization for Apple Placement
def random_apple():
    return {'x': random.randint(0, GRID_WIDTH - 1), 'y': random.randint(0, GRID_HEIGHT - 1)}


def game_loop():
    snake_coords = [{'x': 5, 'y': 5}, {'x': 4, 'y': 5}, {'x': 3, 'y': 5}]
    Orientation = 'right'
    apple = random_apple()
    score = 0  # Initialize the score counter
    
    #Setting up Movement controls
    while True:
        for event in pygame.event.get():
            if event.type == QUIT:
                pygame.quit()
                sys.exit()
            elif event.type == KEYDOWN:
                if event.key == K_LEFT and Orientation != 'right':
                    Orientation = 'left'
                elif event.key == K_RIGHT and Orientation != 'left':
                    Orientation = 'right'
                elif event.key == K_UP and Orientation != 'down':
                    Orientation = 'up'
                elif event.key == K_DOWN and Orientation != 'up':
                    Orientation = 'down'

        if Orientation == 'left':
            location = {'x': snake_coords[0]['x'] - 1, 'y': snake_coords[0]['y']}
        elif Orientation == 'right':
            location = {'x': snake_coords[0]['x'] + 1, 'y': snake_coords[0]['y']}
        elif Orientation == 'up':
            location = {'x': snake_coords[0]['x'], 'y': snake_coords[0]['y'] - 1}
        elif Orientation == 'down':
            location = {'x': snake_coords[0]['x'], 'y': snake_coords[0]['y'] + 1}

        snake_coords.insert(0, location)

        if (snake_coords[0]['x'] == apple['x'] and snake_coords[0]['y'] == apple['y']):
            apple = random_apple()
            score += 1
        else:
            snake_coords.pop()

        #Loading Visuals and Running Functions
        screen.fill(PURPLE)
        create_snake(snake_coords)
        create_apple(apple)
        create_grid()
        create_score(score)
        pygame.display.update()

        # Checking for Wall Collision
        if (snake_coords[0]['x'] < 0 or snake_coords[0]['x'] >= GRID_WIDTH or
            snake_coords[0]['y'] < 0 or snake_coords[0]['y'] >= GRID_HEIGHT):
            return  # Game over

        # Checking for Self Collision
        for snake_part in snake_coords[1:]:
            if (snake_part['x'] == snake_coords[0]['x'] and
                snake_part['y'] == snake_coords[0]['y']):
                return  # Game over

        Time.tick(FPS)

def main():
    while True:
        game_loop()

if __name__ == '__main__':
    main()
