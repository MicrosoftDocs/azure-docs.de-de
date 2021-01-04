---
title: Importieren oder Exportieren einer Azure SQL-Datenbank, ohne Azure-Diensten Zugriff auf den Server zu erlauben
description: Importieren oder Exportieren einer Azure SQL-Datenbank, ohne Azure-Diensten Zugriff auf den Server zu erlauben
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: be966a651df0c896ac7e1973d7783bb7fb686be3
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676498"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importieren oder Exportieren einer Azure SQL-Datenbank, ohne Azure-Diensten Zugriff auf den Server zu erlauben
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Artikel erfahren Sie, wie Sie eine Azure SQL-Datenbank importieren oder exportieren, wenn *Azure-Dienste zulassen* auf dem Server auf *AUS* festgelegt ist. Der Workflow verwendet einen virtuellen Azure-Computer, auf dem SqlPackage ausgeführt wird, um den Import- oder Exportvorgang durchzuführen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-the-azure-virtual-machine"></a>Erstellen des virtuellen Azure-Computers

Erstellen Sie einen virtuellen Azure-Computer, indem Sie die Schaltfläche **In Azure bereitstellen** auswählen.

Diese Vorlage bietet die Möglichkeit, einen einfachen virtuellen Windows-Computer mit einigen wenigen Optionen für die Windows-Version bereitzustellen, wobei die neueste gepatchte Version verwendet wird. Damit wird ein virtueller Computer der Größe A2 an der Position der Ressourcengruppe bereitgestellt, und es wird der voll qualifizierte Domänenname des virtuellen Computers zurückgegeben.
<br><br>

[![Bild einer Schaltfläche mit der Bezeichnung „Bereitstellung in Azure“](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

Weitere Informationen finden Sie unter [Sehr einfache Bereitstellung eines virtuellen Windows-Computers](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).

## <a name="connect-to-the-virtual-machine"></a>Verbinden mit dem virtuellen Computer

In den folgenden Schritten wird gezeigt, wie Sie eine Remotedesktopverbindung zu Ihrem virtuellen Computer herstellen.

1. Navigieren Sie zur VM-Ressource, nachdem die Bereitstellung abgeschlossen wurde.

   ![Der Screenshot zeigt eine Seite „Übersicht über virtuelle Computer“ mit der Schaltfläche „Verbinden“.](./media/database-import-export-azure-services-off/vm.png)  

2. Wählen Sie **Verbinden** .

   Eine Formular für eine RDP-Datei (Remotedesktopprotokoll) wird mit der öffentlichen IP-Adresse und der Portnummer für den virtuellen Computer angezeigt.

   ![RDP-Formular](./media/database-import-export-azure-services-off/rdp.png)  

3. Wählen Sie **RDP-Datei herunterladen** aus.

   > [!NOTE]
   > Sie können für die Verbindung mit Ihrem virtuellen Computer auch SSH verwenden.

4. Schließen Sie das Formular **Mit virtuellem Computer verbinden** .
5. Öffnen Sie die heruntergeladene RDP-Datei, um eine Verbindung mit Ihrem virtuellen Computer herzustellen.
6. Wenn Sie dazu aufgefordert werden, wählen Sie **Connect** aus. Auf einem Macintosh benötigen Sie einen RDP-Client, z. B. diesen [Remotedesktopclien](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12)t aus dem Mac App Store.

7. Geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie beim Erstellen des virtuellen Computers festgelegt haben, und wählen Sie anschließend **OK** aus.

8. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wählen Sie **Ja** bzw. **Weiter** aus, um mit dem Herstellen der Verbindung fortzufahren.

## <a name="install-sqlpackage"></a>Installieren von SqlPackage

[Laden Sie die aktuelle Version von SqlPackage herunter, und installieren Sie sie](/sql/tools/sqlpackage-download).

Weitere Informationen finden Sie unter [SqlPackage.exe](/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Erstellen einer Firewallregel, um dem virtuellen Computer Zugriff auf die Datenbank zu gestatten

Fügen Sie die öffentliche IP-Adresse des virtuellen Computers der Firewall des Servers hinzu.

Mit den folgenden Schritten wird eine IP-Firewallregel auf Serverebene für die öffentliche IP-Adresse Ihres virtuellen Computers erstellt, um eine Verbindung zum virtuellen Computer zu ermöglichen.

1. Wählen Sie im Menü auf der linken Seite die Option **SQL-Datenbanken** und dann auf der Seite **SQL-Datenbanken** Ihre Datenbank aus. Die Übersichtsseite für Ihre Datenbank wird geöffnet. Diese enthält den vollqualifizierten Servernamen (z. B. **servername.database.windows.net** ) und Optionen für die weitere Konfiguration.

2. Kopieren Sie diesen vollqualifizierten Servernamen, damit Sie ihn beim Herstellen der Verbindung mit Ihrem Server und den zugehörigen Datenbanken verwenden können.

   ![Servername](./media/database-import-export-azure-services-off/server-name.png)

3. Wählen Sie in der Symbolleiste die Option **Serverfirewall festlegen** . Die Seite **Firewalleinstellungen** für den Server wird geöffnet.

   ![IP-Firewallregel auf Serverebene](./media/database-import-export-azure-services-off/server-firewall-rule.png)

4. Wählen Sie auf der Symbolleiste die Option **Client-IP-Adresse hinzufügen** aus, um die öffentliche IP-Adresse des virtuellen Computers zu einer neuen IP-Firewallregel auf Serverebene hinzuzufügen. Eine IP-Firewallregel auf Serverebene kann Port 1433 für eine einzelne IP-Adresse oder einen Bereich von IP-Adressen öffnen.

5. Wählen Sie **Speichern** aus. Für die öffentliche IP-Adresse des virtuellen Computers wird eine IP-Firewallregel auf Serverebene erstellt, die auf dem Server den Port 1433 öffnet.

6. Schließen Sie die Seite **Firewalleinstellungen** .

## <a name="export-a-database-using-sqlpackage"></a>Exportieren einer Datenbank mit SqlPackage

Informationen zum Exportieren einer Azure SQL-Datenbank mit dem Befehlszeilenprogramm [SqlPackage](/sql/tools/sqlpackage) finden Sie unter [Exportparameter und -eigenschaften](/sql/tools/sqlpackage#export-parameters-and-properties). Das SqlPackage-Hilfsprogramm ist im Lieferumfang der neuesten Versionen von [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) und [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) enthalten. Alternativ dazu können Sie die neueste Version von [SqlPackage](/sql/tools/sqlpackage-download) herunterladen.

Die Verwendung des SqlPackage-Hilfsprogramms wird aus Gründen der Skalierbarkeit und Leistung für die meisten Produktionsumgebungen empfohlen. Einen Blogbeitrag des SQL Server-Kundenberatungsteams zur Migration mithilfe von BACPAC-Dateien finden Sie unter [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien).

In diesem Beispiel wird gezeigt, wie eine Datenbank mithilfe von „SqlPackage.exe“ mit universeller Active Directory-Authentifizierung exportiert wird. Ersetzen Sie die Werte durch die jeweiligen Werte für Ihre Umgebung.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-a-database-using-sqlpackage"></a>Importieren einer Datenbank mit SqlPackage

Informationen zum Importieren einer SQL Server-Datenbank mit dem Befehlszeilenprogramm [SqlPackage](/sql/tools/sqlpackage) finden Sie unter [Importparameter und -eigenschaften](/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage gehört zu den neuesten Versionen von [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) und [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt). Sie können die neueste Version von [SqlPackage](/sql/tools/sqlpackage-download) auch herunterladen.

Aus Gründen der Skalierbarkeit und Leistung wird die Verwendung von SqlPackage (statt des Azure-Portals) für die meisten Produktionsumgebungen empfohlen. Einen Blogbeitrag des SQL Server-Kundenberatungsteams zur Migration mithilfe von `BACPAC`-Dateien finden Sie unter [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien).

Mit dem folgenden SqlPackage-Befehl wird die Datenbank **AdventureWorks2017** aus dem lokalen Speicher in eine Azure SQL-Datenbank importiert. Er erstellt eine neue Datenbank namens **myMigratedDatabase** mit der **Premium** -Dienstebene und dem Dienstziel **P6** . Ändern Sie diese Werte entsprechend Ihrer Umgebung.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Um von hinter einer Unternehmensfirewall eine Verbindung mit einer Azure SQL-Datenbank herzustellen, muss der Port 1433 der Firewall geöffnet sein.

In diesem Beispiel wird gezeigt, wie eine Datenbank mithilfe von SqlPackage mit universeller Active Directory-Authentifizierung importiert wird.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Überlegungen zur Leistung

Die Exportgeschwindigkeiten schwanken aufgrund vieler Faktoren (z. B. Form der Daten), sodass keine Vorhersage möglich ist, welche Geschwindigkeit erwartet werden kann. Die Ausführung von SqlPackage kann viel Zeit in Anspruch nehmen, insbesondere bei großen Datenbanken.

Um optimale Leistung zu erzielen, können Sie die folgenden Strategien ausprobieren:

1. Stellen Sie sicher, dass für die Datenbank keine andere Workload ausgeführt wird. Das Erstellen einer Kopie vor dem Export ist u. U. die beste Lösung, um sicherzustellen, dass keine andere Workload ausgeführt wird.
2. Erhöhen Sie das Service Level Objective für die Datenbank, um die Verarbeitung der Exportworkload zu verbessern (hauptsächlich Lese-E/A). Wenn die Datenbank zurzeit GP_Gen5_4 ist, kann eine unternehmenskritische Ebene bei der Leseworkload ggf. hilfreich sein.
3. Stellen Sie sicher, dass gruppierte Indizes vorhanden sind, vor allem für große Tabellen.
4. Virtuelle Computer (VMs) sollten sich in derselben Region wie die Datenbank befinden, um Netzwerkeinschränkungen zu vermeiden.
5. VMs sollten über eine SSD mit angemessener Größe zum Erstellen temporärer Artefakte vor dem Hochladen in den Blobspeicher verfügen.
6. VMs sollten über eine ausreichende Kern- und Arbeitsspeicherkonfiguration für die jeweilige Datenbank verfügen.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Speichern der importierten oder exportierten BACPAC-Datei

Die BACPAC-Datei kann in [Azure-Blobs](../../storage/blobs/storage-blobs-overview.md)oder [Azure Files](../../storage/files/storage-files-introduction.md) gespeichert werden.

Verwenden Sie Azure Files, um optimale Leistung zu erzielen. SqlPackage arbeitet mit dem Dateisystem, sodass ein direkter Zugriff auf Azure Files möglich ist.

Verwenden Sie Azure-Blobs, die kostengünstiger sind als eine Azure Premium-Dateifreigabe, um die Kosten zu reduzieren. Allerdings müssen Sie die [BACPAC-Datei](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) vor dem Import- oder Exportvorgang zwischen dem Blob und dem lokalen Dateisystem kopieren. Daher dauert der Prozess länger.

Weitere Informationen zum Hochladen oder Herunterladen von BACPAC-Dateien finden Sie unter [Übertragen von Daten mit AzCopy und Blobspeicher](../../storage/common/storage-use-azcopy-blobs.md) und [Übertragen von Daten mit AzCopy und Dateispeicher](../../storage/common/storage-use-azcopy-files.md).

Abhängig von Ihrer Umgebung müssen Sie u. U. [Azure Storage-Firewalls und virtuelle Netzwerke konfigurieren](../../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Herstellen einer Verbindung mit einer importierten SQL-Datenbank und zum Abfragen einer solchen Datenbank finden Sie unter [Schnellstart: Azure SQL-Datenbank: Verwenden von SQL Server Management Studio zum Herstellen der Verbindung und Abfragen von Daten](connect-query-ssms.md).
- Einen Blogbeitrag des SQL Server-Kundenberatungsteams zur Migration mithilfe von BACPAC-Dateien finden Sie unter [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien).
- Eine Erläuterung des gesamten Migrationsprozesses von SQL Server-Datenbanken, einschließlich Empfehlungen zur Leistung, finden Sie unter [Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank](migrate-to-database-from-sql-server.md).
- Informationen zum sicheren Verwalten und Freigeben von Speicherschlüsseln und SAS finden Sie im [Azure Storage-Sicherheitsleitfaden](../../storage/blobs/security-recommendations.md).