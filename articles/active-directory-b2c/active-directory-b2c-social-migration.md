---
title: Migrieren von Benutzern mit Identitäten sozialer Netzwerke in Azure Active Directory B2C | Microsoft-Dokumentation
description: Hier werden grundlegende Konzepte für die Migration von Benutzern mit Identitäten sozialer Netzwerke zu Azure AD B2C mithilfe der Graph-API erläutert.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bca802bb0099b0d854d752db8341dfe74031ef3b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508029"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migrieren von Benutzern mit Identitäten in sozialen Netzwerken
Wenn Sie Ihren Identitätsanbieter zu Azure AD B2C migrieren möchten, müssen Sie unter Umständen auch Benutzer mit Identitäten sozialer Netzwerke migrieren. Dieser Artikel erklärt, wie man bestehende Konten für soziale Identitäten migriert, wie z.B.: Facebook-, LinkedIn-, Microsoft- und Google-Konten zu Azure AD B2C. Dieser Artikel gilt auch für Verbundidentitäten, jedoch sind diese Migrationen weniger gängig.

## <a name="prerequisites"></a>Voraussetzungen
Dieser Artikel ist eine Fortsetzung des Artikels „Azure Active Directory B2C: Benutzermigration“ und konzentriert sich auf die Migration von Identitäten sozialer Netzwerke. Bevor Sie beginnen, lesen Sie [Azure Active Directory B2C: Benutzermigration](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Einführung in die Migration von Identitäten sozialer Netzwerke

* In Azure AD B2C werden Anmeldenamen **lokaler Konten** (Benutzername oder E-Mail-Adresse) im Benutzerdatensatz in der `signInNames`-Auflistung gespeichert. `signInNames` enthält einen oder mehrere `signInName`-Datensätze, die die Anmeldenamen für den Benutzer angeben. Jeder Anmeldename muss unter den Mandanten eindeutig sein.

* **Konten von Identitäten sozialer Netzwerke** werden in der `userIdentities`-Auflistung gespeichert. Der Eintrag gibt `issuer` (Name des Identitätsanbieters), z.B. „facebook.com“, und `issuerUserId` an, eine eindeutige Benutzer-ID für den Aussteller. Das `userIdentities`-Attribut enthält einen oder mehrere UserIdentity-Datensätze, die den Kontotyp beim sozialen Netzwerk und die eindeutige Benutzer-ID vom Identitätsanbieter des sozialen Netzwerks angeben.

* **Kombinieren eines lokalen Kontos mit einer Identität eines sozialen Netzwerks**: Wie bereits erwähnt, werden Anmeldenamen lokaler Konten und Kontoidentitäten sozialer Netzwerke in verschiedenen Attributen gespeichert. `signInNames` wird für das lokale Konto verwendet und `userIdentities` für das Konto des sozialen Netzwerks. Ein Azure AD B2C-Konto kann entweder ausschließlich ein lokales Konto oder ausschließlich ein Konto eines sozialen Netzwerks sein oder ein lokales Konto mit einer Identität eines sozialen Netzwerks in einem Benutzerdatensatz kombinieren. Dieses Verhalten ermöglicht die Verwaltung eines einzelnen Kontos, wobei sich ein Benutzer mit den Anmeldeinformationen des lokalen Kontos oder mit den sozialen Identitäten anmelden kann.

* `UserIdentity`-Typ: Enthält Informationen über die Identität eines Benutzers mit einem Konto bei einem sozialen Netzwerk in einem Azure AD B2C-Mandanten:
  * `issuer` Die Zeichenfolgendarstellung des Identitätsanbieters, der die Benutzer-ID ausgegeben hat (z.B. „facebook.com“).
  * `issuerUserId` Die eindeutige Benutzer-ID im base64-Format, die vom Identitätsanbieter eines sozialen Netzwerks verwendet wird.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Abhängig vom Identitätsanbieter handelt es sich bei der **Benutzer-ID eines sozialen Netzwerks** um einen eindeutigen Wert für einen bestimmten Benutzer pro Anwendung oder Entwicklungskonto. Konfigurieren Sie die Azure AD B2C-Richtlinie mit der gleichen Anwendungs-ID, die zuvor vom Anbieter des sozialen Netzwerks zugewiesen wurde. Oder konfigurieren Sie eine andere Anwendung innerhalb desselben Entwicklungskontos.

## <a name="use-graph-api-to-migrate-users"></a>Migrieren von Benutzern mithilfe der Graph-API
Das Azure AD B2C-Benutzerkonto wird über die [Graph-API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) erstellt. Um mit der Graph-API zu kommunizieren, benötigen Sie zuerst ein Dienstkonto mit Administratorberechtigungen. In Azure AD registrieren Sie eine Anwendung und eine Authentifizierung für Azure AD. Die Anwendungsanmeldeinformationen bestehen aus Anwendungs-ID und Anwendungsgeheimnis. Die Anwendung verhält sich beim Aufrufen der Graph-API wie sie selbst und nicht wie ein Benutzer. Befolgen Sie die Anweisungen im Artikel [Azure Active Directory B2C: Benutzermigration](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) unter Schritt 1.

## <a name="required-properties"></a>Erforderliche Eigenschaften
Die folgende Liste enthält die Eigenschaften, die beim Erstellen eines Benutzers erforderlich sind.
* **accountEnabled**: true
* **displayName**: Der Name, der im Adressbuch für den Benutzer angezeigt wird.
* **passwordProfile**: Das Kennwortprofil für den Benutzer. 

> [!NOTE]
> Dieses gilt nur für Konten sozialer Netzwerke (ohne Anmeldeinformationen des lokalen Kontos), und Sie müssen weiterhin das Kennwort angeben. Azure AD B2C ignoriert das Kennwort, das Sie für Konten sozialer Netzwerke angeben.

* **userPrincipalName**: Der Benutzerprinzipalname (someuser@contoso.com). Der Benutzerprinzipalname muss eine der überprüften Domänen des Mandanten enthalten. Generieren Sie zur Angabe des UPN einen neuen GUID-Wert, und verketten Sie ihn mit `@` und Ihrem Mandantennamen.
* **mailNickname**: Der E-Mail-Alias für den Benutzer. Dieser Wert kann die gleiche ID sein, die Sie für userPrincipalName verwenden. 
* **signInNames**: Ein oder mehrere SignInName-Datensätze, die die Anmeldenamen für den Benutzer angeben. Jeder Anmeldename muss unter den Unternehmen/Mandanten eindeutig sein. Diese Eigenschaft gilt nur für Konten sozialer Netzwerke und darf leer gelassen werden.
* **userIdentities**: Ein oder mehrere UserIdentity-Datensätze, die den Kontotyp des sozialen Netzwerks und die eindeutige Benutzer-ID vom Identitätsanbieter des sozialen Netzwerks angeben.
* [optional] **otherMails**: Die E-Mail-Adressen des Benutzers (nur bei Konten sozialer Netzwerke) 

Weitere Informationen finden Sie unter [Referenz zur Graph-API](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrieren von ausschließlich Konten sozialer Netzwerke
Um ausschließlich Konten sozialer Netzwerke ohne die Anmeldeinformationen eines lokalen Kontos zu erstellen, senden Sie eine HTTPS-POST-Anforderung an die Graph-API. Der Anforderungstext enthält die Eigenschaften des zu erstellenden Kontobenutzers eines sozialen Netzwerks. Sie müssen mindestens die erforderlichen Eigenschaften angeben. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Übermitteln Sie die folgenden Formulardaten: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Migrieren eines Kontos eines sozialen Netzwerks mit einem lokalen Konto
Um ein kombiniertes lokales Konto mit Identitäten sozialer Netzwerke zu erstellen, senden Sie eine HTTPS-POST-Anforderung an die Graph-API. Der Anforderungstext enthält die Eigenschaften des zu erstellenden Kontobenutzers eines sozialen Netzwerks. Sie müssen mindestens die erforderlichen Eigenschaften angeben. 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Übermitteln Sie folgende Formulardaten: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
### <a name="how-can-i-know-the-issuer-name"></a>Wie kann ich den Namen des Ausstellers in Erfahrung bringen?
Der Name des Ausstellers oder des Identitätsanbieters ist in Ihrer Richtlinie konfiguriert. Wenn Sie nicht wissen, welcher Wert in `issuer` angegeben werden soll, gehen Sie folgendermaßen vor:
1. Melden Sie sich mit einem der Konten eines sozialen Netzwerks an.
2. Kopieren Sie aus dem JWT-Token den `sub`-Wert. `sub` enthält in der Regel die Objekt-ID des Benutzers in Azure AD B2C. Rufen Sie alternativ im Azure-Portal die Eigenschaften des Benutzers auf, und kopieren Sie die Objekt-ID.
3. Öffnen Sie den [Azure AD Graph-Explorer](https://graphexplorer.azurewebsites.net).
4. Melden Sie sich als Administrator an.
5. Führen Sie folgende GET-Anforderung aus. Ersetzen Sie userObjectId durch die von Ihnen kopierte Benutzer-ID. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Machen Sie das `userIdentities`-Element innerhalb der JSON-Rückgabe von Azure AD B2C ausfindig.
7. [Optional] Es wird empfohlen, auch den `issuerUserId`-Wert zu decodieren.

> [!NOTE]
> Verwenden Sie ein B2C-Mandantenadministratorkonto, das für den B2C-Mandanten lokal angeordnet ist. Die Syntax für den Kontonamen lautet admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>Ist es möglich, einem vorhandenen lokalen Konto eine Identität eines sozialen Netzwerks hinzuzufügen?
Ja. Sie können die Identität eines sozialen Netzwerks nach Erstellung des lokalen Kontos hinzufügen. Führen Sie die HTTPS-PATCH-Anforderung aus. Ersetzen Sie userObjectId durch die zu aktualisierende Benutzer-ID. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Übermitteln Sie folgende Formulardaten: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Ist es möglich, mehrere Identitäten sozialer Netzwerke hinzuzufügen?
Ja. Sie können mehrere Identitäten sozialer Netzwerke für ein einzelnes Azure AD B2C-Konto hinzufügen. Führen Sie die HTTPS-PATCH-Anforderung aus. Ersetzen Sie userObjectId durch die Benutzer-ID. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Übermitteln Sie folgende Formulardaten: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>[Optional] Beispiel für eine Anwendung zur Migration von Benutzern
[Laden Sie die Beispiel-App V2 herunter, und führen Sie sie aus](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Die Beispiel-App V2 verwendet eine JSON-Datei, die Platzhalterbenutzerdaten enthält, z.B. für ein lokales Konto, ein Konto eines sozialen Netzwerks sowie für lokale und soziale Identitäten in einem einzelnen Konto.  Öffnen Sie zum Bearbeiten der JSON-Datei die Visual Studio-Projektmappe `AADB2C.UserMigration.sln`. Öffnen Sie im Projekt `AADB2C.UserMigration` die Datei `UsersData.json`. Die Datei enthält eine Liste mit Benutzerentitäten. Jede Benutzerentität weist die folgenden Eigenschaften auf:
* **signInName**: Bei lokalen Konten die E-Mail-Adresse für die Anmeldung
* **displayName**: Anzeigename des Benutzers
* **firstName**: Vorname des Benutzers
* **lastName**: Nachname des Benutzers
* **password**: Bei lokalen Konten das Kennwort des Benutzers (kann leer sein)
* **issuer**: Bei Konten sozialer Netzwerke der Name des Identitätsanbieters
* **issuerUserId**: Bei Konten sozialer Netzwerke die eindeutige Benutzer-ID, die vom Identitätsanbieter eines sozialen Netzwerks verwendet wird. Der Wert sollte in Klartext sein. Die Beispiel-App codiert diesen Wert in base64.
* **email**: Die E-Mail-Adresse des Benutzers (nur bei Konten sozialer Netzwerke, nicht bei kombinierten Konten)

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Wenn Sie die Datei „UsersData.json“ im Beispiel nicht mit Ihren Daten aktualisieren, können Sie sich mit den Beispielanmeldeinformationen für lokale Konten anmelden, jedoch nicht mit den Beispielen für Konten sozialer Netzwerke. Geben Sie für die Migration Ihrer Konten sozialer Netzwerke echte Daten ein.

Weitere Informationen zum Verwenden der Beispiel-App finden Sie unter [Azure Active Directory B2C: Benutzermigration](active-directory-b2c-user-migration.md)
