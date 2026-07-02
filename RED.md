<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Luxe Auto | 3D Experience</title>
    <style>
        /* Luxury Dark Theme CSS */
        body { 
            margin: 0; 
            font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; 
            background-color: #0a0a0a; 
            color: #ffffff; 
            overflow-x: hidden; 
        }
        
        header { 
            padding: 2.5rem; 
            text-align: center; 
            background: linear-gradient(180deg, #1a1a1a 0%, #0a0a0a 100%); 
            border-bottom: 1px solid #d4af37; /* Gold accent */
        }
        
        h1 { 
            margin: 0; 
            font-weight: 300; 
            letter-spacing: 8px; 
            color: #d4af37; 
            text-transform: uppercase; 
        }
        
        p.subtitle { 
            color: #888; 
            letter-spacing: 3px; 
            font-size: 0.9rem; 
            text-transform: uppercase;
            margin-top: 10px;
        }
        
        /* 3D Canvas Container */
        #canvas-container { 
            width: 100vw; 
            height: 65vh; 
            display: block; 
            cursor: grab;
        }
        
        /* Features Section */
        .features { 
            display: flex; 
            justify-content: center; 
            gap: 4rem;
            padding: 4rem 2rem; 
            background-color: #0a0a0a; 
            flex-wrap: wrap;
        }
        
        .feature { 
            text-align: center; 
            max-width: 300px; 
        }
        
        .feature h3 { 
            color: #d4af37; 
            letter-spacing: 2px;
            font-weight: 400;
        }
        
        .feature p {
            color: #cccccc;
            line-height: 1.6;
        }
    </style>
</head>
<body>

    <header>
        <h1>Luxe Auto</h1>
        <p class="subtitle">Experience Elegance in 3D</p>
    </header>

    <div id="canvas-container"></div>

    <section class="features">
        <div class="feature">
            <h3>Aerodynamic Silhouette</h3>
            <p>Engineered for minimal drag and maximum performance, allowing you to cut through the air with absolute silence.</p>
        </div>
        <div class="feature">
            <h3>Bespoke Materials</h3>
            <p>Crafted with the finest metallic alloys, carbon fiber, and a commitment to uncompromised luxury.</p>
        </div>
    </section>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    
    <script>
        // 1. Core Setup: Scene, Camera, and Renderer
        const scene = new THREE.Scene();
        scene.background = new THREE.Color('#0a0a0a'); // Dark background
        scene.fog = new THREE.Fog('#0a0a0a', 15, 40);  // Fades the floor into the background

        const container = document.getElementById('canvas-container');
        const camera = new THREE.PerspectiveCamera(45, window.innerWidth / (window.innerHeight * 0.65), 0.1, 1000);
        camera.position.set(12, 6, 14); // Position camera diagonally above
        camera.lookAt(0, 1, 0);

        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight * 0.65);
        renderer.shadowMap.enabled = true;
        renderer.shadowMap.type = THREE.PCFSoftShadowMap; // Softer, premium shadows
        container.appendChild(renderer.domElement);

        // 2. Premium Lighting Setup (Crucial for luxury metallic look)
        const ambientLight = new THREE.AmbientLight(0xffffff, 0.3); // Soft base light
        scene.add(ambientLight);

        const dirLight = new THREE.DirectionalLight(0xffffff, 0.8); // Main bright light
        dirLight.position.set(10, 15, 10);
        dirLight.castShadow = true;
        dirLight.shadow.mapSize.width = 1024;
        dirLight.shadow.mapSize.height = 1024;
        scene.add(dirLight);

        const goldLight = new THREE.PointLight(0xd4af37, 1.5, 50); // Gold studio accent light
        goldLight.position.set(-10, 5, -10);
        scene.add(goldLight);

        const blueLight = new THREE.PointLight(0x4444ff, 1, 50); // Cool blue accent light
        blueLight.position.set(10, -5, 10);
        scene.add(blueLight);

        // 3. Build the 3D Concept Car
        const carGroup = new THREE.Group();

        // High-end materials
        const paintMaterial = new THREE.MeshStandardMaterial({ 
            color: 0x050505, 
            roughness: 0.1, // Very glossy
            metalness: 0.8  // High metallic finish
        });
        const glassMaterial = new THREE.MeshStandardMaterial({ 
            color: 0x111111, 
            roughness: 0.0, 
            metalness: 0.9, 
            transparent: true, 
            opacity: 0.8 
        });
        const rubberMaterial = new THREE.MeshStandardMaterial({ color: 0x111111, roughness: 0.9 });
        const rimMaterial = new THREE.MeshStandardMaterial({ color: 0xdddddd, roughness: 0.2, metalness: 1.0 });

        // Chassis (Body)
        const bodyGeo = new THREE.BoxGeometry(9, 1.2, 3.8);
        const body = new THREE.Mesh(bodyGeo, paintMaterial);
        body.position.y = 1.2;
        body.castShadow = true;
        carGroup.add(body);

        // Cabin (Roof/Glass)
        const cabinGeo = new THREE.BoxGeometry(4.5, 0.9, 3.2);
        const cabin = new THREE.Mesh(cabinGeo, glassMaterial);
        cabin.position.set(-0.8, 2.2, 0);
        cabin.castShadow = true;
        carGroup.add(cabin);

        // Wheels
        const wheelGeo = new THREE.CylinderGeometry(0.8, 0.8, 0.5, 32);
        wheelGeo.rotateX(Math.PI / 2);
        const rimGeo = new THREE.CylinderGeometry(0.5, 0.5, 0.52, 16);
        rimGeo.rotateX(Math.PI / 2);

        function createWheel(x, z) {
            const wheelGroup = new THREE.Group();
            const tire = new THREE.Mesh(wheelGeo, rubberMaterial);
            tire.castShadow = true;
            const rim = new THREE.Mesh(rimGeo, rimMaterial);
            wheelGroup.add(tire);
            wheelGroup.add(rim);
            wheelGroup.position.set(x, 0.8, z);
            return wheelGroup;
        }

        carGroup.add(createWheel(-2.8, 2));   // Front Left
        carGroup.add(createWheel(-2.8, -2));  // Front Right
        carGroup.add(createWheel(2.8, 2));    // Rear Left
        carGroup.add(createWheel(2.8, -2));   // Rear Right

        scene.add(carGroup);

        // 4. Showroom Floor
        const floorGeo = new THREE.PlaneGeometry(200, 200);
        const floorMat = new THREE.MeshStandardMaterial({ 
            color: 0x050505, 
            roughness: 0.2,
            metalness: 0.5
        });
        const floor = new THREE.Mesh(floorGeo, floorMat);
        floor.rotation.x = -Math.PI / 2; // Lay it flat
        floor.receiveShadow = true;
        scene.add(floor);

        // 5. Animation Loop (Turntable effect)
        function animate() {
            requestAnimationFrame(animate);
            
            // Slowly rotate the car to show off the angles and reflections
            carGroup.rotation.y -= 0.003;

            renderer.render(scene, camera);
        }
        animate();

        // 6. Keep layout responsive on window resize
        window.addEventListener('resize', () => {
            const width = window.innerWidth;
            const height = window.innerHeight * 0.65;
            renderer.setSize(width, height);
            camera.aspect = width / height;
            camera.updateProjectionMatrix();
        });
    </script>
</body>
</html>
