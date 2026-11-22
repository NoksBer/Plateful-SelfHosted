[README.md](https://github.com/user-attachments/files/35235373/README.md)
# Plateful

A self-hosted, fully offline weekly meal planner. Python standard library only, with no pip packages, no internet calls and no accounts.

- **Week-by-week planner.** Each week has its own budget, its own list of who's eating, any guests, and its own set of meals (breakfast, lunch, dinner, snack, per day).
- **Household editor.** Add or remove people at any time. Set diets, allergies, likes and dislikes, and a portion size for each person.
- **Budget-aware suggestions.** "Fill empty meals" respects diets and allergies, learns from 👍/👎, avoids repeats, reuses ingredients across meals, and fits your budget. Lock any meal, then regenerate the rest.
- **Grocery list.** Grouped by aisle, checkable on your phone, with pantry items excluded and extras you add yourself.
- **Price Book.** Your own prices for your store. The starter prices are estimates, so edit them or import a CSV.

## Install on Proxmox (helper script)

Run these on the **Proxmox node shell**. The script creates a Debian 12 LXC (1 core, 512 MB RAM, 4 GB disk), installs Plateful as a systemd service, and prints the URL.

**Option A: from GitHub.** Push this folder to a repo, then run:

```bash
PLATEFUL_REPO=NoksBer/Plateful-SelfHosted bash -c "$(curl -fsSL "https://raw.githubusercontent.com/NoksBer/Plateful-SelfHosted/main/plateful/install/plateful.sh?$(date +%s)")"
```

**Option B: no GitHub.** Copy `plateful.tar.gz` and `install/plateful.sh` to the node (WinSCP or scp), then run:

```bash
PLATEFUL_TARBALL=/root/plateful.tar.gz bash /root/plateful.sh
```

Open `http://<container-ip>:8080`. The first launch runs the setup wizard.

Update later with `PLATEFUL_TARBALL=... bash plateful.sh update <CTID>`. Your data is kept.

Overrides: `CTID`, `CT_NAME`, `CORES`, `RAM`, `DISK`, `BRIDGE`, `STORAGE`, `TEMPLATE_STORAGE`, `PORT`.

## Docker or local alternative

```bash
docker build -t plateful . && docker run -d -p 8080:8080 -v plateful-data:/data plateful
# or, anywhere with Python 3.8+:
python3 server.py        # PLATEFUL_PORT / PLATEFUL_HOST / PLATEFUL_DB env vars
```

## Notes and limits

- **Meijer data.** Meijer has no public price or stock API, so prices come from your Price Book. Live stock lookup would need a scraper, which is fragile and may break their terms, so it's left out for now.
- **Costs.** Costs are estimates. Items sold by the can, jar or head round up to whole units, and bulk-pack sizes aren't modeled.
- **Backup.** The data is one SQLite file at `/opt/plateful/data/plateful.db`. Use a Proxmox backup of the container, or Settings → JSON export.
- **No login.** There is no authentication, so keep it on your LAN or put it behind a reverse proxy.
