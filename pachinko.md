---
layout: page
title: Pachinko
subtitle: Learning
---

As part of the [Unity Essentials Pathway](https://learn.unity.com/pathway/unity-essentials), the second tutorial has you creating a simple 2D scene reminiscent of *Pachinko*, which uses various standard 2D components to bounce a *ball* around obstacles, trying to reach the boxes at the bottom.

This scene doesn't *do* anything except use gravity/physics to make the *ball* fall and respond to hitting other objects.

The sprites chosen are simply a ball of ice, apples, boxes and a ground texture. The latter two aren't anything special.

![Unity Pachinko Scene](/img/Portfolio_Pachinko/Scene.png)

The ball of ice has the following components:

- Sprite Renderer (default)
- Rigidbody 2D
- Capsule Collider 2D (wrapped around the sprite)

![Pachinko ball of ice components](/img/Portfolio_Pachinko/Ball of ice components.png)

The apples have the following components:

- Sprite Renderer (default)
- Capsule Collider 2D

![Apple components](/img/Portfolio_Pachinko/Apple components.png)

<div>
  <iframe id="Pachinko"
      title="Pachinko"
      width="960"
      height="600"
      src="/Builds/Pachinko/index.html">
  </iframe>
</div>
