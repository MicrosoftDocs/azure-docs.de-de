---
title: 'Verwenden des Objektspeichers (Blob-Speichers) in iOS: Azure | Microsoft-Dokumentation'
description: Speichern Sie nicht strukturierte Daten in der Cloud mit Azure Blob Storage (Objektspeicher).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 7fc1b2638c2ab17c4cd58ca8d4508d2e6d244cfa
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95996808"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Verwenden des Blobspeichers mit iOS

In diesem Artikel wird die Ausführung gängiger Szenarien mit Microsoft Azure Blob Storage demonstriert. Die Beispiele sind in Objective-C geschrieben und greifen auf die [Azure-Speicherclientbibliothek für iOS](https://github.com/Azure/azure-storage-ios)zurück. Die behandelten Szenarien umfassen das Hochladen, Auflisten, Herunterladen und Löschen von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#next-steps) . Sie können auch die [Beispielapp](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) herunterladen, um eine schnelle Demonstration der Verwendung von Azure Storage in einer iOS-Anwendung zu sehen.

Weitere Informationen zum Blob Storage finden Sie unter [Einführung in Azure Blob Storage](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importieren der Azure Storage-iOS-Bibliothek in Ihre Anwendung

Sie können die Azure Storage-iOS-Bibliothek entweder mithilfe von [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) oder durch Importieren der **Framework** -Datei in Ihre Anwendung importieren. CocoaPod ist die empfohlene Methode, da es die Integration der Bibliothek vereinfacht. Das Importieren der Frameworkdatei für Ihr bestehendes Projekt ist jedoch weniger aufwendig.

Sie benötigen Folgendes, um diese Bibliothek zu verwenden:

- iOS 8 oder höher
- Xcode 7 oder höher

## <a name="cocoapod"></a>CocoaPod

1. Sofern Sie es noch nicht getan haben, [installieren Sie CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) auf Ihrem Computer, indem Sie ein Terminalfenster öffnen und den folgenden Befehl ausführen:

    ```shell
    sudo gem install cocoapods
    ```

2. Erstellen Sie als Nächstes im Projektverzeichnis (das Verzeichnis mit Ihrer XCODEPROJ-Datei) eine neue Datei namens _Podfile_ (ohne Erweiterung). Fügen Sie _Podfile_ das Folgende hinzu, und speichern Sie die Datei.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. Navigieren Sie im Terminalfenster zu dem Projektverzeichnis, und führen Sie den folgenden Befehl aus:

    ```shell
    pod install
    ```

4. Wenn Ihre XCODEPROJ-Datei in Xcode geöffnet ist, schließen Sie sie. Öffnen Sie im Projektverzeichnis die neu erstellte Projektdatei, die die Erweiterung .xcworkspace erhält. Dies ist die Datei, mit der Sie von jetzt an arbeiten.

## <a name="framework"></a>Framework

Eine andere Möglichkeit, die Bibliothek zu verwenden, ist das manuelle Erstellen des Frameworks:

1. Laden Sie zuerst das [azure-storage-ios-repo](https://github.com/azure/azure-storage-ios)herunter, oder klonen Sie es.
2. Wechseln Sie zu *azure-storage-ios* -> *Lib* -> *Azure Storage-Client-Bibliothek*, und öffnen Sie `AZSClient.xcodeproj` in Xcode.
3. Ändern Sie oben links in Xcode das aktive Schema von "Azure Storage-Client Library" in "Framework".
4. Erstellen Sie das Projekt (⌘+B). Dadurch wird eine `AZSClient.framework`-Datei auf Ihrem Desktop erstellt.

Sie können die Frameworkdatei dann folgendermaßen in Ihre Anwendung importieren:

1. Erstellen Sie ein neues Projekt, oder öffnen Sie das vorhandene Projekt in Xcode.
2. Ziehen Sie `AZSClient.framework` in Ihren Xcode-Projektnavigator.
3. Wählen Sie *Copy items if needed* (Elemente kopieren, wenn nötig) aus, und klicken Sie dann auf *Add* (Hinzufügen).
4. Klicken Sie im linken Navigationsbereich auf Ihr Projekt, und klicken Sie auf die Registerkarte *General* (Allgemein) am oberen Rand des Projekteditors.
5. Klicken Sie unter dem Abschnitt *Linked Frameworks und Libraries* (Verknüpfte Frameworks und Bibliotheken) auf die Schaltfläche zum Hinzufügen (+).
6. Suchen Sie in der Liste der bereits bereitgestellten Bibliotheken nach `libxml2.2.tbd` , und fügen Sie es dem Projekt hinzu.

## <a name="import-the-library"></a>Importieren der Bibliothek

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Wenn Sie Swift verwenden, müssen Sie einen Bridging-Header erstellen und \<AZSClient/AZSClient.h> hierhin importieren:

1. Erstellen Sie eine Headerdatei `Bridging-Header.h`, und fügen Sie die oben genannte Import-Anweisung hinzu.
2. Wechseln Sie zur Registerkarte *Buildeinstellungen*, und suchen Sie nach *Objective-C Bridging Header*.
3. Doppelklicken Sie auf das Feld *Objective-C Bridging Header*, und fügen Sie den Pfad zu Ihrer Header-Datei hinzu: `ProjectName/Bridging-Header.h`
4. Erstellen Sie das Projekt (⌘ + B), um zu prüfen, ob der Bridging-Header von Xcode übernommen wurde.
5. Sie können die Bibliothek direkt in jeder Swift-Datei verwenden, es besteht keine Notwendigkeit für Import-Anweisungen.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynchrone Vorgänge

> [!NOTE]
> Alle Methoden, die eine Anforderung für den Dienst ausführen, sind asynchrone Vorgänge. In den Codebeispielen werden Sie feststellen, dass diese Methoden einen Abschlusshandler haben. Der Code im Abschlusshandler wird **nach** Abschluss der Anforderung ausgeführt. Der Code nach dem Abschlusshandler wird **während** der Anforderung ausgeführt.

## <a name="create-a-container"></a>Erstellen eines Containers

Jeder Blob in Azure Storage muss sich in einem Container befinden. Das folgende Beispiel zeigt, wie Sie einen Container namens *newcontainer* in Ihrem Storage-Konto erstellen, wenn er noch nicht vorhanden ist. Wenn Sie einen Namen für den Container auswählen, achten Sie auf die oben genannten Benennungsregeln.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Sie können überprüfen, ob dies funktioniert, indem Sie sich vergewissern, dass [newcontainer](https://storageexplorer.com) sich im *Microsoft Azure Storage-Explorer* in der Liste der Container für das Speicherkonto befindet.

## <a name="set-container-permissions"></a>Festlegen von Containerberechtigungen

Die Berechtigungen eines Containers werden standardmäßig für den **Privatzugriff** konfiguriert. Container bieten jedoch einige unterschiedliche Optionen für den Containerzugriff:

- **Privat**: Container- und Blobdaten können nur vom Kontobesitzer gelesen werden.
- **Blob**: Blob-Daten innerhalb dieses Containers können über anonyme Anforderungen gelesen werden, Containerdaten sind aber nicht verfügbar. Clients können keine Blobs innerhalb des Containers über anonyme Anforderungen aufzählen.
- **Container**: Container- und Blobdaten können über anonyme Anforderungen gelesen werden. Clients können Blobs innerhalb des Containers über eine anonyme Anforderung aufzählen, können aber keine Container innerhalb des Speicherkontos aufzählen.

Das folgende Beispiel veranschaulicht das Erstellen eines Containers mit **Container**-Zugriffsberechtigungen, die allen Benutzern im Internet den öffentlichen, schreibgeschützten Zugriff gewähren:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Hochladen eines Blobs in einen Container

Wie im Abschnitt „Blobdienstkonzepte“ erwähnt, bietet der Blobspeicher drei verschiedene Arten von Blobs: Blockblobs, Anfügeblobs und Seitenblobs. Die iOS-Bibliothek von Azure Storage unterstützt alle drei Arten von Blobs. In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Das folgende Beispiel zeigt den Upload eines Blockblobs aus einer NSString. Wenn in diesem Container bereits ein Blob mit demselben Namen vorhanden ist, wird der Inhalt dieses Blobs überschrieben.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Sie können überprüfen, ob dies funktioniert, indem Sie sich vergewissern, dass der Container *containerpublic* im [Microsoft Azure Storage-Explorer](https://storageexplorer.com) das Blob *sampleblob* enthält. In diesem Beispiel haben Sie einen öffentlichen Container verwendet, sodass Sie die Funktionsweise der Anwendung auch durch das Aufrufen der Blob-URI überprüfen können:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Neben dem Hochladen eines Blockblobs aus einer NSString gibt es ähnliche Methoden für NSData, NSInputStream oder eine lokale Datei.

## <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Das folgende Beispiel zeigt, wie alle Blobs in einem Container aufgelistet werden. Achten Sie beim Durchführen dieses Vorgangs auf die folgenden Parameter:

- **ContinuationToken** : Das Fortsetzungstoken gibt an, wo der Auflistungsvorgang beginnen soll. Wenn kein Token angegeben wird, listet es Blobs vom Anfang auf. Es kann eine beliebige Anzahl von Blobs aufgeführt werden, von 0 (null) bis zu einem festgelegten Maximum. Auch wenn diese Methode keine Ergebnisse zurückgibt, wenn `results.continuationToken` nicht Null ist, gibt es möglicherweise mehr Blobs für den Dienst, die nicht aufgelistet wurden.
- **prefix** – Sie können das Präfix angeben, das für die Blobliste verwendet werden soll. Nur Blobs, die mit diesem Präfix beginnen, werden aufgelistet.
- **useFlatBlobListing** – Wie im Abschnitt [Benennen von Containern und Blobs und verweisen auf diese](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) erwähnt, können Sie eine virtuelle Hierarchie durch Benennen von Blobs mit Pfadinformationen erstellen, selbst wenn der Blob-Dienst ein flaches Speicherschema ist. Allerdings wird derzeit die nicht-flache Auflistung nicht unterstützt. Diese Funktion ist in Kürze verfügbar. Bis dahin sollte der folgende Wert verwendet werden: **JA**.
- **blobListingDetails** – Sie können angeben, welche Elemente beim Auflisten der Blobs einbezogen werden sollen.
  - _AZSBlobListingDetailsNone_: Listen Sie nur zugesicherte Blobs auf, und geben Sie keine Blobmetadaten zurück.
  - _AZSBlobListingDetailsSnapshots_: Listen Sie zugesicherte Blobs und Blobmomentaufnahmen auf.
  - _AZSBlobListingDetailsMetadata_: Rufen Sie Blobmetadaten für jedes Blob auf, das in der Liste zurückgegeben wird.
  - _AZSBlobListingDetailsUncommittedBlobs_: Listen Sie zugesicherte und nicht zugesicherte Blobs auf.
  - _AZSBlobListingDetailsCopy_: Schließen Sie Kopiereigenschaften in die Liste ein.
  - _AZSBlobListingDetailsAll_: Listen Sie alle verfügbaren zugesicherten Blobs, nicht zugesicherten Blobs und Momentaufnahmen auf, und geben Sie alle Metadaten und den Kopierstatus für diese Blobs zurück.
- **maxResults** – Die maximale Anzahl von Ergebnissen, die für diesen Vorgang zurückgegeben werden. Verwenden Sie -1, um keinen Grenzwert festzulegen.
- **completionHandler** – Der auszuführende Codeblock mit den Ergebnissen des Auflistungsvorgangs.

In diesem Beispiel wird eine Hilfsmethode verwendet, um jedes Mal die Methode zum Auflisten von Blobs wiederholt aufzurufen, wenn ein Fortsetzungstoken zurückgegeben wird.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Herunterladen eines Blobs

Das folgende Beispiel zeigt den Download eines Blobs in ein NSString-Objekt.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Löschen eines BLOBs

Das folgende Beispiel zeigt den Löschvorgang eines Blobs.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Löschen eines Blobcontainers

Das folgende Beispiel zeigt den Löschvorgang eines Containers.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Nächste Schritte

Da Sie jetzt die Verwendung von Blobspeicher in iOS kennen, folgen Sie diesen Links, um weitere Informationen zur iOS-Bibliothek und dem Speicherdienst zu erhalten.

- [Azure Storage-Clientbibliothek für iOS](https://github.com/azure/azure-storage-ios)
- [Azure Storage-iOS-Referenzdokumentation](https://azure.github.io/azure-storage-ios/)
- [REST-API für Azure-Speicherdienste](/rest/api/storageservices/)
- [Azure Storage-Teamblog](/archive/blogs/windowsazurestorage/)

Wenn Sie Fragen zu dieser Bibliothek haben, können Sie diese auf unserer [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-blob-storage.html) oder auf [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files) stellen.
Richten Sie Vorschläge für Azure Storage-Features an [Feedback zu Azure Storage](https://feedback.azure.com/forums/217298-storage/).