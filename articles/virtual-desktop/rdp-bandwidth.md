---
title: Remotedesktopprotokoll-Bandbreitenanforderungen für Windows Virtual Desktop – Azure
titleSuffix: Azure
description: Grundlegendes zu den Bandbreitenanforderungen des Remotedesktopprotokolls (RDP) für Windows Virtual Desktop (WVD).
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: c9ef72241ac62efd8555de59bb52949321364035
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325178"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>Bandbreitenanforderungen des Remotedesktopprotokolls

Das Remotedesktopprotokoll (Remote Desktop Protocol, RDP) ist eine ausgereifte Technologie, die verschiedene Techniken anwendet, um die Bereitstellung von Remotegrafiken durch den Server an den Client zu perfektionieren. Je nach Anwendungsfall, Verfügbarkeit von Computingressourcen und Netzwerkbandbreite passt das RDP dynamisch verschiedene Parameter an, um für ein optimales Benutzererlebnis zu sorgen.

Das Remotedesktopprotokoll vereint mehrere dynamische virtuelle Kanäle (Dynamic Virtual Channels, DVCs) per Multiplexing in einem einzigen Datenkanal, der über verschiedene Methoden für den Netzwerktransport gesendet wird. Es gibt separate DVCs für Remotegrafiken, Eingabe, Geräteumleitung, Druckvorgänge und vieles mehr. WVD-Partner können auch eigene Erweiterungen implementieren, die DVC-Schnittstellen verwenden.
Die Menge der über RDP gesendeten Daten richtet sich nach der Benutzeraktivität. Ein Beispiel: Ein Benutzer arbeitet während des größten Teils einer Sitzung mit einfachen Textinhalten und benötigt dafür nur sehr wenig Bandbreite, sendet dann aber einen Druckauftrag für ein 200-seitigen Dokument an den Drucker. Dieser Druckauftrag verbraucht eine erhebliche Menge an Netzwerkbandbreite.

Bei Verwendung einer Remotesitzung hat die verfügbare Netzwerkbandbreite erhebliche Auswirkungen auf das Benutzererlebnis. Verschiedene Anwendungen und Anzeigeauflösungen erfordern unterschiedliche Netzwerkkonfigurationen. Daher müssen Sie unbedingt sicherstellen, dass Ihre Netzwerkkonfiguration Ihren Anforderungen entspricht.

## <a name="estimating-bandwidth-utilization"></a>Schätzen der Bandbreitennutzung

RDP verwendet verschiedene Komprimierungsalgorithmen für verschiedene Datentypen. Anhand der folgenden Tabelle können Sie die Datenübertragungen abschätzen:

| Datentyp | Direction | Art der Schätzung |
|---|---|---|
| Remotegrafiken | Vom Sitzungshost zum Client | Informationen finden Sie in der [ausführlichen Anleitung](#estimating-bandwidth-used-by-remote-graphics). |
|  Heartbeats | Beide Richtungen | Ca. 20 Bytes alle 5 Sekunden.  |
| Eingabe | Vom Client zum Sitzungshost | Die Datenmenge basiert auf der Benutzeraktivität; weniger als 100 Bytes für die meisten Vorgänge.  |
| Dateiübertragungen | Beide Richtungen | Dateiübertragungen verwenden Massenkomprimierung. Verwenden Sie die ZIP-Komprimierung, um einen Näherungswert zu erhalten. |
| Drucken | Vom Sitzungshost zum Client | Die Datenübertragung bei Druckaufträgen richtet sich nach dem Treiber und verwendet die Massenkomprimierung. Verwenden Sie ZIP-Komprimierung, um einen Näherungswert zu erhalten. |

In anderen Szenarien können sich die Bandbreitenanforderungen je nach der Art der Nutzung ändern, z. B:

* Telefon- oder Videokonferenzen
* Echtzeitkommunikation
* Streamen von 4-K-Videos

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>Schätzen der von Remotegrafiken benötigten Bandbreite

Es ist schwierig, die Bandbreitennutzung von Remotedesktops vorherzusagen. Der größte Teil des Datenverkehrs eines Remotedesktops wird durch Benutzeraktivitäten generiert. Jeder Benutzer ist einzigartig, und Unterschiede in Arbeitsmustern können sich erheblich auf die Netzwerknutzung auswirken.

Um die Bandbreitenanforderungen zu verstehen, überwachen Sie am besten tatsächliche Benutzerverbindungen. Diese Überwachung kann über die integrierten Leistungsindikatoren oder über Netzwerkkomponenten erfolgen.

In vielen Fällen können Sie die Netzwerknutzung aber auch schätzen. Hierzu müssen Sie die Funktionsweise des Remotedesktopprotokolls kennen und die Arbeitsmuster der Benutzer analysieren.

Das Remoteprotokoll sendet die vom Remoteserver generierten Grafikdaten zur Anzeige auf einem lokalen Monitor. Genauer gesagt: Es übermittelt die auf dem Server vollständig zusammengesetzte Desktopbitmap.
Auf den ersten Blick mag das Senden einer Desktopbitmap trivial erscheinen, es erfordert aber eine große Menge an Ressourcen. Ein 1080 Pixel großes Desktopbild umfasst unkomprimiert etwa 8 MB Daten. Die Anzeige dieses Bilds auf einem lokal angeschlossenen Monitor mit einer nicht allzu hohen Bildschirmaktualisierungsrate von 30 Hz erfordert etwa 237 MB/s Bandbreite.

Um die Menge an über das Netzwerk übertragenen Daten zu reduzieren, verwendet RDP eine Kombination aus verschiedenen Techniken, beispielsweise den folgenden:

* Optimierungen der Bildfrequenz
* Klassifizierung des Bildschirminhalts
* Inhaltsspezifische Codecs
* Progressive Bildcodierung
* Clientseitiges Caching

Um Remotegrafiken besser verstehen zu können, beachten Sie Folgendes:

* Je umfangreicher die Grafik ist, desto mehr Bandbreite benötigt sie.
  * Text, Benutzeroberflächenelemente eines Fensters und einfarbige Flächen verbrauchen weniger Bandbreite als jedes andere Element.
  * Komplexe Bilder (z. B. Naturaufnahmen oder Fotografien von Städten oder Personen) beanspruchen am meisten Bandbreite. Die clientseitige Zwischenspeicherung hilft bei der Reduzierung dieser Bandbreite.
* Nur geänderte Teile eines Bildschirms werden übertragen. Wenn keine sichtbaren Aktualisierungen des Bildschirms vorhanden sind, werden keine neuen Daten gesendet.
* Videowiedergaben und andere Inhalte mit hohen Frameraten sind im Grunde eine Diashow aus Bildern. RDP nutzt dynamisch die jeweils geeigneten Videocodecs, um solche Inhalte mit einer Framerate zu übertragen, die nahezu der ursprünglichen Rate entspricht. Dennoch handelt sich weiterhin um Grafiken, und diese tragen weiterhin am meisten zur Bandbreitennutzung bei.
* Leerlaufzeiten in einer Remotedesktopsitzung bedeuten, dass keine oder nur minimale Bildschirmaktualisierungen erfolgen, daher ist die Netzwerknutzung in solchen Zeiten minimal.
* Wenn das Fenster des Remotedesktopclients minimiert wird, sendet der Sitzungshost keine Grafikaktualisierungen.

Denke daran, dass die Belastung deines Netzwerks sowohl von der Ausgabebildfrequenz deiner App-Workloads als auch von der Bildschirmauflösung abhängt. Wenn entweder die Bildfrequenz oder die Bildschirmauflösung erhöht wird, steigt auch der Bandbreitenbedarf. Beispielsweise benötigt eine geringe Workload bei einer Anzeige mit hoher Auflösung mehr verfügbare Bandbreite als eine geringe Workload bei normaler oder niedriger Auflösung. Unterschiedliche Anzeigeauflösungen erfordern unterschiedliche verfügbare Bandbreiten.

Anhand der folgenden Tabelle können Sie die in verschiedenen Grafikszenarien benötigte Datenmenge schätzen. Die Zahlen gelten für eine Einzelmonitorkonfiguration mit einer Auflösung von 1920 × 1080 Pixel. Angegeben werden die Werte sowohl für den Standardgrafikmodus als auch den Grafikmodus H.264/AVC 444.

| Szenario | Standardmodus | Modus H.264/AVC 444 | Miniaturansicht | Beschreibung des Szenarios |
|:---|---:|---:|---|---|
| Idle | 0,3 KBit/s | 0,3 KBit/s |:::image type="content" source="media/idle.png" alt-text="Screenshot der Verbindung im Leerlauf":::| Der Benutzer hat die Arbeit unterbrochen, es gibt keine aktiven Bildschirmaktualisierungen |
| Microsoft Word | 100–150 KBit/s | 200–300 KBit/s |:::image type="content" source="media/word.gif" alt-text="Animation von Microsoft Word":::| Der Benutzer arbeitet aktiv mit Microsoft Word: Er gibt Text ein, kopiert Grafiken und wechselt zwischen Dokumenten. |
| Microsoft Excel | 150–200 KBit/s | 400–500 KBit/s |:::image type="content" source="media/excel.gif" alt-text="Animation von Microsoft Excel":::| Der Benutzer arbeitet aktiv mit Microsoft Excel: Mehrere Zellen mit Formeln sowie Diagramme werden gleichzeitig aktualisiert. |
| Microsoft PowerPoint | 4–4,5 MBit/s | 1,6–1,8 MBit/s |:::image type="content" source="media/powerpoint.gif" alt-text="Animation von Microsoft PowerPoint":::| Der Benutzer arbeitet aktiv mit Microsoft PowerPoint: Er fügt Text und Bilder ein. Der Benutzer verwendet auch umfangreiche Grafiken und Folienübergänge. |
| Browsen im Web | 6–6,5 MBit/s | 0,9–1 MBit/s |:::image type="content" source="media/web.gif" alt-text="Animation des Browsens im Web":::| Der Benutzer arbeitet aktiv mit einer grafisch detailliert gestalteten Website, die mehrere statische und animierte Bilder umfasst. Der Benutzer scrollt horizontal und vertikal durch die Bilder. |
| Bildergalerie | 3,3–3,6 MBit/s | 0,7–0,8 MBit/s |:::image type="content" source="media/image-gallery.gif" alt-text="Animation der Bildergalerie":::| Der Benutzer arbeitet aktiv mit der Bildergalerieanwendung: Er durchsucht, zoomt und dreht Bilder und ändert ihre Größe. |
| Videowiedergabe | 8,5–9,5 MBit/s | 2,5–2,8 MBit/s |:::image type="content" source="media/video.gif" alt-text="Animation der Videowiedergabe":::| Der Benutzer sieht sich ein Video mit 30 Frames pro Sekunde an, das die Hälfte des Bildschirms einnimmt. |
| Videowiedergabe im Vollbild | 7,5–8,5 MBit/s | 2,5–3,1 MBit/s |:::image type="content" source="media/fullscreen-video.gif" alt-text="Animation der Videowiedergabe im Vollbild":::| Der Benutzer sieht sich ein Video mit 30 Frames pro Sekunde an, das im Vollbildmodus wiedergegeben wird. |

## <a name="dynamic-bandwidth-allocation"></a>Dynamische Bandbreitenzuordnung

Remotedesktopprotokoll ist ein modernes Protokoll, das so konzipiert ist, dass es sich dynamisch an sich ändernde Netzwerkbedingungen anpasst.
Anstelle festgelegter Grenzwerte für die Bandbreitennutzung verwendet RDP eine kontinuierliche Netzwerkerkennung, die die verfügbare Netzwerkbandbreite und die Roundtripzeit von Paketen aktiv überwacht. Basierend auf den Ergebnissen wählt RDP dynamisch die Optionen für die Grafikcodierung aus und weist Bandbreite für die Geräteumleitung und andere virtuelle Kanäle zu.  
Dank dieser Technologie kann RDP die gesamte Netzwerkpipe nutzen (sofern verfügbar) und schnell Kapazität wieder freigeben, wenn das Netzwerk für andere Aufgaben benötigt wird.
RDP erkennt die jeweiligen Netzwerkbedingungen und passt Bildqualität, Bildfrequenz und Komprimierungsalgorithmen an, wenn andere Anwendungen Netzwerkbandbreite anfordern.

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>Begrenzen der Nutzung der Netzwerkbandbreite per Drosselungsrate

In den meisten Szenarien muss die Bandbreitennutzung nicht begrenzt werden, da eine solche Begrenzung Funktionen für Benutzer beeinträchtigen kann. In Netzwerken mit eingeschränkter Bandbreite ist es jedoch zuweilen notwendig, die Netzwerknutzung zu begrenzen. Ein anderes Beispiel sind geleaste Netzwerke, deren Gebühren je nach übertragenem Datenverkehr berechnet werden.

In solchen Fällen können Sie ausgehenden RDP-Netzwerkdatenverkehr begrenzen, indem Sie in der QoS-Richtlinie eine Drosselungsrate angeben.

  >[!NOTE]
  > [Stellen Sie sicher, dass RDP Shortpath aktiviert ist](./shortpath.md): Eine Begrenzung der Drosselungsrate wird für den Datentransport in umgekehrten Verbindungen nicht unterstützt.

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>Implementieren der Begrenzung per Drosselungsrate auf dem Sitzungshost mithilfe einer Gruppenrichtlinie

Sie können die richtlinienbasierte Quality of Service (QoS) in einer Gruppenrichtlinie verwenden, um die vordefinierte Drosselungsrate festzulegen.

Um eine QoS-Richtlinie für in die Domäne eingebundene Sitzungshosts zu erstellen, melden Sie sich zuerst bei einem Computer an, auf dem die Gruppenrichtlinienverwaltung installiert wurde. Öffnen Sie die Gruppenrichtlinienverwaltung (wählen Sie „Start“ aus, zeigen Sie auf „Verwaltungstools“, und wählen Sie dann „Gruppenrichtlinienverwaltung“ aus), und führen Sie dann die folgenden Schritte aus:

1. Suchen Sie in der Gruppenrichtlinienverwaltung den Container, in dem die neue Richtlinie erstellt werden soll. Wenn sich z. B. alle Ihre Sitzungshostcomputer in einer Organisationseinheit mit dem Namen **Sitzungshosts** befinden, sollte die neue Richtlinie in dieser Organisationseinheit erstellt werden.

2. Klicken Sie mit der rechten Maustaste auf den entsprechenden Container, und wählen Sie dann **GPO in dieser Domäne erstellen und hier verknüpfen** aus.

3. Geben Sie im Dialogfeld **Neues Gruppenrichtlinienobjekt** den Namen des neuen Gruppenrichtlinienobjekts in das Feld **Name** ein, und klicken Sie auf **OK**.

4. Klicken Sie mit der rechten Maustaste auf die neu erstellte Richtlinie, und wählen Sie **Bearbeiten** aus.

5. Erweitern Sie im Gruppenrichtlinienverwaltungs-Editor **Computerkonfiguration**, erweitern Sie **Windows-Einstellungen**, klicken Sie mit der rechten Maustaste auf **Richtlinienbasierte QoS**, und wählen Sie dann **Neue Richtlinie erstellen** aus.

6. Geben Sie im Dialogfeld **Richtlinienbasierte QoS** auf der sich öffnenden Seite im Feld **Name** den Namen für die neue Richtlinie ein. Wählen Sie **Ausgehende Drosselungsrate angeben**, legen Sie den erforderlichen Wert fest, und klicken Sie dann auf **Weiter**.

7. Wählen Sie auf der nächsten Seite **Nur Anwendungen mit diesem ausführbaren Namen** aus, geben Sie den Namen **svchost.exe** ein, und klicken Sie dann auf **Weiter**. Diese Einstellung weist die Richtlinie an, nur dem entsprechenden Datenverkehr aus dem Remotedesktopdienst Priorität einzuräumen.

8. Stellen Sie auf der dritten Seite sicher, dass **Beliebige Quell-IP-Adresse** und **Beliebige Ziel-IP-Adresse** ausgewählt sind. Wählen Sie **Weiter** aus. Diese beiden Einstellungen stellen sicher, dass Pakete unabhängig davon verwaltet werden, welcher Computer (IP-Adresse) die Pakete gesendet hat und welcher Computer (IP-Adresse) die Pakete empfängt.

9. Wählen Sie auf Seite vier in der Dropdownliste **Protokoll auswählen, auf das diese QoS-Richtlinie angewendet wird** die Option **UDP** aus.

10. Wählen Sie unter der Überschrift **Quellportnummer angeben** die Option **Von diesem Quellport oder -bereich** aus. Geben Sie im zugehörigen Textfeld **3390** ein. Wählen Sie **Fertig stellen** aus.

Die neuen Richtlinien, die Sie erstellt haben, werden erst wirksam, wenn die Gruppenrichtlinie auf Ihren Sitzungshostcomputern aktualisiert wurde. Obwohl die Gruppenrichtlinie regelmäßig automatisch aktualisiert wird, können Sie eine sofortige Aktualisierung erzwingen, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie auf jedem Sitzungshost, für den Sie die Gruppenrichtlinie aktualisieren möchten, eine Eingabeaufforderung als Administrator (*Als Administrator ausführen*).

2. Geben Sie an der Eingabeaufforderung Folgendes ein:

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>Implementieren der Begrenzung per Drosselungsrate auf dem Sitzungshost mithilfe von PowerShell

Sie können die Drosselungsrate für RDP Shortpath mithilfe des folgenden PowerShell-Cmdlets festlegen:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den Bandbreitenanforderungen für Windows Virtual Desktop finden Sie unter [RDP-Bandbreitenanforderungen (Remotedesktopprotokoll)](rdp-bandwidth.md).
* Weitere Informationen zur Netzwerkkonnektivität von Windows Virtual Desktop finden Sie unter [Grundlegendes zur Windows Virtual Desktop-Netzwerkkonnektivität](network-connectivity.md).
* Informationen zu den ersten Schritten mit Quality of Service (QoS) für Windows Virtual Desktop finden Sie unter [Implementieren von Quality of Service (QoS) für Windows Virtual Desktop](rdp-quality-of-service-qos.md).
