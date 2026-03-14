# The FITS File — A Visual Guide

> *"A FITS file is not an image. It is a scientific measurement.*  
> *Understanding the difference changes everything."*

---

## What is FITS?

**FITS** stands for **Flexible Image Transport System.**

It is the universal file format for astronomical data.  
Every major telescope on Earth — and in orbit — produces FITS files.  
It has been the standard since **1981** and is maintained by NASA.

When Hubble takes an observation, the data is stored as FITS.  
When JWST captures a galaxy, the data is stored as FITS.  
When a radio telescope detects a pulsar, the data is stored as FITS.

If astronomy has a native language, FITS is it.

---

## FITS vs. JPEG — The Critical Difference

This is the most important concept in this entire guide.

```
JPEG FILE                          FITS FILE
─────────────────────────────────────────────────────────

PURPOSE:                           PURPOSE:
Display to human eyes              Scientific measurement

STORES:                            STORES:
Compressed RGB color values        Raw photon counts / flux
(3 bytes per pixel)                (4 or 8 bytes per pixel)

DYNAMIC RANGE:                     DYNAMIC RANGE:
0 to 255 per channel               -2,147,483,648 to +2,147,483,647
(256 brightness levels)            (billions of brightness levels)

COMPRESSION:                       COMPRESSION:
Lossy — information is             Lossless — every measurement
permanently discarded              is preserved exactly

METADATA:                          METADATA:
Basic EXIF (camera, date)          Full scientific provenance
                                   (telescope, instrument, exposure,
                                   coordinates, calibration, filters...)

EXAMPLE USE:                       EXAMPLE USE:
Sharing a holiday photo            Recording a supernova detection

CAN YOU DO SCIENCE WITH IT?        CAN YOU DO SCIENCE WITH IT?
No                                 Yes
```

**The bottom line:**  
A JPEG tells you what something *looks like.*  
A FITS file tells you what something *is.*

---

## Anatomy of a FITS File

A FITS file is a **container** — like a ZIP file, but for science.  
Inside it, data is organized into units called **HDUs.**

```
┌─────────────────────────────────────────────────────────────────┐
│                        FITS FILE (.fits)                        │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                    HDU 0 — PRIMARY                        │  │
│  │                                                           │  │
│  │  ┌─────────────────────────────────────────────────────┐  │  │
│  │  │                     HEADER                          │  │  │
│  │  │                                                     │  │  │
│  │  │  SIMPLE  =    T        / Conforms to FITS standard  │  │  │
│  │  │  BITPIX  =  -32        / 32-bit floating point      │  │  │
│  │  │  NAXIS   =    2        / Number of dimensions       │  │  │
│  │  │  NAXIS1  = 1000        / Width in pixels            │  │  │
│  │  │  NAXIS2  = 1000        / Height in pixels           │  │  │
│  │  │  TELESCOP= 'HST   '    / Hubble Space Telescope     │  │  │
│  │  │  INSTRUME= 'ACS   '    / Advanced Camera for Surveys│  │  │
│  │  │  DATE-OBS= '2003-08-11'/ Observation date           │  │  │
│  │  │  EXPTIME =  2400.0     / Exposure time (seconds)    │  │  │
│  │  │  FILTER  = 'F814W '    / Filter used (near infrared)│  │  │
│  │  │  RA_TARG =   10.6847   / Right Ascension of target  │  │  │
│  │  │  DEC_TARG=   41.2691   / Declination of target      │  │  │
│  │  │  BUNIT   = 'ELECTRONS' / Units of pixel values      │  │  │
│  │  │  ...                                                │  │  │
│  │  └─────────────────────────────────────────────────────┘  │  │
│  │                                                           │  │
│  │  ┌─────────────────────────────────────────────────────┐  │  │
│  │  │                      DATA                           │  │  │
│  │  │                                                     │  │  │
│  │  │   A 2D matrix of numbers (the image):               │  │  │
│  │  │                                                     │  │  │
│  │  │   col→  0      1      2      3      4               │  │  │
│  │  │   row                                               │  │  │
│  │  │    0  [  2.1,   2.3,   1.9,   2.0,   2.1, ...]     │  │  │
│  │  │    1  [  2.4,   3.1,   4.5,   3.2,   2.3, ...]     │  │  │
│  │  │    2  [  3.0,  12.4,  89.3,  11.2,   3.1, ...]     │  │  │
│  │  │    3  [  4.1,  45.2, 255.0,  44.8,   4.0, ...]     │  │  │
│  │  │    4  [  3.0,  12.1,  88.7,  11.0,   3.0, ...]     │  │  │
│  │  │    ...                                              │  │  │
│  │  │                                                     │  │  │
│  │  │   Each number = photon flux at that sky position    │  │  │
│  │  │   High value  = bright region (stars, galaxy core)  │  │  │
│  │  │   Low value   = dark region (empty space)           │  │  │
│  │  └─────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │               HDU 1 — EXTENSION (optional)                │  │
│  │   May contain: error arrays, weight maps, catalogs,       │  │
│  │   calibration data, world coordinate system info          │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │               HDU 2 — EXTENSION (optional)                │  │
│  │   Some files have many extensions — one per filter,       │  │
│  │   one per detector chip, one per observation epoch        │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## What is an HDU?

**HDU = Header Data Unit**

Think of a FITS file as a book.  
Each HDU is a chapter.  
Every chapter has:
- A **title page** (the Header) — describing what's inside
- **Content** (the Data) — the actual scientific measurements

```
HDU TYPES:
─────────────────────────────────────────────────────

PRIMARY HDU (HDU 0)
  → Always present. Always first.
  → Contains the main image data (usually)
  → The header here describes the whole observation

IMAGE EXTENSION (HDU 1, 2, 3...)
  → Additional images
  → Example: error map, data quality flags
  → Same structure as PRIMARY but tagged as extension

BINARY TABLE EXTENSION
  → Tabular data — like a spreadsheet
  → Example: a catalog of all detected stars
  → Columns: RA, Dec, Brightness, Size, etc.

ASCII TABLE EXTENSION
  → Human-readable table data
  → Less common in modern files
```

---

## The Header — Explained Line by Line

The header is a sequence of 80-character records called **cards.**  
Each card contains: a keyword, a value, and an optional comment.

```
KEYWORD   = VALUE                  / COMMENT
─────────────────────────────────────────────────────────────────

SIMPLE  =                    T / file conforms to FITS standard
│                               └── Comment explaining the field
│                          └────── Value (T = True)
└──────────────────────────────── Keyword (8 characters max)

BITPIX  =                  -32 / number of bits per data pixel
│ → -32 means 32-bit floating point numbers
│ → Common values:
│     8   = unsigned 8-bit integer  (0 to 255)
│    16   = signed 16-bit integer   (-32768 to 32767)
│    32   = signed 32-bit integer   (-2B to +2B)
│   -32   = 32-bit floating point   (decimals, ~7 sig. figures)
│   -64   = 64-bit floating point   (decimals, ~15 sig. figures)

NAXIS   =                    2 / number of data axes
│ → 2 = this is a 2D image (rows × columns)
│ → 1 = spectrum (1D)
│ → 3 = data cube (2D image + wavelength dimension)

NAXIS1  =                 1000 / length of data axis 1
NAXIS2  =                 1000 / length of data axis 2
│ → Together: a 1000 × 1000 pixel image = 1,000,000 measurements

CRPIX1  =                500.0 / Pixel coordinate of reference point
CRPIX2  =                500.0 / Pixel coordinate of reference point
CRVAL1  =            10.684708 / RA at reference pixel (degrees)
CRVAL2  =            41.269065 / Dec at reference pixel (degrees)
│ → World Coordinate System (WCS)
│ → Maps pixel positions to sky coordinates
│ → This is how Python knows where in the sky each pixel points

CDELT1  =           -0.0002778 / Coordinate increment per pixel
CDELT2  =            0.0002778 / Coordinate increment per pixel
│ → How many degrees of sky each pixel covers
│ → 0.0002778 degrees = 1 arcsecond per pixel
│ → (There are 3600 arcseconds in 1 degree)

BUNIT   = 'ELECTRONS'          / Units of image data
│ → What the numbers in the data array actually mean
│ → Common values:
│     ELECTRONS  = raw detector counts
│     ADU        = Analog-to-Digital Units (similar)
│     MJy/sr     = Megajansky per steradian (calibrated flux)
│     counts/s   = count rate after time normalization
```

---

## From Photons to Numbers — The Full Chain

This is what actually happens between a star emitting light  
and a number appearing in your FITS file.

```
STEP 1 — EMISSION
┌──────────────────────────────────────────────────────────┐
│  A star undergoes nuclear fusion                         │
│  Energy is released as photons                           │
│  Photons travel at 300,000 km/s                          │
│  For Andromeda: 2.5 million years of travel              │
└──────────────────────────┬───────────────────────────────┘
                           │
                           ▼
STEP 2 — COLLECTION
┌──────────────────────────────────────────────────────────┐
│  Telescope mirror (primary)                              │
│  Focuses incoming photons onto a focal plane             │
│  Larger mirror = more photons collected                  │
│  Hubble mirror: 2.4 metres diameter                      │
│  JWST mirror:   6.5 metres diameter                      │
└──────────────────────────┬───────────────────────────────┘
                           │
                           ▼
STEP 3 — FILTERING (optional)
┌──────────────────────────────────────────────────────────┐
│  A filter selects a specific wavelength range            │
│  F814W = near-infrared (814 nanometres)                  │
│  H-alpha = hydrogen emission line (656 nm)               │
│  Filters are how astronomers isolate specific physics    │
└──────────────────────────┬───────────────────────────────┘
                           │
                           ▼
STEP 4 — DETECTION
┌──────────────────────────────────────────────────────────┐
│  CCD or CMOS detector (like a digital camera sensor      │
│  but extraordinarily sensitive)                          │
│                                                          │
│  Each pixel on the detector is a tiny capacitor          │
│  When a photon hits it, it releases an electron          │
│  The capacitor accumulates electrons during exposure     │
│                                                          │
│  Photon → Electron (photoelectric effect)                │
│  Einstein won the Nobel Prize for explaining this. 1905. │
└──────────────────────────┬───────────────────────────────┘
                           │
                           ▼
STEP 5 — READOUT
┌──────────────────────────────────────────────────────────┐
│  After exposure ends, electronics read each pixel        │
│  Electron count → Voltage → Digital number               │
│  This number is the ADU (Analog-to-Digital Unit)         │
│                                                          │
│  NOISE IS INTRODUCED HERE:                               │
│  • Read noise: electronics aren't perfect                │
│  • Dark current: thermal electrons (even with no light)  │
│  • Bias: systematic offset added intentionally           │
│  These are corrected in calibration (Step 6)             │
└──────────────────────────┬───────────────────────────────┘
                           │
                           ▼
STEP 6 — CALIBRATION
┌──────────────────────────────────────────────────────────┐
│  Raw numbers are corrected for instrument effects:       │
│                                                          │
│  BIAS subtraction                                        │
│  → Removes systematic electronic offset                  │
│                                                          │
│  DARK subtraction                                        │
│  → Removes thermal noise (detector generates heat)       │
│                                                          │
│  FLAT FIELD correction                                   │
│  → Corrects for uneven sensitivity across detector       │
│  → Some pixels are more sensitive than others            │
│                                                          │
│  After calibration: numbers represent true photon flux   │
└──────────────────────────┬───────────────────────────────┘
                           │
                           ▼
STEP 7 — STORAGE
┌──────────────────────────────────────────────────────────┐
│  Calibrated numbers + observation metadata               │
│  Packaged into a FITS file                               │
│  Uploaded to public archive                              │
│  Available to anyone. Forever.                           │
└──────────────────────────┬───────────────────────────────┘
                           │
                           ▼
STEP 8 — YOU
┌──────────────────────────────────────────────────────────┐
│  Python downloads the FITS file                          │
│  astropy reads the header and data array                 │
│  matplotlib renders the numbers as light                 │
│  The photon completes its journey                        │
│                                                          │
│  2.5 million years.                                      │
│  On your screen.                                         │
│  Tonight.                                                │
└──────────────────────────────────────────────────────────┘
```

---

## Common FITS Header Keywords Reference

A quick reference for the keywords you will encounter most often.

```
KEYWORD     MEANING                          EXAMPLE VALUE
─────────────────────────────────────────────────────────────

SIMPLE      Conforms to FITS standard        T
BITPIX      Bits per pixel / data type       -32
NAXIS       Number of dimensions             2
NAXIS1      Size of axis 1 (width)           1000
NAXIS2      Size of axis 2 (height)          1000

TELESCOP    Telescope name                   'HST'
INSTRUME    Instrument name                  'ACS/WFC'
FILTER      Filter used                      'F814W'
EXPTIME     Exposure time in seconds         2400.0
DATE-OBS    Date of observation              '2003-08-11'
TIME-OBS    Time of observation              '14:32:11'

OBJECT      Target name                      'M31'
TARGNAME    Target name (HST convention)     'ANDROMEDA'
RA_TARG     Target Right Ascension           10.6847
DEC_TARG    Target Declination               41.2691

CRPIX1      Reference pixel X                500.0
CRPIX2      Reference pixel Y                500.0
CRVAL1      RA at reference pixel (deg)      10.6847
CRVAL2      Dec at reference pixel (deg)     41.2691
CDELT1      Degrees per pixel (X axis)       -0.0002778
CDELT2      Degrees per pixel (Y axis)        0.0002778
CTYPE1      Coordinate type axis 1           'RA---TAN'
CTYPE2      Coordinate type axis 2           'DEC--TAN'

BUNIT       Units of pixel values            'ELECTRONS'
BZERO       Zero point offset                0.0
BSCALE      Scale factor for pixel values    1.0

HISTORY     Processing history               'Calibrated 2003-08-15'
COMMENT     Free text comment                'Science data product'
```

---

## Working with FITS in Python — Quick Reference

```python
from astropy.io import fits
import numpy as np

# ── OPEN A FILE ──────────────────────────────────────────────
hdul = fits.open('observation.fits')

# ── INSPECT STRUCTURE ────────────────────────────────────────
hdul.info()
# Prints: name, type, dimensions, format of each HDU

# ── READ THE HEADER ──────────────────────────────────────────
header = hdul[0].header

# Get a specific value
telescope = header['TELESCOP']      # → 'HST'
exptime   = header['EXPTIME']       # → 2400.0
ra        = header['RA_TARG']       # → 10.6847

# Print all header entries
for key, value in header.items():
    print(f"{key}: {value}")

# ── READ THE DATA ────────────────────────────────────────────
data = hdul[0].data                 # → numpy 2D array

print(data.shape)                   # → (1000, 1000)
print(data.dtype)                   # → float32
print(data.min(), data.max())       # → -2.1, 48291.3

# ── BASIC STATISTICS ─────────────────────────────────────────
print(np.nanmin(data))              # Minimum (ignores NaN)
print(np.nanmax(data))              # Maximum
print(np.nanmean(data))             # Mean
print(np.nanmedian(data))           # Median

# ── ACCESS A SPECIFIC PIXEL ──────────────────────────────────
# NOTE: array indexing is [row, col] = [y, x]
pixel_value = data[500, 500]        # Center pixel

# ── CROP A REGION ────────────────────────────────────────────
# data[y_start:y_end, x_start:x_end]
crop = data[400:600, 400:600]       # 200×200 center crop

# ── ALWAYS CLOSE THE FILE ────────────────────────────────────
hdul.close()

# ── OR USE CONTEXT MANAGER (preferred) ───────────────────────
with fits.open('observation.fits') as hdul:
    data   = hdul[0].data.copy()    # .copy() because file closes
    header = hdul[0].header.copy()
# File is automatically closed here
```

---

## Coordinate Systems — A Visual Primer

Astronomy uses its own coordinate system for locating objects in the sky.  
Understanding this unlocks every sky query you will ever make.

```
THE CELESTIAL SPHERE
────────────────────────────────────────────────────────────

Imagine the Earth surrounded by a giant invisible sphere.
All stars and galaxies appear projected onto this sphere.
We describe positions on it using two angles:

                        North Celestial Pole (+90°)
                               ●
                              /|\
                             / | \
                            /  |  \
          RA = 6h          /   |   \         RA = 18h
              ●───────────────────────────●
             /    Dec = +45° ─ ─ ─ ─ ─   \
            /                              \
           /  Dec = 0° (Celestial Equator)  \
    RA=12h ●──────────────●──────────────── ● RA=0h
           \              |                /
            \             |               /
             \            |              /
              ●───────────────────────●
                           |
                        South Celestial Pole (-90°)

DECLINATION (Dec):
→ Like latitude on Earth, but for the sky
→ Ranges from -90° (south pole) to +90° (north pole)
→ 0° = directly above Earth's equator
→ Andromeda: Dec = +41.3° (northern sky)

RIGHT ASCENSION (RA):
→ Like longitude on Earth, but measured in TIME not degrees
→ Ranges from 0h to 24h (one full rotation = 24 hours)
→ 1 hour = 15 degrees of arc
→ Starts at the Vernal Equinox (a fixed point in the sky)
→ Andromeda: RA = 0h 42m 44s

CONVERTING RA to degrees:
→ RA (degrees) = RA (hours) × 15
→ 0h 42m 44s = 0.7122h × 15 = 10.684°

WHY HOURS FOR RA?
→ Historical: before clocks, astronomers timed when stars
  crossed the meridian (the north-south line overhead)
→ The sky rotates 360° in 24 hours = 15° per hour
→ The unit stuck
```

---

## FITS File Sizes — What to Expect

```
FILE TYPE                    TYPICAL SIZE    PIXELS
─────────────────────────────────────────────────────

Small test / sample image      ~1 MB         512 × 512
Standard survey cutout         ~4 MB         1000 × 1000
Full survey frame (DSS)        ~64 MB        4096 × 4096
Hubble ACS full frame          ~256 MB       4096 × 4096 (×2 chips)
JWST NIRCam full frame         ~500 MB       Multiple detectors
Vera Rubin (per night)         ~20 TB        Full sky mosaic

FOR THIS CHANNEL:
We work with small-to-medium cutouts (1-64 MB)
Large enough for real science, small enough for Colab
```

---

## Troubleshooting Common FITS Issues

```
PROBLEM                    CAUSE                    SOLUTION
─────────────────────────────────────────────────────────────────

Image appears completely   Linear scaling on         Use simple_norm()
black                      high dynamic range data   with 'sqrt' stretch

NaN values in data         Missing / flagged pixels  Use np.nanmin()
                           (common in Hubble data)   instead of np.min()

Image appears upside down  Different origin          Add origin='lower'
                           conventions               to imshow()

Header keyword missing     Not all keywords are      Use header.get('KEY',
(KeyError)                 present in all files      default_value)

File too large for Colab   Full-frame detector data  Request smaller
                                                     cutout from SkyView
                                                     (pixels=500)

astropy can't open file    Corrupted download        Re-download the file
                                                     Check file size > 0
```

---

## Further Reading

| Resource | What it covers | URL |
|----------|---------------|-----|
| FITS Standard (official) | The complete specification | https://fits.gsfc.nasa.gov/fits_standard.html |
| Astropy FITS tutorial | Working with FITS in Python | https://docs.astropy.org/en/stable/io/fits |
| World Coordinate System | How pixels map to sky coords | https://fits.gsfc.nasa.gov/fits_wcs.html |
| NASA SkyView | Querying sky surveys | https://skyview.gsfc.nasa.gov |
| MAST Archive | Hubble and JWST data portal | https://mast.stsci.edu |

---

*Part of the Signal & Noise resource library.*  
*github.com/signal-and-noise*

---

> *"Astronomy images are not pictures.*  
> *They are measurements.*  
> *The difference is everything."*

