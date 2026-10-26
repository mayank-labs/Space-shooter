# Space Shooter

Space Shooter is a local two-player arcade game written in C++ with OpenGL
and GLUT. Two pilots control mirrored flying saucers, dodge vertically and
horizontally, and fire lasers at each other. The first player to reduce the
opponent's LIFE to zero wins.

The project is intentionally dependency-light: the game is contained in
[`main.cpp`](./main.cpp), and the `assets/` directory contains screenshots and
optional project artwork.

## Features

- Two-player keyboard controls on one computer.
- Smooth movement while keys are held.
- Straight, upward, and downward laser shots.
- Five-point damage per successful hit.
- Ship movement boundaries so players stay in the arena.
- Pause/resume, instant restart, and return-to-menu shortcuts.
- Mouse-hover menu with Start Game, Instructions, and Quit.
- Resizable OpenGL window and mirrored player presentation.
- 60 FPS timer-driven animation.

## Requirements

### All platforms

- A C++ compiler with C++03-or-newer support.
- OpenGL development headers and libraries.
- A GLUT implementation:
  - **macOS:** the system GLUT framework supplied by Apple.
  - **Linux:** freeglut and its development package.
  - **Windows:** freeglut development files (headers, library, and DLL).

No external image, audio, or C++ package is required to build the game.

### macOS installation

Install Apple's Command Line Tools if they are not already installed:

```bash
xcode-select --install
```

Verify that a macOS SDK and compiler are available:

```bash
xcode-select -p
clang++ --version
ls /Library/Developer/CommandLineTools/SDKs/*.sdk
```

The build uses Apple's native GLUT framework. Do **not** install Homebrew
`freeglut` for this command; that version depends on X11 and can fail with
`failed to open display` on a normal macOS desktop.

### Ubuntu/Debian installation

```bash
sudo apt update
sudo apt install build-essential freeglut3-dev
```

### Fedora installation

```bash
sudo dnf install gcc-c++ freeglut-devel
```

### Windows installation

Install MinGW-w64 (or another GCC-compatible compiler) and freeglut. Configure
the compiler's include and library search paths, and make sure the freeglut
DLL is beside the executable or on `PATH`.

## Build and run

From the project directory:

### macOS

```bash
clang++ -std=c++11 main.cpp -o space-shooter \
  -framework GLUT -framework OpenGL
./space-shooter
```

If `clang++` is unavailable, `g++` from the Command Line Tools can be used
with the same framework flags:

```bash
g++ -std=c++11 main.cpp -o space-shooter \
  -framework GLUT -framework OpenGL
./space-shooter
```

Run the executable from a logged-in desktop session. GLUT opens a graphical
window and will not work in a headless SSH session unless display forwarding
is configured.

### Linux

```bash
g++ -std=c++11 main.cpp -o space-shooter \
  -lglut -lGL -lGLU
./space-shooter
```

On some distributions the GLUT library is named `freeglut`; the development
package normally still provides the `-lglut` link name.

### Windows with MinGW

```bash
g++ -std=c++11 main.cpp -o space-shooter.exe \
  -lfreeglut -lopengl32 -lglu32
./space-shooter.exe
```

If the linker cannot find `GL/glut.h`, `libfreeglut`, or `freeglut.dll`,
recheck the freeglut include/lib/bin paths in the compiler configuration.

## How to play

1. Launch the executable.
2. Press **Enter** on the title screen.
3. Use the mouse to hover over **Start Game**, then click it.
4. Move and fire using the controls below.
5. Reduce the opponent's LIFE from 100 to 0.

### Player 1 — left/amber ship

| Action | Key |
|---|---|
| Move up | `W` |
| Move down | `S` |
| Move left | `A` |
| Move right | `D` |
| Fire | `C` |

Hold `W` or `S` while holding `C` to angle the laser upward or downward.
Firing keeps the ship stationary, making aiming a deliberate trade-off.

### Player 2 — right/jade ship

| Action | Key |
|---|---|
| Move up | `I` |
| Move down | `K` |
| Move left | `J` |
| Move right | `L` |
| Fire | `M` |

Hold `I` or `K` while holding `M` to aim the laser vertically.

### Round and menu controls

| Key/action | Result |
|---|---|
| `P` | Pause or resume the current round |
| `R` | Restart the round immediately |
| `Esc` | Return to the main menu |
| Mouse click | Activate a highlighted menu button |

Each hit removes 5 LIFE. A hit can register approximately every fifth of a
second while a laser is held on target. If both ships reach zero in the same
update, the result is shown as a draw.

## Project structure

```text
SPACE_SHOOTER/
├── main.cpp                    # Game logic, rendering, input, and entry point
├── README.md                   # Build, installation, and gameplay guide
└── assets/
    ├── emoji-alien-monster.png
    ├── emoji-rocket.png
    ├── screenshot-instructions.png
    └── screenshot-menu.png
```

The PNG files are documentation/project artwork; the executable does not need
to load them at runtime.

## Troubleshooting

### `GL/glut.h` or `GLUT/glut.h` not found

Install the platform's OpenGL/GLUT development package and retry the build.
On macOS, install or repair the Command Line Tools with:

```bash
xcode-select --install
```

### `failed to open display`

Start the game from a graphical desktop session. On macOS, avoid linking this
project against Homebrew X11 freeglut; use the native framework command above.
On Linux, verify that the `DISPLAY` environment variable points to an active
desktop session.

### Window opens and closes immediately

Run the executable from a terminal so that any linker or runtime message stays
visible. Confirm that the freeglut DLL is available on Windows and that the
OpenGL driver is installed on Linux.

### Controls do not respond

Click the game window once to give it keyboard focus. Use lowercase keys;
the game intentionally maps lowercase ASCII input.

## Technical overview

The program uses GLUT callbacks rather than a manually written game loop:

- `display()` draws the active screen.
- `timer()` schedules a redraw every 1/60 second.
- `keyPressed()` and `keyReleased()` maintain held-key state.
- `keyOperations()` applies movement, firing, pause, and restart behavior.
- `reshape()` keeps the viewport current when the window changes size.

The world is rendered with a 2D orthographic projection from `-1200..1200`
on the x-axis and `-700..700` on the y-axis. The game is pure immediate-mode
OpenGL and is suitable as a compact GLUT learning project.

## License and credits

This repository is an educational/student project. The names shown on the
intro screen are retained from the original project. Add a license file before
redistributing the code or artwork outside your intended class/project use.
