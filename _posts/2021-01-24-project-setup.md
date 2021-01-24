---
layout: default
title:  "Project Setup!"
date:   2021-01-24 10:15:33 -0300
---

This is the start of a long journey. Creating a Sandbox MMORPG as a hobby game developer, won't be easy, but challenge is all the joy. That said, I started this devlog in order
to organize my mind and try to have a better focus and mid-term goal on the project, since devlogs helps you to plan and see your overall progress.

If you have any questions, suggestions and such, fell free to contact me: lage.afonso@gmail.com

In order to start the project, we (I like to talk like that) need to make some decisions, like language, engine, architecture and so on.

# Game Design

There is a lot to say about the game design, but since this is a very complex field, I'll explain it in more details later on. For now, let me just explain
some key design choices of the game:
- **MMORPG** - Players have a character, develop it and interact with environment and others players;
- **Sandbox** - Players determine how the world is. Economy, items and even build castles, house, farms and so on. There will be almost none NPCs;
- **2D** - I love the 2D art-style and I think it allows more creative freedom than 3D. Also the VFX in 2D is awesome;
- **Procedural** - This is a sensitive and complex topic, but since I can't create all content by hand, many things like Maps, Monsters and Dungeons will be generated by
algorithms. This allows the game to have almost infinite content, but for a MMORPG, this must be handled with care, to prevent OP veteran players.
- **Instance Based** - I love open world games, like WoW, but this requires huge investment and complexity. So every map will be a instance of a map server.
That way we can have single-threaded services, which is safer and simple, and scale them by having multiples instances. So each map will have at limited number of players,
like 100, but they can interact with each other by social server. Also if the player is in a party, everyone joins on the same instance. 

# Engine

First let's start with engine choice. As far I love [Rust][rust-lang] and also love [Bevy][bevy-engine] it won't be practical or sane to develop a entire Sandbox MMORPG
on a in-development engine, so I had to skip it. My engine of choice is [Unity3D][unity-engine] for the following reasons:
- Easy to use
- Free for indie or small companies[¹][unity-plans]
- Tons of cheap assets
- Easy to find team mates (if someday I'm up to)

Also Unity3D uses C# as scripting language, which I love so for (hated C# in the past, but this has gone after C#7)

# Architecture

Since it's a MMORPG we must have an [authoritative game server][auth-server]. There are another ways of doing so, but I have experience with those kind of servers and I feel much
better having the full control over states in a single source of truth (the server).

Also we'll use a microservice architecture, in order to scale the game. At beginning, we'll have the following services:
- AuthService - The single role is to authenticate users across many sources (like website, launcher, app, client and so on);
- LobbyService - The main role is to allow the character management, display news and redirect to the best MapService available;
- MapService - Here is where the main game logic happens. Each server instance will handle a single map instance. Note that, depending of the capacity of the hardware, we can spawn multiple map server instances, in a single executable. This will be performance-tuning dependent later on.
- SocialService - This server will handle guilds, chats, friends and any social interactions that happens between map servers;
- DBService - This service will communicate with the database. Each Map Server will have it's own db server, but AuthService, LobbyService and social server can share a single DBService.
- ProceduralService - This server will handle procedural generation requests. Since the game is expandable up to infinite, whenever a player reaches a new border of the game, 
the procedural server will start to generate maps, resources, mobs and so on. This is a CPU-Heavy task, so needs to be separated;

In order to create the main logic of the game on the Map Service and aim a good performance, we'll use the ECS Architecture for the main game logic. We'll use the plain old and good
OOP on non-critical services, like Auth, Lobby, etc.

I think it's enough for a start post. Now it's time to search for good libraries and start working on it!

[rust-lang]: https://www.rust-lang.org/
[bevy-engine]:   https://bevyengine.org/
[unity-engine]: https://unity.com/
[unity-plans]: https://store.unity.com/compare-plans
[auth-server]: https://www.gabrielgambetta.com/client-server-game-architecture.html