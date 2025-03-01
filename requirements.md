# Zombie Survival Game Requirements

## Premise
You are a player in a world infested with zombies. Your goal is to survive as long as possible.

### Player
- Can move around a limited-sized map (approximately 5 minutes to explore)
- Moves faster than zombies
- Has health that decreases when attacked
- Can pick up weapons found throughout the map

### Zombies
- Move slowly toward the player
- Perform melee attacks that cause small damage
- Can attack in groups, causing significant damage
- Simple AI that follows the player

### Map/Environment
- Limited size but large enough for exploration
- Contains random objects (trees, obstacles)
- Weapons scattered throughout

### Weapons
- Melee: Simple bat for close combat
- Ranged: Pistol with unlimited ammo

### Perspective
- Third-person view (not FPS)
- Camera from slightly elevated position (similar to classic Pokémon games)
- Top-down or isometric-like perspective

## Technical Requirements

### Technology Stack
- HTML, CSS, JavaScript
- Three.js for 3D rendering
- Single HTML file implementation

### Implementation Guidelines
- Single player only (no multiplayer functionality)
- Browser-based (no server requirements)
- Minimal code approach for MVP
- No external dependencies beyond Three.js

## Game Components Breakdown

### Player
- Controllable via keyboard input
- 3D model representation
- Health tracking
- Weapon inventory system

### Zombies
- Position tracking in 3D world
- Movement AI toward player
- Attack mechanics
- Health system

### Environment
- Ground plane
- Static objects (trees)
- Defined boundaries

### Weapons
- Pickup mechanics
- Different attack patterns
- Visual representations

### Camera
- Follows player movement
- Fixed angle (elevated view)

### User Interface
- Health indicator
- Start/Restart game button
- Game over screen

### Game Logic
- Movement systems
- Collision detection
- Combat mechanics
- Game over conditions

## Function Documentation

### Setup Functions

#### `init()`
- Initializes the game environment, camera, and scene
- Creates player, zombies, weapons, and environment
- Sets up event listeners for user input
- Starts the animation loop

#### `createScene()`
- Creates the Three.js scene
- Sets up the renderer with appropriate size and properties
- Configures lighting for the scene

#### `setupCamera()`
- Creates and positions the camera
- Sets appropriate perspective for top-down/isometric view
- Configures camera to follow player

### Creation Functions

#### `createPlayer()`
- Creates player's 3D model
- Sets initial position and properties
- Adds player to the scene
- Returns the player object

#### `createZombie(position)`
- Creates a zombie 3D model
- Sets position (random if not specified)
- Configures zombie properties (speed, health, damage)
- Adds zombie to scene and zombies array
- Returns the zombie object

#### `createWeapon(type, position)`
- Creates weapon 3D model based on type ('bat' or 'pistol')
- Sets position and properties
- Adds weapon to scene and weapons array
- Returns the weapon object

#### `createTree(position)`
- Creates tree 3D model
- Sets position
- Adds tree to scene and trees array
- Returns the tree object

#### `createGround()`
- Creates ground plane
- Sets appropriate size based on MAP_SIZE
- Adds ground to scene
- Returns the ground object

### Input Handling

#### `setupEventListeners()`
- Sets up keydown and keyup event listeners
- Updates keys object to track pressed keys

### Game Logic Functions

#### `animate()`
- Main game loop using requestAnimationFrame
- Calls update functions for game entities
- Handles collision detection
- Renders the scene

#### `updatePlayer()`
- Updates player position based on keyboard input
- Handles player movement constraints within map boundaries
- Updates camera position to follow player
- Returns updated player position

#### `updateZombies()`
- Updates all zombies' positions
- Implements zombie AI to move toward player
- Checks if zombies are close enough to attack
- Triggers zombie attacks when appropriate

#### `checkWeaponPickup()`
- Checks proximity between player and weapons
- Handles weapon pickup logic
- Updates player's currentWeapon
- Removes picked-up weapon from scene

#### `playerAttack()`
- Checks if attack key is pressed
- Determines attack type based on currentWeapon
- Triggers appropriate attack function

#### `handlePlayerAttack()`
- Implements attack logic based on weapon type
- Checks for hits on zombies
- Applies damage to zombies
- Handles zombie death if health reaches zero

#### `zombieAttack(zombie)`
- Implements zombie attack logic
- Reduces player health
- Updates health display
- Checks if player health reaches zero

#### `checkCollisions()`
- Detects collisions between player and obstacles
- Prevents player from moving through obstacles
- Detects when zombies are close enough to attack player
- Simple boolean check rather than returning detailed collision data

#### `gameOver()`
- Handles end-of-game state
- Displays game over message
- Stops game loop
- Shows restart button (same as initial start button)

### UI Functions

#### `updateHealthBar()`
- Updates health bar display based on player's current health
- Changes color based on health level (green/yellow/red)

#### `showStartScreen()`
- Displays the start game screen with instructions
- Shows start game button

#### `hideStartScreen()`
- Hides the start screen when game begins

#### `startGame()`
- Initializes or resets game state
- Hides start screen
- Begins game loop