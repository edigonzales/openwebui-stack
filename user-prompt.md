Erstelle den SOGIS-Entwicklungsbericht für den 9. bis 15. November 2026.
Das Zeitfenster reicht vom 9. November, 00:00 Uhr inklusive, bis zum
16. November, 00:00 Uhr exklusive, jeweils in Europe/Zurich.

Untersuche alle drei Repositories über GitHub MCP:
- sogis/gretljobs
- sogis/schema-jobs
- sogis/sogis-interlis-repository

Liste je Repository die Branches auf und frage ihre Commits im
Wochenfenster ab. Berücksichtige auch ältere Entwicklungsbranches und
Branches ohne Pull Request. Führe identische Commit-SHAs je Repository
zusammen. Erfasse im Wochenfenster gemergte PRs separat.

Gib am Ende je Repository an, welche Branches du geprüft hast und wo
die Abdeckung lückenhaft war. Nenne für jeden gefundenen Branch mit
eigenständigen Wochen-Commits ohne zugehörigen PR den Branchnamen,
mindestens einen Commit-Link und das Commit-Datum. Gib an, ob du
die PR-Zuordnung prüfen konntest.

Wähle eine konkrete fachliche oder technische Frage, die sich anhand
eines kleinen Patches klären lässt. Falls ein geeigneter Patch vorliegt,
rufe ihn ab und erkläre, was der Code zeigt. Prüfe zuerst die
Dateistatistik. Rufe höchstens drei Datei-Patches im gesamten Bericht
ab, jeweils eine Datei pro Abruf und höchstens 100 hinzugefügte und
entfernte Zeilen zusammen. Bei get_commit verwende stats und
full_patch mit derselben Commit-SHA, derselben page und perPage: 1.
Überspringe vollständige PR-Diffs und Patches mit unbekanntem Umfang.

Liste am Schluss je abgerufenem Patch den Commit-Link, den Dateipfad,
die Anzahl hinzugefügter und entfernter Zeilen und die beantwortete
Frage auf. Falls kein Patch die Kriterien erfüllt, nenne den Grund.
