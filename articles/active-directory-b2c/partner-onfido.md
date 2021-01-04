---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Onfido
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie die Azure AD B2C-Authentifizierung mit Onfido für die Dokument-ID und die Überprüfung der Gesichtsbiometrie integrieren.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46c6eac80ddbff73d99e05c070e66aa1700da174
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928629"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von Onfido mit Azure Active Directory B2C

In diesem Beispieltutorial erhalten Sie eine Anleitung zum Integrieren von Azure AD B2C mit [Onfido](https://onfido.com/). Onfido ist eine Lösung zur Überprüfung von Dokument-ID und Gesichtsbiometrie. Sie ermöglicht es Unternehmen, ihre Anforderungen hinsichtlich *Know Your Customer* und Identitätsprüfung in Echtzeit erfüllen können. Onfido nutzt eine ausgereifte KI-basierte Identitätsüberprüfung, bei der zuerst eine Foto-ID überprüft und anschließend ein Abgleich mit der Gesichtsbiometrie durchgeführt wird. Diese Lösung verknüpft eine digitale Identität mit der echten Person und bietet ein sicheres Onboarding, wobei gleichzeitig die Betrugsmöglichkeiten verringert werden.

In diesem Beispiel verbinden Sie den Dienst von Onfido im Registrierungs- oder Anmeldeflow, um die Identitätsüberprüfung durchzuführen. Basierend auf den Ergebnissen von Onfido können informierte Entscheidungen darüber getroffen werden, auf welche Produkte und Dienste ein Benutzer zugreifen darf.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- [Einen Azure AD B2C-Mandanten](./tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Ein [Testkonto](https://onfido.com/signup/) bei Onfido.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Onfido-Integration umfasst die folgenden Komponenten:

- Azure AD B2C-Mandant: Autorisierungsserver, der für die Überprüfung der Anmeldeinformationen des Benutzers basierend auf den benutzerdefinierten Richtlinien im Mandanten zuständig ist. Er wird auch als Identitätsanbieter bezeichnet. Er hostet die Onfido-Client-App, die die Benutzerdokumente sammelt und an den Onfido-API-Dienst überträgt.

- Onfido-Client: konfigurierbares JavaScript-Hilfsprogramm zur Clientdokumenterfassung, das auf anderen Webseiten bereitgestellt wird. Es sammelt die Dokumente und führt Vorabüberprüfungen (z. B. auf Dokumentgröße und -qualität) durch.

- REST-API der mittleren Schicht: stellt Endpunkte für den Azure AD B2C-Mandanten bereit, um mit dem Onfido-API-Dienst zu kommunizieren, Datenverarbeitung durchzuführen und die Sicherheitsanforderungen bei beidem einzuhalten.

- Onfido-API-Dienst: von Onfido bereitgestellter Back-End-Dienst, der die vom Benutzer bereitgestellten Dokumente speichert und überprüft.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Screenshot für das Onfido-Architekturdiagramm](media/partner-onfido/onfido-architecture-diagram.png)

|Schritt | BESCHREIBUNG |
|:-----| :-----------|
| 1. | Der Benutzer gelangt auf eine Anmeldeseite. Der Benutzer registriert sich, um ein neues Konto zu erstellen, und gibt Informationen auf der Seite ein. Azure AD B2C erfasst die Benutzerattribute. Die in Azure AD B2C gehostete Onfido-Client-App führt Vorabüberprüfungen auf die Benutzerinformationen aus.
| 2. | Azure AD B2C ruft die API der mittleren Ebene auf und übergibt die Benutzerattribute.
| 3. | Die API der mittleren Schicht sammelt die Benutzerattribute und wandelt sie in ein Format um, das von der Onfido-API verarbeitet werden kann. Anschließend werden die Daten an Onfido gesendet.
| 4. | Onfido verarbeitet die Informationen, um die Benutzeridentifizierung zu überprüfen. Anschließend wird das Ergebnis an die API der mittleren Ebene zurückgegeben.
| 5. | Die API der mittleren Schicht verarbeitet die Informationen und sendet relevante Informationen im richtigen JSON-Format zurück an Azure AD B2C.
| 6. | Azure AD B2C empfängt die Informationen von der API der mittleren Ebene. Wenn eine Antwort mit einem Fehler empfangen wird, wird für den Benutzer eine Fehlermeldung angezeigt. Bei einer Erfolgsantwort wird der Benutzer authentifiziert und in das Verzeichnis geschrieben.

## <a name="onboard-with-onfido"></a>Durchführen des Onboardings mit Onfido

1. Wenden Sie sich an [Onfido](https://onfido.com/signup/), um ein Onfido-Konto zu erstellen.

2. Nachdem ein Konto erstellt wurde, erstellen Sie einen [API-Schlüssel](https://documentation.onfido.com/). Für aktive (Live-)Schlüssel fallen Kosten an. Sie können jedoch die [Sandboxschlüssel zum Testen](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) der Lösung verwenden. Die Sandboxschlüssel liefern dieselbe Ergebnisstruktur wie aktive Schlüssel, die Ergebnisse sind jedoch immer vorgegeben. Die Dokumente werden weder verarbeitet noch gespeichert.

>[!NOTE]
> Sie benötigen den Schlüssel später.

Weitere Informationen zu Onfido finden Sie in der [Onfido-API-Dokumentation](https://documentation.onfido.com) und im [Onfido-Entwicklerhub](https://developers.onfido.com).  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Konfigurieren von Azure AD B2C mit Onfido

### <a name="part-1---deploy-the-api"></a>Teil 1: Bereitstellen der API

- Stellen Sie den angegebenen [API-Code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) für einen Azure-Dienst bereit. Der Code kann mit [dieser Anleitung](/visualstudio/deployment/quickstart-deploy-to-azure) aus Visual Studio veröffentlicht werden.
- Richten Sie CORS ein, und fügen Sie als **zulässigen Ursprung** https://{Ihr_Mandantenname}.b2clogin.com hinzu.

>[!NOTE]
>Sie benötigen die URL des bereitgestellten Diensts, um Azure AD mit den erforderlichen Einstellungen zu konfigurieren.

#### <a name="adding-sensitive-configuration-settings"></a>Hinzufügen von vertraulichen Konfigurationseinstellungen

Anwendungseinstellungen können [per App Service in Azure konfiguriert werden](../app-service/configure-common.md#configure-app-settings). Mit App Service können Einstellungen sicher konfiguriert werden, ohne sie in ein Repository einzuchecken. Die REST-API benötigt die folgenden Einstellungen:

| Name der Anwendungseinstellung | `Source` | Notizen |
|:-------------------------|:-------|:-------|
|OnfidoSettings:AuthToken| Onfido-Konto |

### <a name="part-2---deploy-the-ui"></a>Teil 2: Bereitstellen der Benutzeroberfläche

#### <a name="configure-your-storage-location"></a>Konfigurieren des Speicherorts

1. Richten Sie einen [Blob Storage-Container in Ihrem Speicherkonto](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) ein.

2. Speichern Sie die Dateien für die Benutzeroberfläche aus dem [Benutzeroberflächenordner](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) in Ihrem Blobcontainer.

3. Gewähren Sie CORS Zugriff auf den erstellten Speichercontainer, indem Sie diese Anweisungen befolgen:

   a. Wechseln Sie zu **Einstellungen** >**Zulässiger Ursprung**, und geben Sie `https://{your_tenant_name}.b2clogin.com` ein. Ersetzen Sie „your-tenant-name“ durch den Namen Ihres Azure AD B2C-Mandanten. Beispiel: https://fabrikam.b2clogin.com. Verwenden Sie bei der Eingabe Ihres Mandantennamens ausschließlich Kleinbuchstaben.

   b. Wählen Sie für **Zulässige Methoden** die Optionen `GET` und `PUT` aus.

   c. Wählen Sie **Speichern** aus.

#### <a name="update-ui-files"></a>Aktualisieren von Dateien für die Benutzeroberfläche

1. Wechseln Sie in den Dateien für die Benutzeroberfläche zum Ordner [**ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue).

2. Öffnen Sie jede HTML-Datei.

3. Suchen Sie {your-ui-blob-container-url}, und ersetzen diese Zeichenfolge durch die URL, unter der sich die Benutzeroberflächenordner **ocean_blue**, **dist** und **assets** befinden.

4. Suchen Sie {your-intermediate-api-url}, und ersetzen diese Zeichenfolge durch die URL des API-App-Diensts der mittleren Schicht.

#### <a name="upload-your-files"></a>Hochladen Ihrer Dateien

1. Speichern Sie die Dateien für die Benutzeroberfläche aus dem entsprechenden Ordner in Ihrem Blobcontainer.

2. Verwenden Sie für die Verwaltung Ihrer Dateien und Zugriffsberechtigungen [Azure Storage-Explorer](../virtual-machines/disks-use-storage-explorer-managed-disks.md).

### <a name="part-3---configure-azure-ad-b2c"></a>Teil 3: Konfigurieren von Azure AD B2C

#### <a name="replace-the-configuration-values"></a>Ersetzen der Konfigurationswerte

Suchen Sie in den bereitgestellten [benutzerdefinierten Richtlinien](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies) die folgenden Platzhalter, und ersetzen Sie diese durch die entsprechenden Werte aus Ihrer Instanz.

| Platzhalter | Ersetzen durch Wert | Beispiel  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | Kurzname Ihres Mandanten |  „yourtenant“ aus yourtenant.onmicrosoft.com |
| {your_tenantID} | Mandanten-ID Ihres Azure AD B2C-Mandanten | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | App-ID der IdentityExperienceFramework-App, die in Ihrem Azure AD B2C-Mandanten konfiguriert ist      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | App-ID der ProxyIdentityExperienceFramework-App, die in Ihrem Azure AD B2C-Mandanten konfiguriert ist | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | App-ID der Speicheranwendung Ihres Mandanten                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | Objekt-ID der Speicheranwendung Ihres Mandanten                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | Instrumentierungsschlüssel Ihrer App Insights-Instanz*| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| URL des Speicherorts der Ordner **ocean_blue**, **dist** und **assets** für Ihre Benutzeroberfläche | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | URL des App-Diensts, den Sie eingerichtet haben                                             | `https://yourapp.azurewebsites.net`          |

\* App Insights kann sich in einem anderen Mandanten befinden. Dieser Schritt ist optional. Entfernen Sie ggf. die entsprechenden TechnicalProfiles und OrchestrationSteps.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Teil 4: Konfigurieren der Azure AD B2C-Richtlinie

In diesem [Dokument](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) finden Sie Anweisungen zum Einrichten Ihres Azure AD B2C-Mandanten und zum Konfigurieren von Richtlinien.

>[!NOTE]
> Als bewährte Methode empfehlen wir Kunden, auf der Seite für die Erfassung der Attribute eine Benachrichtigung zur Einwilligung hinzufügen. Informieren Sie die Benutzer darüber, dass Informationen zur Identitätsüberprüfung an Drittanbieterdienste gesendet werden.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten, und wählen Sie unter „Richtlinien“ die Option **Identity Experience Framework** aus.

2. Wählen Sie den zuvor erstellten Flow **SignUpSignIn** aus.

3. Wählen Sie **Benutzerflow ausführen** und dann die Einstellungen aus:

   a. **Anwendung:** Wählen Sie die registrierte App aus (Beispiel: JWT).

   b. **Antwort-URL:** Wählen Sie die **Umleitungs-URL** aus.

   c. Wählen Sie **Benutzerflow ausführen** aus.

4. Führen Sie den Registrierungsflow aus, und erstellen Sie ein Konto.

5. Der Onfido-Dienst wird während des Flows aufgerufen, nachdem das Benutzerattribut erstellt wurde. Wenn der Flow unvollständig ist, stellen Sie sicher, dass der Benutzer nicht im Verzeichnis gespeichert ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
