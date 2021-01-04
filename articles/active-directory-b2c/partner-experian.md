---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Experian
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie die Authentifizierung von Azure AD B2C mit Experian integrieren, um die Identitätsüberprüfung und -bestätigung anhand von Benutzerattributen vorzunehmen und Betrug zu verhindern.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2058e5362a65b6cd5f3e5b7cb9c20ce32d020d30
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928697"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von Experian mit Azure Active Directory B2C

In diesem Beispieltutorial erhalten Sie eine Anleitung zum Integrieren von Azure AD B2C mit [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration). Experian bietet viele unterschiedliche Lösungen, über die Sie sich [hier](https://www.experian.com/) informieren können.

In diesem Beispiel wird die integrierte digitale Plattform **CrossCore** von Experian für die Identitätsüberprüfung und die Betrugsprävention verwendet. CrossCore ist ein Identitätsüberprüfungsdienst, der zum Überprüfen der Benutzeridentifizierung verwendet wird. Die Risikoanalyse des Diensts basiert auf mehreren Informationen, die vom Benutzer bei der Registrierung bereitgestellt werden. Mit CrossCore wird ermittelt, ob der Benutzer seine Anmeldung fortsetzen darf oder nicht. Die folgenden Attribute können in der CrossCore-Risikoanalyse verwendet werden:

- Email
- IP-Adresse
- Vorname
- Middle Name
- Surname
- Anschrift
- City
- Bundesland/Kanton
- Postleitzahl
- Land/Region
- Rufnummer

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- [Einen Azure AD B2C-Mandanten](./tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Integration von Experian beinhaltet die folgenden Komponenten:

- Azure AD B2C: Der Autorisierungsserver ist für die Überprüfung der Anmeldeinformationen des Benutzers verantwortlich. Er wird auch als Identitätsanbieter bezeichnet.

- Experian: Der Experian-Dienst überprüft anhand der Eingaben des Benutzers dessen Identität.

- Benutzerdefinierte REST-API: Mit dieser API wird die Integration zwischen Azure AD B2C und dem Experian-Dienst implementiert.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Screenshot für das Experian-Architekturdiagramm](media/partner-experian/experian-architecture-diagram.png)

|Schritt | BESCHREIBUNG |
|:-----| :-----------|
| 1. | Der Benutzer gelangt auf eine Anmeldeseite. Der Benutzer wählt die Registrierung aus, um ein neues Konto zu erstellen, und gibt Informationen auf der Seite ein. Azure AD B2C erfasst die Benutzerattribute.
| 2. | Azure AD B2C ruft die API der mittleren Ebene auf und übergibt die Benutzerattribute.
| 3. | Die API der mittleren Schicht sammelt die Benutzerattribute und wandelt sie in ein Format um, das von der Experian-API verarbeitet werden kann. Anschließend werden die Daten an Experian gesendet.
| 4. | Experian verarbeitet die Informationen, um die Benutzeridentifizierung anhand der Risikoanalyse zu überprüfen. Anschließend gibt Experian das Ergebnis an die API der mittleren Schicht zurück.
| 5. | Die API der mittleren Schicht verarbeitet die Informationen und sendet relevante Informationen im richtigen JSON-Format zurück an Azure AD B2C.
| 6. | Azure AD B2C empfängt die Informationen von der API der mittleren Ebene. Bei einer Fehlerantwort wird dem Benutzer eine Fehlermeldung angezeigt. Bei einer Erfolgsantwort wird der Benutzer authentifiziert und in das Verzeichnis geschrieben.

## <a name="onboard-with-experian"></a>Integrieren mit Experian

1. Zum Erstellen eines Experian-Kontos müssen Sie sich an [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration) wenden.

2. Nachdem das Konto erstellt wurde, erhalten Sie die Informationen, die Sie für die API-Konfiguration benötigen. In den folgenden Abschnitten wird der Prozess beschrieben.

## <a name="configure-azure-ad-b2c-with-experian"></a>Konfigurieren von Azure AD B2C mit Experian

### <a name="part-1---deploy-the-api"></a>Teil 1: Bereitstellen der API

Stellen Sie den angegebenen [API-Code](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) für einen Azure-Dienst bereit. Der Code kann mit [dieser Anleitung](/visualstudio/deployment/quickstart-deploy-to-azure) aus Visual Studio veröffentlicht werden.

>[!NOTE]
>Sie benötigen die URL des bereitgestellten Diensts, um Azure AD mit den erforderlichen Einstellungen zu konfigurieren.

### <a name="part-2---deploy-the-client-certificate"></a>Teil 2: Bereitstellen des Clientzertifikats

Der Aufruf der Experian-API ist durch ein Clientzertifikat geschützt. Dieses Clientzertifikat wird von Experian bereitgestellt. Nach den Anweisungen in diesem [Dokument](../app-service/environment/certificates.md#private-client-certificate) muss das Zertifikat in den Azure-App-Dienst hochgeladen werden. Die Beispielrichtlinie verwendet im Prozess die folgenden wichtigen Schritte:

- Hochladen des Zertifikats

- Legen Sie den `WEBSITE_LOAD_ROOT_CERTIFICATES`-Schlüssel mit dem Zertifikatfingerabdruck fest.

### <a name="part-3---configure-the-api"></a>Teil 3: Konfigurieren der API

Die Anwendungseinstellungen können im [App-Dienst in Azure konfiguriert](../app-service/configure-common.md#configure-app-settings) werden. Mit dieser Methode können Einstellungen sicher konfiguriert werden, ohne sie in ein Repository einzuchecken. Sie müssen die folgenden Einstellungen für die REST-API angeben:

| Anwendungseinstellungen | `Source` | Notizen |
| :-------- | :------------| :-----------|
|CrossCoreConfig:TenantId | Experian-Kontokonfiguration |     |
|CrossCoreConfig:OrgCode | Experian-Kontokonfiguration |     |
|CrossCore:ApiEndpoint |Experian-Kontokonfiguration|  |
|CrossCore:ClientReference | Experian-Kontokonfiguration | |
| CrossCore:ModelCode |Experian-Kontokonfiguration|
| CrossCore:OrgCode | Experian-Kontokonfiguration |
| CrossCore:SignatureKey  | Experian-Kontokonfiguration |
| CrossCore:TenantId  | Experian-Kontokonfiguration |
| CrossCore:CertificateThumbprint | Experian-Zertifikat |
| BasicAuth:ApiUsername | Definieren eines Benutzernamens für die API | Wird in der ExtId-Konfiguration verwendet. |
| BasicAuth:ApiPassword | Definieren eines Kennworts für die API | Wird in der ExtId-Konfiguration verwendet.

### <a name="part-4---create-api-policy-keys"></a>Teil 4: Erstellen von API-Richtlinienschlüsseln

Richten Sie sich nach diesem [Dokument](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys), um zwei Richtlinienschlüssel zu erstellen: einen für den API-Benutzernamen und einen für das oben definierte API-Kennwort für die HTTP-Basisauthentifizierung.

>[!NOTE]
>Sie benötigen die Schlüssel für die spätere Konfiguration der Richtlinien.

### <a name="part-5---replace-the-configuration-values"></a>Teil 5: Ersetzen der Konfigurationswerte

Suchen Sie in den bereitgestellten [benutzerdefinierten Richtlinien](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy) die folgenden Platzhalter, und ersetzen Sie diese durch die entsprechenden Werte aus Ihrer Instanz.

|                      Platzhalter                       |                                   Ersetzen durch Wert                                 |                   Beispiel                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | Kurzname Ihres Mandanten                                                           | „yourtenant“ aus yourtenant.onmicrosoft.com |
| {your_trustframeworkbase_policy}                       | Azure AD B2C-Name der TrustFrameworkBase-Richtlinie                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | App-ID der IdentityExperienceFramework-App, die in Ihrem Azure AD B2C-Mandanten konfiguriert ist      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | App-ID der ProxyIdentityExperienceFramework-App, die in Ihrem Azure AD B2C-Mandanten konfiguriert ist | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | App-ID der Speicheranwendung Ihres Mandanten                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | Objekt-ID der Speicheranwendung Ihres Mandanten                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | Name des Benutzernamenschlüssels, den Sie [hier](#part-4---create-api-policy-keys) erstellt haben             | B2C\_1A\_RestApiUsername                     |
| {your_api_password_key_name}                           | Name des Kennwortschlüssels, den Sie [hier](#part-4---create-api-policy-keys) erstellt haben             | B2C\_1A\_RestApiPassword                     |
| {your_app_service_URL}                                 | URL des App-Diensts, den Sie eingerichtet haben                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Teil 6: Konfigurieren der Azure AD B2C-Richtlinie

In diesem [Dokument](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) finden Sie Anweisungen zum Einrichten Ihres Azure AD B2C-Mandanten und zum Konfigurieren von Richtlinien.

>[!NOTE]
>Diese Beispielrichtlinie basiert auf dem [Starter Pack für lokale Konten](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

>[!NOTE]
> Als bewährte Methode empfehlen wir Kunden, auf der Seite für die Erfassung der Attribute eine Benachrichtigung zur Einwilligung hinzufügen. Informieren Sie die Benutzer darüber, dass Informationen zur Identitätsüberprüfung an Drittanbieterdienste gesendet werden.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten, und wählen Sie unter „Richtlinien“ die Option **Benutzerflows** aus.

2. Wählen Sie den zuvor erstellten **Benutzerflow** aus.

3. Wählen Sie **Benutzerflow ausführen** und dann die Einstellungen aus:

   a. **Anwendung**: Wählen Sie die registrierte App aus (z. B. JWT)

   b. **Antwort-URL:** Wählen Sie die **Umleitungs-URL** aus

   c. Wählen Sie **Benutzerflow ausführen** aus.

4. Führen Sie den Registrierungsflow aus, und erstellen Sie ein Konto.

5. Abmelden

6. Führen Sie den Anmeldungsflow aus.  

7. Nachdem Sie **Fortsetzen** ausgewählt haben, wird das CrossCore-Puzzle eingeblendet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
