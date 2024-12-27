# Mat
import pygame
import random

# Инициализация pygame
pygame.init()

# Размеры окна
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Звездолет против комет")

# Цвета
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Частота кадров
FPS = 60

# Классы
class Spaceship:
    def __init__(self):
        self.image = pygame.Surface((50, 50))
        self.image.fill(BLUE)
        self.rect = self.image.get_rect(center=(WIDTH // 2, HEIGHT - 60))
        self.speed = 5

    def move(self, keys):
        if keys[pygame.K_LEFT] and self.rect.left > 0:
            self.rect.x -= self.speed
        if keys[pygame.K_RIGHT] and self.rect.right < WIDTH:
            self.rect.x += self.speed

    def draw(self):
        screen.blit(self.image, self.rect)

class Meteor:
    def __init__(self):
        self.image = pygame.Surface((40, 40))
        self.image.fill(RED)
        self.rect = self.image.get_rect(center=(random.randint(0, WIDTH), -20))
        self.speed = random.randint(3, 7)

    def move(self):
        self.rect.y += self.speed

    def draw(self):
        screen.blit(self.image, self.rect)

class Bullet:
    def __init__(self, x, y):
        self.image = pygame.Surface((10, 20))
        self.image.fill(WHITE)
        self.rect = self.image.get_rect(center=(x, y))
        self.speed = -10

    def move(self):
        self.rect.y += self.speed

    def draw(self):
        screen.blit(self.image, self.rect)

# Инициализация объектов
spaceship = Spaceship()
meteors = []
bullets = []

# Таймер для создания метеоров
meteor_timer = pygame.USEREVENT + 1
pygame.time.set_timer(meteor_timer, 1000)

# Игровой цикл
clock = pygame.time.Clock()
running = True
score = 0

while running:
    screen.fill(BLACK)

    # События
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            bullets.append(Bullet(spaceship.rect.centerx, spaceship.rect.top))
        if event.type == meteor_timer:
            meteors.append(Meteor())

    # Управление звездолетом
    keys = pygame.key.get_pressed()
    spaceship.move(keys)

    # Движение и проверка столкновений
    for meteor in meteors[:]:
        meteor.move()
        if meteor.rect.top > HEIGHT:
            meteors.remove(meteor)
        if meteor.rect.colliderect(spaceship.rect):
            print(f"Игра окончена! Ваш счет: {score}")
            running = False

    for bullet in bullets[:]:
        bullet.move()
        if bullet.rect.bottom < 0:
            bullets.remove(bullet)
        for meteor in meteors[:]:
            if bullet.rect.colliderect(meteor.rect):
                bullets.remove(bullet)
                meteors.remove(meteor)
                score += 1
                break

    # Отрисовка объектов
    spaceship.draw()
    for meteor in meteors:
        meteor.draw()
    for bullet in bullets:
        bullet.draw()

    # Отображение счета
    font = pygame.font.Font(None, 36)
    score_text = font.render(f"Счет: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))

    # Обновление экрана
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
