---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 7b46360886387bc4ab48f764edb5d01dc52dc812
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244656"
---
Dies sind die Grenzwerte für die Größe der Daten, die in das Speicherkonto kopiert werden. Stellen Sie sicher, dass die von Ihnen hochgeladenen Daten diesen Grenzwerten entsprechen. Die neuesten Informationen zu diesen Einschränkungen finden Sie unter [Skalierbarkeitsziele für Azure-Blobspeicher](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) und [Skalierbarkeitsziele für Azure Files](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Größe der in das Azure-Speicherkonto kopierten Daten                      | Standardlimit          |
|---------------------------------------------------------------------|------------------------|
| Blockblob und Seitenblob                                            | 500 TiB pro Speicherkonto. <br> Dies schließt Daten aus allen Quellen einschließlich Data Box ein.|
| Azure File                                                          | 5 TiB pro Freigabe.<br> Alle Ordner unter *StorageAccount_AzureFiles* müssen diese Beschränkung einhalten.       |
