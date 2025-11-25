import os
import sys
import time
import datetime
import threading
import json

# ============================
# OS INFO
# ============================
OS_NAME = "Youmu DOS"
OS_VERSION = "25Y5"

# ============================
# USERS
# ============================
users = {"Youmu Konpaku": "1"}
current_user = None

# ============================
# FILESYSTEM
# ============================
fs = {"root": {}}
current_path = ["root"]

# ============================
# NETWORKING (Wi-Fi only)
# ============================
wifi_networks = [
    {"ssid": "TouhouNet", "type": "6", "strength": 95},
    {"ssid": "Gensokyo-Guest", "type": "5", "strength": 70},
    {"ssid": "SpiritLink", "type": "6E", "strength": 40}
]
connected_ssid = None

# ============================
# YUAC (Youmu User Account Control)
# ============================
YUAC_ENABLED = True
def yuac_prompt(command_name):
    if YUAC_ENABLED:
        resp = input(f"YUAC: Are you sure you want to run '{command_name}'? (Y/N): ").strip().upper()
        return resp == "Y"
    return True

# ============================
# SAVE/LOAD
# ============================
SAVE_FILE = "youmu_dos_save.json"

def save_os():
    state = {
        "fs": fs,
        "users": users,
        "current_user": current_user,
        "current_path": current_path,
        "connected_ssid": connected_ssid,
        "YUAC_ENABLED": YUAC_ENABLED,
        "OS_NAME": OS_NAME,
        "RTTIME_ENABLED": RTTIME_ENABLED,
        "BGM_ENABLED": BGM_ENABLED,
        "OS_ACTIVATED": OS_ACTIVATED
    }
    with open(SAVE_FILE, "w") as f:
        json.dump(state, f)
    print("OS state saved 💀")

def load_os():
    global fs, users, current_user, current_path, connected_ssid
    global YUAC_ENABLED, OS_NAME, RTTIME_ENABLED, BGM_ENABLED, OS_ACTIVATED
    try:
        with open(SAVE_FILE, "r") as f:
            state = json.load(f)
        fs = state.get("fs", {"root": {}})
        users = state.get("users", {"Youmu Konpaku": "1"})
        current_user = state.get("current_user", None)
        current_path = state.get("current_path", ["root"])
        connected_ssid = state.get("connected_ssid", None)
        YUAC_ENABLED = state.get("YUAC_ENABLED", True)
        OS_NAME = state.get("OS_NAME", "Youmu DOS")
        RTTIME_ENABLED = state.get("RTTIME_ENABLED", False)
        BGM_ENABLED = state.get("BGM_ENABLED", False)
        OS_ACTIVATED = state.get("OS_ACTIVATED", False)
        print("OS state loaded 💀")
    except FileNotFoundError:
        print("No saved OS state found 💀")

# ============================
# PATH & DIRECTORY HELPERS
# ============================
def path_str():
    return "C:\\" + "\\".join(current_path)

def get_current_dir():
    d = fs
    for p in current_path:
        d = d[p]
    return d

# ============================
# BOOT SPLASH + REIMU ASCII + SNAKE
# ============================
def reimu_splash():
    splash = r"""
          ╔════════════════════════╗
          ║      Reimu Hakurei     ║
          ║   ASCII Splash Demo   ║
          ╚════════════════════════╝
              (\_._/)   
              ( o o )   
              /  V  \  
             /(  _  )\ 
              ^^   ^^
    """
    print(splash)
    time.sleep(2)

def snake_animation():
    frames = [
        r"""
         /^\/^\
       _|__|  O|
\/     /~     \_/ \
 \____|__________/  \
        \_______      \
                `\     \                 \
                  |     |                  \
                 /      /                    \
                /     /                       \
              /      /                         \ \
             /     /                            \  \
           /     /             _----_            \   \
          /     /           _-~      ~-_         |   |
         (      (        _-~    _--_    ~-_     _/   |
          \      ~-____-~    _-~    ~-_    ~-_-~    /
            ~-_           _-~          ~-_       _-~
               ~--______-~                ~-___-~
        """,
        r"""
         /^\/^\
       _|__|  O|
\/     /~     \_/ \
 \____|__________/  \
        \_______      \
                `\     \                 \
                  |     |                  \
                 /      /                    \
                /     /                       \
              /      /                         \ \
             /     /                            \  \
           /     /             _----_            \   \
          /     /           _-~      ~-_         |   |
         (      (        _-~    _--_    ~-_     _/   |
          \      ~-____-~    _-~    ~-_    ~-_-~    /
            ~-_           _-~          ~-_       _-~
               ~--______-~                ~-___-~
        """
    ]
    for i in range(3):
        for f in frames:
            os.system("cls" if os.name == "nt" else "clear")
            print(f)
            time.sleep(0.3)

def boot_screen():
    reimu_splash()
    snake_animation()
    print("====================================")
    print(f"       {OS_NAME} {OS_VERSION} 💀⚔️")
    print("====================================")
    time.sleep(1)
    print("Loading ghost blade driver... OK")
    time.sleep(0.5)
    print("Initializing half-spirit kernel... OK")
    print(f"Welcome to {OS_NAME} {OS_VERSION}\n")

# ============================
# LOGIN (username only)
# ============================
def login():
    global current_user
    print("LOGIN (no password needed)")
    while True:
        user = input("Username: ").strip()
        if user in users:
            current_user = user
            print(f"\nWelcome, {user}!\n")
            break
        else:
            print("User not found bro 💀")
            add = input("Create new user? (y/n): ").lower()
            if add == "y":
                users[user] = ""
                current_user = user
                print(f"\nUser '{user}' created.\n")
                break

# ============================
# PASSWORD & USER MANAGEMENT
# ============================
def change_pass():
    newpw = input("Enter NEW password: ")
    users[current_user] = newpw
    print("Password changed!\n")

def switch_user():
    print("\nSwitching user...\n")
    login()

# ============================
# DATE & TIME
# ============================
def show_date():
    print("Date:", datetime.date.today().strftime("%m/%d/%Y"))

def show_time():
    print("Time:", datetime.datetime.now().strftime("%H:%M:%S"))

def show_datetime():
    now = datetime.datetime.now()
    print("Date + Time:", now.strftime("%m/%d/%Y %H:%M:%S"))

# ============================
# RTTIME (Real-Time)
# ============================
RTTIME_ENABLED = False

def rttime_loop():
    while RTTIME_ENABLED:
        now = datetime.datetime.now().strftime("%H:%M:%S")
        print(f"\r[RTTIME] {now}", end="")
        time.sleep(1)
    print("\r", end="")

def toggle_rttime():
    global RTTIME_ENABLED
    if RTTIME_ENABLED:
        RTTIME_ENABLED = False
        print("\nRTTIME disabled 💀 (CPU freed)")
    else:
        print("⚠️ Warning: RTTIME uses PC performance even though it is small 💀")
        RTTIME_ENABLED = True
        t = threading.Thread(target=rttime_loop, daemon=True)
        t.start()

# ============================
# Quiet BGM placeholder
# ============================
BGM_ENABLED = False

def quiet_bgm():
    while BGM_ENABLED:
        time.sleep(1)

def toggle_bgm():
    global BGM_ENABLED
    if BGM_ENABLED:
        BGM_ENABLED = False
        print("Quiet BGM stopped 💀")
    else:
        BGM_ENABLED = True
        t = threading.Thread(target=quiet_bgm, daemon=True)
        t.start()
        print("Quiet BGM started 💀")

# ============================
# FILESYSTEM COMMANDS
# ============================
def dir_cmd():
    folder = get_current_dir()
    print("\nDirectory listing:\n")
    for name in folder:
        if isinstance(folder[name], dict):
            print(f"[DIR]  {name}")
        else:
            print(f"[FILE] {name}")
    print()

def cd_cmd(folder_name):
    folder = get_current_dir()
    if folder_name == "..":
        if len(current_path) > 1:
            current_path.pop()
        return
    if folder_name in folder and isinstance(folder[folder_name], dict):
        current_path.append(folder_name)
    else:
        print("Folder not found bro 💀")

def mkdir_cmd(name):
    folder = get_current_dir()
    if name not in folder:
        folder[name] = {}
        print(f"Folder '{name}' created 💀")
    else:
        print("Folder already exists bro 💀")

def notepad():
    folder = get_current_dir()
    filename = input("Enter filename: ")
    print("Type text. Type 'SAVE' to finish.")
    lines = []
    while True:
        t = input()
        if t == "SAVE":
            break
        lines.append(t)
    folder[filename] = "\n".join(lines)
    print("File saved!\n")

def open_file():
    folder = get_current_dir()
    fname = input("Enter filename: ")
    if fname in folder and isinstance(folder[fname], str):
        print("\n--- FILE CONTENT ---")
        print(folder[fname])
        print("--------------------\n")
    else:
        print("File not found bro 💀")

# ============================
# FAKE APP
# ============================
def fake_app():
    print("Launching app...")
    time.sleep(1)
    print("bro PC can't run Touhou 💀")
    time.sleep(1)
    print("[App closed]\n")

# ============================
# Wi-Fi NETWORKING
# ============================
def wifi_scan():
    print("Scanning for Wi-Fi networks...")
    time.sleep(1)
    for i, net in enumerate(wifi_networks, 1):
        print(f"{i}. {net['ssid']} - Type: {net['type']} - Strength: {net['strength']}%")
    print()

def wifi_connect():
    global connected_ssid
    wifi_scan()
    choice = input("Enter network name to connect: ").strip()
    net = next((n for n in wifi_networks if n["ssid"] == choice), None)
    if net:
        connected_ssid = net['ssid']
        print(f"Connected to {connected_ssid} ✅\n")
    else:
        print("Network not found bro 💀\n")

def wifi_disconnect():
    global connected_ssid
    if connected_ssid:
        print(f"Disconnected from {connected_ssid} 💀\n")
        connected_ssid = None
    else:
        print("Not connected to any Wi-Fi bro 💀\n")

def wifi_status():
    if connected_ssid:
        net = next((n for n in wifi_networks if n["ssid"] == connected_ssid), None)
        if net:
            print(f"Connected to {net['ssid']} - Type: {net['type']} - Strength: {net['strength']}%")
        else:
            print(f"Connected to {connected_ssid} (unknown type/strength)")
    else:
        print("Not connected to any Wi-Fi bro 💀")

# ============================
# OS RENAME
# ============================
def rename_os():
    global OS_NAME
    new_name = input("Enter new OS name: ").strip()
    if new_name:
        OS_NAME = new_name
        print(f"OS renamed to {OS_NAME} 💀")
    else:
        print("Invalid name bro 💀")

# ============================
# ACTIVATION SYSTEM
# ============================
OS_ACTIVATED = False
VALID_KEYS = ["YOUMU-25Y5-2025", "QWERTY-2025-YUUKA"]

def activate_os():
    global OS_ACTIVATED
    if OS_ACTIVATED:
        print(f"{OS_NAME} is already activated 💀")
        return
    key = input("Enter activation key: ").strip()
    if key in VALID_KEYS:
        OS_ACTIVATED = True
        print(f"{OS_NAME} activated! Full commands unlocked 💀")
    else:
        print("Invalid key bro 💀")

# ============================
# LIST COMMANDS
# ============================
def list_commands():
    cmds = [
        "help           - show help menu",
        "list           - show all commands",
        "ver            - show DOS version",
        "renameos       - rename the OS (locked if not activated)",
        "date           - show date",
        "time           - show time",
        "datetime       - date + time",
        "rttime         - toggle real-time clock (warning: uses CPU)",
        "bgm            - toggle quiet background loop to avoid auto-off",
        "app            - launch fake app",
        "notepad        - write file (locked if not activated)",
        "open           - open file",
        "dir            - list folder",
        "cd <dir>       - change folder",
        "mkdir <name>   - make folder (locked if not activated)",
        "pass           - change password",
        "user           - switch user",
        "reboot         - reboot Youmu DOS",
        "command        - run multiple commands separated by ;",
        "wifi-scan      - show available Wi-Fi networks with type/strength",
        "wifi-connect   - connect to a Wi-Fi network (locked if not activated)",
        "wifi-disconnect- disconnect current Wi-Fi",
        "wifi-status    - show current connected Wi-Fi",
        "yuac           - toggle YUAC",
        "saveos         - save current OS state",
        "loados         - load saved OS state",
        "activate       - activate the OS with a key",
        "exit           - shutdown Youmu DOS"
    ]
    print("\nAll commands:\n")
    for c in cmds:
        print(f"  {c}")
    print()

# ============================
# SHOW VERSION
# ============================
def show_version():
    print(f"{OS_NAME} Version {OS_VERSION}")

# ============================
# EXECUTE COMMAND
# ============================
def execute(cmd):
    c = cmd.strip().split()
    if not c:
        return
    c0 = c[0]

    restricted_cmds = ["mkdir", "notepad", "wifi-connect", "renameos"]

    if c0 in restricted_cmds and not OS_ACTIVATED:
        print(f"Command '{c0}' locked! Activate OS to use 💀")
        return

    if c0 == "help":
        list_commands()
    elif c0 == "list":
        list_commands()
    elif c0 == "ver":
        show_version()
    elif c0 == "renameos":
        rename_os()
    elif c0 == "date":
        show_date()
    elif c0 == "time":
        show_time()
    elif c0 == "datetime":
        show_datetime()
    elif c0 == "rttime":
        toggle_rttime()
    elif c0 == "bgm":
        toggle_bgm()
    elif c0 == "app":
        fake_app()
    elif c0 == "notepad":
        if yuac_prompt("notepad"):
            notepad()
    elif c0 == "open":
        open_file()
    elif c0 == "dir":
        dir_cmd()
    elif c0 == "cd":
        if len(c) > 1 and yuac_prompt(f"cd {c[1]}"):
            cd_cmd(c[1])
    elif c0 == "mkdir":
        if len(c) > 1 and yuac_prompt(f"mkdir {c[1]}"):
            mkdir_cmd(c[1])
    elif c0 == "pass":
        change_pass()
    elif c0 == "user":
        switch_user()
    elif c0 == "reboot":
        if yuac_prompt("reboot"):
            os.system("cls" if os.name == "nt" else "clear")
            boot_screen()
    elif c0 == "command":
        rest = " ".join(c[1:])
        cmds = rest.split(";")
        for cm in cmds:
            execute(cm)
    elif c0 == "wifi-scan":
        wifi_scan()
    elif c0 == "wifi-connect":
        if yuac_prompt("wifi-connect"):
            wifi_connect()
    elif c0 == "wifi-disconnect":
        wifi_disconnect()
    elif c0 == "wifi-status":
        wifi_status()
    elif c0 == "yuac":
        global YUAC_ENABLED
        YUAC_ENABLED = not YUAC_ENABLED
        state = "enabled" if YUAC_ENABLED else "disabled"
        print(f"YUAC is now {state} 💀")
    elif c0 == "saveos":
        save_os()
    elif c0 == "loados":
        load_os()
    elif c0 == "activate":
        activate_os()
    elif c0 == "exit":
        print(f"Shutting down {OS_NAME}...")
        sys.exit()
    else:
        print("Unknown command bro 💀")

# ============================
# MAIN LOOP
# ============================
def main_loop():
    while True:
        user_input = input(f"{path_str()}> ")
        execute(user_input)

# ============================
# START OS
# ============================
os.system("cls" if os.name == "nt" else "clear")
boot_screen()
login()
main_loop()

