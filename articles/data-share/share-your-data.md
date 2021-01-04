---
title: 'Tutorial: Freigeben außerhalb Ihrer Organisation: Azure Data Share'
description: 'Tutorial: Freigeben von Daten für Kunden und Partner mit Azure Data Share'
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: 89c2a725b853b5a2a7578dccc1fd503917e12962
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659623"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Tutorial: Freigeben von Daten mithilfe von Azure Data Share  

In diesem Tutorial wird beschrieben, wie Sie eine neue Azure Data Share-Instanz einrichten und mit dem Freigeben Ihrer Daten für Kunden und Partner außerhalb Ihrer Azure-Organisation beginnen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Datenfreigabe
> * Hinzufügen von Datasets zu Ihrer Datenfreigabe
> * Aktivieren eines Momentaufnahmezeitplans für Ihre Datenfreigabe 
> * Hinzufügen von Empfängern zu Ihrer Datenfreigabe 

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* E-Mail-Adresse für die Azure-Anmeldung Ihrer Empfänger (Ein E-Mail-Alias funktioniert nicht.)
* Befindet sich der Azure-Quelldatenspeicher nicht in dem Azure-Abonnement, in dem Sie die Data Share-Ressource erstellen, registrieren Sie [Microsoft.DataShare resource provider](concepts-roles-permissions.md#resource-provider-registration) in dem Abonnement, in dem sich der Azure-Datenspeicher befindet. 

### <a name="share-from-a-storage-account"></a>Freigeben über ein Speicherkonto

* Ein Azure Storage-Konto: Falls Sie noch nicht über ein Konto verfügen, können Sie [hier ein Azure Storage-Konto erstellen](../storage/common/storage-account-create.md).
* Berechtigung zum Schreiben in das Speicherkonto (unter *Microsoft.Storage/storageAccounts/write*). Diese Berechtigung ist in der Rolle **Mitwirkender** vorhanden.
* Berechtigung zum Hinzufügen einer Rollenzuweisung zum Speicherkonto (unter *Microsoft.Authorization/role assignments/write*). Diese Berechtigung ist in der Rolle **Besitzer** vorhanden. 


### <a name="share-from-a-sql-based-source"></a>Freigeben über eine SQL-basierte Quelle
Im Folgenden finden Sie eine Liste der Voraussetzungen für die Freigabe von Daten aus der SQL-Quelle. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Voraussetzungen für die Freigabe von Daten aus Azure SQL-Datenbank oder Azure Synapse Analytics (ehemals Azure SQL DW)
Beim Konfigurieren der Voraussetzungen können Sie sich an der [Schritt-für-Schritt-Demo](https://youtu.be/hIE-TjJD8Dc) orientieren.

* Eine Instanz von Azure SQL-Datenbank oder Azure Synapse Analytics (ehemals Azure SQL DW) mit Tabellen und Ansichten, die Sie freigeben möchten.
* Berechtigung zum Schreiben in die Datenbanken in SQL Server (unter *Microsoft.Sql/servers/databases/write*). Diese Berechtigung ist in der Rolle **Mitwirkender** vorhanden.
* Berechtigung zum Zugreifen auf die Datenbank für die verwaltete Identität der Data Share-Ressource. Die Berechtigung kann mit folgenden Schritten gewährt werden: 
    1. Navigieren Sie im Azure-Portal zur SQL Server-Instanz, und legen Sie sich selbst als **Azure Active Directory-Administrator** fest.
    1. Stellen Sie mit dem [Abfrage-Editor](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) oder SQL Server Management Studio mit Azure Active Directory-Authentifizierung eine Verbindung mit Azure SQL-Datenbank/Data Warehouse her. 
    1. Führen Sie das folgende Skript aus, um die verwaltete Identität der Data Share-Ressource als „db_datareader“ hinzuzufügen. Sie müssen mithilfe von Active Directory und nicht über die SQL Server-Authentifizierung eine Verbindung herstellen. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Beachten Sie, dass *<share_acc_name>* der Name Ihrer Data Share-Ressource ist. Falls Sie noch keine Data Share-Ressource erstellt haben, können Sie später zu dieser Voraussetzung zurückkehren.  

* Ein Azure SQL-Datenbank-Benutzer mit Zugriff vom Typ **db_datareader** zum Navigieren durch Tabellen und/oder Ansichten sowie zum Auswählen der Tabellen und/oder Ansichten, die Sie freigeben möchten. 

* SQL Server-Firewallzugriff. Die Berechtigung kann mit folgenden Schritten gewährt werden: 
    1. Navigieren Sie im Azure-Portal zu „SQL Server“. Wählen Sie im linken Navigationsbereich die Option *Firewalls und virtuelle Netzwerke* aus.
    1. Klicken Sie auf **Ja** für *Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten*.
    1. Klicken Sie auf **+Client-IP hinzufügen**. Die IP-Adresse kann sich ggf. ändern. Dieser Prozess muss unter Umständen bei der nächsten Freigabe von SQL-Daten über das Azure-Portal wiederholt werden. Sie können auch einen IP-Adressbereich hinzufügen.
    1. Klicken Sie auf **Speichern**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Voraussetzungen für die Freigabe von Daten aus einem SQL-Pool von Azure Synapse Analytics (Arbeitsbereich)

* * Ein dedizierter SQL-Pool von Azure Synapse Analytics (Arbeitsbereich) mit Tabellen, die Sie freigeben möchten. Das Freigeben der Ansicht wird derzeit nicht unterstützt. Das Freigeben von Daten aus einem serverlosen SQL-Pool wird derzeit nicht unterstützt.
* Berechtigung zum Schreiben in den SQL-Pool im Synapse-Arbeitsbereich (unter *Microsoft.Synapse/workspaces/sqlPools/write*). Diese Berechtigung ist in der Rolle **Mitwirkender** vorhanden.
* Berechtigung zum Zugreifen auf den SQL-Pool des Synapse-Arbeitsbereichs für die verwaltete Identität der Data Share-Ressource. Die Berechtigung kann mit folgenden Schritten gewährt werden: 
    1. Navigieren Sie im Azure-Portal zum Synapse-Arbeitsbereich. Wählen Sie im linken Navigationsbereich die Option „Administrator für Active Directory in SQL“ aus, und legen Sie sich selbst als **Azure Active Directory-Administrator** fest.
    1. Öffnen Sie Synapse Studio, und wählen Sie im linken Navigationsbereich die Option *Verwalten* aus. Wählen Sie unter „Sicherheit“ die Option *Zugriffssteuerung* aus. Weisen Sie sich selbst die Rolle **SQL-Administrator** oder **Arbeitsbereichsadministrator** zu.
    1. Wählen Sie in Synapse Studio im linken Navigationsbereich die Option *Entwickeln* aus. Führen Sie im SQL-Pool das folgende Skript aus, um die verwaltete Identität der Data Share-Ressource als „db_datareader“ hinzuzufügen. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Beachten Sie, dass *<share_acc_name>* der Name Ihrer Data Share-Ressource ist. Falls Sie noch keine Data Share-Ressource erstellt haben, können Sie später zu dieser Voraussetzung zurückkehren.  

* Firewallzugriff für den Synapse-Arbeitsbereich. Die Berechtigung kann mit folgenden Schritten gewährt werden: 
    1. Navigieren Sie im Azure-Portal zum Synapse-Arbeitsbereich. Wählen Sie im linken Navigationsbereich die Option *Firewalls* aus.
    1. Klicken Sie unter *Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Arbeitsbereich gestatten* auf **EIN**.
    1. Klicken Sie auf **+Client-IP hinzufügen**. Die IP-Adresse kann sich ggf. ändern. Dieser Prozess muss unter Umständen bei der nächsten Freigabe von SQL-Daten über das Azure-Portal wiederholt werden. Sie können auch einen IP-Adressbereich hinzufügen.
    1. Klicken Sie auf **Speichern**. 


### <a name="share-from-azure-data-explorer"></a>Freigeben über Azure Data Explorer
* Ein Azure Data Explorer-Cluster mit Datenbanken, die Sie freigeben möchten.
* Berechtigung zum Schreiben in den Azure Data Explorer-Cluster (unter *Microsoft.Kusto/clusters/write*). Diese Berechtigung ist in der Rolle **Mitwirkender** vorhanden.
* Berechtigung zum Hinzufügen einer Rollenzuweisung zum Azure Data Explorer-Cluster (unter *Microsoft.Authorization/role assignments/write*). Diese Berechtigung ist in der Rolle **Besitzer** vorhanden.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-data-share-account"></a>Erstellen eines Data Share-Kontos

### <a name="portal"></a>[Portal](#tab/azure-portal)

Erstellen Sie eine Azure Data Share-Ressource in einer Azure-Ressourcengruppe.

1. Wählen Sie im Portal links oben die Menüschaltfläche und dann **Ressource erstellen** (+) aus.

1. Suchen Sie nach *Data Share*.

1. Wählen Sie „Data Share“ und dann die Option **Erstellen** aus.

1. Geben Sie die folgenden Informationen als grundlegende Details Ihrer Azure Data Share-Ressource ein. 

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Subscription | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie für Ihr Data Share-Konto verwenden möchten.|
    | Ressourcengruppe | *testresourcegroup* | Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. |
    | Standort | *USA (Ost) 2* | Wählen Sie eine Region für Ihr Data Share-Konto aus.
    | Name | *datashareaccount* | Geben Sie einen Namen für Ihr Data Share-Konto an. |
    | | |

1. Wählen Sie **Überprüfen + erstellen** und dann **Erstellen** aus, um Ihr Data Share-Konto bereitzustellen. Die Bereitstellung eines neuen Data Share-Kontos dauert normalerweise maximal ca. 2 Minuten. 

1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Erstellen Sie eine Azure Data Share-Ressource in einer Azure-Ressourcengruppe.

Bereiten Sie zunächst Ihre Umgebung für die Azure-Befehlszeilenschnittstelle vor:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Verwenden Sie die folgenden Befehle, um die Ressource zu erstellen:

1. Verwenden Sie den Befehl [az account set](/cli/azure/account#az_account_set), um Ihr Abonnement als aktuelles Standardabonnement festzulegen:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Führen Sie den Befehl [az provider register](/cli/azure/provider#az_provider_register) aus, um den Ressourcenanbieter zu registrieren:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen, oder verwenden Sie eine vorhandene Ressourcengruppe:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Führen Sie den Befehl [az datashare account create](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_create) aus, um ein Data Share-Konto zu erstellen:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Führen Sie den Befehl [az datashare account list](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_list) aus, um Ihre Data Share-Konten anzuzeigen:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Erstellen einer Freigabe

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie zu Ihrer Seite mit der Data Share-Übersicht.

    ![Freigeben von Daten](./media/share-receive-data.png "Freigeben von Daten") 

1. Wählen Sie **Start sharing your data** (Mit Freigabe der Daten beginnen).

1. Klicken Sie auf **Erstellen**.   

1. Geben Sie die Details für Ihre Freigabe ein. Geben Sie einen Namen, die Art der Freigabe, eine Beschreibung der Freigabeinhalte und Nutzungsbedingungen (optional) an. 

    ![EnterShareDetails](./media/enter-share-details.png "Eingeben der Details zur Freigabe") 

1. Wählen Sie **Weiter**.

1. Wählen Sie zum Hinzufügen von Datasets zu Ihrer Freigabe **Datasets hinzufügen** aus. 

    ![Hinzufügen von Datasets zu Ihrer Freigabe](./media/datasets.png "Datasets")

1. Wählen Sie den gewünschten Datasettyp für das Hinzufügen aus. Die angezeigte Liste der Datasettypen ist abhängig von der Art der Freigabe (Momentaufnahme oder direkt), die Sie im vorherigen Schritt ausgewählt haben. Wenn Sie für die Freigabe eine Instanz von Azure SQL-Datenbank oder Azure Synapse Analytics (ehemals Azure SQL DW) verwenden, werden Sie beim Auflisten von Tabellen zur Eingabe von SQL-Anmeldeinformationen aufgefordert.

    ![AddDatasets](./media/add-datasets.png "Hinzufügen von Datasets")    

1. Navigieren Sie zum Objekt, das Sie freigeben möchten, und wählen Sie „Datasets hinzufügen“. 

    ![SelectDatasets](./media/select-datasets.png "Auswählen der Datasets")    

1. Geben Sie auf der Registerkarte „Empfänger“ die E-Mail-Adressen Ihrer Datenconsumer ein, indem Sie die Option „+ Empfänger hinzufügen“ wählen. 

    ![AddRecipients](./media/add-recipient.png "Hinzufügen von Empfängern") 

1. Wählen Sie **Weiter**.

1. Wenn Sie eine Momentaufnahmefreigabe ausgewählt haben, können Sie einen Momentaufnahmezeitplan konfigurieren, um Aktualisierungen Ihrer Daten für Ihren Datenconsumer bereitzustellen. 

    ![EnableSnapshots](./media/enable-snapshots.png "Aktivieren von Momentaufnahmen") 

1. Wählen Sie eine Startzeit und ein Wiederholungsintervall aus. 

1. Wählen Sie **Weiter**.

1. Überprüfen Sie auf der Registerkarte „Bewerten + erstellen“ die Angaben für Paketinhalt, Einstellungen, Empfänger und Synchronisierungseinstellungen. Klicken Sie auf **Erstellen**.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Führen Sie den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create) aus, um eine Data Share-Ressource zu erstellen:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Verwenden Sie den Befehl [az storage container create](/cli/azure/storage/container#az_storage_container_create), um einen Container für die Freigabe aus dem vorherigen Befehl zu erstellen:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Führen Sie den Befehl [az datashare create](/cli/azure/ext/datashare/datashare#ext_datashare_az_datashare_create) aus, um Ihre Data Share-Ressource zu erstellen:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Verwenden Sie den Befehl [az datashare invitation create](/cli/azure/ext/datashare/datashare/invitation#ext_datashare_az_datashare_invitation_create), um die Einladung für die angegebene Adresse zu erstellen:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

Ihre Azure Data Share-Instanz wurde jetzt erstellt, und der Empfänger Ihrer Datenfreigabe kann Ihre Einladung nun akzeptieren.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressource nicht mehr benötigt wird, navigieren Sie zur Seite mit der **Data Share-Übersicht**, und wählen Sie **Löschen** aus, um die Ressource zu entfernen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie eine Azure Data Share-Instanz erstellen und Empfänger einladen. Fahren Sie mit dem Tutorial Akzeptieren und Empfangen von Daten fort, um sich darüber zu informieren, wie ein Datenconsumer eine Datenfreigabe akzeptieren und empfangen kann.

> [!div class="nextstepaction"]
> [Tutorial: Akzeptieren und Empfangen von Daten mithilfe von Azure Data Share](subscribe-to-data-share.md)
