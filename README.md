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
* [Mod Manager:Server](https://steamcommunity.com/sharedfiles/filedetails/?id=2725216703)
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

<img width="1920" height="1080" alt="ProjectZomboid64_2uhuZb2rVd" src="https://github.com/user-attachments/assets/43ed4b82-3e6f-491b-aa6c-a7d428551999" />

* Choose a **name** for your server settings (avoid special characters and trailing spaces)

<img width="1920" height="1080" alt="ProjectZomboid64_pEJspxh2HY" src="https://github.com/user-attachments/assets/ab85bc93-9600-49e2-8b32-eabbc332701a" />
<img width="1920" height="1080" alt="ProjectZomboid64_72VCVe2MsN" src="https://github.com/user-attachments/assets/0f9220a5-8e96-4ef8-ab5b-fbdafff7547f" />

### 2. Configure Base Settings

* Back out to the main menu.
* Go to **Mods > Server tab** (bottom-right).

<img width="1920" height="1080" alt="ProjectZomboid64_AukRZxu67R" src="https://github.com/user-attachments/assets/ca00a532-463c-48bb-9a2e-15eae207c2fc" />

  * If it doesn’t show, you haven’t properly subscribed/activated the utility mods.

### 3. Add Mods to the Server

* Select the settings you created earlier
* Add mods manually or use **"From Active Mods"**
* Check for **red** mod entries

  * Red = issues with dependencies, version mismatches, or unsubscribed content
* Fix any issues by subscribing + restarting game

### 4. Sort and Finalize

<img width="1920" height="1080" alt="ProjectZomboid64_HgkQ8eHlhe" src="https://github.com/user-attachments/assets/69349e6a-b802-4842-9b55-474bf7ff1d1d" />
<img width="1920" height="1080" alt="ProjectZomboid64_hSlQoBlxBH" src="https://github.com/user-attachments/assets/dd03ae6d-a0e5-45f9-86f4-4f12bc657c56" />


* Use **Mod Load Order Enhanced > Sort** to auto-fix load order
* Click **Save** when finished

<img width="1920" height="1080" alt="ProjectZomboid64_L3IFeBToFj" src="https://github.com/user-attachments/assets/2827908b-8796-445f-bfa9-7fea556d8205" />

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

--------------------------------------------------------------------------------------------

## 🔧 Troubleshooting

Even a properly configured server can hit snags. Below are common issues and methods to troubleshoot them effectively.

---

### 🚨 1. Server Shuts Down Immediately After Starting

**Symptoms:**
- Docker container starts but exits after a few seconds.
- No server is running despite apparently correct setup.

**Solutions:**
- **Check container logs** via Docker:
  ```bash
  docker logs pzserver
````

Look for messages like:

* `"Steam query port bind failed"`

* `"Workshop item failed to load"`

* `"mod ... not found"` or `"version mismatch"`

* **Check internal logs**:
  Navigate to logs inside the container:

  ```
  /server-data/logs/
  ```

  Look for:

  * `console.txt`
  * `coop-console.txt`
  * `server-console.txt`

* **Common causes:**

  * Missing or invalid `ServerName.ini`, `SandboxVars.lua`, or `SpawnRegions.lua`.
  * Mods enabled in `.ini` but not present in the server's `Mods` or `WorkshopItems` list.
  * Wrong server name in Docker `.env` or volume folder.

---

### 🔄 2. Server Loads with No Mods or Vanilla Settings

**Symptoms:**

* Server launches but no mods are present.
* Sandbox settings don’t match expectations.

**Solutions:**

* Ensure you’ve copied:

  * `ServerName.ini`
  * `SandboxVars.lua`
  * `SpawnRegions.lua`
  * All must match the name defined in `SERVER_NAME` or `.env`.

* Files should be located in:

  ```
  /server-data/Server/
  ```

* If using a collection:

  * Validate mod IDs and workshop IDs:

    ```ini
    Mods=modA;modB;modC
    WorkshopItems=12345678;87654321
    ```
  * Use: [https://getcollectionids.moonguy.me](https://getcollectionids.moonguy.me)

---

### 🔐 3. Clients Get Kicked for Anticheat Violations

**Symptoms:**

* Players get kicked shortly after joining.
* Logs show `Kicked user for AntiCheatProtectionType(x)`.

**Solutions:**

* Disable problem anticheat protections in `ServerName.ini`:

  ```ini
  AntiCheatProtectionType1=false
  AntiCheatProtectionType2=false
  AntiCheatProtectionType12=false
  AntiCheatProtectionType13=false
  ```

* Only disable what’s needed. For private/whitelisted servers, it’s okay to disable more.

---

### 📦 4. Workshop Mods Not Downloading in Docker

**Symptoms:**

* Server fails to start or loops on mod download.
* Log shows `ERROR: Download failed for item`.

**Solutions:**

* Ensure internet access for the container.

* Validate WorkshopItems are reachable.

* Check disk space:

  ```bash
  df -h
  ```

* If mod is broken/removed from the Steam Workshop, you may need to remove it or find a replacement.

---

### 📁 5. Mods Show Red in the Mod Menu or Fail to Load

**Symptoms:**

* Mods appear red in the in-game host UI.
* Server errors like `mod not found`.

**Solutions:**

* Ensure all dependencies are subscribed and activated on the host (Windows) machine.
* In the mod manager:

  * Use **“From Active”**
  * Click **“Mod Load Order Enhanced”** → **Sort**
  * Check that no mods are red
  * Restart game if any changes are made

---

### 🧠 6. Wrong Save Directory or World Not Loading

**Symptoms:**

* Server launches, but progress or world is missing.

**Solutions:**

* Ensure correct Docker volume is mounted:

  ```yaml
  volumes:
    - ./server-data:/server-data
  ```

* Confirm folder and file names match `SERVER_NAME` in `.env`:

  ```
  /server-data/Server/<ServerName>.ini
  ```

* Avoid:

  * Trailing spaces
  * Special characters
  * Mismatched capitalization

---
