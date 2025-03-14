# Lernjournal 2 Container

## Einleitung

Im Rahmen des Moduls "Model Deployment" (MDM) arbeite ich nun mit Docker, um Container-Konzepte wie Images, Container, Networks, Compose und Dockerfiles zu verstehen sowie Deployments mit Azure Web Apps, ACA und ACI durchzuführen. Bereits im Modul "Scientific Programming" konnte ich Erfahrungen mit Docker sammeln. Dort habe ich mit Docker Compose, Images und Containern gearbeitet und eine lokale Flask-Anwendung zusammen mit einer Datenbank in einem Docker-Container erstellt und betrieben. Diese Vorkenntnisse helfen mir nun bei der Umsetzung der aktuellen Übung.

## Docker Web-Applikation

### Verwendete Docker Images

| | Bitte ausfüllen | |
| -------- | ------- | ------- |
| Image 1 | Name/Bezeichnung | wordpress:6.7.0-php8.3
| Image 1 | URL Docker Hub | https://hub.docker.com/_/wordpress
| Image 2 | Name/Bezeichnung | mysql:9.1.0
| Image 2 | URL Docker Hub | https://hub.docker.com/_/mysql
| Docker Compose | URL Github Repo | https://github.com/caso02/MDM-Uebungen/blob/main/Woche%203/docker-compose.yml

### Dokumentation manuelles Deployment

- Schritt 1: Images ziehen
  Die benötigten Images wurden von Docker Hub geladen:

```bash
docker pull wordpress:6.7.0-php8.3
docker pull mysql:9.1.0
```

- Schritt 2: MySQL starten
  Die MySQL-Datenbank wurde mit folgendem Befehl gestartet:

```bash
docker run --name local-mysql \
  -e MYSQL_ROOT_PASSWORD=12345 \
  -e MYSQL_DATABASE=wordpress \
  -e MYSQL_USER=wordpress \
  -e MYSQL_PASSWORD=wordpress \
  -v mysql-data:/var/lib/mysql \
  -d mysql:9.1.0
```
Dies erstellt eine Datenbank namens wordpress mit einem Benutzer wordpress und speichert die Daten in einem Volume mysql-data.

- Schritt 3: WordPress starten
  WordPress wurde mit folgendem Befehl gestartet:

```bash
docker run --name local-wordpress \
  -p 8082:80 \
  -v wordpress-data:/var/www/html \
  -d wordpress:6.7.0-php8.3
```
Der Port 8082 auf meinem lokalen Rechner wurde an den internen Port 80 des Containers gebunden.

- Schritt 4: Netzwerk erstellen und verbinden
  Damit die Container kommunizieren können, wurde ein virtuelles Netzwerk erstellt und die Container verbunden:

```bash
docker network create wordpress-network
docker network connect wordpress-network local-mysql
docker network connect wordpress-network local-wordpress
```
- Schritt 5: Überprüfung
  Im Browser wurde http://localhost:8082 geöffnet, und die WordPress-Installationsseite erschien.
  Danach habe ich die Datenbankverbindung eingerichtet. Nachdem war WordPress lokal funktionsfähig.
  Ebenfalls konnte ich mit folgendem command überprüfen ob die container aktiv sind:

```bash
docker ps
```
![alt text](/lernjournal2-container/images/Datenbankverbindung.png)
![alt text](/lernjournal2-container/images/Datenbankverbindung_erfolg.png)
![alt text](/lernjournal2-container/images/WP_Dashboard.png)
![alt text](/lernjournal2-container/images/docker_desktop.png)

### Dokumentation Docker-Compose Deployment

- Schritt 1: `docker-compose.yml` erstellen
  Um das manuelle Deployment zu vereinfachen, wurde eine `docker-compose.yml` Datei erstellt:

- Schritt 2: Starten
  Die Container wurden mit folgendem Befehl gestartet:

```bash
docker-compose up -d
```

- Schritt 3: Überprüfung
  Nach dem Start war WordPress erneut unter http://localhost:8082 erreichbar. Die Datenbankverbindung wurde automatisch über die Umgebungsvariablen hergestellt, ohne manuelle Netzwerkkonfiguration.
  Ebenfalls konnte ich mit folgendem command überprüfen ob die container aktiv sind:

```bash
docker ps
```

![alt text](/lernjournal2-container/images/docker-compose.png)

## Deployment ML-App

### Variante und Repository

| Gewähltes Beispiel | Bitte ausfüllen |
| -------- | ------- |
| onnx-sentiment-analysis | Ja |
| onnx-image-classification | Nein |
| Repo URL Fork | https://github.com/caso02/onnx-sentiment-analysis |
| Docker Hub URL | https://hub.docker.com/r/caso02/onnx-sentiment-analysis |

### Dokumentation lokales Deployment

Nach dem Forken des Repositories habe ich die Anwendung lokal auf meinem Mac mit Apple Silicon gebaut und gestartet. Dabei war es notwendig, die Plattform auf `linux/amd64` zu setzen, um Kompatibilitätsprobleme zu vermeiden. Der Container lief erfolgreich, und ich konnte die Weboberfläche unter http://localhost:9000 im Browser aufrufen. Dort konnte ich Text eingeben und die Sentiment-Vorhersage testen, was einwandfrei funktionierte. Die Logs bestätigten, dass die Anwendung korrekt startete und keine Fehler auftraten.

![alt text](lokal muss noch)

### Dokumentation Deployment Azure Web App

Für das Deployment auf Azure Web App habe ich zunächst eine Ressourcengruppe erstellt und den Container aus meinem Docker Hub-Repository bereitgestellt. Bevor es jedoch funktionierte, musste ich im Azure-Portal den Ressourcenanbieter für Web Apps aktivieren, da dieser in meinem Abonnement nicht standardmässig registriert war. Nach der Aktivierung lief das Deployment durch, und die Anwendung war unter der generierten URL https://onnx-sentiment-analysis.azurewebsites.net erreichbar. Einmal dachte ich, es läge an einem Port-Problem, weil die Seite nicht sofort lud, aber es stellte sich heraus, dass ich einfach zu ungeduldig war – das Laden dauerte etwas länger als erwartet. Die Logs im Azure-Portal zeigten, dass alles korrekt lief.

![alt text](/lernjournal2-container/images/azurewebapp.png)
![alt text](/lernjournal2-container/images/dockerps_logs.png)
![alt text](/lernjournal2-container/images/dockerhub.png)

### Dokumentation Deployment ACA

Für Azure Container Apps (ACA) habe ich die Anwendung ebenfalls umgesetzt. Zuerst musste ich den Ressourcenanbieter für Container Apps im Azure-Portal registrieren, da er nicht automatisch verfügbar war. Nach der Erstellung einer Umgebung und der Bereitstellung des Containers aus meinem Docker Hub-Repository konnte ich im Azure-Portal sehen, dass die Anwendung hinterlegt wurde. Allerdings lädt die Seite im Frontend nicht, obwohl die Ressourcen korrekt angezeigt werden. Ich habe den genauen Grund dafür nicht herausgefunden, und es bleibt unklar, warum die Anwendung hier nicht wie erwartet funktioniert.

![alt text](fehlermeldung)

### Dokumentation Deployment ACI

Beim Deployment auf Azure Container Instances (ACI) lief zunächst nicht alles glatt. Nach der Erstellung der Ressourcengruppe und dem Versuch, den Container bereitzustellen, musste ich den Ressourcenanbieter für Container Instances aktivieren, was ich ebenfalls im Azure-Portal erledigte. Danach stiess ich auf Fehlermeldungen, weil ich vergessen hatte, das Betriebssystem (Linux) sowie CPU- und Speicheranforderungen anzugeben. Nachdem ich diese Parameter ergänzt hatte, klappte das Deployment, und die Anwendung war unter http://onnx-sentiment-analysis.switzerlandnorth.azurecontainer.io:5000 erreichbar. Die Logs im Azure-Portal bestätigten den erfolgreichen Start, und die Weboberfläche funktionierte wie lokal.

![alt text](/lernjournal2-container/images/microsoftcontainer.png)
![alt text](/lernjournal2-container/images/microsoftcontainer2.png)
![alt text](/lernjournal2-container/images/aci.png)

### Übersicht aller Ressourcen:
![alt text](/lernjournal2-container/images/ressourcengruppe.png)
