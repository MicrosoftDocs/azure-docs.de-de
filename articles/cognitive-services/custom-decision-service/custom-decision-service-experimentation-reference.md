---
title: Experimentieren – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Dieser Artikel ist ein Leitfaden für das Experimentieren mit Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b5f8c853218a1db53f4dd23e7254b35990a7132b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829173"
---
# <a name="experimentation"></a>Experimentieren

Nach der Theorie des [Contextual Bandit (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/) (wörtlich „Kontextbandit“) beobachtet Custom Decision Service wiederholt einen Kontext, führt eine Aktion aus und beobachtet die Relevanz der ausgewählten Aktion. Ein Beispiel hierfür ist die Personalisierung von Inhalten: der Kontext beschreibt einen Benutzer, Aktionen sind potenzielle Storys und die Relevanz misst, wie sehr der Benutzer die empfohlene Story mochte.

Custom Decision Service erstellt eine Richtlinie, während die Kontexte den Aktionen zugeordnet werden. Bei einer spezifischen Zielrichtlinie sollten Sie wissen, welche Relevanz Sie erwarten können. Eine Möglichkeit zum Schätzen der Relevanz ist es, eine Richtlinie online zu verwenden und sie Aktionen auswählen zu lassen (z.B. Benutzern Storys empfehlen). Jedoch kann sich eine solche Onlineauswertung aus zwei Gründen als kostenintensiv erweisen:

* Sie setzt Benutzer einer ungetesteten, experimentellen Richtlinie aus.
* Sie kann nicht auf das Auswerten mehrerer Zielrichtlinien skaliert werden.

Die Auswertung ohne Richtlinien ist ein alternatives Beispiel. Wenn Sie über Protokolle von einem Onlinesystem mit einer Protokollierungsrichtlinie verfügen, kann die Auswertung ohne Richtlinien die erwartete Relevanz neuer Zielrichtlinien schätzen.

Unter Verwendung der Protokolldatei wird beim Experimentieren die Richtlinie mit der höchsten geschätzten Relevanz gesucht, die erwartet werden kann. Zielrichtlinien werden von [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki)-Argumenten parametrisiert. Im Standardmodus prüft das Skript eine Vielzahl von Vowpal Wabbit-Argumenten durch Anfügen an `--base_command`. Dieses Skript führt folgende Aktionen aus:

* Featurenamespaces werden automatisch in den ersten `--auto_lines`-Zeilen der Eingabedatei erkannt.
* Ein erster Sweep wird über Hyperparameter durchgeführt (`learning rate`, `L1 regularization` und `power_t`).
* Die Richtlinienauswertung `--cb_type` wird geprüft (inverse propensity score (umgekehrte Neigungsbewertung, `ips`) oder doubly robust (doppelt robust, `dr`)). Weitere Informationen finden Sie unter [Contextual Bandit example (Beispiel des Kontextbanditen)](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Marginale Werte werden geprüft.
* Quadratische Interaktionsfeatures werden geprüft:
   * **Brute-Force-Phase**: Alle Kombinationen mit `--q_bruteforce_terms`-Paaren oder weniger werden geprüft.
   * **Gierige Phase**: Fügt das beste Paar hinzu, bis es nach `--q_greedy_stop` Durchläufen keine Verbesserung gibt.
* Ein zweiter Sweep wird über Hyperparameter durchgeführt (`learning rate`, `L1 regularization` und `power_t`).

Die Parameter, die diese Schritte steuern, enthalten einige Vowpal Wabbit-Argumente:
- Beispieloptionen für die Manipulation:
  - Freigegebene Namespaces
  - Aktionsnamespaces
  - Marginale Namespaces
  - Quadratische Features
- Aktualisierungsregeloptionen
  - Lernrate
  - L1-Regularisierung
  - Wert „t power“

Eine ausführliche Erläuterung der obigen Argumente finden Sie unter [Vowpal Wabbit command-line arguments (Befehlszeilenargumente für Vowpal Wabbit)](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Voraussetzungen
- Vowpal Wabbit: Installiert und in Ihrem Pfad vorhanden.
  - Windows: [Verwenden des `.msi`-Installationsprogramms](https://github.com/eisber/vowpal_wabbit/releases).
  - Andere Plattformen: [Abrufen des Quellcodes](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Installiert und in Ihrem Pfad vorhanden.
- NumPy: Verwenden Sie einen beliebigen Paket-Manager.
- Das *Microsoft/mwt-ds*-Repository: [Klonen des Repositorys](https://github.com/Microsoft/mwt-ds).
- JSON-Protokolldatei für Custom Decision Service: Der Basisbefehl enthält standardmäßig `--dsjson`, wodurch die Analyse der Eingabedatendatei ermöglicht wird. [Ein Beispiel dieses Formats](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json)

## <a name="usage"></a>Verwendung
Navigieren Sie zu `mwt-ds/DataScience`, und führen Sie `Experimentation.py` wie im folgenden Code gezeigt mit den entsprechenden Argumenten aus:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Ein Protokoll der Ergebnisse wird der Datei *mwt-ds/DataScience/experiments.csv* angefügt.

### <a name="parameters"></a>Parameter
| Eingabe | BESCHREIBUNG | Standard |
| --- | --- | --- |
| `-h`, `--help` | Anzeigen der Hilfemeldung und Beenden. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Datendateipfad (Im Format `.json` oder `.json.gz`. Jede Zeile ist ein `dsjson`). | Erforderlich |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Vowpal Wabbit-Basisbefehl  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Anzahl der zu verwendenden parallelen Prozesse | Logische Prozessoren |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Freigegebene Featurenamespaces (z.B. entspricht `abc` den Namespaces `a`, `b` und `c`).  | Wird automatisch aus der Datendatei ermittelt |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Aktionsfeaturenamespaces | Wird automatisch aus der Datendatei ermittelt |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Marginale Featurenamespaces | Wird automatisch aus der Datendatei ermittelt |  
| `--auto_lines AUTO_LINES` | Anzahl der zu überprüfenden Datendateizeilen für die automatische Erkennung von Featurenamespaces. | `100` |  
| `--only_hp` | Sweep, der ausschließlich über Hyperparameter ausgeführt wird (`learning rate`, `L1 regularization` und `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Spanne der Lernrate in positiven Werten `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Spanne der L1-Regularisierung in positiven Werten `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Spanne von Power_t in positiven Werten `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Anzahl der quadratischen Paare, die in der Brute-Force-Phase getestet werden. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Durchläufe ohne Verbesserungen, nach denen die quadratische gierige Suchphase angehalten wird. | `3` |  

### <a name="examples"></a>Beispiele
Zur Verwendung der voreingestellten Standardwerte:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Gleichermaßen kann Vowpal Wabbit auch `.json.gz`-Dateien aufnehmen:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Für den Sweep nur über Hyperparameter (`learning rate`, `L1 regularization` und `power_t`, wird nach Schritt 2 angehalten):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
