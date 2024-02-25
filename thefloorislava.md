---
layout: page
title: The floor is lava
subtitle: Learning
---

As part of the [Unity Essentials Pathway](https://learn.unity.com/pathway/unity-essentials), you must create a simple physics based scene where the goal is to ensure that the ball doesn't touch the *lava*.

The ball itself consists simply of a [Sphere](https://docs.unity3d.com/Packages/com.unity.probuilder@4.0/manual/Sphere.html) with a [Material](https://docs.unity3d.com/ScriptReference/Material.html).

![Sphere with material that looks like Jupiter](/img/Portfolio_FloorIsLava/dat_u_jupiter.png)

A [Physic Material](https://docs.unity3d.com/Manual/class-PhysicMaterial.html) set to *bouncy*.

![Bouncy physic material](/img/Portfolio_FloorIsLava/bouncy.png)

And a default [Rigid Body](https://docs.unity3d.com/560/Documentation/Manual/class-Rigidbody.html).

![Components of a Sphere object](/img/Portfolio_FloorIsLava/components.png)

The rest of the scene is made up of a [Plane](https://docs.unity3d.com/ScriptReference/Plane-ctor.html) with another Material applied, and a series of [Cubes](https://docs.unity3d.com/Packages/com.unity.probuilder@4.2/manual/Cube.html) that look like ramps for the ball to travel down.

The final part of the tutorial consists of publishing the finished scene online, which is what I'm doing here.

**Important: ** This is intentionally not interactive.

<div>
  <iframe id="TheFloorIsLava"
      title="The Floor Is Lava"
      width="100%"
      height="700"
      src="/Builds/TheFloorIsLava/index.html">
  </iframe>
</div>
