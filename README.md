<p align="center">
  <a href="https://kinetic.place">
    <img src="https://kinetic.place/images/kinetic-logo-animated.gif" alt="Kinetic.place" width="400" />
  </a>
</p>

# 🗄️ Kinetic Exercises Database

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![NPM Version](https://img.shields.io/npm/v/@kinetic-place/exercises-db)](https://npmjs.com/package/@kinetic-place/exercises-db)

An open-source JSON database of **899+ structured fitness exercises**, execution media, and relational mappings to muscles and equipment.

👉 **[Get your free API Key at kinetic.place](https://kinetic.place)**

---

## 🚀 Why this database format?

Setting up proper relational mappings for a fitness app is tedious. Manually tagging hundreds of exercises to primary muscles, secondary muscles, and required equipment isn't sustainable when you're a lean team.

This dataset provides deeply structured records ready to drop into SQL schemas (PostgreSQL, SQLite) or NoSQL document stores completely friction-free. 

**Want a zero-setup solution?**
If deploying and migrating databases for exercise data sounds like overkill, **[join the developer waitlist at kinetic.place](https://kinetic.place)**. We host an edge-optimized REST API so you can just `fetch()` the exact data you need directly into your app.

## 📦 Installation

```bash
npm install @kinetic-place/exercises-db
```
*Or grab the raw `.json` collection seed files from the `/data` folder.*

## 📖 Database Features
- **899+ Verified Exercises**: Extensive coverage for strength training and hypertrophy.
- **Relational Integrity**: Clear separation of `Exercises`, `Muscle Groups`, and `Equipment` for standard SQL modeling.
- **Images & Videos (Coming Soon)**: High-quality execution media loops (GIFs/MP4s) **created by real fitness content creators** are being mapped to these records, designed to be fully **brandable and customizable** for your platform! **[Join the waitlist at kinetic.place](https://kinetic.place)** to get API Key access to the premium media CDN.

*(Schema maps and usage examples coming shortly...)*

## 🙏 Acknowledgments

A massive thank you to [wrkout/exercises.json](https://github.com/wrkout/exercises.json) for providing the foundational list of gym exercises that kickstarted this dataset!
