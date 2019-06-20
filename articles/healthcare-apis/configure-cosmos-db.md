---
title: Konfigurieren von Cosmos DB-Einstellungen in der Azure API for FHIR
description: In diesem Artikel wird beschrieben, wie Sie Cosmos DB-Einstellungen in Azure API for FHIR konfigurieren.
author: matjazl
ms.service: healthcare-apis
ms.topic: reference
ms.date: 05/22/2019
ms.author: matjazl
ms.openlocfilehash: 3d4f2ade64c55c757634e9c2b68aa9dcef51f117
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299596"
---
# <a name="configure-cosmos-db-settings"></a>Konfigurieren von Cosmos DB-Einstellungen 

Azure API for FHIR verwendet Cosmos DB als zugrunde liegende Datenbank zum Speichern von FHIR-Daten. Wenn Sie einen neuen Azure API for FHIR-Dienst bereitstellen, können Sie die Anzahl von Anforderungseinheiten (Request Units, RUs) für Cosmos DB angeben, die vom Dienst verwendet werden.

Weitere Informationen zu Cosmos DB- Anforderungseinheiten finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/request-units).

Um diese Einstellung im Azure-Portal zu ändern, navigieren Sie zu Ihrer Azure API for FHIR-Instanz, und öffnen Sie das Blatt „Cosmos DB“. Dort können Sie den Durchsatz je nach Leistungsanforderungen zum gewünschten Wert ändern. Sie können den Wert maximal auf 10.000 RU/s* festlegen. Wenn Sie einen höheren Wert benötigen, wenden Sie sich an den Azure-Support.

***Hinweis:** Ein höherer Wert bedeutet einen höheren Azure Cosmos DB-Durchsatz und höhere Kosten für den Dienst.

![](media/cosmosdb/cosmosdb-config.png)
