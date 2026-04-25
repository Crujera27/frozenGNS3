## Frozen GNS3 GUI

### 1. Environment Setup

```bash
# RHEL

sudo dnf groupinstall -y "Development Tools"
sudo dnf install -y python3-devel openssl-devel libffi-devel

# or if Debian:

sudo apt update
sudo apt install -y build-essential python3-dev libssl-dev libffi-dev
```

### 2. Clone Source

```bash
cd /tmp
git clone https://github.com/GNS3/gns3-gui.git
cd gns3-gui
```

### 3. Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate
```

### 4. Install Dependencies

```bash
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt
pip install pyinstaller PyQt6 PyQt6-sip
```

### 5. Apply Linux Path Patch

Download the patch from this repo and apply it with the command below.

```bash
git apply linux_frozen.patch
```

### 6. Build Standalone Binary

```bash
pyinstaller --onedir \
    --name gns3-ui \
    --icon=resources/images/GNS3_icon.png \
    --add-data "resources:resources" \
    --exclude-module PyQt5 \
    --hidden-import=sip \
    --hidden-import=PyQt6.sip \
    --hidden-import=distutils \
    --collect-all PyQt6 \
    --collect-all jinja2 \
    --copy-metadata PyQt6 \
    gns3/main.py
```

7. AppImage Tooling

```bash
wget https://github.com/AppImage/appimagetool/releases/download/continuous/appimagetool-x86_64.AppImage
chmod +x appimagetool-x86_64.AppImage
```

8. Create AppDir

```bash
mkdir -p GNS3.AppDir/usr/bin
cp -r dist/gns3-ui/* GNS3.AppDir/usr/bin/
```

9. Copy desktop file

```bash
cp gns3-gui.desktop GNS3.AppDir/AppRun
```

10. Generate the AppImage

```bash
ARCH=x86_64 ./appimagetool-x86_64.AppImage GNS3.AppDir
```
