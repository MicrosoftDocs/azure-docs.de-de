---
title: SaaS-Fulfillment-APIs V2 im kommerziellen Microsoft-Marketplace
description: Hier erfahren Sie, wie Sie ein SaaS-Angebot in Microsoft AppSource und im Azure Marketplace mithilfe der Fulfillment-APIs der Version 2 erstellen und verwalten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 1ea326cc4537176c0ddcff070f4dc3b3f77f4b58
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512034"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>SaaS-Fulfillment-APIs (Version 2) im kommerziellen Marketplace

In diesem Artikel werden die APIs ausführlich beschrieben, mit denen Partner ihre SaaS-Angebote (Software-as-a-Service) in Microsoft AppSource und im Azure Marketplace verkaufen können. Ein Herausgeber muss die Integration in diese APIs implementieren, um ein transaktionsfähiges SaaS-Angebot im Partner Center zu veröffentlichen.

## <a name="managing-the-saas-subscription-life-cycle"></a>Verwalten des Lebenszyklus von SaaS-Abonnements

Im kommerziellen Marketplace wird der gesamte Lebenszyklus eines SaaS-Abonnements nach Erwerb durch den Endkunden verwaltet. Die Angebotsseite, die Fulfillment- und Vorgangs-APIs sowie der Webhook dienen als Mechanismen zur tatsächlichen Aktivierung, Nutzung, Aktualisierung und Kündigung des SaaS-Abonnements. Die Rechnung des Endbenutzers basiert auf dem Zustand des von Microsoft verwalteten SaaS-Abonnements. 

### <a name="states-of-a-saas-subscription"></a>Zustände eines SaaS-Abonnements

Das folgende Diagramm zeigt die Zustände eines SaaS-Abonnements und die zugehörigen Aktionen.

![Diagramm des Lebenszyklus eines Software-as-a-Service-Abonnements im Marketplace.](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Gekauft, aber noch nicht aktiviert (*PendingFulfillmentStart*)

Nachdem ein Endbenutzer (oder Cloud-Dienstanbieter) ein SaaS-Angebot im kommerziellen Marketplace gekauft hat, muss der Herausgeber über den Kauf benachrichtigt werden. Der Herausgeber kann dann auf seiner Seite ein neues SaaS-Konto für den Endbenutzer erstellen und konfigurieren.

Ablauf für die Kontoerstellung:

1. Der Kunde wählt die Schaltfläche **Konfigurieren** aus, die nach erfolgreichem Kauf in Microsoft AppSource oder im Azure-Portal für ein SaaS-Angebot gezeigt wird. Alternativ kann der Kunde die Schaltfläche **Konfigurieren** in der E-Mail auswählen, die er kurz nach dem Kauf erhält.
2. Microsoft benachrichtigt den Partner über den Kauf, indem die URL der Angebotsseite mit dem Tokenparameter (dem Identifizierungstoken für den Kauf im kommerziellen Marketplace) auf der neuen Browserregisterkarte geöffnet wird.

Ein Beispiel eines solchen Aufrufs ist `https://contoso.com/signup?token=<blob>`, wobei die URL der Angebotsseite für dieses SaaS-Angebot im Partner Center als `https://contoso.com/signup` konfiguriert ist. Dieses Token stellt dem Herausgeber eine ID bereit, die den SaaS-Kauf und den Kunden eindeutig identifiziert.

>[!NOTE]
>Der Herausgeber wird erst dann über den SaaS-Kauf benachrichtigt, nachdem der Kunde den Konfigurationsprozess auf Microsoft-Seite eingeleitet hat.

Die URL der Angebotsseite muss rund um die Uhr aktiv sein und jederzeit neue Aufrufe von Microsoft empfangen können. Wenn die Angebotsseite nicht mehr verfügbar ist, können sich Kunden nicht mehr für den SaaS-Dienst registrieren und mit dessen Nutzung beginnen.

Als Nächstes muss der Herausgeber das *-Token* an Microsoft zurückgeben, indem er die [SaaS-Auflösungs-API](#resolve-a-purchased-subscription) aufruft und das Token als Wert des Headerparameters `x-ms-marketplace-token header` eingibt. Durch den Aufruf der Auflösungs-API wird das Token durch die Details des SaaS-Kaufs ersetzt, z. B. eindeutige ID des Kaufs, ID des erworbenen Angebots und ID des erworbenen Plans.

Auf der Angebotsseite sollte der Kunde über einmaliges Anmelden (Single Sign-On, SSO) bei Azure Active Directory (Azure AD) beim neuen oder vorhandenen SaaS-Konto angemeldet sein.

Der Herausgeber muss SSO implementieren, um die von Microsoft für diesen Flow benötigte Benutzeroberfläche bereitzustellen. Stellen Sie sicher, dass Sie beim Konfigurieren von SSO eine mehrinstanzenfähige Azure AD-Anwendung verwenden und sowohl Geschäfts-, Schul- oder Unikonten als auch persönliche Microsoft-Konten zulassen. Diese Anforderung gilt nur für die Angebotsseite und Benutzer, die zum SaaS-Dienst umgeleitet werden, wenn sie bereits mit Microsoft-Anmeldeinformationen angemeldet sind. SSO ist nicht für alle Anmeldungen beim SaaS-Dienst erforderlich.

> [!NOTE]
>Wenn SSO anfordert, dass ein Administrator die Berechtigung für eine App erteilt, muss in der Beschreibung des Angebots im Partner Center angegeben sein, dass Zugriff auf Administratorebene erforderlich ist. Dadurch werden die [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies#10003-authentication-options) eingehalten.

Nach der Anmeldung muss der Kunde die SaaS-Konfiguration auf Herausgeberseite vervollständigen. Anschließend muss der Herausgeber die [API zum Aktivieren des Abonnements](#activate-a-subscription) aufrufen, um ein Signal an Azure Marketplace zu senden, dass die Bereitstellung des SaaS-Kontos abgeschlossen ist.
Dadurch wird der Abrechnungszeitraum des Kunden gestartet. Wird die API zum Aktivieren des Abonnements nicht erfolgreich aufgerufen, wird dem Kunden der Kauf nicht in Rechnung gestellt.


![Diagramm mit den API-Aufrufen für ein Bereitstellungsszenario.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Aktiv (*Abonniert*)

*Aktiv (Abonniert)* ist der stabile Zustand eines bereitgestellten SaaS-Abonnements. Nachdem der Aufruf der [API zum Aktivieren des Abonnements](#activate-a-subscription) auf Microsoft-Seite verarbeitet wurde, wird das SaaS-Abonnement als *Abonniert* gekennzeichnet. Der Kunde kann nun den SaaS-Dienst auf Herausgeberseite verwenden, der ihm in Rechnung gestellt wird.

Wenn ein SaaS-Abonnement bereits aktiv ist, kann der Kunde im Azure-Portal oder Microsoft 365 Admin Center die Option zum **Verwalten der SaaS-Umgebung** auswählen. Diese Aktion bewirkt auch, dass Microsoft die **URL der Angebotsseite** mit dem *Tokenparameter* wie beim Flow „Aktivieren“ aufruft. Der Herausgeber muss zwischen neuen Käufen und der Verwaltung vorhandener SaaS-Konten unterscheiden und diesen Aufruf der URL der Angebotsseite entsprechend handhaben.

#### <a name="being-updated-subscribed"></a>Wird aktualisiert (*Abonniert*)

Diese Aktion bedeutet, dass eine Aktualisierung eines vorhandenen aktiven SaaS-Abonnements sowohl von Microsoft als auch vom Herausgeber verarbeitet wird. Eine solche Aktualisierung kann auf folgende Arten initiiert werden:

- Vom Kunden über den kommerziellen Marketplace
- Vom Cloud-Dienstanbieter über den kommerziellen Marketplace
- Vom Kunden über die SaaS-Website des Herausgebers (gilt nicht für Käufe des Cloud-Dienstanbieters)

Für ein SaaS-Abonnement sind zwei Arten von Aktualisierungen verfügbar:

- Aktualisierung des Plans, wenn der Kunde einen anderen Plan für das Abonnement auswählt
- Aktualisierung der Menge, wenn der Kunde die Anzahl erworbener Arbeitsplätze für das Abonnement ändert

Nur ein aktives Abonnement kann aktualisiert werden. Während das Abonnement aktualisiert wird, lautet der Status auf Microsoft-Seite weiterhin „Aktiv“.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>Über den kommerziellen Marketplace eingeleitetes Update

Bei diesem Flow ändert der Kunde den Abonnementplan oder die Anzahl von Arbeitsplätzen über das Azure-Portal oder das Microsoft 365 Admin Center.

1. Nachdem eine Aktualisierung eingegeben wurde, ruft Microsoft die Webhook-URL des Herausgebers, die im Feld **Verbindungswebhook** im Partner Center konfiguriert ist, mit einem geeigneten Wert für *action* und anderen relevanten Parametern auf. 
1. Auf Herausgeberseite müssen die erforderlichen Änderungen am SaaS-Dienst vorgenommen werden. Microsoft muss nach Abschluss des Vorgangs durch Aufrufen der [API zum Aktualisieren des Vorgangsstatus](#update-the-status-of-an-operation) benachrichtigt werden.
1. Wenn der Patch mit dem Status *Fehler* gesendet wird, wird der Aktualisierungsvorgang auf Microsoft-Seite nicht abgeschlossen. Das SaaS-Abonnement weist weiterhin den vorhandenen Plan und die vorhandene Menge von Arbeitsplätzen auf.

> [!NOTE]
> Der Herausgeber muss *innerhalb eines zehnsekündigen Zeitfensters* nach Empfang der Webhookbenachrichtigung PATCH für die [API zum Aktualisieren des Vorgangsstatus](#update-the-status-of-an-operation) mit der Antwort „Fehler/Erfolg“ aufrufen. Wird „PATCH“ für den Vorgangsstatus nicht innerhalb von zehn Sekunden empfangen, wird der Änderungsplan *automatisch als Erfolg gepatcht*. 

Die Abfolge der API-Aufrufe für ein Aktualisierungsszenario, das über den kommerziellen Marketplace eingeleitet wird, ist in der folgenden Abbildung dargestellt.

![Diagramm der API-Aufrufe für eine über den Marketplace eingeleitete Aktualisierung.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Über den Herausgeber initiierte Aktualisierung

Bei diesem Ablauf ändert der Kunde den Abonnementplan oder die Menge der erworbenen Arbeitsplätze über den SaaS-Dienst selbst. 

1. Der Herausgebercode muss die [Planänderungs-API](#change-the-plan-on-the-subscription) und/oder die [Mengenänderungs-API](#change-the-quantity-of-seats-on-the-saas-subscription) aufrufen, bevor die angeforderte Änderung auf Herausgeberseite vorgenommen wird. 

1. Microsoft wendet die Änderung auf das Abonnement an und fordert dann den Herausgeber über den **Verbindungswebhook** auf, die gleiche Änderung vorzunehmen.

1. Erst dann muss der Herausgeber die erforderliche Änderung am SaaS-Abonnement durchführen und Microsoft durch Aufrufen der [API zum Aktualisieren des Vorgangsstatus](#update-the-status-of-an-operation) benachrichtigen, sobald die Änderung erfolgt ist.

Die Abfolge der API-Aufrufe für ein Aktualisierungsszenario, das auf Herausgeberseite eingeleitet wird, ist in der folgenden Abbildung dargestellt.

![Diagramm der API-Aufrufe für eine auf Herausgeberseite eingeleitete Aktualisierung.](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Ausgesetzt (*Suspended*)

Dieser Status gibt an, dass die Zahlung eines Kunden für den SaaS-Dienst noch nicht eingegangen ist. Der Herausgeber wird von Microsoft über diese Änderung des SaaS-Abonnementstatus benachrichtigt. Die Benachrichtigung erfolgt über einen Aufruf des Webhooks, wobei der Parameter *action* auf *Suspended* festgelegt ist.

Der Herausgeber kann auf Wunsch Änderungen am SaaS-Dienst auf Herausgeberseite vornehmen. Es wird empfohlen, dass der Herausgeber diese Informationen dem jeweiligen Kunden bereitstellt und den Zugriff des Kunden auf den SaaS-Dienst einschränkt oder blockiert. Es besteht die Möglichkeit, dass die Zahlung niemals eingeht.

Microsoft gewährt dem Kunden eine Karenzzeit von 30 Tagen, bevor das Abonnement automatisch gekündigt wird. Wenn sich ein Abonnement im Status *Suspended* (Ausgesetzt) befindet, ist Folgendes gegeben:

* Der Partner oder ISV muss das SaaS-Konto in einem wiederherstellbaren Zustand halten, damit die vollständige Funktionalität ohne Verlust von Daten oder Einstellungen wiederhergestellt werden kann.
* Der Partner oder ISV muss mit einer Anforderung zur Reaktivierung des Abonnements rechnen, wenn die Zahlung während der Toleranzperiode eingeht, oder mit einer Anforderung zur Aufhebung der Bereitstellung des Abonnements nach Ablauf der Toleranzperiode. Beide Anforderungen werden über den Webhookmechanismus gesendet.

Der Abonnementstatus wird auf Microsoft-Seite in „Ausgesetzt“ geändert, bevor der Herausgeber eine Aktion ausführt. Nur aktive Abonnements können ausgesetzt werden.

#### <a name="reinstated-suspended"></a>Reaktiviert (*Suspended*)

Diese Aktion gibt an, dass das Zahlungsinstrument des Kunden wieder gültig geworden ist, eine Zahlung für das SaaS-Abonnement erfolgt ist und das Abonnement reaktiviert wurde. In diesem Fall: 

1. Microsoft ruft den Webhook mit einem *action*-Parameter auf, der auf den Wert *Reinstate* festgelegt ist.
1. Der Herausgeber stellt sicher, dass das Abonnement auf Herausgeberseite wieder voll funktionsfähig ist.
1. Der Herausgeber ruft die [Patchvorgang-API](#update-the-status-of-an-operation) mit dem Status „Erfolgreich“ auf.
1. Der Reaktivierungsprozess ist erfolgreich, und dem Kunden wird das SaaS-Abonnement wieder in Rechnung gestellt. 

Wenn der Patch mit dem Status *Fail* (Fehler) gesendet wird, wird der Reaktivierungsprozess auf Microsoft-Seite nicht beendet, sodass das Abonnement *Suspended* (Ausgesetzt) bleibt.

Nur ein ausgesetztes Abonnement kann reaktiviert werden. Das ausgesetzte SaaS-Abonnement verbleibt im Zustand *Suspended* (Ausgesetzt), während der Dienst reaktiviert wird. Sobald dieser Vorgang abgeschlossen ist, lautet der Status des Abonnements wieder *Active* (Aktiv).

#### <a name="renewed-subscribed"></a>Verlängert (*Subscribed*)

Am Ende der Abonnementlaufzeit von einem Monat oder einem Jahr wird das SaaS-Abonnement automatisch von Microsoft verlängert. Die Standardeinstellung für die automatische Verlängerung lautet für alle SaaS-Abonnements *TRUE*. Aktive SaaS-Abonnements werden weiter in regelmäßigen Abständen verlängert. Microsoft benachrichtigt den Herausgeber nicht, wenn ein Abonnement verlängert wird. Ein Kunde kann die automatische Verlängerung eines SaaS-Abonnements über das Microsoft 365-Verwaltungsportal deaktivieren. In diesem Fall wird das SaaS-Abonnement am Ende des aktuellen Abrechnungszeitraums automatisch gekündigt. Kunden können das SaaS-Abonnement auch jederzeit kündigen.

Nur aktive Abonnements werden automatisch verlängert. Abonnements bleiben während des Verlängerungsvorgangs und bei erfolgreicher automatischer Verlängerung aktiv. Nach der Verlängerung werden das Start- und Enddatum der Abonnementlaufzeit in die Datumsangaben der neuen Laufzeit geändert.

Wenn bei einer automatischen Verlängerung aufgrund eines Zahlungsproblems ein Fehler auftritt, wechselst das Abonnement in den Zustand *Suspended* (Ausgesetzt), worüber der Herausgeber informiert wird.

#### <a name="canceled-unsubscribed"></a>Gekündigt (*Unsubscribed*) 

Abonnements erreichen diesen Zustand entweder als Reaktion auf eine explizite Aktion des Kunden oder Cloud-Dienstanbieters oder durch Kündigung eines Abonnements über die Website des Herausgebers, das Azure-Portal oder das Microsoft 365 Admin Center. Ein Abonnement kann auch bei Nichtzahlung der fälligen Beträge implizit gekündigt werden, nachdem es 30 Tage den Status *Suspended* (Ausgesetzt) aufwies.

Nachdem der Herausgeber einen Webhookaufruf zur Kündigung empfangen hat, muss er die Kundendaten für die Wiederherstellung auf Anforderung mindestens sieben Tage aufbewahren. Erst dann können Kundendaten gelöscht werden.

Ein SaaS-Abonnement kann jederzeit während des Lebenszyklus gekündigt werden. Ein einmal gekündigtes Abonnement kann nicht reaktiviert werden.

## <a name="api-reference"></a>API-Referenz

In diesem Abschnitt werden die SaaS-Abonnement-APIs und SaaS-Vorgangs-APIs beschrieben.

**Abonnement-APIs** sollten zur Handhabung des Lebenszyklus des SaaS-Abonnements vom Kauf bis zur Kündigung verwendet werden.

**Vorgangs-APIs** sollten für Folgendes verwendet werden:

* Überprüfen und Bestätigen der verarbeiteten Webhookaufrufe
* Abrufen einer Liste ausstehender Vorgänge für Apps, die auf Bestätigung durch den Herausgeber warten

> [!NOTE]
> Die TLS-Version 1.2 wird bald als Mindestversion für die HTTPS-Kommunikation erzwungen. Stellen Sie sicher, dass Sie diese TLS-Version in Ihrem Code verwenden. Die TLS-Versionen 1.0 und 1.1 werden in Kürze nicht mehr unterstützt.

### <a name="subscription-apis"></a>Abonnement-APIs

#### <a name="resolve-a-purchased-subscription"></a>Auflösen eines erworbenen Abonnements

Der Auflösungsendpunkt ermöglicht dem Herausgeber, das Identifizierungstoken für den Kauf aus dem kommerziellen Marketplace (im Abschnitt [Gekauft, aber noch nicht aktiviert](#purchased-but-not-yet-activated-pendingfulfillmentstart) als *Token* bezeichnet) gegen eine persistente ID für das erworbene SaaS-Abonnement und dessen Details auszutauschen.

Wenn ein Kunde zur URL der Angebotsseite des Partners weitergeleitet wird, wird das Identifizierungstoken des Kunden in diesem URL-Aufruf als Parameter *token* übergeben. Es wird erwartet, dass der Partner dieses Token verwendet und dessen Auflösung anfordert. Die Antwort der Auflösungs-API enthält die SaaS-Abonnement-ID und andere Details zur eindeutigen Identifizierung des Kaufs. Das mit dem Aufruf der URL der Angebotsseite angegebene *Token* ist in der Regel 24 Stunden gültig. Wenn das von Ihnen erhaltene *Token* bereits abgelaufen ist, wird empfohlen, dem Endkunden die folgenden Anweisungen bereitzustellen:

„Dieser Kauf konnte nicht identifiziert werden. Öffnen Sie dieses Saas-Abonnement im Azure-Portal oder Microsoft 365 Admin Center, und wählen Sie nochmals „Konto konfigurieren“ oder „Konto verwalten“ aus.

Durch das Aufrufen der Auflösungs-API werden Abonnementdetails und der Status von SaaS-Abonnements in allen unterstützten Status zurückgegeben.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.   |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet. Das Format ist `"Bearer <accessaccess_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |
|  `x-ms-marketplace-token`  | Der aufzulösende Parameter *token* zur Identifizierung des Kaufs.  Das Token wird im Aufruf der URL der Angebotsseite übergegeben, wenn der Kunde zur Website des SaaS-Partners weitergeleitet wird (z. B. `https://contoso.com/signup?token=<token><authorization_token>`). <br> <br>  Beachten Sie, dass der Wert von *token* als Teil der URL der Angebotsseite codiert ist und daher decodiert werden muss, bevor er als Parameter in diesem API-Aufruf verwendet wird.  <br> <br> Ein Beispiel einer codierten Zeichenfolge in der URL sieht wie folgt aus: `contoso.com/signup?token=ab%2Bcd%2Fef`, wobei `ab%2Bcd%2Fef` das *Token* ist.  Das gleiche Token sieht decodiert wie folgt aus: `Ab+cd/ef` |
| | |

*Antwortcodes:*

Code: 200 – Gibt eindeutige SaaS-Abonnement-IDs basierend auf dem bereitgestellten `x-ms-marketplace-token` zurück.

Beispiel für einen Antworttext:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Code: 400 – Ungültige Anforderung. `x-ms-marketplace-token` fehlt, ist falsch formatiert, ungültig oder abgelaufen.

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht wurde als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Aktivieren eines Abonnements

Nachdem das SaaS-Konto für einen Endkunden konfiguriert wurde, muss der Herausgeber die API zum Aktivieren des Abonnements auf Microsoft-Seite aufrufen.  Dem Kunden werden erst dann Beträge in Rechnung gestellt, wenn dieser API-Aufruf erfolgreich war.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Verwenden Sie 2018-08-31.   |
| `subscriptionId` | Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der [Auflösungs-API](#resolve-a-purchased-subscription) aufgelöst wurde.
 |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Diese Zeichenfolge korreliert alle Ereignisse des Clientvorgangs mit Ereignissen auf der Serverseite.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `authorization`      |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet. Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |

*Beispiel einer Anforderungsnutzlast:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Antwortcodes:*

Code: 200: Das Abonnement wurde auf Microsoft-Seite als „Abonniert“ gekennzeichnet.

Es ist kein Antworttext für diesen Aufruf vorhanden.

Code: 400 – Ungültige Anforderung: Fehler bei der Überprüfung.

* `planId` ist nicht in den Nutzdaten der Anforderung vorhanden.
* `planId` in den Nutzdaten der Anforderung stimmt nicht mit derjenigen ein, die erworben wurde.
* `quantity` in den Nutzdaten der Anforderung stimmt nicht mit derjenigen ein, die erworben wurde.
* Das SaaS-Abonnement weist den Status *Abonniert* oder *Ausgesetzt* auf.

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben. Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht wurde als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 404 – Nicht gefunden. Das SaaS-Abonnement weist den Status *Gekündigt* auf.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Abrufen einer Liste aller Abonnements

Diese API ruft eine Liste aller erworbenen SaaS-Abonnements für alle Angebote ab, die der Herausgeber im kommerziellen Marketplace veröffentlicht.  SaaS-Abonnements in allen möglichen Status werden zurückgegeben. Gekündigte SaaS-Abonnements werden ebenfalls zurückgegeben, da diese Informationen auf Microsoft-Seite nicht gelöscht werden.

Diese API gibt paginierte Ergebnisse zurück (100 pro Seite).

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Verwenden Sie 2018-08-31.  |
| `continuationToken`  | Dieser Parameter ist optional. Wenn Sie die erste Seite der Ergebnisse abrufen möchten, lassen Sie den Parameter leer.  Verwenden Sie den Wert, der im Parameter `@nextLink` zurückgegeben wird, um die nächste Seite abzurufen. |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `authorization`      |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |

*Antwortcodes:*

Code: 200: Gibt die Liste aller vorhandenen Abonnements für alle Angebote dieses Herausgebers basierend auf dessen Autorisierungstoken zurück.

*Beispiel für einen Antworttext:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Wenn keine erworbenen SaaS-Abonnements für diesen Herausgeber gefunden werden, wird ein leerer Antworttext zurückgegeben.

Code: 403 – Unzulässig. Das Autorisierungstoken ist nicht verfügbar, ungültig oder abgelaufen.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird. 

Code: 500 – Interner Serverfehler. Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-subscription"></a>Abrufen eines Abonnements

Diese API ruft ein angegebenes SaaS-Abonnements für ein SaaS-Angebot ab, das der Herausgeber im kommerziellen Marketplace veröffentlicht. Rufen Sie mithilfe dieses Aufrufs alle verfügbaren Informationen zu einem bestimmten SaaS-Abonnement anhand seiner ID ab, anstatt die API aufzurufen, mit der eine Liste aller Abonnements abgerufen werden kann.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Verwenden Sie 2018-08-31. |
| `subscriptionId`     |  Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde. |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `authorization`     | Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet. Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist.  |

*Antwortcodes:*

Code: 200 – Gibt basierend auf der bereitgestellten `subscriptionId` Details für ein SaaS-Abonnement zurück.

*Beispiel für einen Antworttext:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben. Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird. 

Code: 404 – Nicht gefunden.  Das SaaS-Abonnement mit der angegebenen `subscriptionId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Auflisten verfügbarer Pläne

Diese API ruft alle Pläne für ein SaaS-Angebot ab, das durch die `subscriptionId` eines bestimmten Erwerbs dieses Angebots bestimmt wird.  Verwenden Sie diesen Aufruf, um eine Liste aller privaten und öffentlichen Pläne abzurufen, die der Begünstigte eines SaaS-Abonnements für das Abonnement aktualisieren kann.  Die zurückgegebenen Pläne sind in derselben Region wie der bereits erworbene Plan verfügbar.

Dieser Aufruf gibt eine Liste der Pläne zurück, die für diesen Kunden zusätzlich zu dem bereits erworbenen Plan verfügbar sind.  Die Liste kann einem Endbenutzer auf der Website des Herausgebers angezeigt werden.  Ein Endbenutzer kann den Abonnementplan in einen beliebigen Plan in der zurückgegebenen Liste ändern.  Beim Ändern des Plans in einen nicht in der Liste aufgeführten Plan tritt ein Fehler auf.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.  |
|  `subscriptionId`    |  Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde. |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `x-ms-correlationid`  |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist.  |

*Antwortcodes:*

Code: 200 – Gibt eine Liste aller verfügbaren Pläne für ein vorhandenes SaaS-Abonnement einschließlich des bereits erworbenen Plans zurück.

Beispiel für einen Antworttext:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Wenn `subscriptionId` nicht gefunden wird, wird ein leerer Antworttext zurückgegeben.

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht möglicherweise, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird. 

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Ändern des Plans für das Abonnement

Diese API dient zum Ändern des vorhandenen Plans, der für ein SaaS-Abonnement erworben wurde, in einen neuen Plan (öffentlich oder privat).  Der Herausgeber muss diese API aufrufen, wenn ein Plan auf Herausgeberseite für ein im kommerziellen Marketplace erworbenes SaaS-Abonnement geändert wird.

Diese API kann nur für *aktive* Abonnements aufgerufen werden.  Jeder Plan kann in einen anderen vorhandenen Plan (öffentlich oder privat), aber nicht in sich selbst geändert werden.  Bei privaten Plänen muss der Mandant des Kunden im Partner Center als Teil der Zielgruppe für den Plan definiert werden.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.  |
| `subscriptionId`     | Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde. |

*Anforderungsheader:*
 
|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `x-ms-correlationid`  | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |

*Beispiel einer Anforderungsnutzlast:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Antwortcodes:*

Code: 202 – Die Anforderung zum Ändern des Plans wurde akzeptiert und asynchron verarbeitet.  Vom Partner wird erwartet, dass er die **Standort-URL für den Vorgang** abfragt, um zu ermitteln, ob die Änderungsanforderung für den Plan erfolgreich war oder fehlgeschlagen ist.  Die Abfrage sollte alle paar Sekunden durchgeführt werden, bis der endgültige Status *Fehler*, *Erfolgreich* oder *Konflikt* für den Vorgang empfangen wird.  Der endgültige Vorgangsstatus sollte schnell zurückgegeben werden, doch kann dies in einigen Fällen mehrere Minuten dauern.

Der Partner erhält auch eine Webhookbenachrichtigung, wenn die Aktion auf Seite des kommerziellen Marketplace erfolgreich abgeschlossen werden kann.  Erst dann sollte der Herausgeber die Planänderung auf Herausgeberseite vornehmen.

*Antwortheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL zum Abrufen des Vorgangsstatus.  Beispiel: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code: 400 – Ungültige Anforderung: Fehler bei der Überprüfung.

* Der neue Plan ist nicht vorhanden oder für dieses bestimmte SaaS-Abonnement nicht verfügbar.
* Der neue Plan ist mit dem aktuellen Plan identisch.
* Der Status des SaaS-Abonnements ist nicht *Abonniert*.
* Der Aktualisierungsvorgang für ein SaaS-Abonnement ist nicht in `allowedCustomerOperations` enthalten.

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 404 – Nicht gefunden.  Das SaaS-Abonnement mit `subscriptionId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>Es kann jeweils nur der Plan oder die Menge von Arbeitsplätzen geändert werden, nicht beides gleichzeitig.

>[!Note]
>Diese API kann nur nach ausdrücklicher Genehmigung der Änderung durch den Endbenutzer aufgerufen werden.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Ändern der Menge von Arbeitsplätzen für das SaaS-Abonnement

Diese API dient zum Ändern (Erhöhen oder Verringern) der Menge von Arbeitsplätzen, die für ein SaaS-Abonnement erworben wurden.  Der Herausgeber muss diese API aufrufen, wenn die Menge der Arbeitsplätze für ein im kommerziellen Marketplace erstelltes SaaS-Abonnement über die Herausgeberseite geändert wird.

Die Menge der Arbeitsplätze darf die im aktuellen Plan zulässigen Menge nicht übersteigen.  In diesem Fall muss der Herausgeber den Plan ändern, bevor die Anzahl der Arbeitsplätze geändert wird.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.  |
|  `subscriptionId`     | Ein eindeutiger Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde.  |

*Anforderungsheader:*
 
|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `x-ms-correlationid`  | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     | Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist.  |

*Beispiel einer Anforderungsnutzlast:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Antwortcodes:*

Code: 202 – Die Anforderung zum Ändern der Menge wurde akzeptiert und asynchron verarbeitet. Vom Partner wird erwartet, dass er die **Standort-URL für den Vorgang** abfragt, um zu ermitteln, ob die Änderungsanforderung für die Menge erfolgreich war oder fehlgeschlagen ist.  Die Abfrage sollte alle paar Sekunden durchgeführt werden, bis der endgültige Status *Fehler*, *Erfolgreich* oder *Konflikt* für den Vorgang empfangen wird.  Der endgültige Vorgangsstatus sollte schnell zurückgegeben werden, doch kann dies in einigen Fällen mehrere Minuten dauern.

Der Partner erhält auch eine Webhookbenachrichtigung, wenn die Aktion auf Seite des kommerziellen Marketplace erfolgreich abgeschlossen werden kann.  Erst dann sollte der Herausgeber die Mengenänderung auf Herausgeberseite vornehmen.

*Antwortheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Link zu einer Ressource, um den Vorgangsstatus abzurufen.  Beispiel: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Code: 400 – Ungültige Anforderung: Fehler bei der Überprüfung.

* Die neue Menge ist größer oder kleiner als das Limit für den aktuellen Plan.
* Die neue Menge fehlt.
* Die neue Menge ist identisch mit der aktuellen Menge.
* Der Status des SaaS-Abonnements ist nicht „Abonniert“.
* Der Aktualisierungsvorgang für ein SaaS-Abonnement ist nicht in `allowedCustomerOperations` enthalten.

Code: 403 – Unzulässig.  Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht, auf ein Abonnement zuzugreifen, das nicht dem aktuellen Herausgeber gehört.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird. 

Code: 404 – Nicht gefunden.  Das SaaS-Abonnement mit `subscriptionId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>Es kann jeweils nur ein Plan oder eine Menge geändert werden, nicht beides gleichzeitig.

>[!Note]
>Diese API kann nur nach ausdrücklicher Genehmigung der Änderung durch den Endbenutzer aufgerufen werden.

#### <a name="cancel-a-subscription"></a>Kündigen eines Abonnements

Diese API dient zum Kündigen eines angegebenen SaaS-Abonnements.  Der Herausgeber muss diese API nicht verwenden, und es wird empfohlen, die Kunden zum Kündigen von SaaS-Abonnements an den kommerziellen Marketplace weiterzuleiten.

Wenn der Herausgeber beschließt, die Kündigung von im kommerziellen Marketplace erworbenen SaaS-Abonnements auf seiner Website zu implementieren, muss er diese API aufrufen.  Nach Abschluss dieses Aufrufs lautet der Status des Abonnements auf Microsoft-Seite *Gekündigt*.

Wenn ein Abonnement innerhalb der folgenden Toleranzperiode gekündigt wird, wird es dem Kunden nicht in Rechnung gestellt:

* Bei einem Monatsabonnement 24 Stunden nach Aktivierung
* Bei einem Jahresabonnement 14 Tage nach Aktivierung

Bei Kündigung eines Abonnements nach Ablauf der oben genannten Toleranzperioden wird das Abonnement dem Kunden in Rechnung gestellt.  Der Kunde verliert unmittelbar nach der Kündigung den Zugriff auf das SaaS-Abonnement auf Microsoft-Seite. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.  |
|  `subscriptionId`     | Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde.  |

*Anforderungsheader:*
 
|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `x-ms-correlationid`  | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |

*Antwortcodes:*

Code: 202 – Die Anforderung zum Kündigen wurde akzeptiert und asynchron verarbeitet.  Vom Partner wird erwartet, dass er die **Standort-URL für den Vorgang** abfragt, um zu ermitteln, ob diese Anforderung erfolgreich war oder fehlgeschlagen ist.  Die Abfrage sollte alle paar Sekunden durchgeführt werden, bis der endgültige Status *Fehler*, *Erfolgreich* oder *Konflikt* für den Vorgang empfangen wird.  Der endgültige Vorgangsstatus sollte schnell zurückgegeben werden, doch kann dies in einigen Fällen mehrere Minuten dauern.

Der Partner erhält auch eine Webhookbenachrichtigung, wenn die Aktion auf Seite im kommerziellen Marketplace erfolgreich abgeschlossen wurde.  Erst dann sollte der Herausgeber das Abonnement auf Herausgeberseite kündigen.

*Antwortheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Link zu einer Ressource, um den Vorgangsstatus abzurufen.  Beispiel: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code: 400 – Ungültige Anforderung.  „Delete“ ist nicht in der Liste `allowedCustomerOperations` für dieses SaaS-Abonnement enthalten.

Code: 403 – Unzulässig.  Das Autorisierungstoken ist ungültig, abgelaufen oder nicht verfügbar. Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 404 – Nicht gefunden.  Das SaaS-Abonnement mit `subscriptionId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler. Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>Vorgangs-APIs

#### <a name="list-outstanding-operations"></a>Auflisten ausstehender Vorgänge 

Ruft die Liste der ausstehenden Vorgänge für das angegebene SaaS-Abonnement ab.  Zurückgegebene Vorgänge müssen vom Herausgeber bestätigt werden, indem die [API zum Patchen von Vorgängen](#update-the-status-of-an-operation) aufgerufen wird.

Zurzeit werden nur **Reaktivierungsvorgänge** als Antwort für diesen API-Aufruf zurückgegeben.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Verwenden Sie 2018-08-31.         |
|    `subscriptionId` | Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde.  |

*Anforderungsheader:*
 
|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     |  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist.  |

*Antwortcodes:*

Code: 200 – Gibt einen ausstehenden Reaktivierungsvorgang für das angegebene SaaS-Abonnement zurück.

*Beispiel einer Antwortnutzlast:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Gibt eine leere JSON-Datei zurück, wenn keine Reaktivierungsvorgänge ausstehen.

Code: 400 – Ungültige Anforderung: Fehler bei der Überprüfung.

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird. 

Code: 404 – Nicht gefunden.  Das SaaS-Abonnement mit `subscriptionId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler. Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Abrufen des Vorgangsstatus

Diese API ermöglicht dem Herausgeber das Nachverfolgen des Status des angegebenen asynchronen Vorgangs:  **Unsubscribe** (Kündigen), **ChangePlan** (Plan ändern) oder **ChangeQuantity** (Menge ändern).

Die `operationId` für diesen API-Aufruf kann von dem Wert abgerufen werden, der von **Operation-Location** zurückgegeben wird, dem API-Aufruf zum Abrufen ausstehender Vorgänge oder dem vom Wert des Parameters `<id>`, der in einem Webhookaufruf empfangen wird.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.  |
|  `subscriptionId`    |  Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde. |
|  `operationId`       |  Der eindeutige Bezeichner des Vorgangs, der gerade abgerufen wird. |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist.  |

*Antwortcodes:*

Code: 200 – Ruft Details für den angegebenen SaaS-Vorgang ab. 

*Beispiel einer Antwortnutzlast:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird. 

Code: 404 – Nicht gefunden.  

* Abonnement mit `subscriptionId` wurde nicht gefunden.
* Vorgang mit `operationId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Aktualisieren des Status eines Vorgangs

Diese API dient zum Aktualisieren des Status eines ausstehenden Vorgangs, um anzugeben, ob der Vorgang auf Herausgeberseite erfolgreich oder fehlerhaft war.

Die `operationId` für diesen API-Aufruf kann von dem Wert abgerufen werden, der von **Operation-Location** zurückgegeben wird, dem API-Aufruf zum Abrufen ausstehender Vorgänge oder dem vom Wert des Parameters `<id>`, der in einem Webhookaufruf empfangen wird.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Verwenden Sie 2018-08-31.  |
|   `subscriptionId`   |  Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde.  |
|   `operationId`      |  Der eindeutige Bezeichner des Vorgangs, der gerade abgeschlossen wird. |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|   `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |

*Beispiel einer Anforderungsnutzlast:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Antwortcodes:*

Code: 200 – Ein Aufruf, um über den Abschluss eines Vorgangs auf der Seite des Partners zu informieren.  Diese Antwort kann z. B. den Abschluss einer Änderung der Arbeitsplätze oder Pläne auf Herausgeberseite anzeigen.

Code: 403
- Unzulässig.  Das Autorisierungstoken ist nicht verfügbar, ungültig oder abgelaufen. Die Anforderung versucht möglicherweise, auf ein Abonnement zuzugreifen, das nicht dem aktuellen Herausgeber gehört.
- Unzulässig.  Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 404 – Nicht gefunden.

* Abonnement mit `subscriptionId` wurde nicht gefunden.
* Vorgang mit `operationId` wurde nicht gefunden.

Code: 409 – Konflikt.  Es wurde z. B. bereits eine neuere Transaktion abgeschlossen.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementieren eines Webhooks für den SaaS-Dienst

Beim Erstellen eines transaktionsfähigen SaaS-Angebots im Partner Center stellt der Partner die URL des **Verbindungswebhooks** bereit, die als HTTP-Endpunkt verwendet werden soll.  Dieser Webhook wird von Microsoft mithilfe des HTTP-Aufrufs POST aufgerufen, um die Herausgeberseite über die folgenden Ereignissen zu informieren, die auf Microsoft-Seite auftreten:

* Wenn das SaaS-Abonnement den Status *Abonniert* hat:
    * ChangePlan (Plan ändern) 
    * ChangeQuantity (Menge ändern)
    * Angehalten
    * Abbestellen
* Wenn das SaaS-Abonnement den Status *Ausgesetzt* hat:
    * Reinstate (Reaktivieren)
    * Abbestellen

Der Herausgeber muss einen Webhook im SaaS-Dienst implementieren, um den Status des SaaS-Abonnements mit der Microsoft-Seite konsistent zu halten.  Der SaaS-Dienst muss die API zum Abrufen des Vorgangs aufrufen, um den Webhookaufruf und die Nutzlastdaten zu überprüfen und zu autorisieren, bevor Aktionen auf Grundlage der Webhookbenachrichtigung durchgeführt werden.  Der Herausgeber sollte eine HTTP-200-Antwort an Microsoft zurückgeben, sobald der Webhookaufruf verarbeitet wird.  Dieser Wert bestätigt, dass der Webhookaufruf vom Herausgeber erfolgreich empfangen wurde.

>[!Note]
>Der Webhook-URL-Dienst muss rund um die Uhr aktiv sein und jederzeit neue Aufrufe von Microsoft empfangen können.  Microsoft verfügt über eine Wiederholungsrichtlinie für den Webhookaufruf (500 Wiederholungen in acht Stunden). Doch wenn der Herausgeber den Aufruf nicht akzeptiert und keine Antwort zurückgibt, schlägt der Vorgang, über den der Webhook benachrichtigt wird, schließlich auf Microsoft-Seite fehl.

*Beispiele für Webhooknutzlast:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Entwickeln und Testen

Zu Beginn des Entwicklungsprozesses wird empfohlen, API-Pseudoantworten auf Herausgeberseite zu erstellen.  Diese Antworten können auf Beispielantworten in diesem Artikel basieren.

Wenn der Herausgeber für End-to-End-Tests bereit ist, sollten folgende Schritte ausgeführt werden:

* Veröffentlichen Sie ein SaaS-Angebot für eine begrenzte Vorschauzielgruppe, und belassen Sie es in der Vorschauphase.
* Legen Sie den Planpreis auf 0 fest, um zu vermeiden, dass beim Testen die tatsächliche Abrechnung von Kosten ausgelöst wird.  Eine andere Möglichkeit besteht darin, einen anderen Preis als 0 (null) festzulegen und alle Testkäufe innerhalb von 24 Stunden zu stornieren.
* Stellen Sie sicher, dass alle Flows von Anfang bis Ende aufgerufen werden, um ein reales Kundenszenario zu simulieren.
* Wenn der Partner den vollständigen Kauf- und Abrechnungsflow testen möchte, verwenden Sie hierfür ein Angebot mit einem höheren Preis als 0 €.  Der Kauf wird abgerechnet, und eine Rechnung wird generiert.

Je nachdem, wo das Angebot veröffentlicht wird, kann ein Kaufablauf über das Azure-Portal oder Microsoft AppSource ausgelöst werden.

Die Aktionen *Plan ändern*, *Menge ändern* und *Kündigen* werden auf Herausgeberseite getestet.  Auf Microsoft-Seite kann *Kündigen* sowohl über das Azure-Portal als auch das Admin Center (das Portal, in dem Microsoft AppSource-Käufe verwaltet werden) ausgelöst werden.  *Menge und Plan ändern* kann nur im Admin Center ausgelöst werden.

## <a name="get-support"></a>Support

Supportoptionen für Herausgeber finden Sie unter [Support für das Programm „Kommerzieller Marketplace“ im Partner Center](../support.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Optionen für SaaS-Angebote im kommerziellen Marketplace finden Sie unter [APIs für getaktete Abrechnung im Marketplace](marketplace-metering-service-apis.md).

Überprüfen und verwenden Sie das [SaaS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK), das auf den in diesem Dokument beschriebenen APIs basiert.
