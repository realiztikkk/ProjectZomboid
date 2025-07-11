# Project Zomboid Dedicated Modded Server Setup (2025)

This guide will walk you through setting up a **Project Zomboid** modded server using Docker, from scratch. It assumes you're preparing the server on a Linux machine (e.g., Ubuntu), but configuration is done via a Windows machine using the in-game UI.

---

## 🖥️ System Requirements

* Minimum **10GB of space**

  * \~2GB for the game itself
  * \~8GB for mods, map/player data, and server logs
* Recommended: at least **2 CPU cores** and **4GB RAM**

---

## 🎮 Step 1: Setup on a Windows Machine

Before launching your dedicated server, it's best to prepare everything on a local copy of Project Zomboid (PZ), using the built-in **Host menu**.

### ✅ Install the Following UI/Utility Mods for Setup:

> ⚠️ These should NOT be added to your final server modlist — they are for setup ONLY.

* [Mod Manager](https://steamcommunity.com/sharedfiles/filedetails/?id=2694448564)
* [Mod Manager:Swrver](https://steamcommunity.com/sharedfiles/filedetails/?id=2725216703)
* [Mod Manager Sync](https://steamcommunity.com/sharedfiles/filedetails/?id=2945221351)
* [Mod Load Order Enhanced](https://steamcommunity.com/sharedfiles/filedetails/?id=3180893708)
* [Mod Template Exporter](https://steamcommunity.com/sharedfiles/filedetails/?id=3112199765)

### 🔧 Recommended Gameplay Mods:

(Use these in your final server if desired)

* [Mod Options](https://steamcommunity.com/sharedfiles/filedetails/?id=2725216703)

### ⚠️ Important:

1. Subscribe to the setup mods above.
2. Restart PZ and **activate them** in the Mods menu.
3. Restart again to ensure full activation.

---

## 🏗️ Step 2: Creating Your Server Preset

### 1. Launch PZ and go to **Host > Manage Settings > Create New Settings**

* Choose a **name** for your server settings (avoid special characters and trailing spaces)

### 2. Configure Base Settings

* Back out to the main menu.
* Go to **Mods > Server tab** (bottom-right).

  * If it doesn’t show, you haven’t properly subscribed/activated the utility mods.

### 3. Add Mods to the Server

* Select the settings you created earlier
* Add mods manually or use **"From Active Mods"**
* Check for **red** mod entries

  * Red = issues with dependencies, version mismatches, or unsubscribed content
* Fix any issues by subscribing + restarting game

### 4. Sort and Finalize

* Use **Mod Load Order Enhanced > Sort** to auto-fix load order
* Click **Save** when finished

### 5. Configure Remaining Server Settings

* Return to **Host > Load Settings**
* Finish setting up spawn points, sandbox options, admin, PvP, etc.
* Click **Save** again

---

## 📂 Step 3: Locate Server Files

After saving, your server files are located at:

```
C:\Users\<YourUser>\Zomboid\Server\
```

You should see:

* `SandboxVars.lua`
* `SpawnRegions.lua`
* `<ServerName>.ini`

You'll copy these files or their contents into your Docker server shortly.

---

## 🐳 Step 4: Choose Docker Image

We recommend:

* **[Danixu’s Docker Image (Preferred)](https://github.com/Danixu/project-zomboid-server-docker)**
* Alternative: [PepeCitron’s Docker Image](https://github.com/PepeCitron/projectzomboid-server/tree/main)

Danixu's image is more recently updated

---

## ⚙️ Step 5: Docker Compose Setup

1. Create a new folder on your Docker host (e.g., `/opt/pz-server`)
2. Inside that folder, create:

   * `docker-compose.yml`
   * `env` (optional)
   * Folder for mounted configs and saves (e.g., `config`, `saves`, `mods`)

### ✍️ Compose Template:

> Use [https://getcollectionids.moonguy.me/](https://getcollectionids.moonguy.me/) to extract mod IDs from a Steam collection URL

### Example Mounts/Structure:

```
/opt/pz-server
├── config
│   ├── SandboxVars.lua
│   ├── SpawnRegions.lua
│   └── <ServerName>.ini
├── mods
├── saves
└── docker-compose.yml
```

---

## 📤 Step 6: Transfer Files to Docker Host

Copy your files from the Windows machine to your Docker server:

* `.ini` ➜ into config folder
* `.lua` ➜ into config folder

Ensure the **server name** in your `.ini` matches the name you’ll use in Docker (`SERVER_NAME=yourname`).

---

## 🧪 Step 7: Adjust .ini Settings

### Common adjustments:

```
AntiCheatProtectionType1=false
AntiCheatProtectionType2=false
AntiCheatProtectionType3=false
```

Disable any that interfere with mods.

**Other tips:**

* Enable `Password=` or `Public=false` if you’re running a private server
* Adjust `Memory=XXXX` to your system’s available RAM (e.g., 4096)
* Adjust `SteamPort1`, `SteamPort2`, `ServerPort` if you're hosting multiple servers


---

## 🚀 Step 8: Launch Docker Server

Run:

```bash
docker compose up -d
```

First launch will initialize the server and load mods from Workshop.

Check logs to confirm it’s loading correctly:

```bash
docker logs -f <container_name>
```

If you receive mod-related errors, check:

* Your `.ini` mod ID list
* If the mods downloaded properly
* If file/folder permissions are correct

---

## 🔄 Syncing New Mods

1. Add mods to your Windows PZ setup
2. Re-export config (`.ini`, `.lua`)
3. Copy to Docker server
4. Restart container

---

## 📸 Screenshot References (add these later)

* `compose_setup.png`: Example Docker Compose file
* `server_ini_config.png`: Annotated `.ini` settings
* `pz_host_config.png`: In-game Host config UI
* `mod_order_sort.png`: Mod Load Order Enhanced UI

---

## 🧰 Troubleshooting

* **Red mods** in the list? Check subscriptions, dependencies, versions.
* **Server won’t start?** Check Docker volumes and file paths.
* **Player can't join?** Check for mod mismatches or `AntiCheat` issues.
* **Mods not loading?** Ensure `WorkshopItems=` and `Mods=` are correct in the `.ini`.

---

## ✅ Final Notes

* Always **restart the server** after changing mods or configs.
* Keep your Windows PZ instance to test mod configs before going live.
* Backup your save and config folders regularly.

---

Enjoy your zombie apocalypse!
