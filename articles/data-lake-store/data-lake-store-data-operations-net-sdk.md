---
title: '.NET SDK: Dateisystemvorgänge in Azure Data Lake Storage Gen1 | Microsoft-Dokumentation'
description: Verwenden Sie das Azure Data Lake Storage Gen1 .NET SDK, um Dateisystemvorgänge in Data Lake Storage Gen1 durchzuführen, z.B. das Erstellen von Ordnern usw.
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
ms.openlocfilehash: 0771c9c5311e264fb996bbac1c540f9ed11873cb
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65908071"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit dem .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

In diesem Artikel erfahren Sie, wie Sie Dateisystemvorgänge in Data Lake Storage Gen1 mithilfe des .NET SDK durchführen. Zu den Dateisystemvorgängen gehören das Erstellen von Ordnern in einem Data Lake Storage Gen1-Konto, das Hochladen von Dateien, das Herunterladen von Dateien usw.

Eine Anleitung zum Durchführen von Kontoverwaltungsvorgängen in Data Lake Storage Gen1 mit dem .NET SDK finden Sie unter [Erste Schritte mit Data Lake Storage Gen1 mit dem .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Voraussetzungen
* **Visual Studio 2013 und höher**. In der Anleitung unten wird Visual Studio 2019 verwendet.

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Ein Azure Data Lake Storage Gen1-Konto**. Eine Anleitung zum Erstellen eines Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Erstellen einer .NET-Anwendung
Das auf [GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) verfügbare Codebeispiel veranschaulicht Schritt für Schritt den Prozess zum Erstellen der Dateien im Speicher, zum Verketten von Dateien, zum Herunterladen einer Datei und zum Löschen einiger Dateien aus dem Speicher. In diesem Abschnitt des Artikels werden die wichtigsten Teile des Codes beschrieben.

1. Wählen Sie in Visual Studio das Menü **Datei**, dann die Option **Neu** und anschließend **Projekt** aus.
2. Wählen Sie **Konsolen-App (.NET Framework)** und dann **Weiter** aus.
3. Geben Sie unter **Projektname** `CreateADLApplication` ein, und wählen Sie dann **Erstellen** aus.

4. Fügen Sie Ihrem Projekt die NuGet-Pakete hinzu.

   1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und dann auf **NuGet-Pakete verwalten**.
   2. Stellen Sie auf der Registerkarte **NuGet-Paket-Manager** sicher, dass **Paketquelle** auf **nuget.org** festgelegt und das Kontrollkästchen **Vorabversion einbeziehen** aktiviert ist.
   3. Suchen und installieren Sie die folgenden NuGet-Pakete:

      * `Microsoft.Azure.DataLake.Store`: In diesem Tutorial wird „v1.0.0“ verwendet.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`: In diesem Tutorial wird „v2.3.1“ verwendet.
    
      Schließen Sie den **NuGet-Paket-Manager**.

5. Öffnen Sie **Program.cs**, löschen Sie den vorhandenen Code, und fügen Sie dann die folgenden Anweisungen ein, um Verweise auf Namespaces hinzuzufügen.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6. Deklarieren Sie die Variablen wie unten dargestellt, und geben Sie die Werte für die Platzhalter an. Stellen Sie außerdem sicher, dass der hier angegebene lokale Pfad und der Dateiname auf dem Computer vorhanden sind.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";        
            }
        }

In den restlichen Abschnitten dieses Artikels erfahren Sie, wie Sie die verfügbaren .NET-Methoden verwenden, um Vorgänge wie Authentifizierung, Dateiupload usw. durchzuführen.

## <a name="authentication"></a>Authentication

* Informationen zur Authentifizierung von Endbenutzern für Ihre Anwendung finden Sie unter [Authentifizierung von Endbenutzern mit Data Lake Storage Gen1 über das .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Informationen zur Dienst-zu-Dienst-Authentifizierung für Ihre Anwendung finden Sie unter [Dienst-zu-Dienst-Authentifizierung mit Data Lake Storage Gen1 über das .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Erstellen des Clientobjekts
Mit dem folgenden Codeausschnitt wird das Data Lake Storage Gen1-Dateisystem-Clientobjekt erstellt, das zum Ausführen von Anforderungen für den Dienst genutzt wird.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Erstellen einer Datei und eines Verzeichnisses
Fügen Sie Ihrer Anwendung den unten angegebenen Codeausschnitt hinzu. Mit diesem Codeausschnitt werden eine Datei und ggf. nicht vorhandene übergeordnete Verzeichnisse hinzugefügt.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Anfügen an eine Datei
Mit dem folgenden Codeausschnitt werden Daten an eine vorhandene Datei im Data Lake Storage Gen1-Konto angefügt.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Lesen einer Datei
Mit dem folgenden Codeausschnitt wird der Inhalt einer Datei in Data Lake Storage Gen1 gelesen.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Abrufen von Dateieigenschaften
Mit dem folgenden Codeausschnitt werden die Eigenschaften zurückgegeben, die einer Datei oder einem Verzeichnis zugeordnet sind:

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

Die Definition der `PrintDirectoryEntry`-Methode ist als Teil des Beispiels [auf GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) verfügbar. 

## <a name="rename-a-file"></a>Umbenennen einer Datei
Mit dem folgenden Codeausschnitt wird eine vorhandene Datei im Data Lake Storage Gen1-Konto umbenannt.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Aufzählen eines Verzeichnisses
Mit dem folgenden Codeausschnitt werden Verzeichnisse in einem Data Lake Storage Gen1-Konto aufgezählt:

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

Die Definition der `PrintDirectoryEntry`-Methode ist als Teil des Beispiels [auf GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) verfügbar.

## <a name="delete-directories-recursively"></a>Rekursives Löschen von Verzeichnissen
Mit dem folgenden Codeausschnitt werden ein Verzeichnis und alle Unterverzeichnisse rekursiv gelöscht.

    // Delete a directory and all its subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Beispiele
Hier sind zwei Beispiele für die Nutzung des Data Lake Storage Gen1-Dateisystem-SDK angegeben.
* [Einfaches Beispiel auf GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Erweitertes Beispiel auf GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Weitere Informationen
* [Kontoverwaltungsvorgänge für Data Lake Storage Gen1 mit dem .NET SDK](data-lake-store-get-started-net-sdk.md)
* [.NET SDK-Referenz zu Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Nächste Schritte
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
