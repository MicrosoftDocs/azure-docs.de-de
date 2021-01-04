---
title: Apache Hadoop und Speicher mit sicherer Übertragung – Azure HDInsight
description: Hier erfahren Sie, wie Sie HDInsight-Cluster mit Azure-Speicherkonten mit sicherer Übertragung erstellen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: 69844a392aa03dc962f8af456996ab51f1adf561
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547468"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop-Cluster mit Speicherkonten mit sicherer Übertragung in Azure HDInsight

Das Feature [Sichere Übertragung erforderlich](../storage/common/storage-require-secure-transfer.md) erhöht die Sicherheit Ihres Azure Storage-Kontos, indem alle Anforderungen für Ihr Konto über eine sichere Verbindung übertragen werden müssen. Dieses Feature und das WASB-Schema werden erst ab HDInsight-Clusterversion 3.6 unterstützt.

> [!IMPORTANT]
> Das Aktivieren der sicheren Speicherübertragung nach dem Erstellen eines Clusters kann zu Fehlern bei der Verwendung Ihres Speicherkontos führen und wird daher nicht empfohlen. Es ist besser, einen neuen Cluster mit einem Speicherkonto zu erstellen, bei dem die sichere Übertragung bereits aktiviert ist.

## <a name="storage-accounts"></a>Speicherkonten

### <a name="azure-portal"></a>Azure-Portal

Standardmäßig ist die Eigenschaft für erforderliche sichere Übertragung aktiviert, wenn Sie ein Speicherkonto im Azure-Portal erstellen.

Informationen zum Aktualisieren eines vorhandenen Speicherkontos über das Azure-Portal finden Sie unter [Erfordern sicherer Übertragung über das Azure-Portal](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Stellen Sie für das PowerShell-Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) sicher, dass der Parameter `-EnableHttpsTrafficOnly` auf `1` festgelegt ist.

Informationen zum Aktualisieren eines vorhandenen Speicherkontos mit PowerShell finden Sie unter [Erfordern sicherer Übertragung mit PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Stellen Sie für den Azure CLI-Befehl [az storage account create](/cli/azure/storage/account#az-storage-account-create) sicher, dass der Parameter `--https-only` auf `true` festgelegt ist.

Informationen zum Aktualisieren eines vorhandenen Speicherkontos über die Azure-Befehlszeilenschnittstelle finden Sie unter [Erfordern sicherer Übertragung über die Azure-Befehlszeilenschnittstelle](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Hinzufügen weiterer Speicherkonten

Weitere Speicherkonten mit sicherer Übertragung können auf unterschiedliche Weise hinzugefügt werden:

* Ändern Sie die Azure Resource Manager-Vorlage im letzten Abschnitt.
* Erstellen Sie über das [Azure-Portal](https://portal.azure.com) einen Cluster, und geben Sie ein verknüpftes Speicherkonto an.
* Verwenden Sie die Skriptaktion, um einem vorhandenen HDInsight-Cluster weitere Speicherkonten mit sicherer Übertragung hinzuzufügen. Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Speicherkonten zu HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Nächste Schritte

* Verwendung von Azure Storage (WASB) anstelle von [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) als Standarddatenspeicher
* Informationen zur Verwendung von Azure Storage durch HDInsight finden Sie unter [Verwenden von Azure Storage mit HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Informationen zum Hochladen von Daten in HDInsight finden Sie im Artikel zum [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md).