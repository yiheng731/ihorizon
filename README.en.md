# ihorizon

**简体中文** | [English](README.en.md)

Thanks to [Horizon](https://github.com/horizon-rust/horizon) for open-sourcing its client — this project would not exist without it.

**Minecraft: Java Edition (26.3), running natively on iPhone and iPad.**

It is not the Java edition, but it can **play multiplayer with Java-edition players, no client restriction**.

Not an emulator, not a Java virtual machine, not a jar shoved into a launcher. ihorizon cross-compiles [Horizon](https://github.com/horizon-rust/horizon) — a Minecraft: Java Edition client rewritten from scratch in Rust — into an arm64 iOS executable and draws it with Metal directly.

So there is no JIT, no HotSpot, no GL4ES translation layer at startup: what runs on the screen is native ARM64 code. (Which is where the extreme performance comes from.)
<img width="896" height="414" alt="IMG_3548" src="https://github.com/user-attachments/assets/bf170c08-9764-4517-b944-05288a055bc9" />Minecraft 40fps+ (shaders), iPhone 11
<img width="896" height="414" alt="IMG_3551" src="https://github.com/user-attachments/assets/bc93943c-f2e9-4985-bc8e-8e64d4eddf80" />ihorizon 60fps+ (shaders), iPhone 11


```
iPhone (Metal)  ←  wgpu  ←  horizon-client (Rust)
                                ↑
                    winit's iOS backend (UIApplicationMain)
```

---

## What it is

| | |
|---|---|
| Rendering | wgpu 30, Metal backend, presented straight to `CAMetalLayer` |
| Window / input | winit 0.30.13's iOS backend; touches become pointers and a virtual pad on the Rust side |
| Platform | iPhone / iPad, iOS 15.0+, arm64, landscape |
| Game content | Survival, creative, spectator; three dimensions, redstone, commands, multiplayer (including online-mode auth), LAN |
| Assets | Your own Minecraft 26.3 files, extracted by `horizon-prepare` and imported |
| Distribution | Unsigned `.ipa`, self-signed install; no Mojang assets are shipped in the package |

## What it is not

- **Not a PojavLauncher replacement.** PojavLauncher runs the real Java-edition server logic (JVM + JIT + GL translation) and supports every Fabric/Forge mod, at a performance cost imposed by JIT. ihorizon runs the Rust rewrite and **cannot load Java mods**.
- **Not an official product.** It has no connection to Mojang, Microsoft, or the Horizon upstream.
- **Does not contain game assets.** The package holds the engine only; block textures, sounds and language files have to be extracted from your own copy of the game and imported.

---


## Controls

- **Tap the empty space outside a panel**: closes the current container screen (inventory, crafting table, furnace, chest, shulker box, …);
- The layout is editable: `Documents/ihorizon/touch_layout.json`, with per-button size and position

## Shaders

- Bundles an original shader pack, **Reimagined** (`shaderpacks/reimagined/`, pure WGSL, no GLSL translation layer): sky gradient, sunset band, day/night colour temperature, warm shift on block light, bloom, tone mapping and vignette.
- The colour and lighting approach follows Complementary Reimagined, rewritten in Horizon's native WGSL (no source code from it, 209 lines). Written automatically on first launch to
`Documents/ihorizon/shaderpacks/reimagined/`; enable it under Options → Video Settings → Shaders
- Performance is high: 60fps+ on an iPhone 11
  <img width="896" height="414" alt="IMG_3540" src="https://github.com/user-attachments/assets/89eb677d-7f44-4a8b-9023-bf51043f6c94" />
<img width="896" height="414" alt="IMG_3541" src="https://github.com/user-attachments/assets/e740aeca-7c61-496a-9cc1-b657ded43eb2" />
- Thanks to EminGT, author of [Complementary Shaders](https://www.complementary.dev/),
for the published colour approach, and to [Horizon](https://github.com/horizon-rust/horizon)
for the client and the shader API
- More shader adaptations are planned

Iris/OptiFine-format `.zip` packs also work (drop them in `Documents/ihorizon/shaderpacks/`), but the compatibility layer has clear gaps: no geometry shaders, no compute passes, no `bool` in storage buffers, no `imageLoad`/`Store`. Effects that need those — volumetrics, SSAO, water reflections, coloured light, TAA — cannot be reproduced fully. For the full effect set you have to write for Horizon's native format.

## Mods

- **`.hmod`**: pure data content packs (blocks, items, recipes, effects) — supported.
- **`.hnmod`**: native Rust extensions; they modify the client source and are compiled by the launcher on the spot.
- **Fabric / NeoForge jars**: **not supported**. There is no JVM here and no `net.minecraft` Java classes.
- More mod support is planned

---

## Licence

See [LICENSE](LICENSE): **personal study, research, use and modification on your own devices are allowed; any form of sale or commercial use is prohibited.**
