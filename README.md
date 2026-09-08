# SchlierenEye

Desktop application for Background-Oriented Schlieren (BOS) analysis. This repository hosts downloads and documentation; application source code is not published.

## Download

[**Download the latest Windows release**](https://github.com/DeodatGautier/SchlierenEye/releases/latest)

Open the release Assets and download `SchlierenEye-Setup-2.0.0-x64.exe`. Run the installer and launch SchlierenEye from the Start menu.

- Windows 10/11, x64.
- Python is not required.
- Installation for the current user without administrator privileges.
- English and Russian installer interface; optional desktop shortcut.

## Features

- Image-pair and video BOS analysis.
- Optical-flow and FFT-correlation displacement estimation.
- Curved, color-coded displacement vectors over grayscale images.
- X-T and Y-T diagrams.
- RAW image support and optional NetCDF export.

## Updates and settings

Install a newer release over the existing installation. Existing settings are preserved. Settings and generated results remain after uninstall.

## Verification and installation

Download the installer from the **Releases → Assets** section. Python does not need to be installed. The installation is performed for the current user without administrator privileges.

A `.sha256` file is published next to the installer to verify the checksum. The installer is not digitally signed.

## Reporting problems

Use [Issues](https://github.com/DeodatGautier/SchlierenEye/issues) to describe the problem, Windows version, application version and steps to reproduce it. Remove personal paths and sensitive information from logs before attaching them.
