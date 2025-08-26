# Projektarbeit Modul 143 – Backup- und Restore-System

## 1. Einleitung
In dieser Projektarbeit wird ein praxisnahes Backup- und Restore-System umgesetzt.  
Der gewählte **Use-Case** ist eine **Nextcloud-Installation** auf einem Ubuntu Server, welche als Dateiablage dient und sowohl Anwendungsdaten (Dateien) als auch eine Datenbank (MariaDB/MySQL) beinhaltet.  
Ziel ist es, ein vollständiges Konzept zu entwickeln, die Backup- und Restore-Prozesse technisch zu realisieren und die Ergebnisse nachvollziehbar zu dokumentieren.  

Das Projekt orientiert sich an der Kompetenzmatrix Modul 143 und zielt auf die Stufen **2–3 (Fortgeschritten bis Erweitert)** ab.  
Dies bedeutet, dass nicht nur ein Basis-Backup erstellt wird, sondern eine hybride Lösung (lokal + Cloud) mit Automatisierung, Logging und Wiederherstellungstests umgesetzt wird.  

---

## 2. Konzept
Im Konzept werden die Anforderungen, Strategien und technischen Entscheidungen beschrieben.  

### 2.1 Anforderungen
- Sicherung der Nextcloud-Dateien und der Datenbank  
- Kombination von **lokalen Backups** (NAS) und **Cloud-Speicher** (z. B. S3-kompatibel)  
- Automatisierung und regelmäßige Durchführung der Backups  
- Sicherstellung von Wiederherstellbarkeit (Restore-Tests)  
- Verschlüsselung der Daten bei der Übertragung in die Cloud  

### 2.2 Strategie
- **Backup-Engine:** Restic (verschlüsselt, inkrementell, Cloud-ready)  
- **Backup-Arten:**  
  - Tägliches inkrementelles Backup  
  - Wöchentliches Vollbackup  
- **Speicherorte:**  
  - Lokales NAS (Primärbackup)  
  - Cloud-Speicher (Sekundärbackup, Offsite-Schutz)  
- **Restore-Szenarien:**  
  - Wiederherstellung einzelner Dateien  
  - Vollständiger Restore von Nextcloud inkl. Datenbank  

### 2.3 Machbarkeit
- Speicherbedarf wird anhand der aktuellen Nextcloud-Daten und des erwarteten Wachstums berechnet  
- Technische Umsetzung ist mit Open-Source-Tools realisierbar  
- Kostenanalyse: lokale HDD/NAS vs. günstiger Cloud-Storage (z. B. Wasabi, Backblaze)  

---

## 3. Umsetzung (Plan)
Die Umsetzung erfolgt schrittweise:  

1. **Setup**  
   - Installation von Nextcloud auf Ubuntu Server  
   - Einrichtung von MariaDB/MySQL und Konfiguration der Datenablage  

2. **Backup-Prozesse**  
   - Installation von Restic  
   - Einrichten von Repositories (NAS + Cloud)  
   - Skripte für Datei- und DB-Backups (inkl. `mysqldump`)  
   - Automatisierung via Cronjobs  

3. **Monitoring & Logging**  
   - Protokollierung aller Backup-Läufe  
   - Mail-Benachrichtigung bei Fehlern  

4. **Restore-Tests**  
   - Wiederherstellung einzelner Dateien  
   - Komplett-Restore inkl. Datenbank  
   - Dokumentation mit Screenshots  

---

## 4. Ziele
- Entwicklung eines **verlässlichen Backup- und Restore-Systems** für eine Nextcloud-Umgebung  
- Nachweis der Funktionsfähigkeit durch **erfolgreiche Restore-Tests**  
- Umsetzung einer **hybriden Strategie** (lokal + Cloud) mit Verschlüsselung  
- Erstellung einer **verständlichen Dokumentation** (Prozess, Skripte, Screenshots)  
- Reflexion der Ergebnisse und Erarbeitung möglicher Verbesserungen (z. B. Skalierung, zusätzliche Sicherheitsmaßnahmen)  

*wurde mit hilfe von ChatGPT erstellt*
---
