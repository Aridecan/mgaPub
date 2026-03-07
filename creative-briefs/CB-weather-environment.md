# Creative Brief — Weather & Environment

---

## Overview

Weather and environment in MGA are not cosmetic. Rain reduces visibility, masks footsteps, and makes rooftops slippery. Snow slows movement and leaves trackable footprints. Fog halves perception range and turns every alley into potential cover. Thunderstorms darken the sky, drown out combat sounds, and — uniquely — let Arri call down targeted lightning strikes. The weather system makes the same city feel different every day, and rewards the player who adapts to conditions rather than ignoring them.

All environmental data is deterministic and driven by the almanac library. Sun position, moon positions, weather condition, and temperature are computed per hour from orbital mechanics and a seeded hash. Every player on the same in-game date sees the same weather at the same time. The almanac is the single source of truth — the rendering and gameplay systems consume its output, they do not generate their own.

The almanac currently provides sun/moon positions, sunrise/sunset with six twilight phases, weather conditions (Sunny through Snow), per-hour temperature, moon illumination fractions, and seasonal determination. Three extensions are needed: wind speed/direction, thunderstorm as a distinct condition, and fog/mist as a distinct condition. These are library-level changes, not creative brief scope, but the brief references them as data dependencies.

Weather rendering must respect the anime art style established in [Art Direction](../gdd/art-direction.md). The cel shader's 4-tone banding, the 98-entry colour palette, and the geometry-based outline system all constrain how weather effects are rendered. Post-processing is avoided where a shader or particle solution exists. Rain is particles, not a full-screen overlay. Fog is volumetric, not a depth-based post-process blur.

**Related documents:**

- [Almanac Library](../../almanac/) — data source for all environmental values
- [Art Direction](../gdd/art-direction.md) — cel shader, palette, material system
- [Audio Direction](../gdd/audio-direction.md) — ambient audio pillar, weather-driven soundscapes
- [Core Loop](../gdd/core-loop.md) — 24-hour clock, time advancement
- [CB-traversal](CB-traversal.md) — movement affected by weather/traction
- [CB-combat](CB-combat.md) — ranged accuracy, visibility
- [CB-exploration](CB-exploration.md) — Perception/Search affected by conditions
- [CB-camera](CB-camera.md) — CAMERA drone in weather
- [Character Pipeline](../gdd/character-pipeline.md) — Chaos Cloth for wind interaction
- [Terridyn Planet](../../mgaPriv/world/terridyn-planet.md) — planetary parameters, moons, auroras
- [Difficulty](../gdd/difficulty.md) — weather gameplay effects may scale with difficulty

---

## Use Cases

### UC1 — Day-Night Cycle

**Actor:** The system
**Goal:** Transition lighting, sky colour, and ambient audio to match the sun's position throughout the 24-hour cycle

The almanac provides exact sun azimuth and altitude for any hour, plus eight twilight time events per day (sunrise, sunset, and civil/nautical/astronomical twilight start and end). These values drive the entire lighting pipeline directly — no approximation or interpolation layer sits between the almanac and the directional light.

**Lighting pipeline:**

| Time event | Visual result |
|------------|--------------|
| Astronomical twilight start | First hint of light on horizon; stars begin fading |
| Nautical twilight start | Sky deep blue; horizon band of colour; most stars gone |
| Civil twilight start | Sky brightening rapidly; street lights deactivate |
| Sunrise | Sun disc visible; directional light activates at full colour temperature |
| Golden hour | Warm, long shadows; diffused light quality |
| Midday | Sun at peak altitude; shortest shadows; neutral colour temperature |
| Golden hour (evening) | Warm light returns; shadows lengthen |
| Sunset | Sun disc at horizon; directional light colour shifts warm then fades |
| Civil twilight end | Street lights activate; sky deep orange to purple |
| Nautical twilight end | Stars become visible; sky dark blue |
| Astronomical twilight end | Full night; maximum star visibility; moons dominate illumination |

**Directional light:** Azimuth and altitude from the almanac sun position table drive the UE5 directional light rotation directly. Colour temperature shifts from warm (sunrise/sunset) through neutral (midday) to absent (night). Intensity scales with altitude — low sun produces weaker, warmer light; high sun produces strong, neutral light.

**Sky colour:** A gradient lookup driven by sun altitude. The cel shader's 4-tone banding applies to the sky material — the sky has visible colour bands at transitions rather than photorealistic gradients, consistent with the anime art style.

**Ambient audio responds to time of day:**

- Dawn: bird analogues (Terridyn fauna), distant city waking
- Day: city activity — vehicles, distant voices, construction, the hum of a city built for a million housing eighty-five thousand
- Dusk: activity winding down, bar districts waking up, street lighting activating with audible hum
- Night: quiet streets, distant nightlife from the Neon Strips, occasional vehicle, wind through empty spaces

The half-empty city quality noted in [Audio Direction](../gdd/audio-direction.md) is always present — Terridyn sounds quieter than its architecture expects, day or night.

**NPC schedule responses:**

- Shops open at civil twilight, close at or after sunset (varies by shop type)
- Security patrols rotate at fixed intervals regardless of light
- Bar and entertainment district NPCs appear after sunset, thin out after midnight
- Student NPCs follow school schedules (morning commute, afternoon free time)
- Civilian foot traffic peaks midday, thins in early morning and late night

---

### UC2 — Skybox and Celestial Bodies

**Actor:** The system
**Goal:** Render accurate sun, two moons, and starfield based on almanac orbital data

**Sun disc:** Rendered at the almanac-computed azimuth and altitude. Disc size is fixed. During sunrise and sunset, atmospheric reddening tints the disc. The sun is never directly lookable — the camera exposure system prevents staring at it (bloom + intensity clamp), but it is visible as a disc near the horizon and as a bright point higher in the sky.

**Naidra (primary moon):**

- Position: almanac azimuth and altitude, updated hourly
- Apparent size: ~80% of Earth's moon as seen from Earth
- Illumination: phase fraction 0.0–1.0 from the almanac drives a phase shader (crescent through full)
- Orbital period: 27.6 days (almanac-validated; lore doc correction pending from "20-day cycle")
- Visible when above the horizon and not washed out by direct sunlight
- Provides meaningful illumination at night — a full Naidra lights the landscape noticeably

**Sirdal (secondary moon):**

- Position: almanac azimuth and altitude, updated hourly
- Apparent size: ~50% of Earth's moon as seen from Earth
- Illumination: independent phase fraction, same shader as Naidra
- Orbital period: 60 days
- Dimmer than Naidra; provides supplementary illumination
- Visual distinction: different surface colour/texture from Naidra

**Dual-moon nights:** When both moons are above the horizon and at high illumination, night scenes are noticeably brighter. When both moons are below the horizon or new phase, nights are very dark. The player learns to read moon phases for planning night activities — a double-full-moon night is poor for stealth, a double-new-moon night is ideal.

**Night brightness calculation:**

| Moon state | Night illumination |
|------------|-------------------|
| Both new / both below horizon | Starlight only — very dark |
| One moon, partial phase | Dim — moderate visibility |
| One moon, full phase | Moderate — faces and paths visible |
| Both moons, partial phase | Moderate to good |
| Both moons, full phase | Bright — near-dusk visibility; shadows cast from two directions |

**Starfield:**

- Fixed star pattern (not Earth's sky — Terridyn is ~180 light-years from Earth)
- Rotates with planetary rotation (24-hour period, matching the almanac's day length)
- Visible from nautical twilight onward; brightest at astronomical twilight through to astronomical twilight (morning)
- Cloud cover and light pollution (city glow) reduce star visibility
- The Milky Way equivalent is visible on clear nights outside the city centre

**Auroras:**

- Lore-established: frequent polar auroras from dual moons and a strong magnetosphere
- Visible on clear nights; intensity increases in winter months
- Colour palette constrained to the 98-entry project palette — greens, teals, and purples from the chromatic range
- Auroras are a skybox-layer effect — they appear above the cloud layer and behind the moons
- Not gameplay-affecting; purely atmospheric and beautiful
- Frequency: several nights per T3PC month on clear nights; rare in summer, common in winter

---

### UC3 — Weather Conditions

**Actor:** The system
**Goal:** Display weather matching the almanac's per-hour condition, with visual transitions between states

The almanac generates a weather condition per hour, deterministic by date. The rendering system transitions between conditions gradually — weather does not snap from sunny to rain at the top of the hour.

**Condition definitions:**

| Condition | Visual character | Transition from previous |
|-----------|-----------------|-------------------------|
| **Clear** | No cloud cover; full sun; sharp shadows; maximum draw distance | Clouds dissipate over ~30 min; sky brightens; shadows sharpen |
| **Partly Cloudy** | Scattered volumetric clouds; intermittent shadow as clouds cross the sun; shifting light patches on the ground | Clouds build or thin gradually; shadow play begins or ends |
| **Cloudy** | Developing overcast; diffused light; soft shadows; slightly muted colours | Cloud layer thickens; direct sun becomes intermittent then absent |
| **Overcast** | Full cloud cover; flat, even lighting; no direct sun; no shadows; muted palette | Final gaps in cloud cover close; light flattens; colour saturation drops subtly |
| **Light Rain** | Drizzle particles; wet surface shader activates; puddle accumulation begins; slight haze at distance | Clouds darken; first drops appear as sparse particles; surface wetness fades in |
| **Rain** | Heavy rain particles; reduced visibility (~70% draw distance); puddles; wet reflections on roads and metal; splashes on hard surfaces | Drizzle intensifies; particle density increases; visibility drops; puddles grow |
| **Thunderstorm** | Heavy rain + periodic lightning + thunder; darkened sky; severely reduced visibility (~50%); turbulent cloud layer | Rain intensifies further; sky darkens noticeably; first lightning flash after ~5 min of heavy rain |
| **Snow** | Snowfall particles; ground accumulation; muted colours; soft, diffused light; quiet atmosphere | Temperature must be ≤ 2°C (already enforced in almanac); particles shift from rain to snow; ground begins whitening |
| **Fog** | Reduced draw distance (~40%); volumetric fog at ground level; muted audio; objects fade into grey at distance | Ground-level haze thickens; visibility drops progressively; sounds become muffled |

**Transition timing:** Transitions between conditions take 15–30 minutes of game time. The almanac provides the target condition per hour; the rendering system interpolates. Cloud cover builds before rain starts. Rain tapers before clearing. Fog thickens gradually from ground level upward. The player sees weather developing, not switching.

**Almanac extensions needed:**

- **Thunderstorm** — a distinct condition (currently subsumed under Rain). Triggers when rain is sustained and conditions align (temperature, season, wind)
- **Fog/Mist** — a distinct condition. More common in autumn/spring mornings, rare in summer. Clears by midday on most days

---

### UC4 — Volumetric Clouds

**Actor:** The system
**Goal:** Cloud coverage and type driven by weather condition, with movement driven by wind

Clouds are the primary visual indicator that weather is changing. The player sees clouds building before rain arrives and thinning before the sky clears. Cloud rendering uses UE5's volumetric cloud system, constrained by the anime art style — clouds should feel stylised rather than photorealistic.

**Cloud coverage by condition:**

| Condition | Coverage % | Cloud type | Visual character |
|-----------|-----------|------------|-----------------|
| Clear | 0–10% | Scattered wisps | Thin, high-altitude wisps; barely visible |
| Partly Cloudy | 30–50% | Cumulus | Distinct, rounded cloud shapes moving with wind; cast moving shadows |
| Cloudy | 60–80% | Layered stratus | Flatter cloud layer building; less definition between individual clouds |
| Overcast | 90–100% | Full stratus blanket | Uniform grey-white layer; no gaps; no sun visibility |
| Rain / Thunderstorm | 90–100% | Dark nimbostratus | Low, heavy, dark grey; rumbling internal illumination during thunderstorms |
| Snow | 80–100% | Low grey cover | Heavy, low clouds; cold grey palette; oppressive ceiling |
| Fog | Variable | Low-altitude stratus merging with ground | Cloud layer descends to ground level; distinction between cloud and fog disappears |

**Cloud movement:** Direction and speed driven by the wind system (UC6). Clouds move visibly across the sky — the player can watch them drift. Wind direction changes cause cloud movement to shift over minutes, not instantly.

**Cloud density transitions:** When the almanac condition changes, cloud coverage interpolates toward the target percentage over the transition period (15–30 min). Individual cloud shapes morph and merge (building up) or thin and separate (clearing). This is the player's earliest visual cue that weather is changing.

**Lightning illumination:** During thunderstorms, lightning flashes illuminate clouds from within — a bright pulse that lights the cloud mass from the inside, visible before the bolt itself. This creates a dramatic sky effect and serves as a warning that a lightning flash is imminent.

---

### UC5 — Atmospheric Effects (Rain, Snow, Lightning)

**Actor:** The system
**Goal:** Render precipitation, lightning, and surface effects that respond to weather and wind

#### Rain

**Particle system:**

- Density scales with condition: Light Rain = sparse; Rain = moderate; Thunderstorm = heavy
- Wind affects fall angle — rain does not fall straight down when wind is active. Fall angle = wind direction + wind speed modifier
- Particle rendering respects the cel shader — rain drops are visible as discrete streaks, not a blurred overlay
- Rain particles are local to the camera — rendered in a volume around the player, not across the entire world

**Surface effects:**

- **Wet surface shader:** Increased specularity on hard surfaces (roads, metal, glass, stone). Subtle reflections on flat surfaces — not mirror-quality, but visible wet sheen. Activates gradually as rain begins and persists for a period after rain stops
- **Impact splashes:** Rain hitting hard surfaces produces small splash particles. Different materials produce different visual and audio splash characteristics — metal pings, stone spatters, puddle ripples
- **Puddle accumulation:** Puddles form in predefined low-point locations during rain and grow over rain duration. Drain slowly after rain stops. Puddles reflect the sky and nearby light sources. Walking through puddles produces splash audio and particles

**Audio:**

- Rain on different surfaces: metal roofing (sharp, rhythmic), stone (soft patter), puddles (splashing), glass (tapping)
- Distant rain ambience: a continuous wash that scales with intensity
- Indoor rain: muffled exterior rain sound, occasional loud impact on windows/roof
- Rain on Meghan: subtle audio cue when she is getting wet (clothing type affects sound)

#### Snow

**Particle system:**

- Density scales with snowfall intensity
- Wind affects drift direction — snow drifts sideways in wind
- Snowflakes are larger and slower than rain particles — visible as individual flakes at close range, as a white haze at distance
- Temperature must be ≤ 2°C (almanac-enforced)

**Surface effects:**

- **Ground accumulation:** Snow builds on horizontal surfaces over snowfall duration. Gradual whitening of the world — rooftops first, then ground, then vertical surfaces partially
- **Footprint displacement:** Player and NPC footprints are visible in accumulated snow. Footprints are persistent and represent trackable evidence (Search skill can identify and follow tracks). Footprints fill in slowly during continued snowfall
- **Muted ambient audio:** Snow absorbs sound. Ambient city audio is quieter during and after snowfall. The world feels hushed

**Audio:**

- Crunch underfoot: footstep audio changes on snow-covered surfaces
- Muffled city ambience: volume reduction on distant sounds
- Snow on clothing: soft, nearly inaudible — atmosphere, not alert
- Wind through snow: softer, more diffused than wind without snow

#### Lightning

**Visual:**

- **Cloud-to-cloud lightning:** Most lightning is cloud-to-cloud — visible as bright arcs within and between cloud masses. No ground strike. Purely atmospheric
- **Flash illumination:** Each lightning event produces a brief, intense directional flash. The entire scene brightens momentarily from the flash direction. Shadows cast during the flash come from a different angle than the sun/moon — a single-frame shadow shift that reads as dramatic even at speed
- **No natural ground strikes near characters:** Lightning is atmosphere, not hazard. The player never needs to dodge natural lightning. Ground strikes may occur in the distant background as visual flavour, but never near gameplay space

**Arri exception — called lightning:**

During thunderstorms, Arri (lightning element magical girl, Ch.5 onward) can call down targeted ground strikes as a combat ability. This is the only source of ground-strike lightning that affects characters in the game. The ability requires an active thunderstorm — it cannot be used in clear weather. Arri is channelling existing atmospheric energy, not generating it from nothing. The visual and audio signature of Arri's called lightning is distinct from natural cloud-to-cloud lightning — the player sees a bolt descend to a targeted point, followed by a localised blast effect.

**Thunder audio:**

- Delay based on implied distance: close flash = sharp immediate crack; distant flash = rolling rumble arriving seconds later
- Volume scales with proximity
- Thunder echoes through the city — reflections off buildings create a rumbling tail
- During thunderstorms, thunder is frequent enough to mask other sounds (gameplay effect — see UC7)

---

### UC6 — Wind System

**Actor:** The system
**Goal:** Track wind direction and speed as an environmental value affecting visuals, physics, and audio

Wind is a continuous environmental variable, not a weather condition. All weather conditions have an associated wind state — clear days can be windy, rainy days can be calm (though thunderstorms always have strong wind). The almanac extension will generate wind data per hour using the same deterministic hash approach as weather conditions.

**Wind parameters:**

| Parameter | Range | Notes |
|-----------|-------|-------|
| Direction | 0–359° compass heading | Shifts gradually over hours; no instant reversals |
| Speed | 0–5 scale | See speed table below |

**Wind speed scale:**

| Level | Label | Speed (approx) | Visual effect |
|-------|-------|-----------------|---------------|
| 0 | Calm | < 5 km/h | No visible wind effect; particles fall vertically; cloth at rest |
| 1 | Light breeze | 5–15 km/h | Subtle cloth movement; particles drift slightly; grass sways gently |
| 2 | Moderate | 15–30 km/h | Noticeable cloth movement; rain/snow drifts at visible angle; loose paper/debris moves |
| 3 | Strong | 30–50 km/h | Significant cloth billowing; rain nearly horizontal; street debris airborne; hair physics active |
| 4 | Gale | 50–70 km/h | Extreme cloth deformation; walking speed reduced; rain and particles stream sideways; audio dominant |
| 5 | Storm | 70+ km/h | Thunderstorm/severe weather only; movement significantly impaired; visibility severely reduced |

**Visual effects by wind speed:**

- **Particles:** Rain fall angle, snow drift, dust/debris from streets, loose paper and litter. All particle movement reflects wind direction and speed. At high wind, small debris particles spawn independently of precipitation
- **Chaos Cloth:** Hair and clothing respond to wind via the existing Chaos Cloth system (see [Character Pipeline](../gdd/character-pipeline.md)). Wind direction and speed feed into the cloth simulation as a directional force. At calm wind, cloth hangs naturally. At strong wind, skirts lift, hair streams, loose fabric flaps. The effect is continuous and proportional — not a binary on/off
- **Vegetation:** Tree sway, grass bend, hanging signs swing. All environmental Chaos Cloth or procedural animation assets respond to the same wind vector
- **Dust and debris:** At wind level 2+, loose surface material (dust, sand, paper, leaves) lifts and travels with the wind. City streets produce urban debris; parks and outskirts produce organic debris

**Audio by wind speed:**

| Level | Audio character |
|-------|----------------|
| Calm | No wind audio; ambient sounds unaffected |
| Light breeze | Subtle, low wind tone; occasional gentle whistling past structures |
| Moderate | Continuous wind tone; audible whistling around building corners and through gaps |
| Strong | Prominent wind audio; whistling and howling; flapping sounds from loose materials; partial masking of distant sounds |
| Gale | Dominant wind audio; roaring; rattling windows and signs; significant sound masking |
| Storm | Wind audio dominates the soundscape; near-total masking of distant sounds; only nearby sounds penetrate |

**Structural amplification:** Wind is louder and more turbulent in narrow spaces — alleyways, building gaps, elevated walkways. Open plazas have steady, directional wind. Rooftops are more exposed than street level. The city's architecture shapes the wind experience.

---

### UC7 — Weather Gameplay Effects

**Actor:** The player / NPCs / AI
**Goal:** Weather conditions mechanically affect gameplay systems

Weather is not a visual filter — it changes what works and what doesn't. A player who adapts their approach to conditions gains a meaningful advantage. A player who ignores conditions is disadvantaged but never hard-blocked.

**Condition-to-gameplay mapping:**

| Condition | Perception | Stealth | Movement | Ranged combat | Other |
|-----------|-----------|---------|----------|---------------|-------|
| **Clear** | Full range | Standard | Standard | Standard | — |
| **Partly Cloudy** | Full range | Standard | Standard | Standard | Intermittent shadow from clouds |
| **Cloudy** | Full range | Slight bonus (diffused light, softer shadows) | Standard | Standard | — |
| **Overcast** | Full range | Slight bonus (flat lighting, no sharp shadows) | Standard | Standard | — |
| **Light Rain** | Slight reduction | Slight bonus (sound masking) | Standard | Standard | Wet surfaces begin |
| **Rain** | Moderate reduction (~75%) | Moderate bonus (sound + visual masking) | Traction reduced on smooth surfaces; Parkour/Athletics checks harder | Slight accuracy penalty | Wet surfaces; puddles |
| **Thunderstorm** | Significant reduction (~50%) | Significant bonus (thunder masks sounds; poor visibility) | Traction reduced; exposed rooftop traversal dangerous | Moderate accuracy penalty | Arri's lightning ability enabled; NPC morale penalty |
| **Snow** | Slight reduction | Slight penalty (footprints are trackable) | Speed reduced on accumulation; deeper snow = more reduction | Slight accuracy penalty | Footprints persist as Search evidence |
| **Fog** | Severe reduction (~50%) | Significant bonus (visual concealment) | Standard | Significant accuracy penalty (targets obscured at range) | Draw distance reduced for all actors including AI |
| **High wind (3+)** | Slight reduction (wind noise) | Moderate bonus (sound masking) | Walking speed reduced at gale force | Ranged projectile drift; thrown items affected | Chaos Cloth physics intensify |

**Perception range reductions apply equally to player and AI.** The AI uses the same perception system as the player (see [Enemy AI Notes](../../mgaPriv/mechanics/enemy-ai-notes.md) — signal parity). Rain does not just make it harder for the player to see enemies — it makes it harder for enemies to see the player. This is the core of weather-as-gameplay: conditions create symmetrical changes that the player can exploit through awareness.

**Traction effects:**

- Wet surfaces (rain, puddles): reduced friction on smooth surfaces (metal, polished stone, tile). Parkour wall-runs are harder; Athletics climbing is riskier; sharp direction changes while running may produce skid/stumble
- Snow accumulation: movement speed reduction proportional to depth. Fresh snow is lighter resistance than packed snow. Ice patches (post-thaw refreeze) are the most treacherous surface
- Affects NPCs equally — an NPC chasing Meghan across a wet rooftop is as likely to slip as she is

**Stealth interactions:**

- Rain sound masking: continuous rain audio raises the ambient noise floor, making footstep audio harder for AI perception to detect. At Thunderstorm intensity, thunder cracks provide windows where even loud actions go unheard
- Snow footprints: the stealth penalty is delayed — the footprints don't alert anyone in real time, but a Search-capable NPC or pursuer who reaches the area later can track the player's path. Snow stealth is about leaving a trail, not about being heard
- Fog visual concealment: AI visual perception range is halved in fog (same as player). Targets at range are not visible to either side. Close-range detection remains normal
- Wind sound masking: at strong wind, distant sounds are masked. This helps at range but does nothing at close quarters

**NPC behaviour responses:**

| NPC type | Rain / Thunderstorm | Snow | Fog | Clear |
|----------|-------------------|------|-----|-------|
| **Civilians** | Seek shelter; street population thins significantly | Reduced outdoor activity; children may play in light snow | Reduced movement; stick to known paths | Normal activity |
| **Security patrols** | Patrol routes unchanged; alertness may decrease in poor visibility | Routes unchanged; slower patrol speed | Routes unchanged; closer formation; reduced detection range | Normal patrols |
| **Shop NPCs** | Remain at posts | Remain at posts | Remain at posts | Normal |
| **Gang / hostile NPCs** | Territorial presence maintained; ambush likelihood may increase (they use the same cover) | Reduced patrol range; territorial | Reduced activity; wary | Normal activity |
| **Students** | Rush between buildings; outdoor socialising stops | Campus activity compressed to indoors | Walk in groups; cautious | Outdoor campus life active |

**Arri's lightning ability (thunderstorm only):**

- Requires an active thunderstorm condition (cannot be used in other weather)
- Arri channels existing atmospheric energy — she directs where a bolt strikes, not whether one occurs
- Targeted ground strike with AoE radius at the impact point
- Lightning damage type; bypasses most physical armour; devastating against mechanical/electronic targets
- Visual: bolt descends from cloud layer to targeted point; distinct from cloud-to-cloud lightning in colour and intensity
- Audio: immediate crack at impact point (no distance delay — the player aimed it)
- Cooldown tied to natural lightning frequency during the storm — she is riding the storm's rhythm, not generating infinite bolts

---

### UC8 — Temperature and Seasonal Atmosphere

**Actor:** The system
**Goal:** Seasonal and temperature variation visible in the world

The almanac provides per-hour temperature derived from seasonal base, weather condition modifier, and night modifier. The T3PC year is 400 days with four 100-day seasons. Temperature drives visual effects and reinforces the passage of time in the game world.

**Temperature-driven effects:**

| Temperature range | Visual effect |
|-------------------|--------------|
| Below 0°C | Frost on surfaces; ice on puddles and standing water; breath very visible |
| 0–5°C | Breath visibility (condensation clouds on exhale); frost on metal surfaces in shade |
| 5–15°C | No temperature-specific visuals; neutral atmosphere |
| 15–25°C | Warm light quality; NPCs in lighter clothing |
| Above 25°C | Heat haze near ground level (atmospheric distortion — shader-based, not post-process); NPCs seek shade |

**Breath visibility:** When temperature drops below ~5°C, all characters produce visible breath condensation. This is a particle effect attached to the character's head, timed to the breathing animation cycle. Breath visibility is a subtle atmospheric detail that makes cold days feel cold without a temperature HUD element.

**Seasonal atmosphere:**

| Season | Day length | Light character | Weather tendency | Atmospheric mood |
|--------|-----------|----------------|------------------|-----------------|
| **Spring** | Lengthening | Warming, transitional; morning mist common | Mixed — rain and clear alternating; fog mornings | Growth, change; the city waking up |
| **Summer** | Long days, short nights | Warm, golden; extended golden hours; long shadows at dawn/dusk | Predominantly clear; occasional thunderstorms | Heat, energy; the city at its most alive |
| **Autumn** | Shortening | Cooling; blue-shifted light; earlier sunsets | Increasing cloud and rain; fog returns | Melancholy; beautiful but fading |
| **Winter** | Short days, long nights | Cold, blue-shifted; weak sun; low altitude even at noon | Snow, overcast, fog; clear days are sharp and bright | Quiet, introspective; the half-empty city feels emptiest |

**Midyear Festival (winter solstice):** Falls around day 200 of the T3PC year — deep winter. Snow on the ground is atmospherically critical for this event. The almanac's seasonal system already ensures winter conditions around this date. The festival should feel like a warm gathering against a cold world — interior warmth contrasted with exterior snowfall, street decorations visible through falling snow, warm light spilling from buildings onto white streets.

**Seasonal foliage:** Terridyn's flora is alien, not Earth-transplanted. Whether Terridyn's plant life has deciduous cycles is an open design question — the planet's megaflora (10–20% larger than Earth equivalents due to 23% oxygen) may follow the 400-day seasonal cycle, displaying colour changes in autumn and new growth in spring, or may be primarily evergreen adapted to the longer year. This does not need to be resolved before the weather system is implemented — foliage can be added as a seasonal layer later.

**Heat haze:** On hot summer days (above 25°C), atmospheric distortion appears near ground level — a shimmering effect that warps distant objects slightly. Implemented as a shader-based distortion (not post-process), affecting a thin layer above road surfaces and metal rooftops. Heat haze is a strong visual signal that it is hot without needing a thermometer on-screen.

---

## Almanac Extension Requirements

The brief depends on three almanac extensions that do not yet exist:

| Extension | What it adds | Approach |
|-----------|-------------|----------|
| **Wind** | Per-hour wind speed (0–5 scale) and direction (0–359°), deterministic by date | Same seeded hash approach as weather condition; wind correlates with but is not identical to weather severity |
| **Thunderstorm** | A distinct weather condition separate from Rain | Subset of Rain — triggers when rain is sustained, temperature is above freezing, and a probability check passes. Seasonal: more common in summer |
| **Fog/Mist** | A distinct weather condition | Seasonal: most common in autumn/spring mornings, rare in summer. Time-of-day bias: fog favours pre-dawn and early morning, clears by midday |

These are library-level changes to the almanac codebase, not rendering or gameplay work. The creative brief assumes they will be implemented and their output will follow the existing almanac format (per-hour condition in the weather forecast table).

---

## Discrepancy Flag

**Naidra moon cycle:** `terridyn-planet.md` states "close orbit (20-day cycle)" but the almanac code uses a 27.6-day orbital period. The almanac's 27.6-day value produces validated results across the 400-day CSV output and is the source of truth. The lore document should be corrected to match the code.

---

## Open Items

- Exact wind speed thresholds and gameplay breakpoints — what wind level affects ranged accuracy by how much?
- Whether volcanic activity produces ash/particulate weather events (lore says Terridyn is geologically active — volcanic ash could be a locale-specific condition near volcanic biomes)
- Aurora rendering approach — skybox texture layer, shader effect, or particle system? Must respect the anime art style
- Whether indoor environments fully shelter from weather effects or partially reduce them (rain sound muffled but audible; wind reduced but not absent; fog does not penetrate interiors)
- Snow accumulation persistence — how long does snow stay after snowfall stops? Melt rate by temperature?
- Puddle system scope — predefined locations with puddle meshes that fill/drain, or procedural water accumulation?
- Whether the CAMERA drone is affected by weather (rain on lens, wind buffeting at altitude, lightning interference during thunderstorms)
- How weather integrates with the time-skip system — does skipping hours skip weather transitions or resolve them instantly at the target time?
- Seasonal foliage — does Terridyn's alien megaflora have deciduous cycles or is it primarily evergreen?
- Weather impact on vehicle handling — does rain/snow/ice affect the bicycle, motorbike, and hoverbike differently? (See [CB-traversal — Vehicles](CB-traversal.md))
- Precipitation interaction with the cel shader — do rain and snow particles receive the 4-tone banding or are they rendered outside the cel shader pass?
- Night brightness formula — exact calculation from moon phase, moon altitude, cloud cover, and light pollution
- Whether weather conditions affect magical girl transformation sequences (transforming in rain — do the particles interact?)
- Sound propagation in snow — how much does accumulated snow reduce the effective Perception detection radius?
- Thunderstorm frequency and duration — how long does a thunderstorm last? How many lightning-call opportunities does Arri get per storm?
