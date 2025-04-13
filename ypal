from pygame import *
from random import randint

# Инициализация Pygame
init()

# Определяем размеры окна
win_width = 700
win_height = 500

# Подгружаем шрифты
font.init()
font1 = font.Font(None, 80)
win_text = font1.render('YOU WIN!', True, (255, 255, 255))
lose_text = font1.render('YOU LOSE!', True, (180, 0, 0))

font2 = font.Font(None, 36)
mixer.init()
mixer.music.load('space2.ogg')
mixer.music.play(-1)  # Зацикливаем музыку
fire_sound = mixer.Sound('fire2.ogg')
fire_sound.set_volume(0.1)

# Нам нужны такие картинки:
img_back = "galaxy1.jpg"  # фон игры
img_bullet = "bullet.png"  # пуля
img_hero = "rocket2.png"  # ракета
img_enemy = "ufo.png"  # враг
score = 0  # сбито кораблей
goal = 30  # столько кораблей нужно сбить для победы
lost = 0  # пропущено кораблей
max_lost = 5  # проиграли, если пропустили столько

# Класс-родитель для других спрайтов
class GameSprite(sprite.Sprite):
    def __init__(self, player_image, player_x, player_y, size_x, size_y, player_speed):
        sprite.Sprite.__init__(self)
        self.image = transform.scale(image.load(player_image), (size_x, size_y))
        self.speed = player_speed
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y

    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))

class Player(GameSprite):
    def __init__(self, player_image, player_x, player_y, size_x, size_y, player_speed):
        super().__init__(player_image, player_x, player_y, size_x, size_y, player_speed)
        self.health = 3  # Начальное количество здоровья

    def draw_health(self, surface):
        for i in range(self.health):
            draw.rect(surface, (255, 0, 0), (10 + i * 30, 10, 25, 5))

    def update(self):
        keys = key.get_pressed()
        if keys[K_LEFT] and self.rect.x > 5:
            self.rect.x -= self.speed
        if keys[K_RIGHT] and self.rect.x < win_width - 80:
            self.rect.x += self.speed

    def fire(self):
        bullet = Bullet(img_bullet, self.rect.centerx, self.rect.top, 15, 20, -15)
        bullets.add(bullet)

class Enemy(GameSprite):
    def update(self):
        self.rect.y += self.speed
        global lost
        if self.rect.y > win_height:
            self.rect.x += self.speed
#метод "выстрел"(используем место игрока, чтобы создать там пулю)
    def fire(self):
        bullet = Bullet(img_bullet, self.rect.centerx, self.rect.top, 15, 20, -15)
        bullets.add(bullet)
#класс спрайта-врага
class Enemy(GameSprite):
#движение врага
    def update(self):
        self.rect.y += self.speed
        global lost
#исчезает, если дойдёт до края экрана
        if self.rect.y > win_height:
            self.rect.x = randint(80, win_width - 80)
            self.rect.y = 0
            lost = lost + 1

#класс спрайта-пули
class Bullet(GameSprite):
# движение врага
    def update(self):
        self.rect.y += self.speed
# исчезает, если дойдет до края экрана
        if self.rect.y < 0:
            self.kill()

#создаём окошко
win_width = 700
win_height = 500
display.set_caption("Shooter")
window = display.set_mode((win_width, win_height))
background = transform.scale(image.load(img_back),(win_width, win_height) )
#создаём спрайты
ship = Player(img_hero, 5, win_height - 100, 80, 100, 10)
#создание группы спрайтов-врагов
monsters = sprite.Group()
for i in range(1, 6):
    monster = Enemy(img_enemy, randint(80, win_width - 80), -40, 80, 50, randint(1, 5) )
    monsters.add(monster)
bullets = sprite.Group()
#переменная "игра закончилась": как только там True, в основном цикле перестают работать спрайты
finish = False
#основной цикл игры:
run = True #флаг сбрасывается кнопкой закрытия окна
# Основной цикл игры:
while run:
    # Событие нажатия на кнопку Закрыть
    for e in event.get():
        if e.type == QUIT:
            run = False
        # Событие нажатия на пробел - спрайт стреляет
        elif e.type == KEYDOWN:
            if e.key == K_SPACE:
                fire_sound.play()
                ship.fire()

    # Сама игра: действия спрайтов, проверка правил игры, перерисовка
    if not finish:
        # Обновляем фон
        window.blit(background, (0, 0))

        # Производим движения спрайтов
        ship.update()
        monsters.update()
        bullets.update()

        # Обновляем их в новом местоположении при каждой итерации цикла
        ship.reset()
        monsters.draw(window)
        bullets.draw(window)

        # Проверка столкновения пули и монстров (и монстр, и пуля при касании исчезают)
        collides = sprite.groupcollide(monsters, bullets, True, True)
        for c in collides:
            score += 1
            monster = Enemy(img_enemy, randint(80, win_width - 80), -40, 80, 50, randint(1, 5))
            monsters.add(monster)

        # Проверка столкновения игрока с врагами
        if sprite.spritecollide(ship, monsters, False):
            ship.health -= 1  # Уменьшаем здоровье на 1
            if ship.health <= 0:  # Если здоровье равно 0, игра окончена
                finish = True
                window.blit(lose_text, (200, 200))

        # Проверка выигрыша: сколько очков набрали?
        if score > goal:
            finish = True
            window.blit(win_text, (200, 200))

        # Пишем текст на экране
        text = font2.render("Score: " + str(score), 1, (255, 255, 255))
        window.blit(text, (10, 20))

        text_lose = font2.render("Missed: " + str(lost), 1, (255, 255, 255))
        window.blit(text_lose, (10, 50))

        # Отображаем здоровье
        ship.draw_health(window)

        display.update()

    # Автоматический перезапуск игры
    else:
        finish = False
        score = 0
        lost = 0
        ship.health = 3  # Сброс здоровья игрока
        for b in bullets:
            b.kill()
        for m in monsters:
            m.kill()

        time.delay(3000)
        for i in range(1, 6):
            monster = Enemy(img_enemy, randint(80, win_width - 80), -40, 80, 50, randint(1, 5))
            monsters.add(monster)

    time.delay(50)
