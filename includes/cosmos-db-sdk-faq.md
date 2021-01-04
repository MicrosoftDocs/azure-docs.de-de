---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/29/2020
ms.locfileid: "90068733"
---
**Wie werde ich über die Einstellung eines SDK benachrichtigt?**

Um einen reibungslosen Übergang zu einem unterstützten SDK zu ermöglichen, informiert Microsoft 12 Monate vor Ende des Supports über die Einstellung eines SDK. Sie werden über verschiedene Kommunikationskanäle benachrichtigt: Azure-Portal, Azure-Updates und direkte Kommunikation mit den entsprechenden Dienstadministratoren.

**Kann ich mit einem Azure Cosmos DB SDK, das eingestellt werden soll, während der 12-monatigen Frist Anwendungen erstellen?** 

Ja, mit einem solchen Azure Cosmos DB SDK können Sie während der 12-monatigen Frist Anwendungen erstellen, bereitstellen und ändern. Es wird empfohlen, nach Bedarf innerhalb der 12-monatigen Frist zu einer neueren unterstützten Version des Azure Cosmos DB SDK zu migrieren. 

**Was geschieht nach dem Einstellungsdatum mit Anwendungen, die das nicht unterstützte Azure Cosmos DB SDK verwenden?** 

Nach dem Einstellungsdatum werden von Azure Cosmos DB für die eingestellten SDK-Versionen keine Fehlerbehebungen mehr durchgeführt und keine neuen Funktionen hinzugefügt, und es wird auch kein Support mehr dafür angeboten. Wenn Sie kein Upgrade durchführen möchten, werden von den eingestellten Versionen des SDK gesendete Anforderungen weiterhin vom Azure Cosmos DB-Dienst bedient. 

**Welche SDK-Versionen werden über die neuesten Funktionen und Updates verfügen?**

Neue Funktionen und Updates werden nur der aktuellen Nebenversion der neuesten unterstützten SDK-Hauptversion hinzugefügt. Es wird empfohlen, immer die aktuelle Version zu verwenden, um von neuen Funktionen, Leistungsverbesserungen und Fehlerbehebungen zu profitieren. Wenn Sie eine alte, noch nicht eingestellte Version des SDK verwenden, funktionieren Ihre Anforderungen an Azure Cosmos DB weiterhin, Sie haben jedoch keinen Zugriff auf neue Funktionen.  

**Was muss ich tun, wenn ich meine Anwendung nicht vor einem Stichtag aktualisieren kann?**

Es wird empfohlen, so früh wie möglich auf das neueste SDK zu aktualisieren. Nachdem ein SDK für die Einstellung markiert wurde, bleiben Ihnen 12 Monate zur Aktualisierung Ihrer Anwendung. Wenn Sie die Aktualisierung nicht bis zum Einstellungsdatum vornehmen können, werden von den eingestellten Versionen des SDK gesendete Anforderungen weiterhin von Azure Cosmos DB verarbeitet, sodass Ihre ausgeführten Anwendungen weiterhin funktionieren. Für die eingestellten SDK-Versionen werden jedoch von Azure Cosmos DB keine Fehlerbehebungen mehr durchgeführt und keine neuen Funktionen hinzugefügt, und es wird auch kein Support mehr dafür angeboten. 

Wenn Sie über einen Supportplan verfügen und technischen Support benötigen, [kontaktieren Sie uns](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), indem Sie ein Supportticket erstellen.
    


