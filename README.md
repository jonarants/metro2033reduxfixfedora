# Metro 2033 Redux — Fix: Game Opens on Wrong Monitor (Dual Ultrawide Setup)

## Environment

| Component | Details |
|---|---|
| Distro | Fedora 44 KDE Plasma |
| Session | Wayland |
| GPU | AMD RX 7900 XT |
| Monitors | 2x ultrawide 3440x1440 stacked vertically (one above the other) |
| Primary monitor | DP-1 — `Geometry: 0,1440` (bottom monitor) |
| Secondary monitor | DP-2 — `Geometry: 0,0` (top monitor) |
| Steam | Native Linux |

---

## Problem

When launching **Metro 2033 Redux** using its native Linux version, the game consistently opens on the **secondary (top) monitor**, ignoring the primary monitor configured in KDE.

### Failed attempts

The following methods **did not resolve** the issue:

- SDL environment variables via Launch Options:
  ```
  SDL_VIDEO_FULLSCREEN_DISPLAY=0 %command%
  SDL_VIDEO_FULLSCREEN_DISPLAY=1 %command%
  SDL_VIDEO_WINDOW_POS=0,1440 %command%
  ```
- Editing `user.cfg` with `r_fullscreen off` and correct `r_res_hor/vert` values
- Locking `user.cfg` with `chmod 444` (Steam Cloud overwrites the file)
- KWin Window Rules (Screen → Force) — rule did not persist between sessions

### Root cause

The **4A Engine** (Metro 2033 Redux's engine) in its native Linux build **ignores SDL and the Wayland compositor** for window positioning in multi-monitor setups. This is a known bug documented by the community on [ProtonDB](https://www.protondb.com/app/286690).

---

## Solution

Run the game using **Proton Experimental** instead of the native Linux version.

### Steps inside Steam

1. Open **Steam**
2. Go to your **Library**
3. Right-click **Metro 2033 Redux** → **Properties**
4. Go to the **Compatibility** tab
5. Check **"Force the use of a specific Steam Play compatibility tool"**
6. In the dropdown, select **Proton Experimental**
7. Close the properties dialog
7. In general in Launch Options enter **"PROTON_USE_WINED3D=0 %command%"** (Optional)
8. Close the properties dialog
9. Launch the game normally

### Result

With Proton Experimental, the game correctly respects the primary monitor configured in KDE Plasma under Wayland, since it runs through Wine/DXVK which properly honors the compositor for window placement.

---

## Additional notes

- No extra Launch Options are required
- Performance with Proton Experimental is comparable to native
- If the native Linux build of the 4A Engine is updated in the future, the fix can be revisited by disabling forced compatibility
- Community report reference: [ProtonDB — Metro 2033 Redux](https://www.protondb.com/app/286690)
