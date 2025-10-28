#  Nextcloud Projekt in Docker
**Autor:** Andrija  
**Datum:** 2025-10-28  
**Ziel:** Aufbau, Betrieb und Wartung einer voll funktionsfähigen **Nextcloud-Installation mit Docker** (Container `nc_app` und `nc_db`)

---

##  Inhaltsverzeichnis
1. Projektziele und Übersicht  
2. Voraussetzungen  
3. Grundlagen: Docker verstehen  
4. Projektstruktur  
5. Docker-Installation  
6. Nextcloud & MariaDB mit Docker Compose  
7. Container starten und prüfen  
8. Erster Start & Anmeldung  
9. Netzwerk & Ports  
10. Daten und Backups  
11. Typische Fehler und Behebung  
12. Wartung & Updates  
13. Erweiterungen (Reverse Proxy, HTTPS, Automatisierung)  
14. Screenshots — empfohlene Dokumentation  
15. Zusammenfassung  
16. Befehlsübersicht  

---

## 1.  Projektziele und Übersicht

Ziel dieses Projekts ist es, eine **eigene Cloudplattform (Nextcloud)** mit **Docker-Containern** aufzubauen.  
Nextcloud ist eine **Open-Source-Alternative zu Google Drive oder Dropbox**, mit vollem Datenschutz, Benutzerverwaltung, Dateifreigabe und Synchronisation.

In diesem Projekt wird Nextcloud **in zwei Docker-Containern** betrieben:

| Container | Aufgabe | Technologie |
|------------|----------|-------------|
| `nc_app` | Weboberfläche, PHP-Anwendung und Apache Webserver | PHP / Apache |
| `nc_db` | Datenbank zur Speicherung von Benutzer-, Datei- und Metadaten | MariaDB (MySQL-kompatibel) |

 **Warum Docker?**
- Container sind isoliert: Änderungen an Nextcloud oder der Datenbank beeinflussen das System nicht.  
- Einfache Wartung und Updates durch `docker compose pull`  
- Schnelles Backup & Restore durch Volume-Mounts  
- Leicht portierbar zwischen Servern oder virtuellen Maschinen

 **Zielergebnis:**
Eine stabile, selbstgehostete Cloud, erreichbar über `http://xxxx:8080`, mit persistenten Daten und automatischen Neustarts.

---

## 2.  Voraussetzungen

### Technische Anforderungen
- **Betriebssystem:** Ubuntu 22.04 LTS / Debian 12  
- **Ressourcen:**  
  - Mindestens 2 GB RAM  
  - Mindestens 20 GB freier Speicherplatz  
- **Zugriff:** root oder `sudo`-Berechtigungen  
- **Netzwerk:** Statische IP-Adresse empfohlen  
- **Internetverbindung:** Für das Herunterladen der Container-Images erforderlich

### Software-Anforderungen
Installiere Docker und Docker Compose:

```bash
sudo apt update
sudo apt install -y docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker
Prüfe die Installation:
```

```bash
Code kopieren
docker --version
docker-compose --version
```
Screen!

3.  Grundlagen: Docker verstehen
Bevor wir starten, hier eine kurze Einführung:

 Was ist Docker?
Docker ist eine Plattform, die Anwendungen in Containern isoliert ausführt.
Ein Container ist ein leichtgewichtiger Prozess mit eigenem Dateisystem, der sich auf dem Host-System unabhängig verhält.

 Vorteile gegenüber herkömmlicher Installation:
Keine „Abhängigkeits-Hölle“ (keine Konflikte zwischen PHP, Apache, MySQL-Versionen)

Leicht zu sichern (einfach die Datenordner sichern)

Portable Images (z. B. für Deployment auf anderen Servern)

4.  Projektstruktur
Empfohlene Ordnerstruktur:

```bash
/srv/nextcloud/
├── docker-compose.yml     # Haupt-Compose-Datei
├── db_data/               # Datenbankdaten (persistent)
├── html/                  # Nextcloud-Dateien
├── backups/               # Backups (SQL & Nextcloud-Daten)
└── .env                   # Umgebungsvariablen (z. B. Passwörter)
```
Erstelle die Struktur:

```bash
Code kopieren
sudo mkdir -p /srv/nextcloud/{db_data,html,backups}
cd /srv/nextcloud
sudo nano .env
```
Screenshot:


5.  Docker-Installation prüfen
Teste, ob Docker korrekt läuft:

```bash
sudo docker run hello-world
```
Ausgabe:

```css
Hello from Docker!
This message shows that your installation appears to be working correctly.
Wenn diese Meldung erscheint, ist Docker einsatzbereit .
```

6.  Nextcloud & MariaDB mit Docker Compose
6.1 .env Datei erstellen
```env
Code kopieren
MYSQL_ROOT_PASSWORD=Zli12345
MYSQL_PASSWORD=nextpass
MYSQL_DATABASE=nextcloud
MYSQL_USER=ncuser
NEXTCLOUD_ADMIN_USER=admin
NEXTCLOUD_ADMIN_PASSWORD=Zli12345
NEXTCLOUD_TRUSTED_DOMAIN=192.168.1.50
```
 Hinweis:

Passe die IP-Adresse an deinen Server an.

Verwende sichere Passwörter (keine Standardwerte im Produktivsystem).

6.2 Docker-Compose-Datei (docker-compose.yml)
```yaml
version: '3.9'
services:
  db:
    image: mariadb:10.11
    container_name: nc_db
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
      - ./db_data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_USER=${MYSQL_USER}

  app:
    image: nextcloud:31-fpm
    container_name: nc_app
    restart: always
    ports:
      - "8080:80"
    links:
      - db
    volumes:
      - ./html:/var/www/html
    environment:
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_HOST=db
      - NEXTCLOUD_ADMIN_USER=${NEXTCLOUD_ADMIN_USER}
      - NEXTCLOUD_ADMIN_PASSWORD=${NEXTCLOUD_ADMIN_PASSWORD}
```
Screenshot:


7. Container starten und prüfen
Starten
```bash
cd /srv/nextcloud
docker compose up -d
Status prüfen
bash
Code kopieren
docker ps
```
Erwartete Ausgabe:

```scss
nc_app  ... Up (healthy)
nc_db   ... Up (healthy)
```
📸 Screenshot:


8.  Erster Start & Anmeldung
Rufe im Browser auf:

```cpp
http://192.168.1.50:8080
```
Beim ersten Start wird automatisch die Datenbank initialisiert und Nextcloud eingerichtet.

Melde dich an mit:

Benutzer: admin

Passwort: Zli12345

 Screenshot:


Nach der Anmeldung kannst du Benutzer anlegen, Dateien hochladen, Apps aktivieren oder Synchronisierung einrichten.

9.  Netzwerk & Ports
Port	Beschreibung
8080	Weboberfläche von Nextcloud
3306	interne Datenbankverbindung (MariaDB)

Prüfe Netzwerke:

bash
docker network ls
docker inspect bridge
10. 💾 Daten und Backups
Alle wichtigen Daten liegen außerhalb der Container, im Ordner /srv/nextcloud.

🔸 Datenpfade:
Speicherort	Beschreibung
html/	Nextcloud Web-Dateien
db_data/	MariaDB Datenbankdateien
backups/	Backups deiner Installation

 10.1 Manuelles Backup
 Nextcloud-Dateien sichern
bash
Code kopieren
cd /srv/nextcloud
tar -czf backups/html_$(date +%F).tar.gz html/
 Datenbank sichern
bash
Code kopieren
docker exec nc_db mysqldump -u root -p${MYSQL_ROOT_PASSWORD} nextcloud > backups/db_$(date +%F).sql
 Empfohlen:
Ein Cronjob für tägliche automatische Backups:

bash
Code kopieren
0 3 * * * cd /srv/nextcloud && docker exec nc_db mysqldump -u root -pZli12345 nextcloud > backups/db_$(date +\%F).sql
 10.2 Wiederherstellung (Restore)
Datenbank wiederherstellen:
bash
Code kopieren
docker exec -i nc_db mysql -u root -p${MYSQL_ROOT_PASSWORD} nextcloud < backups/db_YYYY-MM-DD.sql
Dateien wiederherstellen:
bash
Code kopieren
tar -xzf backups/html_YYYY-MM-DD.tar.gz -C /srv/nextcloud/html
 10.3 Backup-Strategie (Empfehlung)
Intervall	Inhalt	Ziel
täglich	Datenbank	schnelle Wiederherstellung
wöchentlich	HTML + Daten	vollständige Sicherung
monatlich	komplette Systemkopie	Langzeitarchiv

11.  Typische Fehler und Behebung
Problem	Ursache	Lösung
Internal Server Error	defekte config.php	Backup wiederherstellen
Datenbankverbindung fehlgeschlagen	falsche DB-Variablen	.env prüfen
Nicht vertrauenswürdige Domain	IP fehlt in Trusted Domains	occ config:system:set trusted_domains 1 --value="192.168.x.x"

12.  Wartung & Updates
Nextcloud aktualisieren
bash
Code kopieren
docker compose pull app
docker compose up -d
Logs anzeigen
bash
Code kopieren
docker logs nc_app --tail 50
docker logs nc_db --tail 50
Datenbank prüfen
bash
Code kopieren
docker exec -it nc_db mariadb -u ncuser -p nextcloud
13. 🔐 Erweiterungen
13.1 HTTPS mit Caddy (Reverse Proxy)
yaml
Code kopieren
services:
  proxy:
    image: caddy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
Caddyfile:

caddy
Code kopieren
https://cloud.deinedomain.ch {
  reverse_proxy nc_app:80
  tls internal
}
📸 Screenshot:


14. 📸 Screenshots — empfohlene Dokumentation
Schritt	Datei	Beschreibung
Docker installiert	docker_versions.png	Docker-Version prüfen
Projektstruktur	project_structure.png	Verzeichnisaufbau
Compose-Datei	docker_compose_yml.png	Konfiguration anzeigen
Containerstatus	docker_ps.png	Container läuft
Login	nextcloud_login.png	Web-Login
HTTPS aktiviert	https_ready.png	Browser-Schloss sichtbar

15. 🧾 Zusammenfassung
Dieses Projekt zeigt, wie man mit Docker eine eigene, sichere Cloud aufbaut:

✅ Containerisierte Umgebung (App & DB getrennt)
✅ Automatische Neustarts und Wartungsfreundlichkeit
✅ Persistent gespeicherte Daten
✅ Sicheres Backup- und Wiederherstellungssystem
✅ Erweiterbar mit HTTPS & Proxy

Ergebnis:

Eine stabile, selbstgehostete Cloudlösung — perfekt für Schule, Projekte oder Privatgebrauch.

16. ⚙️ Befehlsübersicht (kompakt)
bash
Code kopieren
# Projekt starten
cd /srv/nextcloud
docker compose up -d

# Status prüfen
docker ps

# Logs lesen
docker logs nc_app --tail 100

# Backup erstellen
docker exec nc_db mysqldump -u root -pZli12345 nextcloud > backups/db.sql

# Trusted Domain setzen
docker exec -u www-data nc_app php occ config:system:set trusted_domains 1 --value="192.168.1.50"

# Container neu starten
docker compose restart

# Nextcloud öffnen
http://192.168.1.50:8080
Ende der Dokumentation – Version 1.0 (Langfassung, Andrija, 2025-10-28)
