---
title: SaaS-Fulfillment-API v2 | Azure Marketplace
description: Hier wird erläutert, wie Sie ein SaaS-Angebot im Azure Marketplace und in AppSource mit den zugehörigen Fulfillment-APIs der Version 2 erstellen und verwalten.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: da23b90e44869dcbd21acf9b2c4e04f30153ae09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66751778"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS-Fulfillment-APIs Version 2 

In diesem Artikel wird die API, mit der unabhängige Softwarehersteller ihre SaaS-Anwendungen in AppSource und im Azure Marketplace verkaufen können, ausführlich beschrieben. Diese API ist eine Voraussetzung für transaktionsfähige SaaS-Angebote in AppSource und im Azure Marketplace.

## <a name="managing-the-saas-subscription-lifecycle"></a>Verwalten des Lebenszyklus von SaaS-Abonnements

Der SaaS-Dienst von Microsoft verwaltet den gesamten Lebenszyklus ab dem Erwerb für ein SaaS-Abonnement und nutzt die Fulfillment-API als Mechanismus, um die eigentliche Erfüllung, Plan- bzw. Tarifänderungen und das Löschen des Abonnements beim ISV zu steuern. Die Abrechnung basiert auf dem Status des SaaS-Abonnements, das Microsoft verwaltet. Das folgende Diagramm zeigt die Zustände und die Vorgänge, durch die Änderungen des jeweiligen Status gesteuert werden.

![Zustände im Lebenszyklus von SaaS-Abonnements](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Zustände eines SaaS-Abonnements

Die folgende Tabelle enthält die Bereitstellungszustände für ein SaaS-Abonnement, einschließlich einer Beschreibung und eines Sequenzdiagramms für den jeweiligen Status (sofern zutreffend). 

#### <a name="provisioning"></a>Bereitstellung

Wenn ein Kunde einen Kauf initiiert, empfängt der unabhängige Softwarehersteller diese Informationen in einem Authentifizierungscode auf einer für Kunden interaktiven Website mithilfe eines URL-Parameters. Bei `https://contoso.com/signup?token=..` ist beispielsweise `https://contoso.com/signup` der URL-Anbieter der Angebotsseite im Partner Center. Der Authentifizierungscode kann überprüft und ausgetauscht werden, um die erforderlichen Details für die Bereitstellung zu erhalten, indem die Auflösungs-API aufgerufen wird.  Wenn der SaaS-Dienst die Bereitstellung abschließt, sendet er einen Aktivierungsaufruf, um zu signalisieren, dass die Erfüllung abgeschlossen ist und dem Kunden eine Rechnung gestellt werden kann.  Das folgende Diagramm zeigt die Abfolge der API-Aufrufe für ein Bereitstellungsszenario.  

![API-Aufrufe zum Bereitstellen eines SaaS-Diensts.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Bereitgestellt

Dieser Status ist der stabile Zustand eines bereitgestellten Diensts.

#### <a name="provisioning-for-update"></a>Bereitstellung für Aktualisierung
(von Marketplace) 

Dieser Status gibt an, dass eine Aktualisierung für einen vorhandenen Dienst ansteht. Eine derartige Aktualisierung kann durch den Kunden entweder im Marketplace oder über den SaaS-Dienst (nur bei „direkt“ für Kundentransaktionen) initiiert werden. Das folgende Diagramm zeigt die Aktionen für eine Aktualisierung, wenn diese im Marketplace initiiert wird.

![API-Aufrufe, wenn die Aktualisierung im Marketplace initiiert wird.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>Bereitstellung für Aktualisierung  
(von SaaS-Dienst) 

Das folgende Diagramm zeigt die Aktionen für eine Aktualisierung, wenn diese von einem SaaS-Dienst initiiert wird. Der Webhookaufruf wird durch eine durch den SaaS-Dienst initiierte Aktualisierung des Abonnements ersetzt. 

![API-Aufrufe, wenn die Aktualisierung vom SaaS-Dienst initiiert wird.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Ausgesetzt

Dieser Status gibt an, dass die Zahlung des Kunden noch nicht empfangen wurde. Gemäß der Richtlinie gewähren wird dem Kunden eine Karenzzeit, bevor das Abonnement nicht erfüllt wird. Wenn sich ein Abonnement in diesem Zustand befindet, gibt es folgende Möglichkeiten: 

- Als unabhängiger Softwarehersteller können Sie den Zugriff des Benutzers auf den Dienst herabstufen oder sperren. 
- Das Abonnement muss in einem wiederherstellbaren Zustand gespeichert werden, damit die vollständige Funktionalität ohne Verlust von Daten oder Einstellungen wiederhergestellt werden kann. 
- Sie müssen über die Fulfillment-API eine Reaktivierungsanforderung für dieses Abonnement oder, am Ende der Karenzzeit, eine Anforderung zum Aufheben der Bereitstellung erhalten. 

#### <a name="unsubscribed"></a>Abonnement gekündigt 

Abonnements erreichen diesen Status infolge einer expliziten Kundenanforderung oder bei Nichtbezahlung der fälligen Beträge. Der ISV erwartet, dass die Kundendaten für eine Wiederherstellung auf Anforderung für mindestens x Tage gespeichert und dann gelöscht werden. 


## <a name="api-reference"></a>API-Referenz

In diesem Abschnitt werden die SaaS-*Abonnement-API* und die -*Vorgangs-API* dokumentiert.  Der Wert des Parameters `api-version` für Version 2 der APIs lautet `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Parameter- und Entitätsdefinitionen

Die folgende Tabelle enthält die Definitionen für allgemeine Parameter und Entitäten, die von Fulfillment-APIs verwendet werden.

|     Entität/Parameter     |     Definition                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | GUID-Bezeichner für eine SaaS-Ressource  |
| `name`                   | Der vom Kunden für diese Ressource angegebene Anzeigename |
| `publisherId`            | Eindeutiger Zeichenfolgenbezeichner für jeden Herausgeber, z. B. „Contoso“ |
| `offerId`                | Eindeutiger Zeichenfolgenbezeichner für jedes Angebot, z. B. „Angebot1“  |
| `planId`                 | Eindeutiger Zeichenfolgenbezeichner für jeden Plan bzw. jede SKU, z. B. „Silber“ |
| `operationId`            | GUID-Bezeichner für einen bestimmten Vorgang  |
|  `action`                | Die Aktion, die für eine Ressource ausgeführt wird, entweder `subscribe`, `unsubscribe`, `suspend`, `reinstate` oder `changePlan`, `changeQuantity`, `transfer`  |
|   |   |

Eindeutige Bezeichner (Globally Unique Identifier, [GUIDs](https://en.wikipedia.org/wiki/Universally_unique_identifier)) sind 128-Bit-Zahlen (32 hexadezimale Ziffern), die in der Regel automatisch generiert werden. 

#### <a name="resolve-a-subscription"></a>Auflösen eines Abonnements 

Der Auflösungsendpunkt ermöglicht Herausgebern das Auflösen eines Marketplace-Tokens in eine permanente Ressourcen-ID. Die Ressourcen-ID ist der eindeutige Bezeichner des SAAS-Abonnements.  Wenn ein Benutzer zur Website eines ISV weitergeleitet wird, enthält die URL in den Abfrageparametern ein Token. Es wird erwartet, dass der ISV dieses Token verwendet und eine Anforderung zu dessen Auflösung stellt. Die Antwort enthält die eindeutige ID des SAAS-Abonnements, den Namen, die Angebots-ID und den Plan für die Ressource. Dieses Token ist nur eine Stunde gültig. 

**Post:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*Abfrageparameter:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version des für diese Anforderung zu verwendenden Vorgangs.  |

*Anforderungsheader:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Typ      | `application/json` |
|  x-ms-requestid    |  Eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  x-ms-correlationid |  Eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  authorization     |  [Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  Der Tokenabfrageparameter in der URL, wenn der Benutzer von Azure zur Website des unabhängigen Softwareherstellers der SaaS-Anwendung umgeleitet wird (z. B. `https://contoso.com/signup?token=..`). *Hinweis:* Führen Sie eine URL-Decodierung des Tokenwerts aus dem Browser durch, bevor Sie ihn verwenden.  |

*Antwortcodes:*

Code: 200<br>
Löst das nicht transparente Token in ein SaaS-Abonnement auf.<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Code: 404<br>
Nicht gefunden

Code: 400<br>
Ungültige Anforderung. x-ms-marketplace-token fehlt, ist falsch formatiert oder ist abgelaufen.

Code: 403<br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört.

Code: 500<br>
Interner Serverfehler

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>Abonnement-API

Die Abonnement-API unterstützt die folgenden HTTPS-Vorgänge: **GET**, **POST**, **PATCH** und **DELETE**.


#### <a name="list-subscriptions"></a>Auflisten von Abonnements

Listet alle SaaS-Abonnements für einen Herausgeber auf.

**GET:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*Abfrageparameter:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Die Version des für diese Anforderung zu verwendenden Vorgangs.  |

*Anforderungsheader:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Typ       |  `application/json`  |
| x-ms-requestid     |  Eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| x-ms-correlationid |  Eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
| authorization      |  [Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Antwortcodes:*

Code: 200 <br/>
Anhand des Authentifizierungstokens werden der Herausgeber und die entsprechenden Abonnements für alle Angebote des Herausgebers abgerufen.<br> Antwortnutzlast:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Das Fortsetzungstoken ist nur vorhanden, wenn zusätzliche „Seiten“ von Pläne abgerufen werden sollen. 

Code: 403 <br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört. 

Code: 500 Interner Serverfehler

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Abrufen eines Abonnements

Ruft das angegebene SaaS-Abonnement ab. Verwenden Sie diesen Aufruf zum Abrufen von Lizenzinformationen und Plan-/Tarifinformationen.

**GET:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*Abfrageparameter:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Eindeutige ID des SaaS-Abonnements, die nach dem Auflösen des Tokens über die Auflösungs-API abgerufen wird.   |
|  ApiVersion        |   Version des für diese Anforderung zu verwendenden Vorgangs.   |

*Anforderungsheader:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Typ      |  `application/json`  |
|  x-ms-requestid    |  Eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  x-ms-correlationid |  Eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  authorization     |  [Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Antwortcodes:*

Code: 200<br>
Ruft das SaaS-Abonnement zur ID ab<br> Antwortnutzlast:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Code: 404<br>
Nicht gefunden<br> 

Code: 403<br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört.

Code: 500<br>
Interner Serverfehler<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Auflisten verfügbarer Pläne

Verwenden Sie diesen Aufruf, um herauszufinden, ob es private/öffentliche Angebote für den aktuellen Herausgeber gibt.

**GET:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*Abfrageparameter:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Version des für diese Anforderung zu verwendenden Vorgangs.  |

*Anforderungsheader:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Typ     |  `application/json` |
|   x-ms-requestid   |   Eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  x-ms-correlationid  | Eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  authorization     |  [Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Antwortcodes:*

Code: 200<br>
Abrufen einer Liste der verfügbaren Pläne für einen Kunden.<br>

Antworttext:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Code: 404<br>
Nicht gefunden<br> 

Code: 403<br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört. <br> 

Code: 500<br>
Interner Serverfehler<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Aktivieren eines Abonnements

**POST:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*Abfrageparameter:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version des für diese Anforderung zu verwendenden Vorgangs.  |
| subscriptionId     | Eindeutige ID des SaaS-Abonnements, die nach dem Auflösen des Tokens über die Auflösungs-API abgerufen wird.  |

*Anforderungsheader:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Typ      | `application/json`  |
|  x-ms-requestid    | Eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  x-ms-correlationid  | Eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Diese Zeichenfolge korreliert alle Ereignisse des Clientvorgangs mit Ereignissen auf der Serverseite. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  authorization     |  [Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Anforderung:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Antwortcodes:*

Code: 200<br>
Aktiviert das Abonnement.<br>

Code: 404<br>
Nicht gefunden

Code: 400<br>
Ungültige Anforderung – Fehler bei der Überprüfung

Code: 403<br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört.

Code: 500<br>
Interner Serverfehler

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Ändern des Plans für das Abonnement

Aktualisieren Sie den Plan für das Abonnement.

**PATCH:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Abfrageparameter:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Die Version des für diese Anforderung zu verwendenden Vorgangs.  |
| subscriptionId     | Eindeutige ID des SaaS-Abonnements, die nach dem Auflösen des Tokens über die Auflösungs-API abgerufen wird.  |

*Anforderungsheader:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Typ      | `application/json` |
|  x-ms-requestid    |   Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  x-ms-correlationid  |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.    |
| authorization      |  [Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Anforderungsnutzlast:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Anforderungsheader:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Link zu einer Ressource, um den Vorgangsstatus abzurufen.   |

*Antwortcodes:*

Code: 202<br>
Die Anforderung zum Ändern des Plans wurde akzeptiert. Vom unabhängigen Softwarehersteller wird erwartet, dass er „Operation-Location“ abfragt, um zu ermitteln, ob der Vorgang erfolgreich war oder fehlgeschlagen ist. <br>

Code: 404<br>
Nicht gefunden

Code: 400<br>
Ungültige Anforderung – Fehler bei der Überprüfung.

>[!Note]
>Es kann jeweils nur ein Plan oder eine Menge geändert werden, nicht beides gleichzeitig. Es wird ein Abonnement bearbeitet, bei dem **Aktualisieren** nicht zu den `allowedCustomerOperations` gehört.

Code: 403<br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört.

Code: 500<br>
Interner Serverfehler

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>Ändern der Menge für das Abonnement

Aktualisieren Sie die Menge für das Abonnement.

**PATCH:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Abfrageparameter:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Die Version des für diese Anforderung zu verwendenden Vorgangs.  |
| subscriptionId     | Eindeutige ID des SaaS-Abonnements, die nach dem Auflösen des Tokens über die Auflösungs-API abgerufen wird.  |

*Anforderungsheader:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Typ      | `application/json` |
|  x-ms-requestid    |   Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  x-ms-correlationid  |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.    |
| authorization      |  [Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Anforderungsnutzlast:*

```json
Request Body:
{
    "quantity": 5
}
```

*Anforderungsheader:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Link zu einer Ressource, um den Vorgangsstatus abzurufen.   |

*Antwortcodes:*

Code: 202<br>
Akzeptiert: Die Anforderung zum Ändern der Menge wurde akzeptiert. Vom unabhängigen Softwarehersteller wird erwartet, dass er „Operation-Location“ abfragt, um zu ermitteln, ob der Vorgang erfolgreich war oder fehlgeschlagen ist. <br>

Code: 404<br>
Nicht gefunden

Code: 400<br>
Ungültige Anforderung – Fehler bei der Überprüfung.

>[!Note]
>Es kann jeweils nur ein Plan oder eine Menge geändert werden, nicht beides gleichzeitig. Es wird ein Abonnement bearbeitet, bei dem **Aktualisieren** nicht zu den `allowedCustomerOperations` gehört.

Code: 403<br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört.

Code: 500<br>
Interner Serverfehler

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>Löschen eines Abonnements

Kündigen und löschen Sie das angegebene Abonnement.

**DELETE:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*Abfrageparameter:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Die Version des für diese Anforderung zu verwendenden Vorgangs.  |
| subscriptionId     | Eindeutige ID des SaaS-Abonnements, die nach dem Auflösen des Tokens über die Auflösungs-API abgerufen wird.  |

*Anforderungsheader:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Typ     |  `application/json` |
|  x-ms-requestid    |   Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.   |
|  x-ms-correlationid  |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.   |
|  authorization     |  [Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Antwortcodes:*

Code: 202<br>
Vom ISV initiierter Aufruf, um die Kündigung eines SaaS-Abonnements anzugeben.<br>

Code: 404<br>
Nicht gefunden

Code: 400<br>
Es wird ein Abonnement gelöscht, bei dem **DELETE** nicht zu den `allowedCustomerOperations` gehört.

Code: 403<br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört.

Code: 500<br>
Interner Serverfehler

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Vorgangs-API

Die Vorgangs-API unterstützt die folgenden PATCH- und GET-Vorgänge.

#### <a name="list-outstanding-operations"></a>Auflisten ausstehender Vorgänge 

Hiermit werden die ausstehenden Vorgänge für den aktuellen Herausgeber aufgelistet. 

**GET:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*Abfrageparameter:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Die Version des für diese Anforderung zu verwendenden Vorgangs.                |
| subscriptionId     | Eindeutige ID des SaaS-Abonnements, die nach dem Auflösen des Tokens über die Auflösungs-API abgerufen wird.  |

*Anforderungsheader:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Typ     |  `application/json` |
|  x-ms-requestid    |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  x-ms-correlationid |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  authorization     |  [Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Antwortcodes:*

Code: 200<br> Ruft die Liste der ausstehenden Vorgänge für ein Abonnement ab.<br>
Antwortnutzlast:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```

Code: 404<br>
Nicht gefunden

Code: 400<br>
Ungültige Anforderung – Fehler bei der Überprüfung

Code: 403<br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört.

Code: 500<br>
Interner Serverfehler

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Abrufen des Vorgangsstatus

Ermöglicht dem Herausgeber das Nachverfolgen des Status des angegebenen ausgelösten asynchronen Vorgangs (Subscribe/Unsubscribe/Change plan/ChangeQuantity).

**GET:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Abfrageparameter:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Die Version des für diese Anforderung zu verwendenden Vorgangs.  |

*Anforderungsheader:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Typ      |  `application/json`   |
|  x-ms-requestid    |   Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  x-ms-correlationid |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  authorization     |[Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Antwortcodes:* Code: 200<br> Ruft den angegebenen ausstehenden SaaS-Vorgang ab<br>
Antwortnutzlast:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Code: 404<br>
Nicht gefunden

Code: 400<br>
Ungültige Anforderung – Fehler bei der Überprüfung

Code: 403<br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört.
 
Code: 500<br> Interner Serverfehler

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Aktualisieren des Status eines Vorgangs

Aktualisieren Sie den Status eines Vorgangs, um den Erfolg oder Fehlschlag mit den bereitgestellten Werten anzugeben.

**PATCH:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Abfrageparameter:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Die Version des für diese Anforderung zu verwendenden Vorgangs.  |
| subscriptionId     | Eindeutige ID des SaaS-Abonnements, die nach dem Auflösen des Tokens über die Auflösungs-API abgerufen wird.  |
|  operationId       | Der Vorgang, der abgeschlossen wird. |

*Anforderungsheader:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Typ     | `application/json`   |
|   x-ms-requestid   |   Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  x-ms-correlationid |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  authorization     |  [Abrufen eines Tokens basierend auf der Azure AD-App](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Anforderungsnutzlast:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Antwortcodes:*

Code: 200<br> Aufruf, um über den ISV-seitigen Abschluss eines Vorgangs zu informieren. Diese Antwort kann z. B. die Änderung der Arbeitsplätze/Pläne anzeigen.

Code: 404<br>
Nicht gefunden

Code: 400<br>
Ungültige Anforderung – Fehler bei der Überprüfung

Code: 403<br>
Nicht autorisiert. Das Authentifizierungstoken wurde nicht bereitgestellt oder ist ungültig, oder die Anforderung versucht, auf einen Kauf zuzugreifen, der nicht zum aktuellen Herausgeber gehört.

Code: 409<br>
Konflikt. Beispiel: Eine neuere Transaktion wurde bereits erfüllt.

Code: 500<br> Interner Serverfehler

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>Webhook für den SaaS-Dienst

Der Herausgeber muss in diesen SaaS-Dienst einen Webhook implementieren, um proaktiv Benutzer über Änderungen beim Dienst zu benachrichtigen. Die API muss nicht authentifiziert werden und wird vom Microsoft-SaaS-Dienst aufgerufen. Der SaaS-Dienst muss die Vorgangs-API zur Überprüfung und Autorisierung aufrufen, bevor er auf die Webhookbenachrichtigung reagieren kann.

```json
{
  "id": "<this is a Guid operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Eine der folgenden Aktionen kann verwendet werden: 
- `Subscribe` (wenn die Ressource aktiviert wurde)
- `Unsubscribe` (wenn die Ressource gelöscht wurde)
- `ChangePlan` (wenn der Vorgang zum Ändern des Plans abgeschlossen wurde)
- `ChangeQuantity` (wenn der Vorgang zum Ändern der Menge abgeschlossen wurde)
- `Suspend` (wenn die Ressource angehalten wurde)
- `Reinstate` (wenn die Ressource nach dem Anhalten reaktiviert wurde)

Folgende Statuswerte sind möglich: <br>
        – NotStarted <br>
        – InProgress <br>
        – Succeeded <br>
        – Failed <br>
        – Conflict <br>

Handlungsrelevante Status sind „Succeeded“ und „Failed“ in einer Webhookbenachrichtigung. Der Lebenszyklus eines Vorgangs verläuft von „NotStarted“ bis zu einem Endzustand wie „Succeeded“, „Failed“ oder „Conflict“. Wenn „NotStarted“ oder „InProgress“ angezeigt wird, fordern Sie den Status so lange über eine GET-Vorgangs-API an, bis der Vorgang einen Endzustand erreicht hat, bevor Sie Maßnahmen ergreifen. 

## <a name="mock-api"></a>Modell-API

Für erste Entwicklungsschritte, insbesondere zum Erstellen von Prototypen und zum Testen von Projekten, können Sie unsere Modell-APIs verwenden. 

Host-Endpunkt: `https://marketplaceapi.microsoft.com/api` <br/>
API-Version: `2018-09-15` <br/>
Keine Authentifizierung erforderlich <br/>
Beispiel-URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Die API-Endpunktpfade für Modell- und echte APIs sind identisch, aber die API-Versionen unterscheiden sich. Für Modell-APIs wird die Version „2018-09-15“ und für die Produktion die Version „2018-08-31“ verwendet. 

Alle API-Aufrufe in diesem Artikel können an den Endpunkt des Modellhosts gerichtet werden. Erwartungsgemäß werden Modelldaten als Antwort zurückgegeben. Im Allgemeinen können Sie erwarten, dass Sie Modelldaten als Antwort erhalten. Für Aufrufe der Methoden zum Aktualisieren des Abonnements gibt die Modell-API immer 500 zurück. 

## <a name="next-steps"></a>Nächste Schritte

Entwicklern steht außerdem das programmgesteuerte Abrufen und Bearbeiten von Workloads, Angeboten und Herausgeberprofilen mit den [Cloud-Partnerportal-REST-APIs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) zur Verfügung.
