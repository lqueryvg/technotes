# Samsung ML-2010 mac m-series

To get Samsung ML 2010 to work on M5 laptop

The problem: no apple silicon drivers available for this old printer

Steps:

1. Download Apple driver pack

Download the official legacy Samsung driver collection here:
https://support.apple.com/kb/DL1887?viewlocale=en_GB&locale=en_GB

2. Download Pacifist (Installer bypass)

Download the tool to extract the blocked drivers here:
https://www.charlessoft.com/cgi-bin/pacifist_download.cgi

3. Driver installation

- Open the Samsung DMG file.
- Open Pacifist and drag the "SamsungPrinterDrivers.pkg" into the Pacifist window.
- Select the top folder ("Contents of...") and click "Install" in the top toolbar.
- If prompted to overwrite or for a password, say "Yes" or "Always."
- If macOS asks to install Rosetta, click "Install."

4. PRINTER CONFIGURATION

- Plug in the printer via USB.
- Go to System Settings > Printers & Scanners > Add Printer (+).
- Select "Samsung ML-2010" in the list.
- Under "Use," click "Select Software..."
- Search for and select: Samsung ML-2240 Series
