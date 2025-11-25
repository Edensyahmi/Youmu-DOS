Game Code
import random, time, os
import msvcrt

# --- SETTINGS ---
resolutions = {
    "1": {"name":"720p","rows":40,"cols":120},
    "2": {"name":"1080p","rows":54,"cols":160},  # 1080p proportional
    "3": {"name":"1440p","rows":60,"cols":200}
}

difficulties = {
    "1":   {"name":"Easy",       "spawn_chance":0.01, "spread":2},
    "1.5": {"name":"Easy+",      "spawn_chance":0.015,"spread":2},
    "2":   {"name":"Normal",     "spawn_chance":0.02, "spread":3},
    "2.5": {"name":"Normal+",    "spawn_chance":0.025,"spread":3},
    "3":   {"name":"Hard",       "spawn_chance":0.04, "spread":4},
    "3.5": {"name":"Hard+",      "spawn_chance":0.045,"spread":4},
    "4":   {"name":"Lunatic",    "spawn_chance":0.07, "spread":5},
    "4.5": {"name":"Lunatic+",   "spawn_chance":0.08, "spread":5}
}

save_slots = ["save1.txt", "save2.txt", "save3.txt"]

# --- COLORS ---
class Colors:
    RED = "\033[91m"
    YELLOW = "\033[93m"
    BLUE = "\033[94m"
    GREEN = "\033[92m"
    END = "\033[0m"

bullet_colors = [Colors.RED, Colors.YELLOW, Colors.BLUE, Colors.GREEN]

# --- MENUS ---
def main_menu():
    os.system("cls")
    print("ASCII Touhou — Ultimate Version")
    print("1. New Game")
    print("2. Load Game")
    while True:
        choice = input("Choice: ")
        if choice in ["1","2"]: break
    return choice

def select_resolution():
    os.system("cls")
    print("Select Resolution:")
    for k,v in resolutions.items(): print(f"{k}. {v['name']}")
    while True:
        res_choice = input("Enter choice (1-3): ")
        if res_choice in resolutions: break
    return resolutions[res_choice]

def select_difficulty():
    os.system("cls")
    print("Select Difficulty:")
    for k,v in difficulties.items(): print(f"{k}. {v['name']}")
    while True:
        diff_choice = input("Enter choice (1,1.5,2,2.5,3,3.5,4,4.5): ")
        if diff_choice in difficulties: break
    return difficulties[diff_choice]

def select_save_slot():
    os.system("cls")
    print("Select Save Slot:")
    for i, slot in enumerate(save_slots,1):
        print(f"{i}. {slot}")
    while True:
        choice = input("Enter 1-3: ")
        if choice in ["1","2","3"]: break
    return save_slots[int(choice)-1]

# --- DRAW FUNCTION ---
def draw(player, bullets, score, highscore, res, diff):
    print("\033[H", end="")
    grid = [[" "]*res['cols'] for _ in range(res['rows'])]
    grid[player[0]][player[1]] = "@"
    for r,c,color in bullets:
        if 0 <= r < res['rows'] and 0 <= c < res['cols']:
            grid[r][c] = f"{color}*{Colors.END}"
    print("\n".join("".join(r) for r in grid))
    print(f"Resolution: {res['name']}  Difficulty: {diff['name']}")
    print(f"Score: {score}  HighScore: {highscore}  Player:@ Bullets:*")
    print("Use WASD to move, Q to return to menu, E to save")

# --- GAME LOOP ---
def run_game(res, diff, savedata=None):
    rows, cols = res["rows"], res["cols"]
    player = [rows-2, cols//2]
    bullets = []
    score = 0
    highscore = 0

    # Load saved data
    if savedata:
        try:
            with open(savedata,"r") as f:
                data = f.read().split(",")
                player = [int(data[0]), int(data[1])]
                score = int(data[2])
                highscore = int(data[3])
            print("Loaded save!")
            time.sleep(0.5)
        except:
            print("Failed to load save. Starting new game.")
            time.sleep(0.5)

    # Story mode
    story = [
        "🌸 Reimu faces the mysterious youkai in the forest.",
        "🌙 Marisa shoots magical bullets to clear the path.",
        "🔥 Boss approaches! Prepare your dodging skills!",
        "🎆 Victory is near. Keep surviving!"
    ]
    story_index = 0
    story_timer = 0

    while True:
        if story_index < len(story):
            story_timer += 1
            if story_timer % 50 == 0:
                print(story[story_index])
                story_index += 1

        # Key detection
        if msvcrt.kbhit():
            key = msvcrt.getch().decode('utf-8').lower()
            if key == "w" and player[0] > 0: player[0]-=1
            if key == "s" and player[0] < rows-1: player[0]+=1
            if key == "a" and player[1] > 0: player[1]-=1
            if key == "d" and player[1] < cols-1: player[1]+=1
            if key == "q": return
            if key == "e":
                slot = select_save_slot()
                with open(slot,"w") as f:
                    f.write(f"{player[0]},{player[1]},{score},{highscore}")
                print(f"Game saved in {slot}")
                time.sleep(0.5)

        # Move bullets
        new_bullets=[]
        for r,c,color in bullets:
            r_new = r+1
            c_new = c+random.randint(-diff['spread'],diff['spread'])
            if 0<=r_new<rows: new_bullets.append([r_new,max(0,min(c_new,cols-1)),color])
        bullets = new_bullets

        # Spawn bullets
        for _ in range(int(cols*diff['spawn_chance'])):
            bullets.append([0, random.randint(0, cols-1), random.choice(bullet_colors)])

        # Collision detection
        hit=False
        for r,c,_ in bullets:
            if r==player[0] and c==player[1]: hit=True; break
        if hit:
            print("💀 You got hit! Respawning…")
            time.sleep(0.3)
            if score>highscore: highscore=score
            score=0; bullets=[]; player=[rows-2,cols//2]

        draw(player, bullets, score, highscore, res, diff)
        score += 1
        time.sleep(0.05)

# --- MAIN LOOP ---
while True:
    choice = main_menu()
    if choice == "1":
        res = select_resolution()
        diff = select_difficulty()
        run_game(res, diff)
    elif choice == "2":
        slot = select_save_slot()
        res = select_resolution()
        diff = select_difficulty()
        run_game(res, diff, slot)
