---
title: Anzeigen des Integritätsstatus der Azure NetApp Files-Replikationsbeziehung | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie den Replikationsstatus auf dem Quellvolume oder dem Zielvolume von Azure NetApp Files anzeigen.
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
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: a14656bc1abc42fdc3e948fdae1ca07469681d21
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249851"
---
# <a name="display-health-status-of-replication-relationship"></a>Anzeigen des Integritätsstatus der Replikationsbeziehung 

Sie können den Replikationsstatus auf dem Quellvolume oder dem Zielvolume anzeigen.  

## <a name="display-replication-status"></a>Anzeigen des Replikationsstatus

1. Klicken Sie entweder auf dem Quell- oder dem Zielvolume unter „Speicherdienst“ für das jeweilige Volume auf **Replikation**.

    Die folgenden Replikationsstatus- und Integritätsinformationen werden angezeigt:  
    * **Endpunkttyp**: Gibt an, ob das Volume die Quelle oder das Ziel der Replikation ist.
    * **Integrität**: Zeigt den Integritätsstatus der Replikationsbeziehung an.
    * **Spiegelungszustand**: Zeigt einen der folgenden Werte an:
        * *Nicht initialisiert*:  
            Dies ist der anfängliche und der Standardzustand, wenn eine Peeringbeziehung erstellt wird. Der Zustand bleibt „Nicht initialisiert“, bis die Initialisierung erfolgreich abgeschlossen wurde.
        * *Gespiegelt*:   
            Das Zielvolume wurde initialisiert und ist für den Empfang von Spiegelungsaktualisierungen bereit.
        * *Unterbrochen*:   
            Dies ist der Zustand nachdem Sie die Peeringbeziehung unterbrochen haben. Das Ziel Volume ist `‘RW’`, und Momentaufnahmen sind vorhanden.
    * **Beziehungsstatus**: Zeigt einen der folgenden Werte an: 
        * *Idle*:  
            Es wird zurzeit kein Übertragungsvorgang ausgeführt, und zukünftige Übertragungen sind nicht deaktiviert.
        * *Übertragung wird ausgeführt*:  
            Ein Übertragungsvorgang wird ausgeführt, und zukünftige Übertragungen sind nicht deaktiviert.
    * **Replikationszeitplan**: Zeigt an, wie häufig inkrementelle Spiegelungsaktualisierungen ausgeführt werden, wenn die Initialisierung (Baselinekopie) abgeschlossen ist.

    * **Fortschritt gesamt**: Zeigt Die Gesamtmenge der mit dem aktuellen Übertragungsvorgang übertragenen Daten in Bytes an. Diese Menge sind die tatsächlich übertragenen Bits, und der Wert kann sich von dem logischen Bereich unterscheiden, der von den Quell- und Zielvolumes gemeldet wird.  

    ![Replikationsintegritätsstatus](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> Der Integritätsstatus der Replikationsbeziehung wird als *Fehlerhaft* angezeigt, wenn frühere Replikationsaufträge nicht abgeschlossen wurden. Dieser Status ist ein Ergebnis davon, dass große Volumes mit einem niedrigeren Übertragungsfenster übertragen werden (z. B. eine zehnminütige Übertragungszeit für ein großes Volume). In diesem Fall werden der Beziehungsstatus als *Übertragung wird ausgeführt* und der Integritätsstatus als *Fehlerhaft* angezeigt.

## <a name="next-steps"></a>Nächste Schritte  

* [Regionsübergreifende Replikation](cross-region-replication-introduction.md)
* [Verwalten der Notfallwiederherstellung](cross-region-replication-manage-disaster-recovery.md)
* [Metriken für die Volumereplikation](azure-netapp-files-metrics.md#replication)
* [Löschen von Volumereplikationen oder Volumes](cross-region-replication-delete.md)
* [Problembehandlung für die regionsübergreifende Replikation](troubleshoot-cross-region-replication.md)

