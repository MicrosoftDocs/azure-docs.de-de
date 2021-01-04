---
title: Dienst-zu-Dienst-Authentifizierung – Data Lake Storage Gen1 – Java SDK
description: Hier erfahren Sie, wie Sie mithilfe von Azure Active Directory und Java die Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 implementieren.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: 0e320557a7372af6a41038d9b3196db23d2496c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000384"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 mithilfe von Java

> [!div class="op_single_selector"]
> * [Verwenden von Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Verwenden des .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Verwenden von Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Verwenden der REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

In diesem Artikel erfahren Sie, wie Sie mithilfe des Java SDK die Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 durchführen. Die Authentifizierung von Endbenutzern bei Data Lake Storage Gen1 mithilfe des Java SDK wird nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Erstellen einer Azure Active Directory-Webanwendung.** Sie müssen die Schritte unter [Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md) ausgeführt haben.

* [Maven](https://maven.apache.org/install.html). Dieses Tutorial verwendet Maven für die Erstellung und für Projektabhängigkeiten. Obwohl die Erstellung auch ohne ein Buildsystem wie Maven oder Gradle möglich ist, lassen sich Abhängigkeiten mit einem solchen System viel einfacher verwalten.

* (Optional) Eine IDE wie [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) oder [Eclipse](https://www.eclipse.org/downloads/).

## <a name="service-to-service-authentication"></a>Dienst-zu-Dienst-Authentifizierung

1. Erstellen Sie ein Maven-Projekt. Verwenden Sie hierfür [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) in der Befehlszeile oder eine IDE. Eine Anleitung zum Erstellen eines Java-Projekts mit IntelliJ finden Sie [hier](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Eine Anleitung zum Erstellen eines Projekts mit Eclipse finden Sie [hier](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Fügen Sie Ihrer Maven-Datei **pom.xml** die folgenden Abhängigkeiten hinzu. Fügen Sie den folgenden Codeausschnitt vor dem Tag **\</project>** hinzu:

    ```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.2.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    Die erste Abhängigkeit ist die Verwendung des Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`) aus dem Maven-Repository. Die zweite Abhängigkeit ist die Angabe des Protokollierungsframeworks (`slf4j-nop`) für diese Anwendung. Das Data Lake Storage Gen1 SDK verwendet die Protokollierungsfassade [slf4j](https://www.slf4j.org/), bei der Sie aus einer Reihe gängiger Protokollierungsframeworks wie log4j, Java-Protokollierung, Logback usw. wählen oder die Protokollierung deaktivieren können. Da wir im vorliegenden Beispiel die Protokollierung deaktivieren möchten, verwenden wir die Bindung **slf4j-nop**. Informationen zur Verwendung anderer Protokollierungsoptionen für Ihre App finden Sie [hier](https://www.slf4j.org/manual.html#projectDep).

3. Fügen Sie Ihrer Anwendung die folgenden Importanweisungen hinzu.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
    ```

4. Rufen Sie mit dem folgenden Codeausschnitt in Ihrer Java-Anwendung ein Token für die Active Directory-Webanwendung ab, die Sie zuvor mit einer der Unterklassen von `AccessTokenProvider` erstellt haben. (Im folgenden Beispiel wird `ClientCredsTokenProvider` verwendet). Der Tokenanbieter speichert die verwendeten Anmeldeinformationen zwischen, um das Token im Arbeitsspeicher abzurufen, und erneuert das Token automatisch, bevor es abläuft. Sie können zwar auch eigene Unterklassen von `AccessTokenProvider` erstellen, damit Token durch Ihren Kundencode abgerufen werden. Wir verwenden jedoch fürs Erste die Unterklasse aus dem SDK.

    Ersetzen Sie **FILL-IN-HERE** durch die Werte für die Azure Active Directory-Webanwendung.

    ```java
    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   
    ```

Das Data Lake Storage Gen1 SDK ermöglicht die komfortable Verwaltung der Sicherheitstoken, die für die Kommunikation mit dem Data Lake Storage Gen1-Konto benötigt werden. Das SDK ermöglicht jedoch auch die Verwendung anderer Methoden. Token können daher auch auf andere Weise abgerufen werden – etwa mit dem [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) oder mit eigenem benutzerdefiniertem Code.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie die Authentifizierung von Endbenutzern verwenden, um sich mithilfe des Java SDK bei Data Lake Storage Gen1 zu authentifizieren. In den folgenden Artikeln wird erörtert, wie Sie das Java SDK mit Data Lake Storage Gen1 verwenden.

* [Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit dem Java SDK](data-lake-store-get-started-java-sdk.md)
