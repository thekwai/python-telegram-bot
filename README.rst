import pygame
import random
import math
import sys
from pygame.locals import *

# Initialize pygame
pygame.init()
pygame.font.init()

# Constants
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
REEL_WIDTH = 140
REEL_HEIGHT = 400
SYMBOL_SIZE = 120
REEL_COUNT = 3
SYMBOLS_PER_REEL = 5  # Visible symbols per reel
FPS = 60

# Colors
BACKGROUND = (30, 50, 70)
PANEL_BG = (20, 30, 45)
REEL_BG = (15, 25, 35)
REEL_BORDER = (70, 100, 140)
BUTTON_BG = (200, 60, 60)
BUTTON_HOVER = (230, 80, 80)
BUTTON_TEXT = (255, 255, 255)
TEXT_COLOR = (220, 220, 220)
WIN_COLOR = (255, 215, 0)
JACKPOT_COLOR = (255, 50, 50)
COIN_COLOR = (255, 204, 0)

# Symbol names and colors
SYMBOLS = [
    {"name": "CHERRY", "color": (255, 50, 50), "payout": 5},
    {"name": "LEMON", "color": (255, 255, 100), "payout": 10},
    {"name": "ORANGE", "color": (255, 150, 50), "payout": 15},
    {"name": "PLUM", "color": (180, 70, 200), "payout": 20},
    {"name": "GRAPE", "color": (150, 50, 200), "payout": 25},
    {"name": "WATERMELON", "color": (50, 200, 100), "payout": 30},
    {"name": "SEVEN", "color": (100, 200, 255), "payout": 100},
    {"name": "DIAMOND", "color": (100, 240, 255), "payout": 200},
]

# Create the screen
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Lucky Slots Casino")
clock = pygame.time.Clock()

# Fonts
title_font = pygame.font.SysFont("Arial", 48, bold=True)
font_large = pygame.font.SysFont("Arial", 36, bold=True)
font_medium = pygame.font.SysFont("Arial", 24)
font_small = pygame.font.SysFont("Arial", 18)

class SlotMachine:
    def __init__(self):
        self.balance = 1000
        self.bet = 10
        self.win_amount = 0
        self.spinning = False
        self.spin_time = 0
        self.reel_positions = [0, 0, 0]
        self.reel_speeds = [0, 0, 0]
        self.result = [0, 0, 0]
        self.win_lines = []
        self.jackpot = 5000
        self.spin_duration = 2000  # ms
        self.spin_start_time = 0
        
    def start_spin(self):
        if not self.spinning and self.balance >= self.bet:
            self.balance -= self.bet
            self.spinning = True
            self.spin_start_time = pygame.time.get_ticks()
            self.win_amount = 0
            self.win_lines = []
            
            # Set random speeds for each reel
            for i in range(REEL_COUNT):
                self.reel_speeds[i] = random.uniform(15, 25)
    
    def update(self, current_time):
        if not self.spinning:
            return
            
        elapsed = current_time - self.spin_start_time
        
        # Update reel positions
        for i in range(REEL_COUNT):
            # Slow down reels at different times
            if elapsed > 500 + i * 300:
                self.reel_speeds[i] = max(1, self.reel_speeds[i] * 0.96)
            
            self.reel_positions[i] = (self.reel_positions[i] + self.reel_speeds[i]) % len(SYMBOLS)
        
        # Check if spinning should stop
        if elapsed > self.spin_duration:
            self.spinning = False
            # Set final positions to integers
            for i in range(REEL_COUNT):
                self.reel_positions[i] = round(self.reel_positions[i])
                self.result[i] = int(self.reel_positions[i]) % len(SYMBOLS)
            
            # Check for wins
            self.check_win()
    
    def check_win(self):
        # Check center line
        if self.result[0] == self.result[1] == self.result[2]:
            self.win_amount = self.bet * SYMBOLS[self.result[0]]["payout"]
            self.balance += self.win_amount
            self.win_lines.append("center")
            
            # Jackpot for three diamonds
            if SYMBOLS[self.result[0]]["name"] == "DIAMOND":
                self.win_amount += self.jackpot
                self.balance += self.jackpot
    
    def increase_bet(self):
        if self.bet < 100:
            self.bet += 10
    
    def decrease_bet(self):
        if self.bet > 10:
            self.bet -= 10

def draw_symbol(surface, symbol_idx, x, y, size, highlight=False):
    symbol = SYMBOLS[symbol_idx]
    rect = pygame.Rect(x, y, size, size)
    
    # Draw symbol background
    pygame.draw.rect(surface, symbol["color"], rect, border_radius=10)
    
    # Draw highlight if needed
    if highlight:
        pygame.draw.rect(surface, (255, 255, 255, 100), rect, 3, border_radius=10)
    
    # Draw symbol name
    name = symbol["name"]
    if len(name) > 6:  # Shorten long names
        name = name[:4] + "."
    text = font_medium.render(name, True, (30, 30, 30))
    text_rect = text.get_rect(center=(x + size//2, y + size//2))
    surface.blit(text, text_rect)
    
    # Draw payout
    payout_text = font_small.render(f"x{symbol['payout']}", True, (40, 40, 40))
    payout_rect = payout_text.get_rect(center=(x + size//2, y + size - 15))
    surface.blit(payout_text, payout_rect)

def draw_reel(surface, reel_idx, position, x, y, width, height):
    # Draw reel background
    reel_rect = pygame.Rect(x, y, width, height)
    pygame.draw.rect(surface, REEL_BG, reel_rect, border_radius=10)
    pygame.draw.rect(surface, REEL_BORDER, reel_rect, 3, border_radius=10)
    
    # Calculate visible area
    visible_y = y + 20
    visible_height = height - 40
    
    # Draw symbols
    for i in range(-1, SYMBOLS_PER_REEL):
        symbol_idx = int(position + i) % len(SYMBOLS)
        symbol_y = visible_y + (i * visible_height / (SYMBOLS_PER_REEL-1)) - (position % 1) * (visible_height / (SYMBOLS_PER_REEL-1))
        
        # Only draw if visible
        if visible_y <= symbol_y <= visible_y + visible_height - SYMBOL_SIZE:
            highlight = not slot_machine.spinning and i == 2 and reel_idx < len(slot_machine.result)
            draw_symbol(surface, symbol_idx, x + (width - SYMBOL_SIZE)//2, symbol_y, SYMBOL_SIZE, highlight)

def draw_button(surface, rect, text, hover=False):
    color = BUTTON_HOVER if hover else BUTTON_BG
    pygame.draw.rect(surface, color, rect, border_radius=10)
    pygame.draw.rect(surface, (255, 255, 255, 30), rect, 2, border_radius=10)
    
    text_surf = font_medium.render(text, True, BUTTON_TEXT)
    text_rect = text_surf.get_rect(center=rect.center)
    surface.blit(text_surf, text_rect)

def draw_coin(surface, x, y, size):
    pygame.draw.circle(surface, COIN_COLOR, (x, y), size)
    pygame.draw.circle(surface, (200, 160, 50), (x, y), size, 2)
    
    # Draw coin details
    pygame.draw.circle(surface, (230, 190, 80), (x, y), size-5)
    text = font_small.render("$", True, (100, 70, 20))
    text_rect = text.get_rect(center=(x, y))
    surface.blit(text, text_rect)

# Create slot machine
slot_machine = SlotMachine()

# Main game loop
running = True
mouse_pos = (0, 0)

# Buttons
spin_button = pygame.Rect(SCREEN_WIDTH//2 - 80, 500, 160, 50)
bet_up_button = pygame.Rect(SCREEN_WIDTH//2 + 100, 450, 40, 30)
bet_down_button = pygame.Rect(SCREEN_WIDTH//2 - 140, 450, 40, 30)

while running:
    current_time = pygame.time.get_ticks()
    mouse_pos = pygame.mouse.get_pos()
    
    # Handle events
    for event in pygame.event.get():
        if event.type == QUIT:
            running = False
        
        if event.type == MOUSEBUTTONDOWN:
            if spin_button.collidepoint(mouse_pos) and not slot_machine.spinning:
                slot_machine.start_spin()
            elif bet_up_button.collidepoint(mouse_pos):
                slot_machine.increase_bet()
            elif bet_down_button.collidepoint(mouse_pos):
                slot_machine.decrease_bet()
    
    # Update slot machine
    slot_machine.update(current_time)
    
    # Draw background
    screen.fill(BACKGROUND)
    
    # Draw decorative elements
    for i in range(20):
        x = random.randint(0, SCREEN_WIDTH)
        y = random.randint(0, 100)
        size = random.randint(2, 5)
        pygame.draw.circle(screen, (100, 150, 200, 150), (x, y), size)
    
    # Draw title
    title_text = title_font.render("LUCKY SLOTS", True, (255, 255, 255))
    screen.blit(title_text, (SCREEN_WIDTH//2 - title_text.get_width()//2, 20))
    
    subtitle = font_medium.render("Spin to Win!", True, (200, 200, 255))
    screen.blit(subtitle, (SCREEN_WIDTH//2 - subtitle.get_width()//2, 75))
    
    # Draw jackpot
    pygame.draw.rect(screen, (80, 20, 30), (SCREEN_WIDTH//2 - 120, 110, 240, 40), border_radius=20)
    jackpot_text = font_medium.render(f"JACKPOT: ${slot_machine.jackpot}", True, WIN_COLOR)
    screen.blit(jackpot_text, (SCREEN_WIDTH//2 - jackpot_text.get_width()//2, 115))
    
    # Draw reels area
    reel_area = pygame.Rect(
        (SCREEN_WIDTH - REEL_WIDTH * REEL_COUNT - 40) // 2,
        170,
        REEL_WIDTH * REEL_COUNT + 40,
        REEL_HEIGHT + 40
    )
    pygame.draw.rect(screen, PANEL_BG, reel_area, border_radius=15)
    pygame.draw.rect(screen, (60, 90, 120), reel_area, 3, border_radius=15)
    
    # Draw win lines
    if "center" in slot_machine.win_lines:
        pygame.draw.line(
            screen, 
            WIN_COLOR, 
            (reel_area.left + 20, reel_area.centery),
            (reel_area.right - 20, reel_area.centery),
            3
        )
    
    # Draw reels
    reel_spacing = (reel_area.width - 40 - REEL_WIDTH * REEL_COUNT) // (REEL_COUNT - 1)
    for i in range(REEL_COUNT):
        reel_x = reel_area.left + 20 + i * (REEL_WIDTH + reel_spacing)
        reel_y = reel_area.top + 20
        draw_reel(screen, i, slot_machine.reel_positions[i], reel_x, reel_y, REEL_WIDTH, REEL_HEIGHT)
    
    # Draw player info panel
    info_panel = pygame.Rect(SCREEN_WIDTH//2 - 150, 450, 300, 140)
    pygame.draw.rect(screen, PANEL_BG, info_panel, border_radius=15)
    pygame.draw.rect(screen, (60, 90, 120), info_panel, 2, border_radius=15)
    
    # Draw balance
    balance_text = font_medium.render(f"BALANCE: ${slot_machine.balance}", True, TEXT_COLOR)
    screen.blit(balance_text, (SCREEN_WIDTH//2 - balance_text.get_width()//2, 470))
    
    # Draw bet
    bet_text = font_medium.render(f"BET: ${slot_machine.bet}", True, TEXT_COLOR)
    screen.blit(bet_text, (SCREEN_WIDTH//2 - bet_text.get_width()//2, 510))
    
    # Draw bet buttons
    draw_button(screen, bet_up_button, "+", bet_up_button.collidepoint(mouse_pos))
    draw_button(screen, bet_down_button, "-", bet_down_button.collidepoint(mouse_pos))
    
    # Draw spin button
    spin_hover = spin_button.collidepoint(mouse_pos) and not slot_machine.spinning
    draw_button(screen, spin_button, "SPIN" if not slot_machine.spinning else "SPINNING...", spin_hover)
    
    # Draw win message
    if slot_machine.win_amount > 0:
        win_alpha = min(255, (current_time - slot_machine.spin_start_time - slot_machine.spin_duration) // 5)
        win_text = font_large.render(f"WIN: ${slot_machine.win_amount}!", True, WIN_COLOR)
        win_text.set_alpha(win_alpha)
        win_rect = win_text.get_rect(center=(SCREEN_WIDTH//2, 420))
        
        # Draw glow effect
        for i in range(5):
            radius = 100 + i*5
            alpha = 100 - i*20
            s = pygame.Surface((radius*2, radius*2), pygame.SRCALPHA)
            pygame.draw.circle(s, (*WIN_COLOR, alpha), (radius, radius), radius)
            screen.blit(s, (win_rect.centerx - radius, win_rect.centery - radius))
        
        screen.blit(win_text, win_rect)
        
        # Draw coins for big wins
        if slot_machine.win_amount > 100:
            for i in range(10):
                angle = (current_time / 50 + i * 36) % 360
                rad = angle * math.pi / 180
                distance = 50 + math.sin(current_time/200 + i) * 20
                x = win_rect.centerx + math.cos(rad) * distance
                y = win_rect.centery + math.sin(rad) * distance
                size = 10 + math.sin(current_time/150 + i) * 3
                draw_coin(screen, x, y, size)
    
    # Draw instructions
    help_text = font_small.render("Match symbols to win. Three DIAMONDS win the JACKPOT!", True, (180, 180, 200))
    screen.blit(help_text, (SCREEN_WIDTH//2 - help_text.get_width()//2, SCREEN_HEIGHT - 30))
    
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
sys.exit()