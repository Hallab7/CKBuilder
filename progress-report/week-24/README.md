# Week 24 - October 20-26 2026

Twenty-fourth week of the CKBuilder program. Documented **Flashfire**, an original browser-based 2D jetpack arena shooter built with TypeScript, Vite, and an HTML5 game engine, and its publication on UTXOPlay.

The public Flashfire repository was created and implemented on October 28, after this nominal reporting window, and the UTXOPlay route was verified on October 29. This is therefore a retrospective project report: it uses Flashfire for Week 24 as requested without moving its public source or publication dates.

## What I Did

### Built the Browser Game Foundation

Structured Flashfire as a TypeScript and Vite application on an HTML5 game runtime.

The game foundation includes:

- A dedicated arena scene and browser application bootstrap
- A match state machine for menu, countdown, play, pause, round end, and results
- A typed event bus for match, combat, weapon, and pickup updates
- Fixed-step timing helpers
- Seeded random selection for repeatable systems
- Separated content definitions for weapons, grenades, maps, modes, and difficulty
- A production build with the game engine isolated into its own browser chunk

This keeps game rules and content data testable outside the full rendering loop.

### Implemented the Jetpack Combat Loop

Built the core single-player arena-shooter experience around fast horizontal movement and fuel-limited vertical control.

The combat loop covers:

- Jetpack movement, dropping, aiming, and firing
- Health, armor, and jet-fuel resources
- Projectile speed, gravity, spread, falloff, splash damage, and recoil
- Magazine, reserve-ammo, reload, and weapon-switching rules
- Grenade throwing and radial damage
- Death, respawn, scoring, match completion, and rematch
- HUD feedback for player resources, score, timer, weapons, ammunition, and results
- Random weapon, health, armor, and fuel supply drops

Players start with the M9 and can carry two weapons. A nearby third weapon requires an explicit replacement choice instead of silently discarding the active loadout.

### Added Six Game Modes

Implemented distinct match rules for:

- Quick Deathmatch
- Team Deathmatch
- Survival
- Gun Ladder
- Control Point
- Training Range

Each mode defines its own duration, score target, objective behavior, progression rule, and result condition. Training provides untimed weapon metrics, while Survival and Control Point use different completion logic from elimination modes.

### Created Eight Arenas

Added eight playable maps with separate dimensions, route shapes, palettes, ambience, and environmental mechanics:

- Outpost Kilo
- Drydock 12
- Ember Foundry
- Canopy Ruins
- Frost Relay
- Neon Underpass
- Sky Fortress
- Blacksite Zero

The map system loads Tiled JSON data, collision layers, player and bot spawns, pickup points, hazards, and map-specific effects. Later arenas unlock through local progression rather than exposing every map immediately.

### Built the Arsenal and Inventory System

The public repository defines eleven weapons across sidearm, SMG, rifle, shotgun, LMG, sniper, launcher, and energy categories.

Each weapon has explicit values for:

- Damage and pellet count
- Fire cadence and mode
- Magazine and reserve ammunition
- Reload duration
- Projectile behavior
- Standing, moving, and airborne spread
- Recoil
- Damage falloff
- Movement multiplier
- Splash radius where applicable

The inventory system supports automatic collection of the second weapon, explicit replacement after both slots are full, dropped-weapon recovery, and gun-ladder progression.

### Added Five AI Difficulty Levels

Implemented Recruit, Regular, Veteran, Elite, and Flashpoint bot profiles.

Difficulty changes more than enemy health. Profiles tune:

- Reaction time
- Aim error and tracking speed
- Dodge and grenade probability
- Pickup awareness and hearing radius
- Flanking and retreat behavior
- Jetpack efficiency
- Team coordination
- Damage and health multipliers
- Memory duration
- Score multiplier

Navigation, perception, and bot-decision modules keep those behaviors separate from the arena scene.

### Supported Desktop, Gamepad, and Mobile Input

Added keyboard and mouse, gamepad, and landscape-mobile controls.

The control layer includes:

- Rebindable keyboard actions
- Mouse aiming and firing
- Analog gamepad movement and aiming
- Dedicated mobile movement and aim controls
- Touch firing, reload, weapon switching, and pause
- Portrait-orientation blocking
- Input clearing when focus, pointer capture, or orientation changes

A merged mobile fix also hardened held-input cleanup and browser-storage fallback behavior.

### Added Local Progression and Player Settings

Implemented browser persistence for progression and settings without requiring an account or remote profile.

Stored data covers:

- Rank and unlock progress
- Map availability
- Control bindings
- Audio and graphics settings
- Accessibility preferences
- Touch-control sizing

The save repository falls back safely when browser storage is blocked instead of preventing the game from starting.

### Built Release and Quality Tooling

Added automated checks for source quality, content completeness, browser behavior, and deployable artifacts.

The repository includes:

- ESLint and TypeScript checks
- Vitest unit coverage
- Content validation for maps and weapons
- Playwright desktop, mobile, cross-browser, accessibility, visual, performance, and soak suites
- Hidden production source maps
- Content-freeze hashes
- Release artifact fingerprints
- Cache and security-header checks
- Separate deployable-site and private-source-map outputs

### Published Flashfire on UTXOPlay

Verified that [the Flashfire UTXOPlay route](https://store.utxoplay.io/games/flashfire/) returns a hosted Flashfire 1.0.0 game shell with UTXOPlay return and fullscreen controls.

The hosted page, main game bundle, stylesheet, Outpost Kilo map JSON, and map artwork returned HTTP 200 during verification. This establishes that UTXOPlay is serving a Flashfire package rather than only linking to the GitHub repository.

The hosted package does not expose a source commit. Its visible default match text also differs from current `main`, so the store publication and current GitHub revision are reported as separate evidence. Hosting on UTXOPlay is not presented as proof that Flashfire itself performs wallet signing or on-chain gameplay actions.

### Verified the Public Revision and Recorded Gaps

Validated the clean public GitHub revision `dddc0dcbc26ec4ed00a1d9d31729b2fdb4c10561`, excluding unrelated local uncommitted work.

The public revision passed:

- ESLint
- 67 unit tests across 11 test files
- Content validation for 11 weapons and 8 maps
- TypeScript checks
- Production Vite build

The complete Playwright run reported 33 passed, 5 failed, and 7 skipped tests. An isolated rerun cleared the mobile mode-result and camera checks, but reproduced three gaps:

- Vertical arrow-key movement on desktop Chromium
- Vertical arrow-key movement on mobile Chromium
- Desktop damaging-hazard death assertion

The release gate also stops because the current difficulty rebalance was not added to the committed content-freeze manifest. From this verification environment, the large hosted game-engine bundle did not finish loading within the browser timeout, so the report confirms package publication but does not claim a completed live play session on the UTXOPlay copy.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| Flashfire | Original browser-based jetpack arena shooter with six modes, eight maps, eleven weapons, five AI difficulty levels, desktop/gamepad/mobile controls, local progression, release tooling, and a hosted UTXOPlay build | [GitHub](https://github.com/Hallab7/Flashfire), [UTXOPlay](https://store.utxoplay.io/games/flashfire/) |

## Key Concepts Learned

- How game scenes, typed systems, and content definitions divide rendering from game rules
- Why fixed-step timing and seeded randomness improve repeatable game tests
- How weapon balance combines damage, cadence, spread, falloff, reload, mobility, and ammunition
- Why game modes need separate scoring and completion rules instead of UI-only labels
- How map data can drive collisions, spawns, pickups, hazards, and progression locks
- Why AI difficulty should change perception and behavior as well as numeric health
- How a two-slot inventory creates meaningful pickup choices without an inventory menu
- Why touch controls need pointer ownership and cleanup across rotation and capture loss
- How blocked browser storage should degrade gracefully for a local-first game
- Why a hosted game route, a source revision, and a green release gate are different evidence
- How content-freeze manifests prevent unnoticed release drift but must be updated with intentional balance changes
- Why publishing through a CKB-oriented game store does not by itself prove wallet or on-chain integration
