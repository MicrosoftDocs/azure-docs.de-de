---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit LexisNexis
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie die Azure AD B2C-Authentifizierung in LexisNexis integrieren. Dabei handelt es sich um einen Profilerstellungs- und Identitätsüberprüfungsdienst zum Überprüfen der Benutzeridentifikation und zum Bereitstellen umfassender Risikobewertungen basierend auf dem Gerät des Benutzers.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5f408699cae9580188a3780fc8f8654eaa97c26b
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108432"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von LexisNexis mit Azure Active Directory B2C

In diesem Beispieltutorial erhalten Sie eine Anleitung zum Integrieren von Azure AD B2C mit [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). LexisNexis bietet viele unterschiedliche Lösungen an, über die Sie sich [hier](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd) informieren können. In diesem Beispieltutorial wird die Lösung **ThreatMetrix** von LexisNexis behandelt. ThreatMetrix ist ein Profilerstellungs- und Identitätsüberprüfungsdienst. Er wird verwendet, um die Benutzeridentifikation zu überprüfen und umfassende Risikobewertungen basierend auf dem Gerät des Benutzers bereitzustellen.

Bei dieser Integration erfolgt die Profilerstellung anhand einiger weniger Benutzerinformationen, die während der Registrierung vom Benutzer bereitgestellt werden. ThreatMetrix legt fest, ob der Benutzer sich weiterhin anmelden darf oder nicht. Die folgenden Attribute werden in der Risikoanalyse von ThreatMetrix berücksichtigt:

- Email
- Rufnummer
- Profilerstellungsinformationen auf dem Computer des Benutzers

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- [Einen Azure AD B2C-Mandanten](./tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die ThreatMetrix-Integration umfasst die folgenden Komponenten:

- Azure AD B2C: Der Autorisierungsserver ist für die Überprüfung der Anmeldeinformationen des Benutzers verantwortlich. Er wird auch als Identitätsanbieter bezeichnet.

- ThreatMetrix: Der ThreatMetrix-Dienst übernimmt Eingaben vom Benutzer und kombiniert sie mit Profilerstellungsinformationen vom Computer des Benutzers, um die Sicherheit der Benutzerinteraktion zu überprüfen.

- Benutzerdefinierte REST-API: Mit dieser API wird die Integration zwischen Azure AD B2C und dem ThreatMetrix-Dienst implementiert.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Screenshot für das LexisNexis-Architekturdiagramm](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Schritt | BESCHREIBUNG |
|:--------------|:-------------|
|1. | Der Benutzer gelangt auf eine Anmeldeseite. Der Benutzer wählt die Registrierung zum Erstellen eines neuen Kontos aus und gibt Informationen auf der Seite ein. Azure AD B2C erfasst die Benutzerattribute.
| 2. | Azure AD B2C ruft die API der mittleren Ebene auf und übergibt die Benutzerattribute.
| 3. | Die API der mittleren Ebene erfasst die Benutzerattribute und wandelt sie in ein Format um, das von der LexisNexis-API verarbeitet werden kann. Anschließend sendet es sie an LexisNexis.  
| 4. | LexisNexis verarbeitet die Informationen und überprüft mit ihnen die Benutzeridentifizierung basierend auf der Risikoanalyse. Anschließend wird das Ergebnis an die API der mittleren Ebene zurückgegeben.
| 5. | Die API der mittleren Ebene verarbeitet die Informationen und sendet relevante Informationen zurück an Azure AD B2C.
| 6. | Azure AD B2C empfängt die Informationen von der API der mittleren Ebene. Wenn eine Antwort mit einem Fehler empfangen wird, wird für den Benutzer eine Fehlermeldung angezeigt. Bei einer Antwort mit einem Erfolg wird der Benutzer authentifiziert und erhält Zugriff.

## <a name="onboard-with-lexisnexis"></a>Integrieren mit LexisNexis

1. Wenden Sie sich an [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd), um ein LexisNexis-Konto zu erstellen.

2. Erstellen Sie eine LexisNexis-Richtlinie für Ihre Anforderungen. Verwenden Sie dazu die [hier](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd) verfügbare Dokumentation.

>[!NOTE]
> Der Name der Richtlinie wird später verwendet.

Nachdem das Konto erstellt wurde, erhalten Sie die Informationen, die Sie für die API-Konfiguration benötigen. In den folgenden Abschnitten wird der Prozess beschrieben.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Konfigurieren von Azure AD B2C mit LexisNexis

### <a name="part-1---deploy-the-api"></a>Teil 1: Bereitstellen der API

Stellen Sie den angegebenen [API-Code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) für einen Azure-Dienst bereit. Der Code kann mit [dieser Anleitung](/visualstudio/deployment/quickstart-deploy-to-azure) aus Visual Studio veröffentlicht werden.

>[!NOTE]
>Sie benötigen die URL des bereitgestellten Diensts, um Azure AD mit den erforderlichen Einstellungen zu konfigurieren.

### <a name="part-2---configure-the-api"></a>Teil 2: Konfigurieren der API

Anwendungseinstellungen können [per App Service in Azure konfiguriert werden](../app-service/configure-common.md#configure-app-settings).  Mit dieser Methode können Einstellungen sicher konfiguriert werden, ohne sie in ein Repository einzuchecken. Sie müssen die folgenden Einstellungen für die REST-API angeben:

| Anwendungseinstellungen | `Source` | Notizen |
| :-------- | :------------| :-----------|
|ThreatMetrix: url | ThreatMetrix-Kontokonfiguration |     |
|ThreatMetrix:OrgId | ThreatMetrix-Kontokonfiguration |     |
|ThreatMetrix:ApiKey |ThreatMetrix-Kontokonfiguration|  |
|ThreatMetrix: Richtlinie | Name der in ThreatMetrix erstellten Richtlinie | |
| BasicAuth:ApiUsername |Definieren eines Benutzernamens für die API| Der Benutzername wird in der Azure AD B2C-Konfiguration verwendet.
| BasicAuth:ApiPassword | Definieren eines Kennworts für die API | Das Kennwort wird in der Azure AD B2C-Konfiguration verwendet.

### <a name="part-3---deploy-the-ui"></a>Teil 3: Bereitstellen der Benutzeroberfläche

Diese Lösung verwendet benutzerdefinierte Benutzeroberflächenvorlagen, die von Azure AD B2C geladen werden. Diese Benutzeroberflächenvorlagen führen die Profilerstellung aus. Die Profile werden dann direkt an den ThreatMetrix-Dienst gesendet.

Lesen Sie diese [Anweisungen](./customize-ui-with-html.md#custom-page-content-walkthrough) zum Bereitstellen der enthaltenen [Benutzeroberflächendateien](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) in einem Blob Storage-Konto. Die Anweisungen umfassen das Einrichten eines Blob Storage-Kontos, das Konfigurieren von CORS und das Aktivieren des öffentlichen Zugriffs.

Die Benutzeroberfläche basiert auf der [Vorlage „Ozeanblau“](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue). Alle Links auf der Benutzeroberfläche sollten aktualisiert werden, sodass sie auf den bereitgestellten Speicherort verweisen. Suchen Sie im Benutzeroberflächenordner https://yourblobstorage/blobcontainer, und ersetzen Sie die Angabe durch den bereitgestellten Speicherort.

### <a name="part-4---create-api-policy-keys"></a>Teil 4: Erstellen von API-Richtlinienschlüsseln

Richten Sie sich nach diesem [Dokument](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys), um zwei Richtlinienschlüssel zu erstellen: einen für den API-Benutzernamen und einen für das oben definierte API-Kennwort.

Für die Beispielrichtlinie werden diese Schlüsselnamen verwendet:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>Teil 5: Aktualisieren der API-URL

Suchen Sie in der bereitgestellten Richtlinie [TrustFrameworkExtensions](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml) das technische Profil `Rest-LexisNexus-SessionQuery`, und aktualisieren Sie das Metadatenelement `ServiceUrl` mit dem Speicherort der oben bereitgestellten API.

### <a name="part-6---update-ui-url"></a>Teil 6: Aktualisieren der Benutzeroberflächen-URL

Suchen Sie in der bereitgestellten Richtlinie [TrustFrameworkExtensions](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml) nach https://yourblobstorage/blobcontainer/, und fügen Sie per Suchen und Ersetzen den Speicherort ein, an dem die Benutzeroberflächendateien bereitgestellt wurden.

>[!NOTE]
> Als bewährte Methode empfehlen wir Kunden, auf der Seite für die Erfassung der Attribute eine Benachrichtigung zur Einwilligung hinzufügen. Informieren Sie die Benutzer darüber, dass Informationen zur Identitätsüberprüfung an Drittanbieterdienste gesendet werden.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>Teil 7: Konfigurieren der Azure AD B2C-Richtlinie

Richten Sie sich nach diesem [Dokument](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack), um das [LocalAccounts-Startpaket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) herunterzuladen und die [Richtlinie](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) für den Azure AD B2C-Mandanten zu konfigurieren.

>[!NOTE]
>Aktualisieren Sie die bereitgestellten Richtlinien, sodass sie sich auf Ihren genauen Mandanten beziehen.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten, und wählen Sie unter „Richtlinien“ die Option **Benutzerflows** aus.

2. Wählen Sie den zuvor erstellten **Benutzerflow** aus.

3. Wählen Sie **Benutzerflow ausführen** und dann die Einstellungen aus:

   a. **Anwendung:** Wählen Sie die registrierte App aus (Beispiel: JWT).

   b. **Antwort-URL:** Wählen Sie die **Umleitungs-URL** aus.

   c. Wählen Sie **Benutzerflow ausführen** aus.

4. Führen Sie den Registrierungsflow aus, und erstellen Sie ein Konto.

5. Abmelden

6. Führen Sie den Anmeldungsflow aus.  

7. Nachdem Sie **Fortsetzen** auswählen, wird das ThreatMetrix-Puzzle eingeblendet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
