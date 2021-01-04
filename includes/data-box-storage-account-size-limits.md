---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 2868e5a53686cfa94dc206c1aab65fe866d19b6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026816"
---
Dies sind die Grenzwerte für die Größe der Daten, die in das Speicherkonto kopiert werden. Stellen Sie sicher, dass die von Ihnen hochgeladenen Daten diesen Grenzwerten entsprechen. Aktuelle Informationen zu diesen Grenzwerten finden Sie unter [Skalierbarkeits- und Leistungsziele für Blob Storage](../articles/storage/blobs/scalability-targets.md) und [Skalierbarkeits- und Leistungsziele für Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Größe der in das Azure-Speicherkonto kopierten Daten                      | Standardlimit          |
|---------------------------------------------------------------------|------------------------|
| Blockblob und Seitenblob                                            | Der maximale Grenzwert ist identisch mit dem [Speicherlimit, das für das Azure-Abonnement definiert ist](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), und enthält Daten aus allen Quellen(einschließlich Data Box). |
| Azure Files                                                          | Data Box unterstützt große Dateifreigaben (100 TiB), wenn dies vor der Erstellung des Data Box-Auftrags aktiviert wurde. <br> Wenn diese Option nicht vor der Auftragserstellung nicht aktiviert wurde, werden Dateifreigaben von maximal 5 TiB unterstützt. <br> Premium-Dateifreigaben werden noch nicht unterstützt.<br> Alle Ordner unter *StorageAccount_AzureFiles* müssen diese Beschränkung einhalten. <br> Weitere Informationen finden Sie unter [Aktivieren und Konfigurieren von FILESTREAM](../articles/storage/files/storage-files-how-to-create-large-file-share.md).      |