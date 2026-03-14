# Coordinate Systems in Astronomy — A Visual Primer

> *"Before you can find anything in the universe,*  
> *you need to agree on how to describe where things are."*

---

## Why Coordinate Systems Matter

When you query NASA SkyView, you give it a position.  
When you open a FITS file, the header contains a position.  
When you cross-match two catalogs, you compare positions.  

Everything in observational astronomy starts with:  
**Where exactly in the sky are we looking?**

The answer depends on which coordinate system you use.  
There are several. Each exists for a good reason.  
This guide explains all of them — visually, from scratch.

---

## The Fundamental Idea — The Celestial Sphere

Before any coordinate system, there is one mental model  
that underlies all of them:

```
THE CELESTIAL SPHERE
────────────────────────────────────────────────────────────────

Imagine standing at the center of a giant hollow sphere.
Every star, galaxy, and nebula is projected onto the
inner surface of that sphere — like a planetarium dome.

It does not matter that some stars are 10 light years away
and others are 10 billion light years away.
For the purpose of direction — they are all on the sphere.

                         ● North Celestial Pole
                        /|\
                       / | \
           ───────────/──┼──\───────────
          /          /   |   \          \
         /          /    |    \          \
        /          /     |     \          \
       ●──────────────────────────────────●
        \          \     |     /          /
         \          \    |    /          /
          \          \   |   /          /
           ───────────\──┼──/───────────
                       \ | /
                        \|/
                         ● South Celestial Pole

You are at the center.
The sphere rotates around you once every 24 hours
(actually, the Earth rotates — but from your perspective
it looks like the sphere is turning).

Every coordinate system is just a different way of
drawing a grid on the surface of this sphere.
```

---

## System 1 — Equatorial Coordinates (RA / Dec)

**The most important system. Used in almost every database.**

```
WHAT IT IS:
────────────────────────────────────────────────────────────────

Equatorial coordinates project Earth's geographic
coordinate system (latitude/longitude) outward onto
the celestial sphere.

Earth's equator → Celestial Equator (Dec = 0°)
Earth's poles   → Celestial Poles (Dec = ±90°)

The two coordinates are:

┌─────────────────────────────────────────────────────────┐
│                                                         │
│  DECLINATION (Dec)                                      │
│  ─────────────────                                      │
│  The celestial equivalent of LATITUDE                   │
│                                                         │
│  • Measured in degrees (°), arcminutes ('), arcseconds (") │
│  • Range: -90° (south pole) to +90° (north pole)        │
│  • 0° = Celestial Equator                               │
│  • Positive = northern sky                              │
│  • Negative = southern sky                              │
│                                                         │
│  Examples:                                              │
│    Andromeda (M31) : Dec = +41° 16' 09"  (northern)    │
│    Orion Nebula    : Dec = -05° 23' 28"  (slightly south)│
│    Large Magellanic Cloud: Dec = -69° 45' (deep southern)│
│                                                         │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  RIGHT ASCENSION (RA)                                   │
│  ────────────────────                                   │
│  The celestial equivalent of LONGITUDE                  │
│                                                         │
│  • Measured in HOURS, minutes, seconds (NOT degrees)    │
│  • Range: 0h to 24h                                     │
│  • Increases EASTWARD                                   │
│  • Origin (0h) = the Vernal Equinox                    │
│                                                         │
│  CONVERTING RA TO DEGREES:                              │
│    RA (degrees) = RA (hours) × 15                      │
│    1h = 15°, 6h = 90°, 12h = 180°, 24h = 360°         │
│                                                         │
│  WHY HOURS? (historical reason)                         │
│    Astronomers historically measured RA by timing       │
│    when a star crossed the meridian (the N-S line       │
│    directly overhead). The sky rotates 360° in 24h,     │
│    so 1 hour of time = 15 degrees of arc.              │
│    The unit persisted for 400 years.                    │
│                                                         │
│  Examples:                                              │
│    Andromeda (M31) : RA = 00h 42m 44s                  │
│    Orion Nebula    : RA = 05h 35m 17s                  │
│    Virgo Cluster   : RA = 12h 27m 00s                  │
│                                                         │
└─────────────────────────────────────────────────────────┘

VISUAL DIAGRAM:
                    Dec = +90°
                       ●
                       |
    RA = 18h           |           RA = 6h
        ───────────────┼───────────────
       /     Dec=+45°  |  Dec=+45°     \
      /                |                \
─────●─────────────────●─────────────────●─────
  RA=12h          Dec = 0°          RA = 0h
─────●─────────────────●─────────────────●─────
      \                |                /
       \     Dec=-45°  |  Dec=-45°     /
        ───────────────┼───────────────
    RA = 18h           |           RA = 6h
                       |
                       ●
                    Dec = -90°
```

---

## Arcminutes and Arcseconds — Size in the Sky

```
ANGULAR SIZE UNITS
────────────────────────────────────────────────────────────────

When astronomers talk about how big something looks in the sky,
they use angular measurements — not physical size.

1 degree (°)     = 60 arcminutes (')
1 arcminute (')  = 60 arcseconds ('')
1 degree (°)     = 3600 arcseconds ('')

INTUITION GUIDE:

Your fist held at arm's length         ≈ 10°
Your thumb held at arm's length        ≈  2°
The full Moon                          ≈  0.5° = 30 arcminutes
Jupiter (at opposition)                ≈ 45 arcseconds
The Hubble Space Telescope resolution  ≈  0.05 arcseconds
The Event Horizon Telescope resolution ≈ 20 microarcseconds

ANDROMEDA GALAXY:
  Angular diameter ≈ 3° × 1°
  (6× the diameter of the full Moon — but too faint to see easily)

CONVERTING ARCSECONDS TO RADIANS (for calculations):
  1 arcsecond = π / (180 × 3600) ≈ 4.848 × 10⁻⁶ radians

SMALL ANGLE FORMULA (extremely useful):
  physical_size = distance × angular_size_in_radians
  
  Example: A galaxy is 1° wide and 10 Mpc away
  physical_size = 10 Mpc × (1° in radians)
               = 10 Mpc × 0.01745
               = 0.1745 Mpc
               ≈ 175,000 light years across
```

---

## System 2 — Galactic Coordinates (l / b)

**Used when studying the structure of the Milky Way.**

```
WHAT IT IS:
────────────────────────────────────────────────────────────────

Galactic coordinates use the plane of the Milky Way
as the reference plane — instead of Earth's equator.

The two coordinates are:

  l = Galactic Longitude  (0° to 360°, measured along galactic plane)
  b = Galactic Latitude   (-90° to +90°, measured from galactic plane)

ORIGIN POINT:
  l = 0°, b = 0° points toward the Galactic Center
  (the supermassive black hole Sgr A* in Sagittarius)

VISUAL:

         b = +90° (north galactic pole)
              ●
             /|\
            / | \
           /  |  \
──────────/───┼───\──────────  ← Galactic Plane (b = 0°)
          \   |   /
           \  |  /   l increases this way →
            \ | /
             \|/
              ●
         b = -90° (south galactic pole)

  ←──────── l = 180° ────────── l = 0° (galactic center) ──────────→

WHEN TO USE GALACTIC COORDINATES:
  • Studying the distribution of stars in the Milky Way
  • Understanding dust extinction (dust lives in the galactic plane)
  • Studying the galactic halo
  • Finding objects relative to the galactic plane

WHEN NOT TO USE:
  • Extragalactic work (use RA/Dec instead)
  • Querying most databases (they expect RA/Dec)
```

---

## System 3 — Ecliptic Coordinates (λ / β)

**Used in solar system science and spacecraft navigation.**

```
WHAT IT IS:
────────────────────────────────────────────────────────────────

Ecliptic coordinates use the plane of Earth's orbit
around the Sun (the ecliptic plane) as the reference.

  λ = Ecliptic Longitude  (0° to 360°)
  β = Ecliptic Latitude   (-90° to +90°)

The ecliptic is tilted 23.5° relative to the celestial equator
(this is Earth's axial tilt).

VISUAL:

            Celestial Equator (Dec = 0°)
          ─────────────────────────────────
         /    23.5°  /
        /           /  ← Ecliptic (Earth's orbital plane)
       /           /
      ─────────────────────────────────

WHEN TO USE ECLIPTIC COORDINATES:
  • Solar system objects (planets, asteroids, comets)
  • The zodiac constellations all lie near the ecliptic
  • Space mission planning and navigation

WHEN NOT TO USE:
  • Deep sky objects (use RA/Dec)
```

---

## Coordinate Conversion in Python

```python
from astropy.coordinates import SkyCoord
from astropy.coordinates import Galactic, BarycentricTrueEcliptic
import astropy.units as u

# ── CREATE A COORDINATE FROM RA/DEC ──────────────────────────

# Using degrees
m31 = SkyCoord(ra=10.6847*u.degree, dec=41.2691*u.degree)

# Using sexagesimal string (hours:minutes:seconds)
m31 = SkyCoord('00h42m44s', '+41d16m09s')

# Using object name (queries SIMBAD database)
m31 = SkyCoord.from_name('M31')

print(m31)
# → <SkyCoord (ICRS): (ra, dec) in deg (10.6847, 41.2691)>

# ── ACCESS INDIVIDUAL COMPONENTS ─────────────────────────────

print(m31.ra)                    # → 10.6847 deg
print(m31.dec)                   # → 41.2691 deg

# In different formats
print(m31.ra.deg)                # → 10.6847 (float, degrees)
print(m31.ra.hour)               # → 0.7123 (float, hours)
print(m31.ra.hms)                # → hms_tuple(h=0, m=42, s=44.3)
print(m31.dec.dms)               # → dms_tuple(d=41, m=16, s=9.0)

# Human-readable string
print(m31.ra.to_string(unit=u.hour))   # → '0h42m44.33s'
print(m31.dec.to_string())             # → '+41d16m09s'

# ── CONVERT TO GALACTIC COORDINATES ──────────────────────────

m31_galactic = m31.galactic

print(m31_galactic.l)            # → 121.17° (galactic longitude)
print(m31_galactic.b)            # → -21.57° (galactic latitude)

# ── CONVERT TO ECLIPTIC COORDINATES ──────────────────────────

m31_ecliptic = m31.geocentrictrueecliptic

print(m31_ecliptic.lon)          # → ecliptic longitude
print(m31_ecliptic.lat)          # → ecliptic latitude

# ── CONVERT BACK TO RA/DEC ───────────────────────────────────

back_to_equatorial = m31_galactic.icrs
print(back_to_equatorial.ra.deg) # → 10.6847

# ── COMPUTE ANGULAR SEPARATION BETWEEN TWO OBJECTS ───────────

m31  = SkyCoord.from_name('M31')
m33  = SkyCoord.from_name('M33')   # Triangulum Galaxy

separation = m31.separation(m33)

print(separation.deg)            # → ~14.8°
print(separation.arcmin)         # → ~889 arcminutes

# ── FIND ALL OBJECTS WITHIN A RADIUS ─────────────────────────

from astroquery.simbad import Simbad

# Query all objects within 30 arcminutes of M31's center
result = Simbad.query_region(m31, radius=30*u.arcmin)
print(result)

# ── CONVERT PIXEL COORDINATES TO SKY COORDINATES (WCS) ───────

from astropy.wcs import WCS

# Load a FITS file
with fits.open('observation.fits') as hdul:
    header = hdul[0].header
    data   = hdul[0].data

# Build WCS object from header
wcs = WCS(header)

# Convert pixel (x=500, y=500) to sky coordinates
sky = wcs.pixel_to_world(500, 500)
print(sky.ra.deg, sky.dec.deg)

# Convert sky coordinates to pixel position
pixel = wcs.world_to_pixel(m31)
print(pixel)   # → (x_pixel, y_pixel)
```

---

## The World Coordinate System (WCS) — How Pixels Map to Sky

```
WHAT IS WCS?
────────────────────────────────────────────────────────────────

Every FITS image has a header that tells Python:
"pixel (0,0) corresponds to this RA/Dec,
 and each pixel covers this many arcseconds of sky."

This mapping is called the World Coordinate System (WCS).

WITHOUT WCS:
  You have a grid of numbers.
  You know pixel (500, 500) is bright.
  But you don't know WHERE in the sky that is.

WITH WCS:
  You know pixel (500, 500) = RA 10.6847°, Dec 41.2691°
  = The nucleus of the Andromeda Galaxy.

THE KEY HEADER KEYWORDS FOR WCS:

  CRPIX1, CRPIX2  → Reference pixel (the "anchor point")
  CRVAL1, CRVAL2  → Sky coordinates at the reference pixel
  CDELT1, CDELT2  → Degrees per pixel (the "scale")
  CTYPE1, CTYPE2  → Coordinate type ('RA---TAN', 'DEC--TAN')

VISUAL DIAGRAM:

  IMAGE PIXEL SPACE             SKY COORDINATE SPACE
  ──────────────────            ────────────────────

  (0,0) ─────────────────────→ (RA=11.05°, Dec=40.86°)
     │                                  │
     │   (500,500) ──────────────────→ (RA=10.68°, Dec=41.27°)
     │      ●  ← galactic center                ●
     │                                           ↑
     │                                     Andromeda nucleus
     ↓
  (999,999) ──────────────────→ (RA=10.32°, Dec=41.67°)


THE MATH (simplified):
  RA  = CRVAL1 + CDELT1 × (pixel_x - CRPIX1)
  Dec = CRVAL2 + CDELT2 × (pixel_y - CRPIX2)

  (Actual WCS can include rotation and distortion terms,
   but this is the intuition.)
```

---

## Common Coordinate Mistakes — And How to Avoid Them

```
MISTAKE 1: Confusing pixel coordinates with sky coordinates
───────────────────────────────────────────────────────────
WRONG:  data[ra, dec]         ← These are not pixel indices
RIGHT:  use WCS to convert RA/Dec to pixel first,
        then index: data[pixel_y, pixel_x]

Note: array indexing is [row, col] = [y, x], not [x, y]


MISTAKE 2: Forgetting RA decreases left-to-right
───────────────────────────────────────────────────────────
RA increases EASTWARD on the sky.
On a standard sky image, East is to the LEFT.
This means RA increases to the LEFT (opposite to x-axis).
That is why CDELT1 is often NEGATIVE.

Standard image display: origin='lower' in matplotlib
puts South at bottom, North at top, East to left.


MISTAKE 3: Mixing degrees and hours for RA
───────────────────────────────────────────────────────────
WRONG:  SkyCoord(ra=0.711*u.degree, dec=41.27*u.degree)
        (This is almost the south celestial pole!)

RIGHT:  SkyCoord(ra=10.684*u.degree, dec=41.27*u.degree)
   or   SkyCoord('00h42m44s', '+41d16m09s')

RA = 0h 42m 44s = 10.684 degrees (multiply hours by 15)


MISTAKE 4: Not accounting for epoch (J2000 vs B1950)
───────────────────────────────────────────────────────────
The celestial coordinate system slowly drifts due to
Earth's precession (wobble). Coordinates are defined
relative to a reference epoch.

J2000.0 = the standard. Most modern catalogs use this.
B1950.0 = older catalogs. Convert if mixing sources.

In astropy, ICRS (International Celestial Reference System)
is essentially equivalent to J2000 for most purposes.


MISTAKE 5: Separation calculation on large angles
───────────────────────────────────────────────────────────
WRONG:  separation = sqrt((ra1-ra2)² + (dec1-dec2)²)
        (This is wrong near the poles and for large angles)

RIGHT:  use SkyCoord.separation() — it uses the correct
        spherical trigonometry formula automatically
```

---

## Reference Table — Key Coordinates

```
OBJECT                    RA              DEC        NOTES
──────────────────────────────────────────────────────────────

GALAXIES
Andromeda (M31)          00h 42m 44s    +41° 16'   Episode 1
Triangulum (M33)         01h 33m 51s    +30° 39'   Local Group
Whirlpool (M51)          13h 29m 53s    +47° 12'   Face-on spiral
Sombrero (M104)          12h 39m 59s    -11° 37'   Edge-on
Milky Way Center (Sgr A*)17h 45m 40s    -29° 00'   Black hole

NEBULAE
Orion Nebula (M42)       05h 35m 17s    -05° 23'   Star forming
Crab Nebula (M1)         05h 34m 32s    +22° 01'   Supernova remnant
Pillars of Creation      18h 18m 48s    -13° 49'   Eagle Nebula
Ring Nebula (M57)        18h 53m 35s    +33° 02'   Planetary nebula

STAR CLUSTERS
Pleiades (M45)           03h 47m 24s    +24° 07'   Open cluster
Omega Centauri           13h 26m 47s    -47° 29'   Globular cluster
Hercules Cluster (M13)   16h 41m 41s    +36° 28'   Globular cluster

USEFUL REFERENCE POINTS
North Celestial Pole     varies         +90° 00'   (near Polaris)
Galactic Center          17h 45m 40s    -29° 00'   Sgr A*
Vernal Equinox           00h 00m 00s    +00° 00'   RA origin point
```

---

## Further Reading

| Resource | What it covers | URL |
|----------|---------------|-----|
| Astropy Coordinates docs | Full SkyCoord reference | https://docs.astropy.org/en/stable/coordinates |
| WCS standard | How pixels map to sky | https://fits.gsfc.nasa.gov/fits_wcs.html |
| SIMBAD database | Object name → coordinates | https://simbad.u-strasbg.fr |
| NASA/IPAC coordinate converter | Online tool | https://ned.ipac.caltech.edu/forms/calculator.html |

---

*Part of the Signal & Noise resource library.*  
*github.com/signal-and-noise*

---

> *"The universe does not know about our coordinate systems.*  
> *But we need them to find our way around."*
