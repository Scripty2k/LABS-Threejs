# Functional Design Document (FDD)
# Project Name: Scripty2k House – 3D Portfolio Experience

## Goal: 

Create an interactive 3D house or environment using Three.js where users can move around and explore different rooms, each showcasing part of your creative work (music, films, visuals, etc.).

## Objectives:
•	A stylized 3D house (can be low-poly or minimal aesthetic)
•	First-person movement (WASD + mouse look or click to teleport)
•	Clickable items that open projects (music players, video previews, links, etc.)
•	Multiple “rooms” themed for:
o	Music
o	Film/Cinematography
o	Visual Design/Graphics
o	Experiments (TouchDesigner, Three.js, etc.)
•	Subtle ambient effects (lighting, music, shaders maybe)
•	Optional: Day/night toggle or light switches for mood

## User Interactions:
•	Click on certain objects (e.g. a poster, vinyl, TV) to open popups or links
•	Optional: Interact with light switches, play music in rooms

## Visuals:
•	Minimal, dreamy, or vaporwave-inspired interior
•	Posters, furniture, and screens as interactive elements
•	Audio-reactive stuff for extra flair (maybe later)








## Technical Design Document (TDD)

### Tech Stack:
•	three.js
•	GLTF/GLB models for the house and assets (Blender export)
•	Javascript (vanilla or with Vite)
•	HTML/CSS for overlay UI
•	Optional: tweakpane or gui for dev tools/debug
•	Optional: howler.js for music control

