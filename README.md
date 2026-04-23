```python
import pygame, sys, time, os, random
import math

pygame.init()

WIDTH, HEIGHT = 1200, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Heart Floating - Smooth Version")

# Font
try:
    font_path = os.path.join(os.getcwd(), "DFVN-LePetitCochon.ttf")
    font = pygame.font.Font(font_path, 45)
except:
    font = pygame.font.SysFont("Arial", 45, bold=True)

# Lyrics
lyrics = [
    ("Just", 0.2, 0.2),
    ("Just", 0.2, 0.2),
    ("Just", 0.2, 0.2),
    ("I'm just , I'm just ah", 0.2, 0.8),
    ("Anh call để cho", 0.25, 0.3),
    ("Em nghe đôi lời", 0.25, 0.6),
    ("Anh đang ở nơi", 0.2, 0.3),
    ("không em không người", 0.22, 0.5),
    ("Mây và gió đang thay", 0.2, 0.6),
    ("Lời anh nhớ em nhớ luôn tiếng cười", 0.25, 0.6),
]

WHITE = (255, 250, 250)
SOFT_PINK = (255, 182, 193)

# Scanline
scanlines = pygame.Surface((WIDTH, HEIGHT), pygame.SRCALPHA)
for y in range(0, HEIGHT, 4):
    pygame.draw.line(scanlines, (255, 255, 255, 15), (0, y), (WIDTH, y))

# Hearts
hearts = []
for _ in range(40):
    hearts.append({
        'x': random.randint(0, WIDTH),
        'y': random.randint(HEIGHT, HEIGHT + 300),
        'speed': random.uniform(1, 3),
        'size': random.randint(10, 25),
        'alpha': random.randint(120, 255),
        'sin_offset': random.uniform(0, math.pi * 2)
    })

def draw_heart(surface, x, y, size, color):
    pos_list = [
        (x, y + size),
        (x - size, y - size//4),
        (x - size//2, y - size),
        (x, y - size//2),
        (x + size//2, y - size),
        (x + size, y - size//4)
    ]
    pygame.draw.polygon(surface, color, pos_list)

def wrap_text(words, font, max_width):
    lines = []
    current = []
    for w in words:
        test_line = " ".join(current + [w])
        if font.size(test_line)[0] <= max_width - 200:
            current.append(w)
        else:
            lines.append(current)
            current = [w]
    if current:
        lines.append(current)
    return lines

line_index = 0
word_index = 0
last_word_time = time.time()

clock = pygame.time.Clock()
running = True

while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    screen.fill((40, 20, 30))

    # Stars
    for _ in range(5):
        rx, ry = random.randint(0, WIDTH), random.randint(0, HEIGHT)
        pygame.draw.circle(screen, (255, 255, 255), (rx, ry), 1)

    # Hearts animation
    for h in hearts:
        h['y'] -= h['speed']

        # Lượn sóng
        h['x'] += math.sin(time.time() * 2 + h['sin_offset']) * 0.8

        # Fade
        h['alpha'] -= 0.6

        heart_surface = pygame.Surface((60, 60), pygame.SRCALPHA)
        color = (255, 182, 193, int(h['alpha']))
        draw_heart(heart_surface, 30, 30, h['size'], color)
        screen.blit(heart_surface, (h['x'], h['y']))

        # Reset
        if h['y'] < -50 or h['alpha'] <= 0:
            h['y'] = HEIGHT + 50
            h['x'] = random.randint(0, WIDTH)
            h['alpha'] = 255

    # Lyrics
    if line_index < len(lyrics):
        line, word_delay, line_delay = lyrics[line_index]
        words = line.split()

        if time.time() - last_word_time > word_delay and word_index < len(words):
            word_index += 1
            last_word_time = time.time()

        show_words = words[:word_index]
        wrapped = wrap_text(show_words, font, WIDTH * 0.7)

        y_start = HEIGHT // 2 - 20

        for row, line_words in enumerate(wrapped):
            text_line = " ".join(line_words)

            wobble = math.sin(time.time() * 5) * 3

            text_surface = font.render(text_line, True, WHITE)
            rect = text_surface.get_rect(center=(WIDTH // 2, y_start + row * 60 + wobble))

            shadow = font.render(text_line, True, (100, 50, 50))
            screen.blit(shadow, (rect.x + 2, rect.y + 2))
            screen.blit(text_surface, rect)

        if word_index == len(words):
            if time.time() - last_word_time > line_delay:
                line_index += 1
                word_index = 0
                last_word_time = time.time()

    screen.blit(scanlines, (0, 0))

    pygame.display.flip()
    clock.tick(60)

pygame.quit()
sys.exit()
```
