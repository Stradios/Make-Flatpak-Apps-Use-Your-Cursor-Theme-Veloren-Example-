# Make Flatpak Apps Use Your Cursor Theme (Veloren Example)

Flatpak apps run in a sandbox and often **can’t see** cursor themes installed on your host system (e.g. `/usr/share/icons` or `~/.icons`). 
If your cursor is invisible or wrong inside a Flatpak (like **Veloren**), copy the theme into a location Flatpak can see and tell the app to use it.

---

## TL;DR (Mi case: `Fluent-dark-cursors`)

```bash
# 1) Ensure a per-user icons dir exists
mkdir -p ~/.local/share/icons

# 2) Copy your cursor theme into it
cp -r /usr/share/icons/Fluent-dark-cursors ~/.local/share/icons/

# 3) Tell Veloren's Flatpak to use it
flatpak override --env=XCURSOR_THEME=Fluent-dark-cursors net.veloren.airshipper

# 4) Launch Veloren
flatpak run net.veloren.airshipper
```

---

## Why this is needed

- **Sandboxing:** Flatpak apps don’t always have access to host paths like `/usr/share/icons`. 
- **Local copy:** Putting the theme in `~/.local/share/icons` makes it visible to Flatpak apps. 
- **Override:** `XCURSOR_THEME` tells the app which cursor theme to load.

---

## Option A — Use a Built-In Flatpak Cursor Theme

Some default themes are available in Flatpak. If you’re fine with **Adwaita/Breeze/DMZ**:

```bash
flatpak search cursor
# Examples (if offered on your system):
flatpak install org.gtk.Gtk3theme.Adwaita
flatpak install org.gtk.Gtk3theme.Breeze
```

Then run the app with the theme:
```bash
flatpak override --env=XCURSOR_THEME=Adwaita net.veloren.airshipper
flatpak run net.veloren.airshipper
```

---

## Option B — Use Your Custom Theme (Recommended)

1) **Locate your theme (host):**
- Common places:
  - `/usr/share/icons/<YourTheme>`
  - `~/.icons/<YourTheme>`
  - `~/.local/share/icons/<YourTheme>`

2) **Copy it to your per-user icons dir (Flatpak can see this):**
```bash
mkdir -p ~/.local/share/icons
cp -r /path/to/<YourTheme> ~/.local/share/icons/
```

3) **Tell the Flatpak app to use it:**
```bash
flatpak override --env=XCURSOR_THEME=<YourTheme> net.veloren.airshipper
```

4) **Run the app:**
```bash
flatpak run net.veloren.airshipper
```

> **Note:** Setting `XCURSOR_PATH` is usually **not** required when the theme is in `~/.local/share/icons`. If needed:
> ```bash
> flatpak override --env=XCURSOR_PATH=$HOME/.local/share/icons net.veloren.airshipper
> ```

---

## Verify the Theme Is Visible Inside the Sandbox

```bash
flatpak run --command=sh net.veloren.airshipper
ls /usr/share/icons ~/.local/share/icons
exit
```

---

## Check Your Current Desktop Cursor Theme (GNOME)

```bash
gsettings get org.gnome.desktop.interface cursor-theme
```

---

## Troubleshooting

- **Cursor still invisible?**
  - Double-check the theme exists inside the sandbox:
    ```bash
    flatpak run --command=sh net.veloren.airshipper
    ls ~/.local/share/icons/<YourTheme>/cursors
    ```
  - Try a known working theme:
    ```bash
    flatpak override --env=XCURSOR_THEME=Adwaita net.veloren.airshipper
    ```
- **KDE/Breeze users:** Many report success with `breeze_cursors`:
  ```bash
  flatpak override --env=XCURSOR_THEME=breeze_cursors net.veloren.airshipper
  ```
- **Flatseal (GUI) method:** 
  Open Flatseal → pick the app → **Environment** → add:
  ```
  XCURSOR_THEME = <YourTheme>
  ```
  Restart the app.

---

## Revert

Remove the override:
```bash
flatpak override --unset-env=XCURSOR_THEME net.veloren.airshipper
# or for all apps:
flatpak override --unset-env=XCURSOR_THEME --all
```

---

**That’s it!** Your Flatpak apps (including Veloren) should now use the same cursor theme as your desktop.
