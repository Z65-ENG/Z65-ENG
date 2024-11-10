import pygame
import random

# Pygame'i başlat
pygame.init()

# Ekran boyutları
WIDTH, HEIGHT = 400, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Fly Bird")

# Renkler
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)

# Kuş sınıfı
class Bird:
    def __init__(self):
        self.x = 50
        self.y = HEIGHT // 2
        self.width = 30
        self.height = 30
        self.gravity = 0.5
        self.velocity = 0

    def jump(self):
        self.velocity = -10

    def move(self):
        self.velocity += self.gravity
        self.y += self.velocity

    def draw(self, screen):
        pygame.draw.rect(screen, BLACK, (self.x, self.y, self.width, self.height))

# Engel sınıfı
class Pipe:
    def __init__(self):
        self.x = WIDTH
        self.gap = 150
        self.height = random.randint(50, 400)
        self.width = 50

    def move(self):
        self.x -= 5

    def draw(self, screen):
        pygame.draw.rect(screen, GREEN, (self.x, 0, self.width, self.height))  # Üst engel
        pygame.draw.rect(screen, GREEN, (self.x, self.height + self.gap, self.width, HEIGHT))  # Alt engel

    def collide(self, bird):
        if bird.x + bird.width > self.x and bird.x < self.x + self.width:
            if bird.y < self.height or bird.y + bird.height > self.height + self.gap:
                return True
        return False

# Oyun döngüsü
def main():
    clock = pygame.time.Clock()
    bird = Bird()
    pipes = [Pipe()]
    score = 0
    running = True

    while running:
        screen.fill(WHITE)
        bird.move()
        bird.draw(screen)

        if pipes[-1].x < WIDTH - 200:
            pipes.append(Pipe())

        for pipe in pipes:
            pipe.move()
            pipe.draw(screen)
            if pipe.collide(bird):
                running = False
            if pipe.x + pipe.width < 0:
                pipes.remove(pipe)
                score += 1

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    bird.jump()

        pygame.display.flip()
        clock.tick(30)

    pygame.quit()

if __name__ == "__main__":
    main()
