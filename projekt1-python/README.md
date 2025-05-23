# Projekt 1 Python

## Übersicht

| | Bitte ausfüllen |
| -------- | ------- |
| Variante | Eigenes Projekt |
| Datenherkunft | HTML, Wetterdaten in tabellarischer Form |
| Datenherkunft | https://www.timeanddate.de/wetter/schweiz/zuerich/rueckblick |
| ML-Algorithmus | RandomForestRegressor |
| Repo URL | https://github.com/caso02/weather_predictor |

## Dokumentation

### Data Scraping

Zunächst habe ich versucht, CoinMarketCap als Datenquelle zu verwenden, um Kryptowährungsdaten zu scrapen. Dies funktionierte jedoch nicht, da die Webseite jegliche Scraping-Versuche blockiert hatte, vermutlich durch Anti-Bot-Massnahmen wie CAPTCHA oder IP-Sperren. Daraufhin habe ich mich für die Webseite `timeanddate` entschieden, da dort die historischen Wetterdaten von Zürich in einer gut strukturierten Tabelle vorhanden waren. Ich habe Selenium eingesetzt, um die Daten von der Webseite `timeanddate` zu extrahieren. Selenium war notwendig, da die Webseite dynamisch geladen wird und die Daten nicht direkt im HTML-Quelltext verfügbar waren. Nach dem Scraping habe ich die extrahierten Daten (z. B. Datum, Uhrzeit, Temperatur, Wetterbedingungen, Windgeschwindigkeit, Luftfeuchtigkeit, Luftdruck und Sichtweite) in einer CSV-Datei (`weather_history.csv`) gespeichert, um sie für das Training und die Weiterverarbeitung verfügbar zu machen.

### Training

Für das Training des Modells habe ich den `RandomForestRegressor` aus der scikit-learn-Bibliothek verwendet. Dieser Algorithmus wurde gewählt, da er gut für Regressionsaufgaben geeignet ist und robust gegenüber Rauschen in den Daten ist. Ich habe die Daten aus der `weather_history.csv` Datei geladen und vorverarbeitet, indem ich die numerischen Werte bereinigt (z. B. Einheiten wie "°C" entfernt) und kategorische Daten (z. B. Wetterbedingungen) kodiert habe. Die Features, die ich für das Training verwendet habe, umfassen Windgeschwindigkeit, Windrichtung, Luftfeuchtigkeit, Luftdruck, Sichtweite, Stunde des Tages, Tag des Monats und Wochentag. Das Modell wurde trainiert, um die durchschnittliche Temperatur für einen zukünftigen Tag (z. B. 21. März 2025) vorherzusagen. Das trainierte Modell (`temp_model.pkl`) und die Vorhersagen (`predictions.pkl`) wurden gespeichert, um sie später für die Bereitstellung zu verwenden.

### ModelOps Automation

Für die ModelOps-Automatisierung habe ich GitHub Actions verwendet, um den Trainings- und Vorhersageprozess zu automatisieren. Ich habe zwei Workflows erstellt: `model.yml` und `deploy.yml`. Der `model.yml` Workflow führt `train_model.py` aus, um das Modell zu trainieren und Vorhersagen zu generieren. Die resultierenden Dateien (temp_model.pkl und predictions.pkl) werden anschliessend mit dem Skript `save.py` in Azure Blob Storage hochgeladen. Der Workflow kann manuell über `workflow_dispatch` ausgelöst werden und akzeptiert einen Parameter `days_ahead`, um die Anzahl der Tage für die Vorhersage zu spezifizieren. Der `deploy.yml` Workflow wird automatisch nach Abschluss von `model.yml` ausgelöst. Er erstellt ein Docker-Image der Anwendung, pusht es zu GitHub Container Registry und stellt es auf Azure App Service bereit. Die Umgebungsvariablen `MONGODB_URI` und `AZURE_STORAGE_CONNECTION_STRING` werden in Azure App Service gesetzt, um die Verbindung zu MongoDB Atlas und Azure Blob Storage zu ermöglichen. Ursprünglich habe ich SQLite als Datenbank verwendet, bin jedoch später auf MongoDB Atlas gewechselt, um eine cloud-basierte Lösung zu nutzen, die besser für die Bereitstellung geeignet ist.

### Deployment

Für das Deployment habe ich Azure App Service verwendet, um die Flask-Anwendung bereitzustellen. Die Anwendung wurde in ein Docker-Image verpackt, das in der GitHub Container Registry gespeichert wird. Der `deploy.yml` Workflow in GitHub Actions übernimmt den Build- und Bereitstellungsprozess: Er erstellt das Docker-Image, pusht es zu GitHub Container Registry und stellt es auf Azure App Service bereit, unter Verwendung des `AZURE_WEBAPP_NAME` (mdm-wetterpredictor) und des `AZURE_WEBAPP_PUBLISH_PROFILE`, das als Secret in GitHub gespeichert ist. Die Anwendung lädt die Modelle und Vorhersagen aus Azure Blob Storage und verwendet MongoDB Atlas als Datenbank, wobei die Verbindung über die MONGODB_URI-Umgebungsvariable hergestellt wird. Nach dem Deployment ist die Anwendung unter `https://mdm-wetterpredictor.azurewebsites.net` erreichbar und zeigt die Wetterdaten sowie Vorhersagen an.
