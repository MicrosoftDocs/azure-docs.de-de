---
title: Integration von Projekt Akustik in Unreal und Wwise
titlesuffix: Azure Cognitive Services
description: Hier wird beschrieben, wie Sie die Projekt Akustik-Plug-Ins für Unreal und Wwise in Ihr Projekt integrieren.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: c6baa9f8330338c1e5fdc9ee0b5a8cc8b344e871
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61436019"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integration von Projekt Akustik in Unreal und Wwise
Diese exemplarische Vorgehensweise beschreibt die Schritte zur Integration des Projekt Akustik-Plug-In-Pakets in Ihr vorhandenes Unreal- und Wwise-Spieleprojekt. 

Softwareanforderungen:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 oder 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Wwise-Plug-In für Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Wenn Sie das Wwise SDK direkt integriert haben, anstatt die Wwise-Plug-Ins für Unreal zu verwenden, lesen Sie die Dokumentation zum Projekt Akustik-Unreal-Plug-In, und passen Sie Wwise-API-Aufrufe an.

Wenn Sie Projekt Akustik mit einer anderen Audioengine als Wwise verwenden möchten, setzen Sie sich mit uns in den [Projekt Akustik-Foren](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics) in Verbindung. Sie können das Projekt Akustik-Unreal-Plug-In verwenden, um Akustikdaten abzufragen und dann API-Aufrufe an Ihre Engine zu senden.

## <a name="download-project-acoustics"></a>Herunterladen von Projekt Akustik
Wenn dies noch nicht geschehen ist, laden Sie das [Projekt Akustik-Plug-In-Paket für Unreal & Wwise](https://www.microsoft.com/download/details.aspx?id=58090) herunter. 

Das Paket enthält ein Plug-In für die Unreal-Engine und ein Plug-In für den Wwise-Mixer. Das Unreal-Plug-In bietet Editor- und Runtime-Integration. Während eines Spiels berechnet das Projekt Akustik-Unreal-Plug-In Parameter wie z.B. Verdeckung für jedes Spielobjekt in jedem Frame. Diese Parameter werden in Wwise-API-Aufrufe übersetzt.

## <a name="review-integration-steps"></a>Überprüfen der Integrationsschritte

Im Folgenden finden Sie die wichtigsten Schritte, mit denen Sie das Paket installieren und in Ihrem Spiel bereitstellen.
1. Installieren des Wwise-Mixer-Plug-Ins für Projekt Akustik
2. (Erneutes) Bereitstellen von Wwise in Ihrem Spiel – dadurch wird das Mixer-Plug-In in Ihr Spielprojekt integriert
3. Hinzufügen des Projekt Akustik-Unreal-Plug-Ins zu Ihrem Spiel
4. Erweitern der Funktionen des Unreal-Plug-Ins von Wwise
5. Erstellen des Spiels und Überprüfen, ob Python aktiviert ist
6. Einrichten des Wwise-Projekts für die Verwendung von Projekt Akustik
7. Einrichten der Audiofunktionen in Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Installieren des Mixer-Plug-Ins für Projekt Akustik
* Öffnen Sie das Wwise-Startprogramm, und wählen Sie auf der Registerkarte **Plugins** (Plug-Ins) unter **Install New Plugins** (Neue Plug-Ins installieren) die Option **Add From Directory** (Aus Verzeichnis hinzufügen) aus. 

    ![Screenshot zur Installation eines Plug-Ins im Wwise-Startprogramm](media/wwise-install-new-plugin.png)

* Wählen Sie das Verzeichnis `AcousticsWwisePlugin\ProjectAcoustics` aus, das im heruntergeladenen Paket enthalten war. Es enthält das Plug-In-Paket für den Wwise-Mixer.

* Wwise installiert das Plug-In. In Projekt Akustik wird jetzt die Liste der in Wwise installierten Plug-Ins angezeigt.
![Screenshot der Liste der in Wwise installierten Plug-Ins nach der Installation von Projekt Akustik](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Erneutes) Bereitstellen von Wwise in Ihrem Spiel
Stellen Sie Wwise erneut in Ihrem Spiel bereit, auch wenn Sie Wwise bereits integriert haben. Dadurch wird das Wwise-Plug-In für Projekt Akustik übernommen.

* **Engine-Plug-In**: Wenn Sie Wwise als Spiel-Plug-In in einem Unreal-C++-Projekt installiert haben, überspringen Sie diesen Schritt. Wenn Sie Wwise stattdessen als Engine-Plug-In installiert haben, z.B. weil Ihr Unreal-Projekt nur als Blaupause fungiert, ist die Wwise-Bereitstellung mit unserem Mixer-Plug-In komplexer. Erstellen Sie ein leeres Unreal-C++-Dummyprojekt, schließen Sie es, falls der Unreal-Editor geöffnet wird, und führen Sie die verbleibenden Schritte aus, um Wwise in diesem Dummyprojekt bereitzustellen. Kopieren Sie dann das bereitgestellte Wwise-Plug-In.
 
* Klicken Sie im Wwise-Startprogramm auf die Registerkarte **Unreal Engine**, klicken Sie dann auf das Hamburger-Menü neben **Recent Unreal Engine Projects** (Letzte Unreal-Engine-Projekte), und wählen Sie **Browse for project** (Nach Projekt suchen) aus. Öffnen Sie die Unreal-Projektdatei `.uproject` Ihres Spiels.

    ![Screenshot der Registerkarte „Unreal“ im Wwise-Startprogramm](media/wwise-unreal-tab.png)

* Klicken Sie dann auf **Integrate Wwise in Project** (Wwise in Projekt integrieren) oder **Modify Wwise in Project** (Wwise in Projekt ändern). Mit diesem Schritt werden die Binärdateien von Wwise (erneut) in Ihr Projekt integriert, das jetzt das Projekt Akustik-Mixer-Plug-In enthält.

* **Engine-Plug-In**: Wenn Sie Wwise als Engine-Plug-In verwenden und das Dummyprojekt wie oben beschrieben erstellt haben, kopieren Sie den von Wwise bereitgestellten Ordner `[DummyUProject]\Plugins\Wwise`, und fügen Sie ihn oberhalb von `[UESource]\Engine\Plugins\Wwise` ein. `[DummyUProject]` ist der leere Unreal-C++Projektpfad, und `[UESource]` ist das Verzeichnis, in dem Sie die Unreal-Engine-Quellen installiert haben. Wenn Sie mit dem Kopieren fertig sind, können Sie das Dummyprojekt löschen.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Hinzufügen des Projekt Akustik-Unreal-Plug-Ins zu Ihrem Spiel
 
* Kopieren Sie den Ordner `Unreal\ProjectAcoustics` in das Plug-In-Paket, und erstellen Sie einen neuen Ordner namens `[UProjectDir]\Plugins\ProjectAcoustics`, wobei `UProjectDir` der Projektordner Ihres Spiels ist, der die Datei `.uproject` enthält.
  * **Engine-Plug-In**: Wenn Sie Wwise als Engine-Plug-In verwenden, sollten Sie auch Projekt Akustik als Unreal-Engine-Plug-In nutzen. Verwenden Sie anstelle des oben genannten Zielverzeichnisses dieses Verzeichnis: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Überprüfen Sie, ob neben dem Ordner `ProjectAcoustics` auch der Ordner `Wwise` angezeigt wird. Dieser enthält das Wwise-Plug-In sowie Binärdateien für das Mixer-Plug-In, das Sie in Schritt 2 (erneut) bereitgestellt haben.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Erweitern der Funktionen des Unreal-Plug-Ins von Wwise
* Für das Unreal-Plug-In für Projekt Akustik müssen zusätzliche Funktionen aus der Unreal-Plug-In-API von Wwise verfügbar gemacht werden. Befolgen Sie dazu [diese Anweisungen](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html) (in englischer Sprache). Wir haben eine Batchdatei bereitgestellt, um die Patchingprozedur zu automatisieren. 
* Führen Sie in `Plugins\ProjectAcoustics\Resources` die Datei `PatchWwise.bat` aus. Die folgende Beispielabbildung verwendet das Beispielprojekt „AcousticsGame“.

    ![Screenshot des Windows-Explorer-Fensters mit hervorgehobenem Skript zum Patchen von Wwise](media/patch-wwise-script.png)

* Wenn das DirectX SDK nicht installiert ist, müssen Sie in `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` die Zeile mit „DXSDK_DIR“ auskommentieren.

    ![Screenshot des Code-Editors mit der auskommentierten DXSDK-Zeile](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Erstellen des Spiels und Überprüfen, ob Python aktiviert ist

* Kompilieren Sie Ihr Spiel, und stellen Sie sicher, dass der Buildprozess ordnungsgemäß ausgeführt wird. Andernfalls überprüfen Sie oben genannten Schritte sorgfältig, bevor Sie fortfahren. 
* Öffnen Sie Ihr Projekt im Unreal-Editor. 
* **Engine-Plug-In**: Wenn Sie Projekt Akustik als Engine-Plug-In verwenden, stellen Sie sicher, dass dieses aktiviert ist und unter den integrierten Plug-Ins aufgeführt wird.
* Sie sehen einen neuen Modus, der darauf hinweist, dass Projekt Akustik integriert wurde.

    ![Screenshot von Unreal mit Akustikmodus „Vollständig“](media/acoustics-mode-full.png)

* Überprüfen Sie, ob das Python-Plug-In für Unreal aktiviert ist. Dies ist erforderlich, damit die Editor-Integration ordnungsgemäß funktioniert.

    ![Screenshot zum Aktivieren der Python-Erweiterungen im Unreal-Editor](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Einrichten des Wwise-Projekts

Im Download mit den Beispielen ist auch ein Wwise-Beispielprojekt enthalten. Es empfiehlt sich, dieses Projekt gleichzeitig mit diesen Anweisungen anzusehen. Die folgenden Screenshots stammen aus diesem Projekt.

### <a name="bus-setup"></a>Einrichten des Busses
* Das Projekt Akustik-Unreal-Plug-In sucht nach dem zugehörigen Mixer-Plug-In in einem Bus mit ***exakt*** diesem Namen: `Project Acoustics Bus`. Erstellen Sie einen neuen Audiobus mit diesem Namen. Das Mixer-Plug-In funktioniert in verschiedenen Konfigurationen, für die vorliegende exemplarische Vorgehensweise wird jedoch davon ausgegangen, dass es nur zur Verarbeitung von Hall verwendet wird. Dieser Bus trägt das abgemischte Hallsignal für alle Quellen, die Projekt Akustik verwenden. Er kann vorab in jede Busmischungsstruktur gemischt werden. In der folgenden Abbildung sehen Sie ein Beispiel dafür, das aus dem Wwise-Beispielprojekt im Download stammt.

    ![Screenshot von Wise-Bussen mit dem Projekt Akustik-Bus](media/acoustics-bus.png)

* Die Kanalkonfiguration im Bus muss auf einen der folgenden Werte festgelegt werden: `1.0, 2.0, 4.0, 5.1 or 7.1`. Bei anderen Konfigurationen erfolgt keine Ausgabe in diesem Bus.

    ![Screenshot der Kanalkonfigurationsoptionen für den Projekt Akustik-Bus](media/acoustics-bus-channel-config.png)

* Wechseln Sie jetzt zur Detailansicht für den Projekt Akustik-Bus, und vergewissern Sie sich, dass die Registerkarte für das Mixer-Plug-In angezeigt wird.

    ![Screenshot von Wwise, der die Aktivierung der Registerkarte für das Mixer-Plug-In für den Projekt Akustik-Bus zeigt](media/mixer-tab-enable.png)

* Wechseln Sie zur Registerkarte für das Mixer-Plug-In, und fügen Sie das Projekt Akustik-Mixer-Plug-In zum Bus hinzu.

    ![Screenshow von Wwise-Bus, der das Hinzufügen des Projekt Akustik-Mixer-Plug-Ins zeigt](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Einrichten der Akteur-Mixer-Hierarchie
* Aus Leistungsgründen wendet Projekt Akustik Audio-DSP auf alle Quellen gleichzeitig an. Dafür muss das Plug-In als Mixer-Plug-In fungieren. Bei Wwise müssen sich Mixer-Plug-In im Ausgabebus befinden, obwohl der Ausgabebus üblicherweise das reine Ausgabesignal ohne Effekte trägt. Für Projekt Akustik muss das reine Signal über Hilfsbusse geleitet werden, während `Project Acoustics Bus` das Signal mit Effekten trägt. Der folgende Prozess unterstützt die sukzessive Migration zu diesem Signalflow.

* Sehen wir uns folgendes Beispiel an: Sie verfügen über ein Projekt mit einer Akteur-Mixer-Hierarchie mit Klängen für Schritte (Footsteps), Waffen (Weapons) und weitere Elemente auf oberster Ebene. Jedes Element verfügt über einen entsprechenden Ausgabebus für die Mischung ohne Effekte. Nehmen wir nun an, dass Sie das Signal für die Schritte migrieren möchten, um akustische Effekte zu verwenden. Erstellen Sie zunächst einen entsprechenden Hilfsbus für die Mischung ohne Effekte, der ein untergeordnetes Element des Ausgabebusses „Footsteps“ ist. In der Abbildung unten ist diesem Element das Präfix „Dry“ vorangestellt, um die Busse besser zu organisieren – die genaue Bezeichnung ist jedoch nicht wichtig. Alle Zähler oder Effekte, die im Bus „Footsteps“ vorhanden sind, funktionieren weiter wie zuvor.

    ![Screenshot des empfohlenen Wwise-Setups für Mischung ohne Effekte](media/wwise-dry-mix-setup.png)

* Ändern Sie dann die Ausgabestruktur für den Akteur-Mixer-Bus für „Footsteps“ wie folgt: der Projekt Akustik-Bus wird als „Output Bus“ (Ausgabebus) festgelegt, „Dry_Footsteps“ als benutzerdefinierter „Auxiliary Bus“ (Hilfsbus).

    ![Screenshot des empfohlenen Wwise-Setups für Akteur-Mixer-Bus](media/actor-mixer-bus-settings.png)

* Jetzt werden alle Schritte akustisch aufbereitet und geben ihren Halleffekt im Projekt Akustik-Bus aus. Das Signal ohne Effekte wird über „Dry_Footsteps“ weitergeleitet und wie gewohnt mit räumlicher Tiefe versehen.

* Projekt Akustik wird nur auf Klänge angewendet, die eine 3D-Position in der Welt besitzen. Gemäß der [Wwise-Dokumentation](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/) (in englischer Sprache) müssen die Positionierungseigenschaften wie hier gezeigt festgelegt werden. Die Einstellung „3D Spatialization“ (Erzeugung von 3D-Raumtiefe) kann nach Bedarf als „Position“ oder „Position + Orientation“ (Position und Ausrichtung) festgelegt werden.

    ![Screenshot der empfohlenen Wwise-Einstellungen für die Akteurpositionierung](media/wwise-positioning.png)

* Der Ausgabebus kann nicht auf einen anderen Bus festgelegt werden, der vorab in den **Projekt Akustik-Bus** gemischt wird – diese Einstellung funktioniert nicht. Wwise erzwingt diese Anforderung für Mixer-Plug-Ins.

* Wenn ein untergeordnetes Element in der Akteur-Mixer-Hierarchie für „Footsteps“ keine akustischen Effekte verwenden soll, können Sie für dieses Element jederzeit die Option „Override parent“ (Übergeordnetes Element außer Kraft setzen) verwenden, um die Effekte zu deaktivieren.

* Wenn Sie durch das Spiel oder den Benutzer definierte Sendebefehle für Hall in einem Akteur-Mixer-Element im Spiel verwenden, deaktivieren Sie diese Befehle in diesem Mixer, um die doppelte Anwendung von Hall zu verhindern.

### <a name="spatialization"></a>Erzeugen von räumlicher Tiefe
Standardmäßig wendet das Projekt Akustik-Plug-In für den Wwise-Mixer einen Faltungshall an und überlässt Wwise das Erzeugen der räumlichen Tiefe durch Schwenken. Wenn Sie Projekt Akustik in dieser Standardkonfiguration nur mit Hall verwenden, können Sie in Ihrer Mischung ohne Effekte jede Kanalkonfiguration und jede Methode zum Erzeugen räumlicher Tiefe verwenden und so nahezu jeden Spatializer („Raumerzeuger“) mit Projekt Akustik-Hall mischen und abgleichen. Ihnen stehen die Optionen [Ambisonics-based binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) (Ambisonics-basierte binaurale Raumerzeuger) und [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound) zur Verfügung.
 
Projekt Akustik enthält einen optionalen Raumerzeuger, der sowohl objektbasiertes, hochauflösendes HRTF-Rendering als auch Schwenken unterstützt. Aktivieren Sie in den Einstellungen des Mixer-Plug-Ins das Kontrollkästchen „Perform Spatialization“ (Raumerzeugung durchführen), und wählen Sie „HRTF“ oder „Panning“ (Schwenken) aus. Deaktivieren Sie benutzerdefinierte Sendebefehle für Hilfsfunktionen für alle Busse ohne Effekte, um eine doppelte Raumerzeugung durch das Projekt Akustik-Mixer-Plug-In und Wwise zu verhindern. Der Raumerzeugungsmodus kann nicht in Echtzeit geändert werden, da hierfür eine Neugenerierung der Soundbank erforderlich ist. Sie müssen Unreal neu starten und dann die Soundbänke neu generieren, bevor Sie die Wiedergabe starten, damit das Mixer-Plug-In Konfigurationsänderungen wie die Aktivierung des Kontrollkästchens „Perform Spatialization“ (Raumerzeugung durchführen) übernimmt.

![Screenshot der Wwise-Einstellungen für die Raumerzeugung im Mixer-Plug-In](media/mixer-spatial-settings.png)

Leider werden zurzeit keine anderen objektbasierten Raumerzeuger-Plug-Ins unterstützt, da diese als Mixer-Plug-Ins implementiert werden und Wwise derzeit nicht zulässt, dass einem einzigen Akteur-Mixer-Element mehrere Mixer-Plug-Ins zugewiesen sind.  

## <a name="7-audio-setup-in-unreal"></a>7. Einrichten der Audiofunktionen in Unreal
* Zuerst müssen Sie ein Baking für Ihr Spiellevel ausführen, um ein akustisches Medienobjekt zu erhalten, das in `Content\Acoustics` platziert wird. Lesen Sie das [Unreal-Bakingtutorial für Projekt Akustik](unreal-baking.md), und fahren Sie danach hier fort. Das Paket enthält einige Level, für die bereits ein Baking ausgeführt wurde.
* Erstellen Sie einen Acoustics Space-Akteur (akustischer Raum) in Ihrer Szene. Erstellen Sie pro Level nur einen Akteur, da dieser die Akustik für das gesamte Level darstellt. 

    ![Screenshot des Unreal-Editors bei der Erstellung des Acoustics Space-Akteurs](media/create-acoustics-space.png)

* Weisen Sie jetzt das Akustikmedienobjekt, für das bereits ein Baking ausgeführt wurde, der Acoustics Data-Position im Acoustics Space-Akteur zu. Jetzt verfügt Ihre Szene über akustischen Klang!

    ![Screenshot des Unreal-Editors bei der Acoustics-Medienobjektzuweisung](media/acoustics-asset-assign.png)

* Fügen Sie jetzt einen leeren Akteur hinzu, und führen Sie folgende Schritte aus:

    ![Screenshot des Unreal-Editors beim Verwenden der Acoustics-Komponente in einem leeren Akteur](media/acoustics-component-usage.png)

1. Fügen Sie dem Akteur eine Acoustics Audio-Komponente hinzu. Diese Komponente erweitert die Wwise-Audiokomponente um Funktionen für Projekt Akustik.
2. Das Kontrollkästchen „Play on Start“ (Beim Start wiedergeben) ist standardmäßig aktiviert und löst das zugeordnete Wwise-Ereignis beim Starten des Levels aus.
3. Aktivieren Sie das Kontrollkästchen „Show Acoustics Parameters“ (Akustikparameter anzeigen), um Debuginformationen zur Quelle auf dem Bildschirm anzuzeigen.
    ![Screenshot des Bereichs „Acoustics“ des Unreal-Editors für Schallquelle mit aktivierten Debugwerten](media/debug-values.png)
4. Weisen Sie mithilfe des üblichen Wwise-Workflows ein Wwise-Ereignis zu.
5. Stellen Sie sicher, dass die Option „Use Spatial Audio“ (Raumklang verwenden) deaktiviert ist. Wenn Sie Projekt Akustik für eine bestimmte Audiokomponente verwenden, können Sie derzeit nicht gleichzeitig die Spatial Audio-Engine von Wwise für die Akustik verwenden.

Sie sind fertig, Bewegen Sie sich in der Szene, und erkunden Sie die akustischen Effekte.

## <a name="next-steps"></a>Nächste Schritte
* [Entwurfstutorial für Projekt Akustik in Unreal/Wwise](unreal-workflow.md)
* [Erfahren Sie mehr über Bakingvorgänge](unreal-baking.md) für Ihre Spielszenen. 
