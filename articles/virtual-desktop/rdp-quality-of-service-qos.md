---
title: Implementieren von Quality of Service (QoS) für Windows Virtual Desktop (Vorschau)
titleSuffix: Azure
description: Einrichten von QoS (Vorschau) für Windows Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639099"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Implementieren von Quality of Service (QoS) für Windows Virtual Desktop (Vorschau)

> [!IMPORTANT]
> Die Unterstützung der QoS-Richtlinie (Quality of Service) für Windows Virtual Desktop ist zurzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und nicht für Produktionsworkloads empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[RDP Shortpath](./shortpath.md) stellt einen direkten UDP-basierten Transport zwischen Remotedesktopclient und dem Sitzungshost bereit. RDP Shortpath ermöglicht die Konfiguration von QoS-Richtlinien (Quality of Service) für die RDP-Daten.
QoS in Windows Virtual Desktop ermöglicht RDP-Datenverkehr in Echtzeit, der für Netzwerkverzögerungen empfindlich ist, sich vor dem Datenverkehr „vorzudrängeln“, der weniger empfindlich ist. Ein Beispiel für einen derartigen weniger empfindlichen Datenverkehr wäre das Herunterladen einer neuen App, wo eine zusätzliche Sekunde kein Thema ist. QoS verwendet Windows-Gruppenrichtlinienobjekte, um alle Pakete in Echtzeitdatenströmen zu identifizieren und zu markieren, und hilft Ihrem Netzwerk, RDP-Datenverkehr einen dedizierten Anteil an Bandbreite zur Verfügung zu stellen.

Wenn Sie eine große Gruppe von Benutzern unterstützen, die eines der in diesem Artikel beschriebenen Probleme haben, müssen Sie wahrscheinlich QoS implementieren. Ein kleines Unternehmen mit wenigen Benutzern benötigt QoS möglicherweise nicht, aber es sollte auch dort hilfreich sein.

Ohne jede Form von QoS können folgende Probleme auftreten:

* Jitter: RDP-Pakete treffen mit unterschiedlicher Rate ein, was zu visuellen und Audiostörungen führen kann.
* Paketverlust: Pakete werden gelöscht, was zu einer erneuten Übertragung führt, die zusätzliche Zeit erfordert.
* Verzögerte Roundtrip-Zeit (Round-Trip Time, RTT): Es dauert lange, bis RDP-Pakete ihre Ziele erreichen, was zu merkbaren Verzögerungen zwischen dem Eingang und der Reaktion der Remoteanwendung führt.

Die unkomplizierteste Methode, diese Probleme zu beheben, besteht darin, die Kapazität sowohl der internen als auch der Internetdatenverbindungen zu erhöhen. Da dies häufig kostenpflichtig ist, bietet QoS Ihnen eine Möglichkeit, Ihre bestehenden Ressourcen effektiver zu verwalten, anstatt Bandbreite hinzuzufügen. Um Qualitätsprobleme zu beheben, sollten Sie zuerst QoS verwenden und dann nur bei Bedarf Bandbreite hinzuzufügen.

Damit QoS effektiv ist, müssen Sie in ihrer gesamten Organisation konsistente QoS-Einstellungen anwenden. Jeder Teil des Pfads, der Ihre QoS-Prioritäten nicht unterstützt, kann die Qualität der RDP-Sitzung beeinträchtigen.

## <a name="introduction-to-qos-queues"></a>Einführung in QoS-Warteschlangen

Zum Bereitstellen von QoS benötigen Netzwerkgeräte eine Möglichkeit zum Klassifizieren des Datenverkehrs und müssen in der Lage sein, RDP von anderem Netzwerkdatenverkehr zu unterscheiden.

Beim Eingang von Netzwerkdatenverkehr in einen Router wird der Datenverkehr in eine Warteschlange gestellt. Wenn keine QoS-Richtlinie konfiguriert ist, gibt es nur eine Warteschlange, und alle Daten werden mit der gleichen First-in-First-Out-Priorität behandelt. Dies bedeutet, dass RDP-Datenverkehr möglicherweise hinter anderem Datenverkehr stecken bleibt, wenn eine Verzögerung von wenigen Millisekunden kein Problem darstellen würde.

Wenn Sie QoS implementieren, definieren Sie mehrere Warteschlangen mit einem von mehreren Features zur Überlastungsverwaltung wie den Prioritätswarteschlangen von Cisco und [Class-Based Weighted Fair Queueing (CBWFQ)](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641), und Features zur Überlastungsvermeidung wie [Weighted Random Early Detection (WRED)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html).

Eine einfache Analogie ist, dass QoS in Ihrem Datennetzwerk virtuelle „Fahrgemeinschaftsspuren“ erstellt. Daher sind einige Datentypen nie oder selten von einer Verzögerung betroffen. Nachdem Sie diese Spuren erstellt haben, können Sie deren relative Größe anpassen und Ihre bestehende Verbindungsbandbreite viel effektiver verwalten, während Sie den Benutzern in Ihrer Organisation eine Benutzererfahrung in Businessqualität bieten.

## <a name="qos-implementation-checklist"></a>Checkliste für die QoS-Implementierung

Führen Sie allgemein Folgendes aus, um QoS zu implementieren:

1. [Stellen Sie sicher, dass das Netzwerk bereit ist.](#make-sure-your-network-is-ready)
2. [Stellen Sie sicher, dass RDP Shortpath aktiviert ist.](./shortpath.md) QoS-Richtlinien werden für den umgekehrten Reverse Connection-Transport nicht unterstützt.
3. [Implementieren Sie das Einfügen von DSCP-Markern](#insert-dscp-markers) auf Sitzungshosts.

Beachten Sie beim Vorbereiten der QoS-Implementierung die folgenden Richtlinien:

* Der kürzeste Pfad zum Sitzungshost ist der beste.
* Es sollten keine Hindernisse wie Proxys oder Paketuntersuchungsgeräte in diesem Pfad liegen.

## <a name="make-sure-your-network-is-ready"></a>Sicherstellen, dass das Netzwerk bereit ist

Wenn Sie eine QoS-Implementierung in Erwägung ziehen, sollten Sie die Bandbreitenanforderungen und andere [Netzwerkanforderungen](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context) bereits festgelegt haben.
  
Überlastung des Datenverkehrs über ein Netzwerk wirkt sich maßgeblich auf die Medienqualität aus. Ein Bandbreitenmangel führt zu Leistungseinbußen und einer schlechten Benutzererfahrung. Wenn die Übernahme und Nutzung von Windows Virtual Desktop zunimmt, verwenden Sie [Log Analytics](./diagnostics-log-analytics.md), um Probleme zu identifizieren und dann mithilfe von QoS und selektiven Bandbreitenergänzungen Anpassungen vorzunehmen.

### <a name="vpn-considerations"></a>Überlegungen zu VPN

QoS funktioniert nur bei Implementierung für alle Verbindungen zwischen Clients und Sitzungshosts wie erwartet. Wenn Sie QoS in einem internen Netzwerk verwenden und sich ein Benutzer von einem Remotestandort aus anmeldet, können Sie nur innerhalb Ihres internen, verwalteten Netzwerks Prioritäten setzen. Remotestandorte können zwar durch Implementierung eines virtuellen privaten Netzwerks (VPN) eine verwaltete Verbindung empfangen, aber ein VPN ist seinem Wesen nach mit einem Paketmehraufwand verbunden und verursacht Verzögerungen im Echtzeitdatenverkehr.

In einer globalen Organisation mit verwalteten Links, die sich über Kontinente erstrecken, sollten Sie unbedingt QoS verwenden, da die Bandbreite für diese Links im Vergleich zum LAN begrenzt ist.

## <a name="insert-dscp-markers"></a>Einfügen von DSCP-Markern

Sie könnten QoS mithilfe eines Gruppenrichtlinienobjekts (Group Policy Object, GPO) implementieren, um Sitzungshosts anzuweisen, einen DSCP-Marker in IP-Paketheader einzufügen, der sie als einen bestimmten Typ von Datenverkehr kennzeichnet. Router und andere Netzwerkgeräte können so konfiguriert werden, dass sie diese Kennzeichnungen erkennen und den Datenverkehr in eine separate Warteschlange mit höherer Priorität setzen.

Sie können DSCP-Kennzeichnungen mit Poststempeln vergleichen, die Postmitarbeitern zeigen, wie dringend die Lieferung ist, und wie sie am besten für eine schnelle Lieferung sortieren können. Nachdem Sie Ihr Netzwerk so konfiguriert haben, dass RDP-Datenströme Priorität haben, sollten sich Verlust und Verspätung von Paketen erheblich verringern.

Wenn alle Netzwerkgeräte die gleichen Klassifizierungen, Kennzeichnungen und Prioritäten verwenden, ist es möglich, Verzögerungen, gelöschte Pakete und Jitter zu reduzieren oder zu beseitigen. Aus der RDP-Perspektive besteht der wesentliche Konfigurationsschritt in der Klassifizierung und Kennzeichnung von Paketen. Damit End-to-End-QoS jedoch erfolgreich ist, müssen Sie die RDP-Konfiguration auch sorgfältig an der zugrunde liegenden Netzwerkkonfiguration ausrichten.
Der DSCP-Wert weist ein entsprechend konfiguriertes Netzwerk an, welche Priorität einem Paket oder Datenstrom zugewiesen werden soll.

Sie sollten den DSCP-Wert 46 verwenden, der der DSCP-Klasse **Expedited Forwarding (EF)** zugeordnet ist.

### <a name="implement-qos-on-session-host-using-group-policy"></a>Implementieren von QoS auf dem Sitzungshost mithilfe von Gruppenrichtlinien

Sie können den vordefinierten DSCP-Wert in der Gruppenrichtlinie mit richtlinienbasierter Quality of Service (QoS) festlegen.

Um eine QoS-Richtlinie für in die Domäne eingebundene Sitzungshosts zu erstellen, melden Sie sich zuerst bei einem Computer an, auf dem die Gruppenrichtlinienverwaltung installiert wurde. Öffnen Sie die Gruppenrichtlinienverwaltung (wählen Sie „Start“ aus, zeigen Sie auf „Verwaltungstools“, und wählen Sie dann „Gruppenrichtlinienverwaltung“ aus), und führen Sie dann die folgenden Schritte aus:

1. Suchen Sie in der Gruppenrichtlinienverwaltung den Container, in dem die neue Richtlinie erstellt werden sollte. Wenn sich z. B. alle Ihre Sitzungshostcomputer in einer Organisationseinheit mit dem Namen **Sitzungshosts** befinden, sollte die neue Richtlinie in der Organisationseinheit „Sitzungshosts“ erstellt werden.

2. Klicken Sie mit der rechten Maustaste auf den entsprechenden Container, und wählen Sie dann **GPO in dieser Domäne erstellen und hier verknüpfen** aus.

3. Geben Sie im Dialogfeld **Neues Gruppenrichtlinienobjekt** den Namen des neuen Gruppenrichtlinienobjekts in das Feld **Name** ein, und wählen Sie **OK**.

4. Klicken Sie mit der rechten Maustaste auf die neu erstellte Richtlinie, und wählen Sie **Bearbeiten** aus.

5. Erweitern Sie im Gruppenrichtlinienverwaltungs-Editor **Computerkonfiguration**, erweitern Sie **Windows-Einstellungen**, klicken Sie mit der rechten Maustaste auf **Richtlinienbasierte QoS**, und wählen Sie dann **Neue Richtlinie erstellen** aus.

6. Geben Sie im Dialogfeld **Richtlinienbasierte QoS** auf der Öffnungsseite im Feld **Name** den Namen für die neue Richtlinie ein. Wählen Sie **DSCP-Wert angeben** aus, und legen Sie den Wert auf **46** fest. Lassen Sie **Ausgangsdrosselungsrate angeben** deaktiviert, und wählen Sie dann **Weiter** aus.

7. Wählen Sie auf der nächsten Seite **Nur Anwendungen mit diesem ausführbaren Namen** aus, geben Sie den Namen **svchost.exe** ein, und wählen Sie dann **Weiter** aus. Diese Einstellung weist die Richtlinie an, nur dem entsprechenden Datenverkehr aus dem Remotedesktopdienst Priorität einzuräumen.

8. Stellen Sie auf der dritten Seite sicher, dass sowohl **Beliebige Quell-IP-Adresse** als auch **Beliebige Ziel-IP-Adresse** ausgewählt ist, und wählen Sie dann **Weiter** aus. Diese beiden Einstellungen stellen sicher, dass die Pakete unabhängig davon verwaltet werden, welcher Computer (IP-Adresse) die Pakete gesendet hat und welcher Computer (IP-Adresse) die Pakete empfängt.

9. Wählen Sie auf Seite vier in der Dropdownliste **Wählen Sie das Protokoll aus, auf das diese QoS-Richtlinie angewendet wird** die Option **UDP** aus.

10. Wählen Sie unter der Überschrift **Geben Sie die Quellportnummer an** die Option **Von diesem Quellport oder Bereich** aus. Geben Sie im zugehörigen Textfeld **3390** ein. Wählen Sie **Fertig stellen** aus.

Die neuen Richtlinien, die Sie erstellt haben, werden erst wirksam, wenn die Gruppenrichtlinie auf Ihren Sitzungshostcomputern aktualisiert wurde. Obwohl die Gruppenrichtlinie regelmäßig automatisch aktualisiert wird, können Sie eine sofortige Aktualisierung erzwingen, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie auf jedem Sitzungshost, für den Sie die Gruppenrichtlinie aktualisieren möchten, eine Eingabeaufforderung als Administrator (*Als Administrator ausführen*).

1. Geben Sie an der Eingabeaufforderung Folgendes ein:

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>Implementieren von QoS auf dem Sitzungshost mit PowerShell

Sie können QoS für RDP Shortpath mithilfe des folgenden PowerShell-Cmdlets festlegen:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>Verwandte Artikel

* [QoS-Richtlinie (Quality of Service)](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den Bandbreitenanforderungen für Windows Virtual Desktop finden Sie unter [RDP-Bandbreitenanforderungen (Remotedesktopprotokoll)](rdp-bandwidth.md).
* Weitere Informationen zur Netzwerkkonnektivität von Windows Virtual Desktop finden Sie unter [Grundlegendes zur Windows Virtual Desktop-Netzwerkkonnektivität](network-connectivity.md).
