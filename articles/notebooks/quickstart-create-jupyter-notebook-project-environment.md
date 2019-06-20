---
title: Erstellen eines Azure Notebooks-Projekts mit benutzerdefinierter Umgebung
description: Erstellen Sie ein neues Projekt in Azure Notebooks, das mit einem bestimmten Satz installierter Pakete und Startskripts konfiguriert wird.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 954bbc22e42865991ca7f38fbf3f6f0e8e78a437
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754032"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Schnellstart: Erstellen eines Projekts mit benutzerdefinierter Umgebung

Ein Projekt in Azure Notebooks ist eine Sammlung von Dateien, wie etwa Notebooks, Datendateien, Dokumentation, Bildern usw. in Verbindung mit einer Umgebung, die mit bestimmten Setupbefehlen konfiguriert werden kann. Indem Sie die Umgebung zugleich mit dem Projekt definieren, verhelfen Sie jedem, der das Projekt in das eigene Azure Notebooks-Konto klont, zu allen Informationen, die zum Neuerstellen der erforderlichen Umgebung benötigt werden.

## <a name="create-a-project"></a>Erstellen eines Projekts

1. Navigieren Sie zu [Azure Notebooks](https://notebooks.azure.com), und melden Sie sich an. (Ausführliche Informationen hierzu finden Sie unter [Schnellstart: Anmelden bei Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Wählen Sie oben auf Ihrer öffentlichen Profilseite **Meine Projekte** aus:

    ![Link „Meine Projekte“ am oberen Rand des Browserfensters](media/quickstarts/my-projects-link.png)

1. Klicken Sie auf der Seite **My Projects** (Meine Projekte) auf **+ New Project** (+ Neues Projekt, Tastenkombination: n). Wenn Ihr Browserfenster schmal ist, wird möglicherweise nur **+** für die Schaltfläche angezeigt:

    ![Befehl „New Project“ (Neues Projekt) auf der Seite „My Projects“ (Meine Projekte)](media/quickstarts/new-project-command.png)

1. Geben Sie folgende Informationen im angezeigten Popupfenster **Create New Project** (Neues Projekt erstellen) an, und klicken Sie anschließend auf **Create** (Erstellen):

    - **Projektname**: Projekt mit benutzerdefinierter Umgebung
    - **Projekt-ID**: projekt-benutzerdefinierte-umgebung
    - **Public project** (Öffentliches Projekt): (nicht aktiviert)
    - **Create a README.md** (README-Datei erstellen): (nicht aktiviert)

1. Nach einigen Augenblicken navigiert Azure Notebooks zum neuen Projekt. Fügen Sie dem Projekt ein Notebook hinzu, indem Sie das Dropdownelement **+ New** (+ Neu) (das möglicherweise nur als **+** angezeigt wird) und dann **Notebook** auswählen.

1. Geben Sie dem Notebook einen Namen wie *Custom environment.ipynb*, wählen Sie **Python 3.6** als Sprache und dann **Neu** aus.

## <a name="add-a-custom-setup-step"></a>Hinzufügen eines benutzerdefinierten Setupschritts

1. Wählen Sie auf der Projektseite **Projekteinstellungen** aus.

    ![Befehl „Projekteinstellungen“](media/quickstarts/project-settings-command.png)

1. Wählen Sie im Popupfenster **Projekteinstellungen** die Registerkarte **Umgebung** und dann unter **Environment Setup Steps** (Schritte zum Einrichten der Umgebung) **+ Hinzufügen** aus:

    ![Befehl zum Hinzufügen eines neuen Setupschritts für die Umgebung](media/quickstarts/environment-add-command.png)

1. Der Befehl **+ Hinzufügen** erstellt einen Schritt, der durch einen Vorgang und eine Zieldatei definiert ist, die Sie aus den Dateien in Ihrem Projekt auswählen. Die folgenden Operationen werden unterstützt:

    | Vorgang | BESCHREIBUNG |
    | --- | --- |
    | Requirements.txt | Die Abhängigkeiten von Python-Projekten sind in einer Datei „requirements.txt“ definiert. Wählen Sie mit dieser Option die entsprechende Datei aus der Dateiliste des Projekts aus, und wählen Sie in dem zusätzlichen Dropdownfeld, das angezeigt wird, auch die Python-Version aus. Wählen Sie ggf. **Abbrechen** aus, um zum Projekt zurückzukehren, laden Sie die Datei hoch oder erstellen Sie sie, und kehren Sie dann zu **Projekteinstellungen** > **Umgebung** zurück, und erstellen Sie den neuen Schritt. Wenn dieser Schritt implementiert ist, bewirkt das Ausführen eines Notebooks im Projekt die automatische Ausführung von `pip install -r <file>` |
    | Shellskript | Verwenden Sie diese Option, um ein Bash-Shellskript festzulegen (normalerweise eine Datei mit der Erweiterung *.sh*), die alle Befehle enthält, die Sie in der Umgebung initialisieren möchten. |
    | Environment.yml | Ein Python-Projekt, das Conda für die Verwaltung der Umgebung einsetzt, verwendet eine Datei *environments.yml* zum Beschreiben der Abhängigkeiten. Wählen Sie für diese Option die entsprechende Datei in der Dateiliste des Projekts aus. |

1. Um einen Setupschritt zu entfernen, wählen Sie das **X** rechts neben dem Schritt aus.

1. Wenn alle Setupschritte implementiert wurde, wählen Sie **Speichern** aus. (Wählen Sie **Abbrechen** aus, um Änderungen zu verwerfen).

1. Um Ihre Umgebung zu testen, erstellen Sie ein neues Notebook und führen es aus, erstellen Sie dann eine Codezelle mit Anweisungen, die von einem Paket in der Umgebung abhängig sind, etwa dadurch, dass sie eine Python-Anweisung `import` enthalten. Wenn die Anweisung erfolgreich ausgeführt wird, wurde das erforderliche Paket erfolgreich in der Umgebung installiert.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten und Konfigurieren von Projekten in Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Ausführen eines Jupyter-Notebooks für lineare Regression](tutorial-create-run-jupyter-notebook.md)
