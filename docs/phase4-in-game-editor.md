Certainly! Here’s **Phase 4**: **Dynamic Combat System** with **detailed code**, **file creation commands**, and **step-by-step instructions** for each file. This includes the exact lines of code and where they should be placed in each file. 

---

## **Phase 4: Dynamic Combat System**

In this phase, we'll implement a dynamic combat system where players can attack, take damage, and interact with NPCs. We'll handle health, attack mechanics, and combat animations.

---

### **Step 1: Install Dependencies**

To manage key inputs for attacking and movement, and for better control of the player and NPC, install the necessary libraries.

```bash
npm install three keyboardjs
```

---

### **Step 2: Create the Required Files and Directories**

Let’s first create the files and directories needed for this phase.

#### **1. Create Directories**

We’ll create a `combat` directory for combat-related files and `ai` for NPC logic.

```bash
mkdir -p src/combat
mkdir -p src/ai
```

#### **2. Create the Files**

Now, we will create the following files:

- `combat.js` (for combat logic like attacking and damage).
- `weapon.js` (to define weapons).
- `player.js` (for the player's movement and combat logic).
- `npc.js` (for NPC combat behavior).
- `index.js` (for the main game logic).

```bash
touch src/combat/combat.js
touch src/combat/weapon.js
touch src/ai/player.js
touch src/ai/npc.js
touch src/index.js
```

---

### **Step 3: Create Combat Logic**

#### **1. Define the Weapon Class**

The `weapon.js` file will define the weapon class, which contains information about the weapon, like its name and damage.

**File:** `src/combat/weapon.js`

```javascript
// src/combat/weapon.js

class Weapon {
    constructor(name, damage) {
        this.name = name; // Name of the weapon (e.g., Sword)
        this.damage = damage; // Amount of damage dealt
    }
}

export { Weapon };
```

---

#### **2. Create Player Class**

In this class, the player will have health, an attack system, and movement capabilities. The player will be able to use weapons to deal damage to NPCs.

**File:** `src/ai/player.js`

```javascript
// src/ai/player.js
import * as THREE from 'three';
import { Weapon } from '../combat/weapon'; // Import Weapon class

class Player {
    constructor(x, y, z) {
        this.position = new THREE.Vector3(x, y, z);
        this.health = 100; // Player starts with 100 health
        this.attackDamage = 25; // Player's attack damage
        this.speed = 2; // Player movement speed

        // Create Player Model (simple box)
        this.geometry = new THREE.BoxGeometry(2, 3, 1);
        this.material = new THREE.MeshStandardMaterial({ color: 0x0000ff });
        this.mesh = new THREE.Mesh(this.geometry, this.material);
        this.mesh.position.set(x, y, z);

        // Initialize weapon (add a sword as the player's weapon)
        this.weapon = new Weapon('Sword', 25); // Weapon deals 25 damage
    }

    move() {
        // Implement player movement logic here
    }

    attack(target) {
        // Check if player can attack target (e.g., within range)
        if (this.mesh.position.distanceTo(target.position) < 5) {
            console.log('Attacking target!');
            target.takeDamage(this.weapon.damage); // Call the target's takeDamage method
        }
    }

    takeDamage(amount) {
        this.health -= amount;
        if (this.health <= 0) {
            this.die();
        }
    }

    die() {
        console.log('Player has died!');
        // Handle player death (reset health, respawn, etc.)
    }

    getMesh() {
        return this.mesh;
    }
}

export { Player };
```

---

#### **3. Create NPC Class**

Now let’s define the `NPC` class. The NPC will have health, and it will be able to take damage from the player when attacked.

**File:** `src/ai/npc.js`

```javascript
// src/ai/npc.js
import * as THREE from 'three';

class NPC {
    constructor(x, y, z, player) {
        this.position = new THREE.Vector3(x, y, z);
        this.health = 50; // NPC starts with 50 health
        this.speed = 2;
        this.player = player;

        // Create NPC Model (simple sphere for now)
        this.geometry = new THREE.SphereGeometry(2);
        this.material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });
        this.mesh = new THREE.Mesh(this.geometry, this.material);
        this.mesh.position.set(x, y, z);
    }

    move() {
        // NPC moves towards the player (simple logic)
        const directionToPlayer = new THREE.Vector3().subVectors(this.player.position, this.position).normalize();
        this.position.addScaledVector(directionToPlayer, this.speed * 0.1);
        this.mesh.position.set(this.position.x, this.position.y, this.position.z);
    }

    takeDamage(amount) {
        this.health -= amount;
        console.log(`NPC Health: ${this.health}`);
        if (this.health <= 0) {
            this.die();
        }
    }

    die() {
        console.log('NPC has died!');
        // Handle NPC death (e.g., remove from the game, respawn, etc.)
    }

    getMesh() {
        return this.mesh;
    }
}

export { NPC };
```

---

### **Step 4: Main Game Logic**

In this section, we'll manage the player and NPC logic in the `index.js` file. We'll also handle player input and combat logic here.

**File:** `src/index.js`

```javascript
// src/index.js
import * as THREE from 'three';
import { Player } from './ai/player';
import { NPC } from './ai/npc';
import keyboard from 'keyboardjs'; // Add keyboard handling for player actions

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer();

renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// Create player and NPC
const player = new Player(0, 0, 0); // Player at origin
const npc = new NPC(10, 0, 0, player); // NPC at some distance from the player

scene.add(player.getMesh());
scene.add(npc.getMesh());

camera.position.z = 50;

// Key input handling for player attack
keyboard.bind('space', () => {
    player.attack(npc); // Attack NPC when space is pressed
});

function animate() {
    requestAnimationFrame(animate);

    // Player and NPC movement logic here (move player with arrow keys)
    if (keyboard.pressed('left')) player.mesh.position.x -= 0.1;
    if (keyboard.pressed('right')) player.mesh.position.x += 0.1;
    if (keyboard.pressed('up')) player.mesh.position.z -= 0.1;
    if (keyboard.pressed('down')) player.mesh.position.z += 0.1;

    npc.move(); // NPC moves toward the player
    renderer.render(scene, camera);
}

animate();
```

---

### **Step 5: Testing the Combat System**

To test the combat system:

1. **Movement**: Use the arrow keys to move the player.
2. **Attack**: Press the space bar to attack the NPC.
3. **Damage**: Observe the NPC's health decrease as the player attacks.
4. **Death**: When the NPC’s health reaches 0, the NPC will "die" (logged in the console).

---

### **Conclusion**

With this setup, you now have a basic combat system where the player can attack NPCs, deal damage, and watch NPCs die when their health reaches 0. 

---

### **Summary of Files**

1. **`src/combat/weapon.js`** – Contains the `Weapon` class that defines attack damage.
2. **`src/ai/player.js`** – Contains the `Player` class with health, attack, and movement.
3. **`src/ai/npc.js`** – Contains the `NPC` class that includes health and movement.
4. **`src/index.js`** – Manages the player input, NPC movement, and renders the scene.

---

This is the **fully built-out Phase 4** with all necessary steps and detailed guidance on what files to create and where to place each line of code!