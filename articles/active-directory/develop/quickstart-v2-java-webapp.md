---
title: 'Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer Java-Web-App | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Schnellstart erfahren Sie, wie Sie „Mit Microsoft anmelden“ mit OpenID Connect in einer Java-Webanwendung implementieren.
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
ms.openlocfilehash: e93c0c6bb689980cab1b41e529c491cdf3920260
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591715"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer Java-Web-App

In diesem Schnellstart laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine Java-Webanwendung Benutzer anmelden und die Microsoft Graph-API aufrufen kann. Benutzer aus einer beliebigen Azure AD-Organisation (Azure Active Directory) können sich bei der Anwendung anmelden.

 Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel benötigen Sie Folgendes:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 oder höher und [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrieren und Herunterladen Ihrer Schnellstart-App
> Die Schnellstartanwendung kann auf zwei Arten gestartet werden: Express (Option 1) oder manuell (Option 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
>
> 1. Navigieren Sie zur Umgebung des Schnellstarts [Azure-Portal – App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs).
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Folgen Sie den Anweisungen in der Schnellstartumgebung des Portals, um den automatisch konfigurierten Anwendungscode herunterzuladen.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels
>
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
>
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
> 1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
>
> 1. Navigieren Sie zur Seite [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) von Microsoft Identity Platform für Entwickler.
> 1. Wählen Sie **Neue Registrierung** aus.
> 1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen für Ihre Anwendung ein:
>    - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise `java-webapp`).
>    - Wählen Sie **Registrieren**.
> 1. Suchen Sie auf der Seite **Übersicht** die Werte **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** der Anwendung. Kopieren Sie diese Werte für später.
> 1. Wählen Sie im Menü die Option **Authentifizierung** aus, und fügen Sie anschließend folgende Informationen hinzu:
>    - Fügen Sie die Plattformkonfiguration **Web** hinzu.  Fügen Sie `https://localhost:8443/msal4jsample/secure/aad` und `https://localhost:8443/msal4jsample/graph/me` als **Umleitungs-URIs** hinzu.
>    - Wählen Sie **Speichern** aus.
> 1. Wählen Sie im Menü **Certificates & secrets** (Zertifikate und Geheimnisse) aus, und klicken Sie im Abschnitt **Geheime Clientschlüssel** auf **Neuer geheimer Clientschlüssel**:
>
>    - Geben Sie eine Schlüsselbeschreibung ein (beispielsweise „app secret“).
>    - Wählen Sie als Schlüsseldauer die Option **In 1 Jahr** aus.
>    - Der Schlüsselwert wird angezeigt, wenn Sie **Hinzufügen** auswählen.
>    - Kopieren Sie den Wert des Schlüssels zur späteren Verwendung. Der Schlüsselwert wird nicht erneut angezeigt und kann auch nicht auf andere Weise abgerufen werden. Erfassen Sie ihn daher, sobald er im Azure-Portal angezeigt wird.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
>
> Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, sind folgende Schritte erforderlich:
>
> 1. Fügen Sie Antwort-URLs als `https://localhost:8443/msal4jsample/secure/aad` und `https://localhost:8443/msal4jsample/graph/me` hinzu.
> 1. Erstellen Sie einen geheimen Clientschlüssel.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]() (Diese Änderungen für mich vornehmen)
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-aspnet-webapp/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-code-sample"></a>Schritt 2: Herunterladen des Codebeispiels
> [!div renderon="docs"]
> [Codebeispiel herunterladen](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Laden Sie das Projekt herunter, und extrahieren Sie die ZIP-Datei in einen lokalen Ordner, der sich näher am Stammordner befindet (beispielsweise **C:\Azure-Samples**).
>
> Wenn Sie HTTPS mit „localhost“ verwenden möchten, geben Sie die Eigenschaften vom Typ „server.ssl.key“ an. Verwenden Sie zum Generieren eines selbstsignierten Zertifikats das keytool-Hilfsprogramm (in JRE enthalten).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Speichern Sie die generierte Schlüsselspeicherdatei im Ordner „resources“.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip).

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Schritt 3: Konfigurieren des Codebeispiels
> 1. Extrahieren Sie die ZIP-Datei in einen lokalen Ordner.
> 1. Falls Sie eine integrierte Entwicklungsumgebung (Integrated Development Environment, IDE) verwenden, können Sie das Beispiel in Ihrer bevorzugten IDE öffnen. (Dieser Schritt ist optional.)
> 1. Öffnen Sie die Datei „application.properties“ (im Ordner „src/main/resources/“), und ersetzen Sie die Werte der Felder *aad.clientId*, *aad.authority* und *aad.secretKey* durch die entsprechenden Werte für **Anwendungs-ID**, **Mandanten-ID** und **Geheimer Clientschlüssel**:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Hierbei gilt:
>
> - `Enter_the_Application_Id_here` ist die Anwendungs-ID für die von Ihnen registrierte Anwendung.
> - `Enter_the_Client_Secret_Here` ist der **geheime Clientschlüssel**, den Sie unter **Certificates & Secrets** (Zertifikate und Geheimnisse) für die registrierte Anwendung erstellt haben.
> - `Enter_the_Tenant_Info_Here` ist der Wert der **Verzeichnis-ID (Mandant)** der Anwendung, die Sie registriert haben.
> 1. Wenn Sie HTTPS mit „localhost“ verwenden möchten, geben Sie die Eigenschaften vom Typ „server.ssl.key“ an. Verwenden Sie zum Generieren eines selbstsignierten Zertifikats das keytool-Hilfsprogramm (in JRE enthalten).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Speichern Sie die generierte Schlüsselspeicherdatei im Ordner „resources“.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Schritt 3: Ausführen des Codebeispiels
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Schritt 4: Ausführen des Codebeispiels

Bei der Projektausführung haben Sie folgende Möglichkeiten:

Sie können das Projekt entweder direkt über Ihre IDE ausführen, indem Sie den eingebetteten Spring Boot-Server verwenden, oder es mithilfe von [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) als WAR-Datei packen und diese in einer J2EE-Containerlösung wie [Apache Tomcat](http://tomcat.apache.org/) bereitstellen.

##### <a name="running-from-ide"></a>Ausführen per IDE

Wenn Sie die Webanwendung über eine IDE ausführen möchten, klicken Sie auf „Ausführen“, und navigieren Sie zur Startseite des Projekts. In diesem Beispiel lautet die Standard-URL der Startseite https://localhost:8443.

1. Wählen Sie auf der ersten Seite die Schaltfläche **Anmelden** aus, um den Benutzer zu Azure Active Directory umzuleiten und zur Eingabe der Anmeldeinformationen aufzufordern.

1. Nach Abschluss der Benutzerauthentifizierung wird der Benutzer zu *https://localhost:8443/msal4jsample/secure/aad* umgeleitet. Der Benutzer ist nun angemeldet, und auf der Seite werden Informationen zum angemeldeten Konto angezeigt. Auf der Beispielbenutzeroberfläche stehen folgende Schaltflächen zur Verfügung:
    - *Sign Out* (Abmelden): Meldet den aktuellen Benutzer von der Anwendung ab und leitet ihn zur Startseite um.
    - *Show User Info* (Benutzerinformationen anzeigen): Ruft ein Token für Microsoft Graph ab und ruft Microsoft Graph mit einer Anforderung auf, die das Token enthält, woraufhin grundlegende Informationen zum angemeldeten Benutzer zurückgegeben werden.

##### <a name="running-from-tomcat"></a>Ausführen in Tomcat

Wenn Sie das Webbeispiel in Tomcat bereitstellen möchten, müssen Sie einige Änderungen am Quellcode vornehmen.

1. Öffnen Sie „ms-identity-java-webapp/pom.xml“.
    - Fügen Sie `<packaging>war</packaging>` unter `<name>msal-web-sample</name>` hinzu.

2. Öffnen Sie „ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication“.

    - Löschen Sie den gesamten enthaltenen Code, und ersetzen Sie ihn durch Folgendes:

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

3.   Der HTTP-Standardport von Tomcat ist 8080. Es wird jedoch eine HTTPS-Verbindung über Port 8443 benötigt. Gehen Sie wie folgt vor, um dies zu konfigurieren:
        - Öffnen Sie „tomcat/conf/server.xml“.
        - Suchen Sie nach dem Tag `<connector>`, und ersetzen Sie den vorhandenen Connector durch Folgendes:

        ```xml
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ```

4. Öffnen Sie eine Eingabeaufforderung, wechseln Sie zum Stammordner dieses Beispiels (in dem sich die Datei „pom.xml“ befindet), und führen Sie `mvn package` aus, um das Projekt zu erstellen.
    - Dadurch wird eine Datei namens `msal-web-sample-0.1.0.war` im Verzeichnis „/targets“ generiert.
    - Benennen Sie die Datei in `msal4jsample.war` um.
    - Stellen Sie diese WAR-Datei mithilfe von Tomcat oder einer beliebigen anderen J2EE-Containerlösung bereit.
        - Um die Bereitstellung vorzunehmen, kopieren Sie die Datei „msal4jsample.war“ in das Verzeichnis `/webapps/` Ihrer Tomcat-Installation, und starten Sie dann den Tomcat-Server.

5. Navigieren Sie nach der Bereitstellung in Ihrem Browser zu https://localhost:8443/msal4jsample.


> [!IMPORTANT]
> Für die Anwendung in dieser Schnellstartanleitung wird ein Clientgeheimnis verwendet, um sich selbst als vertraulicher Client zu identifizieren. Da der geheime Clientschlüssel Ihren Projektdateien als Nur-Text hinzugefügt wird, empfiehlt es sich aus Sicherheitsgründen, ein Zertifikat anstelle eines Clientgeheimnisses zu verwenden, bevor die Anwendung in der Produktion genutzt wird. Weitere Informationen zur Verwendung eines Zertifikats finden Sie unter [Zertifikatanmeldeinformationen für die Anwendungsauthentifizierung](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Weitere Informationen

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
![Zeigt, wie die in diesem Schnellstart generierte Beispiel-App funktioniert](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Abrufen von MSAL

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

### <a name="msal-initialization"></a>MSAL-Initialisierung

Fügen Sie einen Verweis auf MSAL für Java hinzu, indem Sie am Anfang der Datei, in der Sie MSAL4J verwenden möchten, den folgenden Code hinzufügen:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Eine ausführlichere Erläuterung zum Entwickeln von Web-Apps, die Benutzer bei Microsoft Identity Platform anmelden, finden Sie in unserer mehrteiligen Szenarioreihe:

> [!div class="nextstepaction"]
> [Szenario: Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md?tabs=java)
