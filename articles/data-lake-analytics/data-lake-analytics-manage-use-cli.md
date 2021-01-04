---
title: Verwalten von Azure Data Lake Analytics mithilfe der Azure CLI
description: In diesem Artikel erfahren Sie, wie Sie die CLI zum Verwalten von Data Lake Analytics-Aufträgen, -Datenquellen und -Benutzern verwenden.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 01/29/2018
ms.openlocfilehash: 19b471d85a52fe38b72ad55847d022fb56b3c4f0
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220923"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Verwalten von Azure Data Lake Analytics unter Verwendung der Azure-Befehlszeilenschnittstelle (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Hier erfahren Sie, wie Sie Azure Data Lake Analytics-Konten, -Datenquellen, -Benutzer und -Aufträge mithilfe der Azure CLI verwalten. Klicken Sie oben auf die Auswahlregisterkarte, um Themen anzuzeigen, in denen die Verwaltung mit anderen Tools stattfindet.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie folgende Ressourcen:

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Azure-Befehlszeilenschnittstelle. Weitere Informationen finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

  - Laden Sie für diese Demo die **Vorabversion** der [Azure CLI-Tools](https://github.com/MicrosoftBigData/AzureDataLake/releases) herunter, und installieren Sie sie.

- Authentifizieren Sie sich mithilfe des Befehls `az login`, und wählen Sie das Abonnement aus, das Sie verwenden möchten. Weitere Informationen zur Authentifizierung mit einem Geschäfts- oder Schulkonto finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Jetzt können Sie auf die Befehle für Data Lake Analytics und Data Lake Store zugreifen. Führen Sie den folgenden Befehl aus, um die Data Lake Store- und Data Lake Analytics-Befehle aufzulisten:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Konten verwalten

Zur Ausführung von Data Lake Analytics-Aufträgen ist ein Data Lake Analytics-Konto erforderlich. Im Gegensatz zu Azure HDInsight entstehen durch ein Analytics-Konto keine Kosten, solange darin kein Auftrag ausgeführt wird. Sie bezahlen nur für die Zeit, während der in dem Konto ein Auftrag ausgeführt wird.  Weitere Informationen finden Sie unter [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md).

### <a name="create-accounts"></a>Erstellen von Konten

Führen Sie den folgenden Befehl aus, um ein Data Lake-Konto zu erstellen:

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Aktualisieren von Konten

Der folgende Befehl aktualisiert die Eigenschaften eines vorhandenen Data Lake Analytics-Kontos:

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Auflisten von Konten

Auflisten von Data Lake Analysekonten in einer bestimmten Ressourcengruppe

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Abrufen der Details zu einem Konto

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Löschen eines Kontos

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Datenquellen verwalten

Data Lake Analytics unterstützt derzeit die folgenden beiden Datenquellen:

- [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
- [Azure Storage (in englischer Sprache)](../storage/common/storage-introduction.md)

Beim Erstellen eines Analytics-Kontos müssen Sie ein Azure Data Lake-Speicherkonto als Standardspeicherkonto festlegen. Das Data Lake-Standardspeicherkonto dient zum Speichern von Auftragsmetadaten und -überwachungsprotokollen. Nachdem Sie ein Analytics-Konto erstellt haben, können Sie zusätzliche Data Lake-Speicherkonten und/oder Azure-Speicherkonten hinzufügen.

### <a name="find-the-default-data-lake-store-account"></a>Ermitteln des Data Lake-Standardspeicherkontos

Sie können das Data Lake Store-Standardkonto anzeigen, indem Sie den Befehl `az dla account show` ausführen. Der Name des Standardkontos wird in der defaultDataLakeStoreAccount-Eigenschaft aufgeführt.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Hinzufügen zusätzlicher Blobspeicherkonten

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Nur Blob-Speicherkurznamen werden unterstützt. Verwenden Sie keine vollqualifizierten Domänennamen wie etwa „myblob.blob.core.windows.net“.
>

### <a name="add-additional-data-lake-store-accounts"></a>Hinzufügen zusätzlicher Data Lake-Speicherkonten

Der folgende Befehl aktualisiert das angegebene Data Lake Analytics-Konto mit einem zusätzlichen Data Lake Store-Konto:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Aktualisieren einer vorhandenen Datenquelle

So aktualisieren Sie den Schlüssel eines Blob-Speicherkontos:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Auflisten von Datenquellen

So listen Sie die Data Lake Store-Konten auf:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

So listen Sie das Blobspeicherkonto auf:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Screenshot der Azure-Befehlszeilenschnittstelle mit hervorgehobener Information „dataLakeStoreAccounts:“](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Löschen von Datenquellen

So löschen Sie ein Data Lake-Speicherkonto:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

So löschen Sie ein Blob-Speicherkonto:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Aufträge verwalten

Für das Erstellen eines Auftrags ist ein Data Lake Analytics-Konto erforderlich.  Weitere Informationen finden Sie unter [Verwalten von Data Lake Analytics-Konten](#manage-accounts).

### <a name="list-jobs"></a>Auflisten von Aufträgen

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Data Lake Analytics-Datenquellenliste](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Abrufen von Auftragsdetails

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Übermitteln von Aufträgen

> [!NOTE]
> Die Standardpriorität eines Auftrags ist 1000, und der Standardparallelitätsgrad eines Auftrag ist 1.
>
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Abbrechen von Aufträgen

Suchen Sie mithilfe des Befehls „list“ nach der Auftrags-ID, und brechen Sie den Auftrag anschließend mithilfe des Befehls „cancel“ ab.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Pipelines und Wiederholungen

### <a name="get-information-about-pipelines-and-recurrences"></a>Abrufen von Informationen zu Pipelines und Wiederholungen

Verwenden Sie die Befehle `az dla job pipeline`, um die Pipelineinformationen für zuvor übermittelte Aufträge anzuzeigen.

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Verwenden Sie die Befehle `az dla job recurrence`, um die Wiederholungsinformationen für zuvor übermittelte Aufträge anzuzeigen.

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)
- [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-manage-use-portal.md)
- [Überwachung und Problembehandlung von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Portals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
