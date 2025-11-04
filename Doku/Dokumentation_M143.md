#  M143 Projekt: Backup- und Restore-System mit AWS

##  Projekttitel
**Hybrid Cloud Backup & Restore auf AWS**

##  Projektbeschreibung
In diesem Projekt wird ein professionelles Backup- und Restore-System auf Basis von **zwei virtuellen Maschinen (VMs)** innerhalb der **AWS Cloud** implementiert.  
Das Ziel ist es, ein **zuverlässiges, automatisiertes und verschlüsseltes Backup-System** zu erstellen, das sowohl **lokale** als auch **Cloud-basierte Sicherungen** (Hybrid-Lösung) abbildet.  
Durch die Nutzung von **Veeam Agent for Windows** und **Duplicati** wird das System so konfiguriert, dass es:
- vollständige System-Images erstellt,  
- verschlüsselte Datei-Backups in AWS S3 hochlädt,  
- und eine Wiederherstellung auf einer zweiten VM ermöglicht.  

Diese Umgebung dient als Nachweis für alle Kompetenzen (A1–F1) des Moduls M143 *“Daten sichern und wiederherstellen”*.

---

# 🧠 A1 – Daten klassifizieren und sichern (Advanced)

## 🎯 Ziel
In diesem Schritt werden alle relevanten Daten auf der Backup-VM (**VM1**) systematisch **identifiziert, klassifiziert und strukturiert**, um gezielt zu entscheiden, **welche Daten gesichert**, **wie oft** sie gesichert werden und **welche ausgeschlossen** werden sollen.  
Dies bildet die Grundlage für eine effiziente und nachvollziehbare Backup-Strategie im gesamten Projekt.

---

## ⚙️ Umsetzung

### 1️⃣ Aufbau der Datenstruktur
Auf der Haupt-VM (`VM1`) wurde ein zentraler Datenordner erstellt, der alle projekt- und systemspezifischen Dateien enthält.  
Die Struktur wurde logisch in **Dokumente**, **Logs**, **Backups**, **Konfigurationen** und **temporäre Dateien** gegliedert.

#### 💻 PowerShell-Befehl zur Erstellung
```powershell
New-Item -ItemType Directory -Force C:\Data\Dokumente | Out-Null
New-Item -ItemType Directory -Force C:\Data\Logs | Out-Null
New-Item -ItemType Directory -Force C:\Data\Backups | Out-Null
New-Item -ItemType Directory -Force C:\Data\Konfig | Out-Null
New-Item -ItemType Directory -Force C:\Data\Temp | Out-Null

Set-Content C:\Data\Dokumente\Bericht.docx "Backup-Projektbericht"
Set-Content C:\Data\Logs\System.log "Logeintrag $(Get-Date)"
Set-Content C:\Data\Backups\DatabaseDump.bak "SQL Backup Platzhalter"
Set-Content C:\Data\Konfig\appsettings.json "{ `"env`": `"prod`" }"
Set-Content C:\Data\Temp\cache.tmp "temp file"
```
