---
title: include file
description: include file
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178525"
---
## <a name="view-device-telemetry"></a>Anzeigen der Gerätetelemetrie

Sie können die von Ihrem Gerät gesendeten Telemetriedaten in der Lösung auf der Seite **Device Explorer** sehen.

1. Wählen Sie auf der Seite **Device Explorer** in der Liste der Geräte das von Ihnen bereitgestellte Gerät aus: In einem Bereich werden Informationen über Ihr Gerät angezeigt, einschließlich eines Diagramms der Gerätetelemetrie:

    ![Gerätedetails anzeigen](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Wählen Sie **Druck**, um die Anzeige der Telemetriedaten zu ändern:

    ![Telemetriedaten zum Druck anzeigen](media/iot-suite-visualize-connecting/devicespressure.png)

1. Um Diagnoseinformationen zu Ihrem Gerät anzuzeigen, scrollen Sie nach unten zu **Diagnose**:

    ![Gerätediagnose anzeigen](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Aktionen auf Ihrem Gerät

Um Methoden auf Ihren Geräten aufzurufen, verwenden Sie in der Remoteüberwachungslösung die Seite **Device Explorer**. In der Lösung für die Remoteüberwachung implementieren **Kühlgeräte** beispielsweise eine **Reboot**-Methode.

1. Wählen Sie **Geräte**, um zur Seite **Device Explorer** in der Lösung zu navigieren.

1. Wählen Sie auf der Seite **Device Explorer** in der Liste der Geräte das von Ihnen bereitgestellte Gerät aus:

    ![Auswählen Ihres realen Geräts](media/iot-suite-visualize-connecting/devicesselect.png)

1. Um eine Liste der Methoden anzuzeigen, die Sie auf Ihrem Gerät aufrufen können, wählen Sie **Aufträge** und dann **Methoden** aus. Um einen Auftrag für die Ausführung auf mehreren Geräten zu planen, können Sie mehrere Geräte in der Liste auswählen. Im Bereich **Aufträge** werden die für alle ausgewählten Geräte gemeinsamen Methodentypen angezeigt.

1. Wählen Sie **Reboot**, legen den Auftragsnamen auf **RebootPhysicalChiller** fest, und wählen Sie dann **Übernehmen** aus:

    ![Planen des Firmwareupdates](media/iot-suite-visualize-connecting/deviceschedule.png)

1. In der Konsole wird bei der Ausführung Ihres Gerätecodes eine Reihe von Meldungen angezeigt, während das simulierte Gerät die Methode verarbeitet.

> [!NOTE]
> Um den Status des Auftrags in der Lösung nachzuverfolgen, wählen Sie **Auftragsstatus anzeigen**.

## <a name="next-steps"></a>Nächste Schritte

Im Artikel [Anpassen des Solution Accelerators für die Remoteüberwachung](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) werden einige Möglichkeiten zum Anpassen des Solution Accelerators beschrieben.