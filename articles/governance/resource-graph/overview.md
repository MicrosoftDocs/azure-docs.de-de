---
title: Übersicht über Azure Resource Graph
description: Hier wird erläutert, wie der Azure Resource Graph-Dienst das komplexe bedarfsabhängige Abfragen von Ressourcen ermöglicht.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 9d3385b688208065e5854b6358819b5afad8fe65
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162077"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Übersicht über den Azure Resource Graph-Dienst

Azure Resource Graph ist ein Dienst in Azure, der die Azure-Ressourcenverwaltung mithilfe effizienter und leistungsstarker Ressourcenuntersuchung befähigen soll, übergreifend für eine bestimmte Menge von Abonnements nach Bedarf Abfragen durchzuführen, sodass Sie Ihre Umgebung effektiv beherrschen können. Diese Abfragen verfügen über die folgenden Features:

- Die Fähigkeit zum Abfragen von Ressourcen mit komplexem Filtern, Gruppieren und Sortieren nach Eigenschaften der Ressource.
- Fähigkeit zum iterativen Untersuchen von Ressourcen, basierend auf Governanceanforderungen.
- Möglichkeit zum Bewerten der Auswirkungen der Anwendung von Richtlinien in einer großen Cloudumgebung.
- Möglichkeit zum [Beschreiben von Änderungen der Ressourceneigenschaften](./how-to/get-resource-changes.md) (Vorschau).

In dieser Dokumentation wird jedes Feature ausführlich beschrieben.

> [!NOTE]
> Azure Resource Graph hebt mit der neuen Oberfläche zum Durchsuchen aller Ressourcen und dem [Änderungsverlauf](../policy/how-to/determine-non-compliance.md#change-history-preview) mit 
> _visuellem Diff_ von Azure Policy die Suchleiste des Azure-Portals auf eine neue Höhe. Sie dient zur Unterstützung von Kunden, die umfangreiche Umgebungen verwalten.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Wie Resource Graph Azure Resource Manager ergänzt

Azure Resource Manager unterstützt aktuelle Abfragen von einfachen Ressourcenfeldern, insbesondere Ressourcenname, ID, Typ, Ressourcengruppe, Abonnement und Speicherort. Resource Manager bietet darüber hinaus Funktionen zum Aufrufen einzelner Ressourcenanbieter, um jeweils für eine Ressource auf die Detaileigenschaften zuzugreifen.

Mit Azure Resource Graph können Sie auf diese Eigenschaften zugreifen, die die Ressourcenanbieter zurückgeben, ohne jeden Ressourcenanbieter einzeln anrufen zu müssen. Die unterstützten Ressourcentypen sind in der Tabelle unter [Löschen von Azure-Ressourcen für Bereitstellungen im vollständigen Modus](../../azure-resource-manager/complete-mode-deletion.md) mit **Ja** gekennzeichnet.

Mit Azure Resource Graph können Sie:

- auf die Eigenschaften zugreifen, die die Ressourcenanbieter zurückgeben, ohne jeden Ressourcenanbieter einzeln anrufen zu müssen.
- die letzten 14 Tage des Verlaufs der Änderungen der Ressource anzeigen, um festzustellen, welche Eigenschaften wann geändert wurden. (Vorschauversion)

## <a name="how-resource-graph-is-kept-current"></a>Wie Resource Graph auf dem aktuellen Stand gehalten wird

Beim Update einer Azure-Ressource wird Resource Graph durch Resource Manager über die Änderung informiert.
Darauf hin aktualisiert Resource Graph seine Datenbank. Resource Graph führt außerdem regelmäßig eine _vollständige Überprüfung_ durch. Durch diese Überprüfung wird sichergestellt, dass Resource Graph-Daten auch im Fall einer verpassten Benachrichtigung oder beim Update einer Ressource außerhalb von Resource Manager aktuell sind.

## <a name="the-query-language"></a>Die Abfragesprache

Da Sie nun besser verstehen, was Azure Resource Graph ist, erfahren Sie nun, wie Sie Abfragen erstellen können.

Es ist wichtig zu wissen, dass die Abfragesprache von Azure Resource Graph auf der von Azure Data Explorer verwendeten [Kusto-Abfragesprache](../../data-explorer/data-explorer-overview.md) basiert.

Informationen zu Vorgängen und Funktionen, die mit Azure Resource Graph verwendet werden können, finden Sie unter [Grundlegendes zur Abfragesprache von Azure Resource Graph](./concepts/query-language.md).
Wie Sie Ressourcen durchsuchen, erfahren Sie unter [Untersuchen Ihrer Azure-Ressourcen mit Resource Graph](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Berechtigungen in Azure Resource Graph

Um Resource Graph verwenden zu können, müssen Sie über die richtigen Rechte für die [rollenbasierte Zugriffssteuerung](../../role-based-access-control/overview.md) (Role-Based Access Control, RBAC) und mindestens über Lesezugriff auf die abzufragenden Ressourcen verfügen. Wenn Sie nicht mindestens `read`-Berechtigungen für das Azure-Objekt bzw. die Objektgruppe haben, werden keine Ergebnisse zurückgegeben.

> [!NOTE]
> Ressource Graph verwendet die Abonnements, die für einen Prinzipal während der Anmeldung verfügbar sind. Um Ressourcen eines neuen Abonnements anzuzeigen, die während einer aktiven Sitzung hinzugefügt wurden, muss der Prinzipal den Kontext aktualisieren. Diese Aktion erfolgt nach dem Abmelden und erneuten Anmelden automatisch.

## <a name="throttling"></a>Drosselung

Abfragen an Ressource Graph werden bei der Ausführung als kostenloser Dienst gedrosselt, um für alle Kunden die beste Erfahrung und Antwortzeit bereitzustellen. Wenn Ihre Organisation die Resource Graph-API für umfangreiche und häufige Abfragen verwenden möchte, verwenden Sie das Feedback-Portal auf der [Portal-Seite zu Resource Graph](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Geben Sie Ihr Geschäftsszenario an, und aktivieren Sie das Kontrollkästchen „Microsoft darf mich bezüglich meines Feedbacks per E-Mail kontaktieren“, damit das Team Sie kontaktieren kann.

Resource Graph drosselt Abfragen auf Benutzerebene. Die Dienstantwort enthält die folgenden HTTP-Header:

- `x-ms-user-quota-remaining` (int): Das verbleibende Ressourcenkontingent für den Benutzer. Dieser Wert entspricht der Anzahl von Abfragen.
- `x-ms-user-quota-resets-after` (hh:mm:ss): Der Zeitraum, bis das Kontingent eines Benutzers zurückgesetzt wird

Weitere Informationen finden Sie unter [Drosselung von Resource Manager-Anforderungen](../../azure-resource-manager/resource-manager-request-limits.md).

## <a name="running-your-first-query"></a>Ausführen Ihrer ersten Abfrage

Resource Graph unterstützt die Azure-Befehlszeilenschnittstelle (CLI), Azure PowerShell und das Azure SDK für .NET. Die Abfrage ist für alle Sprachen gleich strukturiert. Informationen zum Aktivieren von Resource Graph in [Azure-Befehlszeilenschnittstelle](first-query-azurecli.md#add-the-resource-graph-extension) und [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Nächste Schritte

- Führen Sie Ihre erste Abfrage mit der [Azure CLI](first-query-azurecli.md) aus.
- Führen Sie Ihre erste Abfrage mit [Azure PowerShell](first-query-powershell.md) aus.
- Beginnen Sie mit [einfachen Abfragen](./samples/starter.md).
- Verbessern Sie Ihre Kenntnisse mit [Erweiterten Abfragen](./samples/advanced.md).