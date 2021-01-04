---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 09b120838c27f8fa6b4f7bdf8617ce806f740e3c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007296"
---
Azure Blob Storage ist die Objektspeicherlösung von Microsoft für die Cloud. Blobspeicher ist für die Speicherung großer Mengen unstrukturierter Daten optimiert. Unstrukturierte Daten sind Daten, die keinem bestimmten Datenmodell und keiner bestimmten Definition entsprechen (also beispielsweise Text- oder Binärdaten).

## <a name="about-blob-storage"></a>Informationen zu Azure-Blobspeicher

Blobspeicher ist für Folgendes konzipiert:

* Speichern von Bildern oder Dokumenten direkt für einen Browser
* Speichern von Dateien für verteilten Zugriff
* Video- und Audio-Streaming
* Schreiben in Protokolldateien
* Speichern von Daten für Sicherung und Wiederherstellung, Notfallwiederherstellung und Archivierung
* Speichern von Daten für Analysen durch einen lokalen oder von Azure gehosteten Dienst

Benutzer- oder Clientanwendungen können von überall auf der Welt über HTTP/HTTPS auf Objekte in Blobspeicher zugreifen. Auf Objekte in Blobspeicher kann über die [Azure Storage-REST-API](/rest/api/storageservices/blob-service-rest-api), über [Azure PowerShell](/powershell/module/az.storage), über die [Azure-Befehlszeilenschnittstelle](/cli/azure/storage) oder über eine Azure Storage-Clientbibliothek zugegriffen werden. Clientbibliotheken sind für verschiedene Sprachen verfügbar, einschließlich:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Informationen zu Azure Data Lake Storage Gen2

Blobspeicher unterstützt Azure Data Lake Storage Gen2, die cloudbasierte Big Data-Analyselösung von Microsoft für Unternehmen. Azure Data Lake Storage Gen2 bietet ein hierarchisches Dateisystem sowie die Vorteile von Blobspeicher, u. a.:

* Kostengünstiger, mehrstufiger Speicher
* Hochverfügbarkeit
* Hohe Konsistenz
* Funktionen für die Notfallwiederherstellung

Weitere Informationen zu Data Lake Storage Gen2 finden Sie in der [Einführung in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md).