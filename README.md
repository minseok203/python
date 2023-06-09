import pygame
import random
import time

# 게임 초기화
pygame.init()

# 게임 화면 크기
screen_width = 600
screen_height = 400
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("1인용 핑퐁 게임 - 공민석")

# 색깔 정의
BLACK = (0, 0, 0)
WHITE = (0, 255, 230)

# 폰트 초기화
font = pygame.font.Font(None, 36)

# 패들 크기 정의
paddle_width = 70
paddle_height = 10

# 패들 위치 정의
paddle_x = screen_width // 2 - paddle_width // 2
paddle_y = screen_height - paddle_height - 50

# 패들 이동 속도 정의
paddle_speed = 3

# 공 크기 및 위치 초기화
ball_size = 12
ball_x = screen_width // 2
ball_y = screen_height // 2
ball_dx = random.choice([-2, 2])
ball_dy = random.choice([-2, 2])
ball_speed = 2

# 점수 초기화
score = 0

# 게임 기회(라이프) 초기화
lives = 3

# 게임 종료 플래그
game_over = False

# 게임 루프
clock = pygame.time.Clock()

while not game_over:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True

    if lives > 0:
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and paddle_x > 0:
            paddle_x -= paddle_speed
        if keys[pygame.K_RIGHT] and paddle_x < screen_width - paddle_width:
            paddle_x += paddle_speed
    
    # 공 이동
    ball_x += ball_dx * ball_speed
    ball_y += ball_dy * ball_speed

    # 공 벽 충돌 처리
    if ball_y < 0:
        ball_dy *= -1
    if ball_y > screen_height - ball_size:
        lives -= 1
        ball_speed += 1  # 속도 증가
        if lives == 0:
            game_over = True
        else:
            ball_x = screen_width // 2
            ball_y = screen_height // 2
            ball_dx = random.choice([-2, 2])
            ball_dy = random.choice([-2, 2])
    
    if ball_x < 0 or ball_x > screen_width - ball_size:
        ball_dx *= -1

    # 공 패들 충돌 처리
    if ball_x >= paddle_x and ball_x <= paddle_x + paddle_width and paddle_y < ball_y < paddle_y + paddle_height:
        ball_dy *= -1
        score += 10
    
    # 화면 그리기
    screen.fill(BLACK)
    pygame.draw.rect(screen, WHITE, (paddle_x, paddle_y, paddle_width, paddle_height))
    pygame.draw.circle(screen, WHITE, (ball_x, ball_y), ball_size)
    
    # 게임 기회 표시
    lives_text = font.render("Lives: " + str(lives), True, WHITE)
    lives_text_rect = lives_text.get_rect()
    lives_text_rect.topright = (100, 40)
    screen.blit(lives_text, lives_text_rect)

    # 점수 표시
    score_text = font.render("Score: " + str(score), True, WHITE)
    score_text_rect = score_text.get_rect()
    score_text_rect.topleft = (10, 10)
    screen.blit(score_text, score_text_rect)
    pygame.display.flip()

    clock.tick(60)
    

# 게임 종료 표시
game_over_text = font.render("Game Over", True, WHITE)
screen.blit(game_over_text, (screen_width // 2 - 80, screen_height // 2 - 20))
pygame.display.flip()

# 3초 대기
time.sleep(3)

# 게임 종료
pygame.quit()
