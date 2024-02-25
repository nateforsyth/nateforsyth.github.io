---
layout: page
title: Feed the animals
subtitle: Learning
---

As part of the [Unity Junior Programmer Pathway](https://learn.unity.com/pathway/junior-programmer), the final quiz for the `Create with Code 1` module tutorial has you modifying an existing scene.

This is the first introduction to using a script to generate a [Primitive Object](https://docs.unity3d.com/510/Documentation/Manual/PrimitiveObjects.html).

You can view this scene just below (scroll down). There's no UI functionality, however you can reload this page to see a different cube each time (more detail below).

## Mod The Cube Challenge

<div>
  <iframe id="ModTheCubeChallenge"
      title="Mod The Cube Challenge"
      width="960"
      height="600"
      src="/Builds/ModTheCubeChallenge/index.html">
  </iframe>
</div>

# What was done

The instructions were provided as requirements.

>Now that you understand what the cube script is doing, you can begin to customize it. As previously mentioned, all of the changes the script is making to the cube are specified in the code. It would be easier to control the cube from the Unity Editor instead of editing the script. You can make this possible by changing variables to be public. Public variables appear in the Inspector window where you can modify their values.
>
>- Open the cube.cs script in your code editor and observe the code.
>- Identify at least two changes that you can make to the cube’s behavior.
>
>Here are a few examples of modifications you might consider:
>
>  - Change the cube's location (transform).
>  - Change the cube's scale.
>  - Change the angle at which the cube rotates.
>  - Change the cube’s rotation speed.
>  - Change the cube’s material color.
>  - Change the cube’s material opacity.
>
>If you’re feeling confident, you can attempt the following as well:
>
>  - Modify any of the changes above so they change randomly each time the scene is played.
>  - Add extra functionality to the cube. For example, how might you change the color of the cube over time?   
>
>- Implement your planned changes in the code.
>- Test the scene regularly as you work to ensure your code is performing as expected.  

## The provided script

The provided script was pretty bare-bones, and simply applied a grey colour to a cube of a specific scale at a specific location, and made it rotate around its X axis.

```cs
// ./Cube.cs
public class Cube : MonoBehaviour
{
    public MeshRenderer Renderer;
    
    void Start()
    {
        transform.position = new Vector3(3, 4, 1);
        transform.localScale = Vector3.one * 1.3f;
        
        Material material = Renderer.material;
        
        material.color = new Color(0.5f, 1.0f, 0.3f, 0.4f);
    }
    
    void Update()
    {
        transform.Rotate(10.0f * Time.deltaTime, 0.0f, 0.0f);
    }
}
```

## My implementation

I decided to randomise the following every time the scene loads.

- Cube scale (1.3-5).
- Rotation angle X (0-3).
- Rotation angle Y (0-3).
- Rotation angle Z (0-3).
- Rotation speed (0-3).

Colour shift interval is validated every second, and then the actual colour is changed every 1-10 seconds.

- Red tint.
- Green tint.
- Blue tint.
- Alpha.

I left the position as is.

Here's an example of this scene running.

![Modified cube](/img/Portfolio_ModTheCube/Screenshot.png)

```cs
// ./Cube.cs
public class Cube : MonoBehaviour
{
    public MeshRenderer Renderer;

    public Vector3 position = new(3, 4, 1);
    private float localScale;
    private float rotatationAngleX;
    private float rotatationAngleY;
    private float rotatationAngleZ;
    private float rotationSpeed;

    public int colorShiftMinS = 1;
    public int colorShiftMaxS = 10;
    private bool changeColorNextFrame = false;
    private Material material;
    
    void Start()
    {
        localScale = UnityEngine.Random.Range(1.3f, 5.0f);
        rotatationAngleX = UnityEngine.Random.Range(0f, 3.0f);
        rotatationAngleY = UnityEngine.Random.Range(0f, 3.0f);
        rotatationAngleZ = UnityEngine.Random.Range(0f, 3.0f);
        rotationSpeed = UnityEngine.Random.Range(0f, 3.0f);

        transform.position = position;
        transform.localScale = Vector3.one * localScale;
        
        material = Renderer.material;        
        material.color = GenerateRandomColor();

        InvokeRepeating(nameof(ValidateColorShift), 1f, 1f);
    }
    
    void Update()
    {
        transform.Rotate(rotatationAngleX * Time.deltaTime * rotationSpeed, rotatationAngleY, rotatationAngleZ);

        if (changeColorNextFrame)
        {
            // generate random colour        
            material.color = GenerateRandomColor();

            changeColorNextFrame = false;
        }
    }

    void ValidateColorShift()
    {
        int delayInS = UnityEngine.Random.Range(colorShiftMinS, colorShiftMaxS);
        _ = TimeSpan.FromSeconds(delayInS);

        changeColorNextFrame = true;
    }

    Color GenerateRandomColor()
    {
        float r = UnityEngine.Random.Range(0f, 1f);
        float g = UnityEngine.Random.Range(0f, 1f);
        float b = UnityEngine.Random.Range(0f, 1f);
        float a = UnityEngine.Random.Range(0f, 1f);

        Color color = new(r, g, b, a);

        return color;
    }
}
```
