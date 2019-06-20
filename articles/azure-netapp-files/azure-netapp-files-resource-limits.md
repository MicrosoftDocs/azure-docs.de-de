---
title: Ressourceneinschränkungen für Azure NetApp Files | Microsoft-Dokumentation
description: Hier werden Grenzwerte für Azure NetApp Files-Ressourcen beschrieben, einschließlich der Grenzwerte für NetApp-Konten, Kapazitätspools, Volumes, Momentaufnahmen und das delegierte Subnetz.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: b55467d77beb8f97b8e392b72682268ae0407e54
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826378"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Ressourcenlimits für Azure NetApp Files

Grundlegende Informationen zu Ressourceneinschränkungen für Azure NetApp Files unterstützen Sie bei der Verwaltung Ihrer Volumes.

## <a name="resource-limits"></a>Ressourceneinschränkungen

In der folgenden Tabelle werden die Ressourcengrenzwerte für Azure NetApp Files beschrieben:

|  Resource  |  Standardlimit  |  Über Supportanfrage anpassbar  |
|----------------|---------------------|--------------------------------------|
|  Anzahl der NetApp-Konten pro Azure-Abonnement   |  10    |  Ja   |
|  Anzahl der Kapazitätspools pro NetApp-Konto   |    25     |   Ja   |
|  Anzahl der Volumes pro Kapazitätspool     |    500   |    Ja     |
|  Anzahl von Momentaufnahmen pro Volume       |    255     |    Nein        |
|  Anzahl der an Azure NetApp Files (Microsoft.NetApp/volumes) delegierten Subnetze pro virtuellem Azure-Netzwerk    |   1   |    Nein    |
|  Maximale Anzahl von VMs (einschließlich virtueller Netzwerke mit Peering), die eine Verbindung mit einem Volume herstellen können     |    1000   |    Nein   |
|  Mindestgröße eines einzelnen Kapazitätspools   |  4 TiB     |    Nein  |
|  Maximale Größe eines einzelnen Kapazitätspools    |  500 TiB   |   Nein   |
|  Mindestgröße eines einzelnen Volumes    |    100 GB    |    Nein    |
|  Maximal zugewiesenes Kontingent eines einzelnen Volumes*   |   92 TiB   |    Nein   |
|  Maximale Größe eines einzelnen Volumes*     |    ca. 100 TiB    |    Nein       |

*Die maximale Größe, mit der ein Volume manuell erstellt bzw. an die ein Volume angepasst werden kann, beträgt 92 TiB. Bei einer Überschreitung kann ein Volume jedoch auf bis zu 100 TiB wachsen. Informationen zur Kapazitätsüberschreitung finden Sie unter [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md). 

## <a name="request-limit-increase"></a>Anfordern einer Erhöhung der Grenzwerte 

Sie können eine Supportanfrage an den Azure-Support stellen, um die anpassbaren Grenzwerte aus der obigen Tabelle zu erhöhen. 

Über den Navigationsbereich im Azure-Portal: 

1. Klicken Sie auf **Hilfe und Support**.
2. Klicken Sie auf **+ Neue Supportanfrage**.
3. Geben Sie die folgenden Informationen auf der Registerkarte „Grundeinstellungen“ an: 
    1. Problemtyp: Wählen Sie **Grenzwerte für Dienste und Abonnements (Kontingente)** aus.
    2. Abonnements: Wählen Sie das Abonnement für die Ressource aus, deren Kontingente erhöht werden sollen.
    3. Kontingenttyp: Wählen Sie **Storage: Azure NetApp Files limits** (Speicher: Azure NetApp Files-Grenzwerte) aus.
    4. Klicken Sie auf **Weiter: Lösungen**.
4. Auf der Registerkarte „Details“:
    1. Geben Sie die folgenden Informationen für den entsprechenden Ressourcentyp im Feld „Beschreibung“ an:

        |  Resource  |    Übergeordnete Ressourcen      |    Angeforderte neue Grenzwerte     |    Grund für die Kontingenterhöhung       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Abonnement-ID*   |  *Angeforderte neue maximale Anzahl an **Konten***    |  *Szenario oder Anwendungsfall, das zur Anforderung geführt hat*  |
        |  Pool    |  *Abonnement-ID, Konto-URI*  |  *Angeforderte neue maximale Anzahl an **Pools***   |  *Szenario oder Anwendungsfall, das zur Anforderung geführt hat*  |
        |  Volume  |  *Abonnement-ID, Konto-URI, Pool-URI*   |  *Angeforderte neue maximale Anzahl an **Volumes***     |  *Szenario oder Anwendungsfall, das zur Anforderung geführt hat*  |

    2. Legen Sie die entsprechende Supportmethode fest, und geben Sie Ihre Kontaktinformationen an.

    3. Klicken Sie auf **Weiter: Überprüfen + erstellen**, um die Anforderung zu erstellen. 


## <a name="next-steps"></a>Nächste Schritte  

- [Grundlegendes zur Speicherhierarchie von Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md)
