---
title: Microsoft Azure Stack Edge Pro – Übersicht | Microsoft-Dokumentation
description: Beschreibt Azure Stack Edge Pro, eine Speicherlösung, die ein physisches Gerät für die netzwerkbasierte Übertragung nach Azure verwendet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/09/2020
ms.author: alkohli
ms.openlocfilehash: 51469c23574f55c102f0c6fb98e5aa5178b59a4a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455734"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>Was ist Azure Stack Edge Pro mit FPGA?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Azure Stack Edge Pro mit FPGA ist ein KI-fähiges Edgecomputinggerät mit Netzwerkfunktionen für die Datenübertragung. Dieser Artikel bietet Ihnen einen Überblick über die Azure Stack Edge Pro-Lösung mit FPGA. Dabei werden die Vorteile der Lösung, ihre wichtigsten Funktionen und die Szenarien beschrieben, in denen Sie das Gerät bereitstellen können.

Azure Stack Edge Pro mit FPGA ist eine HaaS-Lösung (Hardware-as-a-Service). Microsoft liefert Ihnen ein cloudseitig verwaltetes Gerät mit einem integrierten FPGA (Field Programmable Gate Array), das beschleunigte KI-Rückschlüsse ermöglicht und alle Funktionen eines Netzwerkspeichergateways bietet. 

## <a name="use-cases"></a>Anwendungsfälle

Dies sind die verschiedenen Szenarien, in denen Azure Stack Edge Pro für schnelle ML-Rückschlüsse (Machine Learning) am Edge sowie für die Vorverarbeitung von Daten verwendet werden kann, bevor diese an Azure gesendet werden.

- **Rückschlüsse mit Azure Machine Learning**: Mit Azure Stack Edge Pro können Sie ML-Modelle ausführen, um schnell Ergebnisse zu erzielen, auf die Sie reagieren können, bevor die Daten in die Cloud übertragen werden. Das gesamte Dataset kann optional übertragen werden, um Ihre ML-Modelle weiter zu trainieren und zu verbessern. Weitere Informationen zur Verwendung der Azure ML-Modelle mit Hardwarebeschleunigung auf dem Azure Stack Edge Pro-Gerät finden Sie unter [Bereitstellen von Azure ML-Modellen mit Hardwarebeschleunigung auf Azure Stack Edge Pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Vorverarbeiten von Daten**: Transformieren Sie Daten vor dem Senden an Azure, um einen aussagekräftigeren Datensatz zu erstellen. Die Vorverarbeitung ermöglicht Folgendes: 

    - Aggregieren von Daten.
    - Ändern von Daten, um beispielsweise persönliche Daten zu entfernen.
    - Teilmengen von Daten zur Optimierung von Speicher und Bandbreite oder für die weitere Analyse.
    - Analysieren von und Reagieren auf IoT-Ereignisse. 

- **Übertragen von Daten über das Netzwerk an Azure**: Verwenden Sie Azure Stack Edge Pro, um Daten einfach und schnell an Azure zu übertragen. Dadurch werden weitere Berechnungen und Analysen oder eine Archivierung ermöglicht. 

## <a name="key-capabilities"></a>Wichtige Funktionen

Azure Stack Edge Pro bietet die folgenden Funktionen:

|Funktion |BESCHREIBUNG  |
|---------|---------|
|Beschleunigte KI-Rückschlüsse| Ermöglicht durch das integrierte FPGA.|
|Computing       |Ermöglicht das Analysieren, Verarbeiten und Filtern von Daten.|
|Hohe Leistung | Hochleistungscompute und -datenübertragungen.|
|Datenzugriff     | Direkter Datenzugriff aus Azure Storage-Blobs und Azure Files über Cloud-APIs zur zusätzlichen Datenverarbeitung in der Cloud. Lokaler Cache auf dem Gerät wird für schnellen Zugriff auf zuletzt verwendete Dateien verwendet.|
|Von der Cloud verwaltet     |Das Gerät und der Dienst werden über das Azure-Portal verwaltet.  |
|Hochladen von Daten im Offlinemodus     | Der Modus „Getrennt“ unterstützt Szenarien für das Hochladen von Daten im Offlinemodus.|
|Unterstützte Protokolle     | Für die Datenerfassung werden die Standardprotokolle SMB und NFS unterstützt. <br> Weitere Informationen zu den unterstützten Versionen finden Sie unter [Systemanforderungen für Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).|
|Datenaktualisierung     | Möglichkeit, lokale Dateien mit den neuesten Informationen aus der Cloud zu aktualisieren.|
|Verschlüsselung    | BitLocker-Unterstützung zur lokalen Verschlüsselung von Daten und zur sicheren Datenübertragung in die Cloud über *HTTPS*.|
|Bandbreiteneinschränkung| Drosselung, um die Bandbreitennutzung während der Spitzenzeiten einzuschränken.|
|ExpressRoute | Sicherheit durch ExpressRoute hinzugefügt. Verwenden Sie die Peeringkonfiguration, wenn Datenverkehr von lokalen Geräten an die Cloudspeicherendpunkte über ExpressRoute geleitet wird. Weitere Informationen finden Sie unter [ExpressRoute-Übersicht](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Komponenten

Die Azure Stack Edge Pro-Lösung umfasst die Azure Stack Edge-Ressource, ein physisches Azure Stack Edge Pro-Gerät und eine lokale Webbenutzeroberfläche.

* **Physisches Azure Stack Edge Pro-Gerät**: ein von Microsoft bereitgestellter 1U-Rackserver, der für das Senden von Daten an Azure konfiguriert werden kann.
    
* **Azure Stack Edge-Ressource**: eine Ressource im Azure-Portal, mit der Sie ein Azure Stack Edge Pro-Gerät über eine zentrale Webbenutzeroberfläche verwalten können, auf die von verschiedenen geografischen Standorten aus zugegriffen werden kann. Verwenden Sie die Azure Stack Edge-Ressource, um Ressourcen zu erstellen und zu verwalten, Geräte und Warnungen anzuzeigen und zu verwalten sowie Freigaben zu verwalten.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Weitere Informationen finden Sie unter [Erstellen eines Auftrags für ein Azure Stack Edge Pro-Gerät](azure-stack-edge-deploy-prep.md#create-a-new-resource).

* **Lokale Webbenutzeroberfläche für Azure Stack Edge Pro**: Mithilfe der lokalen Webbenutzeroberfläche können Sie Diagnosen ausführen, das Azure Stack Edge Pro-Gerät herunterfahren und neu starten, Kopierprotokolle anzeigen und den Microsoft-Support kontaktieren, um eine Serviceanfrage zu stellen.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Informationen zur Verwendung der webbasierten Benutzeroberfläche finden Sie unter [Verwenden der webbasierten Benutzeroberfläche zum Verwalten Ihres Azure Stack Edge Pro-Geräts](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Regionale Verfügbarkeit

Das physische Azure Stack Edge Pro-Gerät, die Azure-Ressource und das Zielspeicherkonto, an das Sie Daten übertragen, müssen sich nicht in derselben Region befinden.

- **Ressourcenverfügbarkeit**: Eine Liste aller Regionen, in denen die Azure Stack Edge-Ressource verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack Edge Pro kann auch in der Azure Government-Cloud bereitgestellt werden. Weitere Informationen finden Sie unter [What is Azure Government?](../azure-government/documentation-government-welcome.md) (Was ist Azure Government?).
    
- **Zielspeicherkonten**: Die Speicherkonten, in denen die Daten gespeichert werden, sind in allen Azure-Regionen verfügbar. Für eine optimale Leistung sollten sich die Regionen, in denen die Speicherkonten Azure Stack Edge Pro-Daten speichern, in der Nähe des Gerätestandorts befinden. Ein weit entfernt vom Gerät befindliches Speicherkonto führt zu langen Wartezeiten und einer langsameren Leistung.

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen Sie die [Systemanforderungen für Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).
- Machen Sie sich mit den [Einschränkungen von Azure Stack Edge Pro](azure-stack-edge-limits.md) vertraut.
- Stellen Sie [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md) im Azure-Portal bereit.