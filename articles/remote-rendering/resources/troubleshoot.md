---
title: Problembehandlung
description: Informationen zum Troubleshooting bei Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4990f0d0a10709f2c1c5a17806020cd685f999fc
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593332"
---
# <a name="troubleshoot"></a>Problembehandlung

Auf dieser Seite finden Sie allgemeine Probleme, die Azure Remote Rendering beeinträchtigen, sowie Möglichkeiten, diese zu beheben.

## <a name="cant-link-storage-account-to-arr-account"></a>Das Speicherkonto kann nicht mit dem ARR-Konto verknüpft werden.

Manchmal wird während der [Verknüpfung eines Speicherkontos](../how-tos/create-an-account.md#link-storage-accounts) das Remote Rendering-Konto nicht aufgeführt. Um dieses Problem zu beheben, wechseln Sie im Azure-Portal zum ARR-Konto, und wählen Sie **Identität** unter der Gruppe **Einstellungen** auf der linken Seite aus. Stellen Sie sicher, dass **Status** auf **Ein** festgelegt ist.
![Unity-Framedebugger](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Der Client kann keine Verbindung mit dem Server herstellen.

Stellen Sie sicher, dass Ihre Firewalls (auf dem Gerät, innerhalb von Routern usw.) die in den [Systemanforderungen](../overview/system-requirements.md#network-firewall) genannten Ports nicht blockieren.

## <a name="error-disconnected-videoformatnotavailable"></a>Fehler „`Disconnected: VideoFormatNotAvailable`“

Vergewissern Sie sich, dass Ihre GPU-Hardware Videodecodierung unterstützt. Weitere Informationen finden Sie unter [Entwicklungs-PC](../overview/system-requirements.md#development-pc).

Wenn Sie an einem Laptop mit zwei GPUs arbeiten, kann es vorkommen, dass die GPU, die Sie standardmäßig für Ausführungen verwenden, keine Funktion für die Hardwarevideodecodierung bietet. Versuchen Sie in diesem Fall, die Verwendung der anderen GPU durch die App zu erzwingen. Dies ist häufig in den Einstellungen des GPU-Treibers möglich.

## <a name="retrieve-sessionconversion-status-fails"></a>Fehler beim Abrufen des Sitzungs-/Konvertierungsstatus

Das zu häufige Senden von REST-API-Befehlen bewirkt, dass der Server eine Drosselung durchführt und schließlich einen Fehler zurückgibt. Der HTTP-Statuscode für die Drosselung lautet 429 („Zu viele Anforderungen“). Als Faustregel sollte eine Verzögerung von **5–10 Sekunden zwischen nachfolgenden Aufrufen** erfolgen.

Beachten Sie, dass dieses Limit nicht nur direkte REST-API-Aufrufe betrifft, sondern auch die C#-/C++-Entsprechungen wie `Session.GetPropertiesAsync`, `Session.RenewAsync` oder `Frontend.GetAssetConversionStatusAsync`.

Wenn eine serverseitige Drosselung auftritt, ändern Sie den Code so, dass die Aufrufe seltener durchgeführt werden. Der Server setzt den Drosselungsstatus jede Minute zurück, sodass der Code nach einer Minute sicher erneut ausgeführt werden kann.

## <a name="h265-codec-not-available"></a>H.265-Codec nicht verfügbar

Der Server kann aus zwei Gründen die Verbindung mit dem Fehler `codec not available` ablehnen.

**Der H.265-Codec ist nicht installiert:**

Stellen Sie zunächst sicher, dass die **HEVC-Videoerweiterungen** wie im Abschnitt [Software](../overview/system-requirements.md#software) der Systemanforderungen beschrieben installiert wurde.

Wenn weiterhin Probleme auftreten, stellen Sie sicher, dass Ihre Grafikkarte H.265 unterstützt und der neueste Grafiktreiber installiert ist. Herstellerspezifische Informationen finden Sie im Abschnitt [Entwicklungs-PC](../overview/system-requirements.md#development-pc) der Systemanforderungen.

**Der Codec ist installiert, kann aber nicht verwendet werden:**

Der Grund für dieses Problem ist eine falsche Sicherheitseinstellung in den DLLs. Dieses Problem tritt beim Versuch, mit H.265 codierte Videos anzusehen, nicht auf. Das Problem wird durch eine Neuinstallation des Codecs auch nicht behoben. Führen Sie stattdessen die folgenden Schritte aus:

1. Öffnen Sie eine **PowerShell-Instanz mit Administratorrechten**, und führen Sie folgenden Befehl aus:

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Dieser Befehl sollte die `InstallLocation` des Codecs ausgeben, z. B.:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Öffnen Sie diesen Ordner in Windows-Explorer.
1. Es sollten die Unterordner **x86** und **x64** vorhanden sein. Klicken Sie mit der rechten Maustaste auf einen der Ordner, und wählen Sie **Eigenschaften** aus.
    1. Wählen Sie die Registerkarte **Sicherheit** aus, und klicken Sie auf die Schaltfläche **Erweitert**.
    1. Klicken Sie für **Besitzer** auf **Ändern**.
    1. Geben Sie in das Textfeld **Administratoren** ein.
    1. Klicken Sie auf **Namen überprüfen** und auf **OK**.
1. Wiederholen Sie die angegebenen Schritte für den anderen Ordner.
1. Wiederholen Sie außerdem die angegebenen Schritte für jede DLL-Datei in beiden Ordnern. Insgesamt sollten vier DLL-Dateien vorhanden sein.

Um zu überprüfen, ob die Einstellungen jetzt richtig sind, gehen Sie für jede der vier DLL-Dateien folgendermaßen vor:

1. Wählen Sie **Eigenschaften > Sicherheit > Bearbeiten** aus.
1. Gehen Sie die Liste aller **Gruppen/Benutzer** durch, und stellen Sie sicher, dass jeder Eintrag über die Berechtigung **Lesen und ausführen** verfügt (das Kontrollkästchen in der Spalte **Zulassen** muss aktiviert sein).

## <a name="low-video-quality"></a>Geringe Videoqualität

Die Videoqualität kann durch die Netzwerkqualität oder einen fehlenden H.265-Videocodec beeinträchtigt werden.

* Weitere Informationen finden Sie in den Schritten zum [Identifizieren von Netzwerkproblemen](#unstable-holograms).
* Informationen zum Installieren des aktuellen Grafiktreibers finden Sie in den [Systemanforderungen](../overview/system-requirements.md#development-pc).

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Mit MRC aufgezeichnetes Video spiegelt nicht die Qualität des Liveerlebnisses wider

Ein Video kann mit HoloLens mithilfe von [Mixed Reality Capture (MRC)](/windows/mixed-reality/mixed-reality-capture-for-developers) aufgezeichnet werden. Das resultierende Video weist jedoch aus zwei Gründen eine schlechtere Qualität als das Liveerlebnis auf:
* Die Videobildfrequenz ist auf 30 Hz begrenzt, im Gegensatz zu 60 Hz beim Liveerlebnis.
* Die Videobilder durchlaufen nicht den Verarbeitungsschritt [Late Stage Reprojection](../overview/features/late-stage-reprojection.md), daher wirkt das Video weniger flüssig.

Beides sind inhärente Einschränkungen der Aufzeichnungstechnik.

## <a name="black-screen-after-successful-model-loading"></a>Schwarzer Bildschirm nach erfolgreichem Laden des Modells

Wenn Sie mit der Renderingruntime verbunden sind und erfolgreich ein Modell geladen haben, aber danach nur ein schwarzer Bildschirm angezeigt wird, kann dies verschiedene Gründe haben.

Es wird empfohlen, die folgenden Tests auszuführen, bevor Sie eine ausführlichere Analyse durchführen:

* Ist der H.265-Codec installiert? Obwohl ein Fallback auf den H.264-Codec erfolgen sollte, wurden bereits Fälle beobachtet, in denen dieses Fallback nicht ordnungsgemäß funktionierte. Informationen zum Installieren des aktuellen Grafiktreibers finden Sie in den [Systemanforderungen](../overview/system-requirements.md#development-pc).
* Wenn Sie ein Unity-Projekt verwenden, schließen Sie Unity, löschen Sie die temporären Ordner *library* und *obj* im Projektverzeichnis, laden Sie das Projekt erneut, und erstellen Sie es neu. In einigen Fällen konnte das Beispiel aufgrund von zwischengespeicherten Daten nicht ordnungsgemäß ausgeführt werden, ohne dass ein offensichtlicher Grund vorlag.

Wenn diese beiden Schritte nicht hilfreich waren, müssen Sie herausfinden, ob der Client Videoframes empfängt. Dies kann programmgesteuert abgefragt werden, wie im Kapitel zu [serverseitigen Leistungsabfragen](../overview/features/performance-queries.md) erläutert. Das `FrameStatistics struct` weist einen Member auf, der angibt, wie viele Videoframes empfangen wurden. Wenn diese Zahl größer als 0 ist und im Lauf der Zeit zunimmt, empfängt der Client Videoframes vom Server. Es muss sich demzufolge um ein Problem auf der Clientseite handeln.

### <a name="common-client-side-issues"></a>Häufige clientseitige Fehler

**Das Modell überschreitet die Grenzwerte des ausgewählten virtuellen Computers, insbesondere die maximale Anzahl von Polygonen:**

Weitere Informationen finden Sie bei den spezifischen [Servergrößenbeschränkungen](../reference/limits.md#overall-number-of-polygons).

**Das Modell befindet sich nicht im Kamerafrustum:**

In vielen Fällen wird das Modell ordnungsgemäß dargestellt, befindet sich jedoch außerhalb des Kamerafrustums. Ein häufiger Grund dafür ist, dass das Modell mit einem Pivot deutlich außerhalb des Mittelpunkts exportiert wurde, sodass es von der Entfernungsclippingebene der Kamera abgeschnitten wird. Es hilft, den Begrenzungsrahmen des Modells programmgesteuert abzufragen und mit Unity als Linienrahmen zu visualisieren oder die entsprechenden Werte im Debugprotokoll auszugeben.

Der Konvertierungsprozess generiert außerdem neben dem konvertierten Modell eine [JSON-Ausgabedatei](../how-tos/conversion/get-information.md). Beim Debuggen von Problemen mit der Modellpositionierung ist es sinnvoll, einen Blick auf den `boundingBox`-Eintrag im [outputStatistics-Abschnitt](../how-tos/conversion/get-information.md#the-outputstatistics-section) zu werfen:

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Das Begrenzungsfeld wird als `min`- und `max`-Position in Metern im 3D-Raum beschrieben. Eine Koordinate von 1000,0 bedeutet daher eine Entfernung von einem Kilometer vom Ursprung.

Bei diesem Begrenzungsfeld können zwei Probleme auftreten, die zu unsichtbarer Geometrie führen:
* **Das Feld kann stark dezentriert sein**, sodass das Objekt aufgrund von Verdeckung durch die entfernte Ebene in seiner Gesamtheit beschnitten wird. Die `boundingBox`-Werte für diesen Fall würden etwa so aussehen: `min = [-2000, -5,-5], max = [-1990, 5,5]`, mit einem großen Offset auf der X-Achse als Beispiel für diesen Fall. Um diese Art Problem zu lösen, aktivieren Sie die Option `recenterToOrigin` in der [Konfiguration der Modellkonvertierung](../how-tos/conversion/configure-model-conversion.md).
* **Das Feld kann zentriert sein, ist aber um Größenordnungen zu groß**. Das bedeutet, dass die Geometrie des Modells in allen Richtungen abgeschnitten wird, obwohl die Kamera in der Mitte es Modells startet. Typische `boundingBox`-Werte für diesen Fall sehen etwa so aus: `min = [-1000,-1000,-1000], max = [1000,1000,1000]`. Der Grund für diese Art Problem ist normalerweise ein nicht angepasster Einheitenbereich. Geben Sie zum Ausgleich einen [Skalierungswert für die Konvertierung](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) an, oder nehmen Sie ein Markup des Quellmodells mit den richtigen Einheiten vor. Skalierung kann auch auf den Stammknoten angewendet werden, wenn das Modell zur Laufzeit geladen wird.

**Die Unity-Renderpipeline enthält nicht die Renderhooks:**

Azure Remote Rendering erstellt Hooks in der Unity-Renderpipeline, um die Framezusammenstellung mit dem Video und die Neuprojektion durchzuführen. Öffnen Sie das Menü *:::no-loc text="Window > Analysis > Frame debugger":::* , um zu überprüfen, ob diese Hooks vorhanden sind. Aktivieren Sie ihn, und stellen Sie sicher, dass zwei Einträge für `HolographicRemotingCallbackPass` in der Pipeline vorhanden sind:

![Unity-Renderingpipeline](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>Schachbrettmuster wird nach dem Laden des Modells gerendert

Wenn das gerenderte Bild wie folgt aussieht: ![Der Screenshot zeigt ein Raster aus schwarzen und weißen Quadraten mit einem Menü „Extras“.](../reference/media/checkerboard.png)
dann trifft der Renderer auf die [Polygongrenzwerte für die Standardkonfigurationsgröße](../reference/vm-sizes.md). Zum Minimieren der Auswirkungen kann entweder zur Größe **Premium**-Konfiguration gewechselt oder die Anzahl der sichtbaren Polygone reduziert werden.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Das gerenderte Bild in Unity steht auf dem Kopf

Beachten Sie unbedingt das [Unity Tutorial: Anzeigen von Remotemodellen](../tutorials/unity/view-remote-models/view-remote-models.md) genau. Ein umgedrehtes Bild zeigt an, dass Unity erforderlich ist, um ein Renderziel außerhalb des Bildschirms zu erstellen. Dieses Verhalten wird derzeit nicht unterstützt und hat einen großen Einfluss auf die Leistung der HoloLens 2.

Gründe für dieses Problem könnten MSAA, HDR oder die Aktivierung der Nachbearbeitung sein. Stellen Sie sicher, dass das Profil mit niedriger Qualität ausgewählt und in Unity als Standard festgelegt ist. Wechseln Sie dazu zu *Bearbeiten > Projekteinstellungen... > Qualität*.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Unity-Code, der die Remote Rendering-API verwendet, wird nicht kompiliert.

### <a name="use-debug-when-compiling-for-unity-editor"></a>Verwenden von „Debuggen“ beim Kompilieren für den Unity-Editor

Stellen Sie den *Buildtyp* der Unity-Lösung auf **Debug** um. Beim Testen von ARR im Unity-Editor ist die Definition `UNITY_EDITOR` nur in Debugbuilds verfügbar. Beachten Sie, dass dies nicht mit dem Buildtyp in Zusammenhang steht, der für [bereitgestellte Anwendungen](../quickstarts/deploy-to-hololens.md) verwendet wird. Hierbei sollten Sie Releasebuilds verwenden.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Kompilierungsfehler beim Kompilieren von Unity-Beispielen für HoloLens 2

Bei dem Versuch, Unity-Beispiele ( Schnellstart, ShowCaseApp, ...) für HoloLens 2 zu kompilieren, sind fälschlicherweise Fehler aufgetreten. Visual Studio beanstandet, dass einige Dateien nicht kopiert werden können, obwohl sie vorhanden sind. Wenn Sie auf dieses Problem treffen:
* Entfernen Sie alle temporären Unity-Dateien aus dem Projekt, und versuchen Sie es erneut. Schließen Sie hierzu Unity, löschen Sie die temporären Ordner *library* und *obj* im Projektverzeichnis, und laden/erstellen Sie das Projekt erneut.
* Stellen Sie sicher, dass sich die Projekte in einem Verzeichnis auf dem Datenträger mit einem relativ kurzen Pfad befinden, da es beim Kopieren gelegentlich zu Problemen mit langen Dateinamen zu kommen scheint.
* Wenn das nicht hilft, könnte es sein, dass zwischen MS Sense und dem Kopierschritt ein Konflikt besteht. Führen Sie diesen Registrierungsbefehl über die Befehlszeile aus (erfordert Administratorrechte), um eine Ausnahme einzurichten:
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Bei Arm64-Builds für Unity-Projekte treten Fehler auf, da die Datei „AudioPluginMsHRTF.dll“ fehlt.

Die `AudioPluginMsHRTF.dll` für Arm64 wurde dem *Windows Mixed Reality*-Paket *(com.unity.xr.windowsmr.metro)* in Version 3.0.1 hinzugefügt. Stellen Sie sicher, dass Sie Version 3.0.1 oder höher über den Unity Package Manager installiert haben. Navigieren Sie in der Menüleiste von Unity zu *Fenster > Paket-Manager*, und suchen Sie nach dem *Windows Mixed Reality*-Paket.

## <a name="native-c-based-application-does-not-compile"></a>Native C++-basierte Anwendung lässt sich nicht kompilieren

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>Fehler „Bibliothek nicht gefunden“ für UWP-Anwendung oder DLL

Innerhalb des C++-NuGet-Pakets gibt es die Datei `microsoft.azure.remoterendering.Cpp.targets`, die definiert, welche der binären Varianten verwendet werden soll. Um `UWP` zu identifizieren, prüfen die Bedingungen in der Datei auf `ApplicationType == 'Windows Store'`. Es muss daher sichergestellt werden, dass dieser Typ im Projekt festgelegt wird. Dies sollte der Fall sein, wenn Sie eine UWP-Anwendung oder DLL über den Projekt-Assistenten von Visual Studio erstellen.

## <a name="unstable-holograms"></a>Instabile Hologramme

Wenn bei Kopfbewegungen auch gerenderte Objekte bewegt werden, liegen möglicherweise Probleme mit der *Late Stage Reprojection* (LSR) vor. Hinweise zur Vorgehensweise in einer solchen Situation finden Sie im Abschnitt zur [Late Stage Reprojection](../overview/features/late-stage-reprojection.md).

Ein weiterer Grund für instabile Hologramme (wabernde, verzerrte, zitternde oder springende Hologramme) kann eine schlechte Netzwerkkonnektivität sein, insbesondere unzureichende Netzwerkbandbreite oder zu hohe Latenz. Ein guter Indikator für die Qualität der Netzwerkverbindung ist der [Leistungsstatistikwert](../overview/features/performance-queries.md) `ServiceStatistics.VideoFramesReused`. Wiederverwendete Frames deuten auf Situationen hin, in denen ein altes Videoframe auf der Clientseite wiederverwendet werden musste, da kein neues Videoframe verfügbar war – beispielsweise aufgrund von Paketverlusten oder von Schwankungen der Netzwerklatenz. Wenn `ServiceStatistics.VideoFramesReused` häufig größer als null ist, deutet dies auf ein Netzwerkproblem hin.

Ein anderer Wert, der untersucht werden kann, ist `ServiceStatistics.LatencyPoseToReceiveAvg`. Er sollte konstant weniger als 100 ms betragen. Höhere Werte können darauf hinweisen, dass Sie mit einem zu weit entfernten Rechenzentrum verbunden sind.

Eine Liste der möglichen Entschärfungen finden Sie in den [Richtlinien für die Netzwerkkonnektivität](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="z-fighting"></a>Z-Fighting

Obwohl ARR eine [Z-Fighting-Minimierungsfunktion](../overview/features/z-fighting-mitigation.md) bietet, kann Z-Fighting in der Szene immer noch auftreten. Dieser Leitfaden zielt auf die Behebung dieser verbleibenden Probleme ab.

### <a name="recommended-steps"></a>Empfohlene Schritte

Verwenden Sie den folgenden Workflow zur Minimierung von Z-Fighting:

1. Testen Sie die Szene mit den Standardeinstellungen von ARR (Z-Fighting-Minimierung ist aktiviert).

1. Deaktivieren Sie die Z-Fighting-Minimierung über dessen [API](../overview/features/z-fighting-mitigation.md). 

1. Wechseln Sie die nahe und entfernte Ebene der Kamera in einen näheren Bereich.

1. Beheben Sie Fehler in der Szene über den nächsten Abschnitt.

### <a name="investigating-remaining-z-fighting"></a>Untersuchung des verbleibenden Z-Fightings

Wenn die oben genannten Schritte ausgeschöpft sind und das verbleibende Z-Fighting nicht akzeptabel ist, muss die zugrunde liegende Ursache des Z-Fightings untersucht werden. Wie auf der Seite zur [Z-Fighting-Minimierungsfunktion](../overview/features/z-fighting-mitigation.md) dargelegt, gibt es zwei Hauptgründe für Z-Fighting: Tiefengenauigkeitsverlust am äußersten Ende des Tiefenbereichs und Oberflächen, die sich schneiden, obwohl sie koplanar sind. Der Tiefengenauigkeitsverlust ist eine mathematische Eventualität und kann nur durch Befolgen von Schritt 3 oben minimiert werden. Koplanare Oberflächen weisen auf einen Fehler des Quellmedienobjekts hin und sind in den Quelldaten besser behoben.

ARR verfügt über ein Feature zur Bestimmung, ob Z-Fighting für Oberflächen möglich ist: [Schachbrettmusterhervorhebung](../overview/features/z-fighting-mitigation.md) Sie können auch visuell ermitteln, wodurch das Z-Fighting verursacht wird. Die folgende erste Animation zeigt ein Beispiel für den Tiefengenauigkeitsverlust in der Entfernung, und die zweite Animation zeigt ein Beispiel für nahezu koplanare Oberflächen:

![Die Animation zeigt ein Beispiel für den Tiefengenauigkeitsverlust in der Entfernung.](./media/depth-precision-z-fighting.gif)  ![Die Animation zeigt ein Beispiel für nahezu coplanare Oberflächen.](./media/coplanar-z-fighting.gif)

Vergleichen Sie diese Beispiele mit Ihrem Z-Fighting, um die Ursache zu ermitteln, oder folgen Sie optional diesem Schritt-für-Schritt-Workflow:

1. Positionieren Sie die Kamera über den Z-Fighting-Oberflächen, um direkt auf die Oberfläche zu blicken.
1. Bewegen Sie die Kamera langsam nach hinten (weg von den Oberflächen).
1. Wenn das Z-Fighting die ganze Zeit sichtbar ist, sind die Oberflächen perfekt koplanar. 
1. Wenn das Z-Fighting die meiste Zeit sichtbar ist, sind die Oberflächen nahezu koplanar.
1. Wenn das Z-Fighting nur von weitem sichtbar ist, liegt die Ursache in mangelnder Tiefengenauigkeit.

Koplanare Oberflächen können eine Reihe verschiedener Ursachen haben:

* Ein Objekt wurde von der exportierenden Anwendung aufgrund eines Fehlers oder unterschiedlicher Workflowansätze dupliziert.

    Prüfen Sie diese Probleme mit der jeweiligen Anwendung und der Anwendungsunterstützung.

* Oberflächen werden dupliziert und gespiegelt, sodass sie in Renderern, die Frontface oder Backface Culling verwenden, doppelseitig erscheinen.

    Der Import über die [Modellkonvertierung](../how-tos/conversion/model-conversion.md) bestimmt die prinzipielle Seitigkeit des Modells. Doppelseitigkeit wird als Standard angenommen. Die Oberfläche wird als dünne Wand mit physisch korrekter Beleuchtung von beiden Seiten gerendert. Die Einseitigkeit kann durch Flags im Quellmedienobjekt impliziert oder während der [Modellkonvertierung](../how-tos/conversion/model-conversion.md) explizit erzwungen werden. Zusätzlich, aber optional, kann der [einseitige Modus](../overview/features/single-sided-rendering.md) auf „normal“ festgelegt werden.

* Objekte überschneiden sich in den Quellmedienobjekten.

     Objekte, die so transformiert wurden, dass sich einige ihrer Oberflächen überlappen, erzeugen ebenfalls Z-Fighting. Auch das Transformieren von Teilen der Szenenstruktur in der importierten Szene in ARR kann dieses Problem verursachen.

* Oberflächen werden gezielt zum Anfassen erstellt, z. B. Bilder oder Text auf Wänden.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Grafikartefakte mit Stereorendering mit mehreren Durchläufen in nativen C++-Apps

In einigen Fällen können benutzerdefinierte native C++-Apps, die nach dem Aufruf von [**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) einen Stereorenderingmodus mit mehreren Durchläufen für lokale Inhalte verwenden (Rendering für das linke und rechte Auge in getrennten Durchläufen), einen Treiberfehler auslösen. Der Fehler führt zu nicht deterministischen Rasterungsfehlern, die dazu führen, dass einzelne Dreiecke oder Teile von Dreiecken des lokalen Inhalts zufällig verschwinden. Aus Leistungsgründen wird ohnehin empfohlen, lokale Inhalte mit einem moderneren Stereorenderingverfahren mit einzelnem Durchlauf zu rendern, z. B. mithilfe von **SV_RenderTargetArrayIndex**.

## <a name="next-steps"></a>Nächste Schritte

* [Systemanforderungen](../overview/system-requirements.md)
* [Netzwerkanforderungen](../reference/network-requirements.md)