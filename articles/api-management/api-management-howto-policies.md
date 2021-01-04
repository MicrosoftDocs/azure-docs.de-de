---
title: Richtlinien in Azure API Management | Microsoft Docs
description: Erfahren Sie, wie Sie Richtlinien in API Management erstellen, bearbeiten und konfigurieren. Hier finden Sie Codebeispiele und zusätzliche verfügbare Ressourcen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 37ac6369790ed526fd923819558863ae84432aed
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358115"
---
# <a name="policies-in-azure-api-management"></a>Richtlinien in Azure API Management

Richtlinien sind ein umfassendes Werkzeug in Azure API Management (APIM), mit dem Herausgeber das Verhalten der API über eine Konfiguration verändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Häufig verwendete Anweisungen sind z. B. Formatumwandlungen von XML nach JSON und Aufrufbeschränkungen, um die Anzahl eingehender Aufrufe von einem Entwickler zu beschränken. Viele weitere Richtlinien sind vorkonfiguriert verfügbar.

Richtlinien werden im Gateway, das sich zwischen API-Consumer und der verwalteten API befindet, angewendet. Das Gateway empfängt alle Anfragen und leitet diese normalerweise unverändert an die zugrunde liegende API weiter. Richtlinien können jedoch Änderungen an der eingehenden Anfrage und an der ausgehenden Antwort vornehmen.

Richtlinienausdrücke können als Attributwerte oder Textwerte in einer beliebigen API Management-Richtlinie verwendet werden, sofern in der Richtlinie nicht anders angegeben. Einige Richtlinien, beispielsweise [Ablaufsteuerung][Control flow] und [Variable festlegen][Set variable], basieren auf Richtlinienausdrücken. Weitere Informationen finden Sie unter [Erweiterte Richtlinien][Advanced policies] und [Richtlinienausdrücke][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Grundlegendes zur Richtlinienkonfiguration

Die Richtliniendefinition ist ein einfaches XML-Dokument, das eine Sequenz eingehender und ausgehender Anweisungen beschreibt. Das XML-Dokument kann direkt im Definitionsfenster bearbeitet werden. Auf der rechten Seite sehen Sie eine Liste mit Anweisungen, und die für den aktuellen Bereich anwendbaren Anweisungen sind aktiviert und hervorgehoben.

Wenn Sie auf eine aktivierte Anweisung klicken, wird der entsprechende XML-Ausschnitt an der Cursorposition in der Definitionsansicht eingefügt. 

> [!NOTE]
> Wenn die Richtlinie, die Sie hinzufügen möchten, nicht aktiviert ist, stellen Sie sicher, dass Sie sich im richtigen Bereich für diese Richtlinie befinden. Für jede Richtlinienanweisung sind bestimmte Bereiche und Richtlinienabschnitte vorgesehen. Informationen zu den Richtlinienabschnitten und Bereichen für eine Richtlinie finden Sie in der [Richtlinienreferenz][Policy Reference] im Abschnitt **Verwendung** für die jeweilige Richtlinie.
> 
> 

Die Konfiguration ist in `inbound`, `backend`, `outbound` und `on-error` unterteilt. Die Richtlinienanweisungen werden in der Reihenfolge für Anforderung und Antwort ausgeführt.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Wenn bei der Verarbeitung einer Anfrage ein Fehler auftritt, werden alle verbleibenden Schritte in den `inbound`-, `backend`- oder `outbound`-Abschnitten übersprungen und die Ausführung bei den Anweisungen im `on-error`-Abschnitt fortgesetzt. Durch Platzieren von Richtlinienanweisungen im `on-error`-Abschnitt können Sie den Fehler überprüfen, indem Sie die `context.LastError`-Eigenschaft verwenden, die Fehlerantwort mit der `set-body`-Richtlinie untersuchen und anpassen sowie konfigurieren, was geschieht, wenn ein Fehler auftritt. Es gibt Fehlercodes für integrierte Schritte und für Fehler, die während der Verarbeitung von Richtlinienanweisungen auftreten können. Weitere Informationen finden Sie unter [Error handling in API Management policies](./api-management-error-handling-policies.md)(in englischer Sprache).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Konfigurieren von Richtlinien

Informationen zum Konfigurieren von Richtlinien finden Sie unter [How to set or edit Azure API Management policies](set-edit-policies.md) (Festlegen oder Bearbeiten von Azure API Management-Richtlinien).

## <a name="policy-reference"></a>Richtlinienreferenz

In der [Richtlinienreferenz](./api-management-policies.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.

## <a name="policy-samples"></a>Richtlinienbeispiele

Weitere Codebeispiele finden Sie unter [Richtlinien für die API-Verwaltung](./policy-reference.md).

## <a name="examples"></a>Beispiele

### <a name="apply-policies-specified-at-different-scopes"></a>Anwenden von Richtlinien, die für verschiedene Bereiche angegeben sind

Wenn Sie eine Richtlinie auf der globalen Ebene und eine Richtlinie für eine API konfiguriert haben, dann werden immer beide Richtlinien angewendet, wenn diese API aufgerufen wird. API Management ermöglicht eine deterministische Festlegung der Reihenfolge kombinierter Richtlinienanweisungen über das Basiselement. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

In der obigen Beispiel-Richtliniendefinition wird die `cross-domain`-Anweisung vor allen übergeordneten Richtlinien ausgeführt, auf die wiederum die `find-and-replace`-Richtlinie folgt. 

### <a name="restrict-incoming-requests"></a>Einschränken eingehender Anforderungen

Um eine neue Anweisung zur Einschränkung eingehender Anfragen auf bestimmte IP-Adressen zu erstellen, platzieren Sie den Cursor innerhalb des `inbound`-XML-Elements, und klicken Sie auf die Anweisung **Restrict caller IPs**.

![Einschränkungsrichtlinien][policies-restrict]

Daraufhin wird ein XML-Ausschnitt in das `inbound` -Element eingefügt, der Anweisungen zur Konfiguration der Anweisung enthält.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Ändern Sie den XML-Ausschnitt wie folgt, um nur eingehende Anfragen von der IP-Adresse 1.2.3.4 zu erlauben:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](./api-management-policies.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management-Richtlinienbeispiele](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
