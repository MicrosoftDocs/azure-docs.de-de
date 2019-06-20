---
title: include file
description: include file
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178470"
---
Sie können mehrere Dienste in einem Abonnement erstellen. Jeder kann für einen bestimmten Tarif bereitgestellt werden. Sie sind nur durch die Anzahl der Dienste beschränkt, die in einem Tarif zulässig sind. Beispielsweise können Sie in einem Abonnement bis zu 12 Dienste für den Basic-Tarif und weitere 12 Dienste für den S1-Tarif erstellen. Weitere Informationen zu Tarifen finden Sie unter [Auswählen einer SKU oder eines Tarifs für Azure Search](../articles/search/search-sku-tier.md).

Die Obergrenze für die maximale Anzahl von Diensten kann auf Anfrage angehoben werden. Wenden Sie sich an den Support von Azure, wenn Sie in einem Abonnement weitere Dienste benötigen.

| Resource            | Free<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximale Anzahl von Diensten    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximales Skalieren in Sucheinheiten (Search Units, SU)<sup>2</sup> |– |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> „Free“ basiert auf gemeinsamen (nicht dedizierten) Ressourcen. Zentrales Hochskalieren wird für gemeinsame Ressourcen nicht unterstützt.

<sup>2</sup> Sucheinheiten sind Abrechnungseinheiten, die entweder als *Replikat* oder *Partition* zugeordnet werden. Sie benötigen beide Ressourcen für Speicher-, Indizierungs- und Abfragevorgänge. Weitere Informationen zu SU-Berechnungen finden Sie unter [Skalieren von Ressourcenebenen für Abfrage und Indizierung von Arbeitslasten in Azure Search](../articles/search/search-capacity-planning.md). 