---
title: Verwaltete Apps mit Benachrichtigungen
description: Konfigurieren Sie verwaltete Anwendungen mit Webhookendpunkten, um Benachrichtigungen über Erstellungs-, Aktualisierungs- und Löschungsereignisse sowie Fehler in den Instanzen verwalteter Anwendungen zu erhalten.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: cec17b98daa8eca31cda076921288e2838960511
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434531"
---
# <a name="azure-managed-applications-with-notifications"></a>Verwaltete Azure-Anwendungen mit Benachrichtigungen

Azure Managed Applications-Benachrichtigungen ermöglichen es Herausgebern, Aktionen auf Grundlage von Lebenszyklusereignissen der Instanzen verwalteter Anwendungen zu automatisieren. Herausgeber können benutzerdefinierte Benachrichtigungswebhookendpunkte angeben, um Ereignisbenachrichtigungen über neue und vorhandene Instanzen verwalteter Anwendungen zu empfangen. Herausgeber können benutzerdefinierte Workflows zum Zeitpunkt der Bereitstellung, Aktualisierung und Löschung von Anwendungen einrichten.

## <a name="getting-started"></a>Erste Schritte
Um Benachrichtigungen für verwaltete Anwendungen zu erhalten, richten Sie einen öffentlichen HTTPS-Endpunkt ein, und geben Sie ihn an, wenn Sie die Definition der Dienstkataloganwendung oder das Azure Marketplace-Angebot veröffentlichen.

Hier finden Sie die empfohlenen Schritte für einen schnellen Einstieg:
1. Richten Sie einen öffentlichen HTTPS-Endpunkt ein, der die eingehenden POST-Anforderungen protokolliert und `200 OK` zurückgibt.
2. Fügen Sie der Definition der Dienstkataloganwendung bzw. dem Azure Marketplace-Angebot den Endpunkt hinzu, wie später in diesem Artikel erläutert.
3. Erstellen Sie eine Instanz einer verwalteten Anwendung, die auf die Anwendungsdefinition oder das Azure Marketplace-Angebot verweist.
4. Vergewissern Sie sich, dass die Benachrichtigungen empfangen werden.
5. Aktivieren Sie die Autorisierung, wie im Abschnitt **Endpunktauthentifizierung** dieses Artikels erläutert.
6. Befolgen Sie die Anweisungen im Abschnitt **Benachrichtigungsschema** dieses Artikels, um die Benachrichtigungsanforderungen zu analysieren und die Geschäftslogik basierend auf der Benachrichtigung zu implementieren.

## <a name="add-service-catalog-application-definition-notifications"></a>Hinzufügen von Benachrichtigungen zu Definitionen von Dienstkataloganwendungen
#### <a name="azure-portal"></a>Azure-Portal
Informationen zu den ersten Schritten finden Sie unter [Veröffentlichen einer Dienstkataloganwendung über das Azure-Portal](./publish-portal.md).

![Benachrichtigungen zu Definitionen von Dienstkataloganwendungen im Azure-Portal](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST-API

> [!NOTE]
> Derzeit können Sie in den Eigenschaften der Anwendungsdefinition nur einen Endpunkt in den `notificationEndpoints` angeben.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Hinzufügen von Benachrichtigungen zu verwalteten Azure Marketplace-Anwendungen
Informationen hierzu finden Sie unter [Erstellen eines Azure-Anwendungsangebots](../../marketplace/create-new-azure-apps-offer.md).

![Benachrichtigungen zu verwalteten Azure Marketplace-Anwendungen im Azure-Portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Ereignistrigger
In der folgenden Tabelle werden alle möglichen Kombinationen von EventType und ProvisioningState sowie ihre Trigger beschrieben:

EventType | ProvisioningState | Trigger für Benachrichtigung
---|---|---
PUT | Zulässig | Die verwaltete Ressourcengruppe wurde nach dem PUT der Anwendung (vor dem Start der Bereitstellung in der verwalteten Ressourcengruppe) erfolgreich erstellt und projiziert.
PUT | Erfolgreich | Die vollständige Bereitstellung der verwalteten Anwendung war nach Ausführung von „PUT“ erfolgreich.
PUT | Fehler | Fehler beim Ausführen von „PUT“ der Anwendungsinstanzbereitstellung an einem beliebigen Punkt.
PATCH | Erfolgreich | Nach einem erfolgreichen PATCH der Instanz der verwalteten Anwendung zum Aktualisieren von Tags, der JIT-Zugriffsrichtlinie oder der verwalteten Identität.
Delete | Wird gelöscht | Sobald der Benutzer einen DELETE-Vorgang einer Instanz einer verwalteten Anwendung initiiert.
Delete | Deleted | Nach der vollständigen und erfolgreichen Löschung der verwalteten Anwendung.
Delete | Fehler | Nach jedem Fehler während der Aufhebung der Bereitstellung, der den Löschvorgang blockiert.
## <a name="notification-schema"></a>Benachrichtigungsschema
Wenn Sie den Webhookendpunkt für die Behandlung von Benachrichtigungen einrichten, müssen Sie die Nutzlast analysieren, um wichtige Eigenschaften zu ermitteln, damit auf die Benachrichtigung reagiert werden kann. Viele der Eigenschaften von Benachrichtigungen zu verwalteten Anwendungen des Dienstkatalogs und des Azure Marketplace sind identisch. Zwei geringe Unterschiede werden in der Tabelle nach den Beispielen kurz beschrieben.

#### <a name="service-catalog-application-notification-schema"></a>Benachrichtigungsschema für Dienstkataloganwendungen
Im Folgenden finden Sie ein Beispiel für eine Dienstkatalogbenachrichtigung nach der erfolgreichen Bereitstellung einer Instanz einer verwalteten Anwendung:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Wenn die Bereitstellung fehlschlägt, wird eine Benachrichtigung mit den Fehlerdetails an den angegebenen Endpunkt gesendet.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Benachrichtigungsschema für Azure Marketplace-Anwendungen

Im Folgenden finden Sie ein Beispiel für eine Dienstkatalogbenachrichtigung nach der erfolgreichen Bereitstellung einer Instanz einer verwalteten Anwendung:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Wenn die Bereitstellung fehlschlägt, wird eine Benachrichtigung mit den Fehlerdetails an den angegebenen Endpunkt gesendet.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parameter | BESCHREIBUNG
---|---
eventType | Der Typ des Ereignisses, das die Benachrichtigung ausgelöst hat (Beispiele: PUT, PATCH, DELETE.)
applicationId | Der vollqualifizierte Ressourcenbezeichner der verwalteten Anwendung, für die die Benachrichtigung ausgelöst wurde.
eventTime | Der Zeitstempel des Ereignisses, das die Benachrichtigung ausgelöst hat (Datum und Uhrzeit im UTC ISO 8601-Format).
provisioningState | Der Bereitstellungsstatus der Instanz der verwalteten Anwendung. (Beispiele: „Succeeded“, „Failed“, „Deleting“, „Deleted“.)
error | *Wird nur angegeben, wenn provisioningState den Wert „Failed“ aufweist*. Enthält den Fehlercode, die Meldung und Details zu dem Problem, das den Fehler verursacht hat.
applicationDefinitionId | *Wird nur für verwaltete Dienstkataloganwendungen angegeben*. Stellt den vollqualifizierten Ressourcenbezeichner der Anwendungsdefinition dar, für die die Instanz der verwalteten Anwendung bereitgestellt wurde.
Tarif | *Wird nur für verwaltete Azure Marketplace-Anwendungen angegeben*. Stellt den Herausgeber, das Angebot, die SKU und die Version der Instanz der verwalteten Anwendung dar.
billingDetails | *Wird nur für verwaltete Azure Marketplace-Anwendungen angegeben*. Hierbei handelt es sich um die Abrechnungsdetails der verwalteten Anwendungsinstanz. Mithilfe von resourceUsageId können im Azure Marketplace Nutzungsdetails abgefragt werden.

## <a name="endpoint-authentication"></a>Endpunktauthentifizierung
So sichern Sie den Webhookendpunkt und stellen die Echtheit der Benachrichtigung sicher:
1. Geben Sie im Webhook-URI einen Abfrageparameter an, z. B. https\://your-endpoint.com?sig=Guid. Überprüfen Sie bei jeder Benachrichtigung, ob der Abfrageparameter `sig` den erwarteten Wert `Guid` aufweist.
2. Geben Sie für die Instanz der verwalteten Anwendung ein GET mit der applicationId aus. Überprüfen Sie, ob der „provisioningState“ dem „provisioningState“ der Benachrichtigung entspricht, um die Konsistenz sicherzustellen.

## <a name="notification-retries"></a>Benachrichtigungswiederholungen

Der Managed Applications-Benachrichtigungsdienst erwartet vom Webhookendpunkt die Antwort `200 OK` auf die Benachrichtigung. Der Benachrichtigungsdienst versucht erneut, die Benachrichtigung zu senden, wenn der Webhookendpunkt den HTTP-Fehlercode 500 oder einen höheren Fehlercode bzw. den Fehlercode 429 zurückgibt oder vorübergehend nicht erreichbar ist. Wenn der Webhookendpunkt nicht innerhalb von 10 Stunden verfügbar wird, wird die Benachrichtigung gelöscht, und die Versuche zum Senden der Benachrichtigung werden eingestellt.
