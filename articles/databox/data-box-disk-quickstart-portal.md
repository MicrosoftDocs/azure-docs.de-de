---
title: Schnellstart für Microsoft Azure Data Box-Datenträger | Microsoft-Dokumentation
description: Verwenden Sie diese Schnellstartanleitung, um Ihren Azure Data Box-Datenträger schnell im Azure-Portal bereitzustellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 23615daf4a07e02b01bbd5a9cdf57ec9a81a2b76
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347392"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Schnellstart: Bereitstellen von Azure Data Box Disk über das Azure-Portal

In dieser Schnellstartanleitung wird beschrieben, wie Sie den Azure Data Box-Datenträger über das Azure-Portal bereitstellen. In den Schritten erfahren Sie, wie Sie schnell einen Auftrag erstellen, Datenträger erhalten, diese auspacken und anschließen und Daten auf die Datenträger kopieren, damit diese in Azure hochgeladen werden können.

Eine ausführliche Anleitung zur Schritt-für-Schritt-Bereitstellung und zur Nachverfolgung finden Sie unter [Tutorial: Bestellen von Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true) erstellen.

::: zone-end

::: zone target="chromeless"

In diesem Leitfaden wird die Verwendung von Azure Data Box Disk im Azure-Portal erläutert. Dieser Leitfaden beantwortet die folgenden Fragen:

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Voraussetzungen

Vorbereitungen

- Stellen Sie sicher, dass Ihr Abonnement für den Azure Data Box-Dienst aktiviert ist. [Sie müssen sich für den Dienst registrieren](https://aka.ms/azuredataboxfromdiskdocs), um das Abonnement für diesen Dienst zu aktivieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs) beim Azure-Portal an.

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Überprüfen der Voraussetzungen:** Überprüfen Sie die Anzahl von Datenträgern und Kabeln, das Betriebssystem und andere Softwarekomponenten.
> - **Anschließen und Entsperren:** Verbinden Sie das Gerät, und entsperren Sie den Datenträger, um die Daten zu kopieren.
> - **Kopieren von Daten auf den Datenträger und Durchführen der Überprüfung:** Kopieren Sie Daten in die vorab erstellten Ordner auf den Datenträgern.
> - **Zurücksenden der Datenträger:** Schicken Sie die Datenträger zurück ans Azure-Datencenter. Dort werden die Daten in Ihr Speicherkonto hochgeladen.
> - **Überprüfen der Daten in Azure:** Vergewissern Sie sich, dass Ihre Daten in Ihr Speicherkonto hochgeladen wurden, bevor Sie sie vom Quelldatenserver löschen.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Order

### <a name="portal"></a>[Portal](#tab/azure-portal)

Dieser Schritt dauert ungefähr fünf Minuten.

1. Erstellen Sie im Azure-Portal eine neue Azure Data Box-Ressource. 
2. Wählen Sie ein Abonnement aus, das für diesen Dienst aktiviert ist, und wählen Sie als Übertragungstyp **Import** aus. Geben Sie unter **Quellland/-region** an, wo sich die Daten befinden, und legen Sie die **Azure-Zielregion** für die Datenübertragung fest.
3. Wählen Sie **Data Box-Datenträger** aus. Die maximale Kapazität der Lösung beträgt 35 TB, aber Sie können auch mehrere Aufträge für Datenträger erstellen, um weitere Datenträger anzufordern und höhere Datenmengen abzudecken.  
4. Geben Sie die Auftragsdetails und Versandinformationen ein. Wenn der Dienst in Ihrer Region verfügbar ist, können Sie E-Mail-Adressen für Benachrichtigungen angeben, die Zusammenfassung prüfen und anschließend den Auftrag erstellen.

Nachdem der Auftrag erstellt wurde, werden die Datenträger für den Versand vorbereitet.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie die folgenden Azure CLI-Befehle, um einen Data Box Disk-Auftrag zu erstellen.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen, oder verwenden Sie eine vorhandene Ressourcengruppe:

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. Verwenden Sie den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create), um ein Speicherkonto zu erstellen, oder verwenden Sie ein vorhandenes Speicherkonto:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Führen Sie den Befehl [az databox job create](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_create) aus, um einen Data Box-Auftrag mit der SKU „DataBoxDisk“ zu erstellen:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. Führen Sie den Befehl [az databox job update](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_update) aus, um einen Auftrag zu aktualisieren, wie im folgenden Beispiel zum Ändern des Namens und der E-Mail-Adresse des Kontakts gezeigt:

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   Führen Sie den Befehl [az databox job show](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_show) aus, um Informationen zum Auftrag abzurufen:

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   Verwenden Sie den Befehl [az databox job list]( /cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_list), um alle Data Box-Aufträge für eine Ressourcengruppe anzuzeigen:

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   Führen Sie den Befehl [az databox job cancel](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_cancel) aus, um einen Auftrag abzubrechen:

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   Führen Sie den Befehl [az databox job delete](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_delete) aus, um einen Auftrag zu löschen:

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. Verwenden Sie den Befehl [az databox job list-credentials]( /cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_list_credentials), um Anmeldeinformationen für einen Data Box-Auftrag aufzulisten:

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

Nachdem der Auftrag erstellt wurde, wird das Gerät für den Versand vorbereitet.

---

## <a name="unpack"></a>Auspacken

Dieser Schritt dauert ungefähr fünf Minuten.

Die Data Box-Datenträger werden per UPS Express-Paket verschickt. Öffnen Sie das Paket, und vergewissern Sie sich, dass darin Folgendes enthalten ist:

- 1 bis 5 USB-Datenträger in Luftpolsterfolie
- Ein Verbindungskabel pro Datenträger
- Versandetikett für die Rücksendung

## <a name="connect-and-unlock"></a>Anschließen und Entsperren

Dieser Schritt dauert ungefähr fünf Minuten.

1. Verwenden Sie das beigefügte Kabel, um den Datenträger mit einem Windows-/Linux-Computer zu verbinden, auf dem eine unterstützte Version ausgeführt wird. Weitere Informationen zu unterstützten Betriebssystemversionen finden Sie unter den [Systemanforderungen für Azure Data Box-Datenträger](data-box-disk-system-requirements.md). 
2. Entsperren Sie den Datenträger wie folgt:

    1. Wechseln Sie im Azure-Portal zu **Allgemein > Gerätedetails** , und rufen Sie den Hauptschlüssel ab.
    2. Laden Sie das Tool zum Entsperren von betriebssystemspezifischen Data Box-Datenträgern herunter, und extrahieren Sie es auf dem Computer, den Sie zum Kopieren der Daten auf die Datenträger verwenden möchten. 
    3. Führen Sie das Tool zum Entsperren von Data Box-Datenträgern aus, und geben Sie den Hauptschlüssel an. Führen Sie für alle neu eingelegten Datenträger erneut das Entsperrungstool aus, und geben Sie den Hauptschlüssel an. **Verwenden Sie zum Entsperren des Datenträgers nicht das BitLocker-Dialogfeld oder den BitLocker-Schlüssel.** Weitere Informationen zum Entsperren von Datenträgern finden Sie unter [Entsperren von Datenträgern auf einem Windows-Client](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) bzw. [Entsperren von Datenträgern auf einem Linux-Client](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. Der Laufwerkbuchstabe, der dem Datenträger zugewiesen ist, wird vom Tool angezeigt. Notieren Sie sich den Laufwerkbuchstaben des Datenträgers. Er wird in den nachfolgenden Schritten verwendet.

## <a name="copy-data-and-validate"></a>Kopieren von Daten und Durchführen der Validierung

Der Zeitraum, der für die Durchführung dieses Vorgangs erforderlich ist, hängt von Ihrer Datengröße ab.

1. Das Laufwerk enthält die Ordner *PageBlob* , *BlockBlob* , *AzureFile* , *ManagedDisk* und *DataBoxDiskImport*. Kopieren Sie die zu importierenden Daten per Drag & Drop als Blockblobs in den Ordner *BlockBlob*. Ziehen Sie auf VHDs/VHDXs gespeicherte Daten per Drag & Drop in den Ordner *PageBlob* und geeignete Daten in den Ordner *AzureFile*. Kopieren Sie die VHDs, die Sie als verwaltete Datenträger hochladen möchten, in einen Ordner unter *ManagedDisk*.

    Im Azure-Speicherkonto wird für jeden Unterordner der Ordner *BlockBlob* und *PageBlob* ein Container erstellt. Für einen Unterordner in *AzureFile* wird eine Dateifreigabe erstellt.

    Alle Dateien in den Ordnern *BlockBlob* und *PageBlob* werden in den Standardcontainer `$root` unter dem Azure Storage-Konto kopiert. Kopieren Sie Dateien in einen Ordner innerhalb von *AzureFile*. Für alle direkt in den Ordner *AzureFile* kopierten Dateien tritt ein Fehler auf, und sie werden als Blockblobs hochgeladen.

    > [!NOTE]
    > - Für alle Container, Blobs und Dateien sollten die [Azure-Namenskonventionen](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) eingehalten werden. Wenn diese Regeln nicht befolgt werden, tritt beim Datenupload in Azure ein Fehler auf.
    > - Stellen Sie sicher, dass Dateien für Blockblobs eine Größe von ca. 4,75 TiB, für Seitenblobs eine Größe von ca. 8 TiB und für Azure Files eine Größe von ca. 1 TiB nicht überschreiten.

2. **(Optional, aber empfohlen)** Nach Abschluss des Kopiervorgangs wird dringend empfohlen, zumindest `DataBoxDiskValidation.cmd` im Ordner *DataBoxDiskImport* auszuführen und Option 1 auszuwählen, um die Dateien zu überprüfen. Wenn es die Zeit zulässt, wird darüber hinaus empfohlen, Option 2 zu verwenden, um auch die Prüfsummen zur Überprüfung zu generieren. (Abhängig von der Datengröße kann dieser Vorgang eine Weile dauern.) Diese Schritte minimieren die Wahrscheinlichkeit von Fehlern beim Hochladen der Daten in Azure.
3. Sie müssen das Laufwerk sicher entfernen.

## <a name="ship-to-azure"></a>Senden an Azure

Dieser Schritt dauert ungefähr 5 bis 7 Minuten.

1. Legen Sie alle Datenträger zusammen in das Originalpaket. Verwenden Sie das beigefügte Versandetikett. Falls das Etikett beschädigt oder nicht mehr vorhanden ist, können Sie es über das Portal herunterladen. Wechseln Sie zu **Übersicht** , und klicken Sie in der Befehlsleiste auf **Versandetikett herunterladen**.
2. Geben Sie das sicher verschlossene Paket beim Paketdienst ab.  

Der Data Box-Dienst sendet eine E-Mail-Benachrichtigung und aktualisiert den Auftragsstatus im Azure-Portal.

## <a name="verify-your-data"></a>Überprüfen Ihrer Daten

Der Zeitraum, der für die Durchführung dieses Vorgangs erforderlich ist, hängt von Ihrer Datengröße ab.

1. Wenn der Data Box-Datenträger mit dem Netzwerk des Azure-Rechenzentrums verbunden wird, beginnt der Upload der Daten in Azure automatisch.
2. Sie werden vom Azure Data Box-Dienst über das Azure-Portal benachrichtigt, dass der Kopiervorgang für die Daten abgeschlossen ist.
    
    1. Prüfen Sie die Fehlerprotokolle auf Fehler, und ergreifen Sie ggf. entsprechende Maßnahmen.
    2. Stellen Sie sicher, dass sich Ihre Daten in den Speicherkonten befinden, bevor Sie sie aus der Quelle löschen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Dieser Schritt dauert zwei bis drei Minuten.

Sie können den Data Box-Auftrag stornieren und dann löschen, um die Bereinigung durchzuführen.

- Im Azure-Portal können Sie den Data Box-Auftrag stornieren, bevor er verarbeitet wurde. Nachdem der Auftrag verarbeitet wurde, kann er nicht mehr storniert werden. Der Auftrag wird abgearbeitet, bis er den Status „Abgeschlossen“ erreicht hat.

    Navigieren Sie zum Stornieren des Auftrags zu **Übersicht** , und klicken Sie in der Befehlsleiste auf **Stornieren**.  

- Sie können einen Auftrag löschen, wenn im Azure-Portal dafür der Status **Abgeschlossen** oder **Abgebrochen** angezeigt wird.

    Navigieren Sie zum Löschen des Auftrags zu **Übersicht** , und klicken dann in der Befehlsleiste auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Azure Data Box-Datenträger bereitgestellt, über den Ihre Daten in Azure importiert werden. Fahren Sie mit dem folgenden Tutorial fort, um sich weiter über die Verwaltung von Azure Data Box-Datenträgern zu informieren:

> [!div class="nextstepaction"]
> [Use the Azure portal to administer Data Box Disk](data-box-portal-ui-admin.md) (Verwenden des Azure-Portals zum Verwalten des Data Box-Datenträgers)

::: zone-end
