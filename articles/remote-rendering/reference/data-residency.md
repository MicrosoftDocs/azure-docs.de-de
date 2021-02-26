---
title: Datenresidenz
description: Beschreibt die Datenresidenz bei Verwendung von Azure Remote Rendering.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577454"
---
# <a name="azure-remote-rendering-data-residency"></a>Azure Remote Rendering-Datenresidenz 
In diesem Artikel wird das Konzept der Datenresidenz und dessen Anwendung auf Azure Remote Rendering erläutert. 

## <a name="data-residency"></a>Datenresidenz 
Azure Remote Rendering verwendet vom Benutzer bereitgestellte Azure-Blobcontainer für die Modellspeicherung. Wenn das Modell nicht verwendet wird, verbleibt es in der vom Benutzer bereitgestellten Azure Blob Storage-Region. Wenn das Modell zum Rendern verwendet wird, werden die Daten in den Bereich kopiert, den der Benutzer beim Starten der Renderingsitzung auswählt.

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Einrichten eines Azure Blob Storage-Containers für Azure Remote Rendering finden Sie unter [Konvertieren eines Modells für das Rendering](../quickstarts/convert-model.md).
