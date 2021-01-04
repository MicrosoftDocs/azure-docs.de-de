---
title: Vorbereitung auf den Ausfall eines Azure Stack Edge Pro-Geräts
description: Hier wird beschrieben, wie Sie Ersatz für ein ausgefallenes Azure Stack Edge Pro-Gerät erhalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 986a3c56a1e0dcc79ab472a7e18d7eeb7e2fddb5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448349"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Vorbereitung auf den Ausfall eines Azure Stack Edge Pro-GPU-Geräts

Dieser Artikel hilft Ihnen bei der Vorbereitung auf einen Geräteausfall und erläutert, wie die Gerätekonfiguration und Daten auf Ihrem Azure Stack Edge Pro-GPU-Gerät gespeichert und gesichert werden. 

Der Artikel enthält keine Schritte zum Sichern von Kubernetes- und IoT-Containern, die auf Ihrem Azure Stack Edge Pro-GPU-Gerät bereitgestellt werden. 

## <a name="understand-device-failures"></a>Grundlegendes zu Geräteausfällen

Bei Ihrem Azure Stack Edge Pro-GPU-Gerät können zwei Arten von Hardwareausfällen auftreten.

- Tolerierbare Ausfälle, bei denen Sie eine Hardwarekomponente austauschen müssen. Bei diesen Ausfällen können Sie das Gerät in beeinträchtigtem Zustand betreiben. Beispiele für solche Ausfälle sind eine einzelne ausgefallene Stromversorgung oder ein einzelner ausgefallener Datenträger auf dem Gerät. In jedem dieser Fälle kann das Gerät weiterhin betrieben werden. Es wird empfohlen, den Microsoft-Support schnellstmöglich zu kontaktieren, damit die ausgefallenen Komponenten ausgetauscht werden.

- Nicht tolerierbare Ausfälle, bei denen das gesamte Gerät ausgetauscht werden muss. Ein Beispiel dafür wäre der Ausfall von zwei Datenträgern auf dem Gerät. In diesen Fällen wenden Sie sich an den Microsoft-Support, und nachdem festgestellt wurde, dass ein Geräteaustausch erforderlich ist, wird Ihnen beim Austausch Ihres Azure Stack Edge-Geräts geholfen.

Zur Vorbereitung auf nicht tolerierbare Ausfälle müssen Sie Folgendes auf Ihrem Gerät sichern:

- Informationen zur Gerätekonfiguration
- Daten, die sich in lokalen Edge-Freigaben und Edge-Cloudfreigaben befinden
- Dateien und Ordner, die den auf Ihrem Gerät ausgeführten VMs zugeordnet sind


## <a name="back-up-device-configuration"></a>Sichern der Gerätekonfiguration

Während der Erstkonfiguration des Geräts ist es wichtig, dass Sie eine Kopie der Informationen zur Gerätekonfiguration aufbewahren, wie es in der [Bereitstellungsprüfliste](azure-stack-edge-gpu-deploy-checklist.md) beschrieben ist. Während der Wiederherstellung werden diese Konfigurationsinformationen auf das neue Ersatzgerät angewendet. 

## <a name="protect-device-data"></a>Schützen von Gerätedaten

Bei den Gerätedaten kann es sich um einen der folgenden Typen handeln:

- Daten in Edge-Cloudfreigaben
- Daten in lokalen Freigaben
- Dateien und Ordner auf VMs

In den folgenden Abschnitten werden die Schritte und Empfehlungen zum Schutz der einzelnen Datentypen auf Ihrem Gerät erläutert.

## <a name="protect-data-in-edge-cloud-shares"></a>Schützen von Daten in Edge-Cloudfreigaben

Sie können Edge-Cloudfreigaben erstellen, mit denen Daten von Ihrem Gerät in Azure ausgelagert werden. Konfigurieren Sie abhängig von der verfügbaren Netzwerkbandbreite Bandbreitenvorlagen auf Ihrem Gerät, um Datenverluste im Fall eines nicht tolerierbaren Ausfalls zu minimieren.

> [!IMPORTANT] 
> Wenn bei dem Gerät ein nicht tolerierbarer Ausfall auftritt, können lokale Daten, die nicht vom Gerät nach Azure verschoben wurden, verloren gehen. 

## <a name="protect-data-in-edge-local-shares"></a>Schützen von Daten in lokalen Edge-Freigaben

Wenn Sie Kubernetes oder IoT Edge bereitstellen, nehmen Sie die Konfiguration so vor, dass die Anwendungsdaten lokal auf dem Gerät gespeichert und nicht mit Azure Storage synchronisiert werden. Die Daten werden in einer Freigabe auf dem Gerät gespeichert. Möglicherweise ist es wichtig, die Daten in diesen Freigaben zu sichern.

Die folgenden Datenschutzlösungen von Drittanbietern können als Sicherungslösung für die Daten in lokalen SMB- oder NFS-Freigaben dienen. 

| Drittanbietersoftware           | Verweis auf die Lösung                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | https://www.cohesity.com/solution/cloud/azure/ <br> Weitere Informationen erhalten Sie bei Cohesity.          |
| Commvault                      | https://www.commvault.com/azure <br> Wenden Sie sich für weitere Informationen direkt an Commvault.          |
| Veritas                        | http://veritas.com/azure <br> Weitere Informationen erhalten Sie bei Veritas.   |


## <a name="protect-files-and-folders-on-vms"></a>Schützen von Dateien und Ordnern auf VMs

Azure Stack Edge arbeitet mit Azure Backup und anderen Datenschutzlösungen von Drittanbietern, um eine Sicherungslösung zum Schutz von Daten in den auf dem Gerät bereitgestellten VMs bereitzustellen. In der folgenden Tabelle sind Verweise auf verfügbare Lösungen aufgeführt, aus denen Sie eine Auswahl treffen können.


| Sicherungslösungen        | Unterstütztes Betriebssystem   | Referenz                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| MARS-Agent (Microsoft Azure Recovery Services) für Azure Backup | Windows        | [Informationen zum MARS-Agent](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Lösungsübersicht zur Microsoft Azure-Integration, Sicherung und Wiederherstellung](https://www.cohesity.com/solution/cloud/azure) <br>Weitere Informationen erhalten Sie bei Cohesity.                          |
| Commvault               | Windows, Linux | https://www.commvault.com/azure <br>Wenden Sie sich für weitere Informationen direkt an Commvault.                          |
| Veritas                 | Windows, Linux | http://veritas.com/azure <br> Weitere Informationen erhalten Sie bei Veritas.                    |



## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Wiederherstellung nach Ausfall eines Azure Stack Edge Pro-GPU-Geräts](azure-stack-edge-gpu-recover-device-failure.md).