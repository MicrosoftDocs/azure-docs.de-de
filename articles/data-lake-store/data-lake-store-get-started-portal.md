---
title: Erste Schritte mit Azure Data Lake Storage Gen1 unter Verwendung des Azure-Portals | Microsoft-Dokumentation
description: Verwenden Sie das Azure-Portal, um ein Azure Data Lake Storage Gen1-Konto zu erstellen und grundlegende Vorgänge im Data Lake Storage Gen1-Konto auszuführen.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: e021d8c056028c03ac71d2a27c9128272f374da6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60877952"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Erste Schritte mit Data Lake Storage Gen1 unter Verwendung des Azure-Portals

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure-Befehlszeilenschnittstelle](data-lake-store-get-started-cli-2.0.md)
>
> 

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Erfahren Sie, wie Sie im Azure-Portal ein Azure Data Lake Storage Gen1-Konto erstellen und grundlegende Vorgänge ausführen, z. B. Ordner erstellen, Datendateien hoch- und herunterladen, Ihr Konto löschen usw. Weitere Informationen finden Sie in der [Übersicht über Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Erstellen eines Data Lake Storage Gen1-Kontos

1. Melden Sie sich am neuen [Azure-Portal](https://portal.azure.com)an.
2. Klicken Sie auf **Ressource erstellen > Speicher > Data Lake Storage Gen1**.
3. Geben Sie auf dem Blatt **Neuer Data Lake Storage Gen1** die Werte wie im folgenden Screenshot gezeigt an:
   
    ![Erstellen eines neuen Data Lake Storage Gen1-Kontos](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Erstellen eines neuen Data Lake Storage Gen1-Kontos")
   
   * **Name**: Geben Sie einen eindeutigen Namen für das Data Lake Storage Gen1-Konto ein.
   * **Abonnement**. Wählen Sie das Abonnement aus, unter dem Sie ein neues Data Lake Storage Gen1-Konto erstellen möchten.
   * **Ressourcengruppe**. Wählen Sie eine vorhandene Ressourcengruppe aus, oder wählen Sie die Option **Neu erstellen**, um eine Ressourcengruppe zu erstellen. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Anwendung enthält. Weitere Informationen finden Sie unter [Ressourcengruppen in Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Standort**: Wählen Sie einen Standort aus, an dem Sie das Data Lake Storage Gen1-Konto erstellen möchten.
   * **Verschlüsselungseinstellungen**. Drei Optionen stehen zur Verfügung:
     
     * **Verschlüsselung nicht aktivieren**.
     * Wählen Sie **Von Data Lake Storage Gen1 verwaltete Schlüssel verwenden** aus, wenn Ihre Verschlüsselungsschlüssel von Data Lake Storage Gen1 verwaltet werden sollen.
     * **Schlüssel aus Ihrem eigenen Schlüsseltresor verwenden**. Sie können eine vorhandene Azure Key Vault-Instanz auswählen oder eine neue Key Vault-Instanz erstellen. Wenn Sie die Schlüssel aus einer Key Vault-Instanz verwenden möchten, müssen Sie dem Data Lake Storage Gen1-Konto Berechtigungen zum Zugreifen auf die Azure Key Vault-Instanz zuweisen. Eine entsprechende Anleitung finden Sie unter [Zuweisen von Berechtigungen zu Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Data Lake Storage Gen1-Verschlüsselung](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Storage Gen1-Verschlüsselung")
       
        Klicken Sie auf dem Blatt **Verschlüsselungseinstellungen** auf **OK**.

        Weitere Informationen finden Sie unter [Verschlüsselung von Daten in Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Klicken Sie auf **Create**. Wenn Sie die Option zum Anheften des Kontos an das Dashboard ausgewählt haben, wird wieder das Dashboard angezeigt, und Sie können den Fortschritt der Bereitstellung Ihres Data Lake Storage Gen1-Kontos überprüfen. Nachdem die Bereitstellung des Data Lake Storage Gen1-Kontos erfolgt ist, wird das Kontoblatt angezeigt.

## <a name="assign-permissions-to-azure-key-vault"></a>Zuweisen von Berechtigungen zu Azure Key Vault
Wenn Sie Schlüssel aus einem Azure Key Vault zum Konfigurieren der Verschlüsselung für das Data Lake Storage Gen1-Konto verwendet haben, müssen Sie den Zugriff zwischen dem Data Lake Storage Gen1-Konto und dem Azure Key Vault-Konto konfigurieren. Führen Sie hierzu die folgenden Schritte aus:

1. Wenn Sie Schlüssel aus dem Azure Key Vault verwendet haben, wird oben auf dem Blatt für das Data Lake Storage Gen1-Konto eine Warnung angezeigt. Klicken Sie auf die Warnung, um das Blatt **Verschlüsselung** zu öffnen.
   
    ![Data Lake Storage Gen1-Verschlüsselung](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Storage Gen1-Verschlüsselung")
2. Das Blatt enthält zwei Optionen zum Konfigurieren des Zugriffs.

    ![Data Lake Storage Gen1-Verschlüsselung](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Data Lake Storage Gen1-Verschlüsselung")
   
   * Klicken Sie unter der ersten Option auf **Berechtigungen erteilen**, um den Zugriff zu konfigurieren. Die erste Option ist nur aktiviert, wenn der Benutzer, der das Data Lake Storage Gen1-Konto erstellt hat, auch ein Azure Key Vault-Administrator ist.
   * Mit der anderen Option wird das PowerShell-Cmdlet ausgeführt, das auf dem Blatt angezeigt wird. Sie müssen der Besitzer der Azure Key Vault-Instanz sein oder Berechtigungen für Azure Key Vault gewähren können. Wechseln Sie nach dem Ausführen des Cmdlets zurück zum Blatt, und klicken Sie auf **Aktivieren**, um den Zugriff zu konfigurieren.

> [!NOTE]
> Sie können ein Data Lake Storage Gen1-Konto auch mithilfe von Azure Resource Manager-Vorlagen erstellen. Diese Vorlagen stehen unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?term=data+lake+store) zur Verfügung:
> - Ohne Datenverschlüsselung: [Bereitstellen des Azure Data Lake Storage Gen1-Kontos ohne Datenverschlüsselung](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> - Mit Datenverschlüsselung über Data Lake Storage Gen1: [Bereitstellen des Data Lake Storage Gen1-Kontos mit Verschlüsselung (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> - Mit Datenverschlüsselung über Azure Key Vault: [Bereitstellen des Data Lake Storage Gen1-Kontos mit Verschlüsselung (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
> 
> 



## <a name="createfolder"></a>Erstellen von Ordnern in einem Data Lake Storage Gen1-Konto
Sie können in Ihrem Data Lake Storage Gen1-Konto Ordner zum Verwalten und Speichern von Daten erstellen.

1. Öffnen Sie das von Ihnen erstellte Data Lake Storage Gen1-Konto. Klicken Sie im linken Bereich auf **Alle Ressourcen** und dann auf dem Blatt „Alle Ressourcen“ auf den Kontonamen, unter dem Sie Ordner erstellen möchten. Wenn Sie das Konto an das Startmenü angeheftet haben, klicken Sie auf die Kontokachel.
2. Klicken Sie auf dem Blatt für Ihr Data Lake Storage Gen1-Konto auf **Daten-Explorer**.
   
    ![Erstellen von Ordnern in einem Data Lake Storage Gen1-Konto](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Erstellen von Ordnern in einem Data Lake Storage Gen1-Konto")
3. Klicken Sie auf dem Blatt „Daten-Explorer“ auf **Neuer Ordner**, geben Sie einen Namen für den neuen Ordner ein, und klicken Sie dann auf **OK**.
   
    ![Erstellen von Ordnern in einem Data Lake Storage Gen1-Konto](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Erstellen von Ordnern in einem Data Lake Storage Gen1-Konto")
   
    Der neu erstellte Ordner wird auf dem Blatt **Daten-Explorer** angezeigt. Sie können geschachtelte Ordner mit beliebig vielen Schachtelungsebenen erstellen.
   
    ![Erstellen von Ordnern in einem Data Lake-Konto](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Erstellen von Ordnern in einem Data Lake-Konto")

## <a name="uploaddata"></a>Hochladen von Daten in ein Data Lake Storage Gen1-Konto
Sie können Ihre Daten direkt auf die Stammebene eines Data Lake Storage Gen1-Kontos oder in einen im Konto erstellten Ordner hochladen. 

1. Klicken Sie auf dem Blatt **Daten-Explorer** auf **Upload** (Hochladen). 
2. Navigieren Sie auf dem Blatt **Dateien hochladen** zu den Dateien, die Sie hochladen möchten, und klicken Sie dann auf **Ausgewählte Dateien hinzufügen**. Sie können auch mehr als eine Datei für den Upload auswählen.

    ![Hochladen von Daten](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Hochladen von Daten")

Wenn Sie Beispieldaten zum Hochladen verwenden möchten, können Sie den Ordner **Ambulance Data** aus dem [Azure Data Lake-Git-Repository](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)herunterladen.

## <a name="properties"></a>Verfügbare Aktionen für die gespeicherten Daten
Klicken Sie für eine Datei auf das Symbol mit den Auslassungszeichen und dann im Popupmenü auf die Aktion, die Sie für die Daten durchführen möchten.

![Eigenschaften der Daten](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Eigenschaften der Daten") 

## <a name="secure-your-data"></a>Sichern der Daten
Sie können die in Ihrem Data Lake Storage Gen1-Konto gespeicherten Daten mithilfe von Azure Active Directory und der Zugriffssteuerung (Zugriffssteuerungslisten, Access Control Lists, ACLs) sichern. Anweisungen dazu finden Sie unter [Sichern von Daten in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-a-data-lake-storage-gen1-account"></a>Löschen eines Data Lake Storage Gen1-Kontos
Um ein Data Lake Storage Gen1-Konto zu löschen, klicken Sie auf dem Blatt „Data Lake Storage Gen1“ auf **Löschen**. Sie werden aufgefordert, den Namen des zu löschenden Kontos einzugeben, um die Aktion zu bestätigen. Geben Sie den Namen des Kontos ein, und klicken Sie dann auf **Löschen**.

![Löschen eines Data Lake Storage Gen1-Kontos](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Löschen eines Data Lake-Kontos")

## <a name="next-steps"></a>Nächste Schritte
* [Verwenden von Azure Data Lake Storage Gen1 für Big Data-Anforderungen](data-lake-store-data-scenarios.md) 
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Verwenden von Azure HDInsight mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

