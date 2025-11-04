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

![OrdnerStruktur](./Screenshots/Data.png)

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

```
C:\Data\Temp
*.tmp
*.cache
**\node_modules
C:\Users*\AppData\Local\Temp\
```

Diese Regeln werden später direkt in **Duplicati** und optional in **Veeam File-Level Restore** integriert.  
So wird vermieden, dass temporäre oder automatisch generierte Daten unnötig gesichert werden.


# 🧠 A2 – Risikoanalyse und Backup-Strategie (Advanced)

## 🎯 Ziel
In diesem Schritt wird eine vollständige **Risikoanalyse** durchgeführt und darauf aufbauend eine **Backup-Strategie** entwickelt.  
Ziel ist, die **Datenverfügbarkeit, Integrität und Vertraulichkeit** sicherzustellen – auch bei Systemausfällen oder Datenverlust.  

Im Unterschied zu A1 liegt der Fokus hier auf der **strategischen Planung und technischen Umsetzung** der 3-2-1-Backup-Regel mit einer lokalen Cloud-Lösung (**MinIO** als S3-Ersatz).

---

## ⚙️ Umsetzung

### 1️⃣ Risikoanalyse
Zur Bewertung potenzieller Bedrohungen wurde eine Risikoanalyse erstellt.  
Sie dient als Grundlage für die Auswahl der Backup-Strategie.

| Risiko | Ursache | Wahrscheinlichkeit | Auswirkung | Gegenmaßnahme |
|---------|----------|--------------------|-------------|----------------|
| Hardwareausfall | Defekte HDD/SSD | Mittel | Datenverlust | Lokales Backup (Veeam) |
| Virus / Ransomware | Schadsoftware, E-Mail | Hoch | Totalverlust | Cloud-Backup mit Duplicati |
| Benutzerfehler | Unachtsames Löschen | Mittel | Teilverlust | Versionierung aktivieren |
| Feuer / Diebstahl | Physischer Schaden | Niedrig | Komplettverlust | Offsite-Backup (Cloud) |
| Softwarefehler | Fehlkonfiguration | Mittel | Systemausfall | Image-Backup & Restore-Test |

📄 **Datei gespeichert als:**  
`C:\Data\Risikoanalyse_A2.txt`

💾 *Screenshot:*  
![Risikoanalyse](./Screenshots/Risikoanalyse.png)

---

### 2️⃣ RPO & RTO Definition

| Kennzahl | Bedeutung | Wert | Begründung |
|-----------|------------|-------|-------------|
| **RPO** (Recovery Point Objective) | Maximal tolerierter Datenverlust | 4 Stunden | Tägliche Sicherung + Versionierung |
| **RTO** (Recovery Time Objective) | Maximal zulässige Wiederherstellungszeit | 1 Stunde | Daten lokal & Cloud verfügbar |

📄 **Datei gespeichert als:**  
`C:\Data\RPO_RTO_A2.txt`

💾 *Screenshot:*  
![RPO_RTO](./Screenshots/RPO_RTO.png)

---

### 3️⃣ Umsetzung der 3-2-1-Backup-Strategie

Die 3-2-1-Regel besagt:  
- **3 Kopien** der Daten  
- **2 unterschiedliche Speichermedien**  
- **1 Kopie außerhalb des Systems (Offsite)**

| Kopie | Speicherort | Medium | Tool |
|--------|--------------|---------|------|
| 1️⃣ | `C:\Data` | Hauptspeicher | – |
| 2️⃣ | `D:\Backup` *(optional)* | Lokale HDD | Veeam |
| 3️⃣ | `MinIO – Bucket: backup-m143` | Virtuelle Cloud | Duplicati (AES-256) |

---

### 4️⃣ Einrichtung der lokalen Cloud (MinIO)

Da auf AWS keine IAM-Rollen erstellt werden konnten, wurde **MinIO** als lokaler, S3-kompatibler Server eingesetzt.  
Er läuft auf Port **9000 (API)** und **9001 (Konsole)**.

#### 💻 PowerShell-Befehl:
```powershell
cd C:\minio
.\minio.exe server C:\minio\data --console-address ":9001"
```

Nach dem Start:

```
AccessKey: minioadmin
SecretKey: minioadmin
```

🧩 **Web-Konsole:**  
[http://localhost:9001](http://localhost:9001)

📦 **Bucket erstellt:**  
`backup-m143`

💾 **Screenshot:**  
![Minio_Bucket](./Screenshots/Minio_Bucket.png)

---

## 5️⃣ Cloud-Backup mit Duplicati

### 🔧 Verbindung
Duplicati wurde auf der VM eingerichtet und über das Webinterface unter  
[http://localhost:8200](http://localhost:8200) konfiguriert.

### 📋 Backup-Job Einstellungen

| Einstellung | Wert |
|--------------|------|
| **Name** | MinIO Cloud Backup |
| **Storage Type** | S3 compatible |
| **Server URL** | `http://localhost:9000` |
| **Bucket Name** | `backup-m143` |
| **Region** | `eu-local-1` |
| **Access Key** | `minioadmin` |
| **Secret Key** | `minioadmin` |
| **Verschlüsselung** | AES-256 |
| **Zeitplan** | Täglich 22:00 Uhr |
| **Aufbewahrung** | 7D:1D, 4W:1W, 12M:1M |

💾 **Screenshots:**
![Duplicati Connection](./Screenshots/Duplicati_connection.png)
![Cuplicati Filter](./Screenshots/Duplicati_filter.png)
![Duplicati Schedule](./Screenshots/Duplicati_Schedule.png)

## 6️⃣ Ausschlussregeln

Die aus A1 bekannten Filter wurden übernommen, um temporäre oder redundante Daten auszuschließen.

```
C:\Data\Temp
*.tmp
*.cache
**\node_modules
C:\Users*\AppData\Local\Temp\
```

Diese Regeln reduzieren Speicherverbrauch und Upload-Zeit.

---

## 7️⃣ Backup-Test und Wiederherstellung

### 🧩 Testdatei erstellt:
```powershell
echo "MinIO-Testdatei" > C:\Data\Dokumente\Test_A2.txt
```

Duplicati-Backup manuell gestartet ✅

Datei gelöscht

Duplicati → Restore → Test_A2.txt wiederhergestellt

✅ Die Datei konnte erfolgreich wiederhergestellt werden.

💾 Screenshots:

![Backup Success](./Screenshots/Backup_Success.png)

🧩 Fachliche Begründung (Advanced-Niveau)
Durch den Einsatz von MinIO wurde ein Cloud-System aufgebaut, das S3-kompatibel ist.

Die Daten sind mit AES-256 verschlüsselt, womit Vertraulichkeit gewährleistet wird.

Die 3-2-1-Regel sorgt für Redundanz und hohe Verfügbarkeit.

RPO/RTO-Ziele wurden definiert und technisch umgesetzt.

Ausschlussregeln verbessern die Effizienz und senken Speicherbedarf.

Diese Kombination erfüllt die Anforderungen des Kompetenzrasters M143 (Advanced):
Planung, Umsetzung, Test und Dokumentation einer vollständigen Backup-Lösung mit nachvollziehbarer Sicherheitsstrategie.

🧾 Zusammenfassung
Teil	            Ergebnis
Risikoanalyse	    Dokumentiert & bewertet
Backup-Strategie	3-2-1-Regel umgesetzt
Cloud-Backup	    MinIO + Duplicati mit AES-256
Wiederherstellung	Erfolgreich getestet
Dokumentation	    Vollständig mit Screenshots



# 🧠 B1 – Backup implementieren und überwachen (Advanced)

## 🎯 Ziel
In diesem Schritt wird das zuvor geplante Backup-System aus A2 **implementiert**, **automatisiert** und **überwacht**.  
Dadurch wird sichergestellt, dass Backups regelmäßig ausgeführt, dokumentiert und Fehler rechtzeitig erkannt werden.  

Das Ziel ist, eine zuverlässige und nachvollziehbare Backup-Überwachung zu gewährleisten.

---

## ⚙️ Umsetzung

### 1️⃣ Automatische Backups prüfen
Es wird geprüft, ob Duplicati die Sicherungen automatisch ausführt.

1. Duplicati öffnen → [http://localhost:8200](http://localhost:8200)
2. Backup-Job **„MinIO Cloud Backup“** öffnen  
3. **Edit → Schedule (Zeitplan)**  
   - ✅ „Automatically run backups“ aktiv  
   - 🕒 Zeit: 22:00 Uhr  
4. Sicherstellen, dass kein Pause-Zeitfenster aktiviert ist  

💾 **Screenshot:**  
![Schedule_Check](./Screenshots/Duplicati_Schedule_Check.png)

---

### 2️⃣ Backup-Logs aktivieren
Damit die Sicherungen nachvollziehbar sind, werden alle Aktionen in einer Log-Datei gespeichert.

**Einstellungen in Duplicati → Settings → Advanced options:**

```
--log-file=C:\Data\Logs\Duplicati.log
--log-level=Information
```

Nach dem nächsten Backup findet man im Log:

```
[INFO] Backup completed successfully at 2025-11-04 02:24:00
```

💾 **Screenshot:**  
![Log](./Screenshots/Duplicati_log.png)

---

### 3️⃣ Fehlerüberwachung einrichten
Ziel ist, Fehler und Warnungen sichtbar zu machen – entweder per E-Mail oder lokal.

#### Variante A – E-Mail-Report
Falls Internet verfügbar ist, kann eine Benachrichtigung bei Fehlern aktiviert werden:

```
--send-mail-url=smtp://smtp.gmail.com:587
--send-mail-any-operation=true
--send-mail-to=deine-mail@gmail.com

--send-mail-from=duplicati@vm1.local

--send-mail-username=deinmailname
--send-mail-password=deinpasswort
--send-mail-subject=Duplicati Backup Report
```

💾 **Screenshot:**  
![Report](./Screenshots/Report.png)

---

5️⃣ Backup-Monitoring-Dashboard prüfen

Duplicati bietet eine grafische Übersicht aller Sicherungsläufe.

Im Dashboard sind sichtbar:
```
Letztes Backup (Datum/Uhrzeit)

Dauer

Datenmenge

Status (✔️ erfolgreich / ❌ Fehler)
```

Über „Show log“ erhält man detaillierte Informationen zu jedem Lauf.

💾 Screenshot:
![Dashboard](./Screenshots/Dashboard.png)

🧩 Fachliche Begründung (Advanced-Niveau)

Automatisierung: Die Sicherung erfolgt zeitgesteuert, ohne manuelles Eingreifen.

Nachvollziehbarkeit: Logs und Ereignisanzeige ermöglichen Fehleranalyse.

Verfügbarkeit: Restore-Tests belegen Wiederherstellbarkeit.

Monitoring: Überwachung im Dashboard stellt Funktion sicher.

Sicherheit: Verschlüsselung (AES-256) und Ausschlussregeln erhöhen Effizienz.

Diese Punkte erfüllen die Kriterien des Kompetenzrasters M143 (Advanced) –
eigenständige Planung, Umsetzung, Überwachung und Kontrolle einer Backup-Lösung.

🧾 Zusammenfassung
Teil	            Ergebnis
Zeitplanung	        Automatisches Backup aktiv
Protokollierung	    Log-Datei in C:\Data\Logs\
Überwachung	        Ereignisanzeige oder E-Mail-Report
Wiederherstellung	Erfolgreich getestet
Dashboard	        Übersichtliche Kontrolle aller Backups
Status	            Backup-Strategie vollständig implementiert

