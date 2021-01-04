---
title: Zugreifen auf Datenspeicher und Dateifreigaben mit Windows-Authentifizierung
description: In diesem Artikel erfahren Sie, wie Sie den SSIS-Katalog in Azure SQL-Datenbank und die Azure-SSIS Integration Runtime in Azure Data Factory so konfigurieren, dass dieser Pakete ausführt, die die Windows-Authentifizierung verwenden, um auf Datenspeicher und Dateifreigaben zuzugreifen.
ms.date: 10/27/2020
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 545f698f444e99d3f3807f22b308963172018fcb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746654"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Zugreifen auf Datenspeicher und Dateifreigaben mit Windows-Authentifizierung in SSIS-Paketen in Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Sie können die Windows-Authentifizierung verwenden, um auf Datenspeicher wie SQL Server-Instanzen, Dateifreigaben, Azure-Dateien usw. in SSIS-Paketen zuzugreifen, die in Ihrer Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) ausgeführt werden. Ihre Datenspeicher können sich lokal befinden, auf Azure Virtual Machines (VMs) gehostet werden oder als verwaltete Dienste in Azure ausgeführt werden. Wenn sie lokal sind, müssen Sie Ihre Azure-SSIS IR mit einem virtuellen Netzwerk (Microsoft Azure Virtual Network) verknüpfen, das mit Ihrem lokalen Netzwerk verbunden ist. Informationen dazu finden Sie unter [Verknüpfen der Azure-SSIS IR mit einem Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md). Es gibt vier Methoden für den Zugriff auf Datenspeicher mit Windows-Authentifizierung in SSIS-Paketen, die in Ihrer Azure-SSIS IR ausgeführt werden:

| Verbindungsmethode | Effektiver Geltungsbereich | Schritt zum Einrichten | Zugriffsmethode in Paketen | Anzahl der Anmeldeinformationssätze und verbundenen Ressourcen | Typ der verbundenen Ressourcen | 
|---|---|---|---|---|---|
| Einrichten eines Ausführungskontexts auf Aktivitätsebene | Pro Aktivität „SSIS-Paket ausführen“ | Konfigurieren Sie die Eigenschaft **Windows-Authentifizierung** , um einen Kontext des Typs „Ausführung/Ausführen als“ einzurichten, wenn Sie SSIS-Pakete mit der Aktivität „SSIS-Paket ausführen“ in ADF-Pipelines ausführen.<br/><br/> Weitere Informationen finden Sie unter [Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in Azure Data Factory](./how-to-invoke-ssis-package-ssis-activity.md). | Sie können direkt auf Ressourcen in Paketen zugreifen, indem Sie z. B. den UNC-Pfad für den Zugriff auf Dateifreigaben oder Azure Files verwenden: `\\YourFileShareServerName\YourFolderName` oder `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Unterstützung nur für einen Anmeldeinformationssatz für alle verbundenen Ressourcen | – Lokale Dateifreigaben oder Dateifreigaben von Azure-VMs<br/><br/> – Azure Files (siehe [Einbinden einer Azure-Dateifreigabe und Zugreifen auf die Freigabe unter Windows](../storage/files/storage-how-to-use-files-windows.md))<br/><br/> – lokale SQL Server-Instanzen/Azure-VMs mit Windows-Authentifizierung<br/><br/> – Andere Ressourcen mit Windows-Authentifizierung |
| Einrichten eines Ausführungskontexts auf Katalogebene | Pro Azure-SSIS IR, wird aber überschrieben, wenn ein Ausführungskontext auf Aktivitätsebene eingerichtet wird (siehe oben) | Führen Sie die gespeicherte SSISDB-Prozedur `catalog.set_execution_credential` aus, um einen Kontext für „Ausführung/Ausführen als“ einzurichten.<br/><br/> Weitere Informationen finden Sie nachfolgend im weiteren Verlauf dieses Artikels. | Sie können direkt auf Ressourcen in Paketen zugreifen, indem Sie z. B. den UNC-Pfad für den Zugriff auf Dateifreigaben oder Azure Files verwenden: `\\YourFileShareServerName\YourFolderName` oder `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Unterstützung nur für einen Anmeldeinformationssatz für alle verbundenen Ressourcen | – Lokale Dateifreigaben oder Dateifreigaben von Azure-VMs<br/><br/> – Azure Files (siehe [Einbinden einer Azure-Dateifreigabe und Zugreifen auf die Freigabe unter Windows](../storage/files/storage-how-to-use-files-windows.md))<br/><br/> – lokale SQL Server-Instanzen/Azure-VMs mit Windows-Authentifizierung<br/><br/> – Andere Ressourcen mit Windows-Authentifizierung |
| Speicherung von Anmeldeinformationen mit dem Befehl `cmdkey` | Pro Azure-SSIS IR, wird aber überschrieben, wenn ein Ausführungskontext auf Aktivitäts- oder Katalogebene eingerichtet wird (siehe oben) | Führen Sie bei der Bereitstellung der Azure-SSIS IR den Befehl `cmdkey` in einem benutzerdefinierten Setupskript (`main.cmd`) aus, z. B. wenn Sie Dateifreigaben, Azure Files oder SQL Server verwenden:<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey` oder<br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> Weitere Informationen finden Sie unter [Anpassen des Setups für die Azure SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md). | Sie können direkt auf Ressourcen in Paketen zugreifen, indem Sie z. B. den UNC-Pfad für den Zugriff auf Dateifreigaben oder Azure Files verwenden: `\\YourFileShareServerName\YourFolderName` oder `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Unterstützung für mehrere Anmeldeinformationssätze für verschiedene verbundene Ressourcen | – Lokale Dateifreigaben oder Dateifreigaben von Azure-VMs<br/><br/> – Azure Files (siehe [Einbinden einer Azure-Dateifreigabe und Zugreifen auf die Freigabe unter Windows](../storage/files/storage-how-to-use-files-windows.md))<br/><br/> – lokale SQL Server-Instanzen/Azure-VMs mit Windows-Authentifizierung<br/><br/> – Andere Ressourcen mit Windows-Authentifizierung |
| Einbinden von Laufwerken zur Paketausführungszeit (ohne Persistenz) | Pro Paket | Führen Sie den Befehl `net use` im Task „Prozess ausführen“ aus, der zu Beginn der Ablaufsteuerung in Ihren Paketen (z.B. `net use D: \\YourFileShareServerName\YourFolderName`) hinzugefügt wird. | Zugriff auf Dateifreigaben über zugeordnete Laufwerke | Unterstützung für mehrere Laufwerke für verschiedene Dateifreigaben | – Lokale Dateifreigaben oder Dateifreigaben von Azure-VMs<br/><br/> – Azure Files (siehe [Einbinden einer Azure-Dateifreigabe und Zugreifen auf die Freigabe unter Windows](../storage/files/storage-how-to-use-files-windows.md)) |
|||||||

> [!WARNING]
> Wenn Sie keine der oben genannten Methoden verwenden, um mit Windows-Authentifizierung auf Datenspeicher zuzugreifen, können Pakete, die von der Windows-Authentifizierung abhängig sind, nicht auf diese zugreifen, und zur Laufzeit treten Fehler auf. 

Im weiteren Verlauf dieses Artikels wird beschrieben, wie Sie den in der SQL-Datenbank-Instanz bzw. verwalteten SQL-Instanz gehosteten SSIS-Katalog (SSISDB) zur Ausführung von Paketen in der Azure-SSIS IR konfigurieren, die die Windows-Authentifizierung für den Zugriff auf Datenspeicher verwenden. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Sie können nur einen Satz Anmeldeinformationen verwenden

Wenn Sie die Windows-Authentifizierung in einem SSIS-Paket verwenden, können Sie nur einen Satz Anmeldeinformationen verwenden. Die Domänenanmeldeinformationen, die Sie beim Ausführen der in diesem Artikel beschriebenen Schritte angeben, gelten solange für alle (interaktiven oder geplanten) Paketausführungen in Ihrer Azure-SSIS IR, bis Sie sie ändern oder entfernen. Wenn Ihr Paket mit unterschiedlichen Anmeldeinformationen eine Verbindung mit mehreren Datenspeichern herstellen muss, sollten Sie die oben genannten alternativen Methoden in Betracht ziehen.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Angeben von Domänenanmeldeinformationen für die Windows-Authentifizierung

Um Domänenanmeldeinformationen bereitzustellen, die es Paketen ermöglichen, mit der Windows-Authentifizierung auf lokale Datenspeicher zuzugreifen, gehen Sie wie folgt vor:

1. Stellen Sie mithilfe von SQL Server Management Studio (SSMS) oder eines anderen Tools eine Verbindung mit der SQL-Datenbank bzw. der verwalteten SQL-Instanz her, die SSISDB hostet. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SSISDB in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öffnen Sie ein Abfragefragefenster mit SSISDB als aktuelle Datenbank.

3. Führen Sie die folgenden gespeicherten Prozeduren aus, und geben Sie passende Domänenanmeldeinformationen an:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Führen Sie die SSIS-Pakete aus. Die Pakete verwenden die Anmeldeinformationen, die Sie für den Zugriff auf lokale Datenspeicher mit Windows-Authentifizierung angegeben haben.

### <a name="view-domain-credentials"></a>Anzeigen von Anmeldeinformationen für eine Domäne

Führen Sie die folgenden Schritte aus, um die aktiven Anmeldeinformationen einer Domäne anzuzeigen:

1. Stellen Sie mithilfe von SSMS oder eines anderen Tools eine Verbindung mit der SQL-Datenbank bzw. der verwalteten SQL-Instanz her, die SSISDB hostet. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SSISDB in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öffnen Sie ein Abfragefragefenster mit SSISDB als aktuelle Datenbank.

3. Führen Sie die folgende gespeicherte Prozedur aus, und überprüfen Sie die Ausgabe:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Löschen von Anmeldeinformationen für eine Domäne
Führen Sie die folgenden Schritte aus, um die Anmeldeinformationen, die Sie angegeben haben, wie in diesem Artikel beschrieben, zu löschen und zu entfernen:

1. Stellen Sie mithilfe von SSMS oder eines anderen Tools eine Verbindung mit der SQL-Datenbank bzw. der verwalteten SQL-Instanz her, die SSISDB hostet. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SSISDB in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öffnen Sie ein Abfragefragefenster mit SSISDB als aktuelle Datenbank.

3. Führen Sie die folgende gespeicherte Prozedur aus:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Herstellen einer lokalen Verbindung mit SQL Server

Um zu überprüfen, ob Sie sich lokal mit einer SQL Server-Instanz verbinden können, gehen Sie wie folgt vor:

1. Suchen Sie einen Computer, der nicht in eine Domäne eingebunden ist, um diesen Test auszuführen.

2. Führen Sie auf dem Computer, der nicht in eine Domäne eingebunden ist, folgenden Befehl aus, um SSMS mit den Domänenanmeldeinformationen zu starten, die Sie verwenden möchten:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. Überprüfen Sie in SSMS, ob Sie sich lokal mit der SQL Server-Instanz verbinden können.

### <a name="prerequisites"></a>Voraussetzungen

Um in Azure ausgeführten Paketen auf eine lokale SQL Server-Instanz zuzugreifen, gehen Sie wie folgt vor:

1.  Aktivieren Sie im SQL Server-Konfigurations-Manager das Protokoll TCP/IP.

2. Lassen Sie den Zugriff über die Windows-Firewall zu. Weitere Informationen finden Sie unter [Konfigurieren der Windows-Firewall für den Zugriff auf SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Verknüpfen Sie Ihre Azure-SSIS IR mit einem Microsoft Azure Virtual Network, das mit der lokalen SQL Server-Instanz verbunden ist.  Weitere Informationen finden Sie im Artikel zum [Verknüpfen einer Azure-SSIS IR mit einem Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md).

4. Verwenden Sie dann die gespeicherte SSISDB-Prozedur `catalog.set_execution_credential`, um Anmeldeinformationen wie in diesem Artikel beschrieben bereitzustellen.

## <a name="connect-to-a-file-share-on-premises"></a>Herstellen einer lokalen Verbindung mit einer Dateifreigabe

Führen Sie die folgenden Schritte durch, um zu überprüfen, ob Sie eine Verbindung mit einer lokalen Dateifreigabe herstellen können:

1. Suchen Sie einen Computer, der nicht in eine Domäne eingebunden ist, um diesen Test auszuführen.

2. Führen Sie auf dem Computer, der nicht in eine Domäne eingebunden ist, die folgenden Befehle aus. Über diese Befehle öffnen Sie zunächst ein Eingabeaufforderungsfenster mit den Domänenanmeldeinformationen, die Sie verwenden möchten, und testen dann die Konnektivität mit der lokalen Dateifreigabe, indem Sie eine Verzeichnisliste abrufen.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Prüfen Sie, ob die Verzeichnisliste für die lokale Dateifreigabe zurückgegeben wird.

### <a name="prerequisites"></a>Voraussetzungen

Um in Azure ausgeführten Paketen auf eine lokale Dateifreigabe zuzugreifen, gehen Sie wie folgt vor:

1. Lassen Sie den Zugriff über die Windows-Firewall zu.

2. Verknüpfen Sie Ihre Azure-SSIS IR mit einem Microsoft Azure Virtual Network, das mit der lokalen Dateifreigabe verbunden ist.  Weitere Informationen finden Sie im Artikel zum [Verknüpfen einer Azure-SSIS IR mit einem Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md).

3. Verwenden Sie dann die gespeicherte SSISDB-Prozedur `catalog.set_execution_credential`, um Anmeldeinformationen wie in diesem Artikel beschrieben bereitzustellen.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Herstellen einer Verbindung mit einer Dateifreigabe auf einer Azure-VM

Um in Azure ausgeführten Paketen auf eine Dateifreigabe auf einer Azure-VM zuzugreifen, gehen Sie wie folgt vor:

1. Stellen Sie mithilfe von SSMS oder eines anderen Tools eine Verbindung mit der SQL-Datenbank bzw. der verwalteten SQL-Instanz her, die SSISDB hostet. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SSISDB in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öffnen Sie ein Abfragefragefenster mit SSISDB als aktuelle Datenbank.

3. Führen Sie die folgenden gespeicherten Prozeduren aus, und geben Sie passende Domänenanmeldeinformationen an:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Herstellen einer Verbindung mit einer Dateifreigabe in Azure Files

Weitere Informationen zu Azure Files finden Sie unter [Azure Files](https://azure.microsoft.com/services/storage/files/).

Um in Azure ausgeführten Paketen auf eine Dateifreigabe in Azure Files zuzugreifen, gehen Sie wie folgt vor:

1. Stellen Sie mithilfe von SSMS oder eines anderen Tools eine Verbindung mit der SQL-Datenbank bzw. der verwalteten SQL-Instanz her, die SSISDB hostet. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SSISDB in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Öffnen Sie ein Abfragefragefenster mit SSISDB als aktuelle Datenbank.

3. Führen Sie die folgenden gespeicherten Prozeduren aus, und geben Sie passende Domänenanmeldeinformationen an:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Nächste Schritte

- Stellen Sie Ihre Pakete bereit. Weitere Informationen finden Sie unter [Bereitstellen eines SSIS-Projekts mit SQL Server Management Studio (SSMS)](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Führen Sie Ihre Pakete aus. Weitere Informationen finden Sie unter [Ausführen von SSIS-Paketen in Azure mit SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Bestimmen Sie einen Zeitplan für Ihre Pakete. Weitere Informationen finden Sie unter [Planen der Ausführung eines SSIS-Pakets in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).
