Okay, let's break down the implementation into a step-by-step approach.  This will make it easier to build and debug the game incrementally. We'll start with the absolute basics and add features gradually.

**Step 1: Basic HTML Structure and Three.js Setup**

*   **Create the HTML file:** Create a new file named `zombie_game.html`.
*   **Basic HTML:** Add the basic HTML structure (`<!DOCTYPE html>`, `<html>`, `<head>`, `<body>`).
*   **Include Three.js:** Add a `<script>` tag to include the Three.js library from a CDN (just like the reference code).
*   **Add a Start Button:**  Include a `<button>` element with the text "Start Game" within the `<body>`. Give it an `id` (e.g., `startButton`) so we can access it with JavaScript. Initially set the style `display: block;` for visibility.
*   **Add a Canvas Placeholder:** Add a `<div>` that will hold game messages (like "Game Over"). Initially hidden.
*   **Embedded CSS:** Add a `<style>` tag within the `<head>` for basic CSS (reset margins, hide overflow, style the canvas and start button).
*   **Embedded JavaScript:** Add an empty `<script>` tag *after* the Three.js script tag. This is where we'll write our game code.
*   **Initialize Three.js:** Inside the `<script>` tag:
    *   Create a `scene`, `camera`, and `renderer`.
    *   Set the renderer size to the window size.
    *   Append the renderer's DOM element (`renderer.domElement`) to the `<body>`.
    *   Define basic constants like `MAP_SIZE`.
    * Add a very simple `init()` and `animate()` function. The `init` function, for now, should just contain the camera, scene, and renderer initialization.
    *   Add a placeholder `startGame()` function. For now this function simply calls `init()`.
    * Add an event listener to the start button, and call `startGame` on click and make the button disappear on click.
    *   The `animate()` function should contain `requestAnimationFrame(animate);` and `renderer.render(scene, camera);`.
    * Call `startGame()` at the end of your script, *for now*.

**Goal of Step 1:**  When you open `zombie_game.html` in a browser, you should see a black screen (the Three.js canvas) and the Start Game Button, clicking on which should cause the black screen only. No errors in the browser's console.

**Step 2: Create the Ground and Player**

*   **`createGround()` function:**
    *   Create a `PlaneGeometry` with dimensions based on `MAP_SIZE`.
    *   Create a `MeshBasicMaterial` (or `MeshLambertMaterial` if you add lighting later) with a color (e.g., green for grass).
    *   Create a `Mesh` using the geometry and material.
    *   Rotate the plane to be horizontal (`rotation.x = -Math.PI / 2`).
    *   Add the mesh to the `scene`.
*   **`createPlayer()` function:**
    *   Create a simple geometry (e.g., a `BoxGeometry` or `CapsuleGeometry`). A box is the easiest.
    *   Create a `MeshBasicMaterial` with a color (e.g., blue for the player).
    *   Create a `Mesh` using the geometry and material.
    *   Set the player's initial position (e.g., `position.set(0, 1, 0)` – slightly above the ground).
    *   Add the mesh to the `scene`.
    *   Assign the mesh to the global `player` variable.
*   **Call these functions in `init()`:** Call `createGround()` and `createPlayer()` within the `init()` function.

**Goal of Step 2:** You should now see a green ground plane and a blue box (the player) in the center of the screen.

**Step 3: Implement Player Movement**

*   **Keyboard Input:**
    *   Create a `keys` object (e.g., `const keys = {};`) outside of any function.
    *   Add event listeners for `keydown` and `keyup`:
        ```javascript
        window.addEventListener('keydown', (event) => {
            keys[event.code] = true;
        });
        window.addEventListener('keyup', (event) => {
            keys[event.code] = false;
        });
        ```
*   **`updatePlayer()` function:**
    *   Create an `updatePlayer()` function.
    *   Inside the function, check the `keys` object for pressed keys (e.g., `keys['KeyW']`, `keys['KeyA']`, `keys['KeyS']`, `keys['KeyD']`).
    *   Based on the pressed keys, update the player's `position.x` and `position.z` (remember, `y` is up).
    *   Add a small movement speed constant (e.g., `const playerSpeed = 0.1;`). Multiply the direction by this speed.
    *   **Important:** Add basic boundary checks to keep the player within the `MAP_SIZE`.
*   **Call `updatePlayer()` in `animate()`:** Add `updatePlayer();` to the `animate()` function, *before* `renderer.render()`.

**Goal of Step 3:** You should be able to move the blue box (the player) around the green ground plane using the WASD keys. The player should not be able to move off the map.

**Step 4: Camera Follow**

*   **Camera Setup (in `init()` or a separate `setupCamera()` function):**
    *   Position the camera *behind* and *above* the player.  For example:
        ```javascript
        camera.position.set(0, 10, 15); // 10 units up, 15 units back
        camera.lookAt(new THREE.Vector3(0, 0, 0)); // Look at the center of the scene (where the player starts)
        ```
*   **Update Camera in `updatePlayer()`:**
    *   After updating the player's position, update the camera's position to follow the player. Maintain the relative offset.  For example:
        ```javascript
        camera.position.set(player.position.x, 10, player.position.z + 15);
        camera.lookAt(player.position);
        ```

**Goal of Step 4:**  The camera should now smoothly follow the player as you move it around the map, maintaining a top-down, slightly angled perspective.

**Step 5: Add Zombies**

*   **`createZombie()` function:**
    *   Similar to `createPlayer()`, create a zombie 3D model (e.g., a red box).
    *   Give each zombie a random position within the `MAP_SIZE`.
    *   Add each zombie to the `scene` and to the `zombies` array.
    * Add properties for speed and health to each zombie
*   **`updateZombies()` function:**
    *   Create an `updateZombies()` function.
    *   Iterate through the `zombies` array.
    *   For each zombie:
        *   Calculate the direction vector from the zombie to the player (`player.position.clone().sub(zombie.position)`).
        *   Normalize the direction vector (`.normalize()`).
        *   Multiply the normalized direction by the zombie's speed (a small constant, e.g., `0.05`).
        *   Add this result to the zombie's position.
*   **Call these functions:**
    *   In `init()`, create several zombies (e.g., in a loop, call `createZombie()` 5-10 times).
    *   In `animate()`, call `updateZombies()` before rendering.

**Goal of Step 5:**  You should see multiple red boxes (zombies) on the map. They should slowly move towards the player.

**Step 6: Basic Collision Detection (Zombie-Player)**

*   **`checkCollisions()` Function (Simple Version):**
    *   Iterate through the `zombies` array.
    *   For each zombie, calculate the distance between the zombie and the player (`player.position.distanceTo(zombie.position)`).
    *   If the distance is less than a threshold (e.g., `1`), consider it a collision.
        *   For now, just `console.log("Collision!");` when a collision occurs.

* **Call in `animate()`**
    * Call the function `checkCollisions()`

**Goal of Step 6:**  The console should log "Collision!" when a zombie gets close to the player. This is a placeholder for the attack logic.

**Step 7: Implement Basic Attacks and Health**
*   **Add `playerHealth` variable.** Initialize it to 100.
* **`zombieAttack(zombie)`:**
    *   Reduce `playerHealth` by a small amount (e.g., 5).
    *  `console.log("Player health:", playerHealth);`
    * Add an if statement, that if player health goes below 0, call `gameOver()` function
* **Modify `checkCollisions` to attack:**
    *  Instead of the console.log, call `zombieAttack(zombie);`
*   **`gameOver()` function:**
    *   `console.log("Game Over!");`
    * Stop animation: use a boolean `gameRunning = true;` and wrap the content of your `animate()` function inside `if(gameRunning) { ... }`. Set `gameRunning` to `false` in your `gameOver` function.
    * Make the start game button appear again

**Goal of Step 7:** When zombies collide with the player, the player's health decreases. When health reaches 0, "Game Over!" is logged, and the game stops, and the button appears again.

**Step 8: Add a Health Bar (UI)**

*   **HTML:** Add a `<div>` with `id="healthBar"` to your HTML. Position it absolutely (e.g., top-left corner).  Give it a background color (e.g., red) and an initial width (representing 100% health).
*   **CSS:** Style the `#healthBar` to look like a health bar (height, border, etc.).
*   **`updateHealthBar()` function:**
    *   Get the `#healthBar` element using `document.getElementById('healthBar')`.
    *   Set its `style.width` to be proportional to `playerHealth` (e.g., `healthBar.style.width = playerHealth + '%';`).
    *   (Optional) Change the background color based on health (green > yellow > red).
*   **Call `updateHealthBar()` in `animate()`:**

**Goal of Step 8:** A visible health bar appears on the screen and decreases as the player takes damage.

**Step 9: Add Trees (Static Obstacles)**

*   **`createTree()` function:**
    *   Create a simple tree model (e.g., a green cylinder or a combination of shapes).
    *   Set a random position within the `MAP_SIZE`.
    *   Add the tree to the `scene` and to the `trees` array.
*   **Call `createTree()` in `init()`:** Create multiple trees in a loop.
* **Modify checkCollisions()**:
    * Calculate the distance to each tree, using the same approach as with zombies.
    * Prevent the player from moving through the trees by not applying the position change when collision is true.
    

**Goal of Step 9:** Trees are scattered around the map, and the player cannot walk through them.

**Step 10: Add Weapons (Pickup and Use)**

*   **`createWeapon(type, position)` function:**
    *   Create a 3D model for each weapon type ('bat', 'pistol').  A simple box with different colors is fine for the MVP.
    *   Set the weapon's position.
    *   Add the weapon to the `scene` and to the `weapons` array.
*   **Call `createWeapon()` in `init()`:** Place a few weapons on the map.
*   **`checkWeaponPickup()` Function**
    * Check the distance between each weapon and the player
    * Pick up weapon if player distance is close enough
    * Set `currentWeapon`
    * Remove the picked up weapon from the scene
* **`playerAttack()` Function**
    * Check for a keypress (e.g., spacebar).
    * If pressed and currentWeapon is not 'none', then attack.
* **`handlePlayerAttack()`**
    * If `currentWeapon` is 'bat', check for zombies within a short range and reduce their health.
    * If `currentWeapon` is 'pistol', fire a projectile (you could simplify this by just checking for zombies in a line in front of the player and damaging the closest one). Don't implement projectile motion at first, just do a direct hit check.
* **`updateZombies()` modifications**
    * Add zombie health, similar to `playerHealth`.
    * Remove zombies from scene and array when their health is 0.

**Goal of Step 10:** The player can pick up weapons, and use them to defeat the zombies.

This step-by-step approach, starting with the simplest elements and gradually adding complexity, is the best way to build your game. Remember to test frequently and debug as you go. This detailed breakdown should make the coding process much more manageable.
