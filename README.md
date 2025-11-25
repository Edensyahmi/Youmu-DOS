import os
import sys
import time
import datetime
import threading
import json
import random

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
# NETWORKING (Wi-Fi + Ethernet)
# ============================
wifi_networks = [
    {"ssid": "TouhouNet", "type": "5G", "strength": 95},
    {"ssid": "Gensokyo-Guest", "type": "6G", "strength": 70},
    {"ssid": "SpiritLink", "type": "7G", "strength": 40},
    {"ssid": "Moon Network", "type": "7G", "strength": 100},
]

wifi_free_inactive = [
    {"ssid": "TouhouNet-2.4G", "type": "2.4G", "strength": 25},
    {"ssid": "Gensokyo-Guest-2.4G", "type": "2.4G", "strength": 20},
]

ethernet_networks = [
    {"name": "EtherNet-Local", "speed": "1 Gbps"},
    {"name": "Moon LAN", "speed": "100 Tbps"},
]

connected_ssid = None
connected_ethernet = None

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
        "connected_ethernet": connected_ethernet,
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
    global fs, users, current_user, current_path
    global connected_ssid, connected_ethernet, YUAC_ENABLED
    global OS_NAME, RTTIME_ENABLED, BGM_ENABLED, OS_ACTIVATED
    try:
        with open(SAVE_FILE, "r") as f:
            state = json.load(f)
        fs = state.get("fs", {"root": {}})
        users = state.get("users", {"Youmu Konpaku": "1"})
        current_user = state.get("current_user", None)
        current_path = state.get("current_path", ["root"])
        connected_ssid = state.get("connected_ssid", None)
        connected_ethernet = state.get("connected_ethernet", None)
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
    ]
    for i in range(2):
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
    networks = wifi_networks.copy()
    if not OS_ACTIVATED:
        networks += wifi_free_inactive
    for i, net in enumerate(networks, 1):
        print(f"{i}. {net['ssid']} - Type: {net['type']} - Strength: {net['strength']}%")
    print()

def wifi_connect():
    global connected_ssid
    wifi_scan()
    choice = input("Enter network name to connect: ").strip()
    net = next((n for n in wifi_networks + wifi_free_inactive if n["ssid"] == choice), None)
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
        net = next((n for n in wifi_networks + wifi_free_inactive if n["ssid"] == connected_ssid), None)
        if net:
            print(f"Connected to {net['ssid']} - Type: {net['type']} - Strength: {net['strength']}%")
        else:
            print(f"Connected to {connected_ssid} (unknown type/strength)")
    else:
        print("Not connected to any Wi-Fi bro 💀")

# ============================
# Ethernet NETWORKING
# ============================
def ethernet_list():
    print("Available Ethernet networks:")
    for i, net in enumerate(ethernet_networks, 1):
        print(f"{i}. {net['name']} - Speed: {net['speed']}")
    print()

def ethernet_connect():
    global connected_ethernet
    ethernet_list()
    choice = input("Enter Ethernet network name to connect: ").strip()
    net = next((n for n in ethernet_networks if n["name"] == choice), None)
    if net:
        connected_ethernet = net['name']
        print(f"Connected to Ethernet network {connected_ethernet} ✅\n")
    else:
        print("Ethernet network not found bro 💀\n")

def ethernet_status():
    if connected_ethernet:
        net = next((n for n in ethernet_networks if n["name"] == connected_ethernet), None)
        if net:
            print(f"Connected to {net['name']} - Speed: {net['speed']}")
        else:
            print(f"Connected to {connected_ethernet} (unknown speed)")
    else:
        print("Not connected to any Ethernet network bro 💀\n")

# ============================
# Speed Test
# ============================
def speed_test():
    if connected_ssid:
        net = next((n for n in wifi_networks + wifi_free_inactive if n["ssid"] == connected_ssid), None)
        if net:
            print(f"Testing Wi-Fi ({net['ssid']}) speed...")
            time.sleep(1)
            speed = random.randint(50, 100) if net['ssid'] != "Moon Network" else 100_000
            print(f"Speed: {speed} Mbps 💀\n")
    elif connected_ethernet:
        net = next((n for n in ethernet_networks if n["name"] == connected_ethernet), None)
        if net:
            print(f"Testing Ethernet ({net['name']}) speed...")
            time.sleep(1)
            print(f"Speed: {net['speed']} 💀\n")
    else:
        print("Not connected to any network 💀\n")

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
        "reboot         - reboot Youmu DOS / nuclear delete",
        "command        - run multiple commands separated by ;",
        "wifi-scan      - show available Wi-Fi networks with type/strength",
        "wifi-connect   - connect to a Wi-Fi network (locked if not activated)",
        "wifi-disconnect- disconnect current Wi-Fi",
        "wifi-status    - show current connected Wi-Fi",
        "ethernet-list   - list available Ethernet networks",
        "ethernet-connect- connect to Ethernet network",
        "ethernet-status - show connected Ethernet",
        "speed-test      - test current network speed",
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
        print("Command locked! Activate OS to use 💀\n")
        return

    if c0 == "help" or c0 == "list":
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
        notepad()
    elif c0 == "open":
        open_file()
    elif c0 == "dir":
        dir_cmd()
    elif c0 == "cd":
        if len(c) > 1:
            cd_cmd(c[1])
        else:
            print("Specify folder name 💀")
    elif c0 == "mkdir":
        if len(c) > 1:
            mkdir_cmd(c[1])
        else:
            print("Specify folder name 💀")
    elif c0 == "pass":
        change_pass()
    elif c0 == "user":
        switch_user()
    elif c0 == "reboot":
        if yuac_prompt("reboot"):
            choice = input("Delete all code/data and paste new OS? (y/N): ").strip().lower()
            if choice == "y":
                confirm = input("⚠️ Type 'CONFIRM' to delete EVERYTHING: ").strip()
                if confirm == "CONFIRM":
                    try: os.remove(SAVE_FILE)
                    except: pass
                    os.system("cls" if os.name == "nt" else "clear")
                    print("All code/data wiped! Paste new OS now 💀")
                    sys.exit()
                else:
                    print("Reboot aborted 💀")
            else:
                os.system("cls" if os.name == "nt" else "clear")
                boot_screen()
    elif c0 == "command":
        rest = cmd[len("command"):].strip()
        cmds = rest.split(";")
        for cm in cmds:
            execute(cm.strip())
    elif c0 == "wifi-scan":
        wifi_scan()
    elif c0 == "wifi-connect":
        if yuac_prompt("wifi-connect"):
            wifi_connect()
    elif c0 == "wifi-disconnect":
        wifi_disconnect()
    elif c0 == "wifi-status":
        wifi_status()
    elif c0 == "ethernet-list":
        ethernet_list()
    elif c0 == "ethernet-connect":
        if yuac_prompt("ethernet-connect"):
            ethernet_connect()
    elif c0 == "ethernet-status":
        ethernet_status()
    elif c0 == "speed-test":
        speed_test()
    elif c0 == "yuac":
        global YUAC_ENABLED
        YUAC_ENABLED = not YUAC_ENABLED
        print(f"YUAC {'enabled' if YUAC_ENABLED else 'disabled'} 💀")
    elif c0 == "saveos":
        save_os()
    elif c0 == "loados":
        load_os()
    elif c0 == "activate":
        activate_os()
    elif c0 == "exit":
        print("Shutting down Youmu DOS 💀")
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
load_os()
login()
main_loop()



