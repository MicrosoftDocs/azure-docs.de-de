---
title: Tutorial zur globalen Verteilung von Azure Cosmos DB mit der Tabellen-API
description: Hier erfahren Sie, wie die globale Verteilung in Tabellen-API-Konten für Azure Cosmos DB funktioniert und wie Sie die bevorzugte Liste der Regionen konfigurieren.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: f06fbe482327d01917f648ccdec34baa15c46e80
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073909"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Einrichten der globalen Verteilung von Azure Cosmos DB mithilfe der Tabellen-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

In diesem Artikel werden die folgenden Aufgaben behandelt: 

> [!div class="checklist"]
> * Konfigurieren der globalen Verteilung mit dem Azure-Portal
> * Konfigurieren der globalen Verteilung mit der [Tabellen-API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Herstellen einer Verbindung mit einer bevorzugten Region mithilfe der Tabellen-API

Um die [globale Verteilung](distribute-data-globally.md) nutzen zu können, sollte von Clientanwendungen der aktuelle Standort angegeben werden, an dem die Anwendung ausgeführt wird. Hierfür wird die `CosmosExecutorConfiguration.CurrentRegion`-Eigenschaft festgelegt. Die `CurrentRegion`-Eigenschaft sollte nur einen Standort enthalten. Jede Clientinstanz kann ihre eigene Region angeben, um für Lesevorgänge eine geringe Latenz zu erzielen. Für die Benennung der Region muss jeweils der [Anzeigename](/previous-versions/azure/reference/gg441293(v=azure.100)), z. B. „USA, Westen“ verwendet werden. 

Das Azure Cosmos DB Table API SDK wählt je nach Kontokonfiguration und aktueller regionaler Verfügbarkeit automatisch den optimalen Endpunkt für die Kommunikation aus. Die nächstgelegene Region erhält Vorrang, um für Clients die Latenz zu verringern. Nachdem Sie die aktuelle `CurrentRegion`-Eigenschaft festgelegt haben, werden Lese- und Schreibanforderungen wie folgt weitergeleitet:

* **Leseanforderungen:** Alle Leseanforderungen werden an die konfigurierte aktuelle Region (`CurrentRegion`) gesendet. Basierend auf der jeweiligen Nähe wählt das SDK automatisch eine Fallbackregion mit Georeplikation aus, um für Hochverfügbarkeit zu sorgen.

* **Schreibanforderungen:** Das SDK sendet alle Schreibanforderungen automatisch an die aktuelle Schreibregion. Unter einem Konto mit Schreibvorgängen in mehreren Regionen werden die Schreibanforderungen auch von der aktuellen Region bereitgestellt. Basierend auf der jeweiligen Nähe wählt das SDK automatisch eine Fallbackregion mit Georeplikation aus, um für Hochverfügbarkeit zu sorgen.

Wenn Sie die `CurrentRegion`-Eigenschaft auch angeben, nutzt das SDK die aktuelle Schreibregion für alle Vorgänge.

Beispiel: Ein Azure Cosmos-Konto befindet sich in den Regionen „USA, Westen“ und „USA, Osten“. Angenommen, „USA, Westen“ ist die Schreibregion, und die Anwendung befindet sich in „USA, Osten“. Wenn die „CurrentRegion“-Eigenschaft nicht konfiguriert ist, werden alle Lese- und Schreibanforderungen immer an die Region „USA, Westen“ geleitet. Wenn die „CurrentRegion“-Eigenschaft konfiguriert ist, werden alle Leseanforderungen über die Region „USA, Osten“ bereitgestellt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Konfigurieren der globalen Verteilung mit dem Azure-Portal
> * Konfigurieren der globalen Verteilung mithilfe der Table-APIs von Azure Cosmos DB