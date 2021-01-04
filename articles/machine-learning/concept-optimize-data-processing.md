---
title: Optimieren der Datenverarbeitung
titleSuffix: Azure Machine Learning
description: Lernen Sie bewährte Methoden zur Optimierung der Geschwindigkeit der Datenverarbeitung kennen und erfahren Sie, welche Integrationen Azure Machine Learning für eine Datenverarbeitung nach Maß unterstützt.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.custom: data4ml
ms.openlocfilehash: 77edac14ef13901725eed656835e1a937d4f4ddf
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360818"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Optimieren der Datenverarbeitung mit Azure Machine Learning

In diesem Artikel lernen Sie bewährte Methoden kennen, mit denen Sie die Geschwindigkeit der Datenverarbeitung lokal und nach Maß optimieren können.

Azure Machine Learning ist in Open-Source-Pakete und Frameworks zur Datenverarbeitung integriert. Durch Verwenden dieser Integrationen und Anwenden der in diesem Artikel empfohlenen bewährten Methoden können Sie die Geschwindigkeit Ihrer Datenverarbeitung sowohl lokal als auch nach Maß verbessern.

## <a name="parquet-and-csv-file-formats"></a>Die Dateiformate Parquet und CSV

Dateien mit durch Trennzeichen getrennte Werte (CSV) sind gängige Dateiformate für die Datenverarbeitung. Für Aufgaben im Bereich maschinelles Lernen werden jedoch Parquet-Dateiformate empfohlen.

In [Parquet-Dateien](https://parquet.apache.org/) werden Daten in einem binären Spaltenformat gespeichert. Dieses Format ist nützlich, wenn die Daten in mehrere Dateien aufgeteilt werden müssen. Außerdem können Sie mit diesem Format die relevanten Felder für Ihre Experimente zum maschinellen Lernen gezielt ansteuern. Anstatt eine Datendatei mit 20 GB einlesen zu müssen, können Sie diese Datenlast verringern, indem Sie zum Trainieren Ihres ML-Modells die erforderlichen Spalten auswählen. Parquet-Dateien können auch komprimiert werden, um weniger Verarbeitungsleistung zu beanspruchen und Speicherplatz zu belegen.

CSV-Dateien werden häufig zum Im- und Exportieren von Daten verwendet, da sie in Excel leicht zu bearbeiten und zu lesen sind. Die Daten in CSV-Dateien werden als Zeichenfolgen in einem Zeilenformat gespeichert. Die Dateien können komprimiert werden, um die Datenübertragungslast zu verringern. Unkomprimierte CSV-Dateien können um das 2- bis 10-fache erweitert werden. Komprimierte CSV-Dateien können sogar noch größer werden. Dadurch wird eine CSV-Datei mit 5 GB im Arbeitsspeicher auf deutlich mehr als die 8 GB RAM erweitert, die Ihr Computer bietet. Dieses Komprimierungsverhalten kann die Datenübertragungslatenz erhöhen, was bei großen zu verarbeitenden Datenmengen nicht ideal ist. 

## <a name="pandas-dataframe"></a>Pandas-Datenrahmen

[Pandas-Datenrahmen](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) werden häufig für die Datenbearbeitung und -analyse verwendet. `Pandas` eignet sich gut für Datengrößen von weniger als 1 GB, aber die Verarbeitungszeiten für `pandas`-Datenrahmen verlangsamen sich, wenn die Dateigrößen etwa 1 GB erreichen. Diese Verlangsamung ist darauf zurückzuführen, dass die Größe Ihrer Daten im Speicher nicht mit der Größe der Daten in einem Datenrahmen übereinstimmt. Beispielsweise können Daten in CSV-Dateien sich in einem Datenrahmen um das 10-fache ausdehnen, sodass aus einer 1 GB großen CSV-Datei 10 GB in einem Datenrahmen werden können.

`Pandas` arbeitet mit Singlethreadvorgängen, die einzeln nacheinander auf einer einzigen CPU erfolgen. Mit Paketen wie [Modin](https://modin.readthedocs.io/en/latest/), die `Pandas` mithilfe eines verteilten Back-Ends umschließen, können Sie Workloads ganz einfach auf mehrere virtuelle CPUs einer einzelnen Azure Machine Learning-Compute-Instanz parallelisieren.

Um Ihre Aufgaben mit `Modin` und [Dask](https://dask.org) zu parallelisieren, ändern Sie einfach diese Codezeile `import pandas as pd` in `import modin.pandas as pd`.

## <a name="dataframe-out-of-memory-error"></a>Datenrahmen: zu wenig Arbeitsspeicher 

Der Fehler *Zu wenig Arbeitsspeicher* tritt typischerweise auf, wenn Ihr Datenrahmen über den auf Ihrem Computer verfügbaren RAM hinaus ausgedehnt wird. Dieses Konzept gilt auch für ein verteiltes Framework wie `Modin` oder `Dask`.  Das heißt, Ihr Vorgang versucht, den Datenrahmen im Arbeitsspeicher auf jedem Knoten in Ihrem Cluster zu laden, ohne dass jedoch genügend RAM dafür zur Verfügung steht.

Eine Lösung ist die Vergrößerung Ihres RAM dergestalt, dass der Datenrahmen in den Arbeitsspeicher passt. Wir empfehlen, dass Ihre Computegröße und Verarbeitungsleistung der doppelten Größe des RAM entsprechen. Wenn Ihr Datenrahmen also 10 GB groß ist, benötigen Sie ein Computeziel mit mindestens 20 GB RAM, um sicherzustellen, dass der Datenrahmen bequem in den Arbeitsspeicher passt und verarbeitet werden kann. 

Denken Sie bei mehreren virtuellen CPUs (vCPUs) daran, dass eine Partition bequem in den RAM passen soll, über den jede vCPU der VM verfügen kann. Das heißt, wenn Sie 4 vCPUs mit 16 GB RAM haben, benötigen Sie pro vCPU etwa 2 GB große Datenrahmen.

### <a name="local-vs-remote"></a>Lokal und remote im Vergleich

Möglicherweise stellen Sie fest, dass bestimmte pandas-Datenrahmenbefehle beim Arbeiten auf Ihrem lokalen PC schneller ausgeführt werden als auf einer Remote-VM, die Sie mit Azure Machine Learning bereitgestellt haben. Ihr lokaler PC verfügt in der Regel über eine aktivierte Auslagerungsdatei, die es Ihnen ermöglicht, mehr als die Daten zu laden, die in den physischen Arbeitsspeicher passen, d. h. Ihre Festplatte wird als Erweiterung Ihres RAM verwendet. Derzeit werden Azure Machine Learning-VMs ohne Auslagerungsdatei ausgeführt, daher können nur so viele Daten geladen werden, wie physischer RAM verfügbar ist. 

Für rechenintensive Aufträge empfiehlt es sich, eine größere VM auszuwählen, um die Verarbeitungsgeschwindigkeit zu verbessern.

Erfahren Sie mehr über die [verfügbaren VM-Serien und -Größen](concept-compute-target.md#supported-vm-series-and-sizes) für Azure Machine Learning. 

Informationen zu den RAM-Spezifikationen finden Sie auf den entsprechenden Seiten zu den VM-Serien, z. B. zur [Dv2-DSv2-Serie](../virtual-machines/dv2-dsv2-series-memory.md) oder [NC-Serie](../virtual-machines/nc-series.md).

### <a name="minimize-cpu-workloads"></a>Minimieren von CPU-Workloads

Wenn Sie Ihrem Computer nicht mehr RAM hinzufügen können, können Sie die folgenden Maßnahmen ergreifen, um CPU-Workloads zu minimieren und Verarbeitungszeiten zu optimieren. Diese Empfehlungen gelten für Einzel- und verteilte Systeme.

Verfahren | Beschreibung
----|----
Komprimierung | Wählen Sie für Ihre Daten eine andere Darstellung auf eine Weise, die weniger Arbeitsspeicher beansprucht und die Ergebnisse Ihrer Berechnung nicht wesentlich beeinflusst.<br><br>*Beispiel:* Anstatt Einträge als Zeichenfolge mit etwa 10 Bytes oder mehr pro Eintrag zu speichern, speichern Sie sie als boolesche Werte (TRUE oder FALSE), die Sie in 1 Byte speichern könnten.
Segmentierung | Laden Sie Daten in Teilmengen (Segmenten) in den Arbeitsspeicher, wobei die Daten jeweils als eine Teilmenge oder mehrere Teilmengen parallel verarbeitet werden. Diese Methode funktioniert am besten, wenn Sie alle Daten verarbeiten müssen, aber nicht alle Daten auf einmal in den Arbeitsspeicher laden müssen. <br><br>*Beispiel:* Anstatt die Daten eines ganzen Jahres auf einmal zu verarbeiten, laden und verarbeiten Sie die Daten jeweils monatsweise.
Indizierung | Wenden Sie einen Index an, um eine Zusammenfassung zu erhalten, die Ihnen sagt, wo Sie die Daten finden, die Sie interessieren. Eine Indizierung ist nützlich, wenn Sie nur eine Teilmenge der Daten anstelle des vollständigen Datasets verwenden müssen.<br><br>*Beispiel:* Wenn Sie die Umsatzdaten eines ganzen Jahres nach Monaten sortiert haben, hilft Ihnen ein Index bei der schnellen Suche nach dem gewünschten Monat, den Sie verarbeiten möchten.

## <a name="scale-data-processing"></a>Skalieren der Datenverarbeitung

Wenn die vorherigen Empfehlungen nicht ausreichen und Sie keinen virtuellen Computer beschaffen können, der zu Ihren Daten passt, haben Sie folgende Möglichkeiten: 

* Verwenden Sie ein Framework wie `Spark` oder `Dask`, um die Daten trotz zu wenig Arbeitsspeicher zu verarbeiten. Bei dieser Option wird der Datenrahmen partitionsweise in den RAM geladen und verarbeitet, wobei das Endergebnis am Ende zusammengeführt wird.  

* Nehmen Sie mithilfe eines verteilten Frameworks ein Aufskalieren zu einem Cluster vor. Bei dieser Option werden die Datenverarbeitungslasten aufgeteilt und auf mehreren parallel arbeitenden CPUs verarbeitet, wobei das Endergebnis am Ende zusammengeführt wird.

### <a name="recommended-distributed-frameworks"></a>Empfohlene verteilte Frameworks

In der folgenden Tabelle werden verteilte Frameworks, die in Azure Machine Learning integriert sind, basierend auf Ihrer Codepräferenz oder Datengröße empfohlen.

Erfahrung oder Datengröße | Empfehlung
------|------
Wenn Sie mit `Pandas` vertraut sind| `Modin`- oder `Dask`-Datenrahmen
Wenn Sie `Spark` bevorzugen | `PySpark`
Für Daten kleiner als 1 GB | `Pandas` lokal **oder** eine Azure Machine Learning-Remotecompute-Instanz
Für Daten größer als 10 GB| Wechsel zu einem Cluster unter Verwendung von `Ray`, `Dask` oder `Spark`

Sie können `Dask`-Cluster in Azure ML-Computeclustern mit dem Paket [dask-cloudprovider](https://cloudprovider.dask.org/en/latest/#azure) erstellen. Oder Sie können `Dask` lokal auf einer Compute-Instanz ausführen.

## <a name="next-steps"></a>Nächste Schritte

* [Optionen für die Datenerfassung in Azure Machine Learning](concept-data-ingestion.md)
* [Erstellen und Registrieren von Datenspeichern](how-to-create-register-datasets.md)
