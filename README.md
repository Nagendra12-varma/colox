# colox

pip install pygame

import pygame
import sys
import random

# Initialize pygame
pygame.init()
res = (720, 720)

# Initialize mixer for audio
pygame.mixer.init()

# Load and play background music
pygame.mixer.music.load("C:\colox\padaiyappa_bgm.mp3")
pygame.mixer.music.play(-1)

# Set up display
screen = pygame.display.set_mode(res)
clock = pygame.time.Clock()

# Colors
red = (255, 0, 0)
blue = (0, 0, 255)
white = (255, 255, 255)
background_color = ('black')

# Font setup
smallfont = pygame.font.SysFont('Corbel', 35)

# Load high score
high_score = 0
try:
    with open("highscore.txt", "r") as file:
        high_score = int(file.read())
except FileNotFoundError:
    high_score = 0

# Game variables
lead_x = 40
lead_y = screen.get_height() / 2
speed = 60
score = 0
level = 1
enemy_size = 50
e_p = [screen.get_width(), random.randint(50, screen.get_height() - 50)]

def game_over():
    global score, high_score
    if score > high_score:
        high_score = score
        with open("highscore.txt", "w") as file:
            file.write(str(high_score))
    
    while True:
        screen.fill(background_color)
        game_over_text = smallfont.render('GAME OVER', True, white)
        score_text = smallfont.render(f'Score: {score}', True, white)
        high_score_text = smallfont.render(f'High Score: {high_score}', True, white)
        retry_text = smallfont.render('Press R to Retry', True, white)
        exit_text = smallfont.render('Press E to Exit', True, white)
        
        screen.blit(game_over_text, (screen.get_width() / 2 - 100, 250))
        screen.blit(score_text, (screen.get_width() / 2 - 100, 300))
        screen.blit(high_score_text, (screen.get_width() / 2 - 100, 350))
        screen.blit(retry_text, (screen.get_width() / 2 - 100, 400))
        screen.blit(exit_text, (screen.get_width() / 2 - 100, 450))
        
        pygame.display.update()
        
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_r:
                    main()
                if event.key == pygame.K_e:
                    pygame.quit()
                    sys.exit()

def main():
    global lead_y, score, e_p, level, speed
    score = 0
    level = 1
    lead_y = screen.get_height() / 2
    e_p = [screen.get_width(), random.randint(50, screen.get_height() - 50)]
    speed = 60
    running = True
    
    while running:
        screen.fill(background_color)
        keys = pygame.key.get_pressed()
        if keys[pygame.K_UP]:
            lead_y -= 10
        if keys[pygame.K_DOWN]:
            lead_y += 10

        # Enemy movement
        e_p[0] -= 10 + level
        if e_p[0] <= 0:
            e_p[0] = screen.get_width()
            e_p[1] = random.randint(50, screen.get_height() - 50)
            score += 1
            if score % 5 == 0:
                level += 1
                speed += 100  # Increase speed when level increases

        # Collision detection
        player_rect = pygame.Rect(lead_x, lead_y, 40, 40)
        enemy_rect = pygame.Rect(e_p[0], e_p[1], enemy_size, enemy_size)
        if player_rect.colliderect(enemy_rect):
            game_over()

        # Draw elements
        pygame.draw.rect(screen, red, enemy_rect)
        pygame.draw.rect(screen, blue, player_rect)
        score_display = smallfont.render(f'Score: {score}', True, white)
        high_score_display = smallfont.render(f'High Score: {high_score}', True, white)
        level_display = smallfont.render(f'Level: {level}', True, white)
        screen.blit(score_display, (10, 10))
        screen.blit(high_score_display, (10, 50))
        screen.blit(level_display, (10, 90))

        pygame.display.update()
        clock.tick(speed)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

def main_menu():
    while True:
        screen.fill(background_color)
        start_text = smallfont.render('Start', True, white)
        quit_text = smallfont.render('Quit', True, white)
        
        start_button = pygame.Rect(screen.get_width() / 2 - 50, 300, 100, 40)
        quit_button = pygame.Rect(screen.get_width() / 2 - 50, 400, 100, 40)
        pygame.draw.rect(screen, blue, start_button)
        pygame.draw.rect(screen, red, quit_button)
        screen.blit(start_text, (screen.get_width() / 2 - 30, 305))
        screen.blit(quit_text, (screen.get_width() / 2 - 30, 405))
        
        pygame.display.update()
        
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                if start_button.collidepoint(event.pos):
                    main()
                if quit_button.collidepoint(event.pos):
                    pygame.quit()
                    sys.exit()

main_menu()




