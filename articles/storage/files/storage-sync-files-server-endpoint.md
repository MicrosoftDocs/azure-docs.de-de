---
title: Hinzufügen/Entfernen eines Serverendpunkts für die Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der Azure-Dateisynchronisierung Serverendpunkte hinzufügen oder entfernen. Ein Serverendpunkt ist ein bestimmter Speicherort auf einem registrierten Server, z. B. ein Ordner auf einem Servervolume.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f75f0d1ae12db11590f8ce62f3c7b4c0f3e12817
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013937"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Hinzufügen/Entfernen eines Azure-Dateisynchronisierungsserver-Endpunkts
Mit der Azure-Dateisynchronisierung können Sie Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Dies erfolgt durch Umwandeln der Windows-Server in einen Schnellcache der Azure-Dateifreigabe. Sie können alle unter Windows Server verfügbaren Protokolle für den lokalen Zugriff auf Ihre Daten (einschließlich SMB, NFS und FTPS) sowie beliebig viele Caches weltweit verwenden.

Ein *Serverendpunkt* stellt einen bestimmten Speicherort auf einem *registrierten Server* dar, z.B. einen Ordner auf einem Servervolume oder das Stammverzeichnis des Volumes. Mehrere Serverendpunkte können auf dem gleichen Volume vorhanden sein, wenn sich deren Namespaces nicht überschneiden (z. B. „F:\sync1“ und „F:\sync2“) und jeder Endpunkt mit einer eindeutigen Synchronisierungsgruppe synchronisiert wird. Sie können Richtlinien für das Cloudtiering für jeden Serverendpunkt separat konfigurieren. Wenn Sie einen Serverspeicherort mit einem vorhandenen Satz von Dateien einer Synchronisierungsgruppe als Serverendpunkt hinzufügen, werden diese Dateien mit anderen Dateien, die sich bereits auf anderen Endpunkten in der Synchronisierungsgruppe befinden, zusammengeführt.

Informationen zur End-to-End-Bereitstellung der Azure-Dateisynchronisierung finden Sie unter [Bereitstellen der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-deployment-guide.md).

## <a name="prerequisites"></a>Voraussetzungen
Zum Erstellen eines Serverendpunkts müssen Sie zunächst sicherstellen, dass die folgenden Kriterien erfüllt sind: 
- Auf dem Server ist der Azure-Dateisynchronisierungs-Agent installiert und registriert. Anweisungen zum Installieren des Azure-Dateisynchronisierungs-Agents finden Sie im Artikel [Verwalten registrierter Server mit der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-server-registration.md). 
- Stellen Sie sicher, dass der Speichersynchronisierungsdienst bereitgestellt wurde. Ausführliche Informationen zum Bereitstellen eines Speichersynchronisierungsdiensts finden Sie unter [Bereitstellen der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-deployment-guide.md). 
- Stellen Sie sicher, dass eine Synchronisierungsgruppe bereitgestellt wurde. Erfahren Sie, wie Sie eine [Synchronisierungsgruppe erstellen](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Stellen Sie sicher, dass der Server mit dem Internet verbunden ist und dass der Zugriff auf Azure möglich ist. Wir verwenden Port 443 für die gesamte Kommunikation zwischen dem Server und unserem Dienst.

## <a name="add-a-server-endpoint"></a>Hinzufügen eines Serverendpunkts
Um einen Serverendpunkt hinzuzufügen, navigieren Sie zu der gewünschten Synchronisierungsgruppe, und wählen Sie „Serverendpunkt hinzufügen“ aus.

![Hinzufügen eines neuen Serverendpunkts im Bereich „Synchronisierungsgruppe“](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

Unter **Serverendpunkt hinzufügen** sind die folgenden Informationen erforderlich:

- **Registrierter Server:** Der Name des Servers oder Clusters, auf bzw. in dem der Serverendpunkt erstellt werden soll.
- **Pfad:** Der Pfad auf dem Windows-Server, der als Teil der Synchronisierungsgruppe synchronisiert werden soll.
- **Cloudtiering**: Ein Schalter, mit dem Cloudtiering aktiviert oder deaktiviert wird. Wenn das Cloudtiering aktiviert ist, werden Dateien in Ihren Azure-Dateifreigaben *auf mehrere Speicherebenen aufgeteilt (Tiering)* . Hierbei werden lokale Dateifreigaben in einen Cache statt in eine vollständige Kopie des Datasets konvertiert, damit Sie die Speicherplatzeffizienz auf dem Server verwalten können.
- **Freier Speicherplatz auf Volume:** Die Menge des freien Speicherplatzes auf dem Volume, auf dem sich der Serverendpunkt befindet. Wenn z.B. für ein Volume mit einem einzigen Serverendpunkt „Freier Speicherplatz auf Volume“ auf 50 % festgelegt ist, wird ungefähr die Hälfte der Daten in Azure Files ausgelagert. Die Azure-Dateifreigabe enthält immer eine vollständige Kopie der Daten in der Synchronisierungsgruppe, unabhängig davon, ob Cloudtiering aktiviert ist.

Wählen Sie **Erstellen** aus, um den Serverendpunkt hinzuzufügen. Die Dateien im Namespace einer Synchronisierungsgruppe bleiben synchron. 

## <a name="remove-a-server-endpoint"></a>Entfernen eines Serverendpunkts
Wenn Sie die Azure-Dateisynchronisierung für einen bestimmten Serverendpunkt nicht mehr verwenden möchten, können Sie den Serverendpunkt entfernen. 

> [!Warning]  
> Versuchen Sie nicht, Probleme mit der Synchronisierung, dem Cloudtiering oder anderen Aspekten der Azure-Dateisynchronisierung zu behandeln, indem Sie den Serverendpunkt entfernen und neu erstellen, es sei denn, Sie werden ausdrücklich von einem Microsoft-Techniker dazu aufgefordert. Das Entfernen eines Serverendpunkts ist ein destruktiver Vorgang. Mehrstufige Dateien auf dem Serverendpunkt werden nicht erneut mit ihren Speicherorten in der Azure-Dateifreigabe verbunden, nachdem der Serverendpunkt neu erstellt wurde. Dies führt zu Synchronisierungsfehlern. Beachten Sie, dass mehrstufige Dateien, die sich außerhalb des Endpunktnamespaces befinden, dauerhaft verloren gehen können. Auf Ihrem Serverendpunkt sind möglicherweise auch dann mehrstufige Dateien vorhanden, wenn das Cloudtiering nie aktiviert wurde.

Um sicherzustellen, dass alle mehrstufigen Dateien abgerufen werden, bevor der Serverendpunkt entfernt wird, deaktivieren Sie das Cloudtiering auf dem Serverendpunkt, und führen Sie dann das folgende PowerShell-Cmdlet aus, um alle mehrstufigen Dateien innerhalb des Namespaces des Serverendpunkts abzurufen:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Wenn Sie `-Order CloudTieringPolicy` angeben, werden zuerst die zuletzt geänderten Dateien abgerufen.
Weitere optionale, aber nützliche Parameter, die zu berücksichtigen sind:
* `-ThreadCount` bestimmt, wie viele Dateien parallel abgerufen werden können.
* `-PerFileRetryCount` bestimmt, wie oft ein Abruf einer Datei versucht wird, die derzeit blockiert ist.
* `-PerFileRetryDelaySeconds` bestimmt die Zeit in Sekunden zwischen den Versuchen zum erneuten Abrufen und sollte immer in Kombination mit dem vorherigen Parameter verwendet werden.

> [!Note]  
> Wenn das lokale Volume, das den Server hostet, nicht genügend freien Speicherplatz aufweist, um alle ausgelagerten Daten abzurufen, schlägt das Cmdlet `Invoke-StorageSyncFileRecall` fehl.  

So entfernen Sie den Serverendpunkt

1. Navigieren Sie zu dem Speichersynchronisierungsdienst, bei dem der Server registriert ist.
2. Navigieren Sie zur gewünschten Synchronisierungsgruppe.
3. Entfernen Sie in der Synchronisierungsgruppe im Speichersynchronisierungsdienst den gewünschten Serverendpunkt. Klicken Sie hierfür im Bereich „Synchronisierungsgruppe“ mit der rechten Maustaste auf den entsprechenden Serverendpunkt.

    ![Entfernen eines Serverendpunkts aus einer Synchronisierungsgruppe](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Nächste Schritte
- [Registrieren/Aufheben der Registrierung eines Servers bei der Azure-Dateisynchronisierung](storage-sync-files-server-registration.md)
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Überwachen der Azure-Dateisynchronisierung](storage-sync-files-monitoring.md)
