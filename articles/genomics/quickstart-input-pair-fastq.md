---
title: Übermitteln eines Workflows mit FASTQ-Eingabedateien in Microsoft Genomics
titleSuffix: Azure
description: In diesem Artikel wird vorausgesetzt, dass Sie den msgen-Client installiert und erfolgreich die Beispieldaten über den Dienst ausgeführt haben.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 2662a8f52c58a39916e5789fa9ed7fadd91216c0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60333671"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Übermitteln eines Workflows mit FASTQ-Eingabedateien in Microsoft Genomics

In diesem Artikel erfahren Sie, wie Sie einen Workflow an den Microsoft Genomics-Dienst übermitteln, wenn es sich bei Ihren Eingabedateien um ein einzelnes FASTQ-Dateipaar handelt. In diesem Thema wird vorausgesetzt, dass Sie den `msgen`-Client bereits installiert und ausgeführt haben und mit der Verwendung von Azure Storage vertraut sind. Wenn Sie erfolgreich einen Workflow mit den bereitgestellten Beispieldaten übermittelt haben, können Sie mit diesem Artikel fortfahren. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Einrichten: Hochladen Ihrer FASTQ-Dateien in Azure Storage
Angenommen, Sie haben zwei Dateien (*reads_1.fq.gz* und *reads_2.fq.gz*) als **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** und **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>** an Ihr Azure-Speicherkonto *myaccount* hochgeladen. Sie verfügen über die API-URL und den Zugriffsschlüssel. Als Ausgabeziel möchten Sie **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** verwenden.


## <a name="submit-your-job-to-the-msgen-client"></a>Übermitteln Ihres Auftrags an den `msgen`-Client 

Der `msgen`-Client benötigt mindestens folgende Argumente. Zur besseren Übersichtlichkeit wurden Zeilenumbrüche eingefügt:

Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Falls Sie lieber eine Konfigurationsdatei verwenden möchten, muss diese Folgendes enthalten:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Übermitteln Sie die Datei `config.txt` mithilfe des folgenden Aufrufs: `msgen submit -f config.txt`

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie ein FASTQ-Dateipaar in Azure Storage hochgeladen und einen Workflow über den `msgen`-Python-Client an den Microsoft Genomics-Dienst übermittelt. Weitere Informationen zur Workflowübermittlung sowie zu anderen Befehlen für den Microsoft Genomics-Dienst finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-genomics.md). 
