# 2D-Optical-Flow-Clustering 
This analysis pipeline quantifies actin dynamics in developing neurons by clustering 2D optical flow vectors using local dot product similarity. It extracts features like track length, recurrence, and orientation from time-lapse fluorescence microscopy data.


### 1. Vector Fields Using Optical Flow
- Image-registration and intensity-adjustment were performed using `scikit-image` to correct for jitter and photobleaching.
- Videos were temporally smoothed using a Gaussian-like filter across five frames to reduce noise, while preserving actin wave dynamics.
- Optical flow was computed using the Lucas–Kanade method on the smoothed frames.
- A Gaussian weighting matrix (σ = 2 pixels = 0.72 µm) was used to extract local flow vectors.
- Flow vectors below a reliability threshold of 0.1 were discarded.
- Gaussian parameters and threshold choice were validated to ensure robust motion capture.

### 2. Neuronal Masking
- Mean image frames were enhanced using CLAHE for low-contrast videos.
- A white top-hat filter (disk radius = 15 pixels = 5.4 µm) was applied to enhance fine neuronal structures.
- Adaptive local thresholding (Niblack method) was used to generate initial binary masks.
- Morphological opening steps refined the masks, retaining the largest connected components.
- The cell body region was manually excluded to isolate neuronal processes.
- Neuronal orientation was estimated using a rotating Laplacian of Gaussian filter on the mean frame.

### 3. Clustering of Optical Flow Vectors
- Vectors were spatially clustered by averaging in a local Gaussian neighborhood (11×11 matrix, σ = 5 pixels = 1.8 µm).
- Dot product similarity was calculated between each vector and its neighborhood to identify locally aligned motion.
- Clusters were detected using `trackpy`, with:
  - Detection diameter ≈ 5 pixels (1.81 µm)
  - Minimum separation = 15 pixels (5.4 µm)
- Clusters were linked across frames using:
  - Search range = 5 pixels (1.8 µm)
  - Memory = 1 frame (2 s)
- Only tracks persisting for ≥8 frames (~14 s) and with speed >0.5 px/frame (5.4 µm/min) were analyzed.
- Tracked metrics included:
  - Instantaneous shifts, displacement, velocity, movement angle
  - Track length, duration, net distance, average velocity/speed, sinuosity

### 4. Growth Cone Orientation
- Growth cone boundaries were manually segmented in ImageJ.
- The orientation of the central shaft was defined by the major axis of its outline.

### 5. Anisotropy Analysis
- All trajectories were translated to a common origin (0,0).
- Principal Component Analysis (PCA) was applied to the x and y coordinates.
- Anisotropy was quantified as the ratio of the smaller to larger eigenvalue, reflecting track shape asymmetry.

### 6. Burstiness Quantification
- New track appearances per frame were counted (ongoing tracks excluded).
- Counts were summed over non-overlapping 10-frame intervals to create a coarse-grained time series.
- Burstiness was measured as the coefficient of variation (CV = standard deviation / mean).
- A high CV indicates higher temporal irregularity in track initiation.


---

## Suggested Order of Notebooks

To get started with this analysis pipeline, the following Jupyter notebooks can be explored in the order below:

1. **`Drift_Jitter_Correction_Optical_Flow_2D.ipynb`**  
   Performs drift correction and photobleaching adjustment, followed by Lucas–Kanade 2D optical flow computation on the smoothed videos.

2. **`Masking.ipynb`**  
   Demonstrates the semi-manual procedure for extracting and refining neuronal process masks from mean frames.

3. **`Nanoridge_orientation.ipynb`**  
   Extracts the alignment direction of nanoridges from microscopy images using frequency-based filters.

4. **`Nonuniform_illumination.ipynb`**  
   Shows how to handle and correct for uneven illumination across the image plane before further analysis.

5. **`Actin tracking demo_manual.ipynb`**  
   Provides a walkthrough of the actin feature tracking process and shows examples of tracked motion features.

6. **`Cluster Tracking on Glass.ipynb`** and **`Cluster Tracking_Ridges_Testing.ipynb`**  
   Contain examples of local flow vector clustering and downstream track analysis across different movie conditions (glass vs. nanoridges).

7. **`Anisotropy and Burstiness.ipynb`**  
   Explores whether actin tracks show directional bias and whether track initiation occurs in bursts.

8. **`Spatial correlation of tracks.ipynb`**  
   Analyzes spatial relationships between new and existing tracks to assess local clustering or dispersion patterns.

---



