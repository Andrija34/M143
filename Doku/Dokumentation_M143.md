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
**Ergebnis:**  
Die Daten sind nun thematisch getrennt und können individuell gesichert oder ausgeschlossen werden.

---

### 2️⃣ Klassifizierung der Daten
Die Daten wurden nach **Kritikalität und Wiederherstellungsbedarf** eingeteilt.  
Ziel ist es, Ressourcen (Speicher, Zeit, Bandbreite) effizient zu nutzen, ohne wichtige Daten zu gefährden.

| Kategorie | Beispiele | Schutzbedarf | Backup-Art | Aufbewahrung |
|------------|------------|---------------|-------------|---------------|
| **Kritisch** | `C:\Data\Konfig\appsettings.json`, Systemeinstellungen, Lizenzdateien | Hoch | **Image-Backup (Veeam)** | 14 Restore Points |
| **Wichtig** | `C:\Data\Dokumente\*`, `C:\Data\Backups\*.bak` | Mittel | **Datei-Backup (Duplicati, AES-256)** | 7d / 4w / 12m |
| **Unkritisch** | `C:\Data\Temp\*`, `*.tmp`, `*.cache` | Niedrig | Wird **nicht gesichert** | – |

---

### 🧩 Begründung der Klassifizierung
- **Kritische Daten** müssen sofort und vollständig wiederherstellbar sein (Systemintegrität).  
- **Wichtige Daten** sind inhaltlich relevant, ändern sich häufig und werden daher versioniert in die Cloud gesichert.  
- **Unkritische Dateien** verursachen nur unnötigen Speicherverbrauch und werden explizit ausgeschlossen.

---

### 3️⃣ Definition der Ausschlussregeln
Um Cloud-Speicherplatz zu sparen und die Wiederherstellung zu beschleunigen, wurden folgende **Ausschlussregeln** definiert:

C:\Data\Temp
*.tmp
*.cache
**\node_modules
C:\Users*\AppData\Local\Temp\


Diese Regeln werden später direkt in **Duplicati** und optional in **Veeam File-Level Restore** integriert.  
So wird vermieden, dass temporäre oder automatisch generierte Daten unnötig gesichert werden.
