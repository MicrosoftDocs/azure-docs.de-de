---
title: 'Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer Java-Web-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie einer Java-Webanwendung per OpenID Connect die Option „Mit Microsoft anmelden“ hinzufügen.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: a7337175241834cef862b4af07c7bcf7c8b845d0
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103769"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer Java-Web-App

In diesem Schnellstart laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine Java-Webanwendung Benutzer anmelden und die Microsoft Graph-API aufrufen kann. Benutzer aus einer beliebigen Azure AD-Organisation (Azure Active Directory) können sich bei der Anwendung anmelden.

 Eine Übersicht finden Sie im [Diagramm zur Funktionsweise des Beispiels](#how-the-sample-works).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel benötigen Sie Folgendes:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 oder höher. 
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrieren und Herunterladen Ihrer Schnellstart-App
> Sie haben zwei Möglichkeiten, um Ihre Schnellstartanwendung zu starten: „Express“ (Option 1) oder „Manuell“ (Option 2).
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
>
> 1. Navigieren Sie zur Umgebung des Schnellstarts <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">Azure-Portal – App-Registrierungen</a>.
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und klicken Sie auf **Registrieren**.
> 1. Folgen Sie den Anweisungen in der Schnellstartumgebung des Portals, um den automatisch konfigurierten Anwendungscode herunterzuladen.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels
>
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
>
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und die Registrierungsinformationen Ihrer App manuell hinzuzufügen:
>
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie die Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** aus.
> 1. Wählen Sie **Neue Registrierung** aus.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein, z. B. **java-webapp**. Dieser Name wird Benutzern Ihrer App ggf. angezeigt. Sie können ihn später noch ändern.
> 1. Wählen Sie **Registrieren**.
> 1. Notieren Sie sich auf der Seite **Übersicht** die Werte für **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** . Sie benötigen diese Werte später noch.
> 1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
> 1. Wählen Sie **Plattform hinzufügen** > **Web** aus.
> 1. Geben Sie im Abschnitt **Umleitungs-URIs** den URI `https://localhost:8443/msal4jsample/secure/aad` ein.
> 1. Wählen Sie **Konfigurieren** aus.
> 1. Geben Sie im Abschnitt **Web** unter **Umleitungs-URIs** den URI `https://localhost:8443/msal4jsample/graph/me` als zweiten Umleitungs-URI ein.
> 1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus. Wählen Sie im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus.
> 1. Geben Sie eine Schlüsselbeschreibung (z. B. *App-Geheimnis*) ein, behalten Sie den Standardablauf bei, und wählen Sie **Hinzufügen** aus.
> 1. Notieren Sie sich den **Wert** des geheimen Clientschlüssels. Sie benötigen die Information später.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
>
> So verwenden Sie das Codebeispiel in dieser Schnellstartanleitung:
>
> 1. Fügen Sie die Antwort-URLs `https://localhost:8443/msal4jsample/secure/aad` und `https://localhost:8443/msal4jsample/graph/me` hinzu.
> 1. Erstellen Sie einen geheimen Clientschlüssel.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]() (Diese Änderungen für mich vornehmen)
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-aspnet-webapp/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-code-sample"></a>Schritt 2: Herunterladen des Codebeispiels
> [!div renderon="docs"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip).

> [!div class="sxs-lookup" renderon="portal"]
> Laden Sie das Projekt herunter, und extrahieren Sie die ZIP-Datei in einen Ordner in der Nähe des Stammverzeichnisses Ihres Laufwerks. Beispiel: *C:\Azure-Samples*.
>
> Geben Sie die `server.ssl.key`-Eigenschaften an, um HTTPS mit localhost zu verwenden. Verwenden Sie zum Generieren eines selbstsignierten Zertifikats das keytool-Hilfsprogramm (in JRE enthalten).
>
> Hier sehen Sie ein Beispiel:
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Speichern Sie die generierte Keystore-Datei im Ordner *resources*.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip).

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Schritt 3: Konfigurieren des Codebeispiels
> 1. Extrahieren Sie die ZIP-Datei in einen lokalen Ordner.
> 1. *Optional.* Öffnen Sie das Beispiel in dieser Umgebung, wenn Sie eine integrierte Entwicklungsumgebung verwenden.
> 1. Öffnen Sie die Datei *application.properties*. Sie befindet sich im Ordner *src/main/resources/* . Ersetzen Sie die Werte in den Feldern `aad.clientId`, `aad.authority` und `aad.secretKey` durch die Werte für die Anwendungs-ID, Mandanten-ID und den geheimen Clientschlüssel. Dies sollte wie folgt aussehen:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    Im vorherigen Code:
>
>    - `Enter_the_Application_Id_here` ist die Anwendungs-ID für die von Ihnen registrierte Anwendung.
>    - `Enter_the_Client_Secret_Here` ist der **geheime Clientschlüssel**, den Sie unter **Zertifikate und Geheimnisse** für die registrierte Anwendung erstellt haben.
>    - `Enter_the_Tenant_Info_Here` ist der Wert der **Verzeichnis-ID (Mandant)** der Anwendung, die Sie registriert haben.
> 1. Geben Sie die `server.ssl.key`-Eigenschaften an, um HTTPS mit localhost zu verwenden. Verwenden Sie zum Generieren eines selbstsignierten Zertifikats das keytool-Hilfsprogramm (in JRE enthalten).
>
>    Hier sehen Sie ein Beispiel:
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. Speichern Sie die generierte Keystore-Datei im Ordner *resources*.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Schritt 3: Ausführen des Codebeispiels
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Schritt 4: Ausführen des Codebeispiels

Führen Sie zum Ausführen des Projekts einen der folgenden Schritte aus:

- Führen Sie das Codebeispiel direkt über Ihre IDE aus, indem Sie den eingebetteten Spring Boot-Server verwenden. 
- Nutzen Sie das Verpacken als WAR-Datei, indem Sie [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) verwenden und dann die Bereitstellung in einer J2EE-Containerlösung wie [Apache Tomcat](http://tomcat.apache.org/) durchführen.

##### <a name="running-the-project-from-an-ide"></a>Ausführen des Projekts aus einer IDE

Wählen Sie zum Ausführen der Webanwendung aus einer IDE den Befehl „run“ aus, und navigieren Sie dann zur Startseite des Projekts. In diesem Beispiel lautet die Standard-URL der Startseite https://localhost:8443.

1. Wählen Sie auf der ersten Seite die Schaltfläche **Anmelden** aus, um Benutzer zu Azure Active Directory umzuleiten und zur Eingabe der Anmeldeinformationen aufzufordern.

1. Nachdem die Benutzer authentifiziert wurden, werden sie an `https://localhost:8443/msal4jsample/secure/aad` umgeleitet. Der Benutzer ist nun angemeldet, und auf der Seite werden Informationen zum Benutzerkonto angezeigt. Die Beispielbenutzeroberfläche enthält die folgenden Schaltflächen:
    - **Sign Out** (Abmelden): Meldet den aktuellen Benutzer von der Anwendung ab und leitet ihn zur Startseite um.
    - **Show User Info** (Benutzerinformationen anzeigen): Ruft ein Token für Microsoft Graph ab und ruft Microsoft Graph mit einer Anforderung auf, die das Token enthält, woraufhin grundlegende Informationen zum angemeldeten Benutzer zurückgegeben werden.

##### <a name="running-the-project-from-tomcat"></a>Ausführen des Projekts über Tomcat

Wenn Sie das Webbeispiel in Tomcat bereitstellen möchten, müssen Sie einige Änderungen am Quellcode vornehmen.

1. Öffnen Sie *ms-identity-java-webapp/pom.xml*.
    - Fügen Sie `<packaging>war</packaging>` unter `<name>msal-web-sample</name>` hinzu.

2. Öffnen Sie *ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication*.

    - Löschen Sie den gesamten Quellcode, und ersetzen Sie ihn durch den folgenden Code:

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

3.   Der HTTP-Standardport von Tomcat ist 8080. Sie benötigen aber eine HTTPS-Verbindung über Port 8443. Konfigurieren Sie diese Einstellung wie folgt:
        - Navigieren Sie zu *tomcat/conf/server.xml*.
        - Suchen Sie nach dem Tag `<connector>`, und ersetzen Sie den vorhandenen Connector durch diesen Connector:

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

4. Öffnen Sie ein Eingabeaufforderungsfenster. Navigieren Sie zum Stammordner dieses Beispiels (in dem sich die Datei „pom.xml“ befindet), und führen Sie `mvn package` aus, um das Projekt zu erstellen.
    - Mit diesem Befehl wird die Datei *msal-web-sample-0.1.0.war* im Verzeichnis */targets* erstellt.
    - Benennen Sie diese Datei in *msal4jsample.war* um.
    - Stellen Sie diese WAR-Datei mit Tomcat oder einer beliebigen anderen J2EE-Containerlösung bereit.
        - Um die Bereitstellung vorzunehmen, kopieren Sie die Datei „msal4jsample.war“ in das Verzeichnis */webapps/* Ihrer Tomcat-Installation und starten dann den Tomcat-Server.

5. Navigieren Sie nach dem Bereitstellen der Datei in einem Browser zu https://localhost:8443/msal4jsample.


> [!IMPORTANT]
> Für die Anwendung in dieser Schnellstartanleitung wird ein Clientgeheimnis verwendet, um sich selbst als vertraulicher Client zu identifizieren. Da der geheime Clientschlüssel Ihren Projektdateien im Nur-Text-Format hinzugefügt wird, empfehlen wir Ihnen, aus Sicherheitsgründen ein Zertifikat anstelle eines Clientgeheimnisses zu verwenden, bevor Sie die Anwendung in einer Produktionsumgebung nutzen. Weitere Informationen zur Verwendung eines Zertifikats finden Sie unter [Zertifikatanmeldeinformationen für die Anwendungsauthentifizierung](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Weitere Informationen

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
![Diagramm: Funktionsweise der in dieser Schnellstartanleitung generierten Beispiel-App](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>Abrufen von MSAL

MSAL für Java (MSAL4J) ist die Java-Bibliothek zum Anmelden von Benutzern und Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet wird.

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

### <a name="initialize-msal"></a>MSAL initialisieren

Fügen Sie einen Verweis auf MSAL für Java hinzu, indem Sie am Anfang der Datei, in der Sie MSAL4J verwenden möchten, den folgenden Code hinzufügen:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Eine ausführlichere Erläuterung zum Entwickeln von Web-Apps, die Benutzer bei Microsoft Identity Platform anmelden, finden Sie in unserer mehrteiligen Szenarioreihe:

> [!div class="nextstepaction"]
> [Szenario: Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md?tabs=java)
