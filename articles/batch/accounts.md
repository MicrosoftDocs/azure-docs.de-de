---
title: Batch-Konten und Azure Storage-Konten
description: Hier erfahren Sie mehr über Azure Batch Konten und deren Verwendung in der Entwicklung.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 83108a265f91c9feef2fab424f1819939c2d58c9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896747"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Batch-Konten und Azure Storage-Konten

Ein Azure Batch-Konto ist eine eindeutig identifizierte Entität innerhalb des Batch-Diensts. Die meisten Batch-Lösungen verwenden [Azure Storage](../storage/index.yml) zum Speichern von Ressourcen- und Ausgabedateien, sodass jedes Batch-Konto normalerweise einem entsprechenden Speicherkonto zugeordnet ist.

## <a name="batch-accounts"></a>Batch-Konten

Die gesamte Verarbeitung und alle Ressourcen sind einem Batch-Konto zugeordnet. Wenn Ihre Anwendung eine Anforderung an den Batch-Dienst richtet, wird die Anforderung anhand des Azure Batch-Kontonamens, der URL des Kontos und entweder eines Zugriffsschlüssels oder eines Azure Active Directory-Tokens authentifiziert.

Sie können mehrere Batch-Workloads in einem einzelnen Batch-Konto ausführen. Sie können Ihre Workloads auch auf Batch-Konten verteilen, die sich in demselben Abonnement, aber verschiedenen Azure-Regionen befinden.

Ein Azure Batch-Konto lässt sich über das [Azure-Portal](batch-account-create-portal.md) oder programmgesteuert (beispielsweise mit der [Batch Management .NET-Bibliothek ](batch-management-dotnet.md)) erstellen. Bei der Kontoerstellung können Sie ein Azure-Speicherkonto zur Speicherung auftragsbezogener Eingabe- und Ausgabedaten oder Anwendungen zuordnen.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]


## <a name="azure-storage-accounts"></a>Azure Storage-Konten

Die meisten Batch-Lösungen verwenden Azure Storage zum Speichern von Ressourcen- und Ausgabedateien. In Ihren Batch-Tasks (einschließlich Standardtasks, Starttasks und Tasks zur Auftragsvorbereitung und -freigabe) werden beispielsweise in der Regel Ressourcendateien angegeben, die sich in einem Speicherkonto befinden. Speicherkonten speichern auch die Daten, die verarbeitet werden, sowie alle generierten Ausgabedaten.

Batch unterstützt die folgenden Arten von Azure Storage-Konten:

- Konten vom Typ „General Purpose v2“ (GPv2)
- Konten vom Typ „General Purpose v1“ (GPv1)
- BLOB-Speicherkonten (derzeit für Pools, in der VM-Konfiguration unterstützt)

Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](../storage/common/storage-account-overview.md).

Sie können ein Speicherkonto mit Ihrem Batch-Konto verknüpfen – entweder im Zuge der Batch-Kontoerstellung oder zu einem späteren Zeitpunkt. Berücksichtigen Sie bei der Wahl eines Speicherkontos Ihre Kosten- und Leistungsanforderungen. Die Optionen für GPv2 und Blobspeicherkonto unterstützen beispielsweise höhere [Kapazitäts- und Skalierbarkeitsgrenzwerte](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) als GPv1. (Über den Azure-Support kann eine Erhöhung des Speicherlimits angefordert werden.) Diese Kontooptionen können die Leistung von Batch-Lösungen mit einer großen Anzahl paralleler Aufgaben verbessern, die aus dem Speicherkonto lesen oder in das Speicherkonto schreiben.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über [Knoten und Pools](nodes-and-pools.md)
- Erfahren Sie, wie Sie Azure Batch-Konten über das [Azure-Portal](batch-account-create-portal.md) oder [Batch Management .NET](batch-management-dotnet.md) erstellen und verwalten können.
- Erfahren Sie mehr zum Verwenden [privater Endpunkte](private-connectivity.md) mit Azure Batch-Konten.
