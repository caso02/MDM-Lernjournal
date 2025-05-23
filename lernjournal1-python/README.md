# Lernjournal 1 Python

## Repository und Library

| | Bitte ausfüllen |
| -------- | ------- |
| Repository (URL)  | https://github.com/caso02/MDM-Uebungen/tree/main/Woche%202/flask
| Kurze Beschreibung der App-Funktion | Eine Flask-Webanwendung, die ein hochgeladenes Bild in Graustufen umwandelt und das Ergebnis direkt im Browser anzeigt. |
| Verwendete Library aus PyPi (Name) | Flask, Pillow (PIL) |
| Verwendete Library aus PyPi (URL) | https://pypi.org/project/Flask/, https://pypi.org/project/Pillow/ |

## Einleitung

Bevor ich mit der Umsetzung der App begann, habe ich mich zunächst mit pip und virtuellen Umgebungen vertraut gemacht, um eine saubere Arbeitsgrundlage zu schaffen. dies ist hier zu sehen: https://github.com/caso02/MDM-Uebungen/tree/main/Woche%202

![alt text](/lernjournal1-python/images/Uebung_venv.png)
![alt text](/lernjournal1-python/images/Uebung_Flask.png)
![alt text](/lernjournal1-python/images/Pipenv.png)
![alt text](/lernjournal1-python/images/github.png)

## App, Funktionalität

Die Flask-Anwendung "Grey" ermöglicht es, ein Bild hochzuladen und dieses direkt im Browser in Graustufen anzuzeigen. Der Ablauf ist wie folgt:

1. Beim Aufruf der Seite wird `index.html` geladen, das ein Formular mit einem Datei-Upload-Feld und einem Button enthält.
2. Nach dem Klick auf den Button wird `script.js` ausgeführt, das die hochgeladene Datei per `fetch` Anfrage an den Flask-Endpunkt `/process_image` sendet.
3. Die Flask-App empfängt das Bild, wandelt es mit der PIL Image-Bibliothek (genauer `Image.convert("L")`) in Graustufen um und gibt es als PNG zurück. Die PIL Image-Bibliothek wird hier verwendet, um das Bild zu öffnen, zu bearbeiten und zu speichern.
4. Das bearbeitete Bild wird im Frontend direkt in einem `<img>` Tag angezeigt, ohne dass eine erneute Seitenaktualisierung nötig ist.

![alt text](/lernjournal1-python/images/flask_code.png)

## Dependency Management

Das am Anfang gelernte Wissen über virtuelle Umgebungen wurde angewendet, um eine isolierte Entwicklungsumgebung sicherzustellen. Dies war besonders hilfreich, da ich bereits Anaconda genutzt habe und lokal viele Pakete installiert sind, die sonst Konflikte verursachen könnten.

- Eine virtuelle Umgebung wurde mit `python -m venv .env` erstellt.
- Die Umgebung wurde mit `source .env/bin/activate` aktiviert.
- Abhängigkeiten wurden aus einer `requirements.in` Datei mit `pip install -r requirements.in` installiert.
- Die installierten Pakete wurden in eine `requirements.txt` Datei übernommen (`pip freeze > requirements.txt`).
- Für eine neue Umgebung können die Pakete mit `pip install -r requirements.txt` installiert werden.
- Ich habe vergessen die Pillow library am anfang des Projektes zu installieren, deshalb befindet sie sich nur in der requirements.txt Datei


## Deployment

Die Flask Anwendung wurde mit Microsoft Azure in der Cloud bereitgestellt, wodurch sie nicht nur lokal, sondern auch öffentlich zugänglich ist. Dafür wurde folgender Befehl verwendet:

```bash
az webapp up --name flask-grey3 --location switzerlandnorth --plan mdm --resource-group mdm --runtime PYTHON:3.12 --sku F1 --logs
```

Der App-Name lautet flask-grey3, da ich mehrere Versuche brauchte, um das Deployment zum Laufen zu bringen. Anfangs funktionierte es nicht, weil der Port nicht korrekt konfiguriert war. Erst durch Hinzufügen dieses Codes im Flask-Skript lief es auf Azure:

```python
if __name__ == '__main__':
    port = int(os.environ.get('PORT', 5000))
    app.run(host='0.0.0.0', port=port)
```

Dies stellt sicher, dass die App den von Azure vorgegebenen Port nutzt und für externe Zugriffe erreichbar ist. Nach dem Deployment konnte ich die App über den generierten Azure-Link aufrufen.

![alt text](/lernjournal1-python/images/Azure.png)
![alt text](/lernjournal1-python/images/Grey.png)

![Hier finden Sie das Video zur App](/lernjournal1-python/videos/Bildschirmaufnahme%20Grey.mov)

