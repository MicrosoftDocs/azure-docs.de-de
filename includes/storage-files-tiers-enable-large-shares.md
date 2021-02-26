---
title: include file
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569540"
---
Standardmäßig können Standarddateifreigaben nur bis zu 5 TiB umfassen. Der Freigabegrenzwert kann jedoch auf 100 TiB erhöht werden. Aktivieren Sie zum Erhöhen des Freigabegrenzwerts in Ihrem Speicherkonto die Option **Große Dateifreigabe**. Storage Premium-Konten (*FileStorage*-Speicherkonten) verfügen nicht über das Featureflag „große Dateifreigabe“, da alle Premium-Dateifreigaben bereits für die Bereitstellung bis zur gesamten 100-TiB-Kapazität aktiviert sind.

Sie können nur große Dateifreigaben für lokal redundante oder zonenredundante Standardspeicherkonten aktivieren. Sobald Sie das Featureflag für große Dateifreigaben aktiviert haben, können Sie die Redundanzebene nicht mehr in georedundanten oder geozonenredundanten Speicher ändern.

Um große Dateifreigaben für ein vorhandenes Speicherkonto zu aktivieren, navigieren Sie im Inhaltsverzeichnis des Speicherkontos zu **Dateifreigaben**.
Wählen Sie auf diesem Blatt **Freigabekapazität** aus, ändern Sie die Freigabekapazität in **100 TiB**, und wählen Sie **Speichern** aus.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Screenshot: Aktivieren der Abonnementeinstellung für große Dateifreigabe im Azure-Portal" lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

Sie können 100-TiB-Dateifreigaben auch über das [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount)-PowerShell-Cmdlet und den Azure CLI-Befehl [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) aktivieren. Ausführliche Anweisungen zum Aktivieren großer Dateifreigaben finden Sie unter[Aktivieren und Erstellen großer Dateifreigaben](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Weitere Informationen zum Erstellen großer Dateifreigaben in neuen Speicherkonten finden Sie unter [Erstellen einer Azure-Dateifreigabe](../articles/storage/files/storage-how-to-create-file-share.md).