# Meine Projektarbeit (Modul 143): Nextcloud mit Backup & Restore

## 1. Einleitung
Ich habe mich entschieden, in diesem Projekt eine **Nextcloud** aufzusetzen und dazu ein **Backup- und Restore-System** zu entwickeln.  
Die Idee ist, dass ich Daten (Dateien und Datenbank) automatisch sichere und bei Bedarf auch wiederherstellen kann.  
Ich möchte nicht nur ein einfaches Backup machen, sondern auch gleich eine **hybride Lösung** ausprobieren: also einmal lokal (NAS) und einmal in der Cloud.  

Mein Ziel ist es, am Ende ein System zu haben, das **regelmässig Backups erstellt**, diese **verschlüsselt** und wo ich zeigen kann, dass ein Restore wirklich funktioniert.  

---

## 2. Konzept (Planung)
Im Moment bin ich noch am Anfang und überlege, wie ich das genau umsetze. Mein Plan sieht so aus:

### 2.1 Was ich sichern will
- Nextcloud-Dateien (Benutzerdaten und Konfiguration)  
- Die MariaDB-Datenbank von Nextcloud  

### 2.2 Wie ich sichern will
- **Software:** ich will `restic` nutzen, weil es Verschlüsselung und Cloud-Support eingebaut hat  
- **Speicherziele:**  
  - NAS im lokalen Netzwerk (für schnelle Sicherungen)  
  - zusätzlich ein Cloud-Speicher (S3-kompatibel, z. B. Wasabi oder Backblaze)  

### 2.3 Automatisierung
Ich möchte später mit **cronjobs** arbeiten, damit Backups automatisch laufen.  
Geplant ist:  
- täglich ein Backup  
- wöchentlich aufräumen (alte Versionen löschen, Integrität prüfen)  

### 2.4 Restore-Plan
Ich will testen:  
- einzelne Dateien wiederherstellen  
- die ganze Datenbank zurückspielen  
So habe ich den Beweis, dass meine Backups nicht nur da sind, sondern auch funktionieren.  

---

## 3. Umsetzung (Schritte die ich vorhabe)
Ich habe bisher nur einen groben Ablauf. Schritt für Schritt will ich das so machen:

1. **Server vorbereiten**  
   - Ubuntu Server installieren  
   - Updates und Basis-Tools einrichten  

2. **Docker & Compose installieren**  
   - damit ich Nextcloud und MariaDB einfach als Container laufen lassen kann  

3. **Nextcloud starten**  
   - ein Docker Compose File schreiben  
   - prüfen, ob die Nextcloud im Browser läuft  

4. **NAS einbinden**  
   - mein NAS über NFS oder SMB verbinden  
   - testen, ob ich es beschreiben kann  

5. **restic einrichten**  
   - lokale Repo auf dem NAS initialisieren  
   - ein Repo in der Cloud initialisieren  

6. **Backup-Skript schreiben**  
   - Datenbank dumpen  
   - Dateien + Dump mit restic sichern  
   - Logging einbauen  

7. **Automatisieren**  
   - cronjob einrichten, damit das Backup regelmässig läuft  

8. **Restore testen**  
   - einzelne Dateien zurückholen  
   - Datenbank in eine Test-DB zurückspielen  

---

## 4. Meine Ziele
- Ich möchte lernen, wie man ein **Backup- und Restore-System von Grund auf aufbaut**  
- Ich will verstehen, wie man **lokale und Cloud-Speicher** kombiniert  
- Am Ende möchte ich zeigen, dass mein System zuverlässig funktioniert  
- Ich will eine **Dokumentation mit Screenshots** erstellen, damit jeder meine Arbeit nachvollziehen kann  
