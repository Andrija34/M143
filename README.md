# 💾 Automatisiertes Backup- & Restore-System mit Duplicati & MinIO

## 🧩 Projektübersicht
Dieses Projekt implementiert ein vollständig **automatisiertes Backup- und Restore-System** auf Basis von **PowerShell**, **Duplicati CLI** und **MinIO (S3-kompatibel)**.  
Es umfasst die **Planung, Automatisierung, Validierung, Dokumentation und Reflexion** nach dem Kompetenzraster der Module D1–E2.

---

## 🚀 Funktionen

- 🔁 **Automatisiertes Backup** via Task Scheduler & PowerShell  
- 🔒 **AES-256-Verschlüsselung** für maximale Datensicherheit  
- ☁️ **S3-kompatibles Zielsystem** (MinIO)  
- 🧠 **Fehlerprüfung & Log-Analyse** mit automatischer Warnmeldung  
- 📦 **Automatisierte Wiederherstellung (Restore)**  
- 📧 **E-Mail-Benachrichtigung bei Fehlern**  
- 🧾 **Dokumentation & Reflexion** aller Prozesse

---

## ⚙️ Systemarchitektur

```
[Task Scheduler]
       ↓
[RunBackup.ps1] → [Duplicati CLI → MinIO Backup]
       ↓
[CheckBackup.ps1] → prüft Logs auf Fehler
       ↓
[RestoreBackup.ps1] → testet Wiederherstellung
```

---

## 🧩 Verwendete Technologien

| Komponente | Beschreibung |
|-------------|---------------|
| **PowerShell** | Automatisierung der Backup-, Prüf- und Restore-Prozesse |
| **Duplicati CLI** | Kommandozeilen-Tool für verschlüsselte S3-Backups |
| **MinIO** | Lokaler S3-kompatibler Cloud-Speicher |
| **Task Scheduler** | Zeitgesteuerte Backup-Ausführung |
| **Windows Logs** | Speicherung der Backup- und Prüfprotokolle |

---

## 🧰 Skriptübersicht

| Skript | Beschreibung |
|---------|---------------|
| `RunBackup.ps1` | Startet das Backup mit Duplicati |
| `CheckBackup.ps1` | Prüft Logdateien auf Fehler |
| `RestoreBackup.ps1` | Führt eine automatische Wiederherstellung durch |
| `VerifyRestore.ps1` | Vergleicht Hashwerte zur Integritätsprüfung |

Alle Skripte liegen unter:  
`C:\Scripts\`  
Logdateien befinden sich unter:  
`C:\Data\Logs\`

---

## 🕓 Task Scheduler Konfiguration

| Einstellung | Wert |
|--------------|------|
| **Name** | Duplicati_AutoBackup |
| **Trigger** | Täglich um 22:00 Uhr |
| **Aktion** | `powershell.exe -File "C:\Scripts\RunBackup.ps1"` |
| **Bedingung** | Nur bei aktiver Netzwerkverbindung |

---

## 🧪 Validierung

- **Logprüfung:** Automatische Fehlermeldung per `msg *` oder E-Mail  
- **Integritätsprüfung:** Hashvergleich von Original- und Restore-Dateien  
- **Wiederherstellung:** Testweise Rücksicherung über Duplicati CLI

---

## 🧠 Fachliche Begründung

- Vollständige **Automatisierung** mit Fehlerprüfung und Protokollierung  
- Nutzung eines **S3-kompatiblen Cloud-Ziels** (MinIO)  
- **Wiederholbarkeit & Nachvollziehbarkeit** durch Dokumentation und Logs  
- Umsetzung des **3-2-1-Backup-Prinzips**  
- Erfüllt alle **Advanced-Kriterien** des Kompetenzrasters D1–E2  

---

## 🧾 Bewertung & Reflexion

| Kriterium | Bewertung | Beschreibung |
|------------|------------|--------------|
| Planung (A1) | ✅ | Strukturierte Planung und Datenklassifikation |
| Cloud Backup (A2) | ✅ | MinIO erfolgreich integriert |
| Validierung (B1) | ✅ | Integrität mit Hashvergleich bestätigt |
| Optimierung (C1) | ✅ | Kompression & AES-Verschlüsselung |
| Automatisierung (D1) | ✅ | Vollständige Automatisierung |
| Überprüfung (D2) | ✅ | Automatische Log- und Fehlerprüfung |
| Dokumentation (D3) | ✅ | Vollständig nachvollziehbar |
| Prozessübersicht (E1) | ✅ | Alle Schritte aufgelistet |
| Reflexion (E2) | ✅ | Kritische Bewertung & Verbesserungsvorschläge |

---

## 🏁 Fazit

✅ **Projektziel erreicht:**  
Ein sicheres, automatisiertes und dokumentiertes Backup-System auf Cloud-Basis, das fehlerresistent, nachvollziehbar und erweiterbar ist.

---

## 💡 Erweiterungspotenzial

- 🔔 **Benachrichtigungen über Telegram oder Teams**
- 📊 **Integration von Monitoring (Grafana / Prometheus)**
- ☁️ **Vollständige Cloud-Replikation auf AWS S3 oder Azure Blob**
- 🔐 **Zwei-Faktor-Authentifizierung für Backups**

---

## 👤 Autor

**Name:** *Andrija Milosevic*  
**Projekt:** Backup & Restore Automatisierung

```Die Struktur wurde mit Hilfe von Chat-GPT erstellt```