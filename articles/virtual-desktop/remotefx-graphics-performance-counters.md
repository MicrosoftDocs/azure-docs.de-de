---
title: Diagnostizieren von Problemen mit der Grafikleistung in Remotedesktop – Azure
description: Dieser Artikel beschreibt, wie Sie Leistungsindikatoren in RemoteFX-Grafik in Remotedesktopprotokoll-Sitzungen verwenden, um Leistungsprobleme mit Grafiken in Windows Virtual Desktop zu diagnostizieren.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 0b4113f1e0024415135aa99d1fb4e881efe448a3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498594"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnostizieren von Problemen mit der Grafikleistung in Remotedesktop

Wenn das System nicht die erwartete Leistung zeigt, ist es wichtig, die Ursache des Problems zu ermitteln. Dieser Artikel hilft Ihnen beim Erkennen und Beheben von Leistungsengpässen im Zusammenhang mit Grafiken während Remotedesktopprotokoll-Sitzungen (RDP).

## <a name="find-your-remote-session-name"></a>Suchen nach dem Namen der Remotesitzung

Sie benötigen den Namen der Remotesitzung, um Grafikleistungsindikatoren zu identifizieren. Folgen Sie den Anweisungen in diesem Abschnitt, um den Namen Ihrer Windows Virtual Desktop Preview-Remotesitzung zu ermitteln.

1. Öffnen Sie die Windows-Eingabeaufforderung aus der Remotesitzung.
2. Führen Sie den Befehl **qwinsta** aus.
    - Wenn die Sitzung auf einem virtuellen Computer (VM) mit mehreren Sitzungen gehostet wird: Das Suffix jedes Indikatornamens entspricht dem Suffix im Sitzungsnamen, z.B. „rdp-tcp 37“.
    - Wenn die Sitzung auf einem virtuellen Computer gehostet wird, der virtuelle Grafikprozessoren (vGPU) unterstützt: Die Leistungsindikatoren werden auf dem Server und nicht auf dem virtuellen Computer gespeichert. Die Instanzen der Leistungsindikatoren enthalten den Namen des virtuellen Computers anstelle der Zahl im Sitzungsnamen, z.B. „Win8 Enterprise VM“.

>[!NOTE]
> Während Leistungsindikatoren RemoteFX in ihrem Namen enthalten, sind in vGPU-Szenarien auch Remotedesktopgrafiken enthalten.

## <a name="access-performance-counters"></a>Zugreifen auf Leistungsindikatoren

Mithilfe von Leistungsindikatoren in RemoteFX-Grafik können Sie Engpässe erkennen, da Sie Faktoren wie die Codierungszeit für Frames und übersprungene Frames verfolgen können.

Nachdem Sie den Namen der Remotesitzung ermittelt haben, folgen Sie den nachstehenden Anweisungen, um die Leistungsindikatoren in RemoteFX-Grafik für die Remotesitzung zu erfassen.

1. Wählen Sie **Start** > **Verwaltungstools** > **Systemmonitor** aus.
2. Erweitern Sie im Dialogfeld **Systemmonitor** die Option **Überwachungstools**, und wählen Sie **Systemmonitor** und anschließend **Hinzufügen** aus.
3. Erweitern Sie im Dialogfeld **Leistungsindikatoren hinzufügen** in der Liste **Verfügbare Leistungsindikatoren** das Leistungsindikatorobjekt für RemoteFX-Grafik.
4. Wählen Sie die zu überwachenden Leistungsindikatoren aus.
5. Wählen Sie in der Liste **Instanzen des ausgewählten Objekts** die jeweiligen Instanzen aus, die für die ausgewählten Leistungsindikatoren überwacht werden sollen, und wählen Sie dann **Hinzufügen** aus. Zum Auswählen aller verfügbaren Leistungsindikatorinstanzen wählen Sie **Alle Instanzen** aus.
6. Klicken Sie nach dem Hinzufügen der Leistungsindikatoren auf **OK**.

Die ausgewählten Leistungsindikatoren werden auf dem Bildschirm „Systemmonitor“ angezeigt.

>[!NOTE]
>Jede aktive Sitzung auf einem Host verfügt über eine eigene Instanz jedes Leistungsindikators.

## <a name="diagnosis"></a>Diagnose

Leistungsprobleme im Zusammenhang mit Grafiken werden im Allgemeinen in vier Kategorien unterteilt:

- Niedrige Framerate
- Zufällige Unterbrechungen
- Hohe Eingabelatenz
- Schlechte Framequalität

Beginnen Sie mit niedriger Framerate, zufälligen Unterbrechungen und hoher Eingabelatenz. Im nächsten Abschnitt erfahren Sie, mit welchen Leistungsindikatoren die jeweilige Kategorie gemessen wird.

### <a name="performance-counters"></a>Leistungsindikatoren

Dieser Abschnitt hilft Ihnen beim Ermitteln von Engpässen.

Überprüfen Sie zuerst den Leistungsindikator „Ausgabeframes/Sekunde“. Er misst die Anzahl der Frames, die dem Client zur Verfügung gestellt werden. Wenn dieser Wert niedriger als beim Leistungsindikator „Eingabeframes/Sekunde“ ist, werden Frames übersprungen. Zur Ermittlung des Engpasses verwenden Sie die Leistungsindikatoren „Übersprungene Frames/Sekunde“.

Es gibt drei Leistungsindikatoren des Typs „Übersprungene Frames/Sekunde“:

- Übersprungene Frames/Sekunde (Unzureichende Netzwerkressourcen)
- Übersprungene Frames/Sekunde (Unzureichende Clientressourcen)
- Übersprungene Frames/Sekunde (Unzureichende Serverressourcen)

Ein hoher Wert bei einem der Leistungsindikatoren „Übersprungene Frames/Sekunde“ zeigt an, dass das Problem mit der Ressource in Zusammenhang steht, die vom Leistungsindikator überwacht wird. Wenn beispielsweise der Client Frames nicht mit der gleichen Rate decodiert und darstellt, mit der Frames vom Server bereitgestellt werden, weist der Leistungsindikator „Übersprungene Frames/Sekunde (Unzureichende Clientressourcen)“ einen hohen Wert auf.

Wenn der Wert des Leistungsindikators „Ausgabeframes/Sekunde“ dem des Leistungsindikators „Eingabeframes/Sekunde“ entspricht, aber dennoch ungewöhnliche Verzögerungen oder Unterbrechungen auftreten, kann die durchschnittliche Codierungszeit die Ursache des Problems sein. Die Codierung ist ein synchroner Vorgang, der auf dem Server im Szenario mit einer Einzelsitzung (vGPU) und auf dem virtuellen Computer im Szenario mit mehreren Sitzungen stattfindet. Die durchschnittliche Codierungszeit sollte unter 33 ms liegen. Beträgt die durchschnittliche Codierungszeit weniger als 33 ms und treten dennoch Leistungsprobleme auf, kann ein Problem mit der verwendeten App oder dem verwendeten Betriebssystem vorliegen.

Weitere Informationen zum Diagnostizieren von Problemen im Zusammenhang mit einer App finden Sie unter [Leistungsindikatoren für verzögerte Benutzereingabe](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

Da RDP eine durchschnittliche Codierungszeit von 33 ms unterstützt, wird eine Eingabeframerate von bis zu 30 Frames pro Sekunde unterstützt. Beachten Sie, dass 33 ms die maximale unterstützte Framerate ist. In vielen Fällen ist die Framerate für den Benutzer geringer, je nachdem, wie oft ein Frame für RDP von der Quelle bereitgestellt wird. Aufgaben wie das Ansehen eines Videos erfordern beispielsweise die volle Eingabeframerate von 30 Frames pro Sekunde, doch wird für weniger ressourcenintensive Aufgaben wie das gelegentliche Bearbeiten eines Textdokuments keine solch hohe Rate von Eingabeframes pro Sekunde für eine gute Benutzererfahrung benötigt.

Mit dem Leistungsindikator „Framequalität“ können Sie Probleme der Framequalität diagnostizieren. Dieser Leistungsindikator gibt die Qualität des Ausgabeframes als Prozentsatz der Qualität des Quellframes an. Der Qualitätsverlust ist möglicherweise auf RemoteFX zurückzuführen oder bereits in der Grafikquelle vorhanden sein. Wenn RemoteFX den Qualitätsverlust verursacht, kann das Problem fehlende Netzwerk- oder Serverressourcen zum Senden von Inhalten mit höherer Genauigkeit sein.

## <a name="mitigation"></a>Lösung

Wenn Serverressourcen den Engpass verursachen, versuchen Sie die Leistung mit einem der folgenden Schritte zu verbessern:

- Reduzieren Sie die Anzahl von Sitzungen pro Host.
- Erhöhen Sie die Speicher- und Computeressourcen auf dem Server.
- Verringern Sie die Auflösung der Verbindung.

Wenn Netzwerkressourcen den Engpass verursachen, versuchen Sie die Netzwerkverfügbarkeit pro Sitzung mit einem der folgenden Schritte zu verbessern:

- Reduzieren Sie die Anzahl von Sitzungen pro Host.
- Verringern Sie die Auflösung der Verbindung.
- Verwenden Sie ein Netzwerk mit höherer Bandbreite.

Wenn Clientressourcen den Engpass verursachen, versuchen Sie die Leistung mit einem oder zwei der folgenden Schritte zu verbessern:

- Installieren Sie den neuesten Remotedesktopclient.
- Erhöhen Sie die Speicher- und Computeressourcen auf dem Clientcomputer.

> [!NOTE]
> Der Leistungsindikator „Quellframes/Sekunde“ wird derzeit nicht unterstützt. Aktuell ist der Leistungsindikator „Quellframes/Sekunde“ immer auf 0 gesetzt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen eines GPU-optimierten virtuellen Azure-Computers finden Sie unter [Konfigurieren der Beschleunigung durch Graphics Processing Units (GPU) für die Windows Virtual Desktop-Umgebung (Vorschauversion)](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Eine Übersicht über die Problembehandlung und Eskalationspfade finden Sie unter [Überblick über Problembehandlung, Feedback und Support](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Weitere Informationen zum Vorschaudienst finden Sie unter [Umgebung der Windows Desktop-Vorschau](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
