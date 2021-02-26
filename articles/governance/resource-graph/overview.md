---
title: Übersicht über Azure Resource Graph
description: Hier wird erläutert, wie der Azure Resource Graph-Dienst das komplexe bedarfsabhängige Abfragen von Ressourcen für Abonnements und Mandanten ermöglicht.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: b5df124d07b8ecfb20f5dec08830d8156e8df2cd
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919140"
---
# <a name="what-is-azure-resource-graph"></a>Was ist Azure Resource Graph?

Azure Resource Graph ist ein Dienst in Azure, der die Azure-Ressourcenverwaltung mithilfe effizienter und leistungsstarker Ressourcenuntersuchung befähigen soll, übergreifend für eine bestimmte Menge von Abonnements nach Bedarf Abfragen durchzuführen, sodass Sie Ihre Umgebung effektiv beherrschen können. Diese Abfragen verfügen über die folgenden Features:

- Fähigkeit zum Abfragen von Ressourcen mit komplexem Filtern, Gruppieren und Sortieren nach Eigenschaften der Ressource.
- Fähigkeit zum iterativen Untersuchen von Ressourcen, basierend auf Governanceanforderungen.
- Möglichkeit zum Bewerten der Auswirkungen der Anwendung von Richtlinien in einer großen Cloudumgebung.
- Möglichkeit zum [Beschreiben von Änderungen der Ressourceneigenschaften](./how-to/get-resource-changes.md) (Vorschau).

In dieser Dokumentation wird jedes Feature ausführlich beschrieben.

> [!NOTE]
> Azure Resource Graph hebt mit der neuen Oberfläche zum Durchsuchen aller Ressourcen und dem [Änderungsverlauf](../policy/how-to/determine-non-compliance.md#change-history) mit 
> _visuellem Diff_ von Azure Policy die Suchleiste des Azure-Portals auf eine neue Höhe. Sie dient zur Unterstützung von Kunden, die umfangreiche Umgebungen verwalten.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Wie Resource Graph Azure Resource Manager ergänzt

Resource Manager unterstützt derzeit aktuelle Abfragen von einfachen Ressourcenfeldern, insbesondere Ressourcenname, ID, Typ, Ressourcengruppe, Abonnement und Speicherort. Resource Manager bietet darüber hinaus Funktionen zum Aufrufen einzelner Ressourcenanbieter, um jeweils für eine Ressource auf die Detaileigenschaften zuzugreifen.

Mit Azure Resource Graph können Sie auf diese Eigenschaften zugreifen, die die Ressourcenanbieter zurückgeben, ohne jeden Ressourcenanbieter einzeln anrufen zu müssen. Eine Liste der unterstützten Ressourcentypen finden Sie in der [Tabelle und der Referenz für Ressourcentypen](./reference/supported-tables-resources.md). Alternativ können Sie die unterstützten Ressourcentypen über den [Schemabrowser im Azure Resource Graph-Explorer](./first-query-portal.md#schema-browser) anzeigen.

Mit Azure Resource Graph können Sie:

- auf die Eigenschaften zugreifen, die die Ressourcenanbieter zurückgeben, ohne jeden Ressourcenanbieter einzeln anrufen zu müssen.
- die letzten 14 Tage des Verlaufs der Änderungen der Ressource anzeigen, um festzustellen, welche Eigenschaften wann geändert wurden. (Vorschauversion)

> [!NOTE]
> Als _Previewfunktion_ stehen für einige `type`-Objekte zusätzliche Resource Manager-fremde Eigenschaften zur Verfügung. Weitere Informationen finden Sie unter [Erweiterte Eigenschaften (Vorschau)](./concepts/query-language.md#extended-properties).

## <a name="how-resource-graph-is-kept-current"></a>Wie Resource Graph auf dem aktuellen Stand gehalten wird

Beim Update einer Azure-Ressource wird Resource Graph durch Resource Manager über die Änderung informiert.
Daraufhin aktualisiert Resource Graph seine Datenbank. Resource Graph führt außerdem regelmäßig eine _vollständige Überprüfung_ durch. Durch diese Überprüfung wird sichergestellt, dass Resource Graph-Daten auch im Fall einer verpassten Benachrichtigung oder beim Update einer Ressource außerhalb von Resource Manager aktuell sind.

> [!NOTE]
> Resource Graph ruft Eigenschaften und Werte mittels `GET` über die neueste API-Version (keine Vorschauversion) des jeweiligen Ressourcenanbieters ab. Daher kann es vorkommen, dass die erwartete Eigenschaft nicht verfügbar ist. In einigen Fällen wurde die verwendete API-Version überschrieben, um in den Ergebnissen neuere oder gängigere Eigenschaften bereitzustellen. Eine vollständige Liste für Ihre Umgebung finden Sie im Beispiel [Anzeigen der API-Version für die einzelnen Ressourcentypen](./samples/advanced.md#apiversion).

## <a name="the-query-language"></a>Die Abfragesprache

Da Sie nun besser verstehen, was Azure Resource Graph ist, erfahren Sie nun, wie Sie Abfragen erstellen können.

Es ist wichtig zu wissen, dass die Abfragesprache von Azure Resource Graph auf der von Azure Data Explorer verwendeten [Kusto-Abfragesprache](/azure/data-explorer/data-explorer-overview) basiert.

Informationen zu Vorgängen und Funktionen, die mit Azure Resource Graph verwendet werden können, finden Sie unter [Grundlegendes zur Abfragesprache von Azure Resource Graph](./concepts/query-language.md). Wie Sie Ressourcen durchsuchen, erfahren Sie unter [Untersuchen Ihrer Azure-Ressourcen mit Resource Graph](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Berechtigungen in Azure Resource Graph

Um Resource Graph verwenden zu können, müssen Sie über die entsprechenden Rechte für die [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../../role-based-access-control/overview.md) und mindestens über Lesezugriff auf die abzufragenden Ressourcen verfügen. Wenn Sie nicht mindestens `read`-Berechtigungen für das Azure-Objekt bzw. die Objektgruppe haben, werden keine Ergebnisse zurückgegeben.

> [!NOTE]
> Ressource Graph verwendet die Abonnements, die für einen Prinzipal während der Anmeldung verfügbar sind. Um Ressourcen eines neuen Abonnements anzuzeigen, die während einer aktiven Sitzung hinzugefügt wurden, muss der Prinzipal den Kontext aktualisieren. Diese Aktion erfolgt nach dem Abmelden und erneuten Anmelden automatisch.

Die Azure-Befehlszeilenschnittstelle und Azure PowerShell nutzen Abonnements, auf die der Benutzer Zugriff hat. Bei der direkten Verwendung der REST-API wird die Abonnementliste vom Benutzer bereitgestellt. Hat der Benutzer Zugriff auf eins der Abonnements in der Liste, werden die Abfrageergebnisse für die Abonnements zurückgegeben, auf die der Benutzer Zugriff hat. Dies ist das gleiche Verhalten wie beim Aufrufen von [Resource Groups - List](/rest/api/resources/resourcegroups/list) (Ressourcengruppen – Liste): Sie erhalten Ressourcengruppen, auf die Sie Zugriff haben, ohne Hinweis darauf, dass es sich um ein Teilergebnis handeln könnte. Sind in der Abonnementliste keine Abonnements vorhanden, für die der Benutzer über die entsprechenden Berechtigungen verfügt, lautet die Antwort _403_ (Verboten).

> [!NOTE]
> In der REST-API-**Vorschauversion** `2020-04-01-preview` wurde die Abonnementliste unter Umständen ausgelassen.
> Wenn die Eigenschaften `subscriptions` und `managementGroupId` in der Anforderung nicht definiert sind, wird der Bereich (_scope_) auf den Mandanten festgelegt. Weitere Informationen finden Sie unter [Abfragebereich](./concepts/query-language.md#query-scope).

## <a name="throttling"></a>Drosselung

Abfragen an Ressource Graph werden bei der Ausführung als kostenloser Dienst gedrosselt, um für alle Kunden die beste Erfahrung und Antwortzeit bereitzustellen. Wenn Ihre Organisation die Resource Graph-API für umfangreiche und häufige Abfragen verwenden möchte, verwenden Sie das Feedback-Portal auf der [Portal-Seite zu Resource Graph](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Geben Sie Ihr Geschäftsszenario an, und aktivieren Sie das Kontrollkästchen „Microsoft darf mich bezüglich meines Feedbacks per E-Mail kontaktieren“, damit das Team Sie kontaktieren kann.

Resource Graph drosselt Abfragen auf Benutzerebene. Die Dienstantwort enthält die folgenden HTTP-Header:

- `x-ms-user-quota-remaining` (int): Das verbleibende Ressourcenkontingent für den Benutzer. Dieser Wert entspricht der Anzahl von Abfragen.
- `x-ms-user-quota-resets-after` (hh:mm:ss): Der Zeitraum, bis das Kontingent eines Benutzers zurückgesetzt wird

Weitere Informationen finden Sie in der [Anleitung für gedrosselte Anforderungen](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Ausführen Ihrer ersten Abfrage

Der Azure Resource Graph-Explorer im Azure-Portal ermöglicht die Ausführung von Resource Graph-Abfragen direkt im Azure-Portal. Heften Sie die Ergebnisse als dynamische Diagramme an, um dynamische Echtzeitinformationen für den Portalworkflow bereitzustellen. Weitere Informationen finden Sie unter [Quickstart: Run your first Resource Graph query using Azure Resource Graph Explorer](./first-query-portal.md) (Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers).

Resource Graph unterstützt die Azure-Befehlszeilenschnittstelle, Azure PowerShell, das Azure SDK für Python u. v. m. Die Abfrage ist für alle Sprachen gleich strukturiert. Lesen Sie, wie Sie Resource Graph mit einer der folgenden Komponenten aktivieren:

- [Azure-Portal und Resource Graph-Explorer](./first-query-portal.md) 
- [Azure-Befehlszeilenschnittstelle](./first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Abfragesprache](./concepts/query-language.md).
- Informationen zur verwendeten Sprache finden Sie unter [Einfache Abfragen](./samples/starter.md).
- Informationen zur anspruchsvolleren Nutzung finden Sie unter [Erweiterte Abfragen](./samples/advanced.md).
