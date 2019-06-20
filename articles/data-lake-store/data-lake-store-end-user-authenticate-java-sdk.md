---
title: 'Authentifizierung von Endbenutzern: Java mit Azure Data Lake Storage Gen1 mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie die Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory mit Java umsetzen.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8b558fca964f33d47d331e007329d1bae2626877
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878100"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mit Java
> [!div class="op_single_selector"]
> * [Verwenden von Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Verwenden des .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Verwenden von Python](data-lake-store-end-user-authenticate-python.md)
> * [Verwenden der REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

In diesem Artikel erfahren Sie, wie Sie mithilfe des Java SDK die Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 durchführen. Informationen zur Dienst-zu-Dienst-Authentifizierung bei Data Lake Storage Gen1 mithilfe des Java SDK finden Sie unter [Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 mit Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Voraussetzungen
* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Erstellen einer nativen Azure Active Directory-Anwendung.** Sie müssen die Schritte unter [Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) ausgeführt haben.

* [Maven](https://maven.apache.org/install.html). Dieses Tutorial verwendet Maven für die Erstellung und für Projektabhängigkeiten. Die Erstellung ist zwar auch ohne ein Buildsystem wie Maven oder Gradle möglich, mit einem solchen System lassen sich Abhängigkeiten jedoch deutlich einfacher verwalten.

* (Optional) Eine IDE wie [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) oder [Eclipse](https://www.eclipse.org/downloads/).

## <a name="end-user-authentication"></a>Authentifizierung von Endbenutzern
1. Erstellen Sie ein Maven-Projekt. Verwenden Sie hierfür [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) in der Befehlszeile oder eine IDE. Eine Anleitung zum Erstellen eines Java-Projekts mit IntelliJ finden Sie [hier](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Eine Anleitung zum Erstellen eines Projekts mit Eclipse finden Sie [hier](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Fügen Sie Ihrer Maven-Datei **pom.xml** die folgenden Abhängigkeiten hinzu. Fügen Sie den folgenden Codeausschnitt vor dem Tag **\</project>** hinzu:
   
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
   
    Die erste Abhängigkeit ist die Verwendung des Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`) aus dem Maven-Repository. Die zweite Abhängigkeit ist die Angabe des Protokollierungsframeworks (`slf4j-nop`) für diese Anwendung. Das Data Lake Storage Gen1 SDK verwendet die Protokollierungsfassade [slf4j](https://www.slf4j.org/), bei der Sie aus einer Reihe gängiger Protokollierungsframeworks wie log4j, Java-Protokollierung, Logback usw. wählen oder die Protokollierung deaktivieren können. Da wir im vorliegenden Beispiel die Protokollierung deaktivieren möchten, verwenden wir die Bindung **slf4j-nop**. Informationen zur Verwendung anderer Protokollierungsoptionen für Ihre App finden Sie [hier](https://www.slf4j.org/manual.html#projectDep).

3. Fügen Sie Ihrer Anwendung die folgenden Importanweisungen hinzu.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Rufen Sie mit dem folgenden Codeausschnitt in Ihrer Java-Anwendung ein Token für die native Active Directory-Anwendung ab, die Sie zuvor mit `DeviceCodeTokenProvider` erstellt haben. Ersetzen Sie **FILL-IN-HERE** durch die Werte für die native Azure Active Directory-Anwendung.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

Das Data Lake Storage Gen1 SDK ermöglicht die komfortable Verwaltung der Sicherheitstoken, die für die Kommunikation mit dem Data Lake Storage Gen1-Konto benötigt werden. Das SDK ermöglicht jedoch auch die Verwendung anderer Methoden. Token können daher auch auf andere Weise abgerufen werden – etwa mit dem [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) oder mit eigenem benutzerdefiniertem Code.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie die Authentifizierung von Endbenutzern verwenden, um sich mithilfe des Java SDK bei Azure Data Lake Storage Gen1 zu authentifizieren. In den folgenden Artikeln wird erörtert, wie Sie das Java SDK mit Azure Data Lake Storage Gen1 verwenden.

* [Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit dem Java SDK](data-lake-store-get-started-java-sdk.md)


