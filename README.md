# Flask + Three.js Demo — Rotating Cube

A minimal example showing how to integrate **Three.js** into a **Flask** application, styled entirely with **Bootstrap 5** (no custom CSS).

![Tech](https://img.shields.io/badge/Flask-000000?logo=flask&logoColor=white)
![Tech](https://img.shields.io/badge/Three.js-black?logo=three.js&logoColor=white)
![Tech](https://img.shields.io/badge/Bootstrap-7952B3?logo=bootstrap&logoColor=white)

## Features

- 🧊 A 3D rotating cube rendered with Three.js
- 💡 Ambient + point lighting for shading
- 🎨 Change the cube's color with a button click
- ⏯️ Pause / resume the rotation animation
- 📐 Responsive canvas that resizes with the browser window
- 🎨 UI built entirely with Bootstrap components — no internal `<style>` tags

## Tech Stack

| Layer      | Technology            |
|------------|-------------------------|
| Backend    | Flask (Python)           |
| Frontend   | HTML, Bootstrap 5        |
| 3D Engine  | Three.js (r128, CDN)     |
| Styling    | Bootstrap utility classes only |

## Project Structure

```
project-root/
├── session2/
│   └── app.py              # Flask application entry point
└── templates/
    └── index.html          # Page markup + Three.js scene logic
```

> Flask's default `render_template()` looks for templates in a `templates/` folder relative to the app's root path. If `app.py` and `templates/` are not siblings, set `template_folder` explicitly when creating the Flask app.

## Prerequisites

- Python 3.8+
- `pip` (Python package manager)

## Installation

1. Clone or download the project files into the structure shown above.
2. Install Flask:

   ```bash
   pip install flask
   ```

## Running the App

From the directory containing `session2/`:

```bash
cd session2
python app.py
```

Then open your browser and navigate to:

```
http://localhost:5000
```

## Code

### `session2/app.py`

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html")

if __name__ == "__main__":
    app.run(debug=True)
```

### `templates/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flask + Three.js Demo</title>

    <!-- Bootstrap CSS (CDN) -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-dark text-light">

    <nav class="navbar navbar-dark bg-black border-bottom border-secondary">
        <div class="container">
            <span class="navbar-brand mb-0 h1">Flask + Three.js Demo</span>
        </div>
    </nav>

    <div class="container my-4">
        <div class="row">
            <div class="col-12">
                <div class="card bg-secondary bg-opacity-25 border-secondary">
                    <div class="card-header">
                        <h5 class="mb-0">Rotating Cube Scene</h5>
                    </div>
                    <div class="card-body p-0">
                        <!-- Three.js will inject a canvas here -->
                        <div id="scene-container" class="w-100" style="height: 500px;"></div>
                    </div>
                    <div class="card-footer d-flex gap-2">
                        <button id="btn-color" class="btn btn-primary">Change Color</button>
                        <button id="btn-toggle" class="btn btn-outline-light">Pause / Resume</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Bootstrap JS Bundle (CDN) -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>

    <!-- Three.js (CDN) -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

    <script>
        // Scene, camera, renderer setup
        const container = document.getElementById('scene-container');

        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(
            75,
            container.clientWidth / container.clientHeight,
            0.1,
            1000
        );
        camera.position.z = 4;

        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(container.clientWidth, container.clientHeight);
        container.appendChild(renderer.domElement);

        // Lighting
        const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
        scene.add(ambientLight);

        const pointLight = new THREE.PointLight(0xffffff, 1);
        pointLight.position.set(5, 5, 5);
        scene.add(pointLight);

        // Cube
        const geometry = new THREE.BoxGeometry(1.5, 1.5, 1.5);
        const material = new THREE.MeshStandardMaterial({ color: 0x0d6efd });
        const cube = new THREE.Mesh(geometry, material);
        scene.add(cube);

        // Animation control
        let isSpinning = true;

        function animate() {
            requestAnimationFrame(animate);
            if (isSpinning) {
                cube.rotation.x += 0.01;
                cube.rotation.y += 0.01;
            }
            renderer.render(scene, camera);
        }
        animate();

        // Handle window resize
        window.addEventListener('resize', () => {
            camera.aspect = container.clientWidth / container.clientHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(container.clientWidth, container.clientHeight);
        });

        // Bootstrap buttons wired to Three.js scene
        document.getElementById('btn-color').addEventListener('click', () => {
            const randomColor = Math.random() * 0xffffff;
            material.color.setHex(randomColor);
        });

        document.getElementById('btn-toggle').addEventListener('click', () => {
            isSpinning = !isSpinning;
        });
    </script>
</body>
</html>
```

## How It Works

- `app.py` is a minimal Flask app that renders `index.html` via `render_template`.
- The page uses only Bootstrap classes for layout and styling (navbar, card, buttons, spacing utilities) — no `<style>` tags anywhere.
- Three.js creates a scene, camera, and renderer, then injects a `<canvas>` into the `#scene-container` div.
- A `MeshStandardMaterial` cube is lit with an ambient light and a point light, then continuously rotated inside a `requestAnimationFrame` loop.
- Two Bootstrap buttons are wired to the Three.js scene:
  - **Change Color** — sets a random hex color on the cube's material.
  - **Pause / Resume** — toggles the `isSpinning` flag that controls the rotation loop.
- A `resize` event listener keeps the camera aspect ratio and renderer size in sync with the container.

## Customization Ideas

- Swap `BoxGeometry` for `SphereGeometry`, `TorusGeometry`, etc. to render different shapes.
- Add `OrbitControls` (via an additional Three.js addon script) to let users rotate/zoom the camera with the mouse.
- Load a texture with `THREE.TextureLoader` instead of a flat color material.
- Add more objects to the scene and animate them independently.

## License

This project is provided as a demo/learning example and free to use or modify.
