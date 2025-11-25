# Youmu-DOS
My first DOS OS made in Python 

import time
import datetime
import os
import sys

# ============================
# OS Info
# ============================
OS_NAME = "Youmu DOS"
OS_VERSION = "25Y5"

# ============================
# USERS
# ============================
users = {"Youmu Konpaku": "1"}
current_user = None

# ============================
# Virtual FILESYSTEM
# ============================
fs = {"root": {}}
current_path = ["root"]

# ============================
# Networking (Wi-Fi only)
# ============================
wifi_networks = ["TouhouNet", "Gensokyo-Guest", "SpiritLink"]
connected_ssid = None

# ============================
# Helper functions
# ============================
def path_str():
    return "C:\\" + "\\".join(current_path)

def get_current_dir():
    d = fs
    for p in current_path:
        d = d[p]
    return d

# ============================
# ASCII REIMU SPLASH
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

# ============================
# ASCII SNAKE ANIMATION
# ============================
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

# ============================
# Boot screen
# ============================
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
# Change Password
# ============================
def change_pass():
    newpw = input("Enter NEW password: ")
    users[current_user] = newpw
    print("Password changed!\n")

# ============================
# Switch user
# ============================
def switch_user():
    print("\nSwitching user...\n")
    login()

# ============================
# Date + Time
# ============================
def show_date():
    print("Date:", datetime.date.today().strftime("%m/%d/%Y"))

def show_time():
    print("Time:", datetime.datetime.now().strftime("%H:%M:%S"))

def show_datetime():
    now = datetime.datetime.now()
    print("Date + Time:", now.strftime("%m/%d/%Y %H:%M:%S"))

# ============================
# Directory / FS commands
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
    else:
        print("Folder already exists bro 💀")

# ============================
# Notepad
# ============================
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
# Fake App
# ============================
def fake_app():
    print("Launching app...")
    time.sleep(1)
    print("bro PC can't run Touhou 💀")
    time.sleep(1)
    print("[App closed]\n")

# ============================
# Networking commands
# ============================
def wifi_scan():
    print("Scanning for Wi-Fi networks...")
    time.sleep(1)
    for i, net in enumerate(wifi_networks, 1):
        print(f"{i}. {net}")
    print()

def wifi_connect():
    global connected_ssid
    wifi_scan()
    choice = input("Enter network name to connect: ").strip()
    if choice in wifi_networks:
        connected_ssid = choice
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
        print(f"Currently connected to: {connected_ssid}")
    else:
        print("Not connected to any Wi-Fi bro 💀")

# ============================
# Show all commands
# ============================
def list_commands():
    cmds = [
        "help           - show help menu",
        "list           - show all commands",
        "ver            - show DOS version",
        "date           - show date",
        "time           - show time",
        "datetime       - date + time",
        "app            - launch fake app",
        "notepad        - write file",
        "open           - open file",
        "dir            - list folder",
        "cd <dir>       - change folder",
        "mkdir <name>   - make folder",
        "pass           - change password",
        "user           - switch user",
        "reboot         - reboot Youmu DOS",
        "command        - run multiple commands separated by ;",
        "wifi-scan      - show available Wi-Fi networks",
        "wifi-connect   - connect to a Wi-Fi network",
        "wifi-disconnect- disconnect current Wi-Fi",
        "wifi-status    - show current connected Wi-Fi",
        "exit           - shutdown Youmu DOS"
    ]
    print("\nAll commands:\n")
    for c in cmds:
        print(f"  {c}")
    print()

# ============================
# Show version
# ============================
def show_version():
    print(f"{OS_NAME} Version {OS_VERSION}")

# ============================
# Execute single command
# ============================
def execute(cmd):
    c = cmd.strip().split()
    if not c:
        return
    c0 = c[0]
    if c0 == "help":
        list_commands()
    elif c0 == "list":
        list_commands()
    elif c0 == "ver":
        show_version()
    elif c0 == "date":
        show_date()
    elif c0 == "time":
        show_time()
    elif c0 == "datetime":
        show_datetime()
    elif c0 == "app":
        fake_app()
    elif c0 == "notepad":
        notepad()
    elif c0 == "open":
        open_file()
    elif c0 == "dir":
        dir_cmd()
    elif c0 == "cd":
        if len(c) > 1:
            cd_cmd(c[1])
    elif c0 == "mkdir":
        if len(c) > 1:
            mkdir_cmd(c[1])
    elif c0 == "pass":
        change_pass()
    elif c0 == "user":
        switch_user()
    elif c0 == "reboot":
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
        wifi_connect()
    elif c0 == "wifi-disconnect":
        wifi_disconnect()
    elif c0 == "wifi-status":
        wifi_status()
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
        user_input = input(f"{path_str()}> ").strip()
        if not user_input:
            continue
        if ";" in user_input:
            for cmd in user_input.split(";"):
                execute(cmd)
        else:
            execute(user_input)

# ============================
# RUN OS
# ============================
boot_screen()
login()
main_loop()
