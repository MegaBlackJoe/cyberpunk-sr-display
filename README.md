# CyberpunkVR Port

`CyberpunkVR_Stereo` SR display implementation

Repository: <https://github.com/dariulone/cyberpunk-vr-port>

> ⚠️ Experimental community mod. Not affiliated with CD PROJEKT RED. Use at your
> own risk and keep backups of your saves.

## Credits

- **[iPowerTech](https://github.com/iPowerTech)** — **VR driving.** Grabbing the
  wheel with your own hands, the steering geometry, the deadzone and lock-angle
  controls, the horn on the wheel hub, and shooting while you drive are all his
  work, from [his fork](https://github.com/iPowerTech/cyberpunk-vr-port).
  

## Features

- **Real stereo, not reprojection.** The second eye is an actual engine view — a
  render-to-texture camera on the player entity that runs the frame graph for its
  own eye, from its own position, with its own projection. It falls back to mono
  automatically whenever that view has nothing fresh to give (menus, loading).
  

## Requirements

- Cyberpunk 2077 (PC, 2.31).
- Cyber Engine Tweaks
- RED4ext
- ArchiveXL
- TweakXL
- redscript
- Codeware (**1.20 or newer** — older builds fail script compilation)
- Visual Holsters (Automatic Clothes Swap)
- Visible Bullets (Projectile Restoration)
- Equipment-EX
- Nova Optics
- Input Loader
- HUDitor

Recommended but not required: **HUDitor**, **Visible Bullets**, for HUD placement — the port ships a
VR-tuned layout for it in `mods\config\huditor\` and no longer moves HUD widgets
itself.

Install RED4ext, CET and redscript first (the usual Nexus dependencies).

## Installation (drop-in)

Download the release archive and extract its contents into your **Cyberpunk 2077
game root** (the folder that contains `bin\`, `r6\`, `red4ext\`). The files land
as:

```
red4ext\plugins\CyberpunkVR_Stereo\CyberpunkVR_Stereo.dll     # the VR plugin: OpenXR, stereo, overlay, VRIK
red4ext\plugins\CyberpunkVR_Stereo\CyberpunkVR_Sight*.dxil    # sight shaders, loaded by name at PSO swap
                                                              # NOTE: CyberpunkVR_Hands.dll is GONE -- its
                                                              # code is inside CyberpunkVR_Stereo.dll now.
                                                              # An old copy left in red4ext\plugins makes
                                                              # two plugins detour one address, which
                                                              # crashes with a fault at FFFFFFFFFFFFFFFF.
archive\pc\mod\cyberpunkvrport.archive                        # held props, weapon assets, the VRCAM cameras
archive\pc\mod\VRCigarette.archive.xl, vrport_basketball...   # the smoking and basketball props
bin\x64\plugins\cyber_engine_tweaks\mods\CyberpunkVRPort_*\   # CET mods -- see the table below
r6\scripts\CyberpunkVRPort_*\                                 # redscript mods -- see the table below
r6\input\HUDitor.xml, r6\config\...                           # the HUDitor binding and the port's config
```

Then **start your OpenXR runtime first**, and launch the game.

> There is no `dxgi.dll` any more — this is a RED4ext plugin. Anything else that
> proxies dxgi (R.E.A.L. VR, for one) must be out of `bin\x64` or the two fight
> over the same engine hooks; `scripts\deploy_stereo.ps1` moves one aside for you.

> Keep only one `.dll` in each `red4ext\plugins\CyberpunkVR_*` folder. RED4ext
> loads **every** DLL it finds there, so a renamed backup beside the real build
> loads as a second copy of the plugin and the two fight over the same hooks.

From a source tree, install with:

```
cmake --build build --config Release --target cyberpunkvrport_stereo
pwsh scripts\deploy_stereo.ps1 -GameRoot "<game root>"
```

## Controls

VR controller input is merged into the native CP2077 gamepad, so the in-game
"Controller" key bindings apply. Default VR mapping, **on foot**:

| Input | Action |
|---|---|
| Left stick | Move — **one speed whatever the deflection**, direction from the stick |
| Left stick **fully forward, held 0.2 s** | **Sprint** — held, and it survives a dash |
| Right stick X | Turn camera (snap or smooth; a snap needs a FULL push) |
| Right stick **fully up** | **Dash** (dodge, direction from the left stick) — once per push |
| Right stick **fully down** | **Crouch** (R3) |
| Right thumb **click** | **Slide release** — racks the weapon (physical reload) |
| Left hand to your **left ear** + **left grip** | **Scanner**, *toggled* — squeeze to open, squeeze again to close |
| Right trigger / Left trigger | Fire / Aim (left trigger is also melee block) |
| Right grip | Hand-to-holster equip / unequip; melee power modifier |
| Left grip | **Grab the magazine** during a reload (at the ear it is the scanner) |
| Left stick **up / down, to the stop** | Page the quickhack list *while the scanner is open* |
| A | Jump (double jump and charge jump unchanged) |
| B, **weapon in hand** | **Drop the magazine** (physical reload) — not dodge |
| B, **holstered** | The game's own B again — close the phone, back out |
| B, **phone / radio / vehicle list open** | Closes it, weapon in hand or not — and cannot drop a magazine for a second afterwards |
| X / Y | Reload·interact / Weapon switch |
| Left menu button | Pause menu |
| Swing a melee weapon | VR motion melee (native attack along the blade) |

While the scanner is open the same hand works it: the **left stick to the stop**
pages the quickhack list (below the stop it still walks, so you can read and move),
**X** applies the selected hack, the **right trigger** tags the target instead of
firing, the **right stick click** changes the tab and **left trigger + right stick**
zooms. The list used to sit on X and Y, which meant reaching across the face buttons
with the hand that is already holding a grip at your ear — the very reach a one-hand
scanner exists to avoid.

B is the port's only while a weapon is actually in your hand, because a magazine
drop is the only thing the port needs it for. Holstered, it reaches the game — so
the phone and the radial close normally. And while the phone, the radio port or the
vehicle list is up it reaches the game as well, weapon or no weapon: those three are
overlays rather than menus, which is why they used to be impossible to close with a
gun out. Spamming that button is safe — the magazine drop stays blocked for a second
after the popup closes.

In **menus** and in **braindances** the pad is vanilla: none of the remaps below
apply there, including the grips-as-shoulders that used to page menu tabs.

While **driving**, the same controllers do something else:

| Input | Action |
|---|---|
| Grip, hand at the wheel / handlebars | Grab it — that arm goes back to the driving animation |
| Tilt of the line through both controllers | Steering (one hand: that controller against the wheel centre) |
| Hand on the **middle of the wheel** | **Horn** — no grip needed; a hand that is grabbing never honks |
| Right trigger / Left trigger | Throttle / Brake |
| Right trigger **with a weapon drawn** | **Fire.** The throttle latches at the speed it had |
| Left stick forward / back, weapon drawn | Trim the latched throttle |
| **X, held** | **Get out.** B is never the exit in a car, so no stray press can eject you |
| Perspective toggle | **Does nothing** — the camera is held in first person |
| A | **Confirm a dialogue line** (it is X on foot, and X is the exit in here) — the handbrake still fires |

Each hand is independent, so you can hold the wheel with one and keep the other on
a gun. With a weapon equipped the right hand shoots and cannot grab the wheel;
holster it and the wheel is its again — and since reaching for a holster is how you
draw in the first place, that hand is already off the wheel by then. A grip that is
holding the wheel does nothing else: no holster equip, no magazine grab. Nothing
here needs to know which vehicle you are in, because the reference pose is the
game's own driving animation — bikes included.

The exit is a HOLD rather than a tap on purpose. The game's `ExitVehicle_Button`
carries no hold of its own, so the vehicle acts on the first frame it sees the
action — and at speed acting on it means throwing you out of the car.

**D-pad chord.** Hold the **left stick clicked in**, then pick the direction with
the **right stick** — up / down / left / right, **to the stop**, the same full push
every other gesture here asks for so that a resting thumb cannot step a list. While
the chord is held the right stick is taken out of the camera, so selecting a
direction cannot snap-turn you.
Release the left stick *without* having chosen a direction and it emits the normal
L3 press instead, so nothing is lost by using it.

Buttons follow each runtime's interaction profile (Touch / Index / Vive / WMR);
customise the actual actions in the game's *Settings → Key Bindings → Controller*.

Hotkeys:

- `F7` — recenter HMD
- `F8` — toggle the VR menu rectangle between full-HMD and a small panel
- `F10` / `Insert` — open the in-headset settings overlay
- `F11` — HUDitor's editor, if you installed it (the port rebinds it off F7)

## In-headset overlay (F10)

Four tabs, live, and saved to `vrport.ini` — nothing here needs a restart. The
**driving** block sits under Controls: wheel grab and its grab radius, steering
deadzone and full-lock angle, horn on/off with its hub radius,
trigger-fires-the-gun with its throttle trim rate — plus a live read-out of what
is grabbed, what the steering is doing and whether the horn is being pressed.

- **General** — world scale, IPD scale, stereo separation, VR menu FOV and quad
  size, motion prediction, reuse-last-clean-frame, pose pair-lock, and the head
  offset (X right / Y forward / Z up), with a second set that applies only in a
  vehicle.
- **Controls** — decoupled weapon aim and its laser dot, locomotion source
  (Game / HMD / left hand / right hand), snap turn and angle, immersive holsters,
  the driving block, and the current binding list.
- **Stereo** — the second eye itself: which eye VRCAM is sent to, how stale its
  last frame may get before the submit falls back to mono, the HUD composite, and
  the live counters that say whether the second view is producing, being captured
  and reaching the headset.
- **VRIK** — start/stop tracking, physical body rotation and its free-look cone,
  the cutscene-suspend tier, IK calibration (reach scale, height, elbow
  swing/pole, wrist offset), diagnostics.

The launcher (before the game starts) picks the render resolution and carries a
**DEBUG** tick-box that arms every diagnostic probe at once. Leave it off for
play: it is for diagnosis and it costs both frame time and a very large log.

## Mod components

| Component | Type | Purpose |
|---|---|---|
| `CyberpunkVR_Stereo.dll` | RED4ext plugin | OpenXR head tracking, the second engine view, HUD composite, sight shaders, F10 overlay, XInput merge |
| `CyberpunkVR_Stereo.dll` | (same plugin) | Full-body avatar / hand IK, hand recoil, weapon-aim and muzzle override, smoking and reload poses, shared-memory bridge -- was `CyberpunkVR_Hands.dll` until the single-plugin build |
| `cyberpunkvrport.archive` | archive | The magazine/prop carriers on the player templates (Johnny's replacer body included), the weapon assets the reload drives, the case-ejection effects, the 62 VRCAM camera components, and the UI layouts fitted to the square view — phone, vehicle panel, radio, subtitles, dialogue choices, tutorial popups |
| `CyberpunkVRPort_Stereo` | CET | Enables the VRCAM component the launcher picked |
| `CyberpunkVRPort_VRIK` | CET | Starts hand tracking, bridges calibration, publishes the locomotion state |
| `CyberpunkVRPort_Weapon` | CET | Decoupled weapon aim + VR motion-melee detection |
| `CyberpunkVRPort_Reload` | CET | The physical reload — thirteen per-weapon configs, the magazine, the slide, the revolver cylinder |
| `CyberpunkVRPort_HandCollision` | CET | Hand / finger / weapon push-out. **Off since 0.1.2** (`COLL_ON = false`) — the body capsules came back out of the archive after a vehicle launched the player |
| `CyberpunkVRPort_Crosshair` | CET | Hides the game's own crosshair while decoupled VR aim is on |
| `CyberpunkVRPort_Holster` | CET + reds | Hand-to-holster equip/unequip (immersive / simple) |
| `CyberpunkVRPort_Smoking` | CET + reds | Cigarette / lighter props, FX, audio, auto-puff |
| `CyberpunkVRPort_Basketball` | CET + reds | VR basketball with real ball values (the physics half is redscript) |
| `CyberpunkVRPort_WorldMap` | CET + reds | World-map head-lock |
| `CyberpunkVRPort_Hold` | reds | Puts a real item in a hand — `AddItemToSlot` works from redscript and silently does nothing from CET. This is how the Overture's speedloader is carried |
| `CyberpunkVRPort_Melee` | reds | Native melee along the blade segment |
| `CyberpunkVRPort_WeaponUp` | reds | Stops auto-lower / auto-unequip of a drawn MELEE weapon |
| `CyberpunkVRPort_NoAnims` | reds | Disables VR-fighting animations (keeps gameplay systems); also carries no-auto-reload and the firearm half of no-auto-lower |
| `CyberpunkVRPort_LootUi` | reds | The loot window for the square view — the plate at 0.70 and the detailed description centred above the take button instead of off the right edge |
| `CyberpunkVRPort_BraindanceHud` | CET | The braindance notification plate, whose slot is declared in a HUD asset the port does not ship |
| `CyberpunkVRPort_SettingsGuard` | reds | Removes the two cascade shadow rows from the settings menu |

## Logs

- `Cyberpunk 2077\bin\x64\cyberpunkvrport.log` — the plugin's own log, and the
  right file for a bug report. Quiet by default; tick **DEBUG** in the launcher
  for per-frame diagnostics.
- `Cyberpunk 2077\red4ext\logs\` — script validation and plugin load errors. If
  redscript compilation fails, *every* redscript mod is off, not just the one that
  failed, so check here first when something stops working all at once.
- Per-mod CET logs live in each mod folder; they follow the same DEBUG switch.

## Test hardware used during development

- Headset: PICO 4 (via VDXR)
- CPU: AMD Ryzen 7 5800X
- GPU: NVIDIA RTX 5070 Ti
- RAM: 32 GB DDR4
- OS: Windows 11 Pro 25H2 (26200)

## Donations

Donating is your personal choice. It speeds up development and makes new features
possible — nobody is forcing you to do it.

- <https://boosty.to/dariulone>
- <https://dalink.to/dariulone>

| | |
|---|---|
| USDT TRC20 | `TRgmDeRcFumXvsSRqYV5kQAqRAvoFKXJCt` |
| USDT BEP20 | `0x4638c6580d1e684bdc60a1c415e5cb1522b66942` |
| TRX | `TRgmDeRcFumXvsSRqYV5kQAqRAvoFKXJCt` |
| BTC | `13AfpBwZvaezf36FmpjtENHTXjYcnzEsze` |
