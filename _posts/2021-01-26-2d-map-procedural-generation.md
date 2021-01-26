---
layout:	posts
title:	2D Map Procedural Generation #1
date:	2021-01-26 17:11:03 -0300
---

As stated on [previous post][setup-project], most of content of Skyon will be procedural generation. Today I'll talk about the 2D Map Procedural Generation, using noises and
other techniques in order to have a great and immersive 2D Map.

Please note this is the first post about this topic, but as move on on project and enhance it, I'll create others topics and update this one with the links.

# Heightmap

![Heightmap generated with SimplexNoise](/assets/2/heightmap.png)

We'll use a [heightmap][wiki-heightmap] to represent our map, since it's easier to deal with. On a heightmap, each pixel represents the actual height on terrain on the map, zero begin
the lowest number and 1 the highest number. If we use this info to create a landscape, we can have some nice maps.  Since a 2d map is just a grid with a width and a height, nothing special,
and each tile (if it was a 3D map, we would use voxel terminology) represents a piece of land on this map. We can map the 2D Map and the Heightmap (but they must have the same size) and by doing so,
we can also map the individual pixel height to a specific tile. 

Let's say for instance we are creating an island map. Usually a island has the lowest point as the ocean and the highest point some mountain or volcano on the middle. So if we map to the lower points on
heightmap (0) be water and higher points (1) be mountain, we can end with a nice and soft generated map landscape (and using some tricks that I'll show bellow).

![2D Map generated from a heightmap](/assets/2/heightmap_final.png)

# Noise Algorithm

Choosing the right algorithm is a key-factor on any procedural generation content. We'll use a [SimplexNoise][wiki-simplex] algorithm, which is an improved version of [Perlin Noise][wiki-perlin], but Perlin Noise can work fine too.
Later on we'll be using also a [Voronoi Diagram][wiki-voronoi] to represent our biome, but it's a topic for later on future.

SimplexNoise (and also perlin) has some parameters to mess with. I'll list some parameters and show how this can affect our generated content. Please note that the following parameters are my personal understanding of the parameters.
- **Coordinates** (X, Y) - This is the main seed of the SimplexNoise "randomness". If you enter the same coordinates, you will get the same base noise;
- **Frequency** - Controls the "zoom" of the noise. Higher numbers means more noise and, for the sake of this post, more irregular terrain;
- **Fractal Octaves** - In this case, the fractal is just some kind of "layer" on top of base noise. The Octaves means how many "loops" the fractal will have. If you have only one fractal octave, this means you "over layer" is disabled.
- **Fractal Lacunarity** - This is the same "zoom" as the frequency, but only affects the fractal layer;
- **Fractal Gain** - Acts like an alpha of the fractal layer. Zero means only base noise will be visible, 100 means only fractal layer will be visible.

I'll show bellow how those values affects the generated noise. All examples uses the same coordinates (1, 1).

![Frequency_2_heightmap](/assets/2/heightmap_frequency2.png)
- Frequency: 2
- Fractal Octaves: 1 (disabled)


![Frequency_3_heightmap](/assets/2/heightmap_frequency3.png)
- Frequency: 3
- Fractal Octaves: 1 (disabled


![Frequency_2_oct_2_g_05_heightmap](/assets/2/heightmap_frequency2_oct2_g05.png)
- Frequency: 2
- Fractal Octaves: 2
- Fractal Lacunarity: 10
- Fractal Gain: 0,5


![Frequency_2_oct_2_g_3_heightmap](/assets/2/heightmap_frequency2_oct2_g3.png)
- Frequency: 2
- Fractal Octaves: 2
- Fractal Lacunarity: 10
- Fractal Gain: 3

As you can imagine, by tweaking those parameters, we can have very different maps.

# Borders

If we just apply some tile-mapping on the current heightmap, the result will be a map without borders, so players can leave the map. In Skyon, this won't be allowed, unless the player exit in some specifics points. But how can we add some nice
borders on our map? The answer is we'll just create some nice frame and apply it over our heightmap. Since the heightmap is just a picture, we can modify it as we want.

So given a heightmap like this:

![Borderless](/assets/2/borderless.png)

If we just create a simple border, like this one:

![Border_only](/assets/2/border_only.png)

And merge both together, we end up having a nice heightmap with borders:

![Border](/assets/2/border.png)

Also, we can instead of subtract (black borders) we can add to the height, and having nice mountain borders:

![Border_mountain](/assets/2/border_mountain.png)
![Border_mountain_color](/assets/2/border_mountain_colour.png)

I'll not add any core here, because this is very straightforward, just create a rect where you don't wanna "paint" the borders, and whenever you are inside that rect, skip the paoint.

# Connections

Players need to enter and leave somehow. I'll call those connections, where is just "connections" with other maps. You can also think of entrance, exit or even portals. Now that we know
our height map is just an image, it's easy to manipulate it and add our connections.

For the sake of simplicity, since we are going to revisit the map generation later on, we'll just add some random squares on the edge of the map. You can just set the square for any arbitrary
value, like 0.5 (which will be the middle height of the map), or blend with the already existing values, creating a smooth square. I'll do the later on.

The logic is also very straightforward, just choose some random corner of the map, create a square with a determined size and blend it's value with 0.5.

![Connections](/assets/2/connections.png)

![Connections_blend](/assets/2/connections_blend.png)

![Connections_colored](/assets/2/connections_colored.png)

# Everything together

When you combine all those techniques you can have some nice maps. There are still room for improvements, like sometimes the connections is placed over rocks or on the ocean, but we'll address
those issues later on the project.

I'll show some nice random combination so you can see the potential behind this method of procedural 2d map generation.

![example_2](/assets/2/example_2.png)
![example_3](/assets/2/example_3.png)
![example_4](/assets/2/example_4.png)
![example_5](/assets/2/example_5.png)

That's all for today. See you on the next post!

[setup-project]: https://afonsolage.github.io/2021/01/24/project-setup.html
[wiki-heightmap]: https://en.wikipedia.org/wiki/Heightmap
[wiki-simplex]: https://en.wikipedia.org/wiki/Simplex_noise
[wiki-perlin]: https://en.wikipedia.org/wiki/Perlin_noise
[wiki-voronoi]: https://en.wikipedia.org/wiki/Voronoi_diagram