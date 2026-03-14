# 🌌 Signal & Noise

> *An engineer's journey learning to read the universe — one dataset at a time.*

---

## What is this?

This repository contains every notebook, dataset reference, and visualization produced for the **Signal & Noise** YouTube channel.

I am a 46-year-old executive, father, and engineer who one day asked a simple question:

*Where does the data go?*

When Hubble takes an image. When JWST captures a galaxy 13 billion light years away. When a telescope detects a faint dip in a star's brightness that might — just might — be a planet passing in front of it.

The answer stopped me completely.

**It's public. All of it. Free. Available to anyone with a browser and a basic knowledge of Python.**

The telescope I never had as a kid? I've had access to hundreds of them for years. I just didn't know how to read what they were saying.

That's what this channel is. Me — learning to read the universe. Week by week. Dataset by dataset. One hour at a time.

And you're welcome to follow along.

---

## The Philosophy

This is not a tutorial channel. It is not an expert talking down.

It is a **documented journey** — with all the failures, confusion, wrong turns, and occasional moments of genuine wonder that come with learning something hard as a busy adult.

The universe has been broadcasting for 13.8 billion years.  
The data is public.  
The tools are free.  
The only thing that was ever missing — was someone willing to learn to listen.

---

## Episodes

| # | Title | Notebook | Key Concepts | Data Source |
|---|-------|----------|--------------|-------------|
| 01 | **First Light** — Rendering the Andromeda Galaxy from raw photon data | [📓 Open](./episode-01-first-light.ipynb) | FITS files, DSS survey, contrast stretching, astropy | NASA SkyView / DSS |
| 02 | **Three Filters** — Building a Pillars of Creation style image | *Coming soon* | Multi-band imaging, Hubble filters, false color | HST Archive |
| 03 | **Counting Stars** — Automated source detection in a deep field | *Coming soon* | Photutils, aperture photometry, source catalogs | NASA SkyView |
| 04 | **How Bright?** — Measuring stellar brightness from raw data | *Coming soon* | Photometry, magnitude scale, flux calibration | SDSS |
| 05 | **The Dip** — Reading a light curve for the first time | *Coming soon* | Time series, lightkurve, stellar variability | NASA TESS |
| 06 | **Transit** — Hunting for an exoplanet in Kepler data | *Coming soon* | Kepler pipeline, BLS algorithm, period folding | NASA Kepler |
| 07 | **Wrong** — My exoplanet detector was completely broken | *Coming soon* | Debugging, overfitting, scientific skepticism | NASA Kepler |
| 08 | **Found** — I detected a planet outside our solar system | *Coming soon* | Confirmed transit, validation, uncertainty | NASA Kepler |
| 09 | **Pattern Recognition** — Teaching a machine to classify galaxies | *Coming soon* | scikit-learn, Galaxy Zoo data, ML basics | Galaxy Zoo |
| 10 | **Anomaly** — Looking for things that don't fit any known category | *Coming soon* | Anomaly detection, isolation forest, follow-up | SDSS |

---

## Stack

```python
# Core astronomy
astropy        == 6.x        # The backbone. FITS, coordinates, units, time.
astroquery     == 0.4.11     # Query NASA, ESA, SDSS, Gaia directly from Python.
photutils      == 1.x        # Source detection and photometry.
lightkurve     == 2.x        # NASA Kepler and TESS light curve analysis.

# Data science
numpy          == 1.26.x     # Matrix operations on photon count arrays.
pandas         == 2.x        # Tabular catalog data.
scipy          == 1.x        # Signal processing and statistics.
scikit-learn   == 1.x        # Machine learning for classification.

# Visualization
matplotlib     == 3.x        # Core plotting engine.
plotly         == 5.x        # Interactive visualizations.

# Animation (coming in later episodes)
manim                        # Mathematical animation engine (3Blue1Brown).
```

All notebooks run in **Google Colab** — no local installation required.

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com)

---

## Data Sources

Every dataset used in this channel is **publicly available and free.**

| Source | What it contains | URL |
|--------|-----------------|-----|
| NASA SkyView | Virtual telescope — query any patch of sky | https://skyview.gsfc.nasa.gov |
| MAST Archive | Hubble, JWST, Kepler, TESS raw data | https://mast.stsci.edu |
| SDSS | Sloan Digital Sky Survey — billions of objects | https://www.sdss.org |
| NASA Kepler | Exoplanet light curves | https://keplerscience.arc.nasa.gov |
| NASA TESS | New exoplanet survey, ongoing | https://tess.mit.edu |
| Gaia Archive | 1.8 billion stars with precise positions | https://gea.esac.esa.int |
| Galaxy Zoo | Human-classified galaxy morphologies | https://www.zooniverse.org/projects/zookeeper/galaxy-zoo |
| Minor Planet Center | Asteroid and comet data | https://www.minorplanetcenter.net |

---

## How to Use This Repository

**If you just want to run a notebook:**
1. Click the **Open in Colab** badge inside any episode folder
2. Run Cell 1 to install dependencies
3. Run cells in order — every step is explained

**If you want to follow the full journey:**
1. Watch the episode on YouTube first
2. Open the companion notebook
3. Modify, experiment, break things
4. Come back next episode

**If you find something interesting in the data:**
Open an issue. Seriously. This is citizen science.  
If you find something worth investigating, I want to know.

---

## Repository Structure

```
signal-and-noise/
│
├── README.md                          ← You are here
│
├── episode-01-first-light/
│   ├── render_andromeda_from_nasa_data.ipynb
│   └── images/
│       └── andromeda_first_light.png
│
├── episode-02-three-filters/          ← Coming soon
├── episode-03-counting-stars/         ← Coming soon
│
├── resources/
│   ├── astronomy-python-cheatsheet.md
│   ├── fits-format-explained.md
│   └── coordinate-systems-primer.md
│
└── data/
    └── README.md                      ← Data is not stored here.
                                         All data is pulled live
                                         from public archives.
```

---

## The Mantra

> *"The Andromeda Galaxy is approaching the Milky Way at 110 kilometres per second.*  
> *In 4.5 billion years, they will merge.*  
> *The photons from that collision haven't left yet.*  
> *We'll be ready."*

---

## Connect

| Platform | Link |
|----------|------|
| 📺 YouTube | *Coming soon* |
| 🐦 X / Twitter | *Coming soon* |
| 📸 Instagram | *Coming soon* |

---

## License

All code in this repository is released under the **MIT License** — use it freely, modify it, share it, run it, learn from it.

The universe's data is already public.  
The code that reads it should be too.

---

*Started: 2026. By a developer who finally looked up.*
