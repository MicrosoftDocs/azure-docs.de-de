---
title: Behandeln von Azure Cosmos DB-Ausnahmen des Typs „Unzulässig“
description: Erfahren Sie mehr über die Diagnose und Behebung der Ausnahme „Unzulässig“.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99971891"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnostizieren und Behandeln von Azure Cosmos DB-Ausnahmen des Typs „Unzulässig“
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Der HTTP-Statuscode 403 bedeutet, dass die Anforderung unzulässig ist und nicht abgeschlossen werden kann.

## <a name="firewall-blocking-requests"></a>Durch Firewall blockierte Anforderungen
Bei diesem Szenario tauchen häufig Fehler wie die folgenden auf:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Lösung
Vergewissern Sie sich, dass Ihre aktuellen [Firewalleinstellungen](how-to-configure-firewall.md) ordnungsgemäß sind und die IP-Adressen oder Netzwerke einschließen, über die Sie versuchen, eine Verbindung herzustellen.
Wenn Sie sie vor Kurzem aktualisiert haben, bedenken Sie, dass das Übernehmen von Änderungen **bis zu 15 Minuten** dauern kann.

## <a name="next-steps"></a>Nächste Schritte
* Konfigurieren der [IP-Firewall](how-to-configure-firewall.md)
* Konfigurieren des Zugriffs über [virtuelle Netzwerke](how-to-configure-vnet-service-endpoint.md)
* Konfigurieren des Zugriffs über [private Endpunkte](how-to-configure-private-endpoints.md)
