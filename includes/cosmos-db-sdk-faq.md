---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177842"
---
**1. Wie werden Kunden über die Deaktivierung eines SDK benachrichtigt?**

Bei Deaktivierung eines SDK benachrichtigt Microsoft seine Kunden 12 Monate vor Beendigung der Unterstützung, um einen reibungslosen Übergang zu einem unterstützten SDK sicherzustellen. Kunden werden über verschiedene Kommunikationskanäle benachrichtigt: Azure-Verwaltungsportal, Developer Center, Blogbeitrag und direkter Austausch mit den Administratoren der entsprechenden Dienste.

**2. Können Kunden mit einem Azure Cosmos DB SDK, das deaktiviert werden soll, während des 12-monatigen Zeitraums Anwendungen entwickeln?** 

Ja, mit einem solchen Azure Cosmos DB SDK können Kunden während der 12-monatigen Frist Anwendungen bei vollem Zugriff erstellen, bereitstellen und ändern. Während der 12-monatigen Frist wird Kunden empfohlen, ggf. zu einer neueren unterstützten Version des Azure Cosmos DB SDK zu wechseln.

**3. Können Kunden mit einem deaktivierten Azure Cosmos DB SDK nach Ablauf des 12-monatigen Benachrichtigungszeitraums Anwendungen erstellen und ändern?**

Das SDK wird nach Ablauf des 12-monatigen Benachrichtigungszeitraums deaktiviert. Der Zugriff auf Azure Cosmos DB durch eine Anwendung mithilfe eines deaktivierten SDK wird von der Azure Cosmos DB-Plattform nicht zugelassen. Darüber hinaus bietet Microsoft keinen Kundensupport für das deaktivierte SDK.

**4. Was geschieht mit Kunden, die Anwendungen ausführen, für die eine nicht unterstützte Version des Azure Cosmos DB SDK verwendet wird?**

Alle Versuche zum Herstellen einer Verbindung mit dem Azure Cosmos DB-Dienst mit einer deaktivierten SDK-Version werden abgelehnt. 

**5. Werden neue Features und Funktionen auf alle noch aktivierten SDKs angewendet?**

Neue Features und Funktionen werden nur neuen Versionen hinzugefügt. Wenn Sie eine alte, noch aktivierte Version des SDK verwenden, funktionieren Ihre Anforderungen an Azure Cosmos DB weiter wie bisher, aber Sie haben keinen Zugriff auf neue Funktionen.  

**6. Was muss ich tun, wenn ich meine Anwendung nicht vor einem Stichtag aktualisieren kann?**

Es wird empfohlen, so früh wie möglich auf das neueste SDK zu aktualisieren. Sobald ein SDK für die Deaktivierung markiert wurde, bleiben Ihnen 12 Monate zur Aktualisierung Ihrer Anwendung. Wenn Sie aus einem beliebigen Grund die Aktualisierung der Anwendung in diesem Zeitraum nicht schaffen, kontaktieren Sie das [Cosmos DB-Team](mailto:askcosmosdb@microsoft.com), und bitten Sie vor dem Stichtag um Hilfe.

