---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178161"
---
Änderungen, die über das Azure-Portal oder den SMB an der Azure-Dateifreigabe vorgenommen wurden, werden im Gegensatz zu Änderungen am Serverendpunkt nicht sofort erkannt und repliziert. Azure Files verfügt bislang über keine Änderungsmitteilungen oder Journalfunktion, sodass es keine Möglichkeit gibt, eine Synchronisierungssitzung automatisch zu initiieren, sobald Dateien geändert werden. Unter Windows Server verwendet die Azure-Dateisynchronisierung das [Windows-USN-Journaling](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx), um automatisch eine Synchronisierungssitzung zu starten, wenn sich Dateien ändern.<br /><br /> Zur Erkennung von Änderungen an der Azure-Dateifreigabe verfügt die Azure-Dateisynchronisierung über einen geplanten Auftrag: den so genannten *Änderungserkennungsauftrag*. Ein Änderungserkennungsauftrag zählt jede Datei in der Dateifreigabe auf und vergleicht sie anschließend mit der Synchronisierungsversion für die betreffende Datei. Wenn der Änderungserkennungsauftrag feststellt, dass sich Dateien geändert haben, startet die Azure-Dateisynchronisierung eine Synchronisierungssitzung. Der Änderungserkennungsauftrag wird alle 24 Stunden ausgelöst. Da der Änderungserkennungsauftrag jede Datei in der Dateifreigabe von Azure aufzählt, dauert die Änderungserkennung bei großen Namespaces länger als bei kleineren Namespaces. Bei großen Namespaces dauert die Ermittlung der geänderten Dateien unter Umständen länger als 24 Stunden.<br /><br />
Hinweis: Änderungen, die mithilfe von REST an einer Azure-Dateifreigabe vorgenommen wurden, bewirken keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen. <br /><br />
Möglicherweise wird für Azure-Dateifreigaben noch eine Änderungserkennung hinzugefügt, die mit USN für Volumes unter Windows Server vergleichbar ist. Helfen Sie uns, dieses Feature für die künftige Entwicklung zu priorisieren, indem Sie auf der [UserVoice-Seite zu Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files) dafür abstimmen.
