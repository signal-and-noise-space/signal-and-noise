# Astronomy Python Cheatsheet

> *"The commands you will use in 90% of all sessions —*  
> *in one place, with no fluff."*

---

## Quick Install

```python
# Run this at the top of every new Google Colab session
!pip install astropy astroquery photutils lightkurve --quiet

# Verify versions
import astropy; print('astropy:', astropy.__version__)
import astroquery; print('astroquery:', astroquery.__version__)
```

---

## 1. FITS Files

```python
from astropy.io import fits
import numpy as np

# ── OPEN & INSPECT ───────────────────────────────────────────
with fits.open('file.fits') as hdul:
    hdul.info()                        # Show HDU structure
    header = hdul[0].header.copy()     # Copy header
    data   = hdul[0].data.copy()       # Copy image data

# ── READ HEADER VALUES ───────────────────────────────────────
telescope = header['TELESCOP']         # Exact key (raises error if missing)
exptime   = header.get('EXPTIME', 1.0) # Safe read with default value

# Print all header entries
for key, value in header.items():
    if key.strip():
        print(f"{key:<10} = {value}")

# ── IMAGE STATISTICS ─────────────────────────────────────────
print(data.shape)                      # (rows, cols) = (height, width)
print(data.dtype)                      # float32, int16, etc.
print(np.nanmin(data))                 # Minimum (ignores NaN)
print(np.nanmax(data))                 # Maximum
print(np.nanmean(data))                # Mean
print(np.nanmedian(data))              # Median
print(np.nanstd(data))                 # Standard deviation

# ── CROP A REGION ────────────────────────────────────────────
# data[y_start:y_end, x_start:x_end]  (row first, then column)
crop = data[400:600, 400:600]          # 200×200 center region

# Safe crop relative to center
cy, cx = data.shape[0]//2, data.shape[1]//2
size = 200
crop = data[cy-size:cy+size, cx-size:cx+size]

# ── SAVE A NEW FITS FILE ─────────────────────────────────────
hdu = fits.PrimaryHDU(data=processed_data, header=header)
hdul_out = fits.HDUList([hdu])
hdul_out.writeto('output.fits', overwrite=True)
```

---

## 2. Sky Coordinates

```python
from astropy.coordinates import SkyCoord
import astropy.units as u

# ── CREATE COORDINATES ───────────────────────────────────────
# From degrees
c = SkyCoord(ra=10.6847*u.degree, dec=41.2691*u.degree)

# From sexagesimal string
c = SkyCoord('00h42m44s', '+41d16m09s')

# From object name (queries SIMBAD — needs internet)
c = SkyCoord.from_name('M31')
c = SkyCoord.from_name('Andromeda Galaxy')
c = SkyCoord.from_name('NGC 224')      # Same object, different name

# ── READ COMPONENTS ──────────────────────────────────────────
c.ra.deg                               # RA in decimal degrees → 10.6847
c.ra.hour                              # RA in decimal hours   → 0.7123
c.ra.hms                               # RA as (h, m, s) tuple
c.dec.deg                              # Dec in decimal degrees
c.dec.dms                              # Dec as (d, m, s) tuple

# Human-readable strings
c.ra.to_string(unit=u.hour)            # '0h42m44.33s'
c.dec.to_string()                      # '+41d16m09.0s'

# ── CONVERT BETWEEN SYSTEMS ──────────────────────────────────
c_gal = c.galactic                     # Galactic coordinates
c_gal.l.deg                            # Galactic longitude
c_gal.b.deg                            # Galactic latitude

c_back = c_gal.icrs                    # Back to equatorial

# ── ANGULAR SEPARATION ───────────────────────────────────────
m31 = SkyCoord.from_name('M31')
m33 = SkyCoord.from_name('M33')

sep = m31.separation(m33)
sep.deg                                # Separation in degrees → ~14.8
sep.arcmin                             # Separation in arcminutes
sep.arcsec                             # Separation in arcseconds

# ── UNIT CONVERSIONS ─────────────────────────────────────────
(1.5 * u.degree).to(u.arcmin)          # 90 arcmin
(3600 * u.arcsec).to(u.degree)         # 1 degree
(10 * u.kpc).to(u.lyr)                 # kiloparsecs to light years
(1 * u.parsec).to(u.lyr)              # 1 parsec = 3.2616 light years
```

---

## 3. Querying Astronomical Databases

```python
from astroquery.skyview import SkyView
from astroquery.simbad import Simbad
from astroquery.vizier import Vizier
from astroquery.mast import Observations
from astropy.coordinates import SkyCoord
import astropy.units as u

# ── SKYVIEW — Virtual Telescope (any survey, any position) ───
coords = SkyCoord.from_name('M31')

images = SkyView.get_images(
    position=coords,
    survey=['DSS'],              # Survey name
    pixels=1000                  # Image size in pixels
)
data   = images[0][0].data
header = images[0][0].header

# Available surveys (selection):
# 'DSS'          Digitized Sky Survey (optical)
# 'DSS2 Red'     DSS2 Red filter
# '2MASS-J'      Near infrared J band
# '2MASS-K'      Near infrared K band
# 'GALEX Near UV' Ultraviolet
# 'WISE 3.4'     Mid infrared (WISE mission)
# 'NVSS'         Radio continuum (1.4 GHz)

# List all available surveys
survey_list = SkyView.list_surveys()

# ── SIMBAD — Object Database ─────────────────────────────────

# Query a single object by name
result = Simbad.query_object('M31')
print(result.colnames)         # See available columns
print(result['RA', 'DEC', 'MAIN_ID'])

# Query all objects in a region
result = Simbad.query_region(
    coords,
    radius=30*u.arcmin
)

# Query with custom fields
custom_simbad = Simbad()
custom_simbad.add_votable_fields('distance', 'flux(V)', 'sptype')
result = custom_simbad.query_object('M31')

# ── VIZIER — Astronomical Catalogs ───────────────────────────

# Query a specific catalog by ID
# (Catalog IDs from vizier.u-strasbg.fr)
v = Vizier(columns=['*'])      # Return all columns

# Gaia DR3 catalog
result = v.query_object('M31', catalog='I/355/gaiadr3', radius=5*u.arcmin)

# 2MASS point source catalog
result = v.query_object('M31', catalog='II/246/out', radius=5*u.arcmin)

# ── MAST — Hubble & JWST Archive ─────────────────────────────

# Search for Hubble observations of M31
obs_table = Observations.query_object(
    'M31',
    radius=0.1*u.degree
)

# Filter by telescope/instrument
mask = obs_table['obs_collection'] == 'HST'
hst_obs = obs_table[mask]
print(hst_obs['obsid', 'filters', 'exptime', 't_obs_release'])

# Get download links
data_products = Observations.get_product_list(hst_obs[0])
manifest = Observations.download_products(
    data_products,
    productType='SCIENCE'
)
```

---

## 4. Visualization

```python
import matplotlib.pyplot as plt
import matplotlib.colors as mcolors
from astropy.visualization import simple_norm, ImageNormalize
from astropy.visualization import ZScaleInterval, SqrtStretch

# ── BASIC IMAGE DISPLAY ──────────────────────────────────────
fig, ax = plt.subplots(figsize=(8, 8))
ax.imshow(data, cmap='gray', origin='lower')
plt.colorbar(ax.images[0], ax=ax, label='Photon flux')
plt.show()

# ── CONTRAST STRETCHING ──────────────────────────────────────

# simple_norm — quick and effective
norm = simple_norm(data, stretch='sqrt', percent=99.5)
# stretch options: 'linear', 'sqrt', 'log', 'asinh', 'power'

# ZScale — mimics DS9 astronomy viewer behavior
norm_z = ImageNormalize(data, interval=ZScaleInterval())

# Manual min/max
norm_manual = simple_norm(data, stretch='sqrt', min_cut=0, max_cut=5000)

# Apply norm to imshow
ax.imshow(data, cmap='gray', norm=norm, origin='lower')

# ── COLORMAPS ────────────────────────────────────────────────
# Scientific standard     : 'gray'
# Thermal / galaxy        : 'inferno'
# High contrast           : 'plasma'
# Perceptually uniform    : 'viridis'
# Blue tones / nebulae    : 'Blues_r'
# Classic astronomy       : 'hot'
# Diverging (residuals)   : 'RdBu_r'

# ── ADDING ANNOTATIONS ───────────────────────────────────────
fig, ax = plt.subplots(figsize=(10, 10), facecolor='black')
ax.imshow(data, cmap='inferno', norm=norm, origin='lower')
ax.axis('off')

# Text annotation
ax.text(0.02, 0.98, 'M31 — Andromeda Galaxy',
        transform=ax.transAxes,
        color='white', fontsize=14, fontweight='bold',
        va='top')

# Arrow pointing to a feature
ax.annotate('Galactic nucleus',
            xy=(500, 500),           # Arrow tip (pixel coords)
            xytext=(600, 600),       # Text position
            color='cyan', fontsize=10,
            arrowprops=dict(arrowstyle='->', color='cyan'))

# Circle marking a region
from matplotlib.patches import Circle
circle = Circle((500, 500), radius=50,
                fill=False, edgecolor='cyan', linewidth=1.5)
ax.add_patch(circle)

# ── SIDE BY SIDE COMPARISON ──────────────────────────────────
fig, axes = plt.subplots(1, 2, figsize=(16, 8))
axes[0].imshow(data, cmap='gray', origin='lower')
axes[0].set_title('Before')
axes[1].imshow(data, cmap='gray', norm=norm, origin='lower')
axes[1].set_title('After')
plt.tight_layout()
plt.show()

# ── SAVE HIGH RESOLUTION ─────────────────────────────────────
plt.savefig('output.png', dpi=150, bbox_inches='tight',
            facecolor='black')
```

---

## 5. Source Detection (Photutils)

```python
from photutils.detection import DAOStarFinder, IRAFStarFinder
from photutils.background import Background2D, MedianBackground
from photutils.aperture import CircularAperture, aperture_photometry
import numpy as np

# ── ESTIMATE AND SUBTRACT BACKGROUND ─────────────────────────
bkg_estimator = MedianBackground()
bkg = Background2D(data, (50, 50),           # Box size for local background
                   filter_size=(3, 3),
                   bkg_estimator=bkg_estimator)

data_bkg_subtracted = data - bkg.background

# ── DETECT STARS ─────────────────────────────────────────────
# Estimate noise level
mean, median, std = np.nanmean(data), np.nanmedian(data), np.nanstd(data)

# DAOStarFinder — good for stellar fields
daofind = DAOStarFinder(
    fwhm=3.0,                              # Expected star width (pixels)
    threshold=5.0 * std                    # Detection threshold (5σ)
)
sources = daofind(data_bkg_subtracted)

print(f"Detected {len(sources)} sources")
print(sources['id', 'xcentroid', 'ycentroid', 'peak', 'flux'])

# ── APERTURE PHOTOMETRY ──────────────────────────────────────
# Define apertures at source positions
positions = list(zip(sources['xcentroid'], sources['ycentroid']))
apertures = CircularAperture(positions, r=5.0)  # 5-pixel radius

# Measure flux in each aperture
phot_table = aperture_photometry(data_bkg_subtracted, apertures)
print(phot_table['id', 'xcenter', 'ycenter', 'aperture_sum'])

# ── OVERLAY DETECTIONS ON IMAGE ──────────────────────────────
fig, ax = plt.subplots(figsize=(10, 10))
ax.imshow(data, cmap='gray', norm=norm, origin='lower')
apertures.plot(axes=ax, color='red', lw=1.5, alpha=0.7)
ax.set_title(f'{len(sources)} sources detected')
plt.show()
```

---

## 6. Light Curves (Lightkurve — Kepler / TESS)

```python
import lightkurve as lk

# ── SEARCH FOR DATA ──────────────────────────────────────────
# Search Kepler data for a target
search_result = lk.search_lightcurve(
    'Kepler-10',                           # Target name or KIC ID
    mission='Kepler',
    cadence='long'                         # 'long' (30min) or 'short' (1min)
)
print(search_result)                       # Shows available quarters

# Search TESS data
search_result = lk.search_lightcurve(
    'TOI-700',
    mission='TESS'
)

# ── DOWNLOAD AND LOAD ────────────────────────────────────────
lc_collection = search_result.download_all()
lc = lc_collection.stitch()               # Combine all quarters

# ── INSPECT ──────────────────────────────────────────────────
print(lc)
print(lc.time[:5])                        # First 5 timestamps
print(lc.flux[:5])                        # First 5 flux values

# ── BASIC PLOT ───────────────────────────────────────────────
lc.plot()
plt.show()

# ── FLATTEN (remove stellar variability) ─────────────────────
flat_lc = lc.flatten(window_length=401)   # Savitzky-Golay filter
flat_lc.plot()

# ── FOLD ON A KNOWN PERIOD ───────────────────────────────────
folded_lc = flat_lc.fold(period=0.8375,   # Period in days
                         epoch_time=2454833.0)
folded_lc.plot()

# ── BOX LEAST SQUARES (find transit periods) ─────────────────
pg = flat_lc.to_periodogram(method='bls',
                             period=np.arange(0.5, 20, 0.001))
pg.plot()

best_period = pg.period_at_max_power
print(f"Best period: {best_period:.4f}")

# ── BINNED FOLDED LIGHT CURVE ────────────────────────────────
folded_binned = folded_lc.bin(time_bin_size=0.005)
folded_binned.plot(marker='o', markersize=4)
```

---

## 7. World Coordinate System (WCS)

```python
from astropy.wcs import WCS
from astropy.io import fits

# ── BUILD WCS FROM FITS HEADER ───────────────────────────────
with fits.open('image.fits') as hdul:
    header = hdul[0].header
    data   = hdul[0].data

wcs = WCS(header)

# ── PIXEL → SKY ──────────────────────────────────────────────
sky_coord = wcs.pixel_to_world(500, 500)
print(sky_coord.ra.deg, sky_coord.dec.deg)

# Multiple pixels at once
x_pixels = [100, 500, 900]
y_pixels = [100, 500, 900]
sky_coords = wcs.pixel_to_world(x_pixels, y_pixels)

# ── SKY → PIXEL ──────────────────────────────────────────────
from astropy.coordinates import SkyCoord
import astropy.units as u

m31_center = SkyCoord(ra=10.6847*u.deg, dec=41.2691*u.deg)
x_pix, y_pix = wcs.world_to_pixel(m31_center)
print(f"M31 center at pixel ({x_pix:.1f}, {y_pix:.1f})")

# ── DISPLAY IMAGE WITH WCS AXES ──────────────────────────────
fig, ax = plt.subplots(figsize=(8, 8),
                       subplot_kw={'projection': wcs})
ax.imshow(data, cmap='inferno', norm=norm, origin='lower')

# Axis labels become RA/Dec automatically
ax.set_xlabel('Right Ascension')
ax.set_ylabel('Declination')
ax.coords.grid(color='white', alpha=0.3)
plt.show()
```

---

## 8. NumPy Operations for Astronomy

```python
import numpy as np

# ── HANDLING NaN VALUES ──────────────────────────────────────
# Always use nan-safe functions with astronomical data
np.nanmin(data)         # Minimum ignoring NaN
np.nanmax(data)         # Maximum ignoring NaN
np.nanmean(data)        # Mean ignoring NaN
np.nanmedian(data)      # Median ignoring NaN
np.nanstd(data)         # Std ignoring NaN
np.nansum(data)         # Sum ignoring NaN

# Replace NaN with a value (e.g. for display)
data_clean = np.nan_to_num(data, nan=0.0)

# Create a mask of valid pixels
valid = np.isfinite(data)           # True where not NaN or Inf

# ── SIGMA CLIPPING (remove outliers) ─────────────────────────
from astropy.stats import sigma_clipped_stats, sigma_clip

# Get robust statistics ignoring outliers
mean, median, std = sigma_clipped_stats(data, sigma=3.0)

# Mask outliers
clipped = sigma_clip(data, sigma=3.0, maxiters=5)
# clipped.mask = True where data was clipped

# ── REBINNING (downsample an image) ──────────────────────────
def rebin(arr, factor):
    """Rebin 2D array by integer factor"""
    new_shape = (arr.shape[0]//factor, arr.shape[1]//factor)
    return arr.reshape(new_shape[0], factor,
                       new_shape[1], factor).mean(axis=(1,3))

small = rebin(data, 4)              # 1000×1000 → 250×250

# ── RADIAL PROFILE ───────────────────────────────────────────
def radial_profile(data, center):
    """Compute azimuthally averaged radial profile"""
    y, x = np.indices(data.shape)
    r = np.sqrt((x - center[0])**2 + (y - center[1])**2)
    r = r.astype(int)
    tbin = np.bincount(r.ravel(), data.ravel())
    nr   = np.bincount(r.ravel())
    return tbin / nr

profile = radial_profile(data, (500, 500))

# ── GAUSSIAN FIT TO A STAR ───────────────────────────────────
from scipy.optimize import curve_fit
from scipy.ndimage import center_of_mass

def gaussian_2d(xy, amplitude, x0, y0, sigma_x, sigma_y, offset):
    x, y = xy
    return (offset + amplitude *
            np.exp(-((x-x0)**2/(2*sigma_x**2) +
                     (y-y0)**2/(2*sigma_y**2)))).ravel()

# Fit to a small star cutout
star = data[490:510, 490:510]
y, x = np.mgrid[0:star.shape[0], 0:star.shape[1]]
popt, _ = curve_fit(gaussian_2d, (x, y), star.ravel(),
                    p0=[star.max(), 10, 10, 2, 2, star.min()])
amplitude, x0, y0, sigma_x, sigma_y, offset = popt
fwhm = 2.355 * np.mean([sigma_x, sigma_y])  # FWHM in pixels
```

---

## 9. Useful Astronomical Constants

```python
from astropy import constants as const
import astropy.units as u

# ── PHYSICAL CONSTANTS ───────────────────────────────────────
const.c          # Speed of light    = 2.998 × 10⁸ m/s
const.G          # Gravitational constant
const.h          # Planck constant
const.k_B        # Boltzmann constant
const.m_e        # Electron mass
const.m_p        # Proton mass
const.M_sun      # Solar mass        = 1.989 × 10³⁰ kg
const.R_sun      # Solar radius      = 6.957 × 10⁸ m
const.L_sun      # Solar luminosity  = 3.846 × 10²⁶ W
const.M_earth    # Earth mass
const.R_earth    # Earth radius

# ── UNIT CONVERSIONS ─────────────────────────────────────────
# Distance
(1 * u.parsec).to(u.lyr)            # 1 pc = 3.2616 ly
(1 * u.kpc).to(u.lyr)               # 1 kpc = 3261.6 ly
(1 * u.Mpc).to(u.lyr)               # 1 Mpc = 3.2616 × 10⁶ ly
(1 * u.AU).to(u.lyr)                # 1 AU = 1.581 × 10⁻⁵ ly

# Time
(1 * u.yr).to(u.s)                  # 1 year in seconds
(1 * u.Gyr).to(u.yr)                # Gigayear to years

# ── MAGNITUDE SYSTEM ─────────────────────────────────────────
# Magnitude difference → flux ratio
def mag_to_flux_ratio(delta_mag):
    return 10 ** (delta_mag / 2.5)

# Flux ratio → magnitude difference
def flux_ratio_to_mag(flux_ratio):
    return 2.5 * np.log10(flux_ratio)

# Apparent magnitude → absolute magnitude
def absolute_magnitude(apparent_mag, distance_pc):
    return apparent_mag - 5 * np.log10(distance_pc / 10)

# Distance modulus
# m - M = 5 * log10(d/10pc)
distance_pc = 10 ** ((apparent_mag - abs_mag + 5) / 5)
```

---

## 10. Google Colab — Useful Commands

```python
# ── FILE MANAGEMENT ──────────────────────────────────────────

# List files in current directory
import os
os.listdir('.')

# Download a file from the internet
!wget https://example.com/file.fits

# Check file size
!ls -lh file.fits

# ── MOUNT GOOGLE DRIVE ───────────────────────────────────────
from google.colab import drive
drive.mount('/content/drive')

# Access your Drive files
# /content/drive/MyDrive/signal-and-noise/episode-01/

# ── DOWNLOAD OUTPUT TO YOUR COMPUTER ─────────────────────────
from google.colab import files
files.download('andromeda_first_light.png')

# ── CHECK AVAILABLE MEMORY ───────────────────────────────────
!cat /proc/meminfo | head -5

# ── INSTALL SPECIFIC VERSION ─────────────────────────────────
!pip install astropy==6.0.0 --quiet

# ── RESTART RUNTIME (if something breaks) ────────────────────
# Runtime menu → Restart Runtime
# Then re-run all cells from Cell 1

# ── CHECK GPU AVAILABILITY ───────────────────────────────────
import tensorflow as tf
print(tf.config.list_physical_devices('GPU'))

# Or via nvidia-smi
!nvidia-smi
```

---

## Quick Reference Card

```
TASK                              ONE-LINER
────────────────────────────────────────────────────────────────────

Open FITS file                    hdul = fits.open('f.fits')
Get image data                    data = hdul[0].data
Get header value                  val = hdul[0].header.get('KEY', default)
Coordinates from name             c = SkyCoord.from_name('M31')
Download sky data                 SkyView.get_images(position=c, survey=['DSS'], pixels=1000)
Apply contrast stretch            norm = simple_norm(data, 'sqrt', percent=99.5)
Display image                     plt.imshow(data, cmap='gray', norm=norm, origin='lower')
Save image                        plt.savefig('out.png', dpi=150, bbox_inches='tight')
Sigma clip stats                  mean, med, std = sigma_clipped_stats(data, sigma=3)
Detect stars                      DAOStarFinder(fwhm=3, threshold=5*std)(data)
Measure brightness                aperture_photometry(data, CircularAperture(pos, r=5))
Load light curve                  lk.search_lightcurve('Kepler-10').download_all().stitch()
Flatten light curve               lc.flatten(window_length=401)
Fold on period                    lc.fold(period=3.52)
Convert RA to degrees             ra_deg = ra_hours * 15
Angular separation                c1.separation(c2).arcmin
Pixel to sky coords               wcs.pixel_to_world(x, y)
Sky to pixel coords               wcs.world_to_pixel(SkyCoord(ra, dec))
```

---

## Cheatsheet for Common Objects

```python
# Objects used frequently in this channel
# Copy-paste ready

from astropy.coordinates import SkyCoord

objects = {
    'M31':  SkyCoord.from_name('M31'),    # Andromeda Galaxy
    'M42':  SkyCoord.from_name('M42'),    # Orion Nebula
    'M45':  SkyCoord.from_name('M45'),    # Pleiades
    'M51':  SkyCoord.from_name('M51'),    # Whirlpool Galaxy
    'M57':  SkyCoord.from_name('M57'),    # Ring Nebula
    'M87':  SkyCoord.from_name('M87'),    # Virgo A (black hole image)
    'NGC1': SkyCoord.from_name('NGC 1'),  # First NGC object
    'Crab': SkyCoord.from_name('Crab Nebula'),
    'SgrA': SkyCoord.from_name('Sgr A*'), # Galactic center
}

for name, coord in objects.items():
    print(f"{name:<6} RA={coord.ra.to_string(unit='hour'):<14} "
          f"Dec={coord.dec.to_string()}")
```

---

*Part of the Signal & Noise resource library.*  
*github.com/signal-and-noise*

---

> *"The universe does not come with documentation.*  
> *We write it ourselves."*
