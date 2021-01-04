---
title: 'Tutorial: Akzeptieren und Empfangen von Daten: Azure Data Share'
description: 'Tutorial: Akzeptieren und Empfangen von Daten per Azure Data Share'
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: a225989f0670e9b62b00a35bac719c9357c8a130
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017048"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Tutorial: Akzeptieren und Empfangen von Daten mithilfe von Azure Data Share  

In diesem Tutorial wird beschrieben, wie Sie mit Azure Data Share eine Einladung für eine Datenfreigabe annehmen. Sie erfahren, wie Sie die für Sie freigegebenen Daten empfangen und ein Intervall für die regelmäßige Aktualisierung einrichten. So können Sie sicherstellen, dass Sie immer über die aktuellste Momentaufnahme der Daten verfügen, die für Sie freigegeben werden. 

> [!div class="checklist"]
> * Annehmen einer Azure Data Share-Einladung
> * Erstellen eines Azure Data Share-Kontos
> * Angeben eines Ziels für Ihre Daten
> * Erstellen eines Abonnements Ihrer Datenfreigabe für die geplante Aktualisierung

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie eine Einladung zu einer Datenfreigabe annehmen können, müssen Sie einige Azure-Ressourcen bereitstellen. Diese sind unten aufgeführt. 

Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind, bevor Sie die Einladung zu einer Datenfreigabe annehmen. 

* Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Eine Data Share-Einladung: Eine Einladung von Microsoft Azure mit dem Betreff „Azure Data Share-Einladung von **<yourdataprovider@domain.com>** “.
* Registrieren Sie den [Ressourcenanbieter „Microsoft.DataShare“](concepts-roles-permissions.md#resource-provider-registration) in dem Azure-Abonnement, in dem Sie eine Data Share-Ressource erstellen möchten, und in dem Azure-Abonnement, in dem sich die Azure-Zieldatenspeicher befinden.

### <a name="receive-data-into-a-storage-account"></a>Empfangen von Daten in einem Speicherkonto

* Ein Azure Storage-Konto: Falls Sie noch nicht über ein Konto verfügen, können Sie [hier ein Azure Storage-Konto erstellen](../storage/common/storage-account-create.md). 
* Berechtigung zum Schreiben in das Speicherkonto (unter *Microsoft.Storage/storageAccounts/write*). Diese Berechtigung ist in der Rolle „Mitwirkender“ vorhanden. 
* Berechtigung zum Hinzufügen einer Rollenzuweisung zum Speicherkonto (unter *Microsoft.Authorization/role assignments/write*). Diese Berechtigung ist in der Rolle „Besitzer“ vorhanden.  

### <a name="receive-data-into-a-sql-based-target"></a>Empfangen von Daten an einem SQL-basierten Ziel
Wenn Sie Daten in Azure SQL-Datenbank oder Azure Synapse Analytics empfangen möchten, finden Sie nachfolgend eine Liste mit Voraussetzungen, die erfüllt sein müssen. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Voraussetzungen für den Empfang von Daten in Azure SQL-Datenbank oder Azure Synapse Analytics (ehemals Azure SQL DW)
Beim Konfigurieren der Voraussetzungen können Sie sich an der [Schritt-für-Schritt-Demo](https://youtu.be/aeGISgK1xro) orientieren.

* Eine Instanz von Azure SQL-Datenbank oder Azure Synapse Analytics (ehemals Azure SQL DW).
* Berechtigung zum Schreiben in Datenbanken auf dem SQL-Server (unter *Microsoft.Sql/servers/databases/write*). Diese Berechtigung ist in der Rolle **Mitwirkender** vorhanden. 
* Berechtigung zum Zugreifen auf die Azure SQL-Datenbank- oder Azure Synapse Analytics-Instanz für die verwaltete Identität der Data Share-Ressource. Die Berechtigung kann mit folgenden Schritten gewährt werden: 
    1. Navigieren Sie im Azure-Portal zur SQL Server-Instanz, und legen Sie sich selbst als **Azure Active Directory-Administrator** fest.
    1. Stellen Sie mit dem [Abfrage-Editor](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) oder SQL Server Management Studio mit Azure Active Directory-Authentifizierung eine Verbindung mit Azure SQL-Datenbank/Data Warehouse her. 
    1. Führen Sie das folgende Skript aus, um die verwaltete Data Share-Identität als „db_datareader, db_datawriter, db_ddladmin“ hinzuzufügen. Sie müssen mithilfe von Active Directory und nicht über die SQL Server-Authentifizierung eine Verbindung herstellen. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Beachten Sie, dass *<share_acc_name>* der Name Ihrer Data Share-Ressource ist. Falls Sie noch keine Data Share-Ressource erstellt haben, können Sie später zu dieser Voraussetzung zurückkehren.         

* SQL Server-Firewallzugriff. Die Berechtigung kann mit folgenden Schritten gewährt werden: 
    1. Navigieren Sie in SQL Server im Azure-Portal zu *Firewalls und virtuelle Netzwerke*.
    1. Klicken Sie auf **Ja** für *Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten*.
    1. Klicken Sie auf **+Client-IP hinzufügen**. Die IP-Adresse kann sich ggf. ändern. Dieser Prozess muss unter Umständen bei der nächsten Freigabe von SQL-Daten über das Azure-Portal wiederholt werden. Sie können auch einen IP-Adressbereich hinzufügen.
    1. Klicken Sie auf **Speichern**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Voraussetzungen für den Empfang von Daten in einem SQL-Pool von Azure Synapse Analytics (Arbeitsbereich)

* Ein dedizierter SQL-Pool von Azure Synapse Analytics (Arbeitsbereich). Das Empfangen von Daten in einem serverlosen SQL-Pool wird derzeit nicht unterstützt.
* Berechtigung zum Schreiben in den SQL-Pool im Synapse-Arbeitsbereich (unter *Microsoft.Synapse/workspaces/sqlPools/write*). Diese Berechtigung ist in der Rolle **Mitwirkender** vorhanden.
* Berechtigung zum Zugreifen auf den SQL-Pool des Synapse-Arbeitsbereichs für die verwaltete Identität der Data Share-Ressource. Die Berechtigung kann mit folgenden Schritten gewährt werden: 
    1. Navigieren Sie im Azure-Portal zum Synapse-Arbeitsbereich. Wählen Sie im linken Navigationsbereich die Option „Administrator für Active Directory in SQL“ aus, und legen Sie sich selbst als **Azure Active Directory-Administrator** fest.
    1. Öffnen Sie Synapse Studio, und wählen Sie im linken Navigationsbereich die Option *Verwalten* aus. Wählen Sie unter „Sicherheit“ die Option *Zugriffssteuerung* aus. Weisen Sie sich selbst die Rolle **SQL-Administrator** oder **Arbeitsbereichsadministrator** zu.
    1. Wählen Sie in Synapse Studio im linken Navigationsbereich die Option *Entwickeln* aus. Führen Sie im SQL-Pool das folgende Skript aus, um die verwaltete Identität der Data Share-Ressource als „db_datareader, db_datawriter, db_ddladmin“ hinzuzufügen. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Beachten Sie, dass *<share_acc_name>* der Name Ihrer Data Share-Ressource ist. Falls Sie noch keine Data Share-Ressource erstellt haben, können Sie später zu dieser Voraussetzung zurückkehren.  

* Firewallzugriff für den Synapse-Arbeitsbereich. Die Berechtigung kann mit folgenden Schritten gewährt werden: 
    1. Navigieren Sie im Azure-Portal zum Synapse-Arbeitsbereich. Wählen Sie im linken Navigationsbereich die Option *Firewalls* aus.
    1. Klicken Sie unter *Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Arbeitsbereich gestatten* auf **EIN**.
    1. Klicken Sie auf **+Client-IP hinzufügen**. Die IP-Adresse kann sich ggf. ändern. Dieser Prozess muss unter Umständen bei der nächsten Freigabe von SQL-Daten über das Azure-Portal wiederholt werden. Sie können auch einen IP-Adressbereich hinzufügen.
    1. Klicken Sie auf **Speichern**. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Empfangen von Daten in einem Azure Data Explorer-Cluster: 

* Ein Azure Data Explorer-Cluster im gleichen Azure-Rechenzentrum wie der Data Explorer-Cluster des Datenanbieters: Sollten Sie über keinen solchen Cluster verfügen, können Sie einen [Azure Data Explorer-Cluster erstellen](/azure/data-explorer/create-cluster-database-portal). Falls Ihnen das Azure-Rechenzentrum des Datenanbieterclusters nicht bekannt ist, können Sie den Cluster später in diesem Prozess erstellen.
* Berechtigung zum Schreiben in den Azure Data Explorer-Cluster (unter *Microsoft.Kusto/clusters/write*). Diese Berechtigung ist in der Rolle „Mitwirkender“ vorhanden. 
* Berechtigung zum Hinzufügen einer Rollenzuweisung zum Azure Data Explorer-Cluster (unter *Microsoft.Authorization/role assignments/write*). Diese Berechtigung ist in der Rolle „Besitzer“ vorhanden. 

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="open-invitation"></a>Öffnen der Einladung

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Sie können die Einladung über die E-Mail oder direkt im Azure-Portal öffnen. 

   Wenn Sie die Einladung über die E-Mail öffnen möchten, überprüfen Sie Ihren Posteingang auf eine Einladung von Ihrem Datenanbieter. Die Einladung stammt von Microsoft Azure und hat die Bezeichnung **Azure Data Share-Einladung von <yourdataprovider@domain.com>** . Klicken Sie auf **Einladung anzeigen**, um Ihre Einladung in Azure anzuzeigen. 

   Wenn Sie eine Einladung direkt im Azure-Portal öffnen möchten, suchen Sie im Azure-Portal nach **Einladungen zu Data Share**. Dadurch gelangen Sie zur Liste mit den Data Share-Einladungen.

   ![Liste mit Einladungen](./media/invitations.png "Liste mit Einladungen") 

1. Wählen Sie die Freigabe aus, die Sie anzeigen möchten. 

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Bereiten Sie die Umgebung für Ihre Azure-Befehlszeilenschnittstelle vor, und zeigen Sie anschließend Ihre Einladungen an.

Bereiten Sie zunächst Ihre Umgebung für die Azure-Befehlszeilenschnittstelle vor:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Führen Sie den Befehl [az datashare consumer invitation list](/cli/azure/ext/datashare/datashare/consumer/invitation#ext_datashare_az_datashare_consumer_invitation_list) aus, um Ihre aktuellen Einladungen anzuzeigen:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Kopieren Sie Ihre Einladungs-ID. Sie wird im nächsten Abschnitt benötigt.

---

## <a name="accept-invitation"></a>Annehmen der Einladung

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Stellen Sie sicher, dass Sie alle Felder überprüfen – auch die **Nutzungsbedingungen**. Wenn Sie den Nutzungsbedingungen zustimmen, müssen Sie das entsprechende Kontrollkästchen aktivieren, um dies anzugeben. 

   ![Nutzungsbedingungen](./media/terms-of-use.png "Nutzungsbedingungen") 

1. Wählen Sie unter *Target Data Share Account* (Data Share-Zielkonto) das Abonnement und die Ressourcengruppe für die Bereitstellung Ihrer Data Share-Instanz aus. 

   Wählen Sie für das Feld **Data Share Account** (Data Share-Konto) die Option **Neue erstellen**, falls Sie nicht über ein vorhandenes Data Share-Konto verfügen. Wählen Sie andernfalls ein vorhandenes Data Share-Konto für Ihre Datenfreigabe aus. 

   Im Feld **Received Share Name** (Name der empfangenen Freigabe) können Sie den vom Datenanbieter angegebenen Standardnamen übernehmen oder einen neuen Namen für die empfangene Freigabe angeben. 

   Nachdem Sie den Nutzungsbedingungen zugestimmt und ein Data Share-Konto für die Verwaltung der empfangenen Freigabe angegeben haben, wählen Sie **Accept and configure** (Akzeptieren und konfigurieren) aus. Ein Freigabeabonnement wird erstellt. 

   ![Optionen zum Akzeptieren](./media/accept-options.png "Optionen zum Akzeptieren") 

   Dadurch gelangen Sie zur empfangenen Freigabe in Ihrem Data Share-Konto. 

   Wählen Sie *Ablehnen*, falls Sie die Einladung nicht annehmen möchten. 

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az datashare consumer share-subscription create](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_create), um die Data Share-Ressource zu erstellen.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Konfigurieren der empfangenen Freigabe

### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um zu konfigurieren, wo Sie Daten empfangen möchten.

1. Wählen Sie die Registerkarte **Datasets** aus. Aktivieren Sie das Kontrollkästchen neben dem Dataset, dem Sie ein Ziel zuweisen möchten. Wählen Sie **+ Dem Ziel zuordnen** aus, um einen Zieldatenspeicher auszuwählen. 

   ![Dem Ziel zuordnen](./media/dataset-map-target.png "Dem Ziel zuordnen") 

1. Wählen Sie einen Zieldatenspeichertyp aus, in dem die Daten gespeichert werden sollen. Alle Datendateien oder -tabellen im Zieldatenspeicher mit demselben Pfad und Namen werden überschrieben. 

   Wählen Sie für die direkte Freigabe einen Datenspeicher am angegebenen Speicherort aus. Der Speicherort ist das Azure-Rechenzentrum, in dem sich der Quelldatenspeicher des Datenanbieters befindet. Nach der Zuordnung des Datasets können Sie über den Link im Zielpfad auf die Daten zugreifen.

   ![Zielspeicherkonto](./media/dataset-map-target-sql.png "Zielspeicher") 

1. Wenn der Datenanbieter für die momentaufnahmebasierte Freigabe einen Momentaufnahmezeitplan erstellt hat, um die Daten regelmäßig zu aktualisieren, können Sie auch den Momentaufnahmezeitplan aktivieren, indem Sie die Registerkarte **Momentaufnahmezeitplan** auswählen. Aktivieren Sie das Kontrollkästchen neben „Momentaufnahmezeitplan“, und wählen Sie **+ Aktivieren** aus.

   ![Aktivieren von „Momentaufnahmezeitplan“](./media/enable-snapshot-schedule.png "Aktivieren des Momentaufnahmezeitplans")

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie die folgenden Befehle, um zu konfigurieren, wo Sie Daten empfangen möchten.

1. Führen Sie den Befehl [az datashare consumer share-subscription list-source-dataset](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_dataset) aus, um die Dataset-ID abzurufen:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Führe Sie den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create) aus, um ein Speicherkonto für diese Data Share-Ressource zu erstellen:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Verwenden Sie den Befehl [az storage account show](/cli/azure/storage/account#az_storage_account_show), um die Speicherkonto-ID abzurufen:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Verwenden Sie den folgenden Befehl, um die Kontoprinzipal-ID abzurufen:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Verwenden Sie den Befehl [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create), um eine Rollenzuweisung für den Kontoprinzipal zu erstellen:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Erstellen Sie auf der Grundlage der Dataset-ID eine Variable für die Zuordnung:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Verwenden Sie den Befehl [az datashare consumer dataset-mapping create](/cli/azure/ext/datashare/datashare/consumer/dataset-mapping#ext_datashare_az_datashare_consumer_dataset_mapping_create), um die Datasetzuordnung zu erstellen:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Führen Sie den Befehl [az datashare consumer share-subscription synchronization start](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_start) aus, um die Datasetsynchronisierung zu starten.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Führen Sie den Befehl [az datashare consumer share-subscription synchronization list](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_list) aus, um eine Liste Ihrer Synchronisierungen anzuzeigen:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Verwenden Sie den Befehl [az datashare consumer share-subscription list-source-share-synchronization-setting](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_share_synchronization_setting), um die für Ihre Freigabe festgelegten Synchronisierungseinstellungen anzuzeigen.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Auslösen einer Momentaufnahme

### <a name="portal"></a>[Portal](#tab/azure-portal)

Diese Schritte sind nur für die momentaufnahmebasierte Freigabe relevant.

1. Sie können eine Momentaufnahme auslösen, indem Sie die Registerkarte **Details** und anschließend **Trigger snapshot** (Momentaufnahme auslösen) auswählen. Hier können Sie eine vollständige oder inkrementelle Momentaufnahme Ihrer Daten auslösen. Wählen Sie die Option für das vollständige Kopieren, falls Sie zum ersten Mal Daten von Ihrem Datenanbieter erhalten. 

   ![Auslösen der Momentaufnahme](./media/trigger-snapshot.png "Auslösen der Momentaufnahme") 

1. Wenn der Status der letzten Ausführung *Erfolgreich* lautet, navigieren Sie zum Zieldatenspeicher, um die empfangenen Daten anzuzeigen. Wählen Sie **Datasets** aus, und klicken Sie auf den Link im Zielpfad. 

   ![Consumerdatasets](./media/consumer-datasets.png "Consumerdatasetzuordnung") 

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie den Befehl [az datashare consumer trigger create](/cli/azure/ext/datashare/datashare/consumer/trigger#ext_datashare_az_datashare_consumer_trigger_create) aus, um eine Momentaufnahme auszulösen:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Verwenden Sie diesen Befehl nur für die momentaufnahmebasierte Freigabe.

---

## <a name="view-history"></a>Anzeigen des Verlaufs
Dieser Schritt ist nur für die momentaufnahmebasierte Freigabe relevant. Wählen Sie zum Anzeigen des Verlaufs der Momentaufnahmen die Registerkarte **Verlauf** aus. Hier ist ein Verlauf aller Momentaufnahmen angegeben, die in den letzten 30 Tagen generiert wurden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressource nicht mehr benötigt wird, navigieren Sie zur Seite mit der **Data Share-Übersicht**, und wählen Sie **Löschen** aus, um die Ressource zu entfernen.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie gelernt, wie Sie eine Azure Data Share-Instanz akzeptieren und empfangen. Weitere Informationen zu den Konzepten von Azure Data Share finden Sie in der Terminologie zu Azure Data Share.

> [!div class="nextstepaction"]
> [Azure Data Share-Konzepte](terminology.md)