---
title: include file
description: include file
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 331886f01345aba576cd8f96f95077f9bbdae704
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754324"
---
Sie können nun mithilfe des Daten-Explorer-Tools im Azure-Portal eine Datenbank und einen Container erstellen. 

1. Klicken Sie auf **Daten-Explorer** > **Neuer Container**. 
    
    Der Bereich **Container hinzufügen** wird ganz rechts angezeigt. Möglicherweise müssen Sie nach rechts scrollen, damit Sie ihn sehen.

    ![Daten-Explorer im Azure-Portal, Blatt „Container hinzufügen“](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. Geben Sie auf der Seite **Container hinzufügen** die Einstellungen für den neuen Container ein.

    |Einstellung|Empfohlener Wert|BESCHREIBUNG
    |---|---|---|
    |**Datenbank-ID**|Aufgaben|Geben Sie *Tasks* als Namen für die neue Datenbank ein. Datenbanknamen müssen zwischen 1 und 255 Zeichen lang sein und dürfen weder /, \\, #, ? noch nachgestellte Leerzeichen enthalten. Aktivieren Sie die Option **Provision database throughput** (Datenbankdurchsatz bereitstellen). Diese Option ermöglicht es Ihnen, den für die Datenbank bereitgestellten Durchsatz auf alle Container in der Datenbank zu verteilen. Darüber hinaus hilft sie Ihnen dabei, Kosten zu sparen. |
    |**Durchsatz**|400|Belassen Sie den Durchsatz bei 400 Anforderungseinheiten pro Sekunde (RU/s). Sie können den Durchsatz später zentral hochskalieren, wenn Sie Wartezeiten reduzieren möchten.| 
    |**Container-ID**|Items|Geben Sie *Items* als Namen für den neuen Container ein. Für Container-IDs gelten dieselben Zeichenanforderungen wie für Datenbanknamen.|
    |**Partitionsschlüssel**| /category| Das in diesem Artikel beschriebene Beispiel verwendet */category* als Partitionsschlüssel. Indem Sie einen Partitionsschlüssel festlegen, kann Azure Cosmos DB Ihre Sammlung skalieren, um die Speicher- und Durchsatzanforderungen Ihrer Anwendung zu erfüllen. Im Allgemeinen wird ein Partitionsschlüssel empfohlen, der eine Vielzahl unterschiedlicher Werte umfasst und eine gleichmäßige Verteilung des Speicher- und Anforderungsvolumens in Ihrer Workload bewirkt. [Weitere Informationen zur Partitionierung](../articles/cosmos-db/partitioning-overview.md)|
    
    Zusätzlich zu den zuvor beschriebenen Einstellungen können Sie optional auch **eindeutige Schlüssel** für den Container hinzufügen. In diesem Beispiel lassen wir das Feld leer. Eindeutige Schlüssel bieten Entwicklern die Möglichkeit, ihrer Datenbank eine zusätzliche Datenintegritätsebene hinzuzufügen. Durch das Erstellen einer Richtlinie für eindeutige Schlüssel beim Erstellen eines Containers wird die Eindeutigkeit von einem oder mehreren Werten pro Partitionsschlüssel gewährleistet. Weitere Informationen finden Sie im Artikel [Eindeutige Schlüssel in Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Klicken Sie auf **OK**. Im Daten-Explorer werden die neue Datenbank und der neue Container angezeigt.

