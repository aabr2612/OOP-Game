# Prison Escape

**Prison Escape** is a 2D arcade-style game built in C# using Windows Forms. Players navigate a character through a level, avoiding or destroying enemies with directional projectiles, collecting rewards, and aiming to eliminate all enemies to win. The game features a robust backend (`GameLibrary.GL`) for game mechanics and a frontend (`Prison_Escape.UI`) for user interface and navigation.

## Table of Contents
- [Features](#features)
- [Backend Overview](#backend-overview)
  - [Game Logic](#game-logic)
  - [Game Objects](#game-objects)
  - [Movement System](#movement-system)
  - [Collision Detection](#collision-detection)
  - [Firing Mechanism](#firing-mechanism)
- [Frontend Overview](#frontend-overview)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Dependencies](#dependencies)
- [Contact](#contact)

## Features
- **Dynamic Gameplay**: Control a player to dodge or shoot enemies, collect rewards, and clear levels.
- **Multiple Movement Patterns**: Enemies move horizontally, vertically, or in zigzag patterns, while players use keyboard controls.
- **Projectile System**: Fire bullets in eight directions (cardinal and diagonal) to eliminate enemies.
- **Collision Handling**: Supports interactions like player-enemy collisions (game over), bullet-enemy hits (score increase), and reward collection.
- **Game States**: Tracks play, pause, win, and lose states with score and enemy count displays.
- **Modular Design**: Backend logic is separated from the UI, making it reusable for other games.
- **Simple UI**: Includes a main menu, instructions, level selection, and win/lose screens.

## Backend Overview

The backend, housed in the `GameLibrary.GL` namespace, is the core of **Prison Escape**, managing all game mechanics. It uses a singleton `Game` class to orchestrate game objects, movement, collisions, and firing, ensuring efficient state management and extensibility.

### Game Logic
- **Singleton Pattern**: The `Game` class (`Game.cs`) is a singleton, instantiated with a `Form` or `Panel`, ensuring a single game instance (`GetInstance`, `ResetInstance`).
- **Game State**: Tracks status (`Play`, `Pause`, `Win`, `Lose`), score, and enemy count, updated via `UpdateGame()`.
- **Win/Lose Conditions**:
  - Win: All enemies are eliminated (`WinCheck`).
  - Lose: Player collides with an enemy.
- **UI Integration**: Displays score and enemy count via `Label` controls on the form.

### Game Objects
- **GameObject Class** (`GameObject.cs`): Represents all entities (players, enemies, bullets, rewards, walls).
  - Properties: `PictureBox` for rendering, `IMovement` for motion, and `ObjectType` enum (`Player`, `Enemy`, `Bullet`, `Reward`, `Wall`).
  - Methods: `Update()` applies movement, `GetPictureBox()` and `GetObjectType()` for rendering and logic.
- **Creation**: Objects are added via `Game.AddGameObject()`, specifying an image, position, movement, and type.

### Movement System
The `Movement` namespace defines movement behaviors through the `IMovement` interface (`Move(Point)`), implemented by:
- **HorizontalMovement** (`HorizontalMovement.cs`): Moves left or right, reversing at boundaries.
- **VerticalMovement** (`VerticalMovement.cs`): Moves up or down, reversing at boundaries.
- **ZigZagMovement** (`ZigZagMovement.cs`): Moves diagonally (e.g., `DiagUpRight`), changing direction at boundaries for a zigzag effect.
- **KeyMovements** (`KeyMovements.cs`): Player movement via arrow keys, constrained by boundaries.
- **FireMovement** (`FireMovement.cs`): Bullet movement in eight directions (cardinal and diagonal) at a fixed speed.

Directions are defined in `Direction.cs` (`Left`, `Right`, `Up`, `Down`, `DiagUpRight`, etc.).

### Collision Detection
- **Collisions Class** (`Collisions.cs`): Implements `ICollision` interface.
  - `CheckCollisions()`: Detects overlaps using `PictureBox.Bounds.IntersectsWith`.
  - `CheckCollisionAction()`: Determines outcomes based on object types, returning an `Action` enum (`RemovePlayer`, `RemoveEnemy`, `RemoveBullet`, `RemoveEB`, `IncreaseScore`, `DoNothing`).
- **Collision Rules**:
  - Player-Enemy: Ends game (`RemovePlayer`).
  - Bullet-Enemy: Removes both (`RemoveEB`), decreases enemy count.
  - Bullet-Wall/Bullet-Reward: Removes bullet (`RemoveBullet`).
  - Player-Reward: Increases score (`IncreaseScore`).
  - Player-Wall/Enemy-Wall: No action (`DoNothing`).

### Firing Mechanism
- **FirePlayer** (`Game.cs`): Triggered by keys (`W`, `A`, `S`, `D`, `Q`, `E`, `Z`, `C`), spawns bullets with `FireMovement`.
- **Bullet Creation**: Uses direction-specific images (e.g., `Resource1.UBullet` for up) and positions bullets relative to the player.
- **Bullet Count**: Tracks active bullets via `bulletCount()`.

The backend is designed for extensibility, allowing new movement patterns, object types, or collision rules to be added easily.

## Frontend Overview

The frontend, in the `Prison_Escape.UI` and `Prison_Escape.UI.GameStates` namespaces, provides the user interface using Windows Forms.

- **Main Menu** (`Main.cs`): Entry point with options to start `Level1`, view `Instructions`, or exit.
- **Instructions** (`Instructions.cs`): Displays game controls and rules, with a back button to the main menu.
- **Level1** (`Level1.cs`): The primary game level, initializing the `Game` instance and adding:
  - One enemy with `HorizontalMovement`.
  - One enemy with `VerticalMovement`.
  - One enemy with `ZigZagMovement`.
  - A player with `KeyMovements`.
  - Game loop via a `Timer` (`Timer1_Tick`), updating the game state every 30ms.
- **Win/Lose Screens** (`WinGame.cs`, `LoseGame.cs`): Displayed when the game ends.
  - Options to return to the main menu or replay `Level1`.
- **Resources**: Images (e.g., `Resource1.GPlayer`, `Resource1.Enemy1`) are embedded for rendering objects.

The UI is simple, focusing on navigation and gameplay display, with maximized windows for an immersive experience.

## Project Structure
- **`GameLibrary/GL/`**:
  - `Game.cs`: Core game logic and singleton.
  - `GameObject.cs`: Base class for entities.
  - `Collision/Collisions.cs`: Collision detection and actions.
  - `Fire/FireMovement.cs`: Bullet movement logic.
  - `Movement/`:
    - `HorizontalMovement.cs`, `VerticalMovement.cs`, `ZigZagMovement.cs`, `KeyMovements.cs`: Movement implementations.
  - `Enum/`:
    - `Direction.cs`, `Action.cs`, `ObjectType.cs`: Enums for game logic.
  - `Interfaces/`:
    - `IMovement.cs`, `ICollision.cs`: Interfaces for movement and collision.
- **`Prison_Escape/`**:
  - `Program.cs`: Application entry point.
  - `Main.cs`: Main menu form.
  - `UI/`:
    - `Level1.cs`: Game level form.
    - `Instructions.cs`: Instructions form.
  - `UI/GameStates/`:
    - `WinGame.cs`, `LoseGame.cs`: Win/lose state forms.
- **`Resources/`**:
  - `Resource1.resx`: Embedded images for player, enemies, bullets, etc.

## Installation
1. **Clone the Repository**:
   ```bash
   git clone https://github.com/aabr2612/OOP-Game.git
   
2. **Open in Visual Studio**:
   - Launch Visual Studio (2019 or later recommended).
   - Select `File > Open > Project/Solution`.
   - Navigate to the cloned repository folder and open the `.sln` file (e.g., `Prison_Escape.sln`).

3. **Verify Dependencies**:
   - Ensure `EZInput` is referenced for keyboard input. If missing, add `EZInput.dll` to `References` in `Solution Explorer`.
   - Confirm `System.Windows.Forms` and `System.Drawing` are included (default in .NET Framework projects).

4. **Build the Solution**:
   - Go to `Build > Build Solution` or press `Ctrl+Shift+B`.
   - Verify that resources (e.g., `Resource1.resx` with images) are included to avoid build errors.

## Usage
1. **Run the Game**:
   - Set `Prison_Escape` as the startup project in `Solution Explorer`.
   - Press `F5` or select `Debug > Start Debugging` to launch the main menu.
2. **Navigation**:
   - **Main Menu**: Click to start `Level1`, view `Instructions`, or exit.
   - **Instructions**: Read controls and return to the main menu.
   - **Level1**: Play the game; win/lose screens offer replay or menu options.
3. **Controls**:
   - **Arrow Keys**: Move the player.
   - **W, A, S, D**: Shoot up, left, down, right.
   - **Q, E, Z, C**: Shoot diagonally (up-left, up-right, down-left, down-right).
4. **Gameplay**:
   - Eliminate enemies by shooting to reduce the enemy count.
   - Collect rewards to increase score.
   - Win by clearing all enemies; lose if an enemy hits the player.
5. **Example Setup** (in `Level1.cs`):
   ```csharp
   game.AddGameObject(Resource1.GPlayer, 10, 10, new KeyMovements(10, new Point(1280, 720), Resource1.GPlayer.Width, Resource1.GPlayer.Height));
   game.AddGameObject(Resource1.Enemy1, 101, 105, new HorizontalMovement(10, new Point(1280, 720), Resource1.Enemy1.Width, Direction.Left));
   
## Dependencies
- .NET Framework 4.8: Required for Windows Forms.
- EZInput: Library for keyboard input handling.
- System.Windows.Forms: Core UI framework.
- System.Drawing: For rendering images.

## Contact
For questions or feedback, feel free to reach out:
- GitHub: [aabr2612](https://github.com/aabr2612)
- Email: aabr2612@gmail.com
