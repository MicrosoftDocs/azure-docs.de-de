---
title: Skalieren des Durchsatzes in Azure Cosmos DB
description: Dieser Artikel beschreibt, wie Azure Cosmos DB den Durchsatz elastisch skaliert.
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: f930b5c478cc880952b4559be4c6647b260efcf2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243491"
---
# <a name="globally-scale-provisioned-throughput"></a>Globales Skalieren von bereitgestelltem Durchsatz 

In Azure Cosmos DB wird der bereitgestellte Durchsatz als Anforderungseinheit/Sekunde (RU/s oder die Pluralform RUs) dargestellt. Anforderungseinheiten messen die Kosten für Lese- und Schreibvorgänge für Ihre Cosmos-Container wie in der folgenden Abbildung dargestellt:

![Anforderungseinheiten](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Sie können Anforderungseinheiten für einen Cosmos-Container oder eine Cosmos-Datenbanken bereitstellen. In einem Container bereitgestellte Anforderungseinheiten stehen ausschließlich für die Vorgänge zur Verfügung, die in diesem Container ausgeführt werden. In einer Datenbank bereitgestellte Anforderungseinheiten werden für alle Container in dieser Datenbank (ausgenommen Container mit exklusiv zugewiesenen RUs) freigegeben.

Sie können jederzeit die bereitgestellte RU/s erhöhen oder verringern, um den bereitgestellten Durchsatz elastisch zu skalieren. Weitere Informationen finden Sie im Artikel zum [Bereitstellen von Durchsatz](set-throughput.md) und zur elastischen Skalierung von Cosmos-Containern und -Datenbanken. Um den Durchsatz global zu skalieren, können Sie Ihrem Cosmos-Konto jederzeit Regionen hinzufügen oder daraus entfernen. Weitere Informationen finden Sie unter [Verwalten von Datenbankkonten in Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Die Zuordnung mehrerer Regionen zu einem Cosmos-Konto ist in vielen Szenarien wichtig, um eine geringe Latenz und [Hochverfügbarkeit](high-availability.md) auf der ganzen Welt zu erreichen.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Verteilen des bereitgestellten Durchsatzes über mehrere Regionen

Wenn Sie *R* RUs in einem Cosmos-Container (oder einer Datenbank) bereitstellen, stellt Cosmos DB sicher, dass in *jeder* Region, die mit Ihrem Cosmos-Konto verknüpft ist, *R* RUs verfügbar sind. Jedes Mal, wenn Sie Ihrem Konto eine neue Region hinzufügen, stellt Cosmos DB automatisch *R* RUs in der neu hinzugefügten Region bereit. Die Vorgänge, die für Ihren Cosmos-Container ausgeführt werden, erhalten garantiert *R* RUs in jeder Region. Sie können RUs nicht selektiv einer bestimmten Region zuweisen. Die in einem Cosmos-Container (oder einer Datenbank) bereitgestellten RUs werden in allen mit Ihrem Cosmos-Konto verbundenen Regionen bereitgestellt.

Angenommen, ein Cosmos-Container ist mit *R* RUs konfiguriert und es gibt *N* Regionen, die dem Cosmos-Konto zugeordnet sind:

- Wenn das Cosmos-Konto mit einer einzigen Schreibregion konfiguriert ist, beträgt die Gesamtzahl von global im Container verfügbaren RUs: *R* x *N*.

- Wenn das Cosmos-Konto mit mehreren Schreibregionen konfiguriert ist, beträgt die Gesamtzahl von global im Container verfügbaren RUs: *R* x (*N*+1). Die zusätzlichen *R* RUs werden automatisch bereitgestellt, um Updatekonflikte und Anti-Entropie-Datenverkehr in den Regionen zu verarbeiten.

Die Wahl des [Konsistenzmodells](consistency-levels.md) wirkt sich auch auf den Durchsatz aus. Für die eher gelockerten Konsistenzebenen (z.B. *Sitzung*, *Präfixkonsistenz* und *letztliche Konsistenz*) können Sie den etwa 2-fachen Lesedurchsatz im Vergleich zu höheren Konsistenzebenen (z.B. *begrenzte Veraltung* oder *starke Konsistenz*) erzielen.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes erfahren Sie, wie Sie den Durchsatz für einen Container oder eine Datenbank konfigurieren:

* [Festlegen und Abrufen des Durchsatzes für Container und Datenbanken](set-throughput.md) 

