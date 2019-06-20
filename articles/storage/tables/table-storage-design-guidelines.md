---
title: Richtlinien für den Entwurf von Azure Storage-Tabellen | Microsoft Docs
description: Entwerfen Sie Ihren Azure-Tabellendienst, um Lesevorgänge effizient zu unterstützen.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61269833"
---
# <a name="guidelines-for-table-design"></a>Richtlinien für den Entwurf von Tabellen

Das Entwerfen von Tabellen für die Verwendung mit dem Azure Storage-Tabellenspeicherdienst unterscheidet sich grundlegend von dem Entwerfen einer relationalen Datenbank. In diesem Artikel werden Richtlinien beschrieben, die Sie beim Entwerfen einer Tabellenspeicherdienstlösung mit effizienten Lese- und Schreibvorgängen unterstützen.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Entwerfen Ihrer Tabellenspeicherdienstlösung für größtmögliche Effizienz von Lesevorgängen

* ***Legen Sie den Entwurf für Abfragen in leseintensiven Anwendungen aus.*** Bedenken Sie beim Entwerfen von Tabellen die Abfragen (insbesondere Abfragen mit sensiblem Latenzverhalten), die Sie ausführen werden, bevor Sie überlegen, wie die Entitäten aktualisiert werden sollen. Dies führt in der Regel zu einer effizienten und leistungsstarken Lösung.  
* ***Geben Sie „PartitionKey“ und „RowKey“ in den Abfragen an.*** *Punktabfragen* wie diese sind die effizientesten Tabellenspeicherdienst-Abfragen.  
* ***Erwägen Sie die Speicherung duplizierter Kopien von Entitäten.*** Tabellenspeicher ist günstig. Erwägen Sie daher, ein und dieselbe Entität mehrfach zu speichern (mit verschiedenen Schlüsseln), um effizientere Abfragen zu ermöglichen.  
* ***Erwägen Sie das Denormalisieren der Daten.*** Tabellenspeicher ist günstig. Erwägen Sie daher, Ihre Daten zu denormalisieren. Speichern Sie z. B. zusammengefasste Entitäten, damit Abfragen für zusammengefasste Daten nur auf eine einzelne Entität zugreifen müssen.  
* ***Verwenden Sie Verbundschlüsselwerte.*** **PartitionKey** und **RowKey** sind die einzigen Schlüssel, über die Sie verfügen. Verwenden Sie z. B. Verbundschlüsselwerte, um alternative Schlüsselzugriffspfade für Entitäten zu aktivieren.  
* ***Verwenden Sie die Abfrageprojektion.*** Sie können die Datenmenge reduzieren, die Sie über das Netzwerk übertragen, indem Sie Abfragen verwenden, die nur die benötigten Felder auswählen.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Entwerfen Ihrer Tabellenspeicherdienstlösung für größtmögliche Effizienz von Schreibvorgängen  

* ***Erstellen Sie keine Hot-Partitionen.*** Wählen Sie Schlüssel aus, die es Ihnen ermöglichen, Ihre Anforderungen zu jedem Zeitpunkt auf mehrere Partitionen zu verteilen.  
* ***Vermeiden Sie Lastspitzen.*** Sorgen Sie für einen reibungslosen Datenverkehr über einen angemessenen Zeitraum und vermeiden Sie Lastspitzen.
* ***Erstellen Sie nicht unbedingt eine separate Tabelle für jeden Entitätstyp.*** Wenn atomische Transaktionen über Entitätstypen hinweg erforderlich sind, können Sie diese verschiedenen Entitätstypen in derselben Tabelle in derselben Partition speichern.
* ***Bedenken Sie den maximalen Durchsatz, der erzielt werden muss.*** Sie müssen die Skalierbarkeitsziele für den Tabellenspeicherdienst berücksichtigen und sicherstellen, dass diese durch Ihren Entwurf nicht überschritten werden.  

Beim Durchlesen dieses Handbuch finden Sie Beispiele für die Umsetzung dieser Grundlagen in die Praxis. 

## <a name="next-steps"></a>Nächste Schritte

- [Entwurfsmuster für die Tabelle](table-storage-design-patterns.md)
- [Entwurf für Abfragen](table-storage-design-for-query.md)
- [Verschlüsseln von Tabellendaten](table-storage-design-encrypt-data.md)
- [Entwurf für die Datenänderung](table-storage-design-for-modification.md)
