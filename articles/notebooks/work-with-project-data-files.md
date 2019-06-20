---
title: Importieren und Exportieren von Daten mit Projekten in Azure Notebooks
description: Einbringen von Daten aus externen Quellen in ein Azure Notebooks-Projekt und Exportieren von Daten aus einem Projekt.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 586b423b-6668-4bdd-9592-4c237d7458fb
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b522b0bd641d0147518843b11be4cd3a1430ae20
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240431"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Arbeiten mit Datendateien in Azure Notebooks-Projekten

Daten bilden den zentralen Bestandteil vieler Jupyter-Notebooks, insbesondere von Notebooks, die für Data Science verwendet werden. Mit Azure Notebooks können Sie Daten auf einfache Weise aus einer Vielzahl von Quellen in ein Projekt importieren und diese Daten dann aus Notebooks heraus verwenden. Ferner können Sie Notebooks Daten generieren lassen, die im Projekt gespeichert werden und die Sie dann zur Verwendung an anderer Stelle herunterladen können.

Das Menü **Daten** innerhalb eines ausgeführten Notebooks stellt außerdem Befehle zum **Hochladen** und **Herunterladen** bereit, die sowohl für Dateien im Projekt als auch für temporäre Dateien in der aktuellen Notebooksitzung verwendet werden können.

Ferner können Sie Code innerhalb eines Notebooks verwenden, um direkt auf eine Vielzahl von Datenquellen zuzugreifen, einschließlich Dateien innerhalb eines Projekts. Auch der Zugriff auf beliebige Daten ist durch Verwendung von Befehlen in einer Codezelle möglich. Da solche Daten in Variablen innerhalb der Notebooksitzungen gespeichert werden, werden sie nicht im Projekt gespeichert, es sei denn, Sie verwenden spezifischen Code zur Generierung von Projektdateien.

Das Arbeiten mit Code in Daten lässt sich am besten innerhalb eines ausgeführten Notebooks erfahren: Lesen Sie dazu [Getting to your data in Azure Notebooks sample notebook](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb) (Zugreifen auf Ihre Daten im Azure Notebooks-Beispielnotebook).

Der Rest dieses Artikels bietet Details zu Dateioperationen auf Projektebene.

## <a name="import-data"></a>Daten importieren

Sie können Dateien auf dem Projektdashboard oder innerhalb eines ausgeführten Notebooks mithilfe des Menüs **Daten** oder eines Befehls wie `curl` in ein Projekt importieren.

### <a name="import-files-from-the-project-dashboard"></a>Importieren von Dateien auf dem Projektdashboard

1. Navigieren Sie im Projekt zu dem Ordner, in den Sie die Dateien importieren möchten.

1. Wählen Sie den Befehl **Hochladen** und dann entweder **Von URL** oder **Von Computer** aus, und geben Sie die erforderlichen Details für die zu importierenden Daten an:

   - **Von URL**: Geben Sie die Quelladresse im Feld **Datei-URL** und den Dateinamen, der dem Notebook in Ihrem Projekt zugewiesen werden soll, im Feld **Dateiname** an. Wählen Sie dann **+ Datei hinzufügen** aus, um die Datei der Uploadliste hinzuzufügen. Wiederholen Sie den Vorgang für alle weiteren URLs, und wählen Sie dann **Fertig** aus.

     ![Hochladen über das URL-Popup](media/quickstarts/upload-from-url-popup.png)

   - **Von Computer**: Ziehen Sie Dateien auf das Popup, und legen Sie sie dort ab, oder wählen Sie **Dateien auswählen** aus, navigieren Sie dann zu den zu importierenden Datendateien, und wählen Sie sie aus. Sie können eine beliebige Anzahl Dateien beliebigen Typs und in beliebigem Format ablegen oder auswählen, da das Öffnen der Dateien und das Analysieren ihrer Daten dem Code im Notebook obliegt.

     ![Hochladen aus dem Computerpopup](media/quickstarts/upload-from-computer-popup.png)

1. Nach dem Import werden Dateien auf dem Projektdashboard angezeigt. Der Zugriff darauf kann innerhalb von Notebookcode mithilfe relativer Pfade zum enthaltenden Ordner erfolgen.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importieren von Dateien über das Menü „Datei“ in einem Notebook

1. Wählen Sie innerhalb eines ausgeführten Notebooks den Befehl **Datei** > **Hochladen** aus:

    ![Menübefehl „Datei“ > „Hochladen“ in einem Notebook](media/file-menu-upload.png)

1. Navigieren Sie in dem Dialogfeld, das sich öffnet, zu den Dateien, die Sie hochladen möchten, und wählen Sie sie aus. Sie können eine beliebige Anzahl Dateien beliebigen Typs auswählen. Wählen Sie nach Abschluss des Vorgangs **Öffnen**aus.

1. Wählen Sie im Popup **Uploadstatus**, das angezeigt wird, einen **Zielordner** in der Dropdownliste aus:

    - Sitzungsordner ( *~/* ): Lädt Dateien in die aktuelle Notebooksitzung hoch, erstellt aber keine Dateien im Projekt. Der Sitzungsordner befindet sich auf gleicher Ebene wie der Projektordner, bleibt aber nach dem Ende der Sitzung nicht erhalten. Um auf Sitzungsdateien in Code zuzugreifen, stellen Sie den Dateinamen den relativen Pfad *../* voran.

        Das Verwenden des Sitzungsordners ist für Experimente nützlich und hilft, das Anfüllen des Projekts mit Dateien, die Sie langfristig möglicherweise nicht benötigen, zu vermeiden. Ferner können Sie in den Sitzungsordner Dateien hochladen, die identische Namen wie Dateien im Projektordner aufweisen, ohne dadurch Konflikte auszulösen und ohne die Dateien umzubenennen. Nehmen wir beispielsweise an, Sie besitzen bereits eine Version von *data.csv* im Projekt, möchten aber experimentell eine andere Version von *data.csv* verwenden. Durch Hochladen der Datei in den Sitzungsordner können Sie das Notebook mit den Daten in der hochgeladenen Datei (indem Sie mithilfe von *../data.csv* auf sie verweisen) anstelle der Daten in der Datei des Projekts ausführen.

    - Projektordner ( */project*): Lädt Dateien in das Projekt hoch, wo über relative Pfade im Code auf sie zugegriffen werden kann. Das Hochladen einer Datei in diesen Ordner bewirkt das Gleiche wie das Hochladen einer Datei auf dem Projektdashboard. Die Datei wird mit dem Projekt gespeichert und steht in späteren Sitzungen zur Verfügung.

        Beim Hochladen tritt ein Fehler auf, wenn Sie versuchen, eine Datei mit gleichem Namen wie eine bereits im Projekt vorhandene Datei hochzuladen. Um eine Datei zu überschreiben, laden Sie die neue Datei stattdessen auf dem Projektdashboard hoch, wo Ihnen die Option zum Überschreiben zur Verfügung steht.

1. Wählen Sie **Upload starten** aus, um den Vorgang abzuschließen.

### <a name="create-or-import-files-using-commands"></a>Erstellen oder Importieren von Dateien mithilfe von Befehlen

Sie können Befehle in einem Terminalfenster oder innerhalb einer Python-Codezelle verwenden, um wahlweise im Projekt- oder im Sitzungsordner Dateien zu erstellen. Beispielsweise lassen sich mit Befehlen wie `curl` und `wget` Dateien direkt aus dem Internet herunterladen.

Um Dateien im Terminal herunterzuladen, wählen Sie auf dem Projektdashboard den Befehl **Terminal** aus, und geben Sie dann passende Befehle ein:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Bei der Verwendung einer Python-Codezelle in einem Notebook stellen Sie den Befehlen `!` voran.

Der Projektordner ist der Standardordner, die Angabe eines Zieldateinamens wie *oil_price.csv* bewirkt daher die Erstellung der Datei im Projekt. Um eine Sitzungsdatei zu erstellen, stellen Sie dem Namen *../* voran, wie in *../oil_price.csv*.

### <a name="create-files-in-code"></a>Erstellen von Dateien im Code

Wenn Sie Code zum Erstellen einer Datei verwenden, wie etwa die Pandas-Funktion `write_csv`, sind Pfadnamen immer relativ zum Projektordner. Mit der Angabe von *../* wird eine Sitzungsdatei erstellt, die beim Beenden und Schließen des Notebooks verworfen wird.

## <a name="export-files"></a>Exportieren von Dateien

Sie können Daten auf dem Projektdashboard oder aus einem Notebook exportieren.

## <a name="export-files-from-the-project-dashboard"></a>Exportieren von Dateien auf dem Projektdashboard

Klicken Sie auf dem Projektdashboard mit der rechten Maustaste auf eine Datei, und wählen Sie **Herunterladen** aus:

![Befehl „Herunterladen“ im Kontextmenü eines Projektelements](media/download-command.png)

Sie können aber auch eine Datei auswählen und den Befehl **Herunterladen** (Tastenkombination: d) auf dem Dashboard verwenden:

![Symbolleistenbefehl „Herunterladen“ auf dem Projektdashboard](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportieren von Dateien über das Menü „Daten“ in einem Notebook

1. Wählen Sie den Menübefehl **Datei** > **Herunterladen** aus:

    ![Menübefehl „Datei“ > „Herunterladen“ in einem Notebook](media/file-menu-download.png)

1. Ein Popupfenster mit den Ordnern in der Sitzung wird angezeigt; der Ordner *project* enthält die Projektdateien:

    ![Popupfenster des Befehls „Daten“ > „Herunterladen“ zur Auswahl von Dateien und Ordnern](media/file-menu-download-popup.png)

1. Markieren Sie die Felder links neben den herunterzuladenden Dateien und Ordnern, und wählen Sie dann **Ausgewählte herunterladen** aus.

1. Das Notebook bereitet eine einzelne *ZIP*-Datei vor, die die ausgewählten Dateien enthält, die Sie dann im Browser wie gewohnt speichern. Das Notebook erstellt auch dann eine *ZIP*-Datei, wenn Sie nur eine einzelne Datei herunterladen.

## <a name="next-steps"></a>Nächste Schritte

- [Zugreifen auf Clouddaten in einem Notebook](access-data-resources-jupyter-notebooks.md)
