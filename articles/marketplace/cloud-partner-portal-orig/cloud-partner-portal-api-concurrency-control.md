---
title: Gleichzeitigkeitssteuerung | Azure Marketplace
description: Strategien für die Gleichzeitigkeitssteuerung der Veröffentlichungs-APIs des Cloud-Partnerportals.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 8cdcfd84a2f3bd4f920b97392255237db173cbf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935588"
---
# <a name="concurrency-control"></a>Gleichzeitigkeitssteuerung

In jedem Aufruf einer Veröffentlichungs-API des Cloud-Partnerportals muss explizit angegeben sein, welche Strategie für die Gleichzeitigkeitssteuerung verwendet werden soll. Fehler beim Bereitstellen des **If-Match**-Headers führen zu einer HTTP 400-Fehlerantwort. Es gibt zwei Strategien für die Gleichzeitigkeitssteuerung.

-   **Optimistisch**: Der Client, der die Aktualisierung ausführt, prüft, ob die Daten geändert wurden, seitdem er sie letztmalig gelesen hat.
-   **Letzter gewinnt**: Der Client aktualisiert die Daten sofort, unabhängig davon, ob eine andere Anwendung sie seit dem letzten Lesevorgang geändert hat.

<a name="optimistic-concurrency-workflow"></a>Workflow für optimistische Gleichzeitigkeit
-------------------------------

Es empfiehlt sich, die optimistische Gleichzeitigkeitsstrategie mit dem folgenden Workflow zu verwenden, damit sichergestellt ist, dass keine unerwarteten Änderungen an Ihren Ressourcen vorgenommen werden.

1.  Rufen Sie eine Entität mit den APIs ab. Die Antwort enthält einen ETag-Wert, der die aktuell gespeicherte Version der Entität (zum Zeitpunkt der Antwort) kennzeichnet.
2.  Fügen Sie diesen ETag-Wert zum Zeitpunkt der Aktualisierung in den obligatorischen **If-Match**-Anforderungsheader ein.
3.  Die API vergleicht in einer atomischen Transaktion den ETag-Wert in der Anforderung mit dem aktuellen ETag-Wert der Entität.
    *   Sind die ETag-Werte unterschiedlich, gibt die API eine `412 Precondition Failed`-HTTP-Antwort zurück. Dieser Fehler kennzeichnet, dass entweder ein anderer Prozess die Entität aktualisiert hat, seit der Client sie zuletzt abgerufen hat, oder dass der in der Anforderung angegebene ETag-Wert falsch ist.
    *  Sind die ETag-Werte gleich oder enthält der **If-Match**-Header das Sternchen-Platzhalterzeichen (`*`), führt die API den angeforderten Vorgang aus. Der API-Vorgang aktualisiert auch den gespeicherten ETag-Wert der Entität.


> [!NOTE]
> Ist das Platzhalterzeichen (*) im **If-Match**-Header angegeben, verwendet die API die Gleichzeitigkeitsstrategie „Letzter gewinnt“. In diesem Fall erfolgt kein ETag-Vergleich, und die Ressource wird ohne jegliche Prüfungen aktualisiert. 
