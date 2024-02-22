---
layout: page
title: Feed the animals
subtitle: Learning
---

As part of the [Unity Junior Programmer Pathway](https://learn.unity.com/pathway/junior-programmer), the second tutorial has you creating your first feature rich game. specifically a simple game where animals rush towards you and you need to *feed* them by firing a food item at them.

This tutorial is the first introduction to [Prefabs](https://docs.unity3d.com/Manual/Prefabs.html) which are preconfigured [GameObject](https://docs.unity3d.com/ScriptReference/GameObject.html)s that can be instantiated with settings and components as originally designed.

You can try this game out at the bottom of this page. To reset the game, you'll need to refresh your browser (I've not bothered to implement restart functionality as it's not part of the scope of the tutorial).

## Prefabs

The asset pack provided for the tutorial gave a few options for assets to use in the game.

Each of these prefabs included the following components:

- [Box Collider](https://docs.unity3d.com/Manual/class-BoxCollider.html) surrounding the asset's geometry, *IsTrigger* set to true
- Script; MoveForward.
- Script; DestroyOutOfBounds.

I chose the following and turned them into Prefabs:

### Animal_Doe

![Doe Prefab](/img/Portfolio_FeedTheAnimals/prefab_doe.png)

### Animal_Fox

![Fox Prefab](/img/Portfolio_FeedTheAnimals/prefab_fox.png)

### Animal_Stag

![Stag Prefab](/img/Portfolio_FeedTheAnimals/prefab_stag.png)

### Food

This prefab is a simple cookie and serves as the projectile you're shooting at the animal to feed them.

In addition to the components previously mentioned, this Prefab includes a [Rigidbody](https://docs.unity3d.com/ScriptReference/Rigidbody.html) so that the game can detect when one object touches another.

![Food Prefab](/img/Portfolio_FeedTheAnimals/prefab_food.png)

### Prefab Scripts

There are two scripts that are shared by all Prefab objects in this game - a good example of standard Software Engineering practices such as [DRY (Don't Repeat Yourself)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), [Encapsulation](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)) and [Code Resuability](https://www.opslevel.com/resources/what-is-code-reuse-and-why-is-it-important).

#### MoveForward

The `MoveForward` script is very simple, it simply allows for a GameObjects' [Transform](https://docs.unity3d.com/ScriptReference/Transform.html) to be able to move a specified distance, the specified direction in the specified amount of time (in this case, every second). This uses the [Translate](https://docs.unity3d.com/ScriptReference/Transform.Translate.html) method.

~~~cs
// ./MoveForward.cs
public class MoveForward : MonoBehaviour
{
    public float speed = 40f;

    // Start is called before the first frame update
    void Start() {}

    // Update is called once per frame
    void Update()
    {
        transform.Translate(speed * Time.deltaTime * Vector3.forward);
    }
}
~~~


#### DestroyOutOfBounds

The `DestroyOutOfBounds` script is used to despawn items that have exited the field of play.

~~~cs
// ./DestroyOutOfBounds.cs
public class DestroyOutOfBounds : MonoBehaviour
{
    private float topBound = 35f;
    private float lowerBound = -13f;

    // Start is called before the first frame update
    void Start() {}

    // Update is called once per frame
    void Update()
    {
        if (transform.position.z > topBound)
        {
            Destroy(gameObject);
        }
        
        if (transform.position.z < lowerBound)
        {
            Debug.Log("Game over!");
            Destroy(gameObject);
        }
    }
}
~~~

## The scene itself

![Unity Feed the animals scene](/img/Portfolio_FeedTheAnimals/FeedTheAnimalsScene.png)

There's a bit going on in this scene, so I'll be brief.

### Scene objects

We have the following:

- A Camera.
- Directional Light.
- A grassy plain with left and right borders.
- The Player; he's a farmer.
- A SpawnManager object.

Some parts are ootb, so I won't detail them.

#### The Player

The Player object is quite simple, he's a 3D model with a script attached. The script itself has a few pieces responsibilities:

- Define a maximum horizontal movement speed.
- Define the boundaries for the Player within the scene.
- Actually move the player horizontally in the scene.
- Launch projectiles (Prefabs > Food).

~~~cs
// ./PlayerController.cs
public class PlayerController : MonoBehaviour
{
    public float horizontalSpeed = 10f;
    public float horizontalInput;
    public float bounds = 10f;

    public GameObject projectilePrefab;

    // Start is called before the first frame update
    void Start() {}

    // Update is called once per frame
    void LateUpdate()
    {
        #region Keep player in bounds
        if (transform.position.x < -bounds)
        {
            transform.position = new Vector3(-bounds, transform.position.y, transform.position.z);
        }
        
        if (transform.position.x > bounds)
        {
            transform.position = new Vector3(bounds, transform.position.y, transform.position.z);
        }
        #endregion

        #region Player movement
        horizontalInput = Input.GetAxis("Horizontal");
        transform.Translate(horizontalInput * horizontalSpeed * Time.deltaTime * Vector3.right);
        #endregion

        #region Projectile management
        if (Input.GetKeyDown(KeyCode.Space)) // launch projectile
        {
            Instantiate(projectilePrefab, transform.position, projectilePrefab.transform.rotation);
        }
        #endregion
    }
}
~~~

![Player Object](/img/Portfolio_FeedTheAnimals/object_player.png)

#### The SpawnManager

The `SpawnManager` object is an empty GameObject with a script attached by the same name. This includes the following functionality:

- An array of GameObjects used to store the Prefabs of animals.
- `ValidateSpawnNextAnimal`; this is invoked within the `Start` method, and waits a random number of seconds before setting a boolean signifying that the next frame should spawn an animal; `spawnAnimalNextFrame`.
- The `Update` method simply checks whether a new animal instance should be spawned, and invokes `SpawnRandomAnimal`.
- `SpawnRandomAnimal` surrounds instantiation functionality with exception handling, and finally resets the `spawnAnimalNextFrame` boolean to false, (potentially) ready for next frame.

```cs
// ./SpawnManager.cs
public class SpawnManager : MonoBehaviour
{
    public GameObject[] animalPrefabs;
    public float bounds = 20f;
    public float animalPositionZ = 20f;

    private bool spawnAnimalNextFrame = false;
    public int spawnTimeMin = 500;
    public int spawnTimeMax = 3000;

    // Start is called before the first frame update
    void Start()
    {
        InvokeRepeating(nameof(ValidateSpawnNextAnimal), 2f, 1f);
    }

    void ValidateSpawnNextAnimal()
    {
        int msToWaitForSpawn = UnityEngine.Random.Range(spawnTimeMin, spawnTimeMax);
        _ = TimeSpan.FromMilliseconds(msToWaitForSpawn);

        spawnAnimalNextFrame = true;
    }

    // Update is called once per frame
    void Update()
    {
        if (spawnAnimalNextFrame)
        {
            SpawnRandomAnimal();
        }
    }

    private void SpawnRandomAnimal()
    {
        int animalIndex = UnityEngine.Random.Range(0, animalPrefabs.Length);
        float animalPositionX = UnityEngine.Random.Range(-bounds, bounds);
        Vector3 spawnPosition = new Vector3(animalPositionX, 0, animalPositionZ);

        try
        {
            Instantiate(animalPrefabs[animalIndex], spawnPosition, animalPrefabs[animalIndex].transform.rotation);
        }
        catch (System.Exception ex)
        {
            Debug.Log(ex.Message);
        }
        finally
        {
            spawnAnimalNextFrame = false;
        }
    }
}
```





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
      src="/Builds/FeedTheAnimals/index.html">
  </iframe>
</div>
