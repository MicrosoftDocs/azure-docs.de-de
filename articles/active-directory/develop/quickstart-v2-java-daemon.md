---
title: 'Schnellstart: Aufrufen von Microsoft Graph über einen Java-Daemon | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie eine Java-App unter Verwendung der App-eigenen Identität ein Zugriffstoken abrufen und eine API aufrufen kann, die durch einen Microsoft Identity Platform-Endpunkt geschützt ist.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/22/2021
ms.author: nacanuma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 196b80a704b8a270a4cbb7d3505d5f9be1e23479
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820318"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über eine Java-Konsolen-App anhand der Identität der App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine Java-Anwendung mithilfe der App-Identität ein Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen und eine [Liste mit Benutzern](/graph/api/user-list) im Verzeichnis anzuzeigen. Das Codebeispiel veranschaulicht, wie ein unbeaufsichtigter Auftrag oder Windows-Dienst mit einer Anwendungsidentität anstelle der Identität eines Benutzers ausgeführt werden kann. 

> [!div renderon="docs"]
> ![Funktionsweise der in dieser Schnellstartanleitung generierten Beispiel-App](media/quickstart-v2-java-daemon/java-console-daemon.svg)

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel benötigen Sie Folgendes:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 oder höher
- [Maven](https://maven.apache.org/)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrieren und Herunterladen Ihrer Schnellstart-App

> [!div renderon="docs" class="sxs-lookup"]
>
> Die Schnellstartanwendung kann auf zwei Arten gestartet werden: „Express“ (Option 1 unten) und „Manuell“ (Option 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
>
> 1. Navigieren Sie zur Umgebung des Schnellstarts <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs" target="_blank">Azure-Portal – App-Registrierungen</a>.
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit nur einem Klick herunterzuladen und automatisch zu konfigurieren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `Daemon-console`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
> 1. Wählen Sie **Registrieren**.
> 1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
> 1. Wählen Sie unter **Geheimer Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus, geben Sie einen Namen ein, und wählen Sie dann **Hinzufügen** aus. Notieren Sie den Wert für den geheimen Schlüssel an einem sicheren Ort, damit Sie ihn in einem späteren Schritt verwenden können.
> 1. Wählen Sie unter **Verwalten** die Optionen **API-Berechtigungen** > **Berechtigung hinzufügen** aus. Wählen Sie **Microsoft Graph**.
> 1. Wählen Sie **Anwendungsberechtigungen**.
> 1. Wählen Sie unter dem Knoten **Benutzer** die Option **User.Read.All** und dann **Berechtigungen hinzufügen** aus.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>Herunterladen und Konfigurieren der Schnellstart-App
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Schritt 1: Konfigurieren der Anwendung im Azure-Portal
> Damit das Codebeispiel für diese Schnellstartanleitung funktioniert, müssen Sie einen geheimen Clientschlüssel erstellen und die Anwendungsberechtigung **User.Read.All** aus der Graph-API hinzufügen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]() (Diese Änderungen für mich vornehmen)
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-netcore-daemon/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-java-project"></a>Schritt 2: Herunterladen des Java-Projekts

> [!div renderon="docs"]
> [Laden Sie das Java-Daemon-Projekt herunter.](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip).

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-java-project"></a>Schritt 3: Konfigurieren des Java-Projekts
>
> 1. Extrahieren Sie die ZIP-Datei in einem lokalen Ordner in der Nähe des Datenträger-Stammverzeichnisses (beispielsweise *C:\Azure-Samples*).
> 1. Navigieren Sie zum Unterordner **msal-client-credential-secret**.
> 1. Bearbeiten Sie *src\main\resources\application.properties*, und ersetzen Sie die Felder `AUTHORITY`, `CLIENT_ID` und `SECRET` durch den folgenden Codeausschnitt:
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    Hierbei gilt:
>    - `Enter_the_Application_Id_Here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.
>    - `Enter_the_Tenant_Id_Here`: Ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (z. B. „contoso.microsoft.com“).
>    - `Enter_the_Client_Secret_Here`: Ersetzen Sie diesen Wert durch den geheimen Clientschlüssel, den Sie in Schritt 1 erstellt haben.
>
> > [!TIP]
> > Die Werte für **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** finden Sie im Azure-Portal auf der Seite **Übersicht** der App. Navigieren Sie zum Generieren eines neuen Schlüssels zur Seite **Zertifikate & Geheimnisse**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Schritt 3: Administratorzustimmung

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Schritt 4: Administratorzustimmung

Wenn Sie zu diesem Zeitpunkt versuchen, die Anwendung auszuführen, wird der Fehler *HTTP 403 – Verboten* angezeigt: `Insufficient privileges to complete the operation`. Dieser Fehler tritt auf, da für eine *nur für die App geltende Berechtigung* eine Administratoreinwilligung erforderlich ist. Ein globaler Administrator Ihres Verzeichnisses muss der Anwendung also seine Einwilligung geben. Wählen Sie je nach Ihrer Rolle eine der unten angegebenen Optionen aus:

##### <a name="global-tenant-administrator"></a>Globaler Mandantenadministrator

> [!div renderon="docs"]
> Gehen Sie wie folgt vor, wenn Sie ein globaler Mandantenadministrator sind: Navigieren Sie im Azure-Portal unter **App-Registrierungen** zur Seite **API-Berechtigungen**, und wählen Sie **Administratorzustimmung für „{Mandantenname}“ erteilen** aus. (Hierbei steht „{Mandantenname}“ für den Namen Ihres Verzeichnisses.)

> [!div renderon="portal" class="sxs-lookup"]
> Gehen Sie als globaler Administrator wie folgt vor: Navigieren Sie zur Seite **API-Berechtigungen**, und wählen Sie **Administratorzustimmung für <Mandantennamen_hier_eingeben> erteilen** aus.
> > [!div id="apipermissionspage"]
> > [Navigieren Sie zur Seite „API-Berechtigungen“]().

##### <a name="standard-user"></a>Standardbenutzer

Wenn Sie ein Standardbenutzer Ihres Mandanten sind, müssen Sie einen globalen Administrator bitten, die Administratoreinwilligung für Ihre Anwendung zu erteilen. Übermitteln Sie hierzu die folgende URL an Ihren Administrator:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Hierbei gilt:
>> * `Enter_the_Tenant_Id_Here`: Ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (z.B. „contoso.microsoft.com“).
>> * `Enter_the_Application_Id_Here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Schritt 4: Ausführen der Anwendung

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Schritt 5: Ausführen der Anwendung

Sie können das Beispiel direkt testen, indem Sie die main-Methode von „ClientCredentialGrant.java“ über Ihre IDE ausführen.

Verwenden Sie in Ihrer Shell oder Befehlszeile Folgendes:

```
$ mvn clean compile assembly:single
```

Dadurch wird die Datei „msal-client-credential-secret-1.0.0.jar“ im Verzeichnis „/targets“ erstellt. Führen Sie diese mithilfe der ausführbaren Java-Datei wie folgt aus:

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

Nach der Ausführung sollte die Anwendung die Liste der Benutzer im konfigurierten Mandanten anzeigen.


> [!IMPORTANT]
> Für die Anwendung in dieser Schnellstartanleitung wird ein Clientgeheimnis verwendet, um sich selbst als vertraulicher Client zu identifizieren. Da das Clientgeheimnis Ihren Projektdateien als Nur-Text hinzugefügt wird, wird aus Sicherheitsgründen empfohlen, ein Zertifikat anstelle eines Clientgeheimnisses zu verwenden, bevor die Anwendung als Produktionsanwendung eingestuft wird. Weitere Informationen zur Verwendung eines Zertifikats finden Sie in [diesen Anweisungen](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) im zweiten Ordner (**msal-client-credential-certificate**) des GitHub-Repositorys für dieses Beispiel.

## <a name="more-information"></a>Weitere Informationen

### <a name="msal-java"></a>MSAL Java

[MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) ist die Bibliothek zum Anmelden von Benutzern und Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet wird. In dieser Schnellstartanleitung werden wie beschrieben Token angefordert, indem keine delegierten Berechtigungen verwendet werden, sondern die eigene Identität der Anwendung. Der hier genutzte Authentifizierungsablauf wird als *[OAuth-Ablauf mit Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md)* bezeichnet. Weitere Informationen zur Verwendung von MSAL Java mit Daemon-Apps finden Sie in [diesem Artikel](scenario-daemon-overview.md).

Sie können Ihrer Anwendung MSAL4J hinzufügen, indem Sie Maven oder Gradle für die Verwaltung Ihrer Abhängigkeiten verwenden. Hierzu sind in Ihrer Anwendung folgende Änderungen an der Datei „pom.xml“ (Maven) oder „build.gradle“ (Gradle) erforderlich:

In „pom.xml“:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

In „build.gradle“:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>MSAL-Initialisierung

Fügen Sie einen Verweis auf MSAL für Java hinzu, indem Sie am Anfang der Datei, in der Sie MSAL4J verwenden möchten, den folgenden Code hinzufügen:

```Java
import com.microsoft.aad.msal4j.*;
```

Initialisieren Sie MSAL anschließend mit dem folgenden Code:

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | Hierbei gilt: |BESCHREIBUNG |
> |---------|---------|
> | `CLIENT_SECRET` | Der geheime Clientschlüssel, der für die Anwendung im Azure-Portal erstellt wird. |
> | `CLIENT_ID` | Die **Anwendungs-ID (Client)** für die im Azure-Portal registrierte Anwendung. Dieser Wert befindet sich im Azure-Portal auf der Seite **Übersicht** der App. |
> | `AUTHORITY`    | Der STS-Endpunkt für den zu authentifizierenden Benutzer. Normalerweise `https://login.microsoftonline.com/{tenant}` für die öffentliche Cloud, wobei „{tenant}“ der Name Ihres Mandanten bzw. Ihre Mandanten-ID ist.|

### <a name="requesting-tokens"></a>Anfordern von Token

Verwenden Sie die `acquireToken`-Methode, um ein Token mit der Identität einer App anzufordern:

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |Hierbei gilt:| BESCHREIBUNG |
> |---------|---------|
> | `SCOPE` | Enthält die angeforderten Bereiche. Für vertrauliche Clients sollte ein Format wie `{Application ID URI}/.default` verwendet werden. Hiermit wird angegeben, dass die angeforderten Bereiche diejenigen sind, die im App-Objekt, das im Azure-Portal festgelegt ist, statisch definiert sind (für Microsoft Graph wird für `{Application ID URI}` auf `https://graph.microsoft.com` verwiesen). Für benutzerdefinierte Web-APIs wird `{Application ID URI}` im Azure-Portal unter **App-Registrierungen** im Abschnitt **Eine API verfügbar machen** definiert.|

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Daemon-Anwendungen finden Sie auf der Landing Page des Szenarios.

> [!div class="nextstepaction"]
> [Daemon-App zum Aufrufen von Web-APIs](scenario-daemon-overview.md)
