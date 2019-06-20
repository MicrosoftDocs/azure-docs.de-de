---
title: Übermitteln eines Workflows mit mehreren Eingaben – Microsoft Genomics
titleSuffix: Azure
description: In diesem Artikel erfahren Sie, wie Sie einen Workflow an den Microsoft Genomics-Dienst übermitteln, wenn Ihre Eingabedatei mehrere FASTQ- oder BAM-Dateien aus dem gleichen Beispiel umfasst. Sie haben den msgen-Client bereits installiert und erfolgreich die Beispieldaten über den Dienst ausgeführt.
services: genomics
ms.service: genomics
author: grhuynh
manager: cgronlund
ms.author: grhuynh
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 399b1ed735ce1b7a3fca1d27155863f6bfa18776
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60780877"
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Übermitteln eines Workflows mit mehreren Eingaben aus dem gleichen Beispiel

In diesem Artikel erfahren Sie, wie Sie einen Workflow an den Microsoft Genomics-Dienst übermitteln, wenn Ihre Eingabedatei mehrere FASTQ- oder BAM-Dateien **aus dem gleichen Beispiel** umfasst. Wenn Sie das **gleiche Beispiel** auf dem Sequencer beispielsweise in mehreren Bereichen ausführen, gibt der Sequencer ggf. für jeden Bereich ein FASTQ-Dateipaar aus. Anstatt diese FASTQ-Dateien vor dem Alignment und Variant Calling zu verketten, können Sie alle Eingaben direkt an den `msgen`-Client übermitteln. Die Ausgabe des `msgen`-Clients ist dann ein **einzelner Satz** mit Dateien, der eine BAM-, BAI- und VCF-Datei umfasst. 

Bedenken Sie allerdings, dass eine Übermittlung **keine Mischung aus FASTQ- und BAM-Dateien** enthalten darf. Außerdem ist es **nicht** möglich, mehrere FASTQ- oder BAM-Dateien von mehreren Einzelpersonen zu übermitteln. 

In diesem Artikel wird vorausgesetzt, dass Sie den `msgen`-Client bereits installiert und ausgeführt haben und mit der Verwendung von Azure Storage vertraut sind. Wenn Sie erfolgreich einen Workflow mit den bereitgestellten Beispieldaten übermittelt haben, können Sie mit diesem Artikel fortfahren. 


## <a name="multiple-bam-files"></a>Mehrere BAM-Dateien

### <a name="upload-your-input-files-to-azure-storage"></a>Hochladen Ihrer Eingabedateien in Azure Storage
Angenommen, Sie haben mehrere BAM-Eingabedateien (*reads.bam*, *additional_reads.bam* und *yet_more_reads.bam*) in Ihr Azure-Speicherkonto *myaccount* hochgeladen. Sie verfügen über die API-URL und den Zugriffsschlüssel. Als Ausgabeziel möchten Sie **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** verwenden.


### <a name="submit-your-job-to-the-msgen-client"></a>Übermitteln Ihres Auftrags an den `msgen`-Client 

Zur Übermittlung mehrerer BAM-Dateien können Sie deren Namen an das Argument „--input-blob-name-1“ übergeben. Beachten Sie, dass alle Dateien aus dem gleichen Beispiel stammen müssen. Ihre Reihenfolge ist jedoch unerheblich. Der folgende Abschnitt enthält ausführliche Informationen zu Beispielübermittlungen über eine Windows-Befehlszeile, über eine Unix-Befehlszeile und unter Verwendung einer Konfigurationsdatei. Zur besseren Übersichtlichkeit wurden Zeilenumbrüche eingefügt:


Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
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
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Falls Sie lieber eine Konfigurationsdatei verwenden möchten, muss diese Folgendes enthalten:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Übermitteln Sie die Datei `config.txt` mithilfe des folgenden Aufrufs: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Mehrere FASTQ-Dateipaare

### <a name="upload-your-input-files-to-azure-storage"></a>Hochladen Ihrer Eingabedateien in Azure Storage
Angenommen, Sie verfügen über mehrere FASTQ-Dateipaare als Eingabe: *reads_1.fq.gz* und *reads_2.fq.gz*, *additional_reads_1.fq.gz* und *additional_reads_2.fq.gz* sowie *yet_more_reads_1.fq.gz* und *yet_more_reads_2.fq.gz*. Sie haben sie in Ihr Azure-Speicherkonto *myaccount* hochgeladen und verfügen über die API-URL und über Ihren Zugriffsschlüssel. Als Ausgabeziel möchten Sie **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** verwenden.


### <a name="submit-your-job-to-the-msgen-client"></a>Übermitteln Ihres Auftrags an den `msgen`-Client 

FASTQ-Dateipaare müssen nicht nur aus dem gleichen Beispiel stammen, sondern auch gemeinsam verarbeitet werden.  Wenn sie als Argumente an „--input-blob-name-1“ und „--input-blob-name-2“ übergeben werden, muss auf die Reihenfolge der Dateinamen geachtet werden. 

Der folgende Abschnitt enthält ausführliche Informationen zu Beispielübermittlungen über eine Windows-Befehlszeile, über eine Unix-Befehlszeile und unter Verwendung einer Konfigurationsdatei. Zur besseren Übersichtlichkeit wurden Zeilenumbrüche eingefügt:


Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
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
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
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
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Übermitteln Sie die Datei `config.txt` mithilfe des folgenden Aufrufs: `msgen submit -f config.txt`

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie mehrere BAM-Dateien oder FASTQ-Dateipaare in Azure Storage hochgeladen und einen Workflow über den `msgen`-Python-Client an den Microsoft Genomics-Dienst übermittelt. Weitere Informationen zur Workflowübermittlung sowie zu anderen Befehlen für den Microsoft Genomics-Dienst finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-genomics.md). 