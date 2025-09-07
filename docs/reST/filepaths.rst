import pygame
import sys

# Initialize
pygame.init()

# Screen setup
WIDTH, HEIGHT = 900, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Fighting Game")

# Colors
WHITE = (255, 255, 255)
RED = (200, 0, 0)
BLUE = (0, 0, 200)
GREEN = (0, 255, 0)
BLACK = (0, 0, 0)

# Player setup
player_width, player_height = 60, 120
player1 = pygame.Rect(100, HEIGHT - player_height, player_width, player_height)
player2 = pygame.Rect(740, HEIGHT - player_height, player_width, player_height)

# Game variables
speed = 6
punching1 = False
punching2 = False
health1 = 200
health2 = 200

clock = pygame.time.Clock()
font = pygame.font.Font(None, 50)


def draw_health_bar(x, y, health, color):
    pygame.draw.rect(screen, BLACK, (x, y, 200, 20))  # Border
    pygame.draw.rect(screen, color, (x, y, health, 20))  # Health fill


# Game loop
while True:
    screen.fill(WHITE)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

        # Punch controls
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_f:  # Player 1 punch
                punching1 = True
            if event.key == pygame.K_l:  # Player 2 punch
                punching2 = True

        if event.type == pygame.KEYUP:
            if event.key == pygame.K_f:
                punching1 = False
            if event.key == pygame.K_l:
                punching2 = False

    keys = pygame.key.get_pressed()

    # Player 1 movement (WASD)
    if keys[pygame.K_a] and player1.left > 0:
        player1.x -= speed
    if keys[pygame.K_d] and player1.right < WIDTH:
        player1.x += speed

    # Player 2 movement (Arrow keys)
    if keys[pygame.K_LEFT] and player2.left > 0:
        player2.x -= speed
    if keys[pygame.K_RIGHT] and player2.right < WIDTH:
        player2.x += speed

    # Punch effect
    if punching1:
        punch_rect = pygame.Rect(player1.x + player_width, player1.y + 40, 25, 25)
        pygame.draw.rect(screen, RED, punch_rect)
        if punch_rect.colliderect(player2):
            health2 -= 2

    if punching2:
        punch_rect = pygame.Rect(player2.x - 25, player2.y + 40, 25, 25)
        pygame.draw.rect(screen, BLUE, punch_rect)
        if punch_rect.colliderect(player1):
            health1 -= 2

    # Draw players
    pygame.draw.rect(screen, RED, player1)
    pygame.draw.rect(screen, BLUE, player2)

    # Draw health bars
    draw_health_bar(50, 30, health1, GREEN)
    draw_health_bar(WIDTH - 250, 30, health2, GREEN)

    # Check win condition
    if health1 <= 0:
        winner_text = font.render("PLAYER 2 WINS!", True, BLACK)
        screen.blit(winner_text, (WIDTH // 2 - 150, HEIGHT // 2))
        pygame.display.update()
        pygame.time.delay(3000)
        pygame.quit()
        sys.exit()

    if health2 <= 0:
        winner_text = font.render("PLAYER 1 WINS!", True, BLACK)
        screen.blit(winner_text, (WIDTH // 2 - 150, HEIGHT // 2))
        pygame.display.update()
        pygame.time.delay(3000)
        pygame.quit()
        sys.exit()

    pygame.display.update()
    clock.tick(60)
