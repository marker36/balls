<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Reactive 3D Background</title>
    <style>
        /* Base page styling */
        body {
            margin: 0;
            overflow: hidden; /* Prevents scrollbars from canvas pushing the layout */
            background-color: #050505;
            font-family: 'Helvetica Neue', Arial, sans-serif;
        }

        /* The 3D canvas stays fixed in the background */
        #canvas-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: 1;
        }

        /* Overlay content stays above the canvas */
        .content {
            position: relative;
            z-index: 2;
            color: white;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            /* Crucial: Allows the mouse to interact with the canvas behind the text */
            pointer-events: none; 
        }

        h1 {
            font-size: 4rem;
            letter-spacing: 3px;
            margin: 0;
            text-shadow: 0 4px 20px rgba(0, 0, 0, 0.8);
        }

        p {
            font-size: 1.2rem;
            color: #888888;
            letter-spacing: 1px;
            margin-top: 15px;
        }
    </style>
</head>
<body>
    
    <div id="canvas-container"></div>
    
    <div class="content">
        <h1>Interactive Depth</h1>
        <p>Move your mouse to shift the perspective.</p>
    </div>

    <!-- Load Three.js from CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    
    <script>
        // 1. Scene Setup
        const container = document.getElementById('canvas-container');
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
        
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(window.devicePixelRatio); // Ensures sharp rendering on high-res displays
        container.appendChild(renderer.domElement);

        // 2. Generate 3D Particles
        const particlesGeometry = new THREE.BufferGeometry();
        const particlesCount = 4000;
        const posArray = new Float32Array(particlesCount * 3); // 3 coordinates (x,y,z) per particle

        for(let i = 0; i < particlesCount * 3; i++) {
            // Spread particles across a wide 3D space (-5 to +5 on each axis)
            posArray[i] = (Math.random() - 0.5) * 10;
        }

        particlesGeometry.setAttribute('position', new THREE.BufferAttribute(posArray, 3));
        
        const material = new THREE.PointsMaterial({
            size: 0.005,
            color: 0x00d2ff, // Neon blue
            transparent: true,
            opacity: 0.8,
            blending: THREE.AdditiveBlending // Creates a glowing effect when particles overlap
        });

        const particlesMesh = new THREE.Points(particlesGeometry, material);
        scene.add(particlesMesh);

        camera.position.z = 3; // Pull camera back to see the particles

        // 3. Track Mouse Movement
        let mouseX = 0;
        let mouseY = 0;
        let targetX = 0;
        let targetY = 0;
        
        const windowHalfX = window.innerWidth / 2;
        const windowHalfY = window.innerHeight / 2;

        document.addEventListener('mousemove', (event) => {
            mouseX = (event.clientX - windowHalfX);
            mouseY = (event.clientY - windowHalfY);
        });

        // 4. Animation and Reactivity Loop
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);
            const elapsedTime = clock.getElapsedTime();

            // Apply constant slow rotation
            particlesMesh.rotation.y = elapsedTime * 0.05;
            particlesMesh.rotation.x = elapsedTime * 0.02;

            // Apply reactive rotation based on mouse position
            targetX = mouseX * 0.001;
            targetY = mouseY * 0.001;
            
            // The easing effect (0.05) creates a smooth lag behind the cursor
            particlesMesh.rotation.y += 0.05 * (targetX - particlesMesh.rotation.y);
            particlesMesh.rotation.x += 0.05 * (targetY - particlesMesh.rotation.x);

            renderer.render(scene, camera);
        }

        animate();

        // 5. Handle Window Resizing
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>
</html>
