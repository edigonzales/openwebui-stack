# Open WebUI lokal

Open WebUI läuft auf dem Mac unter `http://localhost:3000`. DeepSeek und Infomaniak übernehmen die Modellinferenz über ihre APIs.

## Start und Ersteinrichtung

Docker starten und im Repository einmalig eine lokale `.env` erzeugen. Den Schlüssel für spätere Starts und Container-Neuerstellungen beibehalten.

```sh
(umask 077; set -C; printf 'WEBUI_SECRET_KEY=%s\n' "$(openssl rand -hex 32)" > .env)
docker compose config --quiet
docker compose up -d
```

Sobald `curl --fail http://localhost:3000/health` erfolgreich ist, [Open WebUI](http://localhost:3000) öffnen und das erste Konto anlegen. Es erhält Admin-Rechte und Open WebUI schliesst die Registrierung automatisch. Unter **Admin Panel → Settings → Authentication** prüfen, dass **New Sign Ups** deaktiviert ist. In einem privaten Browserfenster prüfen, dass Open WebUI die Anmeldung verlangt und keine Registrierung anbietet.

## DeepSeek und Infomaniak verbinden

Unter **Admin Panel → Settings → Connections → OpenAI API** eine eventuell vorhandene OpenAI-Standardverbindung entfernen und für jeden Anbieter eine serverseitige OpenAI-kompatible Verbindung hinzufügen.

| Anbieter | Basis-URL | Prefix ID |
| --- | --- | --- |
| DeepSeek direkt | `https://api.deepseek.com` | `deepseek` |
| Infomaniak | `https://api.infomaniak.com/2/ai/<produkt-id>/openai/v1` | `infomaniak` |

Jeweils den API-Schlüssel im Verbindungsdialog eintragen. Für Infomaniak die ID des Produkts **AI Services** einsetzen. Die Verbindungen speichern und im Chat pro Anbieter ein Modell aus der Liste auswählen. Mit einer kurzen Anfrage prüfen, dass beide antworten. Bei fehlenden Einträgen im Verbindungsdialog URL und Schlüssel prüfen und bei Bedarf eine vom Anbieter unterstützte Modell-ID unter **Model IDs** eintragen.

Details zu den Schnittstellen stehen in der [Open WebUI-Anbieteranbindung](https://docs.openwebui.com/getting-started/quick-start/connect-a-provider/starting-with-openai-compatible/), der [DeepSeek API](https://api-docs.deepseek.com/) und der [Infomaniak API](https://developer.infomaniak.com/docs/api/get/2/ai/%7Bproduct_id%7D/openai/v1/models).

## Betrieb

- Logs ansehen mit `docker compose logs -f open-webui`.
- Stoppen mit `docker compose down` und wieder starten mit `docker compose up -d`.
- Mit `docker compose up -d --force-recreate` den Container neu erstellen. Anschliessend Anmeldung, Verbindungen und Testchats prüfen. Diese Daten liegen im Docker-Volume `open-webui-data`. `docker compose down -v` löscht dieses Volume samt Daten.

## SOGIS GitHub-Wochenbericht

Die Open-WebUI-Automation erstellt montags um 08:00 Uhr (Schweizer Zeit) einen Chat mit den Issue- und PR-Aktivitäten der letzten sieben Tage. Der Filter in [github_weekly.py](github_weekly.py) sendet lediglich Repo-Namen, Titel, Status, Links und den Berichtszeitraum an Qwen bei Infomaniak.

Die lokale `.env` enthält `GITHUB_WEEKLY_REPOS` als kommagetrennte Liste im Format `sogis/repo-a,sogis/repo-b`. Neue Repos dort ergänzen. Für private Repos `GITHUB_WEEKLY_TOKEN` mit einem Fine-grained Personal Access Token für die ausgewählten Repos setzen. Der GitHub-Endpunkt benötigt **Issues: Read-only**. Nach einer Änderung `docker compose up -d` ausführen. [GitHub-API für Repository-Issues](https://docs.github.com/en/rest/issues/issues#list-repository-issues)

Bei einer frischen Installation im Open WebUI Admin-Bereich unter **Functions** einen aktiven globalen Filter namens **SOGIS GitHub-Wochenbericht** mit diesem Code anlegen:

```python
from runpy import run_path

Filter = run_path("/app/github_weekly.py")["Filter"]
```

Unter **Automations** eine wöchentliche Automation mit dem Modell `infomaniak.Qwen/Qwen3.8-27B-FP8` und der Anweisung `Erstelle den SOGIS GitHub-Wochenbericht.` speichern. Der Filter greift nur für diesen exakten Text in einem Automationslauf eines Admins mit diesem Modell. **Run Now** erstellt einen Testchat. Den Mac und Docker zum geplanten Zeitpunkt laufen lassen. [Open WebUI Automations](https://docs.openwebui.com/features/chat-conversations/chat-features/automations/)

Open WebUI verwendet für die Berechnung die vom Browser gemeldete Profilzeitzone. In der lokalen Instanz meldet LibreWolf derzeit `Atlantic/Reykjavik`. Der gespeicherte Zeitplan enthält deshalb bis zum 10. November 2031 die umgerechneten Montagstermine für 08:00 Uhr in `Europe/Zurich`, einschliesslich der Sommerzeit. Wenn Open WebUI eine andere Profilzeitzone übernimmt oder dieser Zeitraum endet, den Zeitplan neu berechnen.

## Spätere Etappen

Der PPTX-Pilot folgt mit einer konkreten Vorlage. Nach dem lokalen Test ist der Umzug auf eine ovscloud-VPS mit Caddy und HTTPS vorgesehen, voraussichtlich unter `ai.interlis.guru`.
