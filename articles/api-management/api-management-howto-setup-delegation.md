---
title: Delegieren von Benutzerregistrierung und Produktabonnierung
description: Erfahren Sie, wie Sie die Benutzerregistrierung und Produktabonnements an einen Dritten in Azure API Management delegieren.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 54193c9333c75fd8b973ebe33470fca3617e2f2d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341840"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Delegieren von Benutzerregistrierung und Produktabonnierung

Mit der Delegierung können Sie Anmeldung, Registrierung und Produktabonnierung von Entwicklern mit Ihrer vorhandenen Website umsetzen, anstatt die integrierte Funktion im Entwicklerportal zu verwenden. Dadurch besitzt die Website die Benutzerdaten und kann die Prüfung dieser Schritte auf selbst definierte Weise durchführen.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegieren der Anmeldung und Registrierung für Entwickler

Um die Anmeldung und Registrierung für Entwickler bei Ihrer vorhandenen Website zu delegieren, müssen Sie einen speziellen Delegierungsendpunkt für Ihre Website erstellen. Er muss als Einstiegspunkt für über das API Management-Entwicklerportal initiierte Anforderungen dieser Art fungieren.

Der komplette Workflow sieht wie folgt aus:

1. Ein Entwickler klickt auf den Anmeldungs- oder Registrierungslink im API Management-Entwicklerportal.
2. Der Browser wird zum Delegierungsendpunkt umgeleitet.
3. Der Delegierungsendpunkt wiederum leitet den Benutzer um oder zeigt eine Benutzeroberfläche für die Anmeldung oder Registrierung an.
4. Im Erfolgsfall wird der Benutzer anschließend wieder auf die Seite im API Management-Entwicklerportal umgeleitet, von der er ursprünglich gekommen ist.

Richten Sie zunächst in API Management die Weiterleitung von Anfragen über Ihren Delegierungsendpunkt ein. Suchen Sie im Azure-Portal in Ihrer API Management-Ressource nach **Sicherheit** , und klicken Sie auf das Element **Delegierung**. Klicken Sie auf das Kontrollkästchen zum Aktivieren von „Anmeldung und Abmeldung delegieren“.

![Delegierungsseite][api-management-delegation-signin-up]

* Legen Sie die URL für Ihren speziellen Delegierungsendpunkt fest und geben Sie sie in das Feld **Delegierungsendpunkt-URL** ein. 
* Geben Sie im Feld „Schlüssel für delegierte Authentifizierung“ einen geheimen Schlüssel ein. Dieser wird zur Erstellung einer Signatur verwendet, mit der sichergestellt wird, dass die Anfrage tatsächlich von Azure API Management stammt. Sie können auf die Schaltfläche **Generieren** klicken, damit API Management nach dem Zufallsprinzip einen Schlüssel für Sie generiert.

Anschließend müssen Sie den **Delegierungsendpunkt** einrichten. Dieser Endpunkt erfüllt mehrere Aufgaben:

1. Empfang einer Anforderung in der folgenden Form:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL der Herkunftsseite}&salt={Zeichenfolge}&sig={Zeichenfolge}*
   
    Abfrageparameter für Anmeldung und Registrierung:
   
   * **operation** : Identifiziert den Typ der Delegierungsanforderung – in diesem Fall kann dieser nur **SignIn** sein.
   * **returnUrl:** die URL der Seite, auf der der Benutzer auf einen Anmeldungs- oder Registrierungslink geklickt hat
   * **salt** : Eine spezielle Salt-Zeichenfolge, mit der ein Sicherheitshash generiert wird
   * **sig** : Ein berechneter Sicherheitshash zum Vergleich mit dem von Ihnen generierten Hash
2. Überprüfen, ob die Anfrage von Azure API Management stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)
   
   * Generieren Sie einen HMAC-SHA512-Hash für eine Zeichenfolge basierend auf den Abfrageparametern **returnUrl** und **salt** ( [Beispielcode finden Sie unter]):
     
     > HMAC( **salt** + '\n' + **returnUrl** )

   * Vergleichen Sie den generierten Hash mit dem Wert des **sig** -Abfrageparameters. Fahren Sie mit dem nächsten Schritt fort, wenn die Hashes übereinstimmen. Lehnen Sie die Anfrage andernfalls ab.
3. Überprüfen Sie, ob Sie eine Anfrage zur Anmeldung/Registrierung erhalten haben: Der Abfrageparameter **operation** ist in diesem Fall auf **SignIn** festgelegt.
4. Anzeigen der Benutzeroberfläche für Anmeldung oder Registrierung
5. Falls sich der Benutzer registriert, müssen Sie das entsprechende Konto in API Management erstellen. [Erstellen Sie einen Benutzer] mit der REST-API für API Management. Achten Sie dabei darauf, die Benutzer-ID auf den Wert aus Ihrem Benutzerspeicher oder eine andere für Sie nachverfolgbare ID festzulegen.
6. Nach erfolgreicher Authentifizierung des Benutzers:
   
   * [Fordern Sie ein SAS-Token an]. Verwenden Sie hierzu die REST-API von API Management.
   * Hängen Sie einen returnUrl-Abfrageparameter an die SSO-URL an, die Sie aus dem obigen API-Aufruf erhalten haben:
     
     > Beispiel: `https://<developer portal domain, for example: contoso.developer.azure-api.net>/signin-sso?token=<URL-encoded token>&returnUrl=<URL-encoded URL, for example: %2Freturn%2Furl>` 
     
   * Leiten Sie den Benutzer an die oben generierte URL um.

Zusätzlich zum Anmeldevorgang ( **SignIn** ) können Sie auch eine Kontoverwaltung durchführen, indem Sie die vorherigen Schritte ausführen und einen der folgenden Vorgänge verwenden:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**
* **SignOut**

Sie müssen die folgenden Abfrageparameter für Operationen zur Kontoverwaltung übergeben.

* **operation** : Gibt an, um welche Art von Delegierungsanforderung es sich handelt ("ChangePassword", "ChangeProfile" oder "CloseAccount")
* **userId:** die Benutzer-ID des Kontos, das verwaltet werden soll
* **salt** : Eine spezielle Salt-Zeichenfolge, mit der ein Sicherheitshash generiert wird
* **sig** : Ein berechneter Sicherheitshash zum Vergleich mit dem von Ihnen generierten Hash

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegieren der Produktabonnierung

Die Delegierung der Produktabonnierung funktioniert genauso wie die Delegierung der Anmeldung und Registrierung. Der komplette Workflow sieht wie folgt aus:

1. Ein Entwickler wählt ein Produkt im API Management-Entwicklerportal aus und klickt auf die Schaltfläche „Abonnieren“.
2. Der Browser wird zum Delegierungsendpunkt umgeleitet.
3. Der Delegierungsendpunkt führt die erforderlichen Schritte für die Produktabonnierung aus. Die Schritte dazu können Sie selbst festlegen. Sie können z. B. eine Weiterleitung auf eine Seite zur Eingabe von Zahlungsdaten oder zusätzlichen Fragen beinhalten oder einfach die Speicherung der Daten ohne weiteres Eingreifen des Benutzers.

Klicken Sie auf der Seite **Delegierung** auf **Produktabonnierung delegieren** , um diese Funktion zu aktivieren.

Stellen Sie anschließend sicher, dass der Delegierungsendpunkt die folgenden Aktionen ausführt:

1. Empfang einer Anforderung in der folgenden Form:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={Vorgang}&productId={zu abonnierendes Produkt}&userId={anfordernder Benutzer}&salt={Zeichenfolge}&sig={Zeichenfolge}*
   >
   
    Abfrageparameter für die Produktabonnierung:
   
   * **operation** : gibt den Typ der Delegierungsanforderung an. Für Produktabonnierungsanfragen sind die folgenden Optionen gültig:
     * „Subscribe“: Anfrage für ein Abonnement eines Produkts mit der angegebenen ID (siehe unten) für den Benutzer
     * „Unsubscribe“: Anfrage zur Beendigung des Abonnement für ein Produkt durch den Benutzer
     * „Renew“: Anfrage zur Verlängerung eines Abonnements (weil dieses z. B. demnächst abläuft)
   * **productId:** für *Abonnieren* – die ID des Produkts, das der Benutzer abonnieren möchte
   * **subscriptionId:** für *Abonnement kündigen* und *Erneuern* – die ID des Produktabonnements
   * **userId:** für *Abonnieren* – die ID des Benutzers, für den die Anforderung erfolgt
   * **salt** : Eine spezielle Salt-Zeichenfolge, mit der ein Sicherheitshash generiert wird
   * **sig** : Ein berechneter Sicherheitshash zum Vergleich mit dem von Ihnen generierten Hash

2. Überprüfen, ob die Anfrage von Azure API Management stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)
   
   * Generieren Sie einen HMAC-SHA512-Hash für eine Zeichenfolge aus den Abfrageparametern **productId** , **userId** und **salt** :
     
     > HMAC( **salt** + '\n' + **productId** + '\n' + **userId** )
     > 
     > 
   * Vergleichen Sie den generierten Hash mit dem Wert des **sig** -Abfrageparameters. Fahren Sie mit dem nächsten Schritt fort, wenn die Hashes übereinstimmen. Lehnen Sie die Anfrage andernfalls ab.
3. Verarbeiten Sie das Produktabonnement basierend auf dem im Parameter **operation** angeforderten Vorgang, z. B. Abrechnung, weitere Fragen usw.
4. Nachdem der Benutzer das Produkt auf Ihrer Seite erfolgreich abonniert hat, abonnieren Sie das Produkt in API Management für den Benutzer, indem Sie die [REST-API für Abonnements] aufrufen.

## <a name="example-code"></a><a name="delegate-example-code"> </a> Beispielcode

Die Codebeispiele zeigen Folgendes:

* Abrufen des *Validierungsschlüssels für Delegierungen* , der auf dem Bildschirm für die Delegierung im Herausgeberportal festgelegt wird
* Erstellen eines HMAC, mit dem dann die Signatur überprüft und dadurch die Gültigkeit der übergebenen returnUrl nachgewiesen wird.

Der gleiche Code funktioniert mit geringfügigen Änderungen auch für "productId" und "userID".

**C#-Code zum Generieren des Hash von "returnUrl"**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**NodeJS-Code zum Generieren des Hash von "returnUrl"**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> Sie müssen Sie [das Entwicklerportal erneut veröffentlichen](api-management-howto-developer-portal-customize.md#publish), damit die Delegierungsänderungen wirksam werden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Delegieren finden Sie im folgenden Video:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Anfordern eines SAS-Tokens]: /rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken
[Erstellen Sie einen Benutzer]: /rest/api/apimanagement/2019-12-01/user/createorupdate
[Aufrufen der REST-API für Abonnements]: /rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[Beispielcode finden Sie unter]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
