from pygame import* 

from random import randint
font.init()

class GameSprite(sprite.Sprite):
    #конструктор класса
    def __init__(self, player_x, player_y, size_x, size_y, player_speed):
        #вызываем конструктор класса (Sprite):
        sprite.Sprite.__init__(self)
        #каждый спрайт должен хранить свойство image - изображение
        self.image = Surface((size_x, size_y))
        self.speed = player_speed
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y
        #каждый спрайт должен хранить свойство rect - прямоугольник, в который он вписан
    #метод, отрисовывающий героя на окне
    def reset(self):
        self.image.fill((0,0,0))
        window.blit(self.image, (self.rect.x, self.rect.y))
 
#класс главного игрока
class Player(GameSprite):
    #метод для управления спрайтом стрелками клавиатуры
    def update_L(self):
        keys = key.get_pressed()
        if keys[K_UP] and self.rect.y >= 0:
            self.rect.y -= self.speed
        if keys[K_DOWN] and self.rect.y <= 500:
            self.rect.y += self.speed

    def update_R(self):
        keys = key.get_pressed()
        if keys[K_w] and self.rect.y >= 0:
            self.rect.y -= self.speed
        if keys[K_s] and self.rect.y <= 500:
            self.rect.y += self.speed

class Ball(GameSprite):
    
    flag = 0
    speed_x = 3 
    speed_y = 3 

    def update(self, p1, p2):
        if self.flag == 0:
            global score_L, score_R
            self.rect.x += self.speed_x
            self.rect.y += self.speed_y
            if sprite.collide_rect(self, p1) or sprite.collide_rect(self,p2):
                self.speed_x *= -1
            if ball.rect.y <= 0 or ball.rect.y >= 650:
                self.speed_y *= -1

            if ball.rect.x >= 650:
                self.flag = 1
                score_L += 1
                self.rect.x = 250
                self.rect.y = 250
            
            if ball.rect.x <= 0:
                self.flag = 1
                score_R += 1
                self.rect.x = 250
                self.rect.y = 250
        else:
            self.speed_x = randint(-1,1)
            while self.speed_x == 0:
                self.speed_x = randint(-1,1)
            self.speed_x *= 3
            self.speed_y = randint(-1,1)
            while self.speed_y == 0:
                self.speed_y = randint(-1,1)
            self.speed_y *= 3
            self.flag = 0

        
    

f1 = font.Font(None, 36)

window = display.set_mode((700,700))

window.fill((0,255,255))

score_L = 0
score_R = 0

rocket_L = Player(0, 200, 50, 200, 5)
rocket_R = Player(650, 200, 50, 200, 5)
ball = Ball(200,200,50,50,5)

clock = time.Clock()

game = True

while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
    window.fill((0,255,255))
    rocket_L.reset()
    rocket_R.reset()
    rocket_L.update_L()
    rocket_R.update_R()
    ball.reset()
    ball.update(rocket_L,rocket_R)

    text1 = f1.render(str(score_L), True,(180, 0, 0))
    text2 = f1.render(str(score_R), False,(0, 180, 0))

    window.blit(text1, (100,100))
    window.blit(text2, (500,100))
    clock.tick(144)
    display.update()
