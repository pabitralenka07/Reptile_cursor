# 🦎 Interactive Reptile Cursor

<p align="center">
  <img src="https://img.shields.io/badge/JavaScript-Vanilla%20ES6+-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript" />
  <img src="https://img.shields.io/badge/HTML5-Canvas%202D-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML5 Canvas" />
  <img src="https://img.shields.io/badge/Dependencies-Zero-success?style=for-the-badge" alt="Zero Dependencies" />
  <img src="https://img.shields.io/badge/License-MIT-blue?style=for-the-badge" alt="MIT License" />
  <img src="https://img.shields.io/badge/PRs-Welcome-brightgreen?style=for-the-badge" alt="PRs Welcome" />
</p>

<p align="center">
  <strong>A mesmerizing, biologically inspired procedural inverse-kinematics (IK) creature simulation that organically tracks your cursor across the screen.</strong>
</p>

<p align="center">
  Featuring dynamic gait cycles, spine curvature, anatomical ribcages, multi-jointed limbs, articulate digits, and fluid locomotion physics—built entirely with pure JavaScript and HTML5 Canvas with <strong>zero external dependencies</strong>.
</p>

---

## 📑 Table of Contents

- [✨ Highlights & Features](#-highlights--features)
- [🎮 Live Demo & Quick Start](#-live-demo--quick-start)
- [🧠 How It Works (Kinematics & Mathematics)](#-how-it-works-kinematics--mathematics)
  - [1. Inverse & Forward Kinematics (`Segment`)](#1-inverse--forward-kinematics-segment)
  - [2. Multi-Joint IK Solver (`LimbSystem`)](#2-multi-joint-ik-solver-limbsystem)
  - [3. Dynamic Gait Engine (`LegSystem`)](#3-dynamic-gait-engine-legsystem)
  - [4. Creature Physics & Locomotion (`Creature`)](#4-creature-physics--locomotion-creature)
- [🦎 Built-in Creature Presets](#-built-in-creature-presets)
- [📂 Project Structure](#-project-structure)
- [⚙️ Customization & Parameters](#-customization--parameters)
- [🕹️ Controls & Interaction](#️-controls--interaction)
- [🌐 Browser Compatibility](#-browser-compatibility)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)
- [👤 Author](#-author)

---

## ✨ Highlights & Features

- 🧬 **Procedural Inverse Kinematics (IK):** Every joint dynamically calculates its angle, flexion, and reach relative to its parent node using trigonometric constraint solvers.
- 🐾 **Biological Gait & Stepping Cycle:** Limbs organically alternate between stance and swing phases, choosing natural footholds based on body velocity, shoulder rotation, and randomized stride variations.
- 🦎 **Procedural Anatomy:** Builds realistic multi-segment spines, vertebrae, ribcages, hips, humerus, forearms, claw digits, and tapered tails.
- ⚡ **Physical Locomotion:** Forward acceleration, rotational momentum, centrifugal turning damping, and friction thresholds simulate genuine biological movement.
- 🎲 **Stochastic Creature Generation:** Procedurally randomizes leg pairs (1 to 12) and proportional tail lengths on every reload for endless variety.
- 📱 **Fully Responsive & Touch-Enabled:** Automatically adapts to any screen resolution and supports touch tracking for mobile and tablet devices.
- 🪶 **Ultra Lightweight:** 100% vanilla code, zero external libraries, zero build steps, and runs at a buttery-smooth 60 FPS.

---

## 🎮 Live Demo & Quick Start

You can run this project locally in seconds without installing any dependencies or packages:

### Method 1: Direct File Launch
Simply clone the repository and double-click `index.html` to open it in your default web browser.

### Method 2: Local HTTP Server

```bash
# 1. Clone the repository
git clone https://github.com/pabitralenka07/Reptile_cursor.git

# 2. Navigate to the project directory
cd Reptile_cursor

# 3. Launch with Python 3
python3 -m http.server 8000

# Or launch with Node.js npx
# npx serve .
```

Open your browser and navigate to `http://localhost:8000`.

---

## 🧠 How It Works (Kinematics & Mathematics)

The simulation relies on hierarchical node chains combining **Forward Kinematics (FK)** and **Inverse Kinematics (IK)**:

```
[Target: Mouse / Touch]
          │
          ▼
   [Creature Head]
          │
    ┌─────┴─────┐
[Spinal Segments] [Limbs / Systems]
    │                 │
[Ribs & Vertebrae] [Humerus -> Forearm -> Digits]
    │                 │
 [Tail Nodes]    [Stance / Swing Footholds]
```

### 1. Inverse & Forward Kinematics (`Segment`)
Each `Segment` maintains:
- `size`: Link length in pixels.
- `relAngle`: Normalized angle relative to its parent:
  $$\Delta\theta = \theta_{rel} - 2\pi \left\lfloor \frac{\theta_{rel} - \theta_{def}}{2\pi} + \frac{1}{2} \right\rfloor$$
- `range` & `stiffness`: Angular constraints that simulate tendon flexibility and prevent joints from folding unnaturally.
- `follow()` & `updateRelative()`: Calculates position $(x, y)$ and angle through trigonometry:
  $$x = x_{parent} + \cos(\theta_{abs}) \cdot \text{size}$$
  $$y = y_{parent} + \sin(\theta_{abs}) \cdot \text{size}$$

### 2. Multi-Joint IK Solver (`LimbSystem`)
Reaches backwards from the end-effector (foot) through the joint chain to the root (hip/shoulder) using iterative constraint satisfaction (FABRIK-style backward-forward adjustment):
- Adjusts each node's position and angle towards the target.
- Propagates forward kinematics down to non-limb children (such as claws and toes).

### 3. Dynamic Gait Engine (`LegSystem`)
Implements a 2-stage stepping state machine:
- **Phase 0 (Stance):** Foot remains locked to its foothold on the ground while the body advances forward.
- **Phase 1 (Swing):** When the hip moves too far from the foot (`reach` threshold exceeded), the foot enters a forward swing phase toward a newly calculated foothold ahead of the hip:
  $$\text{goal} = \text{hip} + \text{reach} \cdot [\cos(\theta_{swing}), \sin(\theta_{swing})] + \mathcal{N}(0, \sigma)$$

### 4. Creature Physics & Locomotion (`Creature`)
Controls body momentum and steering toward the cursor:
- Checks how many feet are grounded before applying forward acceleration:
  $$\text{accel} = a_{forward} \cdot \left(\frac{\sum \text{grounded legs}}{\text{total legs}}\right)$$
- Rotational resistance ($r_{res}$) and friction ($r_{fric}$) prevent instantaneous snaps, giving the creature realistic physical weight and momentum.

---

## 🦎 Built-in Creature Presets

`script.js` includes several procedural creature presets ready to be activated:

| Preset Function | Description |
|---|---|
| `setupLizard(size, legs, tail)` | *(Default)* Realistic multi-legged reptile with flexible neck, articulated ribcage, multi-jointed legs with 4 claws, and a tapered tail. |
| `setupTestSquid(size, legs)` | Radial cephalopod creature with many flexible reaching tentacles that scuttle toward the cursor. |
| `setupTentacle()` | A singular, sinuous tentacle with multi-joint IK reaching dynamically for the cursor. |
| `setupArm()` | A 3-joint robotic arm performing inverse kinematics to follow the cursor. |
| `setupSimple()` | A 128-segment sinuous serpentine chain demonstrating pure forward/inverse follow dynamics. |

To switch presets, simply open [`script.js`](./script.js#L556-L565) and uncomment your desired preset at the bottom of the file:

```javascript
// Example: Switch to a 10-legged cephalopod
setupTestSquid(1.5, 10);
```

---

## 📂 Project Structure

```
Reptile_cursor/
│
├── index.html        # HTML5 container with fullscreen canvas styling
├── script.js         # Core procedural IK engine, kinematics math, & creature generator
├── LICENSE           # MIT open source license
├── .gitignore        # Standard git ignore definitions
└── README.md         # Comprehensive project documentation
```

---

## ⚙️ Customization & Parameters

You can easily tweak the creature's visual appearance and movement characteristics in [`script.js`](./script.js):

### Creature Mechanics:
```javascript
// Creature(x, y, angle, fAccel, fFric, fRes, fThresh, rAccel, rFric, rRes, rThresh)
var critter = new Creature(
  window.innerWidth / 2,   // Initial X
  window.innerHeight / 2,  // Initial Y
  0,                       // Initial angle
  12,                      // Forward acceleration
  1,                       // Forward friction
  0.5,                     // Linear resistance (drag)
  16,                      // Pursuit threshold distance
  0.5,                     // Angular rotational acceleration
  0.085,                   // Rotational friction
  0.5,                     // Rotational resistance
  0.3                      // Angle threshold before turning
);
```

### Canvas Styling:
```javascript
// Modify canvas background or stroke color in script.js:
canvas.style.backgroundColor = "#0a0a14"; // Midnight dark
ctx.strokeStyle = "#00ffcc";              // Cyberpunk cyan glow
ctx.lineWidth = 1.5;                      // Thicker skeletal lines
```

---

## 🕹️ Controls & Interaction

| Interaction | Action |
|---|---|
| **Mouse Move** | The creature turns and crawls towards your cursor position. |
| **Touch Drag** | On mobile/tablet screens, touch and drag anywhere to lead the creature. |
| **Mouse Buttons** | Left, middle, and right click inputs are tracked in `Input.mouse`. |
| **Window Resize** | Canvas automatically adapts to the new viewport size without stretching. |
| **Page Refresh** | Randomizes the number of legs and tail length for a new procedural organism! |

---

## 🌐 Browser Compatibility

Tested and fully supported across all modern web standards:

- ✅ Google Chrome (Desktop & Mobile)
- ✅ Mozilla Firefox (Desktop & Mobile)
- ✅ Apple Safari (macOS & iOS)
- ✅ Microsoft Edge
- ✅ Opera & Brave

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](./LICENSE) file for details.

---

## 👤 Author

**Pabitra Lenka**
- GitHub: [@pabitralenka07](https://github.com/pabitralenka07)

---

<p align="center">
  ⭐ If you enjoyed this project, give it a star on GitHub! ⭐
</p>
