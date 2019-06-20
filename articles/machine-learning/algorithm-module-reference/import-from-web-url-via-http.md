---
title: 'Importieren aus Web-URL über HTTP: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie das Modul zum Importieren aus einer Web-URL über HTTP in Azure Machine Learning Service verwenden können, um Daten auf einer öffentlichen Webseite für die Verwendung in einem Experiment zum maschinellen Lernen zu lesen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bff913efb38c9e5589c795386dfbbc480d799a37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411440"
---
# <a name="import-from-web-url-via-http-module"></a>Importieren aus Web-URL über das HTTP-Modul

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für den Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul zum Lesen von Daten auf einer öffentlichen Webseite zur Verwendung in einem Experiment für maschinelles Lernen.

Die folgenden Einschränkungen gelten für Daten, die auf einer Webseite veröffentlicht sind:

- Daten müssen in einem der unterstützten Formate vorliegen: CSV, TSV, ARFF oder SvmLight. Andere Daten verursachen Fehler.
- Eine Authentifizierung ist nicht erforderlich und wird auch nicht unterstützt. Daten müssen öffentlich verfügbar sein. 

Zum Abrufen von Daten gibt es zwei Möglichkeiten: die Verwendung des Assistenten zum Einrichten der Datenquelle oder die manuelle Konfiguration der Datenquelle.

## <a name="use-the-data-import-wizard"></a>Verwenden des Assistenten zum Importieren von Daten

1. Fügen Sie das Modul **Daten importieren** zu Ihrem Experiment hinzu. Sie finden das Modul in der Kategorie **Dateneingabe und -ausgabe** der Benutzeroberfläche.

2. Klicken Sie auf **Launch Import Data Wizard** (Datenimport-Assistenten starten), und wählen Sie „Web URL via HTTP“ aus.

3. Fügen Sie die URL ein, und wählen Sie ein Datenformat aus.

4. Wenn die Konfiguration abgeschlossen ist, klicken Sie mit der rechten Maustaste auf das Modul, und wählen Sie „Auswahl ausführen“ aus.

Starten Sie den Assistenten erneut, um eine vorhandene Datenverbindung zu bearbeiten. Der Assistent lädt alle vorherigen Konfigurationsdetails, damit Sie nicht ganz von vorne beginnen müssen.

## <a name="manually-set-properties-in-the-import-data-module"></a>Manuelles Festlegen der Eigenschaften im Modul „Daten importieren“

Die folgenden Schritte beschreiben, wie Sie die Importquelle manuell konfigurieren.

1. Fügen Sie das Modul [Daten importieren](import-data.md) zu Ihrem Experiment hinzu. Sie finden das Modul in der Kategorie **Dateneingabe und -ausgabe** der Benutzeroberfläche.

2. Wählen Sie als **Datenquelle** die Option **Web URL via HTTP** aus.

3. Für **URL** geben oder fügen Sie die vollständige URL der Seite mit den Daten ein, die Sie laden möchten.

    Die URL muss die Website-URL und den vollständigen Pfad (mit Dateiname und Erweiterung) zu der Seite mit den zu ladenden Daten enthalten.

    Die folgende Seite enthält beispielsweise den Iris-Datensatz aus dem Repository für maschinelles Lernen der University of California, Irvine:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Für **Datenformat** wählen Sie eines der unterstützten Datenformate aus der Liste aus.

    Es wird empfohlen, die Daten immer vorab zu überprüfen, um das Format zu bestimmen. Die UC Irvine-Seite verwendet das CSV-Format. Andere unterstützte Datenformate sind TSV, ARFF und SvmLight.

5. Wenn die Daten im CSV- oder TSV-Format vorliegen, verwenden Sie die Option **Die Datei enthält eine Kopfzeile**, um anzugeben, ob die Quelldaten eine Kopfzeile enthalten oder nicht. Die Kopfzeile dient der Vergabe von Spaltennamen.

6. Wählen Sie die Optionen zum **Verwenden zwischengespeicherter Ergebnisse** , wenn Sie nicht erwarten, dass sich die Daten stark ändern, oder wenn Sie vermeiden möchten, dass die Daten bei jeder Durchführung des Experiments neu geladen werden.

    Wenn diese Option ausgewählt ist, lädt das Experiment die Daten bei der ersten Ausführung des Moduls und verwendet danach eine zwischengespeicherte Version des Datasets.

    Wenn Sie das Dataset bei jeder Iteration des Experimentdatasets neu laden möchten, deaktivieren Sie die Option zum **Verwenden zwischengespeicherter Ergebnisse**. Ergebnisse werden auch erneut geladen, wenn Änderungen an den Parametern von [Importdaten](import-data.md) erfolgt sind.

7. Führen Sie das Experiment aus.

## <a name="results"></a>Ergebnisse

Klicken Sie nach Abschluss des Experiments auf das Ausgabedataset, und wählen Sie **Visualisieren** aus, um festzustellen, ob die Daten erfolgreich importiert wurden.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 