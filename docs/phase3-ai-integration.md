Certainly! Below is **Phase 3: AI Integration**, with **step-by-step commands for file creation**, **code snippets**, and **detailed instructions** for each file, fully implemented.

---

### **Full Phase 3: AI Integration Guide**

#### **Step 1: Setting Up AI Environment**

In this phase, we'll integrate AI into the game, enabling NPCs (Non-Player Characters) to navigate, interact, and react based on certain conditions.

---

### **1. Install Required Libraries**

We need some libraries for AI functionality in this phase. Start by installing `brain.js` (for simple neural network AI) and `three.js` (for 3D rendering).

Run the following command in your terminal to install the necessary dependencies:

```bash
npm install three brain.js keyboardjs
```

---

### **2. Create Directories and Files**

Now, let's create the necessary folders and files for AI-related code.

#### **Create the AI folder**

First, create a directory called `ai` within the `src` folder for all AI-related files.

```bash
mkdir -p src/ai
```

#### **Create the NPC File**

Now, create the file `npc.js` inside the `ai` folder where the NPC logic will reside.

```bash
touch src/ai/npc.js
```

#### **Create the Main Game File**

Create the `index.js` file where the main game logic will be handled.

```bash
touch src/index.js
```

---

### **Step 2: Basic NPC Creation**

We’ll now implement the NPC logic, including movement and basic AI for wandering and following the player.

#### **1. Define the NPC Class**

Open the `npc.js` file and write the following code to create an NPC class that moves around the world:

```javascript
// src/ai/npc.js
import * as THREE from 'three';

class NPC {
    constructor(x, y, z) {
        this.position = new THREE.Vector3(x, y, z);
        this.speed = 2; // Speed at which the NPC moves
        this.direction = new THREE.Vector3(1, 0, 0); // Initial movement direction (right)
        
        // Create NPC model (simple sphere for now)
        this.geometry = new THREE.SphereGeometry(1);
        this.material = new THREE.MeshStandardMaterial({ color: 0x00ff00 }); // Green
        this.mesh = new THREE.Mesh(this.geometry, this.material);
        this.mesh.position.set(x, y, z);
    }

    move() {
        // NPC moves in a random direction
        this.position.addScaledVector(this.direction, this.speed * 0.1);
        this.mesh.position.set(this.position.x, this.position.y, this.position.z);
        
        // Randomly change direction
        if (Math.random() < 0.01) {
            this.direction.set(Math.random() - 0.5, 0, Math.random() - 0.5);
            this.direction.normalize();
        }
    }

    getMesh() {
        return this.mesh;
    }
}

export { NPC };
```

#### **2. Update Main File to Add NPC**

Now, open `index.js` and write the following code to create and move the NPC in the game world:

```javascript
// src/index.js
import * as THREE from 'three';
import { NPC } from './ai/npc'; // Import NPC class

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer();

renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

const npc = new NPC(0, 0, 0); // Create NPC at origin
scene.add(npc.getMesh());

camera.position.z = 50;

function animate() {
    requestAnimationFrame(animate);
    npc.move(); // Move NPC
    renderer.render(scene, camera);
}

animate();
```

---

### **Step 3: AI Movement Based on Player Location**

We want the NPC to move towards the player. To achieve this, we’ll calculate the direction from the NPC to the player and make the NPC follow.

#### **1. Update NPC Code to Track Player**

First, let’s modify the `npc.js` file to allow the NPC to track the player’s position.

```javascript
// src/ai/npc.js
import * as THREE from 'three';

class NPC {
    constructor(x, y, z, player) {
        this.position = new THREE.Vector3(x, y, z);
        this.speed = 2;
        this.player = player; // Reference to player

        // Create NPC model (simple sphere)
        this.geometry = new THREE.SphereGeometry(1);
        this.material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });
        this.mesh = new THREE.Mesh(this.geometry, this.material);
        this.mesh.position.set(x, y, z);
    }

    move() {
        // Calculate the direction to the player
        const directionToPlayer = new THREE.Vector3().subVectors(this.player.position, this.position).normalize();
        this.position.addScaledVector(directionToPlayer, this.speed * 0.1);
        this.mesh.position.set(this.position.x, this.position.y, this.position.z);
    }

    getMesh() {
        return this.mesh;
    }
}

export { NPC };
```

#### **2. Update `index.js` to Track Player**

Now, update `index.js` to include player controls and pass the player object to the NPC.

```javascript
// src/index.js
import * as THREE from 'three';
import { NPC } from './ai/npc';
import keyboard from 'keyboardjs'; // Add keyboard handling for player movement

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer();

renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// Player (simple cube for now)
const playerGeometry = new THREE.BoxGeometry(1, 1, 1);
const playerMaterial = new THREE.MeshStandardMaterial({ color: 0x0000ff });
const player = new THREE.Mesh(playerGeometry, playerMaterial);
player.position.set(10, 0, 0); // Initial player position
scene.add(player);

const npc = new NPC(0, 0, 0, player); // Pass player to NPC
scene.add(npc.getMesh());

camera.position.z = 50;

function animate() {
    requestAnimationFrame(animate);
    
    // Update player position (move player with arrow keys)
    if (keyboard.pressed('left')) player.position.x -= 0.1;
    if (keyboard.pressed('right')) player.position.x += 0.1;
    if (keyboard.pressed('up')) player.position.z -= 0.1;
    if (keyboard.pressed('down')) player.position.z += 0.1;

    npc.move(); // NPC moves toward the player
    renderer.render(scene, camera);
}

animate();
```

Make sure to install `keyboardjs` for handling player input:

```bash
npm install keyboardjs
```

---

### **Step 4: Add AI Decision Making Using `brain.js`**

Now, we will introduce a neural network using `brain.js` to allow NPCs to make decisions. For simplicity, we’ll train the NPC to either chase the player or wander randomly.

#### **1. Set Up Neural Network**

In `npc.js`, update the NPC class to incorporate decision-making using the neural network.

```javascript
// src/ai/npc.js
import * as THREE from 'three';
import { NeuralNetwork } from 'brain.js';

class NPC {
    constructor(x, y, z, player) {
        this.position = new THREE.Vector3(x, y, z);
        this.speed = 2;
        this.player = player;

        // Create NPC model (simple sphere)
        this.geometry = new THREE.SphereGeometry(1);
        this.material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });
        this.mesh = new THREE.Mesh(this.geometry, this.material);
        this.mesh.position.set(x, y, z);

        // Initialize AI with a simple neural network
        this.nn = new NeuralNetwork();
        this.nn.train([
            { input: [0, 1], output: [1] }, // Move towards the player
            { input: [1, 0], output: [0] }, // Wander around
        ]);
    }

    move() {
        // Calculate distance to the player
        const distance = this.position.distanceTo(this.player.position);
        
        // Use neural network to decide whether to chase or wander
        const input = [distance < 10 ? 0 : 1, distance < 10 ? 1 : 0];
        const output = this.nn.run(input);

        // If the network tells us to chase, move towards the player
        if (output > 0.5) {
            const directionToPlayer = new THREE.Vector3().subVectors(this.player.position, this.position).normalize();
            this.position.addScaledVector(directionToPlayer, this.speed * 0.1);
        } else {
            // Otherwise, wander randomly
            const randomDirection = new THREE.Vector3(Math.random() - 0.5, 0, Math.random() - 0.5).normalize();
            this.position.addScaledVector(randomDirection, this.speed * 0.1);
        }

        this.mesh.position.set(this.position.x, this.position.y, this.position.z);
    }

    getMesh() {
        return this.mesh;
    }
}

export { NPC };
```

---

### **Step 5: Final Testing**

After integrating AI decision-making, you can test the NPCs. The NPC should now make intelligent decisions based on its distance from the player. If the NPC is close enough, it should chase the player. If it's far away, it will wander randomly.

---

### **Conclusion**

You’ve now successfully integrated basic AI into your game, with NPCs that can move around, chase the player, and make decisions using simple neural networks.

