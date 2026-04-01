<p align="center">
  <a href="https://kinetic.place">
    <img src="https://kinetic.place/images/kinetic-logo-animated.gif" alt="Kinetic.place" width="400" />
  </a>
</p>

# 🗄️ Kinetic Exercises Database

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![NPM Version](https://img.shields.io/npm/v/@kinetic-place/exercises-db)](https://npmjs.com/package/@kinetic-place/exercises-db)

An open-source, database-ready exercise dataset with **899 exercises**, **17 muscle groups**, and **36 equipment types** — structured as separate entity files ready to seed into SQL or NoSQL databases. Available in English and Spanish.

---

## 📦 Installation

```bash
npm install @kinetic-place/exercises-db
```

Or download the `.json` files directly from the `en/` and `es/` folders.

## 📂 What's Included

```
exercises-db/
├── en/
│   ├── exercises.json       # 899 exercises with relations
│   ├── muscle_groups.json   # 17 muscle groups
│   └── equipment.json       # 36 equipment types
└── es/
    ├── exercises.json       # 899 exercises (Spanish)
    ├── muscle_groups.json   # 17 muscle groups (Spanish)
    └── equipment.json       # 36 equipment types (Spanish)
```

Unlike `exercises-json` (a single flat file), this package provides **separate entity files** with referential IDs — designed for direct database seeding.

## 🚀 Quick Start

### Seed a PostgreSQL Database

```sql
-- Create tables
CREATE TABLE muscle_groups (
  id UUID PRIMARY KEY,
  slug TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL
);

CREATE TABLE equipment (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL,
  type TEXT NOT NULL,               -- 'weight' | 'resistance'
  usage_type TEXT                   -- 'single' | 'double' | 'multiple'
);

CREATE TABLE exercises (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL,
  type TEXT NOT NULL,               -- 'reps' | 'time' | 'distance'
  difficulty_level TEXT,            -- 'beginner' | 'intermediate' | 'expert'
  force_type TEXT,                  -- 'push' | 'pull' | 'static'
  mechanics TEXT,                   -- 'compound' | 'isolation'
  category TEXT,                    -- 'strength' | 'cardio' | etc.
  instructions JSONB
);

-- Seed from JSON files
-- Use your preferred JSON import tool (pg_restore, Prisma seed, Drizzle, etc.)
```

### Seed with Node.js

```typescript
import exercises from '@kinetic-place/exercises-db/en/exercises.json';
import muscles from '@kinetic-place/exercises-db/en/muscle_groups.json';
import equipment from '@kinetic-place/exercises-db/en/equipment.json';

// Seed muscle groups first (referenced by exercises)
for (const muscle of muscles) {
  await db.insert('muscle_groups', muscle);
}

// Then equipment
for (const equip of equipment) {
  await db.insert('equipment', equip);
}

// Then exercises (with embedded relations)
for (const exercise of exercises) {
  const { muscleGroups, equipment: equip, ...data } = exercise;
  await db.insert('exercises', data);

  // Insert relations
  for (const mg of muscleGroups) {
    await db.insert('exercise_muscles', {
      exercise_id: data.id,
      muscle_group_id: mg.id,
      type: mg.type  // 'primary' | 'secondary' | 'tertiary'
    });
  }
}
```

### MongoDB / NoSQL

```javascript
const exercises = require('@kinetic-place/exercises-db/en/exercises.json');

// Documents are already denormalized — insert directly
await db.collection('exercises').insertMany(exercises);
```

## 📖 Data Schema

### exercises.json

Each exercise contains the full relational data embedded:

```json
{
  "id": "d586b5aa-c2f4-4cb5-8038-d10b03c3b763",
  "name": "Barbell Bench Press",
  "type": "reps",
  "difficultyLevel": "intermediate",
  "forceType": "push",
  "mechanics": "compound",
  "category": "strength",
  "instructions": [
    "Lie flat on a bench with your feet planted firmly on the floor...",
    "As you breathe in, slowly lower the bar to your mid-chest..."
  ],
  "muscleGroups": [
    { "id": "...", "slug": "chest", "name": "Chest", "type": "primary" },
    { "id": "...", "slug": "triceps", "name": "Triceps", "type": "primary" }
  ],
  "equipment": [
    { "id": "...", "name": "Barbell", "type": "weight", "usageType": "single", "numItems": null }
  ]
}
```

#### Exercise Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | `string` | UUID — stable across versions |
| `name` | `string` | Exercise name (localized) |
| `type` | `string` | Measurement type: `reps`, `time`, or `distance` |
| `difficultyLevel` | `string \| null` | `beginner`, `intermediate`, or `expert` |
| `forceType` | `string \| null` | Primary force: `push`, `pull`, or `static` |
| `mechanics` | `string \| null` | Movement type: `compound` or `isolation` |
| `category` | `string \| null` | Exercise category (see values below) |
| `instructions` | `string[]` | Step-by-step coaching cues |
| `muscleGroups` | `MuscleGroup[]` | Muscles targeted with role classification |
| `equipment` | `Equipment[]` | Required equipment |

**Categories:** `strength` · `stretching` · `cardio` · `plyometrics` · `powerlifting` · `olympicWeightlifting` · `strongman`

### muscle_groups.json

```json
{ "id": "6427e37e-...", "slug": "chest", "name": "Chest" }
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | `string` | UUID — matches IDs in `exercise.muscleGroups[].id` |
| `slug` | `string` | URL-safe identifier (e.g., `chest`, `lower-back`) |
| `name` | `string` | Display name |

**17 muscle groups:** abdominals · abductors · adductors · biceps · calves · chest · forearms · glutes · hamstrings · lats · lower back · middle back · neck · quadriceps · shoulders · traps · triceps

### equipment.json

```json
{ "id": "97df708e-...", "name": "Barbell", "type": "weight", "usageType": "single" }
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | `string` | UUID — matches IDs in `exercise.equipment[].id` |
| `name` | `string` | Equipment name |
| `type` | `string` | `weight` or `resistance` |
| `usageType` | `string` | `single`, `double`, or `multiple` |

**36 equipment types** including: Barbell · Dumbbell · Cable · Machine · Body Only · Kettlebell · Bands · Medicine Ball · Exercise Ball · and more.

### Muscle Group Targeting

When exercises reference muscle groups, each includes a `type` field:

| Type | Meaning |
|------|---------|
| `primary` | Main muscle(s) targeted |
| `secondary` | Supporting muscles engaged |
| `tertiary` | Stabilizers with minor activation |

## 🌐 Locales

| Language | Files | Status |
|----------|-------|--------|
| English | `en/*.json` | ✅ Complete |
| Spanish | `es/*.json` | ✅ Complete |

## 💡 When to Use This vs. Other Packages

| Need | Use |
|------|-----|
| Single JSON file to import into your app | [`@kinetic-place/exercises-json`](https://github.com/kinetic-place/exercises-json) |
| **Separate entity files to seed a database** | **`@kinetic-place/exercises-db`** ← you're here |
| REST API with search and filtering | [`@kinetic-place/exercises-api`](https://github.com/kinetic-place/exercises-api) |

## 🔌 Prefer an API?

Skip the database setup entirely — use the free REST API:

```bash
curl "https://api.kinetic.place/v1/exercises?muscle=chest&equipment=dumbbell&level=beginner"
```

See [`@kinetic-place/exercises-api`](https://github.com/kinetic-place/exercises-api) for self-hosting, or use the free hosted instance at `https://api.kinetic.place`.

## 🙏 Acknowledgments

A massive thank you to [wrkout/exercises.json](https://github.com/wrkout/exercises.json) for providing the foundational list of gym exercises that kickstarted this dataset!

## 🚀 Building a Fitness App?

This dataset is part of [**Kinetic.place**](https://kinetic.place) — the open platform for developers and fitness creators. Whether you're building a workout tracker, a coaching app, or creating fitness content, Kinetic provides the data, APIs, and tools to ship faster.

👉 **[kinetic.place](https://kinetic.place)** — Exercises, APIs, and content for fitness builders.

## License

MIT © [Kinetic.place](https://kinetic.place)
