# Game Description
## Intro
This is the final project of ECE243 Computer Organization. Inspired by [Operius](https://gx.games/games/8z54je/operius/), It is implemented on the NIOS II core of a DE1-SOC.
## Title Screen
![title_screen](https://github.com/GaryZhous/StarWrap/blob/main/title_screen.png)
- When the game first starts, the title screen will load and a background audio will play.
- Press the “Enter” key on the PS2 keyboard to start the game.

## Game
<table>
  <tr>
    <td><img src="https://github.com/GaryZhous/StarWrap/blob/main/game_play1.png" alt="Image 1" width="400" /></td>
    <td><img src="https://github.com/GaryZhous/StarWrap/blob/main/game_play2.png" alt="Image 2" width="400" /></td>
  </tr>
</table>

- **Controls**:
  - Control the player UFO by using the “A” and “D” keys on the PS2 Keyboard, which will move the UFO left and right, respectively, in a parabolic curve.
  - To shoot bullets at the enemy UFOs (note that they will be aimed down the tunnel and thus towards the middle of the screen), press the “Space” bar on the PS2 keyboard.
- **Enemy UFOs**:
  - Enemy UFOs will be destroyed if hit by a player bullet, after which they will respawn and the score will increase by 1 point. The score is shown on the hex display.
  - Enemies will shoot bullets at a certain rate, and if they hit you, you will lose one life or health point (HP).
- **Lives**:
  - You have a maximum of 3 lives, represented by hearts in the top right corner of the VGA. If you lose a life, you will lose one heart.
- **Goal**:
  - The goal of the game is to get as high of a score as possible without losing all 3 lives.

## Gameover Screen
![Game_Over](https://github.com/GaryZhous/StarWrap/blob/main/game_over.png)
- If you lose all 3 lives, you have lost the game! A gameover transition sound will play and the VGA will draw the game over screen.
- A background gameover song will then play until you press the “Enter” key to restart the game, which will bring you back to the title screen.

# Implemented Features

## VGA
- Double buffering is used for all elements drawn on the VGA.
  - Includes background images, background tunnel lines, player and enemy images, bullets, and player health icons.
- Background images and player/enemy images are stored as C arrays.
- The two sets of moving tunnel lines are offset by 80 frames, with animation determined by edge cases depending on line start and end points.
  - Each set restarts from the middle of the screen only once all lines have reached the outer edges of the screen for seamless animation.
- All clearing functions are optimized to clear only the previous locations of each element drawn on the screen with the background image at those specific pixel locations, instead of redrawing the entire background.

## PS2 Keyboard for Control
- Polling is used to detect key presses.
  - Only the last key press is read to avoid complications with multiple keys pressed simultaneously.
  - “Enter” key to start/restart the game.
  - “A” and “D” keys to control the player character moving in a curve, as well as moving the background to emulate the sense of rotating in a tunnel.
  - “Space” key to shoot bullets.

## Sound/Music
- Different verses of music are played for the start and end scenes.
- Polling of the PS2 keyboard occurs every time writing to the audio FIFO, allowing pseudo-interrupt functionality where the user can press a key while audio is playing.
  - This automatically stops the current audio and transitions to the next game state.
- A transition sound is played right when the player loses all 3 lives to denote the game loss.
  - A simpler audio playback function is used to prevent endless looping.

## Bullets
- **Player’s Bullets**:
  - Move from the player’s current position to the middle of the screen, where they disappear.
- **Enemies’ Bullets**:
  - Spawn at the enemy’s current position and move to the bottom of the screen.
- Both player’s and enemies’ bullets use double buffers for smooth movement.
- Enemies are destroyed when their hitboxes are touched by player bullets, and the score is incremented by 1.
- The player loses one life when its hitbox is touched by enemies’ bullets.

## Enemies
- Enemy UFOs move along line y = 120, bouncing back and forth between a limited range of x-coordinates.
- Upon being hit by player bullets, enemies respawn at the center of the screen.

## Player HP Display
- Maximum 3 hearts are drawn at the top right corner of the screen.
- If the player character is hit by enemies’ bullets, one heart is lost.
- Once all three hearts are lost, the game-over condition is met, and the end scene will appear.

## Score (Displayed on Hex Display)
- A long array is used to map every number to the segments of the hex display.
- The display can handle scores up to a maximum of 99, at which point the user has beaten the game.
