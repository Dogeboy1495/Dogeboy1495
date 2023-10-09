
import pygame
import random

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
PLAYER_SIZE = 50
BULLET_SIZE = 10
ENEMY_SIZE = 40
PLAYER_SPEED = 5
BULLET_SPEED = 10
ENEMY_SPEED = 2
WHITE = (255, 255, 255)
RED = (255, 0, 0)

# Create the display
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Insane Gun Game")

# Initialize player and bullet positions
player_x = (WIDTH - PLAYER_SIZE) // 2
player_y = HEIGHT - PLAYER_SIZE
bullet_x = 0
bullet_y = 0
bullet_state = "ready"  # "ready" means the bullet is not on screen, "fire" means it's moving

# Initialize enemy
enemy_x = random.randint(0, WIDTH - ENEMY_SIZE)
enemy_y = random.randint(50, 150)

# Score
score = 0
font = pygame.font.Font(None, 36)

# Main game loop
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_x > 0:
        player_x -= PLAYER_SPEED
    if keys[pygame.K_RIGHT] and player_x < WIDTH - PLAYER_SIZE:
        player_x += PLAYER_SPEED

    # Fire a bullet
    if keys[pygame.K_SPACE] and bullet_state == "ready":
        bullet_x = player_x + PLAYER_SIZE // 2 - BULLET_SIZE // 2
        bullet_y = player_y
        bullet_state = "fire"

    # Update bullet position
    if bullet_state == "fire":
        bullet_y -= BULLET_SPEED
        if bullet_y < 0:
            bullet_state = "ready"

    # Update enemy position
    enemy_y += ENEMY_SPEED
    if enemy_y > HEIGHT:
        enemy_x = random.randint(0, WIDTH - ENEMY_SIZE)
        enemy_y = random.randint(50, 150)

    # Check for collisions between bullet and enemy
    if bullet_state == "fire" and (
        enemy_x < bullet_x < enemy_x + ENEMY_SIZE
        and enemy_y < bullet_y < enemy_y + ENEMY_SIZE
    ):
        bullet_state = "ready"
        score += 1
        enemy_x = random.randint(0, WIDTH - ENEMY_SIZE)
        enemy_y = random.randint(50, 150)

    # Draw everything
    screen.fill((0, 0, 0))
    pygame.draw.rect(screen, WHITE, (player_x, player_y, PLAYER_SIZE, PLAYER_SIZE))
    pygame.draw.rect(screen, RED, (bullet_x, bullet_y, BULLET_SIZE, BULLET_SIZE))
    pygame.draw.rect(screen, WHITE, (enemy_x, enemy_y, ENEMY_SIZE, ENEMY_SIZE))

    # Display score
    score_display = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_display, (10, 10))

    pygame.display.update()

# Quit the game
pygame.quit()
