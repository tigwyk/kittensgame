# Kittens Game: Game Design Analysis

This document captures notable game design elements and progression systems discovered through codebase analysis of Kittens Game, a JavaScript-based incremental game with legacy browser compatibility.

## Core Architecture Philosophy

### Legacy-First Design
- **IE6 Compatibility**: Deliberately avoids modern ES6+ features (no arrow functions, const/let, webpack)
- **Dojo Toolkit**: Uses pre-1.7 Dojo for inheritance system with `dojo.declare()`
- **Effect System**: Centralized effect calculation with metadata-driven caching
- **TabManager Pattern**: All game sections extend core `TabManager` for consistent structure

## Progression Systems

### 1. Exponential Price Scaling (`priceRatio`)
**Location**: `core.js:2025-2039`, various building definitions

**Key Insight**: Dynamic price scaling with prestige mitigation
```javascript
// Base formula: price * Math.pow(priceRatio, buildingCount)
priceRatio: 1.15  // Standard building scaling (15% increase per building)
```

**Prestige Reduction Effects**:
- Engineering: -1% price ratio reduction
- Golden Ratio: -(1+√5)/200 ≈ -1.618% (mathematical elegance)
- Renaissance: -2.25% (most powerful price reduction)

**Design Choice**: Uses mathematical constants (Golden Ratio) for meaningful progression milestones.

### 2. Multi-Tier Resource Hierarchy
**Location**: `js/resources.js:21-300`

**Resource Classifications**:
- **Common**: Basic materials (catnip, wood, minerals, iron, titanium, gold, oil, uranium, unobtainium)
- **Uncommon**: Luxury resources (furs, ivory, spice) - don't persist through resets
- **Rare**: Special resources (unicorns, alicorn, necrocorn, paragon, timeCrystal)
- **Exotic**: End-game resources (relic)

**Persistence Rules**:
- `persists: undefined` - Carries over based on Chronospheres
- `persists: true` - Always carries over (paragon, burnedParagon, sorrow)
- `persists: false` - Never carries over (kittens, zebras, luxury resources)

### 3. Science Technology Gates
**Location**: `js/science.js:13-300`

**Progression Gating**:
- **Early**: 30 science (calendar) → 100 (agriculture) → 300 (archery)
- **Mid**: 15,000 (machinery) → 35,000 (navigation) 
- **Late**: 500,000+ for space technologies

**Unlock Chains**: Technologies unlock buildings, jobs, tabs, policies, and other technologies in dependency chains.

**Design Choice**: Exponential science costs with manuscript/compedium requirements for advanced techs.

### 4. Religion & Transcendence System
**Location**: `js/religion.js:1-300`

**Faith Mechanics**:
- `faith`: Temporary worship pool
- `faithRatio`: Permanent conversion bonus from resets
- `transcendenceTier`: Reset-persistent progression level

**Corruption (Necrocorn) Production**:
```javascript
// Multi-factor calculation with additive/multiplicative bonuses
// Base: Markers + existNecrocorn penalty/bonus + Black Radiances
finalCorruptionPerTick = base * modifiers - siphoning
```

**Design Choice**: Complex nested bonus system with detailed UI breakdown for transparency.

### 5. Prestige Paragon System
**Location**: `js/prestige.js:3-300`

**Paragon Costs**: Exponential progression (5 → 25 → 50 → 75 → 100 → 150 → 250 → 400 → 500 → 750)

**Effect Categories**:
- **Economic**: Price ratio reductions, crafting bonuses
- **Population**: Kitten growth bonuses
- **Diplomatic**: Standing improvements
- **Mystical**: Numerology tree with Kabbalah references (Malkuth, Yesod)

### 6. Space Program Progression
**Location**: `js/space.js:23-200`

**Mission Costs**: Dramatic resource scaling
- Orbital Launch: 15K oil, 5K manpower, 100K science, 250 starcharts
- Centaurus System: 40K titanium, 800K science, 100K starcharts, 50K kerosene

**Building Price Ratios**:
- Standard: 1.12-1.15 (space buildings scale faster than terrestrial)
- Expensive: 1.18 (premium space infrastructure)

## Balancing Mechanisms

### 1. Diminishing Returns System
**Location**: `core.js:185-191`

```javascript
_hasLimitedDiminishingReturn: function(name) {
    return name == "catnipDemandRatio" 
        || name == "fursDemandRatio"
        || name == "ivoryDemandRatio" 
        || name == "spiceDemandRatio"
        || name == "unhappinessRatio";
}
```

**Design Choice**: Prevents demand reduction from becoming overpowered through stacking.

### 2. Energy Gating System
**Location**: `js/buildings.js:180-183`

```javascript
if ((effectName == "productionRatio" || effectName == "magnetoRatio")
    && (game.resPool.energyCons > game.resPool.energyProd)){
    effect *= game.resPool.getEnergyDelta();
}
```

**Design Choice**: Production bonuses require energy balance, creating infrastructure dependencies.

### 3. Iron Will Mode
**Location**: `core.js:2100-2121`

**Mechanic**: Special challenge mode broken by building population housing.
- Triggers confirmation dialogs for buildings with `maxKittens` effects
- Creates meaningful choice between challenge completion and progression

### 4. Automation Systems
**Location**: Building controllers

**Features**:
- `isAutomationEnabled`: Per-building automation toggle
- `togglable`/`togglableOnOff`: Manual on/off controls
- Batch building with Ctrl+click (batchSize configurable)

## Workshop Upgrade Patterns
**Location**: `js/workshop.js:7-200`

**Tiered Improvements**:
- Tool progression: Mineral → Iron → Steel → Titanium → Alloy → Unobtainium
- Multiplicative job bonuses: +50% wood/catnip production per tier
- Building ratio bonuses: +15-25% efficiency per upgrade

**Design Choice**: Clear upgrade paths with meaningful but not overwhelming power scaling.

## Innovative Design Elements

### 1. Stageable Buildings
**Location**: `js/buildings.js:45-75`

Buildings can have multiple stages with different effects, prices, and properties. Stage progression changes the building's behavior entirely.

### 2. Effect Metadata System
**Location**: `core.js:432-500`

Automated effect title generation based on resource names and effect types:
- `catnipPerTick` → "Catnip"
- `catnipMax` → "Catnip storage"
- `catnipRatio` → "Catnip production"

### 3. Complex Price Dependencies
**Location**: Modern button controller

Recursive price unrolling shows component costs for craftable resources, with ETA calculations based on current production rates.

### 4. Seasonal Effects System
Calendar system that modifies resource production based on seasons, creating temporal strategy elements.

### 5. Challenge Integration
Challenges modify core game mechanics (like price ratios) providing alternative progression paths.

## Key Takeaways for Game Developers

1. **Mathematical Elegance**: Use meaningful constants (golden ratio) for progression milestones
2. **Transparency**: Complex systems need detailed breakdowns (corruption calculation)
3. **Gating Variety**: Multiple resource types prevent single-resource dominance
4. **Reset Value**: Prestige systems should provide meaningful permanent progression
5. **Infrastructure Dependencies**: Advanced features require supporting systems (energy)
6. **Legacy Considerations**: Sometimes constraints (IE6 support) force cleaner, more maintainable designs
7. **Nested Complexity**: Layer simple mechanics (price scaling) into complex meta-systems (prestige reduction)
8. **Automation Balance**: Give players control over automation granularity
9. **Resource Persistence**: Carefully design what carries over through resets for progression pacing
10. **Effect Composition**: Build complex behaviors from simple, composable effect systems

## Progression Curve Analysis

### Early Game (0-50 buildings)
- Linear progression dominates
- Simple resource chains (catnip → wood → minerals → iron)
- Focus on basic infrastructure

### Mid Game (50-500 buildings) 
- Exponential costs become significant
- Prestige becomes necessary for progress
- Complex resource interdependencies emerge

### Late Game (500+ buildings)
- Transcendence and space exploration
- Multiple parallel progression systems
- Prestige optimization becomes core gameplay

The game successfully transforms from simple resource management to complex optimization puzzle through well-designed scaling mechanisms.