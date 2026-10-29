# OpenTofu-Lab für den Unterricht: Infrastructure as Code mit Plan, Apply und State

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-tofu-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-tofu-lab/actions/workflows/check.yml)

Ein fertiges **OpenTofu-Lab**, um Infrastructure as Code zu unterrichten, von der [MitsuruTech-Lernplattform](https://mitsurutech.co.uk/de/learning), auf der Schulen und Hochschulen jeder Schülerin und jedem Schüler eigene Maschinen und ein Terminal im Browser geben. Jede Person erhält eine Ubuntu-**VM** mit installiertem **OpenTofu** und drei Ordner mit Dateien im Terraform-Stil. Sie schreibt ein kleines Projekt mit **Variablen**, **Outputs**, **count** und **for_each**, liest nach einer Änderung einen **Plan**, sieht sich den **State** an und **repariert ein kaputtes Projekt**. Auf dem eigenen Computer wird nichts installiert, und OpenTofu ist kostenlos und quelloffen.

## Was die Lernenden tun

Das Lab legt drei Ordner in `~/tofu-lab` an und eine `README.md` im Home-Verzeichnis, die in sechs Teilen sagt, was zu tun ist, und keine Lösung verrät:

1. `hello`: ein erstes `tofu init`, `tofu plan` und `tofu apply` und die State-Datei, die zurückbleibt.
2. `hello`: eine Variable hinzufügen, den Output ändern und sehen, ob eine Änderung ein Update oder ein Ersetzen ist.
3. `campus`: ein Projekt von Grund auf schreiben. Drei Variablen mit Standardwert, je Servername eine `terraform_data`-Ressource mit `for_each`, je Raum eine mit `count`, eine Zusammenfassungs-Ressource, die beide liest, und zwei Outputs. `tofu fmt` darf an den Dateien nichts ändern.
4. `campus`: die Liste der Server ändern und allein anhand des Plans sagen, was zerstört, was angelegt und was unberührt bleibt.
5. `hello`: den State mit `tofu state list` lesen, ihn löschen und zusehen, wie der Plan alles neu anlegen will.
6. `fix`: ein Projekt mit drei Fehlern (ein Wert vom falschen Typ, eine Variable, die es nicht gibt, zwei Ressourcen, die ewig aufeinander warten), die sich mit `tofu validate` finden lassen. Ist es repariert, muss `tofu apply -var pods=7` sieben Pods melden.

Auch eine Basics-Lektion der Plattform, OpenTofu Basics, wird in diesem Lab durchgearbeitet.

## So wird bewertet

Das Lab hat eine **Prüfung**, ein Skript, das eine abgegebene VM bewertet, ohne dass die Lehrkraft sie lesen muss. Es läuft auf einer Kopie der gespeicherten Maschine nach einem frischen Start. Es kopiert die Ordner `campus` und `fix` der Lernenden an einen neuen Ort, sodass deren eigener State nicht zählt, und lehnt jeden Provider und Provisioner ab. Es führt `tofu validate` und `tofu fmt -check` aus. Für `campus` **wählt es dann Werte, die niemand kennt** (einen zufälligen Standortnamen, eigene Server und Räume), plant, wendet an und prüft den State: ein Server je Name mit Standort und Name als Eingabe, die Räume ab eins nummeriert, ein Output `summary`, der eine Ressource liest, statt von Hand eingetragen zu sein, und der Output `server_names`. Es nimmt einen Server heraus und fügt zwei hinzu, plant erneut und verlangt, dass nur der eine zerstört und nur die zwei angelegt werden. Es wendet an, verlangt, dass nichts mehr zu ändern bleibt, und zerstört alles wieder. Zuletzt muss `fix` validieren, sich mit `pods=7` anwenden lassen, seine drei Ressourcen behalten und der Variable folgen. Die erste ausgegebene Zeile sagt, was nicht stimmt.

Was es nicht tut: Es erreicht nie eine echte Cloud, weil aus dem Lab keine erreichbar ist, und wer auf der eigenen VM root ist, könnte OpenTofu selbst noch manipulieren. Es ist eine Prüfung für den Unterricht, kein Beweis.

## Was in der Datei steht

`tofu-lab.lab.yaml` ist eine vollständige **VM-Lab-Definition**, genau das, was die Plattform ausführt:

| Schlüssel | Was es ist |
| --- | --- |
| `cpus`, `memory`, `disk` | die Größe der VM jeder Person: 1 CPU, 1024 MB und 10 GB |
| `open` | die Ports, die im Browser geöffnet werden: keine, das Lab ist das Terminal |
| `setup` | ein Skript, das einmal als root läuft, wenn die VM jeder Person zum ersten Mal startet: Es installiert einige Werkzeuge aus dem Ubuntu-Archiv, lädt OpenTofu 1.13.2 aus dem GitHub-Release herunter, prüft es gegen eine festgelegte SHA-256-Summe und installiert es, und schreibt dann die drei Ordner, das Hilfswerkzeug `tofulab` (`sudo tofulab install` und `sudo tofulab reset`) und die README |
| `check` | das Bewertungsskript, das als root auf einer Kopie der gespeicherten Festplatte nach einem frischen Start läuft; sein Exit-Status ist das Ergebnis, `0` heißt bestanden |

## Auf der Plattform nutzen

**So geben Sie es einer Klasse als Aufgabe:**

1. Fügen Sie in Ihrem Modul eine Aufgabe hinzu und wählen Sie die Karte **Ubuntu-VM-Lab**.
2. Drücken Sie auf das OpenTofu-Beispiel oder fügen Sie den Inhalt von `tofu-lab.lab.yaml` ein. Ein Beispiel ersetzt den Text, und Rückgängig bringt Ihren zurück.
3. Veröffentlichen Sie und probieren Sie zuerst Ihre eigene Kopie. Der erste Start dauert ein bis zwei Minuten, während OpenTofu heruntergeladen wird. Jede Person erhält eine eigene VM, die zwischen den Sitzungen erhalten bleibt.
4. Weisen Sie es wie jede Aufgabe zu. Nach der Abgabe führen Sie die Prüfung in der Zeile einer Person in der Klassenliste aus: Sie startet eine Kopie von deren gespeicherter Maschine, die nicht zurückgespeichert wird, und zeigt, ob sie bestanden hat und was ausgegeben wurde.

Die [Seite zu diesem Lab](https://mitsurutech.co.uk/de/help/lab-tofu-lab) im Handbuch, die [Anleitung zu Ubuntu-VM-Labs](https://mitsurutech.co.uk/de/help/labs) und die Seite mit den [Beispiel-Labs](https://mitsurutech.co.uk/de/help/sample-labs) erklären den Rest.

Noch kein Konto für Ihre Einrichtung? [Das erste Jahr ist kostenlos](https://mitsurutech.co.uk/de/pricing) für bis zu 50 Lernende, und die [Registrierung](https://mitsurutech.co.uk/de/signup) braucht nur eine E-Mail-Adresse.

## Die Datei prüfen

Bei jedem Push prüft `.github/workflows/check.yml`, dass die Datei gültiges YAML mit den fünf genannten Schlüsseln ist und dass `setup` und `check` jeweils unter der 16-KB-Grenze der Plattform liegen. Es startet keine VM: Das geschieht auf den Maschinen der Plattform.

## Was in einem Lab nicht funktioniert

Die VM erreicht das Paketarchiv von Ubuntu, PyPI, npm und GitHub, sonst nichts. Deshalb **lässt sich kein Provider herunterladen, und es wird nie etwas Echtes angelegt**: AWS, Docker und die übrigen kommen aus einer Registry, die die VM nicht erreicht. Das Lab nutzt die eine in OpenTofu eingebaute Ressource, `terraform_data`, die nur einen Wert speichert und zurückgibt; Variablen, Outputs, `count`, `for_each`, Abhängigkeiten, Plan, Apply, Destroy und State sind dagegen echt. Die README der Lernenden zeigt den Ressourcenblock eines Providers als Text zum Lesen, und das Lab führt ihn nie aus. OpenTofu selbst wird von GitHub heruntergeladen, das die VM einer Schülerin oder eines Schülers nur mit einem kostenlosen Konto erreicht: Die VM eines Besuchers kann es nicht installieren, und die Prüfung meldet dann, dass OpenTofu fehlt.

## Über uns

Erstellt von [Mitsuru Technologies](https://mitsurutech.co.uk/de) für die [MitsuruTech-Lernplattform](https://mitsurutech.co.uk/de/learning): Python, Java, Go, Node, SQL und Linux im Browser für Schulen und Hochschulen, mit Container-Labs, VM-Labs, von GitHub bereitgestellten Web-Apps und Quizzen, die sich selbst bewerten. Code ohne Konto ausprobieren können Sie im [Playground](https://mitsurutech.co.uk/de/playground).

Weitere Labs vom selben Anbieter: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-reverse-lab](https://github.com/mitsurutech/lab-reverse-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-hdl-lab](https://github.com/mitsurutech/lab-hdl-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) und [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Lizenz

Dieses Werk steht unter der Lizenz [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Sie dürfen es für Lernen und Lehre teilen und anpassen, wenn Sie Mitsuru Technologies Ltd nennen, es nicht kommerziell nutzen und alles, was Sie daraus machen, unter derselben Lizenz weitergeben. Der vollständige Text steht in der Datei `LICENSE`.

[OpenTofu](https://opentofu.org) (MPL-2.0) wird beim Start einer VM aus dem GitHub-Release des Projekts heruntergeladen; hier wird nichts davon verteilt.
