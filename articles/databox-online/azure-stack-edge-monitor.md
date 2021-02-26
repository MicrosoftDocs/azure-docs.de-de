---
title: Überwachen des Azure Stack Edge Pro-Geräts | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie über das Azure-Portal und die lokale Webbenutzeroberfläche Ihr Azure Stack Edge Pro-Gerät überwachen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 388be1b13f89a50bed003731c01c6ab6287faaf9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491063"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>Überwachen Ihres Azure Stack Edge Pro-Geräts

In diesem Artikel wird beschrieben, wie Sie Ihr Azure Stack Edge Pro-Gerät überwachen. Zum Überwachen Ihres Geräts können Sie das Azure-Portal oder die lokale Webbenutzeroberfläche verwenden. Verwenden Sie das Azure-Portal, um Geräteereignisse anzuzeigen, Warnungen zu konfigurieren und zu verwalten und Metriken anzuzeigen. Verwenden Sie die lokale Webbenutzeroberfläche auf Ihrem physischen Gerät, um den Hardwarestatus der verschiedenen Gerätekomponenten anzuzeigen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> * Anzeigen von Geräteereignissen und den entsprechenden Warnungen
> * Anzeigen des Hardwarestatus von Gerätekomponenten
> * Anzeigen von Kapazitäts- und Transaktionsmetriken für Ihr Gerät

## <a name="view-device-events"></a>Anzeigen von Geräteereignissen

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Anzeigen des Hardwarestatus

Führen Sie die folgenden Schritte auf der lokalen Webbenutzeroberfläche aus, um den Hardwarestatus Ihrer Gerätekomponenten anzuzeigen.

1. Stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche Ihres Geräts her.
2. Navigieren Sie zu **Wartung > Hardwarestatus**. Sie können die Integrität der verschiedenen Gerätekomponenten anzeigen.

    ![Anzeigen des Hardwarestatus](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Anzeigen von Metriken

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Metriken auf Ihrem Gerät

In diesem Abschnitt werden die Überwachungsmetriken auf Ihrem Gerät beschrieben. Folgende Metriken sind möglich:

* Kapazitätsmetriken. Die Kapazitätsmetriken beziehen sich auf die Kapazität des Geräts.

* Transaktionsmetriken. Die Transaktionsmetriken beziehen sich auf die Lese- und Schreibvorgänge in Azure Storage.

* Edgecomputingmetriken. Die Edgecomputingmetriken beziehen sich auf die Verwendung von Edgecomputing auf Ihrem Gerät.

Eine vollständige Liste der Metriken enthält die folgende Tabelle:

|Kapazitätsmetriken                     |BESCHREIBUNG  |
|-------------------------------------|-------------|
|**Verfügbare Kapazität**               | Bezieht sich auf die Menge der Daten, die auf das Gerät geschrieben werden können. Das bedeutet, dass diese Metrik die Kapazität ist, die auf dem Gerät zur Verfügung gestellt werden kann. <br></br>Sie können die Gerätekapazität freigeben, indem Sie die lokale Kopie von Dateien löschen, die sowohl auf dem Gerät als auch in der Cloud über eine Kopie verfügen.        |
|**Gesamtkapazität**                   | Bezieht sich auf die Gesamtanzahl der Bytes auf dem Gerät, in die Daten geschrieben werden sollen, was auch als Gesamtgröße des lokalen Caches bezeichnet wird. <br></br> Sie können jetzt die Kapazität eines vorhandenen virtuellen Geräts erhöhen, indem Sie einen Datenträger hinzufügen. Fügen Sie einen Datenträger über die Hypervisor-Verwaltung für den virtuellen Computer hinzu, und starten Sie den virtuellen Computer neu. Der lokale Speicherpool des Gatewaygeräts wird erweitert, um den neu hinzugefügten Datenträger aufzunehmen. <br></br>Weitere Informationen finden Sie unter [Hinzufügen einer Festplatte für virtuelle Hyper-V-Computer](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Transaktionsmetriken              | BESCHREIBUNG         |
|-------------------------------------|---------|
|**Hochgeladene Cloudbytes (Gerät)**    | Summe aller Bytes, die über alle Freigaben auf Ihrem Gerät hochgeladen wurden        |
|**Hochgeladene Cloudbytes (Freigabe)**     | Hochgeladene Bytes pro Freigabe Diese Metrik kann folgende Werte aufweisen: <br></br> Avg, d. h. (Summe aller hochgeladenen Bytes pro Freigabe/Anzahl der Freigaben)  <br></br>Max, d. h. die maximale Anzahl von Bytes, die von einer Freigabe hochgeladen wurden <br></br>Min, d. h. die minimale Anzahl von Bytes, die von einer Freigabe hochgeladen wurden      |
|**Clouddownloaddurchsatz (Freigabe)**| Heruntergeladene Bytes pro Freigabe Diese Metrik kann folgende Werte aufweisen: <br></br> Avg, d. h. (Summe aller von einer Freigabe gelesenen oder heruntergeladenen Bytes/Anzahl der Freigaben) <br></br> Max, d. h. die maximale Anzahl von Bytes, die von einer Freigabe heruntergeladen wurden<br></br> Min, d. h. die minimale Anzahl von Bytes, die von einer Freigabe heruntergeladen wurden  |
|**Cloudlesedurchsatz**            | Summe aller Bytes, die über alle Freigaben auf Ihrem Gerät aus der Cloud gelesen wurden     |
|**Clouduploaddurchsatz**          | Summe aller Bytes, die über alle Freigaben auf Ihrem Gerät in die Cloud geschrieben wurden     |
|**Clouduploaddurchsatz (Freigabe)**  | Summe aller Bytes, die von einer Freigabe in die Cloud geschrieben wurden/Anzahl der Freigaben ist durchschnittlich, maximal und minimal pro Freigabe      |
|**Lesedurchsatz (Netzwerk)**           | Enthält den Systemnetzwerkdurchsatz für alle aus der Cloud gelesenen Bytes Diese Ansicht kann Daten enthalten, die nicht auf Freigaben beschränkt sind. <br></br>Bei der Aufteilung wird der Datenverkehr über alle Netzwerkadapter auf dem Gerät angezeigt, einschließlich der Adapter, die nicht verbunden sind oder aktiviert wurden.      |
|**Schreibdurchsatz (Netzwerk)**       | Enthält den Systemnetzwerkdurchsatz für alle in die Cloud geschriebenen Bytes Diese Ansicht kann Daten enthalten, die nicht auf Freigaben beschränkt sind. <br></br>Bei der Aufteilung wird der Datenverkehr über alle Netzwerkadapter auf dem Gerät angezeigt, einschließlich der Adapter, die nicht verbunden sind oder aktiviert wurden.          |

| Edgecomputingmetriken              | BESCHREIBUNG         |
|-------------------------------------|---------|
|**Edgecomputing – Arbeitsspeichernutzung**      |           |
|**Edgecomputing – CPU in Prozent**    |         |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Bandbreite verwalten](azure-stack-edge-manage-bandwidth-schedules.md).
Erfahren Sie, wie Sie [Warnbenachrichtigungen zu Geräteereignissen verwalten](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).
