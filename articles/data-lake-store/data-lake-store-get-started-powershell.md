---
title: Erste Schritte mit Azure Data Lake Storage Gen1 mithilfe von PowerShell | Microsoft-Dokumentation
description: Verwenden von Azure PowerShell zum Erstellen eines Azure Data Lake Storage Gen1-Kontos und Ausführen grundlegender Vorgänge
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 5bec627f114a20033ca4364c39c048763df36b67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161482"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Erste Schritte mit Azure Data Lake Storage Gen1 mithilfe von Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure-Befehlszeilenschnittstelle](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Erfahren Sie, wie Sie mit Azure PowerShell ein Azure Data Lake Storage Gen1-Konto erstellen und grundlegende Vorgänge ausführen, z. B. Ordner erstellen, Datendateien hoch- und herunterladen, Ihr Konto löschen usw. Weitere Informationen zu Data Lake Storage Gen1 finden Sie unter [Übersicht über Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Mindestens Azure PowerShell 1.0**. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentication
In diesem Artikel wird ein einfacheres Authentifizierungskonzept mit Data Lake Storage Gen1 verwendet, bei dem Sie zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto aufgefordert werden. Die Zugriffsebene für das Data Lake Storage Gen1-Konto und das Dateisystem hängt dann von der Zugriffsebene des angemeldeten Benutzers ab. Für die Authentifizierung mit Data Lake Storage Gen1 stehen mit **Endbenutzerauthentifizierung** und **Dienst-zu-Dienst-Authentifizierung** aber auch noch andere Konzepte zur Verfügung. Anweisungen und weitere Informationen zur Authentifizierung finden Sie unter [Endbenutzerauthentifizierung](data-lake-store-end-user-authenticate-using-active-directory.md) oder [Dienst-zu-Dienst-Authentifizierung](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Erstellen eines Data Lake Storage Gen1-Kontos
1. Öffnen Sie auf Ihrem Desktop ein neues Windows PowerShell-Fenster. Geben Sie den folgenden Codeausschnitt ein, um sich bei Ihrem Azure-Konto anzumelden, das Abonnement festzulegen und den Data Lake Storage Gen1-Anbieter zu registrieren. Stellen Sie bei der Aufforderung zum Anmelden sicher, dass Sie sich als einer der Administratoren/Besitzer des Abonnements anmelden:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Ein Data Lake Storage Gen1-Konto wird einer Azure-Ressourcengruppe zugeordnet. Erstellen Sie zunächst eine Azure-Ressourcengruppe.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Erstellen einer Azure-Ressourcengruppe](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Erstellen einer Azure-Ressourcengruppe")
3. Erstellen Sie ein Data Lake Storage Gen1-Konto. Der angegebene Name darf nur Kleinbuchstaben und Zahlen enthalten.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    ![Erstellen eines Data Lake Storage Gen1-Kontos](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Erstellen eines Data Lake Storage Gen1-Kontos")
4. Stellen Sie sicher, dass das Konto erfolgreich erstellt wurde.

        Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

    Die Ausgabe für das Cmdlet sollte **True** lauten.

## <a name="create-directory-structures-in-your-data-lake-storage-gen1-account"></a>Erstellen von Verzeichnisstrukturen in Ihrem Data Lake Storage Gen1-Konto
Sie können in Ihrem Data Lake Storage Gen1-Konto Verzeichnisse zum Verwalten und Speichern von Daten erstellen.

1. Legen Sie ein Stammverzeichnis fest.

        $myrootdir = "/"
2. Erstellen Sie ein neues Verzeichnis namens **mynewdirectory** unter dem festgelegten Stammverzeichnis.

        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
3. Stellen Sie sicher, dass das neue Verzeichnis erfolgreich erstellt wurde.

        Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir

    Es sollte eine Ausgabe wie im folgenden Screenshot angezeigt werden:

    ![Überprüfen des Verzeichnisses](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Überprüfen des Verzeichnisses")

## <a name="upload-data-to-your-data-lake-storage-gen1-account"></a>Hochladen von Daten in Ihr Data Lake Storage Gen1-Konto
Sie können Ihre Daten direkt auf die Data Lake Storage Gen1-Stammebene oder in ein im Konto erstelltes Verzeichnis hochladen. Die Codeausschnitte in diesem Abschnitt veranschaulichen das Hochladen von Beispieldaten in das im vorigen Abschnitt erstellte Verzeichnis (**mynewdirectory**).

Wenn Sie Beispieldaten zum Hochladen verwenden möchten, können Sie den Ordner **Ambulance Data** aus dem [Azure Data Lake-Git-Repository](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)herunterladen. Laden Sie die Datei herunter, und speichern Sie sie in einem lokalen Verzeichnis auf dem Computer, z.B. „C:\sampledata\“.

    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-storage-gen1-account"></a>Umbenennen, Herunterladen und Löschen von Daten in Ihrem Data Lake Storage Gen1-Konto
Verwenden Sie zum Umbenennen einer Datei den folgenden Befehl:

    Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Verwenden Sie zum Downloaden einer Datei den folgenden Befehl:

    Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Verwenden Sie zum Löschen einer Datei den folgenden Befehl:

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Geben Sie nach entsprechender Aufforderung **Y** ein, um das Element zu löschen. Wenn mehrere Dateien gelöscht werden sollen, können Sie die betreffenden Pfade durch Kommas getrennt bereitstellen.

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-data-lake-storage-gen1-account"></a>Löschen Ihres Data Lake Storage Gen1-Kontos
Verwenden Sie den folgenden Befehl zum Löschen Ihres Data Lake Storage Gen1-Kontos.

    Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

Geben Sie nach entsprechender Aufforderung **Y** ein, um das Konto zu löschen.

## <a name="next-steps"></a>Nächste Schritte
* [Leitfaden zur Leistungsoptimierung für die Verwendung von PowerShell mit Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Verwenden von Azure Data Lake Storage Gen1 für Big Data-Anforderungen](data-lake-store-data-scenarios.md) 
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Verwenden von Azure HDInsight mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
