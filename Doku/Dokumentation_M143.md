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

##  A1 – Daten klassifizieren und sichern

### Ziel
Zu Beginn des Projekts werden die zu sichernden Daten identifiziert, klassifiziert und in eine logische Struktur gebracht, um eine sinnvolle Backup-Strategie zu entwickeln.

### Umsetzung
- Auf **VM1 (Backup-Server)** wurde ein Verzeichnis `C:\Data\` erstellt, das als **Datenquelle** für alle Tests dient.  
- Darin befinden sich verschiedene Dateiarten:
  - Dokumente (`.docx`, `.pdf`)
  - Systemlogs (`.log`)
  - Datenbank-Dumps (`.bak`)
  - Testdaten (`.txt`, `.csv`)

Diese Daten wurden in drei Kategorien eingeteilt:

| Kategorie | Beschreibung | Backup-Typ |
|------------|---------------|-------------|
| **Kritisch** | Systemkonfigurationen, Logfiles | Image-Backup (Veeam) |
| **Wichtig** | Projektdaten, Dokumente | Datei-Backup (Duplicati, verschlüsselt) |
| **Unkritisch** | temporäre Dateien | werden ausgeschlossen |

### Begründung
Durch die **Klassifizierung der Daten** wird sichergestellt, dass wichtige Informationen priorisiert und ressourcenschonend gesichert werden.  
Dadurch wird Speicherplatz optimiert und der Wiederherstellungsaufwand im Ernstfall minimiert.

### Nachweis (Screenshots)
- Ordnerstruktur `C:\Data\`
- Beispiel-Dateien mit unterschiedlichen Endungen
- Tabelle der Datenklassen

---

##  A2 – Risiken analysieren und Backupstrategie planen

### Ziel
Analyse potenzieller Risiken, die zum Datenverlust führen könnten, und Planung geeigneter Schutzmaßnahmen (Backup-Strategie).

### Risikoanalyse

| Risiko | Beschreibung | Auswirkung | Gegenmaßnahme |
|--------|---------------|-------------|----------------|
| Hardwareausfall | AWS-Volume beschädigt oder unzugänglich | Datenverlust | Image-Backup mit Veeam |
| Menschlicher Fehler | Datei gelöscht oder überschrieben | Datenverlust | Versionierung mit Duplicati |
| Malware/Ransomware | Verschlüsselung oder Zerstörung von Daten | Komplettausfall | Cloud-Backup mit AES-Verschlüsselung |
| Fehlkonfiguration | Backup unvollständig oder fehlerhaft | Teilverlust | Automatisierte Health Checks |

### Backupstrategie
- Umsetzung der **3-2-1-Regel**:
  - 3 Kopien (Original + lokal + Cloud)
  - 2 verschiedene Medien (EBS + S3)
  - 1 Offsite (Cloud)
- Kombination aus:
  - **Image-Backup (Veeam)** → für komplette Systemwiederherstellung  
  - **Datei-Backup (Duplicati)** → für versionierte Cloud-Sicherungen

### Zeitplan
| Aufgabe | Häufigkeit | Tool |
|----------|-------------|------|
| System-Backup (Veeam) | täglich 02:00 | Veeam Agent |
| Datei-Backup (Duplicati) | täglich 03:00 | Duplicati |
| Integritätsprüfung | wöchentlich | Veeam Health Check |
| Test-Wiederherstellung | monatlich | VM2 |

### Begründung
Diese Strategie erfüllt das Ziel eines **zuverlässigen, redundanten Backup-Systems** mit schnellen Wiederherstellungszeiten (RTO) und akzeptablen Datenverlustgrenzen (RPO).

### Nachweis (Screenshots)
- Planungsskizze der 3-2-1-Strategie (z. B. aus PowerPoint oder Draw.io)
- Tabelle mit Zeitplan und Tools

---

##  B1 – Backup-Lösung konzipieren und Infrastruktur aufbauen

### Ziel
Aufbau einer funktionierenden Infrastruktur in AWS, die Backups und Restores realistisch simulieren kann.

### Umsetzung
In **AWS EC2** wurden zwei virtuelle Maschinen erstellt:

| Komponente | Beschreibung |
|-------------|---------------|
| **VM1: Backup-Server** | Hauptsystem für Backup-Erstellung |
| **VM2: Recovery-Server** | Testsystem für Wiederherstellung |
| **Storage (EBS)** | 50 GB Root + 100 GB Backup-Volume |
| **S3 Bucket** | Cloud-Ziel für Duplicati-Backups |

### Netzwerkeinstellungen
- Beide Instanzen befinden sich in derselben AWS-VPC.
- **Security Group:** RDP (Port 3389) freigegeben.
- Private Kommunikation zwischen VM1 und VM2 erlaubt.

### Begründung
Durch den Aufbau von zwei separaten Systemen wird das **Disaster-Recovery-Szenario** realitätsnah simuliert.  
So kann nachgewiesen werden, dass eine vollständige Wiederherstellung möglich ist – unabhängig vom ursprünglichen Server.

### Nachweis (Screenshots)
- AWS EC2 Dashboard mit beiden Instanzen  
- EC2 Instance Details (AMI, Storage, IP)  
- S3 Bucket Übersicht  
- RDP-Verbindung zu VM1 & VM2  

---

##  C1 – Speicherlösungen dokumentieren

### Ziel
Erfassen und beschreiben der Speicherinfrastruktur, die für Backup und Restore eingesetzt wird.

### Umsetzung
| Speicherort | Typ | Zweck | Tool |
|--------------|-----|--------|------|
| **C:** | Lokaler Volume (50 GB) | Betriebssystem & Daten | – |
| **D:** | Zweites EBS Volume (100 GB) | Backup-Ziel für Veeam | Veeam Agent |
| **AWS S3 Bucket** | Cloud Storage | Offsite-Backup für Duplicati | Duplicati |
| **VM2 (Recovery)** | EC2 Volume | Restore-Ziel | Veeam / Duplicati |

### Begründung
Die Kombination aus lokalen und Cloud-Speichern gewährleistet:
- schnelle Wiederherstellung lokaler Backups,
- zusätzliche Sicherheit durch geografisch getrennte Speicherung (AWS S3).

### Nachweis (Screenshots)
- Windows Explorer: C: und D: Laufwerke  
- AWS S3 Bucket Details  
- Beleg der Verschlüsselung (AES-256)  

---
