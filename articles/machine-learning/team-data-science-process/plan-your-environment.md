---
title: 'Bestimmen von Szenarien und Planen des Analyseprozesses: Team Data Science-Prozess | Azure Machine Learning'
description: Bestimmen von Szenarien und Planen der Datenverarbeitung für die erweiterte Analyse durch Berücksichtigen einer Reihe wichtiger Fragen.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ffe1a4b9b32223cc57c6a6399fdc074a9d51d714
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322398"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Bestimmen von Szenarien und Planen der Datenverarbeitung für die erweiterte Analyse

Welche Ressourcen werden benötigt, um eine Umgebung zu erstellen, die eine erweiterte Analyseverarbeitung für ein Dataset durchführen kann? In diesem Artikel werden verschiedene zu stellende Fragen vorgeschlagen, mit deren Hilfe Sie die für Ihr Szenario relevanten Aufgaben und Ressourcen bestimmen können.

Weitere Informationen zur Reihenfolge der allgemeinen Schritte für Predictive Analytics finden Sie unter [Was ist der Team Data Science-Prozess (TDSP)?](overview.md). Jeder Schritt erfordert bestimmte Ressourcen für die Aufgaben, die für Ihr spezielles Szenario relevant sind.

Beantworten Sie wichtige Fragen in den folgenden Bereichen, um Ihr Szenario zu bestimmen:

* Datenlogistik
* Dateneigenschaften
* Dataset-Qualität
* bevorzugte Tools und Sprachen

## <a name="logistic-questions-data-locations-and-movement"></a>Logistische Fragen: Datenspeicherorte und -verschiebung

Die Fragen zur Datenlogistik behandeln die folgenden Elemente:

* Speicherort der Datenquelle
* Ziel in Azure
* Anforderungen für das Verschieben von Daten, einschließlich Zeitplan, Menge und beteiligte Ressourcen

Die Daten müssen möglicherweise während des Analyseprozesses mehrere Male verschoben werden. Ein gängiges Szenario ist das Verschieben lokaler Daten in eine Form von Speicher in Azure und anschließend in Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Was ist Ihre Datenquelle?

Sind Ihre Daten lokal oder in der Cloud vorhanden? Mögliche Speicherorte sind:

* eine öffentlich zugängliche HTTP-Adresse
* ein lokaler Speicherort oder ein Speicherort im Netzwerk
* eine SQL Server-Datenbank
* ein Azure Storage-Container

### <a name="what-is-the-azure-destination"></a>Was ist das Azure-Ziel?

Wo müssen sich Ihre Daten für Verarbeitungs- oder Modellierungszwecke befinden? 

* Azure Blob Storage
* SQL Azure-Datenbanken
* SQL Server auf Azure-VMs
* HDInsight (Hadoop in Azure) oder Hive-Tabellen
* Azure Machine Learning
* Bereitstellbare virtuelle Azure-Datenträger

### <a name="how-are-you-going-to-move-the-data"></a>Wie möchten Sie die Daten verschieben?

Verfahren und Ressourcen zum Erfassen oder Laden von Daten in eine Vielzahl von verschiedenen Speicher- und Verarbeitungsumgebungen finden Sie hier:

* [Laden von Daten in Speicherumgebungen für Analysen](ingest-data.md)
* [Importieren von Trainingsdaten aus verschiedenen Datenquellen in Azure Machine Learning Studio (klassisch)](../classic/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Müssen die Daten nach einem regelmäßigen Zeitplan verschoben oder während der Migration geändert werden?

Erwägen Sie den Einsatz von Azure Data Factory (ADF), wenn Daten kontinuierlich migriert werden müssen. ADF kann hilfreich sein für:

* ein Hybridszenario für sowohl lokale als auch cloudbasierte Ressourcen
* ein Szenario, in dem die Daten durch die Geschäftslogik im Zuge der Migration transaktiert, modifiziert oder geändert werden.

Weitere Informationen finden Sie unter [Verschieben von Daten von einer SQL Server-Datenbank zu SQL Azure mithilfe von Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Wie viele Daten werden in Azure verschoben?

Große Datasets können die Speicherkapazität bestimmter Umgebungen überschreiten. Ein Beispiel finden Sie in der Erörterung von Größenbeschränkungen für Machine Learning Studio (klassisch) im nächsten Abschnitt. In solchen Fällen kann eine Stichprobe der Daten während der Analyse verwendet werden. Details zum Erstellen von Stichproben in verschiedenen Azure-Umgebungen finden Sie unter [Stichprobendaten im Team Data Science-Prozess](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Fragen zu Datenmerkmalen: Typ, Format und Größe

Diese Fragen sind wichtig für die Planung Ihrer Speicher- und Verarbeitungsumgebungen. Sie helfen Ihnen, das für Ihren Datentyp geeignete Szenario auszuwählen und die Einschränkungen zu verstehen.

### <a name="what-are-the-data-types"></a>Was sind die Datentypen?

* Numerisch
* Kategorisch
* Zeichenfolgen
* Binary

### <a name="how-is-your-data-formatted"></a>Wie sind Ihre Daten formatiert?

* Durch Trennzeichen getrennte (CSV) oder durch Tabulator getrennten (TSV) Flatfiles
* Komprimierte oder nicht komprimierte
* Azure-Blobs
* Hadoop Hive-Tabellen
* SQL Server-Tabellen

### <a name="how-large-is-your-data"></a>Wie umfangreich sind Ihre Daten?

* Klein: Weniger als 2 GB
* Mittel: Mehr als 2 GB, aber weniger als 10 GB
* Groß: Mehr als 10 GB

Nehmen wir beispielsweise die (klassische) Azure Machine Learning Studio-Umgebung:

* Eine Liste der Datenformate und -typen, die von Azure Machine Learning Studio unterstützt werden, finden Sie im Abschnitt [Unterstützte Datenformate und Datentypen](../classic/import-data.md#supported-data-formats-and-data-types) .
* Informationen zu den Einschränkungen anderer Azure-Dienste, die im Analyseprozess verwendet werden, finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Fragen zur Datenqualität: Untersuchung und Vorverarbeitung

### <a name="what-do-you-know-about-your-data"></a>Was wissen Sie über Ihre Daten?

Verstehen der grundlegenden Merkmale Ihrer Daten:

* Welche Muster oder Trends weisen sie auf
* Gibt es Ausreißer
* Wie viele Werte fehlen

Mit diesem wichtigen Schritt können Sie:

* Bestimmen, wie viel Verarbeitung vorab benötigt wird
* Hypothesen formulieren, die die am besten geeignete Funktionen oder der Typ der Analyse vorschlagen
* Pläne für die Sammlung von zusätzlichen Daten formulieren

Zu den nützlichen Techniken für die Dateninspektion gehören deskriptive Statistikberechnungen und Visualisierungsdiagramme. Details zum Untersuchen eines Datasets in verschiedenen Azure-Umgebungen finden Sie unter [Durchsuchen von Daten im Team Data Science-Prozess](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Erfordern die Daten eine Vorverarbeitung oder Bereinigung?

Möglicherweise müssen Sie Ihre Daten vorverarbeiten und bereinigen, bevor Sie das Dataset effektiv für Machine Learning nutzen können. Rohdaten sind oft verrauscht und unzuverlässig. Es fehlen möglicherweise Werte. Die Verwendung dieser Daten für die Modellierung kann zu falschen Ergebnissen führen. Eine Beschreibung finden Sie unter [Aufgaben zur Vorbereitung von Daten für erweitertes Machine Learning](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Fragen zu Tools und Sprachen

Es gibt viele Optionen für Tools, Sprachen und Entwicklungsumgebungen. Achten Sie auf Ihre Anforderungen und Prioritäten.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Welche Sprachen bevorzugen Sie für die Analyse?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Welche Tools sollen Sie für die Datenanalyse verwendet werden?

* [Microsoft Azure PowerShell](/powershell/azure/): eine Skriptsprache zum Verwalten von Azure-Ressourcen
* [Azure Machine Learning Studio](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
* [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools for Visual Studio](/visualstudio/python/)
* [Anaconda](https://www.anaconda.com/)
* [Jupyter-Notebooks](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Bestimmen des Szenarios für die erweiterte Analyse

Nachdem Sie die Fragen im vorherigen Abschnitt beantwortet haben, können Sie bestimmen, welches Szenario für Ihren Fall am besten geeignet ist. Die Beispielszenarien werden unter [Szenarien für die erweiterte Analyse in Azure Machine Learning](plan-sample-scenarios.md)erläutert.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Was ist der Team Data Science-Prozess (TDSP)?](overview.md)