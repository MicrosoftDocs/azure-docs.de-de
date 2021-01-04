---
title: Kopieren von Daten aus Blob Storage in SQL-Datenbank – Azure
description: In diesem Tutorial erfahren Sie, wie Sie die Kopieraktivität in einer Azure Data Factory-Pipeline verwenden, um Daten aus Blob Storage in SQL-Datenbank zu kopieren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 15bce219b96268124729de2f475e33fc386348a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021213"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Tutorial: Kopieren von Daten aus Blob Storage in SQL-Datenbank mithilfe von Data Factory
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopier-Assistent](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-Vorlage](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im [Tutorial zur Kopieraktivität](../quickstart-create-data-factory-dot-net.md).

In diesem Tutorial wird eine Data Factory mit einer Pipeline erstellt, um Daten aus Blob Storage in SQL-Datenbank zu kopieren.

Die Kopieraktivität dient zum Verschieben von Daten in Azure Data Factory. Sie basiert auf einem global verfügbaren Dienst, mit dem Daten zwischen verschiedenen Datenspeichern sicher, zuverlässig und skalierbar kopiert werden können. Ausführliche Informationen zur Kopieraktivität finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) .  

> [!NOTE]
> Eine ausführliche Übersicht über den Data Factory-Dienst finden Sie im Artikel [Einführung in Azure Data Factory](data-factory-introduction.md) .
>
>

## <a name="prerequisites-for-the-tutorial"></a>Voraussetzungen für das Tutorial
Bevor Sie mit diesem Tutorial beginnen, müssen folgende Voraussetzungen erfüllt sein:

* **Azure-Abonnement**.  Wenn Sie über kein Abonnement verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Im Artikel [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) finden Sie Details.
* **Azure Storage-Konto**. In diesem Tutorial verwenden Sie den Blobspeicher als **Quelldatenspeicher** . Wenn Sie kein Azure Storage-Konto haben, finden Sie im Artikel [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md) Schritte zum Erstellen eines Azure Storage-Kontos.
* **Azure SQL-Datenbank**. In diesem Tutorial wird Azure SQL-Datenbank als **Zieldatenspeicher** verwendet. Sollten Sie über keine Datenbank in Azure SQL-Datenbank verfügen, die Sie in diesem Tutorial verwenden können, finden Sie unter [Erstellen und Konfigurieren einer Datenbank in Azure SQL-Datenbank](../../azure-sql/database/single-database-create-quickstart.md) eine entsprechende Anleitung.
* **SQL Server 2012/2014 oder Visual Studio 2013**. Sie verwenden SQL Server Management Studio oder Visual Studio zum Erstellen einer Beispieldatenbank und zum Anzeigen der Ergebnisdaten in der Datenbank.  

## <a name="collect-blob-storage-account-name-and-key"></a>Erfassen von Blob-Speicherkontoname und -schlüssel
Sie benötigen in diesem Tutorial den Kontonamen und -schlüssel Ihres Azure-Speicherkontos. Notieren Sie sich **Kontoname** und **Kontoschlüssel** Ihres Azure-Speicherkontos.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie im Menü links auf **Alle Dienste**, und wählen Sie **Speicherkonten** aus.

    ![Durchsuchen > Speicherkonten](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. Wählen Sie auf dem Blatt **Speicherkonten** das **Azure-Speicherkonto** aus, das Sie in diesem Tutorial verwenden möchten.
4. Klicken Sie unter **EINSTELLUNGEN** auf den Link **Zugriffsschlüssel**.
5. Klicken Sie auf die (Bild-)Schaltfläche **Kopieren** neben dem Textfeld **Speicherkontoname**, und speichern Sie den Eintrag beispielsweise in einer Textdatei.
6. Wiederholen Sie den vorherigen Schritt zum Kopieren oder Notieren von **key1**.

    ![Speicherzugriffsschlüssel](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Schließen Sie alle Blätter, indem Sie auf **X** klicken.

## <a name="collect-sql-server-database-user-names"></a>Erfassen der Namen von SQL Server, Datenbank und Benutzer
Sie benötigen die Namen der logischen SQL Server-Instanz, der Datenbank und Benutzer, um dieses Tutorial durchzuführen. Notieren Sie sich den Namen des **Servers**, der **Datenbank** und des **Benutzers** für Azure SQL-Datenbank.

1. Klicken Sie links im **Azure-Portal** auf **Alle Dienste**, und wählen Sie **SQL-Datenbanken** aus.
2. Wählen Sie auf dem Blatt **SQL-Datenbanken** die **Datenbank**, die Sie in diesem Tutorial verwenden möchten. Notieren Sie sich den **Datenbanknamen**.  
3. Klicken Sie auf dem Blatt **SQL-Datenbank** unter **EINSTELLUNGEN** auf **Eigenschaften**.
4. Notieren Sie sich die Werte für **SERVERNAME** und **SERVER-ADMIN-ANMELDUNG**.
5. Schließen Sie alle Blätter, indem Sie auf **X** klicken.

## <a name="allow-azure-services-to-access-sql-server"></a>Gewähren des Zugriffs auf SQL Server für Azure-Dienste
Stellen Sie sicher, dass die Einstellung **Zugriff auf Azure-Dienste erlauben** für Ihren Server **aktiviert** ist, damit der Data Factory-Dienst auf Ihren Server zugreifen kann. Führen Sie folgende Schritte aus, um diese Einstellung zu überprüfen und zu aktivieren:

1. Klicken Sie links im Hub auf **Alle Dienste** und anschließend auf **SQL Server**.
2. Wählen Sie Ihren Server aus, und klicken Sie unter **EINSTELLUNGEN** auf **Firewall**.
3. Klicken Sie auf dem Blatt **Firewalleinstellungen** für **Zugriff auf Azure-Dienste erlauben** auf **EIN**.
4. Schließen Sie alle Blätter, indem Sie auf **X** klicken.

## <a name="prepare-blob-storage-and-sql-database"></a>Vorbereiten von Blob Storage und SQL-Datenbank
Bereiten Sie nun Ihre Azure Blob Storage-Instanz und Azure SQL-Datenbank für das Tutorial vor:  

1. Starten Sie den Editor. Kopieren Sie den folgenden Text, und speichern Sie ihn in der Datei **emp.txt** im Ordner **C:\ADFGetStarted** auf der Festplatte.

    ```
    John, Doe
    Jane, Doe
    ```
2. Verwenden Sie Tools wie den [Azure Storage-Explorer](https://storageexplorer.com/) zum Erstellen des Containers **adftutorial** und zum Hochladen der Datei **emp.txt** in den Container.

3. Verwenden Sie das folgende SQL-Skript, um die Tabelle **emp** in der Azure SQL-Datenbank zu erstellen.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Wenn Sie SQL Server 2012/2014 auf Ihrem Computer installiert haben**: Befolgen Sie die Anweisungen unter [Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio](../../azure-sql/database/single-database-manage.md), um eine Verbindung mit Ihrem Server herzustellen und das SQL-Skript auszuführen.

    Wenn Ihr Client nicht auf die logische SQL Server-Instanz zugreifen darf, müssen Sie die Firewall für Ihren Server so konfigurieren, dass der Zugriff über Ihren Computer (bzw. dessen IP-Adresse) ermöglicht wird. Schritte zum Konfigurieren der Firewall für Ihren Server finden Sie in [diesem Artikel](../../azure-sql/database/firewall-configure.md).

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory
Sie haben die Voraussetzungen erfüllt. Mit einer der folgenden Methoden können Sie eine Data Factory erstellen. Klicken Sie oben auf eine der Optionen in der Dropdownliste oder einen der folgenden Links, um das Tutorial auszuführen.     

* [Kopier-Assistent](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager-Vorlage](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Die Datenpipeline in diesem Tutorial kopiert Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher. Sie transformiert keine Eingabedaten in Ausgabedaten. Ein Tutorial zum Transformieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Erstellen Ihrer ersten Pipeline zum Transformieren von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md).
>
> Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Ausführliche Informationen finden Sie unter [Data Factory – Planung und Ausführung](data-factory-scheduling-and-execution.md).