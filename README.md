                                        Game
Я создал игру "pygame window". Использовал при этом версию PyCharm 2022 1.3.
При создании игры использовал модуль pygame и random.
Сначала создал окно, потом мячик, которым управляешь с помощью мыши. 
Затем черные мячики: появляется один черный мячик, по истечении некоторого времени
будут появляться все больше мячиков. Черные мячики отталкиваются от стенки
и катятся к следующей стенке. С самого начала игры прибавляются очки. Чем
больше держишься по времени, тем больше начисляется очков. Если один из черных
мячиков попадает в красный мячик, то игра заканчивается.
Цель игры: продержаться как можно дольше, не дав до тебя дотронуться черными мячиками.

![](../../OneDrive/Desktop/программирование/Скрин/1.jpg)


import pygame
from random import random
from pygame.locals import *

Задаем размер окна игры
resolution = (400,300)
Настраиваем цвета
white = (255,255,255)
black = (0,0,0)
red = (255,0,0)

screen = pygame.display.set_mode(resolution)

Создаем игровые объекты
class Ball:
    def __init__(self, xPos =  resolution[0] / 2, yPos = resolution[1] / 2, xVel = 1, yVel = 1, rad = 15):
        self.x = xPos
        self.y = yPos
        self.dx = xVel
        self.dy = yVel
        self.radius = rad
        self.type = "ball"

    def draw(self, surface):
        pygame.draw.circle(surface, black, (int(self.x), int(self.y)), self.radius)
Заставляем объект двигаться
    def update(self, gameObjects):
        self.x += self.dx
        self.y += self.dy
        if (self.x <= 0 or self.x >= resolution[0]):
            self.dx *= -1
        if (self.y <= 0 or self.y >= resolution[1]):
            self.dy *= -1
 создаем еще один круг, управляемый движением мыши.
class Player:
    def __init__(self, rad = 20):
        self.x = 0
        self.y = 0
        self.radius = rad
        self.type = "player"
        self.gameOver = False
Определяем отрисовку объекта игрока
    def draw(self, surface):
        pygame.draw.circle(surface, red, (self.x, self.y), self.radius)
Проверка столкновения игрока с шарами
    def update(self, gameObjects):
        cord = pygame.mouse.get_pos()
        self.x = cord[0]
        self.y = cord[1]
        for gameObj in gameObjects:
            if gameObj.type == "ball":
                if (gameObj.x - self.x)**2 + (gameObj.y - self.y)**2 <= (gameObj.radius + self.radius)**2:
                    self.gameOver = True
Создаем шар, его появление
class GameController:
    def __init__(self, interval = 5):
        self.inter = interval
        self.next = pygame.time.get_ticks() + (2 * 1000)
        self.type = "game controller"
        
        self.score = 0
        self.scoreText = pygame.font.Font(None, 12)
Частота обновления шаров, случайные скорости шаров
    def update(self, gameObjects):
        if self.next < pygame.time.get_ticks():
            self.next = pygame.time.get_ticks() + (self.inter * 1000)
            gameObjects.append(Ball(xVel=random()*2, yVel=random()*2))

        self.score += 1

    def draw(self, screen):
        screen.blit(self.scoreText.render(str(self.score), True, black), (5,5))  )Добавление счета)
Создаем новый экземпляр объекта и добавляем его в список gameObjects
class game():
    def __init__(self):
        pygame.init()
        
        self.screen = pygame.display.set_mode(resolution)
        self.clock = pygame.time.Clock()
        self.gameObjects = []
        self.gameObjects.append(GameController())
        self.gameObjects.append(Player())
        self.gameOver = False
Задаем конец игры при столкновении игрока с шаром
    def handleEvents(self):
        for event in pygame.event.get():
            if event.type == QUIT:
                pygame.quit()

    def run(self):
        while True:
            self.handleEvents()

            if not self.gameOver:
                for gameObj in self.gameObjects:
                    gameObj.update(self.gameObjects)
                    if gameObj.type == "player":
                        self.gameOver = gameObj.gameOver

            self.screen.fill(white)

            for gameObj in self.gameObjects:
                gameObj.draw(self.screen)

            self.clock.tick(60)
            pygame.display.flip()

game().run()