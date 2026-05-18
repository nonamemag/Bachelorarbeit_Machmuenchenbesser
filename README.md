# Bachelorarbeit_Machmuenchenbesser
In diesem Repository findet sich der Code zur Bachelorarbeit mit dem Titel: "KI-gestützte Analyse von Bürgermeldungen als
Entscheidungsunterstützung in kommunalen Prozessen"

Das Projekt demonstriert die Konzeption, Implementierung und Evaluation eines IT-Artefakts im Rahmen des Design-Science-Research-Paradigmas. Es vergleicht ein klassisches Textklassifikationsmodell (TF-IDF) mit einem modernen kontextuellen Transformer-Modell (SBERT). Zusätzlich wird eine konfidenzbasierte Rückweisungslogik (Risk-Coverage Trade-off) implementiert, um unsichere Vorhersagen gezielt an menschliche Sachbearbeitende ("Human-in-the-Loop") weiterzuleiten.

Voraussetzungen und Installation
Das Projekt wurde in Python 3.13.9 entwickelt. Um Konflikte mit bestehenden Paketen zu vermeiden, wird die Nutzung einer virtuellen Umgebung wie conda empfohlen.

Repository klonen oder entpacken:
Navigieren Sie im Terminal in das entpackte Projektverzeichnis.

Abhängigkeiten installieren:
Führen Sie folgenden Befehl aus, um die  Versionen der verwendeten Bibliotheken zu installieren:
pip install -r requirements.txt

Datenstruktur
Der Ausgangspunkt der Pipeline ist sind die beigefügten Trainings und Testdaten.

Pipeline-Architektur und Ausführung
Der Code ist modular aufgebaut

Bitte führen Sie die Skripte in dieser Reihenfolge aus, in der die Ordner nummeriert sind:




1: Training der Modelle und Hyperparameter-Tuning:

Benötigte Notebooks:
Training-SBERT.ipynb
Training-TF-IDF.ipynb

Input: Die beigefügten Trainingsdaten.
Für SBERT: train_SBERT.json
Für TF-IDF: train_TFIDF.json

Prozess: Führt eine 5-fache Kreuzvalidierung (Cross-Validation) mittels GridSearchCV durch, um den optimalen Regularisierungsparameter C für die Logistische Regression (optimiert auf f1_macro) zu finden und trainiert das Modell mit den Trainingsdaten.

Output: Generiert die persistenten Modell-Artefakte (.joblib-Dateien), die für die Assistenzpipeline.

2: Evaluation und McNemar-Test:


Benötigte Notebooks:
evaluation-der-Modelle.ipynb

Input: Die beigefügten Testdaten:
test_SBERT.json und test_TFIDF.json

Prozess: Berechnet Top-1-Accuracy, Top-3-Accuracy und den Macro-F1-Score auf den unbekannten Testdaten. Zeichnet Confusion Matrices zur qualitativen Fehleranalyse. Führt abschließend einen asymptotischen McNemar-Test ohne Kontinuitätskorrektur zur statistischen Signifikanzprüfung der Modelldifferenzen durch.

Output: Konsolenausgabe der Metriken, Visualisierung der Matrizen und Ausgabe der Kontingenztabelle inkl. p-Wert.

3: Ermittlung des Confidence-Thresholds:


Benötigte Notebooks:
ermittlung_Schwellenwert.ipynb

Input: Testdaten_SBERT.json und trainiertes_Klassifikationsmodell_SBERT.joblib. Letzteres wurde in Schritt 1 erstellt und persistent abgespeichert.

Prozess: Ermittelt iterativ die Confidence-Margin (Differenz zwischen Top-1- und Top-2-Wahrscheinlichkeit) für verschiedene Schwellenwerte.

Output: Gibt die Trade-off-Tabelle (Risk vs. Coverage bs. Accuracy vs. Human-in-the-Loop) aus, die in der Thesis zur Festlegung des Thresholds von 0.40 verwendet wird.

4: Prototypisches Assistenzsystem:

Bennötigte Notebooks:
Assistenzpipeline.ipynb

Input: Eine simulierte Eingabe in Form der Testmeldung-1.json.

Prozess: Wendet die Vorverarbeitung für SBERT an und leitet den Text durch das SBERT-Modell. Nutzt den etablierten Schwellenwert (0.40), um regelbasiert zu entscheiden, ob die Meldung automatisiert geroutet oder an die Sachbearbeitung (inkl. Top-3-Vorschlägen) abgewiesen wird.

Output: Konsolen-Log des simulierten administrativen Entscheidungsprozesses.
