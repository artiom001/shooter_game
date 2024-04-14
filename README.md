from pygame import *
from random import randint
window = display.set_mode((700,500))

background = transform.scale(image.load('galaxy.jpg'),(700,500))

mixer.init()
mixer.music.load('space.ogg')
mixer.music.play()

score = 0
lost = 0
live = 3

font.init()
font1 = font.SysFont('Arial', 50)
font2 = font.SysFont('Arial', 36)
font3 = font.SysFont('Arial', 74)

class GameSprite(sprite.Sprite):
    def __init__(self, image_player, speed, x, y, width , height):
        super().__init__()
        self.image = transform.scale(image.load(image_player), (width,height))
        self.speed = speed
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y

    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))

class Player(GameSprite):
    def update(self):
        keys_pressed = key.get_pressed()
        if keys_pressed[K_a] and self.rect.x > 0:
            self.rect.x -= self.speed
        elif keys_pressed[K_d] and self.rect.x < 600:
            self.rect.x += self.speed

    def shoot(self):
        bullet = Bullet('bullet.png', speed = 6 , x = self.rect.centerx,
        y = self.rect.y , width = 15, height = 20)
        bullets.add(bullet)

class Enemy(GameSprite):
    
    def update(self):
        global lost
        self.rect.y += self.speed
        if self.rect.y > 500:
            self.rect.y = -20
            self.rect.x = randint(0,600)
            lost += 1


#boss = sprite.Group
#boss = Enemy(image_player = 'ufo.png', speed= 10, x=randint(0, 600), y=0, width = 200, height = 200)  
#boss.add(boss)

asteroids = sprite.Group()
for i in range(3):
    asteroid = Enemy(image_player='asteroid.png', speed = 4, x = randint(0, 600), y=0, width=100, height = 100)
    asteroids.add(asteroid)


class Bullet(GameSprite):

    def update(self):
        self.rect.y -= self.speed
        if self.rect.y < 0:
            self.kill()


rocket = Player(image_player = 'rocket.png', speed = 10, x=0, y=390, width = 70, height = 100)  
ufos = sprite.Group()

bullets = sprite.Group()
for i in range(5):
    ufo = Enemy(image_player = 'ufo.png', speed= randint(2, 8), x= randint (0,600), y=0, width = 80, height = 50)  
    ufos.add(ufo)

clock = time.Clock()
FPS = 60
game = True
finish = False
while game:
    for i in event.get():
        if i.type == QUIT:
            game = False
        if i.type == KEYDOWN:
            if i.key == K_SPACE:
                rocket.shoot()    
    if not finish:
        live_text = font1.render('LIVES:'+ str(live), True, (255,255,255))
        score_text = font2.render('Счёт:'+ str(score),True , (255,255,255))
        miss_text = font2.render('Пропущено:'+ str(lost), True,(255,255,255))
        lose_text = font3.render('GAME OVER',True , (0,255,0))
        window.blit(background, (0,0))
        window.blit(live_text, (525,40))
        window.blit(score_text, (50,30))
        window.blit(miss_text, (50,60))
        rocket.update()
        rocket.reset()
        ufos.update()
        ufos.draw(window)
        bullets.draw(window)
        bullets.update()
        asteroids.draw(window)
        asteroids.update()
        # boss.reset()
        # boss.update()
       # if sprite.spritecollide(rocket, ufos, True) or sprite.spritecollide(rocket, asteroids, True):
       #     live -= 1
       # if live == 3:
        #    live_text = font1.render('LIVES:', color = (0, 255, 0))
       # if live == 2:
           # live_text = font1.render('LIVES:', color = (180, 180, 255))
        #if live == 1:
            #live_text = font1.render('LIVES:', color = (255, 0, 0))
        if live <= 0:
            finish = True
            window.blit(lose_text, (250,250))
            lose_text = font3.render('GAME OVER',True , (255,255,0))


        if sprite.groupcollide(ufos, bullets, True , True):
            score += 1
            ufo = Enemy(image_player = 'ufo.png', speed= randint(1,4), x= randint (0,600), y=0, width = 80, height = 50)  
            ufos.add(ufo)
        # if score >= 10:
        #     for i in ufos:
        #         i.kill()
        #     boss.reset()
    

    clock.tick(FPS)
    display.update()
