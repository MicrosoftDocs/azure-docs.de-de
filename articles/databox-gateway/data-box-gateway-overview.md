---
title: Microsoft Azure Data Box Gateway – Übersicht | Microsoft-Dokumentation
description: In diesem Artikel wird Azure Data Box Gateway beschrieben, eine Speicherlösung mit einem virtuellen Gerät, die zum Übertragen von Daten in Azure dient.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: aa68b746ef5c22c97fcfdcca889bf71577c19479
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96583210"
---
# <a name="what-is-azure-data-box-gateway"></a>Was ist Azure Data Box Gateway?

Azure Data Box Gateway ist eine Speicherlösung, die zum nahtlosen Übertragen von Daten in Azure dient. Dieser Artikel bietet Ihnen einen Überblick über die Azure Data Box Gateway-Lösung, ihre Vorteile, ihre wichtigsten Funktionen und die Szenarien, in denen Sie dieses Gerät bereitstellen können.

Data Box Gateway ist ein auf einem virtuellen Computer basierendes virtuelles Gerät, das in Ihrer virtualisierten Umgebung oder im Hypervisor bereitgestellt wird. Das virtuelle Gerät befindet sich an Ihrem Standort, und Sie beschreiben es mit den Protokollen NFS und SMB mit Daten. Das Gerät überträgt dann Ihre Daten in ein Azure-Blockblob, -Seitenblob oder Azure Files.

## <a name="use-cases"></a>Anwendungsfälle

Data Box Gateway kann für die Übertragung von Daten in die Cloud genutzt werden, z.B. zur Archivierung in der Cloud, Notfallwiederherstellung oder bei Bedarf zur Verarbeitung Ihrer Daten in der Cloud. Es folgen die verschiedenen Szenarien, in denen Data Box Gateway für die Datenübertragung verwendet werden kann.

- **Archivierung in der Cloud**: Kopieren Sie mit Data Box Gateway Hunderte von TBs an Daten auf sichere und effiziente Weise in Azure Storage. Zur Archivierung können die Daten einmalig oder fortlaufend erfasst werden.

- **Kontinuierliche Datenerfassung**: Erfassen Sie kontinuierlich Daten auf dem Gerät, um sie in die Cloud zu kopieren (unabhängig von der Datengröße). Während des Schreibens der Daten auf dem Gatewaygerät lädt das Gerät die Daten in Azure Storage hoch.  

- **Anfängliche Massenübertragung gefolgt von inkrementellen Übertragungen**: Verwenden Sie Data Box für die Massenübertragung im Offlinemodus (Startseed) und Data Box Gateway für inkrementelle Übertragungen (kontinuierlicher Feed) über das Netzwerk.

Weitere Informationen finden Sie unter [Azure Data Box Gateway – Anwendungsfälle](data-box-gateway-use-cases.md).

## <a name="benefits"></a>Vorteile

Data Box Gateway hat die folgenden Vorteile:

- **Einfache Datenübertragung**: Macht das Verschieben von Daten in und aus Azure Storage so einfach wie das Arbeiten mit einer lokalen Netzwerkfreigabe.  
- **Hohe Leistung**: Erleichtert mit leistungsstarken Übertragungsfunktionen den Transport von Netzwerkdaten aus und in Azure.
- **Schneller Zugriff und hohe Datenerfassungsrate während der Geschäftszeiten**: Data Box Gateway verfügt über einen lokalen Cache, den Sie bei der Bereitstellung des virtuellen Geräts als lokale Kapazitätsgröße definieren. Die Datenträgergröße muss unter Berücksichtigung der [Mindestanforderungen des virtuellen Geräts](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device) angegeben werden. Der lokale Cache hat folgende Vorteile:
    - Der lokale Cache ermöglicht eine hohe Datenerfassungsrate. Wenn zu Spitzenzeiten große Datenmengen erfasst werden, kann der Cache die Daten aufnehmen und in die Cloud hochladen.
    - Der lokale Cache ermöglicht schnellen Lesezugriff bis zu einem bestimmten Schwellenwert. Bis das Gerät zu 50–60 Prozent voll ist, werden alle Lesevorgänge für das Gerät über den Cache abgewickelt, um sie zu beschleunigen. Übersteigt die Speicherplatznutzung auf dem Gerät diesen Schwellenwert, werden lokale Dateien auf dem Gerät entfernt.
 
- **Eingeschränkte Bandbreitenauslastung**: Daten können in Azure geschrieben werden, auch wenn das Netzwerk gedrosselt ist, um die Nutzung während der Hauptgeschäftszeiten einzuschränken.  

## <a name="key-capabilities"></a>Wichtige Funktionen

Data Box Gateway bietet die folgenden Funktionen:

|Funktion |BESCHREIBUNG  |
|---------|---------|
|Geschwindigkeit     | Vollständig automatisierte und stark optimierte Datenübertragung und Bandbreite.|
|Unterstützte Protokolle     | Für die Datenerfassung werden die Standardprotokolle SMB und NFS unterstützt. <br> Weitere Informationen zu unterstützten Versionen finden Sie unter [Systemanforderungen für Data Box Gateway](data-box-gateway-system-requirements.md).|
|Datenzugriff     | Sobald sich die vom Gerät gesendeten Daten in der Cloud befinden, ist eine weitere Anpassung mittels Direktzugriff auf die Cloud-APIs möglich.|
|Schneller Zugriff     | Lokaler Cache auf dem Gerät für schnellen Zugriff auf zuletzt verwendete Dateien.|
|Hochladen von Daten im Offlinemodus     | Der Modus „Getrennt“ unterstützt Szenarien für das Hochladen von Daten im Offlinemodus.|
|Datenaktualisierung     | Möglichkeit, lokale Dateien mit den neuesten Informationen aus der Cloud zu aktualisieren.|
|Resilienz     | Integrierte Netzwerkresilienz.        |


## <a name="specifications"></a>Spezifikationen

Das virtuelle Data Box Gateway-Gerät weist die folgenden Merkmale auf:

| Spezifikationen                                          | BESCHREIBUNG              |
|---------------------------------------------------------|--------------------------|
| Virtuelle Prozessoren (Kerne)   | Mindestens 4 |
| Arbeitsspeicher  |Mindestens 8 GB|
| Verfügbarkeit|Einzelner Knoten|
| Datenträger|Betriebssystemdatenträger: 250 GB <br> Datenträger für Daten: Mindestens 2 TB, für schlanke Speicherzuweisung geeignet, muss durch SSD-Datenträger unterstützt werden|
| Netzwerkschnittstellen |Mindestens eine virtuelle Netzwerkschnittstelle|
| Native Dateifreigabeprotokolle|SMB und NFS  |
| Sicherheit|Authentifizierung, um den Zugriff auf Gerät und Daten zu entsperren <br> Verschlüsselung der Daten während der Übertragung mithilfe von AES-256-BitLocker-Verschlüsselung|
| Verwaltung|Lokale Webbenutzeroberfläche: Ersteinrichtung, Diagnose und Energieverwaltung für das Gerät <br> Azure-Portal: Für die sonstige Verwaltung von Data Box Gateway-Geräten       |

## <a name="components"></a>Komponenten

Die Data Box Gateway-Lösung umfasst die Data Box Gateway-Ressource, ein virtuelles Data Box Gateway-Gerät und eine lokale Webbenutzeroberfläche.

- **Virtuelles Data Box Gateway-Gerät**: Ein auf einem virtuellen Computer basierendes virtuelles Gerät, das in Ihrer virtualisierten Umgebung oder im Hypervisor bereitgestellt wird und die Übertragung von Daten in Azure ermöglicht.
    
- **Data Box Gateway-Ressource:** Eine Ressource im Azure-Portal, mit der Sie ein Data Box Gateway-Gerät auf einer zentralen Webbenutzeroberfläche verwalten können, auf die an verschiedenen geografischen Standorten zugegriffen werden kann. Verwenden Sie die Data Box Gateway-Ressource zum Anzeigen und Verwalten des Geräts sowie zum Anzeigen und Verwalten von Freigaben, Benutzern und Warnungen. Weitere Informationen finden Sie unter [Verwalten von Freigaben auf Ihrer Azure Data Box Gateway-Ressource über das Azure-Portal](data-box-gateway-manage-shares.md).

- **Lokale Webbenutzeroberfläche für Data Box**: Auf der lokalen Webbenutzeroberfläche können Sie Diagnosen ausführen, das Gerät herunterfahren und neu starten, ein Supportpaket generieren sowie den Microsoft-Support kontaktieren, um eine Serviceanfrage zu stellen. Weitere Informationen finden Sie unter [Verwalten des Zugriffs, der Energieeinstellungen und des Konnektivitätsmodus für Azure Data Box Gateway (Vorschau)](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Regionale Verfügbarkeit

Das physische Data Box Gateway-Gerät, die Azure-Ressource und das Zielspeicherkonto, an das Sie Daten übertragen, müssen sich nicht alle in der gleichen Region befinden.

- **Ressourcenverfügbarkeit** – Eine Liste aller Regionen, in denen die Azure Data Box Gateway-Ressource verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox). Data Box Gateway kann auch in der Azure Government-Cloud bereitgestellt werden. Weitere Informationen finden Sie unter [What is Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) (Was ist Azure Government?).

- **Zielspeicherkonten**: Die Speicherkonten, in denen die Daten gespeichert werden, sind in allen Azure-Regionen verfügbar.

    Die Regionen, in denen die Speicherkonten Data Box-Daten speichern, sollten sich in der Nähe des Standorts des Geräts befinden, um eine optimale Leistung zu erzielen. Ein weit entfernt vom Gerät befindliches Speicherkonto führt zu langen Wartezeiten und einer langsameren Leistung.


## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Data Box Gateway-Systemanforderungen](data-box-gateway-system-requirements.md) an.
- Machen Sie sich mit den [Data Box Gateway-Einschränkungen](data-box-gateway-limits.md) vertraut.
- Stellen Sie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) im Azure-Portal bereit.

