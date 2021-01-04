---
title: Data Factory-Anwendungsfall – Produktempfehlungen
description: Informationen zu einem Anwendungsfall, der mithilfe von Azure Data Factory zusammen mit anderen Diensten implementiert wurde.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: fc1e0398f98dc72d57039530dce9751af012f154
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495292"
---
# <a name="use-case---product-recommendations"></a>Anwendungsfall – Produktempfehlungen
Azure Data Factory ist einer der zahlreichen Dienste, die zum Implementieren der Cortana Intelligence Suite von Lösungsoptimierungen verwendet werden.  Auf der Seite [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) finden Sie Details zu dieser Suite. In diesem Dokument wird ein allgemeiner Anwendungsfall beschrieben, den Azure-Benutzer bereits mithilfe von Azure Data Factory und anderen Cortana Intelligence-Komponentendiensten gelöst und implementiert haben.

## <a name="scenario"></a>Szenario
Onlinehändler möchten ihre Kunden zum Kauf von Produkten verleiten, indem sie ihnen Produkte präsentieren, an denen sie am wahrscheinlichsten interessiert sind und die sie daher am wahrscheinlichsten kaufen werden. Hierzu müssen Onlinehändler die Onlineerfahrung der einzelnen Benutzer mithilfe personalisierter Produktempfehlungen anpassen. Diese personalisierten Produktempfehlungen basieren auf aktuellen und historischen Daten zum Kaufverhalten, auf Produktinformationen, auf neu eingeführten Marken sowie auf Produkt- und Kundensegmentierungsdaten.  Darüber hinaus können dem Benutzer Produktempfehlungen auf der Grundlage der Analyse des kombinierten Gesamtverhaltens aller Benutzer gezeigt werden.

Ziel dieser Händler ist es, mehr Interessenten in Käufer umzuwandeln, um einen höheren Umsatzerlös zu generieren.  Diese Conversion wird durch die Bereitstellung kontextbezogener, verhaltensbasierter Produktempfehlungen auf der Grundlage von Kundeninteressen und Aktionen erreicht. Für diesen Anwendungsfall verwenden wir Onlinehändler als Beispiel für Unternehmen, die die Produktpräsentation für ihre Kunden optimieren möchten. Diese Prinzipien gelten jedoch für alle Unternehmen, die ihre Kunden an ihre Waren und Dienstleistungen binden und deren Kauferfahrung mit personalisierten Produktempfehlungen verbessern möchten.

## <a name="challenges"></a>Herausforderungen
Es gibt viele Herausforderungen, mit denen Onlinehänder bei dem Versuch konfrontiert sind, diese Art von Anwendungsfall zu implementieren. 

Zunächst einmal müssen verschiedenste Daten aus mehreren (lokalen und cloudbasierten) Datenquellen erfasst werden. Hierzu zählen Produktdaten, historische Daten zum Kundenverhalten und Benutzerdaten, die während der Navigation des Benutzers auf der Website des Onlinehändlers gesammelt werden. 

Zweitens müssen personalisierte Produktempfehlungen angemessen und präzise vorausberechnet werden. Neben Produkt, Marke, Kundenverhalten und Suchdaten müssen Onlinehändler bei der Bestimmung der optimalen Produktempfehlungen für den jeweiligen Benutzer auch Kundenfeedback zu vorherigen Produktkäufen berücksichtigen. 

Drittens müssen die Empfehlungen dem Kunden sofort zur Verfügung stehen, um ihm eine nahtlose Such- und Kauferfahrung sowie die aktuellesten und relevantesten Empfehlungen zu bieten. 

Drittens müssen Onlinehändler die Effektivität des Vorgehens ermitteln, indem sie den Gesamterfolg von Up-Selling und Cross-Selling beim Verkauf nachverfolgen und ihre künftigen Empfehlungen entsprechend anpassen.

## <a name="solution-overview"></a>Übersicht über die Lösungen
Dieser Beispielanwendungsfall wurde von realen Azure-Benutzern mit Azure Data Factory und anderen Cortana Intelligence-Komponentendiensten gelöst und implementiert, darunter [HDInsight](https://azure.microsoft.com/services/hdinsight/) und [Power BI](https://powerbi.microsoft.com/).

Der Onlinehändler verwendet einen Azure-Blobspeicher, einen lokalen SQL Server, Azure SQL-Datenbank und einen relationalen Datamart als Datenspeicheroptionen im gesamten Workflow.  Der Blobspeicher enthält Kundeninformationen, Daten zum Kundenverhalten und Produktdaten. Die Produktdaten enthalten Informationen zur Produktmarke und einen Produktkatalog, der lokal in Azure Synapse Analytics gespeichert ist. 

Alle Daten werden kombiniert und einem Produktempfehlungssystem hinzugefügt, um personalisierte Empfehlungen auf der Grundlage der Interessen und Aktionen der Kunden zu präsentieren, während der Benutzer die Produkte im Katalog auf der Website durchsucht. Auf der Grundlage allgemeiner, nicht benutzerspezifischer Website-Nutzungsmuster werden den Kunden auch Produkte angezeigt, die mit dem betrachteten Produkt in Verbindung stehen.

![Anwendungsfalldiagramm](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes an unformatierten Webprotokolldateien werden tagtäglich von der Website des Onlinehändlers als teilstrukturierte Daten erzeugt. Die unformatierten Webprotokolldateien sowie die Kunden- und Produktkataloginformationen werden regelmäßig in einer Azure Blob Storage-Instanz erfasst. Dazu wird die global als Dienst bereitgestellte Datenverschiebung von Data Factory verwendet. Die unformatierten Protokolldateien für den jeweiligen Tag werden im Blobspeicher zur langfristigen Speicherung nach Jahr und Monat partitioniert.  Zum Partitionieren der unformatierten Protokolldateien im Blobspeicher sowie zum bedarfsgerechten Verarbeiten der erfassten Protokolle mithilfe von Hive- und Pig-Skripts wird [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) verwendet. Die partitionierten Webprotokolldaten werden anschließend verarbeitet, um die erforderlichen Eingaben für ein durch maschinelles Lernen gestütztes Empfehlungssystem zu extrahieren, das zum Erzeugen der personalisierten Produktempfehlungen dient.

Das durch maschinelles Lernen gestützte Empfehlungssystem in diesem Beispiel ist eine Open-Source-Empfehlungsplattform mit maschinellem Lernen von [Apache Mahout](https://mahout.apache.org/).  In diesem Szenario kann ein beliebiges, auf [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) basierendes oder benutzerdefiniertes Modell verwendet werden.  Das Mahout-Modell dient zum Prognostizieren der Ähnlichkeit zwischen Artikeln auf der Website anhand allgemeiner Nutzungsmuster sowie zum Generieren personalisierter Empfehlungen auf der Grundlage des jeweiligen Benutzers.

Schließlich wird das Resultset der personalisierten Produktempfehlungen in einen relationalen Datamart zur Nutzung auf der Website des Onlinehändlers verschoben.  Auf das Resultset im Blobspeicher können andere Anwendungen auch direkt zugreifen. Es kann auch für andere Nutzer und Anwendungsfälle in weitere Datenspeicher verschoben werden.

## <a name="benefits"></a>Vorteile
Durch Optimieren ihrer Produktempfehlungsstrategie und deren Ausrichtung auf Unternehmensziele erfüllt die Lösung die Verkaufsförderungs- und Marketingziele des Onlinehändlers. Darüber hinaus kann das Unternehmen den Workflow für Produktempfehlungen nun effizient, zuverlässig und wirtschaftlich betreiben und verwalten. Das Modell kann mühelos aktualisiert und seine Effektivität basierend auf den Kennzahlen für die Kundengewinnung und -bindung optimiert werden. Mithilfe von Azure Data Factory konnte der Onlinehändler von seiner zeitaufwändigen und kostspieligen manuellen Cloudressourcenverwaltung zu einer bedarfsgesteuerten Lösung wechseln. Dadurch konnte er nicht nur Zeit und Geld sparen, sondern auch die Zeit bis zur Lösungsbereitstellung verkürzen. Ansichten der Datenherkunft und Integrität von Diensten lassen sich dank der intuitiven Data Factory-Benutzeroberfläche für Überwachung und Verwaltung im Azure-Portal einfach visualisieren. Die Lösung kann nun so geplant und verwaltet werden, dass zuverlässig relevante Daten erzeugt und den Benutzern bereitgestellt werden. Zudem werden Daten- und Verarbeitungsabhängigkeiten automatisch verwaltet.

Durch dieses personalisierte Einkaufserlebnis hat der Onlinehändler eine wettbewerbsfähigere, ansprechende Kundenerfahrung geschaffen, die zu mehr Umsatz und höherer Kundenzufriedenheit führt.

