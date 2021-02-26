---
title: Sicherheit für Azure Digital Twins-Lösungen
titleSuffix: Azure Digital Twins
description: Grundlegendes zu bewährten Sicherheitsmethoden mit Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b589f98ee78d0709b2a74ba4e364cec0e486e968
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547161"
---
# <a name="secure-azure-digital-twins"></a>Schützen von Azure Digital Twins

Zur Gewährleistung der Sicherheit ermöglicht Azure Digital Twins eine exakte Zugriffssteuerung für bestimmte Daten, Ressourcen und Aktionen in Ihrer Bereitstellung. Hierzu wird eine präzise Rollen- und Rechteverwaltungsstrategie namens **rollenbasierte Zugriffssteuerung in Azure** (Role-Based Access Control, Azure RBAC) verwendet. Informationen zu den allgemeinen Prinzipien von Azure RBAC finden Sie [hier](../role-based-access-control/overview.md).

Azure Digital Twins unterstützt auch die Verschlüsselung ruhender Daten.

## <a name="roles-and-permissions-with-azure-rbac"></a>Rollen und Berechtigungen mit Azure RBAC

Azure RBAC wird in Azure Digital Twins über die Integration mit [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) bereitgestellt.

Mit Azure RBAC können einem *Sicherheitsprinzipal* Berechtigungen erteilt werden. Bei einem Sicherheitsprinzipal kann es sich um einen Benutzer, um eine Gruppe oder um einen Anwendungsdienstprinzipal handeln. Der Sicherheitsprinzipal wird durch Azure AD authentifiziert und erhält im Gegenzug ein OAuth 2.0-Token. Dieses Token kann verwendet werden, um eine an eine Azure Digital Twins-Instanz gerichtete Zugriffsanforderung zu autorisieren.

### <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

Der Zugriff mit Azure AD ist ein zweistufiger Prozess. Wenn ein Sicherheitsprinzipal (ein Benutzer, eine Gruppe oder eine Anwendung) versucht, auf Azure Digital Twins zuzugreifen, muss die Anforderung *authentifiziert* und *autorisiert* werden. 

1. Zunächst wird die Identität des Sicherheitsprinzipals *authentifiziert* und ein OAuth 2.0-Token zurückgegeben.
2. Anschließend wird das Token als Teil einer Anforderung an den Azure Digital Twins-Dienst übergeben, um den Zugriff auf die angegebene Ressource zu *autorisieren*.

Für den Authentifizierungsschritt muss jede Anwendungsanforderung zur Laufzeit ein OAuth 2.0-Zugriffstoken enthalten. Wird eine Anwendung in einer Azure-Entität ausgeführt (beispielsweise in einer [Azure Functions](../azure-functions/functions-overview.md)-App), kann für den Zugriff auf die Ressourcen eine **verwaltete Identität** verwendet werden. Weitere Informationen zu verwalteten Identitäten finden Sie im nächsten Abschnitt.

Für den Autorisierungsschritt muss dem Sicherheitsprinzipal eine Azure-Rolle zugewiesen werden. Die möglichen Berechtigungen eines Sicherheitsprinzipals sind durch die Rollen vorgegeben, die dem Prinzipal zugewiesen sind. Azure Digital Twins stellt Azure-Rollen bereit, die Berechtigungssätze für Azure Digital Twins-Ressourcen enthalten. Diese Rollen werden weiter unten in diesem Artikel beschrieben.

Weitere Informationen zu in Azure unterstützten Rollen und Rollenzuweisungen finden Sie in der Azure RBAC-Dokumentation unter [*Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen*](../role-based-access-control/rbac-and-directory-admin-roles.md).

#### <a name="authentication-with-managed-identities"></a>Authentifizierung mit verwalteten Identitäten

[Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) ist ein Azure-übergreifendes Feature, mit dem Sie eine sichere Identität für die Bereitstellung erstellen können, in der Ihr Anwendungscode ausgeführt wird. Dieser Identität können dann Zugriffssteuerungsrollen zugeordnet werden, um benutzerdefinierte Berechtigungen für den Zugriff auf bestimmte Azure-Ressourcen zu erteilen, die Ihre Anwendung benötigt.

Die Azure-Plattform verwaltet diese Laufzeitidentität mit verwalteten Identitäten. Sie müssen keine Zugriffsschlüssel in Ihrem Anwendungscode speichern und schützen – weder für die Identität selbst noch für die Ressourcen, auf die zugegriffen werden muss. Von einer Azure Digital Twins-Client-App, die innerhalb einer Azure App Service-Anwendung ausgeführt wird, müssen keine SAS-Regeln und -Schlüssel oder andere Zugriffstoken verarbeitet werden. Die Client-App benötigt nur die Endpunktadresse des Azure Digital Twins-Namespace. Wenn die App eine Verbindung herstellt, bindet Azure Digital Twins den Kontext der verwalteten Entität an den Client. Nach der Zuordnung zu einer verwalteten Identität können von Ihrem Azure Digital Twins-Client sämtliche autorisierten Vorgänge ausgeführt werden. Zur Autorisierung wird dann eine verwaltete Entität einer Azure Digital Twins-Rolle zugeordnet, wie im Anschluss beschrieben.

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autorisierung: Azure-Rollen für Azure Digital Twins

Azure bietet **zwei integrierte Azure-Rollen** für die Autorisierung des Zugriffs auf die [Datenebenen-APIs](how-to-use-apis-sdks.md#overview-data-plane-apis) von Azure Digital Twins. Sie können auf die Rollen entweder über den Namen oder über die ID verweisen:

| Integrierte Rolle | BESCHREIBUNG | id | 
| --- | --- | --- |
| Azure Digital Twins Data Owner (Azure Digital Twins-Datenbesitzer) | Bietet Vollzugriff auf Azure Digital Twins-Ressourcen | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure Digital Twins Data Reader (Azure Digital Twins-Datenleser) | Bietet schreibgeschützten Zugriff auf Azure Digital Twins-Ressourcen | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

Rollen können auf zwei Arten zugewiesen werden:
* Über den Bereich „Zugriffssteuerung (IAM)“ für Azure Digital Twins im Azure-Portal (weitere Informationen finden Sie unter [*Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal*](../role-based-access-control/role-assignments-portal.md))
* Mithilfe von CLI-Befehlen zum Hinzufügen oder Entfernen einer Rolle

Ausführlichere Informationen hierzu finden Sie im [*Azure Digital Twins-Tutorial: Erstellen einer End-to-End-Lösung*](tutorial-end-to-end.md).

Weitere Informationen zur Definition integrierter Rollen finden Sie in der Azure RBAC-Dokumentation unter [*Grundlegendes zu Azure-Rollendefinitionen*](../role-based-access-control/role-definitions.md). Informationen zum Erstellen von benutzerdefinierten Azure-Rollen finden Sie unter [*Benutzerdefinierte Azure-Rollen*](../role-based-access-control/custom-roles.md).

##### <a name="automating-roles"></a>Automatisieren von Rollen

Wenn Sie sich auf Rollen in automatisierten Szenarien beziehen, empfiehlt es sich, auf diese mit ihren **IDs** und nicht mit ihren Namen zu verweisen. Die Namen können sich zwischen den einzelnen Versionen ändern, die IDs jedoch nicht, was sie zu einer beständigeren Referenz in der Automation macht.

> [!TIP]
> Wenn Sie Rollen mit einem Cmdlet zuweisen, z. B. `New-AzRoleAssignment` ([Verweis](/powershell/module/az.resources/new-azroleassignment)), können Sie den Parameter `-RoleDefinitionId` anstelle von `-RoleDefinitionName` verwenden, um eine ID anstelle eines Namens für die Rolle zu übergeben.

### <a name="permission-scopes"></a>Berechtigungsbereiche

Bevor Sie einem Sicherheitsprinzipal eine Azure-Rolle zuweisen, legen Sie den Zugriffsbereich fest, den der Sicherheitsprinzipal haben soll. Es empfiehlt sich, immer nur den kleinstmöglichen Umfang zu gewähren.

In der folgenden Liste werden die Ebenen beschrieben, auf denen Sie den Zugriff auf Azure Digital Twins-Ressourcen einschränken können:
* Modelle: Die Aktionen für diese Ressource geben die Kontrolle über [Modelle](concepts-models.md) vor, die in Azure Digital Twins hochgeladen werden.
* Abfragen des Digital Twins-Graphen: Die Aktionen für diese Ressource bestimmen die Fähigkeit, [Abfragevorgänge](concepts-query-language.md) für digitale Zwillinge innerhalb des Azure Digital Twins-Graphen auszuführen.
* Digitaler Zwilling: Die Aktionen für diese Ressource ermöglichen die Steuerung von CRUD-Vorgängen für [digitale Zwillinge](concepts-twins-graph.md) im Zwillingsgraphen.
* Digitale Zwillingsbeziehung: Die Aktionen für diese Ressource definieren die Steuerung von CRUD-Vorgängen für [Beziehungen](concepts-twins-graph.md) zwischen digitalen Zwillingen im Zwillingsgraphen.
* Ereignisroute: Die Aktionen für diese Ressource bestimmen Berechtigungen zum [Weiterleiten von Ereignissen](concepts-route-events.md) aus Azure Digital Twins an einen Endpunktdienst wie [Event Hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md) oder [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Problembehandlung bei Berechtigungen

Wenn ein Benutzer versucht, eine Aktion auszuführen, die für seine Rolle nicht zulässig ist, wird möglicherweise der folgende Fehler von der Dienstanforderung zurückgegeben: `403 (Forbidden)`. Weitere Informationen und Hilfe bei der Problembehandlung finden Sie unter [*Problembehandlung: Fehler bei Azure Digital Twins-Anforderung mit dem Status ''403 (Forbidden)'' (403 (Unzulässig))*](troubleshoot-error-403.md).

## <a name="managed-identity-for-accessing-other-resources-preview"></a>Verwaltete Identität für den Zugriff auf andere Ressourcen (Vorschau)

Mithilfe einer eingerichteten **verwalteten Identität** von [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) für eine Azure Digital Twins-Instanz kann die Instanz einfach auf andere von Azure AD geschützte Ressourcen zugreifen, z. B. auf [Azure Key Vault](../key-vault/general/overview.md). Die Identität wird von der Azure-Plattform verwaltet. Sie müssen keine Geheimnisse bereitstellen oder rotieren. Weitere Informationen zu verwalteten Identitäten in Azure AD finden Sie unter  [*Was sind verwaltete Identitäten für Azure-Ressourcen?*](../active-directory/managed-identities-azure-resources/overview.md). 

Azure unterstützt zwei Arten von verwalteten Identitäten: systemseitig zugewiesene und benutzerseitig zugewiesene. Aktuell unterstützt Azure Digital Twins nur **systemseitig zugewiesene Identitäten**. 

Sie können eine systemseitig zugewiesene verwaltete Identität für Ihre Azure Digital Twins-Instanz für die Authentifizierung bei einem [benutzerdefinierten Endpunkt](concepts-route-events.md#create-an-endpoint) verwenden. Azure Digital Twins unterstützt die systemseitig zugewiesene identitätsbasierte Authentifizierung für Endpunkte für [Event Hub](../event-hubs/event-hubs-about.md)- und  [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)-Ziele sowie für einen Endpunkt für [Azure Storage-Container](../storage/blobs/storage-blobs-introduction.md) für [Ereignisse unzustellbarer Nachrichten](concepts-route-events.md#dead-letter-events). [Event Grid](../event-grid/overview.md)-Endpunkte werden für verwaltete Identitäten aktuell nicht unterstützt.

Eine Anleitung zum Aktivieren einer vom System verwalteten Identität für Azure Digital Twins und ihrer Verwendung zum Weiterleiten von Ereignissen finden Sie unter [*Aktivieren einer verwalteten Identität für die Weiterleitung von Azure Digital Twins-Ereignissen (Vorschau)*](how-to-enable-managed-identities.md).

## <a name="private-network-access-with-azure-private-link-preview"></a>Zugriff auf private Netzwerke mit Azure Private Link (Vorschau)

[Azure Private Link](../private-link/private-link-overview.md) ist ein Dienst, der es Ihnen ermöglicht, auf Azure-Ressourcen wie [Azure Event Hubs](../event-hubs/event-hubs-about.md), [Azure Storage](../storage/common/storage-introduction.md) und [Azure Cosmos DB](../cosmos-db/introduction.md) sowie von Azure gehostete Kunden- und Partnerdienste über einen privaten Endpunkt in Ihrer [Azure Virtual Network-Instanz](../virtual-network/virtual-networks-overview.md) zuzugreifen. 

Auf ähnliche Weise können Sie private Endpunkte für Ihre Azure Digital Twins-Instanz verwenden, um es Clients, die sich in Ihrem virtuellen Netzwerk befinden, zu ermöglichen, sicher über Private Link auf die Instanz zuzugreifen. 

Der private Endpunkt verwendet eine IP-Adresse aus dem Adressraum Ihrer Azure Virtual Network-Instanz. Der Netzwerkdatenverkehr zwischen einem Client in Ihrem privaten Netzwerk und der Azure Digital Twins-Instanz wird über das virtuelle Netzwerk und Private Link im Microsoft-Backbonenetzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt. Dies ist eine visuelle Darstellung dieses Systems:

:::image type="content" source="media/concepts-security/private-link.png" alt-text="Eine Abbildung eines Netzwerks für PowerGrid Company: Hierbei handelt es sich um ein geschütztes virtuelles Netzwerk ohne Zugriff auf das Internet oder die öffentliche Cloud. Die Verbindung wird über Private Link zu einer Azure Digital Twins-Instanz namens CityOfTwins hergestellt.":::

Das Konfigurieren eines privaten Endpunkts für Ihre Azure Digital Twins-Instanz ermöglicht es Ihnen, Ihre Azure Digital Twins-Instanz zu schützen und eine öffentliche Offenlegung zu vermeiden. Außerdem ist die Datenexfiltration aus Ihrem virtuellen Netzwerk so auch nicht möglich.

Eine Anleitung zum Einrichten von Private Link für Azure Digital Twins finden Sie unter [*Aktivieren des privaten Zugriffs mit Private Link (Vorschau)*](how-to-enable-private-link.md).

### <a name="design-considerations"></a>Überlegungen zum Entwurf 

Hier finden Sie einige Faktoren, die Sie berücksichtigen sollten, wenn Sie Private Link mit Azure Digital Twins verwenden:
* **Preise:** Ausführliche Preisinformationen finden Sie unter  [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link). 
* **Regionale Verfügbarkeit:** Für Azure Digital Twins steht dieses Feature in allen Azure-Regionen zur Verfügung, in denen auch Azure Digital Twins verfügbar ist. 
* **Maximale Anzahl privater Endpunkte pro Azure Digital Twins-Instanz:** 10

Informationen zu den Limits für Private Link finden Sie unter  [„Einschränkungen“ im Artikel „Was ist der Azure Private Link-Dienst?“](../private-link/private-link-service-overview.md#limitations).

## <a name="service-tags"></a>Diensttags

Ein **Diensttag** steht für eine Gruppe von IP-Adresspräfixen aus einem bestimmten Azure-Dienst. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Tag automatisch, wenn sich die Adressen ändern. Auf diese Weise wird die Komplexität häufiger Updates an Netzwerksicherheitsregeln minimiert. Weitere Informationen zu Diensttags finden Sie unter  [*Tags für virtuelle Netzwerke*](../virtual-network/service-tags-overview.md). 

Sie können Diensttags verwenden, um Netzwerkzugriffssteuerungen für  [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#security-rules) oder [Azure Firewall](../firewall/service-tags.md) zu definieren, indem Sie Diensttags anstelle spezifischer IP-Adressen verwenden, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen (in diesem Fall  **AzureDigitalTwins**) im entsprechenden Feld *Quelle* oder *Ziel* einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. 

Im Folgenden finden Sie die Details zum **AzureDigitalTwins**-Diensttag.

| Tag | Zweck | Eingehend oder ausgehend möglich? | Regional möglich? | Einsatz mit Azure Firewall möglich? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>Hinweis: Dieses Tag oder die von diesem Tag abgedeckten IP-Adressen können verwendet werden, um den Zugriff auf Endpunkte einzuschränken, die für [Ereignisrouten](concepts-route-events.md) konfiguriert sind. | Eingehend | Nein | Ja |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Verwenden von Diensttags für den Zugriff auf Ereignisroutenendpunkte 

Die folgenden Schritte sind für den Zugriff auf [Ereignisroutenendpunkte](concepts-route-events.md) mithilfe von Diensttags mit Azure Digital Twins erforderlich.

1. Laden Sie zunächst diese JSON-Dateireferenz mit Azure-IP-Bereichen und -Diensttags herunter: [*Azure-IP-Bereiche und -Diensttags*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. Suchen Sie in der JSON-Datei nach IP-Adressbereichen für „AzureDigitalTwins“.  

3. In der Dokumentation zur externen Ressource, die mit dem Endpunkt verbunden ist (z. B. [Event Grid](../event-grid/overview.md), [Event Hub](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) oder [Azure Storage](../storage/blobs/storage-blobs-overview.md) für [Ereignisse für unzustellbare Nachrichten](concepts-route-events.md#dead-letter-events)), erfahren Sie, wie Sie IP-Filter für diese Ressource festlegen.

4. Legen Sie IP-Filter für die externen Ressourcen mithilfe der IP-Adressbereiche aus *Schritt 2* fest.  

5. Aktualisieren Sie die IP-Adressbereiche regelmäßig nach Bedarf. Die Bereiche können sich im Laufe der Zeit ändern. Daher empfiehlt es sich, diese regelmäßig zu überprüfen und bei Bedarf zu aktualisieren. Die Häufigkeit dieser Updates kann variieren, aber es ist ratsam, sie ein Mal pro Woche zu überprüfen.

## <a name="encryption-of-data-at-rest"></a>Verschlüsselung für ruhende Daten

Azure Digital Twins bietet Verschlüsselung von ruhenden Daten und bei der Übertragung, während sie in unseren Rechenzentren geschrieben werden, und entschlüsselt die Daten, wenn Sie auf sie zugreifen. Diese Verschlüsselung erfolgt mithilfe eines von Microsoft verwalteten Verschlüsselungsschlüssels.

## <a name="cross-origin-resource-sharing-cors"></a>Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)

**CORS (Cross-Origin Resource Sharing)** wird in Azure Digital Twins derzeit nicht unterstützt. Wenn Sie eine REST-API über eine Browser-App, eine [API Management (APIM)](../api-management/api-management-key-concepts.md)-Schnittstelle oder einen [Power Apps](/powerapps/powerapps-overview)-Connector aufrufen, wird daher möglicherweise ein Richtlinienfehler angezeigt.

Zum Beheben dieses Fehlers können Sie eine der folgenden Aktionen durchführen:
* Entfernen Sie den CORS-Header `Access-Control-Allow-Origin` aus der Nachricht. Dieser Header gibt an, ob die Antwort freigegeben werden kann. 
* Alternativ können Sie einen CORS-Proxy erstellen und über diesen die REST-API-Anforderung von Azure Digital Twins stellen. 

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu diesen Konzepten finden Sie in der Anleitung [*Vorgehensweise: Einrichten einer Instanz und der Authentifizierung*](how-to-set-up-instance-portal.md).

* Weitere Informationen zur Interaktion mit diesen Konzepten über den Clientanwendungscode finden Sie unter [*Vorgehensweise: Schreiben von App-Authentifizierungscode*](how-to-authenticate-client.md).

* Weitere Informationen zu [Azure RBAC](../role-based-access-control/overview.md).