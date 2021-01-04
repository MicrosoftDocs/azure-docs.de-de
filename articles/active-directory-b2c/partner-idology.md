---
title: IDology-Integration in Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Es wird beschrieben, wie Sie eine Beispiel-App für Onlinezahlungen mit IDology in Azure AD B2C integrieren. IDology ist ein Anbieter von unterschiedlichen Lösungen für Identitätsüberprüfungen und -nachweise.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b6001932b6f53a60eda76b6136611a10011391a
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928680"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von IDology mit Azure Active Directory B2C 

In diesem Beispieltutorial erhalten Sie eine Anleitung zum Integrieren von Azure AD B2C mit [IDology](https://www.idology.com/solutions/). IDology ist ein Anbieter von unterschiedlichen Lösungen für Identitätsüberprüfungen und -nachweise. In diesem Beispiel geht es um die Lösung „ExpectID“ von IDology.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* [Einen Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die IDology-Integration umfasst die folgenden Komponenten:

- Azure AD B2C: Der Autorisierungsserver, mit dem die Anmeldeinformationen des Benutzers überprüft werden. Er wird auch als Identitätsanbieter bezeichnet.
- IDology: Mit dem IDology-Dienst werden die Eingaben des Benutzers überprüft, um seine Identität zu verifizieren.
- Benutzerdefinierte REST-API: Mit dieser API wird die Integration zwischen Azure AD und dem IDology-Dienst implementiert.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Architekturdiagramm für IDology](media/partner-idology/idology-architecture-diagram.png)

| Schritt | BESCHREIBUNG |
|------|------|
|1     | Ein Benutzer navigiert zur Anmeldeseite. |
|2     | Der Benutzer wählt die Registrierungsoption für die Erstellung eines neuen Kontos aus und gibt seine Informationen auf der Seite ein. Azure AD B2C erfasst die Benutzerattribute. |
|3     | Azure AD B2C ruft die API der mittleren Ebene auf und übergibt die Benutzerattribute. |
|4     | Die API der mittleren Ebene erfasst die Benutzerattribute und wandelt sie in ein Format um, das von der IDology-API genutzt werden kann. Anschließend sendet sie die Informationen an IDology. |
|5     | IDology nutzt und verarbeitet die Informationen und gibt das Ergebnis dann an die API der mittleren Ebene zurück. |
|6     | Die API der mittleren Ebene verarbeitet die Informationen und sendet die relevanten Informationselemente zurück an Azure AD B2C. |
|7     | Azure AD B2C empfängt die Informationen von der API der mittleren Ebene. Wenn eine Antwort mit einem **Failure** (Fehler) empfangen wird, wird für den Benutzer eine Fehlermeldung angezeigt. Wenn eine Antwort mit **Success** (Erfolg) empfangen wird, wird der Benutzer authentifiziert und in das Verzeichnis geschrieben. |
|      |      |

> [!NOTE]
> Der Kunde kann von Azure AD B2C auch aufgefordert werden, die Step-up-Authentifizierung durchzuführen. Eine Beschreibung dieses Szenarios würde aber den Rahmen dieses Tutorials sprengen.

## <a name="onboard-with-idology"></a>Durchführen des Onboardings mit IDology

1. IDology bietet viele unterschiedliche Lösungen an, über die Sie sich [hier](https://www.idology.com/solutions/) informieren können. In diesem Beispiel verwenden wir „ExpectID“.

2. Wenden Sie sich an [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/), um ein IDology-Konto zu erstellen.

3. Nachdem das Konto erstellt wurde, erhalten Sie die Informationen, die Sie für die API-Konfiguration benötigen. In den folgenden Abschnitten wird der Prozess beschrieben.

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

### <a name="part-1---deploy-the-api"></a>Teil 1: Bereitstellen der API

Stellen Sie den angegebenen [API-Code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) für einen Azure-Dienst bereit. Der Code kann mit [dieser Anleitung](/visualstudio/deployment/quickstart-deploy-to-azure) aus Visual Studio veröffentlicht werden.

Sie benötigen die URL des bereitgestellten Diensts, um Azure AD mit den erforderlichen Einstellungen zu konfigurieren.

### <a name="part-2---configure-the-api"></a>Teil 2: Konfigurieren der API 

Anwendungseinstellungen können [per App Service in Azure konfiguriert werden](../app-service/configure-common.md#configure-app-settings). Mit dieser Methode können Einstellungen sicher konfiguriert werden, ohne dass sie in ein Repository eingecheckt werden müssen. Sie müssen die folgenden Einstellungen für die REST-API angeben:

| Anwendungseinstellungen | `Source` | Notizen |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | IDology-Kontokonfiguration |     |
|IdologySettings:ApiPassword | IDology-Kontokonfiguration |     |
|WebApiSettings:ApiUsername |Definieren eines Benutzernamens für die API| Wird in der ExtId-Konfiguration verwendet. |
|WebApiSettings:ApiPassword | Definieren eines Kennworts für die API | Wird in der ExtId-Konfiguration verwendet.

### <a name="part-3---create-api-policy-keys"></a>Teil 3: Erstellen von API-Richtlinienschlüsseln

Verwenden Sie die Anleitung in [diesem Dokument](secure-rest-api.md#add-rest-api-username-and-password-policy-keys), um zwei Richtlinienschlüssel zu erstellen: einen für den API-Benutzernamen, und einen für das oben definierte API-Kennwort.

Für die Beispielrichtlinie werden diese Schlüsselnamen verwendet:

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Teil 4: Konfigurieren der Azure AD B2C-Richtlinie

1. Befolgen Sie die Anleitung in [diesem Dokument](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack), um das [LocalAccounts-Startpaket herunterzuladen](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) und die Richtlinie für den Azure AD B2C-Mandanten zu konfigurieren. Befolgen Sie die Anleitung, bis Sie den Abschnitt **Testen der benutzerdefinierten Richtlinie** abgeschlossen haben.

2. Laden Sie die beiden Beispielrichtlinien [hier](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy) herunter.

3. Aktualisieren Sie die beiden Beispielrichtlinien:

   1. Öffnen Sie beide Richtlinien:

      1. Aktualisieren Sie im Abschnitt `Idology-ExpectId-API` das Metadatenelement `ServiceUrl`, indem Sie den Speicherort der oben bereitgestellten API angeben.

      1. Ersetzen Sie `yourtenant` durch den Namen des Azure AD B2C-Mandanten.
      Wenn der Name Ihres Azure AD B2C-Mandanten beispielsweise  `contosotenant` lautet, müssen Sie alle Instanzen von `yourtenant.onmicrosoft.com` durch `contosotenant.onmicrosoft.com` ersetzen.

   1. Öffnen Sie die Datei „TrustFrameworkExtensions.xml“:

      1. Suchen Sie nach dem Element `<TechnicalProfile Id="login-NonInteractive">`. Ersetzen Sie beide Instanzen von `IdentityExperienceFrameworkAppId` durch die Anwendungs-ID der IdentityExperienceFramework-Anwendung, die Sie zuvor erstellt haben.

      1. Ersetzen Sie beide Instanzen von `ProxyIdentityExperienceFrameworkAppId` durch die Anwendungs-ID der ProxyIdentityExperienceFramework-Anwendung, die Sie zuvor erstellt haben.

4. Ersetzen Sie die Dateien „SignInorSignUp.xml“ und „TrustFrameworkExtensions.xml“, die Sie in Schritt 1 in Azure AD B2C hochgeladen haben, durch die beiden aktualisierten Beispielrichtlinien.

> [!NOTE]
> Als bewährte Methode empfehlen wir Kunden, auf der Seite für die Erfassung der Attribute eine Benachrichtigung zur Einwilligung hinzufügen. Informieren Sie Benutzer darüber, dass Informationen zur Identitätsüberprüfung an Drittanbieterdienste gesendet werden.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten, und wählen Sie unter **Richtlinien** die Option **Benutzerflows** aus.

2. Wählen Sie den zuvor erstellten **Benutzerflow** aus.

3. Wählen Sie **Benutzerflow ausführen** und dann die Einstellungen aus:

   1. **Anwendung**: Wählen Sie die registrierte App aus (Beispiel: JWT).

   1. **Antwort-URL**: **Wählen Sie die Umleitungs-URL aus**.

   1. Wählen Sie **Benutzerflow ausführen** aus.

4. Führen Sie den Registrierungsablauf aus, und erstellen Sie ein Konto.

5. Melden Sie sich ab.

6. Führen Sie den Ablauf für die Anmeldung durch.

7. Das IDology-Puzzle wird angezeigt, nachdem Sie **Weiter** ausgewählt haben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](custom-policy-get-started.md?tabs=applications)
