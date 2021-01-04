---
title: Freigeben und Empfangen von Daten aus Azure SQL-Datenbank und Azure Synapse Analytics
description: Erfahren Sie, wie Sie Daten aus Azure SQL-Datenbank und Azure Synapse Analytics freigeben und empfangen
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 87d6ca8ee69ca49cf52b61e6beddb56721658afa
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593738"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Freigeben und Empfangen von Daten aus Azure SQL-Datenbank und Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure Data Share unterstützt die momentaufnahmenbasierte Freigabe von Daten aus Azure SQL-Datenbank und Azure Synapse Analytics. In diesem Artikel wird erläutert, wie Sie Daten aus diesen Quellen freigeben und empfangen.

Azure Data Share unterstützt die Freigabe sowohl von Tabellen als auch Sichten aus Azure SQL-Datenbank und Azure Synapse Analytics (ehemals Azure SQL DW) sowie die Freigabe von Tabellen aus einem dedizierten SQL-Pool von Azure Synapse Analytics (Arbeitsbereich). Die Freigabe aus einem serverlosen SQL-Pool von Azure Synapse Analytics (Arbeitsbereich) wird derzeit nicht unterstützt. Datenconsumer können die Daten in Azure Data Lake Storage Gen2 und Azure Blob Storage als CSV- oder Parquet-Datei, in Azure SQL-Datenbank und Azure Synapse Analytics als Tabellen akzeptieren.

Wenn Sie Daten in Azure Data Lake Store Gen2 oder Azure Blob Storage akzeptieren, überschreiben vollständige Momentaufnahmen den Inhalt der Zieldatei (sofern bereits vorhanden).
Wenn Daten in einer SQL-Tabelle empfangen werden und die Zieltabelle noch nicht vorhanden ist, erstellt Azure Data Share die SQL-Tabelle mit dem Quellschema. Wenn bereits eine Zieltabelle mit demselben Namen vorhanden ist, wird sie gelöscht und mit der letzten vollständigen Momentaufnahme überschrieben. Inkrementelle Momentaufnahmen werden derzeit nicht unterstützt.

## <a name="share-data"></a>Freigeben von Daten

### <a name="prerequisites-to-share-data"></a>Voraussetzungen für das Freigeben von Daten

* Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* E-Mail-Adresse für die Azure-Anmeldung Ihrer Empfänger (Ein E-Mail-Alias funktioniert nicht.)
* Befindet sich der Azure-Quelldatenspeicher nicht in dem Azure-Abonnement, in dem Sie die Data Share-Ressource erstellen, registrieren Sie [Microsoft.DataShare resource provider](concepts-roles-permissions.md#resource-provider-registration) in dem Abonnement, in dem sich der Azure-Datenspeicher befindet. 

### <a name="prerequisites-for-sql-source"></a>Voraussetzungen für SQL-Quellen
Im Folgenden finden Sie eine Liste der Voraussetzungen für die Freigabe von Daten aus der SQL-Quelle. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Voraussetzungen für die Freigabe von Daten aus Azure SQL-Datenbank oder Azure Synapse Analytics (ehemals Azure SQL DW)
Beim Konfigurieren der Voraussetzungen können Sie sich an der [Schritt-für-Schritt-Demo](https://youtu.be/hIE-TjJD8Dc) orientieren.

* Eine Instanz von Azure SQL-Datenbank oder Azure Synapse Analytics (ehemals Azure SQL DW) mit Tabellen und Sichten, die Sie freigeben möchten.
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

* Ein Azure SQL-Datenbank-Benutzer mit Zugriff vom Typ **db_datareader** zum Navigieren durch Tabellen und/oder Sichten sowie zum Auswählen der Tabellen und/oder Sichten, die Sie freigeben möchten. 

* SQL Server-Firewallzugriff. Die Berechtigung kann mit folgenden Schritten gewährt werden: 
    1. Navigieren Sie im Azure-Portal zu „SQL Server“. Wählen Sie im linken Navigationsbereich die Option *Firewalls und virtuelle Netzwerke* aus.
    1. Klicken Sie auf **Ja** für *Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten*.
    1. Klicken Sie auf **+Client-IP hinzufügen**. Die IP-Adresse kann sich ggf. ändern. Dieser Prozess muss unter Umständen bei der nächsten Freigabe von SQL-Daten über das Azure-Portal wiederholt werden. Sie können auch einen IP-Adressbereich hinzufügen.
    1. Klicken Sie auf **Speichern**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Voraussetzungen für die Freigabe von Daten aus einem SQL-Pool von Azure Synapse Analytics (Arbeitsbereich)

* Ein dedizierter SQL-Pool von Azure Synapse Analytics (Arbeitsbereich) mit Tabellen, die Sie freigeben möchten. Das Freigeben der Sicht wird derzeit nicht unterstützt. Das Freigeben von Daten aus einem serverlosen SQL-Pool wird derzeit nicht unterstützt.
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

### <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

### <a name="create-a-data-share-account"></a>Erstellen eines Data Share-Kontos

Erstellen Sie eine Azure Data Share-Ressource in einer Azure-Ressourcengruppe.

1. Wählen Sie im Portal links oben die Menüschaltfläche und dann **Ressource erstellen** (+) aus.

1. Suchen Sie nach *Data Share*.

1. Wählen Sie „Data Share“ und dann die Option **Erstellen** aus.

1. Geben Sie die folgenden Informationen als grundlegende Details Ihrer Azure Data Share-Ressource ein. 

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Subscription | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie für Ihr Data Share-Konto verwenden möchten.|
    | Ressourcengruppe | *test-resource-group* | Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. |
    | Standort | *USA (Ost) 2* | Wählen Sie eine Region für Ihr Data Share-Konto aus.
    | Name | *datashareaccount* | Geben Sie einen Namen für Ihr Data Share-Konto an. |
    | | |

1. Wählen Sie **Überprüfen + erstellen** und dann **Erstellen** aus, um Ihr Data Share-Konto bereitzustellen. Die Bereitstellung eines neuen Data Share-Kontos dauert normalerweise maximal ca. 2 Minuten. 

1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.

### <a name="create-a-share"></a>Erstellen einer Freigabe

1. Navigieren Sie zu Ihrer Seite mit der Data Share-Übersicht.

    ![Freigeben von Daten](./media/share-receive-data.png "Freigeben von Daten") 

1. Wählen Sie **Start sharing your data** (Mit Freigabe der Daten beginnen).

1. Klicken Sie auf **Erstellen**.   

1. Geben Sie die Details für Ihre Freigabe ein. Geben Sie einen Namen, die Art der Freigabe, eine Beschreibung der Freigabeinhalte und Nutzungsbedingungen (optional) an. 

    ![EnterShareDetails](./media/enter-share-details.png "Eingeben der Details zur Freigabe") 

1. Wählen Sie **Weiter**.

1. Wählen Sie zum Hinzufügen von Datasets zu Ihrer Freigabe **Datasets hinzufügen** aus. 

    ![Hinzufügen von Datasets zu Ihrer Freigabe](./media/datasets.png "Datasets")

1. Wählen Sie den gewünschten Datasettyp für das Hinzufügen aus. Die angezeigte Liste der Datasettypen ist abhängig von der Art der Freigabe (Momentaufnahme oder direkt), die Sie im vorherigen Schritt ausgewählt haben. 

    ![AddDatasets](./media/add-datasets.png "Hinzufügen von Datasets")    

1. Wählen Sie Ihre SQL Server-Instanz oder Ihren Synapse-Arbeitsbereich aus, geben Sie auf Aufforderung Ihre Anmeldeinformationen ein, und wählen Sie **Weiter** aus, um zu dem Objekt zu navigieren, das Sie freigeben möchten. Wählen Sie dann „Datasets hinzufügen“ aus. Sie können Tabellen und Sichten aus Azure SQL-Datenbank und Azure Synapse Analytics (ehemals Azure SQL DW) oder Tabellen aus dem dedizierten SQL-Pool von Azure Synapse Analytics (Arbeitsbereich) auswählen. 

    ![SelectDatasets](./media/select-datasets-sql.png "Auswählen der Datasets")    

1. Geben Sie auf der Registerkarte „Empfänger“ die E-Mail-Adressen Ihrer Datenconsumer ein, indem Sie die Option „+ Empfänger hinzufügen“ wählen. Die E-Mail-Adresse muss die E-Mail-Adresse des Empfängers für die Anmeldung bei Azure sein.

    ![AddRecipients](./media/add-recipient.png "Hinzufügen von Empfängern") 

1. Wählen Sie **Weiter**.

1. Wenn Sie eine Momentaufnahmefreigabe ausgewählt haben, können Sie einen Momentaufnahmezeitplan konfigurieren, um Aktualisierungen Ihrer Daten für Ihren Datenconsumer bereitzustellen. 

    ![EnableSnapshots](./media/enable-snapshots.png "Aktivieren von Momentaufnahmen") 

1. Wählen Sie eine Startzeit und ein Wiederholungsintervall aus. 

1. Wählen Sie **Weiter**.

1. Überprüfen Sie auf der Registerkarte „Bewerten + erstellen“ die Angaben für Paketinhalt, Einstellungen, Empfänger und Synchronisierungseinstellungen. Klicken Sie auf **Erstellen**.

Ihre Azure Data Share-Instanz wurde jetzt erstellt, und der Empfänger Ihrer Datenfreigabe kann Ihre Einladung nun akzeptieren. 

## <a name="receive-data"></a>Empfangen von Daten

### <a name="prerequisites-to-receive-data"></a>Voraussetzungen für den Empfang von Daten
Bevor Sie eine Einladung zu einer Datenfreigabe annehmen können, müssen Sie einige Azure-Ressourcen bereitstellen. Diese sind unten aufgeführt. 

Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind, bevor Sie die Einladung zu einer Datenfreigabe annehmen. 

* Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Eine Data Share-Einladung: Eine Einladung von Microsoft Azure mit dem Betreff „Azure Data Share-Einladung von **<yourdataprovider@domain.com>** “.
* Registrieren Sie den [Ressourcenanbieter „Microsoft.DataShare“](concepts-roles-permissions.md#resource-provider-registration) in dem Azure-Abonnement, in dem Sie eine Data Share-Ressource erstellen möchten, und in dem Azure-Abonnement, in dem sich die Azure-Zieldatenspeicher befinden.

### <a name="prerequisites-for-target-storage-account"></a>Voraussetzungen für das Zielspeicherkonto
Wenn Sie Daten in Azure Storage empfangen möchten, finden Sie nachfolgend eine Liste mit Voraussetzungen, die erfüllt sein müssen.

* Ein Azure Storage-Konto: Falls Sie noch nicht über ein Konto verfügen, können Sie [hier ein Azure Storage-Konto erstellen](../storage/common/storage-account-create.md). 
* Berechtigung zum Schreiben in das Speicherkonto (unter *Microsoft.Storage/storageAccounts/write*). Diese Berechtigung ist in der Rolle **Mitwirkender** vorhanden. 
* Berechtigung zum Hinzufügen einer Rollenzuweisung der verwalteten Identität für die Data Share-Ressource zum Speicherkonto (unter *Microsoft.Authorization/role assignments/write*). Diese Berechtigung ist in der Rolle **Besitzer** vorhanden.  

### <a name="prerequisites-for-sql-target"></a>Voraussetzungen für SQL-Ziele
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

### <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

### <a name="open-invitation"></a>Öffnen der Einladung

1. Sie können die Einladung über die E-Mail oder direkt im Azure-Portal öffnen. 

   Wenn Sie die Einladung über die E-Mail öffnen möchten, überprüfen Sie Ihren Posteingang auf eine Einladung von Ihrem Datenanbieter. Die Einladung stammt von Microsoft Azure und hat die Bezeichnung **Azure Data Share-Einladung von <yourdataprovider@domain.com>** . Klicken Sie auf **Einladung anzeigen**, um Ihre Einladung in Azure anzuzeigen. 

   Wenn Sie eine Einladung direkt im Azure-Portal öffnen möchten, suchen Sie im Azure-Portal nach **Einladungen zu Data Share**. Dadurch gelangen Sie zur Liste der Data Share-Einladungen.

   ![Liste mit Einladungen](./media/invitations.png "Liste mit Einladungen") 

1. Wählen Sie die Freigabe aus, die Sie anzeigen möchten. 

### <a name="accept-invitation"></a>Annehmen der Einladung
1. Stellen Sie sicher, dass Sie alle Felder überprüfen – auch die **Nutzungsbedingungen**. Wenn Sie den Nutzungsbedingungen zustimmen, müssen Sie das entsprechende Kontrollkästchen aktivieren, um dies anzugeben. 

   ![Nutzungsbedingungen](./media/terms-of-use.png "Nutzungsbedingungen") 

1. Wählen Sie unter *Target Data Share Account* (Data Share-Zielkonto) das Abonnement und die Ressourcengruppe für die Bereitstellung Ihrer Data Share-Instanz aus. 

   Wählen Sie für das Feld **Data Share Account** (Data Share-Konto) die Option **Neue erstellen**, falls Sie nicht über ein vorhandenes Data Share-Konto verfügen. Wählen Sie andernfalls ein vorhandenes Data Share-Konto für Ihre Datenfreigabe aus. 

   Im Feld **Received Share Name** (Name der empfangenen Freigabe) können Sie den vom Datenanbieter angegebenen Standardnamen übernehmen oder einen neuen Namen für die empfangene Freigabe angeben. 

   Nachdem Sie den Nutzungsbedingungen zugestimmt und ein Data Share-Konto für die Verwaltung der empfangenen Freigabe angegeben haben, wählen Sie **Accept and configure** (Akzeptieren und konfigurieren) aus. Ein Freigabeabonnement wird erstellt. 

   ![Optionen zum Akzeptieren](./media/accept-options.png "Optionen zum Akzeptieren") 

   Dadurch gelangen Sie zu der empfangenen Freigabe in Ihrem Data Share-Konto. 

   Wählen Sie *Ablehnen*, falls Sie die Einladung nicht annehmen möchten. 

### <a name="configure-received-share"></a>Konfigurieren der empfangenen Freigabe
Führen Sie die folgenden Schritte aus, um zu konfigurieren, wo Sie Daten empfangen möchten.

1. Wählen Sie die Registerkarte **Datasets** aus. Aktivieren Sie das Kontrollkästchen neben dem Dataset, dem Sie ein Ziel zuweisen möchten. Wählen Sie **+ Dem Ziel zuordnen** aus, um einen Zieldatenspeicher auszuwählen. 

   ![Dem Ziel zuordnen](./media/dataset-map-target.png "Dem Ziel zuordnen") 

1. Wählen Sie einen Zieldatenspeicher aus, in dem die empfangenen Daten gespeichert werden sollen. Alle Datendateien oder -tabellen im Zieldatenspeicher mit demselben Pfad und Namen werden überschrieben. 

   ![Zielspeicherkonto](./media/dataset-map-target-sql.png "Zieldatenspeicher") 

1. Wenn der Datenanbieter für die momentaufnahmebasierte Freigabe einen Momentaufnahmezeitplan erstellt hat, um die Daten regelmäßig zu aktualisieren, können Sie auch den Momentaufnahmezeitplan aktivieren, indem Sie die Registerkarte **Momentaufnahmezeitplan** auswählen. Aktivieren Sie das Kontrollkästchen neben „Momentaufnahmezeitplan“, und wählen Sie **+ Aktivieren** aus.

   ![Aktivieren von „Momentaufnahmezeitplan“](./media/enable-snapshot-schedule.png "Aktivieren des Momentaufnahmezeitplans")

### <a name="trigger-a-snapshot"></a>Auslösen einer Momentaufnahme
Diese Schritte sind nur für die momentaufnahmebasierte Freigabe relevant.

1. Sie können eine Momentaufnahme auslösen, indem Sie die Registerkarte **Details** und anschließend **Trigger snapshot** (Momentaufnahme auslösen) auswählen. Hier können Sie eine vollständige oder inkrementelle Momentaufnahme Ihrer Daten auslösen. Wählen Sie die Option für das vollständige Kopieren, falls Sie zum ersten Mal Daten von Ihrem Datenanbieter erhalten. Bei SQL-Quellen werden nur vollständige Momentaufnahmen unterstützt.

   ![Auslösen der Momentaufnahme](./media/trigger-snapshot.png "Auslösen der Momentaufnahme") 

1. Wenn der Status der letzten Ausführung *Erfolgreich* lautet, navigieren Sie zum Zieldatenspeicher, um die empfangenen Daten anzuzeigen. Wählen Sie **Datasets** aus, und klicken Sie auf den Link im Zielpfad. 

   ![Consumerdatasets](./media/consumer-datasets.png "Consumerdatasetzuordnung") 

### <a name="view-history"></a>Anzeigen des Verlaufs
Dieser Schritt ist nur für die momentaufnahmebasierte Freigabe relevant. Wählen Sie zum Anzeigen des Verlaufs der Momentaufnahmen die Registerkarte **Verlauf** aus. Hier ist ein Verlauf aller Momentaufnahmen angegeben, die in den letzten 30 Tagen generiert wurden. 

## <a name="supported-data-types"></a>Unterstützte Datentypen
Wenn Sie Daten aus einer SQL-Quelle freigeben, wird während des Momentaufnahmevorgangs die folgende Zuordnung von SQL Server-Datentypen zu Azure Data Share-Zwischendatentypen durchgeführt. 

| SQL Server-Datentyp | Azure Data Share-Zwischendatentyp |
|:--- |:--- |
| BIGINT |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |String |

>[!NOTE]
> 1. Für Datentypen, die dem Zwischendatentyp „Decimal“ zugeordnet sind, unterstützt die Momentaufnahme derzeit eine Genauigkeit von bis zu 28. Wenn Ihre Daten eine höhere Genauigkeit als 28 erfordern, erwägen Sie, sie in eine Zeichenfolge zu konvertieren. 
> 1.  Wenn Sie Daten aus einer Azure SQL-Datenbank-Instanz in Azure Synapse Analytics freigeben, werden nicht alle Datentypen unterstützt. Weitere Informationen finden Sie unter [Tabellendatentypen in einem dedizierten SQL-Pool](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md). 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL mit Always Encrypted oder dynamischer Datenmaskierung
Derzeit bietet Azure Data Share keine Unterstützung für Azure SQL-Datenbanken, für die Always Encrypted konfiguriert ist. 

Bei SQL-Quelltabellen mit dynamischer Datenmaskierung werden Daten auf Empfängerseite maskiert angezeigt.

## <a name="sql-snapshot-performance"></a>Leistung von SQL-Momentaufnahmen
Die Leistung von SQL-Momentaufnahmen wird durch eine Reihe von Faktoren beeinflusst. Es wird immer empfohlen, eigene Leistungstests durchzuführen. Nachfolgend sind einige Beispielfaktoren aufgerührt, die sich auf die Leistung auswirken.

* Hardwarekonfiguration (z. B. virtuelle Kerne, Arbeitsspeicher, DWU) des SQL-Quell- und Zieldatenspeichers. 
* Gleichzeitiger Zugriff auf Quell- und Zieldatenspeicher. Wenn Sie mehrere Tabellen und Sichten aus demselben SQL-Datenspeicher gemeinsam nutzen oder mehrere Tabellen und Sichten im gleichen SQL-Datenspeicher empfangen, wirkt sich dies auf die Leistung aus.   
* Standort der Quell- und Zieldatenspeicher. 

## <a name="troubleshoot-sql-snapshot-failure"></a>Problembehandlung für Fehler bei der SQL-Momentaufnahme
Die häufigste Ursache für einen Fehler bei der Momentaufnahme ist, dass Data Share nicht über die Berechtigung für den Quell- oder Zieldatenspeicher verfügt. Um Data Share die Berechtigung für Quell- oder Zielinstanz von Azure SQL-Datenbank oder Azure Synapse Analytics (ehemals Azure SQL DW) zu erteilen, müssen Sie das bereitgestellte SQL-Skript ausführen, wenn Sie eine Verbindung mit der SQL-Datenbank mithilfe der Azure Active Directory-Authentifizierung herstellen. Informationen zur Problembehandlung für weitere Fehler bei der SQL-Momentaufnahme finden Sie unter [Problembehandlung für Fehler bei Momentaufnahme](data-share-troubleshoot.md#snapshot-failed).

## <a name="next-steps"></a>Nächste Schritte
Sie haben erfahren, wie Sie Daten aus SQL-Quellen mithilfe des Azure Data Share-Diensts freigeben und empfangen. Weitere Informationen zum Freigeben von Daten aus anderen Datenquellen finden Sie unter [Unterstützte Datenspeicher](supported-data-stores.md).