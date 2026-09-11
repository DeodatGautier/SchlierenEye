# SchlierenEye

**Desktop software for Background-Oriented Schlieren (BOS) analysis.**

Compare reference and disturbed images, process video sequences, and explore how a BOS signal evolves along a selected image line. SchlierenEye provides a graphical interface for displacement estimation, visualization, and scientific data export.

This repository provides application downloads and documentation. Application source code is not published.

[**Download the latest Windows release**](https://github.com/DeodatGautier/SchlierenEye/releases/latest) · [Report a problem](https://github.com/DeodatGautier/SchlierenEye/issues)

## Download and install

1. Open the [latest release](https://github.com/DeodatGautier/SchlierenEye/releases/latest) and expand **Assets**.
2. Download `SchlierenEye-Setup-<version>-x64.exe` — for example, `SchlierenEye-Setup-2.1.0-x64.exe`.
3. Run the installer, then launch **SchlierenEye** from the Start menu or the optional desktop shortcut.

The Windows distribution supports **Windows 10/11, x64**. Python is not required. Installation is per user and does not require administrator privileges. The installer offers English and Russian; the application interface uses English labels.

Download the installer from **Assets**. GitHub's automatically generated “Source code” archives are not application installers.

## Changes in 2.1.0

- **FFT Color mode:** FFT correlation now honors **Grayscale** and **Multichannel RGB**. Standard image-file channel order is also handled consistently before displacement calculation.
- **Whole video and Stacking:** Whole analyzes the complete recording in ordinary video and Stacking modes, including consecutive pairs. Unused frame ranges no longer cause a range-validation error.
- **Lowercase notation:** interface labels and diagram titles use **x-t**, **y-t**, **u**, and **v**. CSV component metadata and NetCDF component variable names are lowercase.
- **Compatibility:** existing settings using `U` or `V` remain accepted. New NetCDF files use `u`, `v`, `u_meters`, and `v_meters`; update analysis scripts that previously accessed uppercase variable names. Existing exported files are unchanged.

## Features

- **Image pairs:** compare a reference photograph with a disturbed photograph.
- **Video analysis:** process a selected frame range or an entire recording against a fixed or median reference, or compare consecutive frames.
- **Displacement estimation:** dense Farnebäck optical flow and subpixel FFT correlation.
- **Intensity comparison:** absolute-difference visualization for qualitative analysis.
- **Field visualization:** magnitude and u/v component maps, plus curved, color-coded arrows over a grayscale photograph.
- **x-t and y-t diagrams:** track magnitude or a displacement component along a horizontal or vertical image line.
- **Scientific export:** optional NetCDF for image pairs and CSV for space–time diagrams.
- **Image and video output:** PNG maps and MP4 result videos, with selectable colormaps and percentile-based normalization.
- **Input support:** common image and video formats, including supported camera RAW files such as CR3, CR2, NEF, and ARW. Decoding depends on the camera format or video codec.

## Quick start

### Analyze an image pair

1. In **Processing**, select **Image** and choose **Optical Flow** or **FFT Correlation**.
2. In **Input**, select the **Reference** and **Disturbed** images. Both must have matching dimensions.
3. In **Processing**, set **Pixel size** for your camera in micrometres and choose **Color mode**. For both **Optical Flow** and **FFT Correlation**, **Grayscale** estimates displacement from luminance; **Multichannel RGB** estimates displacement separately in each RGB channel and averages the resulting components.
4. In **Output**, choose a result directory. Enable **Save NetCDF for image pair** if you need numerical data, and **Save displacement components (u, v)** for component maps and the vector overlay.
5. Click **Start Processing** and inspect the saved results.

Use a separate output directory for each experiment or processing variant: repeated runs use the same filenames and can overwrite earlier results.

### Process a video

Select **Video** in **Processing**, then load the recording in **Input**. Choose **Range** or **Whole**. For a common reference, select **Single frame**, **Median (whole)**, or **Median (range)**. Enable **Consecutive** to compare neighboring frames instead.

Frame numbering starts at **0**. In a range, **From** is included and **To** is excluded: `From 0 / To 300` processes frames 0–299.

Enable **Global normalization for video** to use common display limits across the processed recording. Choose an output directory and click **Start Processing**. The resulting MP4 displays the calculated field as a color map.

### Build an x-t or y-t diagram

With a video loaded, select **Range** and set the required frame interval, or select **Whole** to analyze the entire recording. Then open **Stacking**. Choose **x-t** for a horizontal line or **y-t** for a vertical line, set **Line index**, and select **Magnitude**, **u**, or **v**. Enable **Export scientific data (.csv)** to retain numerical values, then click **Generate Diagram**.

**Whole** now uses the entire recording in Stacking as well as ordinary video processing, including **Consecutive** mode. The inactive **Range** values do not restrict Whole processing. At least two selected frames are required for Stacking. With Consecutive enabled, each diagram row corresponds to the later frame of a pair, so N selected frames produce N−1 rows. A previously processed MP4 is not needed.

## Output files

Default filenames are listed below. Optional files are created only when the corresponding export is enabled and the selected algorithm supports it.

| File | Contents |
| --- | --- |
| `bos_result.png` | Displacement magnitude map, or intensity difference for Absolute Difference |
| `bos_result_u.png`, `bos_result_v.png` | Horizontal and vertical displacement maps |
| `bos_result_vector_field.png` | Curved displacement arrows over the disturbed photograph |
| `bos_analysis_results.nc` | Numerical image-pair fields, coordinates, and processing metadata |
| `bos_results.mp4` | Processed video |
| `diagram.png` | x-t or y-t diagram |
| `diagram_data.csv` | Diagram values, frame indices, times, spatial coordinates, and metadata |
| `image_result_config.json`, `video_result_config.json`, `diagram_config.json` | Processing configuration records |

For diagrams, also retain the CSV or record the selected line, orientation, and field separately. `diagram_config.json` alone does not fully capture those interface selections. Video processing does not export a NetCDF file for every frame.

## Understanding the results

- **Coordinates and sign:** the image origin is at the top left; x points right and y points down. u and v are correction displacements from the disturbed image toward the reference. A feature shifted right in the disturbed image therefore produces a negative u correction.
- **Units:** NetCDF stores `u`, `v`, and `magnitude` in pixels, along with their sensor-plane equivalents in metres. The metric variables are `u_meters`, `v_meters`, and `magnitude_meters`; spatial coordinates are `x` and `y` and use the configured pixel size. Object-plane distances require a separate calibration or optical magnification factor.
- **Scientific values versus display colors:** PNG and MP4 outputs are normalized visualizations. Use NetCDF or CSV for measurements. Diagram colors are normalized to 0–1; exported displacement values remain in pixels. Common video normalization applies within a run, not automatically across separate recordings.
- **Vector arrows:** curved arrows visualize the local displacement field over a grayscale background. Arrow length and color use the 99.9th percentile of nonzero finite magnitudes at displayed arrow sites as their upper limit; larger values are clipped for display. The underlying numerical fields are not clipped by this rendering step. Arrows are not particle trajectories, and their display scale is calculated separately for each figure. The legend contains only the magnitude color scale in pixels. Separate stroke fields, streamlines and SVG files are not generated.
- **Absolute Difference:** this mode measures normalized intensity change, not displacement. It does not provide u/v fields. In Stacking, use **Magnitude** to display its intensity-difference result.

SchlierenEye does not automatically reconstruct temperature, density, refractive index, or fluid velocity from a BOS displacement field.

## Updates and settings

Close SchlierenEye and run the newer installer to update the existing installation. The installer preserves an existing `bos_config.json`; that file and user-generated results also remain after uninstall.

The current graphical interface starts with built-in defaults and empty input paths. Preserving the configuration file does not restore the previous GUI session. Keep exported configuration records together with your source data and results for reproducibility.

## Verify a download

The installer is **not digitally signed**. Download the matching `.sha256` file from the release assets to verify the installer checksum.

To calculate the checksum in PowerShell, run this command in the download directory, replacing the filename with the version you downloaded:

```powershell
Get-FileHash -Algorithm SHA256 .\SchlierenEye-Setup-2.1.0-x64.exe
```

Compare the `Hash` value with the matching `.sha256` file. A matching checksum confirms that the file matches the published checksum; it is not a digital signature.

## Reporting problems

Please [open an issue](https://github.com/DeodatGautier/SchlierenEye/issues) and include:

- SchlierenEye version and Windows version.
- Processing mode and algorithm.
- Input format, image or video dimensions, and frame range where relevant.
- Steps to reproduce the problem, expected behavior, and the actual result or error message.
- A screenshot, exported configuration, or small sample that reproduces the issue, if possible.

Before attaching logs or configuration files, remove personal paths and sensitive information. Share only input data you have permission to publish.
