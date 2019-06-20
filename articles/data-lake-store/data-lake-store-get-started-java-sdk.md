---
title: 'Java SDK: Dateisystemvorgänge in Azure Data Lake Storage Gen1 | Microsoft-Dokumentation'
description: Verwenden Sie das Azure Data Lake Storage Gen1 Java SDK, um in Data Lake Storage Gen1 Dateisystemvorgänge wie etwa das Erstellen von Ordnern auszuführen.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bc6e0718cdc4ccb18480dc760279da9c177db4cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60877463"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit dem Java SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Hier erfahren Sie, wie Sie mit dem Azure Data Lake Storage Gen1 Java SDK einfache Vorgänge ausführen (also beispielsweise Ordner erstellen oder Datendateien hoch- und herunterladen). Weitere Informationen zu Data Lake Storage Gen1 finden Sie unter [Azure Data Lake Storage Gen1](data-lake-store-overview.md).

Die Java SDK-API-Dokumente für Azure Data Lake Storage Gen1 finden Sie [hier](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Voraussetzungen
* Java Development Kit (JDK 7 oder höher mit Java-Version 1.7 oder höher)
* Data Lake Storage Gen1-Konto. Befolgen Sie die Anweisungen unter [Erste Schritte mit Azure Data Lake Storage Gen1 über das Azure-Portal](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Dieses Tutorial verwendet Maven für die Erstellung und für Projektabhängigkeiten. Die Erstellung ist zwar auch ohne ein Buildsystem wie Maven oder Gradle möglich, mit einem solchen System lassen sich Abhängigkeiten jedoch deutlich einfacher verwalten.
* (Optional) Eine IDE wie [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) oder [Eclipse](https://www.eclipse.org/downloads/).

## <a name="create-a-java-application"></a>Erstellen einer Java-Anwendung
Das auf [GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) verfügbare Codebeispiel veranschaulicht Schritt für Schritt den Prozess zum Erstellen der Dateien im Speicher, zum Verketten von Dateien, zum Herunterladen einer Datei und zum Löschen einiger Dateien aus dem Speicher. In diesem Abschnitt des Artikels werden die wichtigsten Teile des Codes beschrieben.

1. Erstellen Sie ein Maven-Projekt. Verwenden Sie hierfür [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) in der Befehlszeile oder eine IDE. Eine Anleitung zum Erstellen eines Java-Projekts mit IntelliJ finden Sie [hier](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Eine Anleitung zum Erstellen eines Projekts mit Eclipse finden Sie [hier](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Fügen Sie Ihrer Maven-Datei **pom.xml** die folgenden Abhängigkeiten hinzu. Fügen Sie den folgenden Codeausschnitt vor dem Tag **\</project>** hinzu:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
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
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Authentication

* Informationen zur Authentifizierung von Endbenutzern für Ihre Anwendung finden Sie unter [Authentifizierung von Endbenutzern mit Data Lake Storage Gen1 über Java](data-lake-store-end-user-authenticate-java-sdk.md).
* Informationen zur Dienst-zu-Dienst-Authentifizierung für Ihre Anwendung finden Sie unter [Dienst-zu-Dienst-Authentifizierung mit Data Lake Storage Gen1 über Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-a-data-lake-storage-gen1-client"></a>Erstellen eines Data Lake Storage Gen1-Clients
Zum Erstellen eines Objekts vom Typ [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) müssen Sie den Data Lake Storage Gen1-Kontonamen und den Tokenanbieter angeben, den Sie beim Authentifizieren mit Data Lake Storage Gen1 angegeben haben (siehe Abschnitt [Authentifizierung](#authentication)). Beim Namen des Data Lake Storage Gen1-Kontos muss es sich um einen vollqualifizierten Domänennamen handeln. Ersetzen Sie **FILL-IN-HERE** also beispielsweise durch einen Wert wie **mydatalakestoragegen1.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Die Codeausschnitte in den folgenden Abschnitten enthalten Beispiele für einige allgemeine Dateisystemvorgänge. Weitere Vorgänge finden Sie in den vollständigen [API-Dokumenten für das Data Lake Storage Gen1 Java SDK](https://azure.github.io/azure-data-lake-store-java/javadoc/) des Objekts **ADLStoreClient**.

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Im folgenden Codeausschnitt wird eine Verzeichnisstruktur im Stammverzeichnis des von Ihnen angegebenen Data Lake Storage Gen1-Kontos erstellt.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Erstellen von Dateien

Im folgenden Codeausschnitt wird eine Datei (c.txt) in der Verzeichnisstruktur erstellt, und es werden einige Daten in die Datei geschrieben.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

Sie können auch mithilfe von Bytearrays eine Datei (d.txt) erstellen.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

Die Definition für die Funktion `getSampleContent` im vorherigen Codeausschnitt ist im Rahmen des Beispiels [auf GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) verfügbar. 

## <a name="append-to-a-file"></a>Anfügen an eine Datei

Im folgenden Codeausschnitt wird Inhalt an eine vorhandene Datei angefügt.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

Die Definition für die Funktion `getSampleContent` im vorherigen Codeausschnitt ist im Rahmen des Beispiels [auf GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) verfügbar.

## <a name="read-a-file"></a>Lesen einer Datei

Im folgenden Codeausschnitt wird Inhalt aus einer Datei in einem Data Lake Storage Gen1-Konto gelesen.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Verketten von Dateien

Im folgenden Codeausschnitt werden zwei Dateien in einem Data Lake Storage Gen1-Konto verkettet. Bei erfolgreicher Ausführung ersetzt die verkettete Datei die beiden vorhandenen Dateien.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Umbenennen einer Datei

Im folgenden Codeausschnitt wird eine Datei in einem Data Lake Storage Gen1-Konto umbenannt.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Abrufen von Metadaten für eine Datei

Im folgenden Codeausschnitt werden die Metadaten für eine Datei in einem Data Lake Storage Gen1-Konto gelesen.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Festlegen von Berechtigungen für eine Datei

Im folgenden Codeausschnitt werden Berechtigungen für die Datei festgelegt, die Sie im vorherigen Abschnitt erstellt haben.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Auflisten des Verzeichnisinhalts

Im folgenden Codeausschnitt wird der Inhalt eines Verzeichnisses rekursiv aufgelistet.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

Die Definition für die Funktion `printDirectoryInfo` im vorherigen Codeausschnitt ist im Rahmen des Beispiels [auf GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) verfügbar.

## <a name="delete-files-and-folders"></a>Löschen von Dateien und Ordnern

Im folgenden Codeausschnitt werden die angegebenen Dateien und Ordner in einem Data Lake Storage Gen1-Konto rekursiv gelöscht.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Erstellen und Ausführen der Anwendung
1. Verwenden Sie in einer IDE die Schaltfläche **Ausführen**, um die Anwendung auszuführen. Verwenden Sie in Maven den Befehl [exec:exec](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html), um die Anwendung auszuführen.
2. Wenn Sie eine eigenständige, über die Befehlszeile ausführbare JAR-Datei generieren möchten, erstellen Sie mithilfe des [Maven-Assembly-Plug-Ins](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html) eine JAR-Datei mit sämtlichen Abhängigkeiten. Ein Beispiel hierzu finden Sie in der Datei „pom.xml“ aus dem [Beispielquellcode auf GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml).

## <a name="next-steps"></a>Nächste Schritte
* [Kennenlernen von JavaDoc für das Java SDK](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)


