# Installer Proxmox

Proxmox er en Linux distribution beregnet til at hoste virtuelle maskiner.
Du kan tænke på det som et open source alternativ til VMware ESX.

Denne guide gennemgår hvordan man installere Proxmox på en Minisforum computer.

## Forbered USB-drev

1. Download Etcher fra <https://etcher.balena.io/#download-etcher>
2. Download Proxmox VE fra <https://www.proxmox.com/en/downloads>
3. Åben balenaEtcher
4. Klik "Flash from file"
5. Vælg "proxmox-ve_9.2-1.iso" fra Download mappen
6. Klik "Select target"
7. Vælg "USB SanDisk 3.2Gen1 USB Device"
8. Klik "Flash!" og vent
    - Kan ske du bliver spurgt om Etcher må lave ændringer af enheden, klik "Yes"
9. Slet balenaEtcher og proxmox fra Download mappen

## Installer Proxmox

1. Tilslut strøm, skærm og keyboard til Minisforum computeren
2. Indsæt USB-drev
3. Tænd og tryk F7 indtil at der står "Please select boot device"
4. Vælg "UEFI: USB ..."
5. Installer Proxmox ([video gennemgang](https://easv.cloud.panopto.eu/Panopto/Pages/Viewer.aspx?id=1cb5403f-984d-4ce7-8a36-b4db006e9ba0))

> [!IMPORTANT]
> Spørg underviser hvilken IP adresse du skal bruge under netværk
> konfigurationen.
