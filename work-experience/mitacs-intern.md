---
layout: page
title: "Mitacs Research Intern - BCIT and Seaspan Shipyards"
permalink: /HenryChen/work-experience/mitacs-intern/
---

**Jan 2026 – Present · Vancouver, BC**
*Supervisor: Mathew Smith*

## The Problem

Seaspan builds large marine vessels and uses robotic arms for welding. For the robot to weld correctly it needs to know exactly where the seam starts and ends in 3D space. Right now that kind of information has to be provided manually which is slow and doesn't really scale. The goal of this project is to automate that part entirely. Point a few cameras at a weld joint sitting on a welding table, and have the system work out the weld coordinates on its own.

## 3D Reconstruction

The core of the pipeline uses [VGGT](https://github.com/facebookresearch/vggt), a model out of Meta Research that takes multiple images of a scene and predicts a dense 3D point cloud from them. You give it three photos of the weld joint taken from different angles and it outputs the full 3D geometry of the scene as a set of world coordinates.

Our script handles the full pipeline. It loads and preprocesses the images, runs VGGT inference to get depth maps and 3D world points for every pixel, and then exports the result as a `.ply` point cloud file you can load into any 3D viewer or drop straight into WeldPath Viz.

## Calibration

The point cloud VGGT produces is in scene units, not real millimetres. That matters because we eventually need to pass coordinates to a robot that operates in actual physical space. To solve this we use the welding table itself as a calibration target. The table has a grid of holes drilled at a known 50mm spacing, so if we can detect those holes in the point cloud we can figure out the scale factor.

The calibration process works like this. First we run RANSAC plane fitting to isolate the flat table surface from the rest of the scene. Then we project those table points into 2D and build a density map of where the points land. The holes in the table show up as low density regions in that map, so we detect them, measure the spacing between them in scene units, and divide that into the known 50mm to get the scale. Once we have it every coordinate gets multiplied through so the final point cloud is in real millimetres.

## WeldPath Viz

To actually inspect reconstructions and pick weld coordinates we built [WeldPath Viz](https://chenry513.github.io/weldpath-viz/visualize_weld.html), a browser based 3D point cloud viewer. You drop in the `.ply` file, rotate and zoom around the scene, then click directly on the point cloud to set a start and end point for the weld. It outputs the X, Y, Z coordinates and direction vector for the seam, and plays back an animated simulation of the weld path so you can check it visually before anything goes near a robot. No installs, runs entirely in the browser.

Below is an example of a reconstructed butt joint `.ply` loaded into the visualizer, with the weld seam start and end points set.

![WeldPath Viz example](/HenryChen/assets/img/blog/weldpath-example.PNG)

## ROS2 Integration (In Progress)

The next step is taking the weld seam coordinates out of the visualizer and getting them into an actual robot control system. We are building a ROS2 node that handles automated start and end point detection, so the seam coordinates get published directly to the robot without needing to go through the manual click workflow in WeldPath Viz. The idea is a fully automated pipeline from raw images to robot-ready weld path with no human in the loop.

## Stack

- **Python** — reconstruction pipeline, RANSAC calibration, PLY export
- **VGGT** (Meta Research) — multi-view 3D reconstruction
- **PyTorch** — model inference
- **ROS2** — robot integration node (in progress)
- **HTML / CSS / JS** — WeldPath Viz, Canvas 2D rendering
