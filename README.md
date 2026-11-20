# MeowMeow (咪)

A cozy 2D cat-collecting game in C++ and Allegro 5. You put down treats to attract cats, catch them, and deliver them to customers for coins.

<p align="center">
  <img src="assets/image/start.png" alt="MeowMeow title screen artwork" width="640">
</p>

<!-- TODO: add gameplay screenshot/GIF -->

This is the final project for **Introduction to Programming II (CSI2P)** at National Tsing Hua University (NTHU).

## Gameplay

The in-game *About* page puts it simply: "a silly little cat game: use lickable treats to lure silly cats, collect them, and let your customers have cats to cuddle."

1. **Buy treats.** The **Shop** sells four flavors of lickable cat treat (肉泥). Each one costs a different number of coins and brings a different cat.
2. **Lure cats.** Drag a treat from its basket onto the field. A cat walks in from the edge of the screen to eat it, then falls asleep where it is.
3. **Catch them.** Turn on capture mode to get a collar cursor, then click a sleeping cat to catch it.
4. **Fill orders.** The **Order** board shows customers who want a certain cat. Deliver that cat to earn coins.
5. **Complete the cat book.** The **Book** (圖鑑) records every cat you have caught and marks newly found ones.

**Settings** has three difficulty modes, and each one sets your starting coins: 99,999, 1,000 or 100.

## Controls

| Input | Action |
| --- | --- |
| Mouse | Click menu buttons and the in-game Book / Shop / Order / Back buttons |
| Left-click + drag | Pick up a treat from its basket and drop it on the field |
| `C` | Turn on capture mode (collar cursor) |
| `X` | Turn off capture mode |
| Left-click (in capture mode) | Catch the sleeping cat under the cursor |
| `A` / `D` | Turn pages in the cat book |
| `Esc` | Quit the game |

## Scenes

- **Menu** has Start, Settings, About and Quit.
- **Game** is the main field, with overlay panels for the Book, Shop and Order board.
- **Settings** is where you pick the difficulty mode.
- **About** is the information page.
- **Leave** asks you to confirm before you quit.

## Architecture

```
Main.cpp            → creates Game and runs the loop
GameWindow.*        → Allegro setup, event queue, 60 FPS timer, update/draw loop
scene/              → Scene base class + Menu, GameScene, Setting, Info, Leave
  sceneManager.*    → create_scene(SceneType) factory
element/            → game entities and UI panels (cat, meat, basket, capture, shop, book, order, …)
shapes/             → Point / Rectangle / Circle hitboxes with overlap tests
algif5/             → GIF loading helper
global.*            → shared game state (money, inventory, owned cats, input state)
assets/             → images, fonts, sounds
```

- **Game loop.** `Game` (in `GameWindow.cpp`) polls Allegro events. It records keyboard and mouse state in global arrays. On every timer tick it calls `scene->Update()` and then `scene->Draw()`. When a scene finishes, the loop destroys it and asks the scene manager for the next one.
- **Scene manager.** `Scene` is an abstract C++ class with pure virtual `Update`, `Draw` and `Destroy` methods. Each screen is a subclass, and `create_scene()` builds the subclass that matches a `SceneType` enum value.
- **Elements.** Each scene keeps a registry of `Elements`, grouped by label. An element has its own `Update`, `Draw`, `Interact` and `Destroy` functions, plus a list of the labels it interacts with. On each frame, `GameScene` updates every element, runs the interactions between elements, and removes any element marked for deletion.
- **Collision.** Hitboxes come from `shapes/`: `Point`, `Rectangle` and `Circle`, which share a common `overlap()` interface. The game uses them, for example, to check whether a dragged treat is over a basket.

## My contribution

This was a collaborative project. My roommate wrote the main structure of the game. My part was **refactoring the scene manager into an object-oriented design**. The result is the `Scene` base class, with virtual `Update`, `Draw` and `Destroy` methods, and the concrete scene classes in `scene/`, which `create_scene()` builds.

## Build & run

The build steps below are for **Windows with MinGW**. The makefile also has a macOS/Linux branch that uses `pkg-config`. For setup on macOS, see the Allegro install guide under Resources.

1. Clone the repo or download it as a ZIP.
2. Download `MinGW.zip` from [Google Drive](https://drive.google.com/file/d/1FSgx6IQebsziu2vYENt-Jz2YVg7aVoX4/view?usp=sharing).
3. Extract **both** `MinGW.zip` and `allegro.zip` into the project root. You should then have `./MinGW` and `./allegro` folders.
4. From the project root, build the game:
   ```sh
   make release   # or `make` / `make debug` for a debug build
   ```
5. Copy the runtime DLLs from `libs/` into the project root, next to `game.exe`.
6. Run:
   ```sh
   .\game.exe
   ```

### Resources

- Allegro releases: <https://github.com/liballeg/allegro5/releases>
- Allegro install guide (macOS): <https://hackmd.io/@Jiza/BkZ5a5yL2>
- Allegro documentation: <https://www.allegro.cc/manual/5/index.html>
- GIF converter: <https://ezgif.com/video-to-gif>

## Tech stack

- **C++17**, built with `g++` (MinGW-w64 on Windows) and `make`
- **Allegro 5.2**: graphics, input, audio (acodec), fonts (TTF), images and primitives
- `algif5` for GIF animation

## Credits

- Cat illustrations in the art style of モモンゲ (Naoki Gamo). See `assets/image/source.txt`.
- Fonts: GenSen Rounded and Kose / Xiaolai (both under the SIL Open Font License), plus others in `assets/font/`.
