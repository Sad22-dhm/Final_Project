# Final_Project
import pygame
import random

pygame.init()

LARGEUR = 800
HAUTEUR = 600

ecran = pygame.display.set_mode((LARGEUR, HAUTEUR))
pygame.display.set_caption("Banana Game")

BLANC = (255,255,255)
NOIR = (0,0,0)

font = pygame.font.SysFont(None, 40)
clock = pygame.time.Clock()


class GameSprite(pygame.sprite.Sprite):

    def __init__(self, x, y, w, h, image_path):
        super().__init__()

        self.image = pygame.image.load(image_path).convert_alpha()
        self.image = pygame.transform.scale(self.image, (w, h))

        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y

    def draw(self, surface):
        surface.blit(self.image, self.rect)


class Player(GameSprite):

    SPEED = 6

    def update(self):

        keys = pygame.key.get_pressed()

        if keys[pygame.K_UP]:
            self.rect.y -= self.SPEED

        if keys[pygame.K_DOWN]:
            self.rect.y += self.SPEED

        if self.rect.top < 0:
            self.rect.top = 0

        if self.rect.bottom > HAUTEUR:
            self.rect.bottom = HAUTEUR


class Item(GameSprite):

    def __init__(self, x, y, w, h, image_path, item_type):
        super().__init__(x, y, w, h, image_path)

        self.item_type = item_type
        self.speed = 5

    def update(self):
        self.rect.x -= self.speed


joueur = Player(50,250,80,80,"player.png")
image_gorille = pygame.image.load("player.png")
objets = []

SPAWN = pygame.USEREVENT
pygame.time.set_timer(SPAWN, 1000)

score = 0

running = True

while running:

    clock.tick(60)

    for event in pygame.event.get():

        if event.type == pygame.QUIT:
            running = False

        if event.type == SPAWN:

            if random.choice([True, False]):

                objets.append(Item(LARGEUR,random.randint(0, HAUTEUR - 60),60,60,"banana.png","banane"))

            else:

                objets.append(
                    Item(LARGEUR,random.randint(0, HAUTEUR - 60),60,60,"bomb.png","bombe"))

    joueur.update()

    for objet in objets[:]:

        objet.update()

        if joueur.rect.colliderect(objet.rect):

            if objet.item_type == "banane":
                score += 1
            else:
                score -= 3

            objets.remove(objet)

        elif objet.rect.right < 0:
            objets.remove(objet)

    ecran.fill(BLANC)

    joueur.draw(ecran)

    for objet in objets:
        objet.draw(ecran)

    texte = font.render(f"Score : {score}", True, NOIR)
    ecran.blit(texte, (10, 10))

    pygame.display.flip()

pygame.quit()

