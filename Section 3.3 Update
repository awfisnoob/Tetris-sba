##Here is the complete revised Pygame code refactored into OOS machine.


import pygame
import sys
import random

# ==========================================
# 1. INITIALIZATION & CONSTANTS
# ==========================================
pygame.init()

SCREEN_WIDTH = 600
SCREEN_HEIGHT = 800
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Tetris v0.5 - State Machine Edition")

clock = pygame.time.Clock()

BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
GRAY = (128, 128, 128)
CYAN = (0, 255, 255)
BLUE = (0, 0, 255)
ORANGE = (255, 165, 0)
YELLOW = (255, 255, 0)
GREEN = (0, 255, 0)
PURPLE = (128, 0, 128)
RED = (255, 0, 0)

GRID_COLS = 10
GRID_ROWS = 20
BLOCK_SIZE = 35

GRID_X = (SCREEN_WIDTH - (GRID_COLS * BLOCK_SIZE)) // 2
GRID_Y = (SCREEN_HEIGHT - (GRID_ROWS * BLOCK_SIZE)) // 2

SHAPES = {
    'I': [[1, 1, 1, 1]],
    'J': [[1, 0, 0], [1, 1, 1]],
    'L': [[0, 0, 1], [1, 1, 1]],
    'O': [[1, 1], [1, 1]],
    'S': [[0, 1, 1], [1, 1, 0]],
    'T': [[0, 1, 0], [1, 1, 1]],
    'Z': [[1, 1, 0], [0, 1, 1]]
}

SHAPE_COLORS = {
    'I': CYAN, 'J': BLUE, 'L': ORANGE,
    'O': YELLOW, 'S': GREEN, 'T': PURPLE, 'Z': RED
}

DIFFICULTY_SETTINGS = {
    "easy": {"starting_delay": 1000, "lines_per_level": 10},
    "medium": {"starting_delay": 700, "lines_per_level": 8},
    "hard": {"starting_delay": 450, "lines_per_level": 5}
}

LINE_SCORES = {1: 100, 2: 300, 3: 500, 4: 800}

font = pygame.font.Font(None, 32)
title_font = pygame.font.Font(None, 64)
hud_font = pygame.font.Font(None, 24)

# ==========================================
# 2. HELPER UI FUNCTIONS
# ==========================================
def draw_button(text, rectangle):
    pygame.draw.rect(screen, BLUE, rectangle)
    pygame.draw.rect(screen, WHITE, rectangle, 2)
    button_text = font.render(text, True, WHITE)
    text_rectangle = button_text.get_rect(center=rectangle.center)
    screen.blit(button_text, text_rectangle)

# ==========================================
# 3. SECTION 3.3 ARCHITECTURE: STATE MACHINE
# ==========================================

# --- SECTION 3.3.4: CENTRAL STATE MANAGER ---
class StateManager:
    def __init__(self):
        self.current_state = None

    def change_state(self, new_state):
        self.current_state = new_state

    def handle_event(self, event):
        if self.current_state:
            self.current_state.handle_event(event)

    def update(self, elapsed_time):
        if self.current_state:
            self.current_state.update(elapsed_time)

    def draw(self, screen):
        if self.current_state:
            self.current_state.draw(screen)

# Base State Class (Interface)
class State:
    def __init__(self, manager):
        self.manager = manager

    def handle_event(self, event):
        pass

    def update(self, elapsed_time):
        pass

    def draw(self, screen):
        pass

# --- SECTION 3.3.3: STATE CLASSES ---

class MainMenuState(State):
    def __init__(self, manager):
        super().__init__(manager)
        self.start_button = pygame.Rect(150, 250, 300, 60)
        self.timed_button = pygame.Rect(150, 330, 300, 60)
        self.exit_button = pygame.Rect(150, 410, 300, 60)
        self.rules_button = pygame.Rect(SCREEN_WIDTH - 50, 10, 40, 40)

    def handle_event(self, event):
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if self.start_button.collidepoint(event.pos):
                self.manager.change_state(DifficultyMenuState(self.manager, game_mode="classic"))
            elif self.timed_button.collidepoint(event.pos):
                self.manager.change_state(GameplayState(self.manager, game_mode="timed", difficulty="medium"))
            elif self.rules_button.collidepoint(event.pos):
                self.manager.change_state(RulesMenuState(self.manager))
            elif self.exit_button.collidepoint(event.pos):
                pygame.quit()
                sys.exit()

    def draw(self, screen):
        screen.fill(BLACK)
        title_text = title_font.render("TETRIS", True, CYAN)
        title_rect = title_text.get_rect(center=(SCREEN_WIDTH // 2, 120))
        screen.blit(title_text, title_rect)

        draw_button("Start Classic Mode", self.start_button)
        draw_button("Timed Challenge", self.timed_button)
        draw_button("Exit", self.exit_button)

        # Rules button
        pygame.draw.rect(screen, BLUE, self.rules_button)
        pygame.draw.rect(screen, WHITE, self.rules_button, 2)
        q_text = font.render("?", True, WHITE)
        screen.blit(q_text, q_text.get_rect(center=self.rules_button.center))


class DifficultyMenuState(State):
    def __init__(self, manager, game_mode):
        super().__init__(manager)
        self.game_mode = game_mode
        self.easy_button = pygame.Rect(150, 220, 300, 60)
        self.medium_button = pygame.Rect(150, 300, 300, 60)
        self.hard_button = pygame.Rect(150, 380, 300, 60)
        self.back_button = pygame.Rect(150, 460, 300, 60)

    def handle_event(self, event):
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if self.easy_button.collidepoint(event.pos):
                self.manager.change_state(GameplayState(self.manager, self.game_mode, "easy"))
            elif self.medium_button.collidepoint(event.pos):
                self.manager.change_state(GameplayState(self.manager, self.game_mode, "medium"))
            elif self.hard_button.collidepoint(event.pos):
                self.manager.change_state(GameplayState(self.manager, self.game_mode, "hard"))
            elif self.back_button.collidepoint(event.pos):
                self.manager.change_state(MainMenuState(self.manager))

    def draw(self, screen):
        screen.fill(BLACK)
        title_text = title_font.render("SELECT DIFFICULTY", True, WHITE)
        title_rect = title_text.get_rect(center=(SCREEN_WIDTH // 2, 120))
        screen.blit(title_text, title_rect)

        draw_button("Easy", self.easy_button)
        draw_button("Medium", self.medium_button)
        draw_button("Hard", self.hard_button)
        draw_button("Back", self.back_button)


class RulesMenuState(State):
    def __init__(self, manager):
        super().__init__(manager)
        self.back_from_rules_button = pygame.Rect(150, 600, 300, 60)
        self.rules_text_list = [
            "GAME RULES:",
            "- Move Left/Right: Left/Right Arrow keys",
            "- Rotate Piece: Up Arrow key",
            "- Soft Drop: Down Arrow key",
            "- Hard Drop: Spacebar",
            "- Pause Game: P key",
            "- Restart Game: R key",
            "- Main Menu: ESC key",
            "- Classic Mode: Play until the grid fills up",
            "- Timed Mode: Score as much as possible in 60s"
        ]

    def handle_event(self, event):
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if self.back_from_rules_button.collidepoint(event.pos):
                self.manager.change_state(MainMenuState(self.manager))

    def draw(self, screen):
        screen.fill(BLACK)
        title_text = title_font.render("RULES", True, WHITE)
        title_rect = title_text.get_rect(center=(SCREEN_WIDTH // 2, 80))
        screen.blit(title_text, title_rect)

        start_y = 160
        for line in self.rules_text_list:
            rule_surface = font.render(line, True, WHITE)
            screen.blit(rule_surface, (50, start_y))
            start_y += 45

        draw_button("Back", self.back_from_rules_button)


class GameplayState(State):
    def __init__(self, manager, game_mode, difficulty):
        super().__init__(manager)
        self.game_mode = game_mode
        self.difficulty = difficulty

        self.resume_button = pygame.Rect(150, 320, 300, 60)
        self.quit_to_menu_button = pygame.Rect(150, 400, 300, 60)
        self.pause_overlay = pygame.Surface((SCREEN_WIDTH, SCREEN_HEIGHT))
        self.pause_overlay.set_alpha(180)
        self.pause_overlay.fill(BLACK)

        self.game_over_overlay = pygame.Surface((SCREEN_WIDTH, SCREEN_HEIGHT))
        self.game_over_overlay.set_alpha(180)
        self.game_over_overlay.fill(BLACK)

        self.move_delay = 100
        self.last_move_time = 0

        self.restart_game()

    def restart_game(self):
        self.grid = [[None] * GRID_COLS for _ in range(GRID_ROWS)]
        self.score = 0
        self.lines_cleared = 0
        self.level = 1
        self.fall_timer = 0
        self.game_over = False
        self.paused = False
        self.piece_bag = []

        self.remaining_time = 60000 if self.game_mode == "timed" else 0
        self.fall_delay = DIFFICULTY_SETTINGS[self.difficulty]["starting_delay"]

        self.spawn_piece()

    def create_piece_bag(self):
        bag = list(SHAPES.keys())
        random.shuffle(bag)
        return bag

    def get_next_shape_name(self):
        if not self.piece_bag:
            self.piece_bag = self.create_piece_bag()
        return self.piece_bag.pop()

    def spawn_piece(self):
        shape_name = self.get_next_shape_name()
        self.current_shape = [row[:] for row in SHAPES[shape_name]]
        self.current_color = SHAPE_COLORS[shape_name]
        self.current_x = (GRID_COLS // 2 - len(self.current_shape[0]) // 2)
        self.current_y = 0

        if not self.is_valid_position(self.current_shape, self.current_x, self.current_y):
            self.game_over = True

    def is_valid_position(self, shape, x, y):
        for row in range(len(shape)):
            for col in range(len(shape[row])):
                if shape[row][col]:
                    grid_x = x + col
                    grid_y = y + row
                    if grid_x < 0 or grid_x >= GRID_COLS or grid_y < 0 or grid_y >= GRID_ROWS:
                        return False
                    if self.grid[grid_y][grid_x] is not None:
                        return False
        return True

    def move_piece(self, dx, dy):
        new_x = self.current_x + dx
        new_y = self.current_y + dy
        if self.is_valid_position(self.current_shape, new_x, new_y):
            self.current_x = new_x
            self.current_y = new_y
            return True
        return False

    def rotate_piece(self):
        rotated_shape = [list(row) for row in zip(*self.current_shape[::-1])]
        if self.is_valid_position(rotated_shape, self.current_x, self.current_y):
            self.current_shape = rotated_shape
        elif self.is_valid_position(rotated_shape, self.current_x - 1, self.current_y):
            self.current_shape = rotated_shape
            self.current_x -= 1
        elif self.is_valid_position(rotated_shape, self.current_x + 1, self.current_y):
            self.current_shape = rotated_shape
            self.current_x += 1

    def hard_drop(self):
        while self.is_valid_position(self.current_shape, self.current_x, self.current_y + 1):
            self.current_y += 1

    def lock_piece(self):
        for row in range(len(self.current_shape)):
            for col in range(len(self.current_shape[row])):
                if self.current_shape[row][col]:
                    grid_x = self.current_x + col
                    grid_y = self.current_y + row
                    if grid_y < 0:
                        self.game_over = True
                        return
                    if 0 <= grid_x < GRID_COLS and grid_y < GRID_ROWS:
                        self.grid[grid_y][grid_x] = self.current_color

    def clear_lines(self):
        remaining_rows = [row for row in self.grid if any(cell is None for cell in row)]
        number_of_lines = GRID_ROWS - len(remaining_rows)
        new_rows = [[None] * GRID_COLS for _ in range(number_of_lines)]
        self.grid = new_rows + remaining_rows

        self.lines_cleared += number_of_lines
        if number_of_lines in LINE_SCORES:
            self.score += LINE_SCORES[number_of_lines] * self.level
        self.update_difficulty()

    def update_difficulty(self):
        lines_per_level = DIFFICULTY_SETTINGS[self.difficulty]["lines_per_level"]
        starting_delay = DIFFICULTY_SETTINGS[self.difficulty]["starting_delay"]
        self.level = self.lines_cleared // lines_per_level + 1
        self.fall_delay = max(100, starting_delay - (self.level - 1) * 70)

    def handle_event(self, event):
        if self.paused and event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if self.resume_button.collidepoint(event.pos):
                self.paused = False
            elif self.quit_to_menu_button.collidepoint(event.pos):
                self.manager.change_state(MainMenuState(self.manager))

        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_ESCAPE:
                self.manager.change_state(MainMenuState(self.manager))
            elif event.key == pygame.K_r:
                self.restart_game()
            elif event.key == pygame.K_p:
                self.paused = not self.paused
            elif self.paused or self.game_over:
                return
            elif event.key == pygame.K_UP:
                self.rotate_piece()
            elif event.key == pygame.K_SPACE:
                self.hard_drop()

    def update(self, elapsed_time):
        if self.paused or self.game_over:
            return

        # Continuous Input Processing
        keys = pygame.key.get_pressed()
        self.last_move_time += elapsed_time

        if self.last_move_time >= self.move_delay:
            if keys[pygame.K_DOWN]:
                self.move_piece(0, 1)
                self.last_move_time = 0
            elif keys[pygame.K_LEFT]:
                self.move_piece(-1, 0)
                self.last_move_time = 0
            elif keys[pygame.K_RIGHT]:
                self.move_piece(1, 0)
                self.last_move_time = 0

        # Piece Falling Logic
        self.fall_timer += elapsed_time
        if self.fall_timer >= self.fall_delay:
            self.fall_timer = 0
            if not self.move_piece(0, 1):
                self.lock_piece()
                self.clear_lines()
                self.spawn_piece()

        # Timed Challenge Logic
        if self.game_mode == "timed":
            self.remaining_time -= elapsed_time
            if self.remaining_time <= 0:
                self.remaining_time = 0
                self.game_over = True

    def draw(self, screen):
        screen.fill(BLACK)

        # Draw Grid Blocks
        for row in range(GRID_ROWS):
            for col in range(GRID_COLS):
                cell_x = GRID_X + col * BLOCK_SIZE
                cell_y = GRID_Y + row * BLOCK_SIZE
                pygame.draw.rect(screen, GRAY, (cell_x, cell_y, BLOCK_SIZE, BLOCK_SIZE), 1)
                if self.grid[row][col] is not None:
                    pygame.draw.rect(screen, self.grid[row][col], (cell_x + 1, cell_y + 1, BLOCK_SIZE - 2, BLOCK_SIZE - 2))

        # Draw Current Active Piece
        if self.current_shape:
            for row in range(len(self.current_shape)):
                for col in range(len(self.current_shape[row])):
                    if self.current_shape[row][col]:
                        cell_x = GRID_X + (self.current_x + col) * BLOCK_SIZE
                        cell_y = GRID_Y + (self.current_y + row) * BLOCK_SIZE
                        pygame.draw.rect(screen, self.current_color, (cell_x + 1, cell_y + 1, BLOCK_SIZE - 2, BLOCK_SIZE - 2))

        # Draw HUD Text
        screen.blit(hud_font.render(f"Score: {self.score}", True, WHITE), (20, 20))
        screen.blit(hud_font.render(f"Level: {self.level}", True, WHITE), (20, 55))
        if self.game_mode == "timed":
            screen.blit(hud_font.render(f"Time: {self.remaining_time / 1000.0:.2f}", True, WHITE), (20, 90))

        # Draw Pause Screen Overlay
        if self.paused:
            screen.blit(self.pause_overlay, (0, 0))
            pause_title = title_font.render("PAUSED", True, YELLOW)
            screen.blit(pause_title, pause_title.get_rect(center=(SCREEN_WIDTH // 2, 220)))
            draw_button("Resume (P)", self.resume_button)
            draw_button("Main Menu", self.quit_to_menu_button)

        # Draw Game Over Overlay
        elif self.game_over:
            screen.blit(self.game_over_overlay, (0, 0))
            over_text = title_font.render("GAME OVER", True, RED)
            screen.blit(over_text, over_text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 - 40)))
            restart_text = font.render("Press 'R' to Restart", True, WHITE)
            screen.blit(restart_text, restart_text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 + 20)))
            menu_text = font.render("Press 'ESC' for Main Menu", True, GRAY)
            screen.blit(menu_text, menu_text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 + 60)))


# ==========================================
# 4. SECTION 3.3.4: REFACTORED MAIN GAME LOOP
# ==========================================
state_manager = StateManager()
state_manager.change_state(MainMenuState(state_manager))

running = True
while running:
    # 1. Event Delegation
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        state_manager.handle_event(event)

    # 2. Timing & Frame Tick
    elapsed_time = clock.tick(60)

    # 3. State Logic Delegation
    state_manager.update(elapsed_time)

    # 4. State Render Delegation
    state_manager.draw(screen)

    pygame.display.flip()

pygame.quit()
sys.exit()

