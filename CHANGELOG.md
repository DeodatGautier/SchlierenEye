# SchlierenEye 2.2.0

Changes since **2.1.0**.

## Displacement algorithms

- **FCD (Fast Checkerboard Demodulation)** is now available for suitable periodic backgrounds with two independent carrier directions. It uses carrier selection, phase demodulation and phase unwrapping, with quality diagnostics. FCD is not intended as a general random-texture method.
- **Affine DIC** is now available. Local affine digital image correlation estimates translation and deformation using a multiscale iterative solver, photometric normalization and a regularized fit. Local affine models are blended at the output coordinates. The interface name is Affine DIC; the existing `affine_flow` configuration identifier remains compatible.
- **FFT Correlation** now uses linear zero-mean normalized correlation with an extended search region, instead of circular matching. RGB channels contribute to one normalized correlation surface. Iterative subpixel refinement, texture checks, peak ambiguity checks and validity masks improve interpretation of unreliable matches. CPU and CUDA use the same matching/refinement formulation.
- **Optical Flow** now calculates the disturbed-to-reference mapping directly at disturbed-image coordinates. The internal profile was restored and checked at native experimental resolution after a previous tuning variant generated large false matches outside small test crops.
- **Absolute Difference** remains a qualitative intensity-change diagnostic, with no displacement-vector accuracy claim. The CUDA example was used for acceleration work; MQD was not added as a separate method.

## GPU support and processing reliability

- Optional accelerated execution is available for all five methods: OpenCV OpenCL Farneback for Optical Flow; CUDA/CuPy for FFT Correlation, FCD, Affine DIC and Absolute Difference. Parts of a method may still execute on CPU.
- Unavailable or failing GPU execution falls back to CPU. Logs and numerical result metadata identify the actual backend and the fallback reason.
- The Windows installer includes the CuPy backend; a compatible graphics driver/runtime is still required. Python installations have separate optional GPU requirements.
- Video output now writes frames with the dimensions expected by the encoder, correcting the FFT video export failure.
- Streaming video processing limits the number of frames in flight to bound memory use while allowing concurrent work.

**Performance:** acceleration depends on the method, frame size, driver and hardware. Recent 384 × 384 RGB checks on a GTX 1060 favored OpenCL Optical Flow, but CUDA FFT Correlation and Affine DIC were slower than CPU. The earlier approximately 77× FFT measurement used an older circular-correlation algorithm and is not a speed claim for this release.

## Internal profiles and quantitative evaluation

- Optical Flow, FFT Correlation and Affine DIC internal defaults were reassessed with quality and spatial resolution ahead of performance. Smaller windows, a denser output grid or smoother-looking fields were not accepted as improvements by appearance alone.
- A two-round profile study performed **3,264 synthetic calculations**, including independent holdout cases, zero motion with noise, nonuniform deformations, photometric changes and spatial-frequency tests. Native-resolution images and video frames supplemented synthetic measurements.
- The balanced defaults remain Optical Flow with a 15-pixel window, FFT Correlation with a 12-pixel window / 4-pixel grid step, and Affine DIC with a 15-pixel window / 8-pixel step. No displacement postfilter was added to make fields appear smoother.
- An optional **nonperiodic-texture FFT profile** is included as `bos/bos_config_texture_detail.json`: a 10-pixel window, 4-pixel step and adjusted image prefilter. On the fresh tested nonperiodic holdout backgrounds its mean endpoint error decreased from approximately **0.157 to 0.135 pixels**. On noisy checkerboards its false-displacement RMS increased from **0.171 to 0.736 pixels**, so it was rejected as a universal default.
- The broader all-method synthetic study was rebuilt around supersampled latent backgrounds, deformation before optical blur/pixel integration, known displacement truth, multiple backgrounds/deformations/noise levels, and seed-based uncertainty estimates. It comprises **2,208 image pairs and 11,040 method evaluations**.
- Evaluation distinguishes endpoint error, spatial-frequency response, deformation-gradient error, reliable coverage, false acceptance and execution time. Rejected vectors and periodic ambiguities are reported; Absolute Difference is evaluated as an intensity diagnostic. Experimental alignment without known truth is not presented as absolute displacement accuracy.
- These are internal reproducible evaluations, not a new peer-reviewed publication or a claim of universal superiority. CPU/GPU timing and historical results retain their method/profile provenance.

## Interface and appearance

- Application naming is consistently **SchlierenEye**. The redundant title banner above the tabs was removed.
- The interface follows Windows light/dark appearance and provides hover highlighting in lists.
- All algorithm-specific parameter controls were hidden. Internal parameters remain in configuration records; general processing, compute backend, color mode and calibration remain accessible.
- The new eye icon uses the interface's graphite, neutral gray and orange palette with a clean background.
- The affine method is labeled **Affine DIC** throughout the interface.

## Packaging and compatibility

- Version **2.2.0** is shared by the application, Windows executable metadata and installer. Installer creation verifies the packaged application version and rejects stale builds.
- Standalone packaging checks dependencies used by RAW decoding and scientific NetCDF export, includes the additional FFT profile, and verifies launch without the source working directory.
- Upgrades preserve an existing `bos_config.json`; configuration backups protect edited settings during rebuilds. Input paths, calibration and user results are retained.
- Existing `affine_flow` method identifiers and legacy U/V configuration selectors remain accepted. Scientific export names remain lowercase as in 2.1.0.

## Installation

Download **SchlierenEye-Setup-2.2.0-x64.exe** from [release assets](https://github.com/DeodatGautier/SchlierenEye/releases/tag/v2.2.0) and run it on **Windows 10/11 x64**. Python is not required. Close the application before upgrading. The installer is unsigned; the matching SHA256 file verifies download integrity.

Sample images and video remain available in [release 2.1.0](https://github.com/DeodatGautier/SchlierenEye/releases/tag/v2.1.0); they are not bundled with the installer. This repository distributes application downloads and documentation; application source code is not published.
