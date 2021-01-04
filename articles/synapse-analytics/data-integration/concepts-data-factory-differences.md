---
title: Unterschiede zu Azure Data Factory
description: Erfahren Sie, wie sich die Datenintegrationsfunktionen von Azure Synapse Analytics von denen in Azure Data Factory unterscheiden.
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 10f5336dd4c8a02acf623b1b14226ca676006953
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357648"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Datenintegration in Azure Synapse Analytics im Vergleich zu Azure Data Factory

In Azure Synapse Analytics basieren die Datenintegrationsfunktionen wie Synapse-Pipelines und Datenflüsse auf denen von Azure Data Factory. Weitere Informationen finden Sie unter [Was ist Azure Data Factory?](../../data-factory/introduction.md). Fast alle Funktionen sind identisch oder ähnlich, und die Dokumentation wird von den beiden Diensten gemeinsam genutzt. In diesem Artikel werden die aktuellen Unterschiede zwischen Azure Data Factory und Azure Synapse hervorgehoben und identifiziert.

Überprüfen Sie den Moniker am Anfang des Artikels, um zu sehen, ob eine Azure Data Factory-Funktion oder ein diesbezüglicher Artikel für Azure Synapse gilt.

![„Gilt für“-Moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "„Gilt für“-Moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Funktionen in Azure Data Factory, die nicht für Azure Synapse geplant sind

Die folgenden Funktionen sind in Azure Data Factory verfügbar, sie sind aber nicht für Azure Synapse geplant.

* **Übertragen von SSIS-Paketen per Lift & Shift:** Sie haben in Azure Data Factory die Möglichkeit, SSIS-Pakete mithilfe der SSIS-Integration Runtime per Lift & Shift zu übertragen. Die SSIS-Integration Runtime und die Aktivität „SSIS-Paket ausführen“ sind in Synapse-Arbeitsbereichen nicht verfügbar. 
* **Gültigkeitsdauer:** Die Gültigkeitsdauer ist eine Einstellung in der Azure Integration Runtime, die dem Spark-Cluster ermöglicht, in Zuordnungsdatenflüssen für einen gewissen Zeitraum nach Abschluss eines Datenflusses aktiv (*warm*) zu bleiben. Dieses Feature ist in Synapse-Arbeitsbereichen nicht verfügbar.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>In Azure Data Factory nicht unterstützte Azure Synapse-Funktionen

Die folgenden Funktionen sind in Azure Synapse verfügbar, sie sind aber nicht für Azure Data Factory geplant.

* **Spark-Auftragsüberwachung von Zuordnungsdatenflüssen:** In Synapse ist die Spark-Engine im Abonnement des Benutzers enthalten, sodass Benutzer ausführliche Spark-Protokolle anzeigen können. In Azure Data Factory erfolgt die Auftragsausführung auf einem von Azure Data Factory verwalteten Spark-Cluster, und diese Informationen sind nicht verfügbar. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory-Funktionen, die sich in Synapse anders verhalten

Die folgenden Funktionen verhalten sich entweder anders oder sind zurzeit in Azure Synapse nicht vorhanden. 

* **Wranglingdatenflüsse:** Die Aktivität „Wranglingdatenfluss“ ist zurzeit nur in Azure Data Factory verfügbar.
* **Der Lösungsvorlagenkatalog:** In Azure Data Factory können Benutzer Pipelinevorlagen im Lösungsvorlagenkatalog suchen. In Synapse-Arbeitsbereichen enthält das Knowledge Center andere Vorlagen sowie zusätzliche Datasets und SQL-Skripts. 
* **Git-Integration und eine native CI/CD-Lösung:** Zurzeit kann ein Synapse-Arbeitsbereich keine Verbindung mit einem Git-Repository herstellen. Außerdem wird nicht derselbe Prozess für Continuous Integration und Continuous Delivery wie in Azure Data Factory ausgeführt.
* **Integration mit Azure Monitor:** Synapse-Arbeitsbereiche werden nicht wie Azure Data Factory mit Azure Monitor integriert.
* **Hybridkonfiguration für die Integration Runtime:** Ein Benutzer kann innerhalb eines Synapse-Arbeitsbereichs nicht sowohl über eine verwaltete VNET-IR als auch über eine Azure IR verfügen. Diese Funktionalität wird in Azure Data Factory unterstützt.
* **Integration Runtime-Freigabe:** Selbstgehostete Integration Runtimes können nicht zwischen Synapse-Arbeitsbereichen freigegeben werden. Diese Funktionalität wird in Azure Data Factory unterstützt.
* **Regionsübergreifende Integration Runtimes für Datenflüsse:** Datenflüsse können nicht in Integration Runtimes in anderen Regionen als ein Synapse-Arbeitsbereich ausgeführt werden. Diese Funktionalität wird in Azure Data Factory unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit der Datenintegration in Ihrem Synapse-Arbeitsbereich, indem Sie lernen, wie Sie [Daten in einem Azure Data Lake Storage Gen2-Konto erfassen](data-integration-data-lake.md).
