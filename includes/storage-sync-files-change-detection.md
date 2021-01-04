---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 1387933dc82c07e73b7715d6593238ea8c993e93
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005317"
---
Änderungen, die über das Azure-Portal oder den SMB an der Azure-Dateifreigabe vorgenommen wurden, werden im Gegensatz zu Änderungen am Serverendpunkt nicht sofort erkannt und repliziert. Azure Files verfügt bislang über keine Änderungsmitteilungen oder Journalfunktion, sodass es keine Möglichkeit gibt, eine Synchronisierungssitzung automatisch zu initiieren, sobald Dateien geändert werden. Unter Windows Server verwendet die Azure-Dateisynchronisierung das [Windows-USN-Journaling](/windows/win32/fileio/change-journals), um automatisch eine Synchronisierungssitzung zu starten, wenn sich Dateien ändern.

Zur Erkennung von Änderungen an der Azure-Dateifreigabe verfügt die Azure-Dateisynchronisierung über einen geplanten Auftrag: den so genannten *Änderungserkennungsauftrag*. Ein Änderungserkennungsauftrag zählt jede Datei in der Dateifreigabe auf und vergleicht sie anschließend mit der Synchronisierungsversion für die betreffende Datei. Wenn der Änderungserkennungsauftrag feststellt, dass sich Dateien geändert haben, startet die Azure-Dateisynchronisierung eine Synchronisierungssitzung. Der Änderungserkennungsauftrag wird alle 24 Stunden ausgelöst. Da der Änderungserkennungsauftrag jede Datei in der Dateifreigabe von Azure aufzählt, dauert die Änderungserkennung bei großen Namespaces länger als bei kleineren Namespaces. Bei großen Namespaces dauert die Ermittlung der geänderten Dateien unter Umständen länger als 24 Stunden.

Um Dateien, die in der Azure-Dateifreigabe geändert wurden, sofort zu synchronisieren, kann das PowerShell-Cmdlet **Invoke-AzStorageSyncChangeDetection** verwendet werden, um die Erkennung von Änderungen in der Azure-Dateifreigabe manuell auszulösen. Dieses Cmdlet ist für Szenarien vorgesehen, in denen irgendein automatisierter Prozess Änderungen in der Azure-Dateifreigabe vornimmt oder die Änderungen von einem Administrator vorgenommen werden (etwa Verschieben von Dateien und Verzeichnissen in die Freigabe). Für Endbenutzeränderungen empfiehlt es sich, den Azure-Dateisynchronisierungs-Agent auf einem virtuellen IaaS-Computer zu installieren und Endbenutzern den Zugriff auf die Dateifreigabe über den virtuellen IaaS-Computer zu ermöglichen. Auf diese Weise werden alle Änderungen schnell mit anderen Agents synchronisiert, ohne dass das Cmdlet „Invoke-AzStorageSyncChangeDetection“ verwendet werden muss. Weitere Informationen hierzu finden Sie in der Dokumentation von [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).

>[!NOTE]
>Änderungen, die mithilfe von REST an einer Azure-Dateifreigabe vorgenommen wurden, bewirken keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen.

Möglicherweise wird für Azure-Dateifreigaben noch eine Änderungserkennung hinzugefügt, die mit USN für Volumes unter Windows Server vergleichbar ist. Helfen Sie uns, dieses Feature für die künftige Entwicklung zu priorisieren, indem Sie auf der [UserVoice-Seite zu Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files) dafür abstimmen.