---
title: 'Tutorial: Schritt-für-Schritt-Anleitung zum Erstellen einer neuen HoloLens Unity-App mit Azure Spatial Anchors | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie mit Azure Spatial Anchors eine neue HoloLens Unity-App erstellen.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/14/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3c125c54c1e0a70cdec19af912b17759d82a9936
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969429"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Tutorial: Schritt-für-Schritt-Anleitung zum Erstellen einer neuen HoloLens Unity-App mit Azure Spatial Anchors

In diesem Tutorial wird gezeigt, wie Sie mit Azure Spatial Anchors eine neue HoloLens Unity-App erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial ausführen können, benötigen Sie folgende Komponenten:

1. Einen Windows-Computer mit Installation von <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> mit der Workload **Entwicklung für die universelle Windows-Plattform** und der Komponente **Windows 10 SDK (10.0.17763.0 oder höher)** und <a href="https://git-scm.com/download/win" target="_blank">Git für Windows</a>.
2. Die [C++/WinRT-Visual Studio-Erweiterung (VSIX)](https://aka.ms/cppwinrt/vsix) für Visual Studio sollte über den [Visual Studio Marketplace](https://marketplace.visualstudio.com/) installiert werden.
3. Ein HoloLens-Gerät mit aktiviertem [Entwicklermodus](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio). Für diesen Artikel wird ein HoloLens-Gerät mit dem [Windows 10-Update von Oktober 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (auch als RS5 bezeichnet) benötigt. Öffnen Sie zum Aktualisieren auf das neueste HoloLens-Release die App **Einstellungen**, navigieren Sie zu **Update und Sicherheit**, und wählen Sie anschließend die Schaltfläche **Nach Updates suchen**.

## <a name="getting-started"></a>Erste Schritte

Wir werden zuerst unser Projekt und die Unity-Szene einrichten:
1. Starten Sie Unity.
2. Wählen Sie **Neu**aus.
4. Vergewissern Sie sich, dass **3D** ausgewählt ist.
5. Benennen Sie Ihr Projekt, und geben Sie einen **Speicherort** ein.
6. Klicken Sie auf **Projekt erstellen**.
7. Speichern Sie die leere Standardszene in einer neuen Datei mithilfe von: **Datei** > **Speichern unter**.
8. Nennen Sie die neue Szene **Main** (Haupt), und drücken Sie auf die Schaltfläche **Speichern**.

**Einrichten der Projekteinstellungen**

Jetzt legen wir einige Unity-Projekteinstellungen fest, die uns dabei helfen, das Windows Holographic SDK für die Entwicklung zu verwenden. 

Zuerst legen wir Qualitätseinstellungen für unsere Anwendung fest. 
1. Wählen Sie **Bearbeiten** > **Projekteinstellungen** > **Qualität** aus.
2. Klicken Sie in der Spalte unter dem **Windows Store**-Logo auf den Pfeil in der Zeile **Standard**, und wählen Sie **Sehr niedrig** aus. Sie wissen, dass die Einstellung ordnungsgemäß angewendet wurde, wenn das Feld in der Spalte **Windows Store** und der Zeile **Sehr niedrig** grün ist.

Wir müssen Unity mitteilen, dass die App, die wir exportieren möchten, eine plastische Ansicht anstelle einer 2D-Ansicht erstellen soll. Wir erstellen eine plastische Ansicht durch Aktivieren der Unterstützung für virtuelle Realität in Unity mit dem Windows 10 SDK als Ziel.

1. Wechseln Sie zu **Bearbeiten** > **Projekteinstellungen** > **Player**.
2. Wählen Sie im **Inspector-Bereich** für **Playereinstellungen**das **Windows Store**-Symbol aus.
3. Erweitern Sie die Gruppe **XR-Einstellungen**.
4. Aktivieren Sie im Abschnitt **Rendering** das Kontrollkästchen **Virtuelle Realität unterstützt**, um eine neue Liste mit **Virtual Reality SDKs** hinzuzufügen.
5. Überprüfen Sie, ob in der Liste **Windows Mixed Reality** angezeigt wird. Wenn nicht, wählen Sie die Schaltfläche **+** am Ende der Liste aus, und wählen Sie **Windows Mixed Reality** aus.
 
> [!NOTE]
> Wenn das Windows Store-Symbol nicht angezeigt wird, vergewissern Sie sich erneut, dass Sie das Windows Store .NET-Skript-Back-End vor der Installation ausgewählt haben. Wenn nicht, müssen Sie Unity eventuell mit der richtigen Windows-Installation neu installieren.

**Überprüfen der .NET-Konfiguration**
1. Wechseln Sie zu **Bearbeiten** > **Projekteinstellungen** > **Player** (eventuell haben Sie **Player** noch aus dem vorherigen Schritt geöffnet).
2. Wählen Sie im **Inspector-Bereich** für **Playereinstellungen**das **Windows Store**-Symbol aus.
3. Stellen Sie im Konfigurationsabschnitt **Weitere Einstellungen** sicher, dass **Skript-Back-End-** auf **.NET** festgelegt ist.

**Festlegen von Funktionen**
1. Wechseln Sie zu **Bearbeiten** > **Projekteinstellungen** > **Player** (eventuell haben Sie **Player** noch aus dem vorherigen Schritt geöffnet).
2. Wählen Sie im **Inspector-Bereich** für **Playereinstellungen**das **Windows Store**-Symbol aus.
3. Aktivieren Sie Konfigurationsabschnitt **Veröffentlichungseinstellungen** die Kontrollkästchen **InternetClientServer** und **SpatialPerception**.

**Einrichten der virtuellen Hauptkamera**
1. Wählen Sie im **Hierarchiebereich** die Option **Hauptkamera** aus.
2. Legen Sie im **Inspector** die Transformationsposition auf **0,0,0** fest.
3. Suchen Sie die Eigenschaft **Flags löschen** und ändern Sie das Dropdown von **Skybox** in **Volltonfarbe**.
4. Klicken Sie auf das Feld **Hintergrund**, um eine Farbauswahl zu öffnen.
5. Legen Sie **R, G, B und A** auf **0** fest.
6. Wählen Sie **Komponente hinzufügen** aus, und suchen Sie nach **Spatial Mapping Collider**.

**Erstellen unseres Skripts**
1. Erstellen Sie im Bereich **Projekt** einen neuen Ordner namens **Skripts** unter dem Ordner **Assets** (Ressourcen). 
2. Klicken Sie mit der rechten Maustaste auf den Ordner, und wählen Sie dann **Erstellen >** , **C#-Skript** aus. Benennen Sie es mit **AzureSpatialAnchorsScript**. 
3. Wechseln Sie zu **GameObject** -> **Leer erstellen**. 
4. Wählen Sie es aus, und benennen Sie es im **Inspector** von **GameObject** in **MixedRealityCloud** um. Wählen Sie **Komponente hinzufügen** aus, suchen Sie nach **AzureSpatialAnchorsScript**, und fügen Sie es hinzu.

## <a name="trying-it-out"></a>Ausprobieren
Um zu testen, dass alles funktioniert, erstellen Sie Ihre App in **Unity**, und stellen Sie sie aus **Visual Studio** bereit. Gehen Sie hierzu wie in Kapitel 6 des [**MR-Grundlagen 100: Erste Schritte mit Unity**-Kurses](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) vor. Es sollte der Unity-Startbildschirm und anschließend eine leere Anzeige angezeigt werden.

## <a name="place-an-object-in-the-real-world"></a>Platzieren eines Objekts in der realen Welt
Wir erstellen über Ihre App jetzt ein Objekt und platzieren es. Öffnen Sie die Visual Studio-Projektmappe, die wir beim [Bereitstellen unserer App](#trying-it-out) erstellt haben. 

Fügen Sie zunächst die folgenden Importe für `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs` hinzu:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Fügen Sie anschließend die folgenden Membervariablen in Ihrer `AzureSpatialAnchorsScript`-Klasse hinzu: 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-37,43-47,55-74)]

Fügen Sie als Nächstes den folgenden Code in Ihrer `Start()`-Methode hinzu. Dieser Code bindet `GestureRecognizer` ein, der erkennt, wenn in die Luft getippt wird, und dann `HandleTap` aufruft.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-85,88&highlight=4-10)]

Wir müssen nun die folgende `HandleTap()`-Methode unterhalb von `Update()` hinzufügen. Sie führt ein Raycasting aus und erhält einen Trefferpunkt (Hit Point), an dem eine Kugel platziert wird. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-274,295-297,301-309)]

Diese Kugel müssen wir nun erstellen. Die Kugel wird anfänglich weiß sein, aber dieser Wert wird später noch angepasst. Fügen Sie die folgende `CreateAndSaveSphere()`-Methode hinzu:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-324,389)]

Führen Sie Ihre App aus **Visual Studio** heraus aus, um sie noch einmal zu überprüfen. Tippen Sie diesmal auf den Bildschirm, um Ihre weiße Kugel zu erstellen und auf der Oberfläche Ihrer Wahl zu platzieren.

## <a name="set-up-the-dispatcher-pattern"></a>Einrichten des Dispatcher-Musters

Bei der Arbeit mit Unity müssen alle Unity-APIs, z. B. APIs, die Sie für Aktualisierungen der Benutzeroberfläche verwenden, im Hauptthread ausgeführt werden. In dem Code, den wir schreiben, erhalten wir Rückrufe an andere Threads. In diesen Rückrufen möchten wir die Benutzeroberfläche aktualisieren, weshalb wir eine Möglichkeit benötigen, um einem Nebenthread zum Hauptthread zu gelangen. Um Code aus einem Nebenthread im Hauptthread auszuführen, verwenden wir das Dispatcher-Muster. 

Fügen wir eine Membervariable namens „dispatchQueue“ hinzu, bei der es sich um eine Warteschlange mit Aktionen handelt. Wir pushen Aktionen in die Warteschlange, und entfernen sie dann im Hauptthread aus der Warteschlange und führen sie aus. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=33-46&highlight=6-9)]

Als Nächstes fügen wir eine Möglichkeit hinzu, um der Warteschlange eine Aktion hinzuzufügen. Fügen Sie direkt hinter `Update()` `QueueOnUpdate()` hinzu:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=102-112)]

Nun verwenden wir die Update()-Schleife, um zu überprüfen, ob eine Aktion in der Warteschlange vorhanden ist. Wenn ja, entfernen wir die Aktion aus der Warteschlange und führen sie aus.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=90-100&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Abrufen des Azure Spatial Anchors SDK

Jetzt laden wir das Azure Spatial Anchors SDK herunter. Wechseln Sie zur Seite [Azure Spatial Anchors GitHub Releases](https://github.com/Azure/azure-spatial-anchors-samples/releases). Laden Sie unter „Assets“ (Ressourcen) die Datei **AzureSpatialAnchors.unitypackage** herunter. 

Wechseln Sie in Unity zu **Assets** (Ressourcen), und klicken Sie auf **Import Package (Paket importieren)**  > **Custom Package... (Benutzerdefiniertes Paket...)** . Navigieren Sie zu dem Paket, und wählen Sie **Öffnen** aus.

Wählen Sie in dem neuen Fenster **Import Unity Package** (Unity-Paket importieren), das angezeigt wird, unten links **None** (Keine) aus. Wählen Sie anschließend unter **AzureSpatialAnchorsPlugin** >  **-Plug-Ins** die Einträge **Common** (Allgemein), **Editor** und  **HoloLens** aus. Klicken Sie in der unteren rechten Ecke auf **Import** (Importieren).

Nun müssen wir Nuget-Pakete wiederherstellen, um das Azure Spatial Anchors SDK zu erhalten. Kompilieren Sie aus **Unity** heraus, und öffnen Sie dann die sich ergebende **Visual Studio**-Projektmappe, und erstellen Sie diese erneut, wie unter [Ausprobieren](#trying-it-out) dargelegt. 

Fügen Sie in Ihrer **Visual Studio**-Projektmappe den folgenden Import in Ihrem `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs` hinzu:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Fügen Sie anschließend die folgenden Membervariablen in Ihrer `AzureSpatialAnchorsScript`-Klasse hinzu:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-58&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Anfügen eines lokalen Azure-Raumankers an den lokalen Anker

Richten wir die CloudSpatialAnchorSession des Azure-Raumankers ein. Wir beginnen damit, dass wir die folgende `InitializeSession()`-Methode in Ihrer `AzureSpatialAnchorsScript`-Klasse hinzufügen. Nach dem Aufrufen wird sichergestellt, dass eine Azure Spatial Anchors-Sitzung erstellt und beim Starten Ihrer App richtig initialisiert wird.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=169-197,200-204)]

Nun müssen wir Code schreiben, um Delegataufrufe zu verarbeiten. Im weiteren Verlauf werden wir diesen noch mehr hinzufügen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-221)]

Nun binden wir Ihre `initializeSession()`-Methode in Ihre `Start()`-Methode ein.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-88&highlight=12)]

Fügen Sie abschließend Ihrer `CreateAndSaveSphere()`-Methode den folgenden Code hinzu. Er fügt der Kugel, die wir in der realen Welt platzieren, einen lokalen Azure-Raumanker an.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-337,390&highlight=16-31)]

Vor dem Fortfahren müssen Sie einen Azure Spatial Anchors-Kontobezeichner und -schlüssel erstellen, falls Sie nicht bereits darüber verfügen. Befolgen Sie die Anleitung im folgenden Abschnitt, um diese Angaben zu erhalten.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Hochladen Ihres lokalen Ankers in die Cloud

Wenn Ihnen der Azure Spatial Anchors-Kontobezeichner und -schlüssel vorliegen, fügen Sie die `Account Id` in `SpatialAnchorsAccountId` und `Account Key` in `SpatialAnchorsAccountKey` ein.

Im letzten Schritt werden alle Komponenten zusammengefügt. Fügen Sie in Ihrer `SpawnNewAnchoredObject()`-Methode den folgenden Code hinzu. Er ruft die `CreateAnchorAsync()`-Methode auf, sobald Ihre Kugel erstellt wurde. Nach der Methodenrückgabe wird die Kugel mit dem unten angegebenen Code noch ein letztes Mal aktualisiert, indem ihre Farbe in Blau geändert wird.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-389&highlight=28-78)]

Führen Sie Ihre App ein weiteres Mal aus **Visual Studio** heraus aus. Bewegen Sie Ihren Kopf, und tippen Sie dann in die Luft, um Ihre Kugel zu platzieren. Nachdem genügend Frames vorhanden sind, ändert sich die Farbe der Kugel in Gelb, und der Upload in die Cloud beginnt. Nach Abschluss des Uploadvorgangs ändert sich die Farbe der Kugel in Blau. Optional könnten Sie auch das Ausgabefenster innerhalb von **Visual Studio** verwenden, um die Protokollmeldungen zu überwachen, die von Ihrer App gesendet werden. Sie können den empfohlenen Erstellungsstatus beobachten sowie den Ankerbezeichner, den die Cloud nach Abschluss des Uploadvorgangs zurückgibt.

> [!NOTE]
> Wenn Sie „DllNotFoundException: Die DLL 'AzureSpatialAnchors' kann nicht geladen werden: Das angegebene Modul wurde nicht gefunden.“ erhalten, sollten Sie Ihre Projektmappe **Bereinigen** und erneut **Erstellen**.

## <a name="locate-your-cloud-spatial-anchor"></a>Suchen nach Ihrem Raumanker für die Cloud

Nachdem Ihr Anker in die Cloud hochgeladen wurde, können wir erneut versuchen, danach zu suchen. Fügen wir nun den folgenden Code in Ihrer `HandleTap()`-Methode hinzu. Dieser Code bewirkt Folgendes:

* Er ruft `ResetSession()` auf, wodurch die `CloudSpatialAnchorSession` beendet und unsere vorhandene blaue Kugel vom Bildschirm entfernt wird.
* Er initialisiert `CloudSpatialAnchorSession` erneut. Dies machen wir, damit sichergestellt ist, dass der zu suchende Anker aus der Cloud stammt und es sich nicht um den von uns erstellten lokalen Anker handelt.
* Er erstellt einen **Watcher**, der nach dem Anker sucht, den wir in Azure Spatial Anchors hochgeladen haben.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-302&highlight=13-31,34-36)]

Nun fügen wir unserer `ResetSession()`- und `CleanupObjects()`-Methoden hinzu. Sie können sie unterhalb von `QueueOnUpdate()` einfügen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=114-167)]

Nun müssen wir Code einbinden, der aufgerufen wird, wenn der von uns abgefragte Anker gefunden wird. Fügen Sie innerhalb von `InitializeSession()` die folgenden Rückrufe hinzu:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=195-201&highlight=4-5)]

 
Jetzt fügen wir Code hinzu, der eine grüne Kugel erstellt und platziert, nachdem der CloudSpatialAnchor gefunden wurde. Auch das Tippen auf den Bildschirm wird wieder aktiviert, sodass Sie das gesamte Szenario wiederholen können: weiteren lokalen Anker erstellen, Upload durchführen und anschließend danach suchen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=223-262)]

Das ist alles! Führen Sie Ihre App ein letztes Mal aus **Visual Studio** heraus aus, um das gesamte Szenario von Anfang bis Ende auszuprobieren. Bewegen Sie Ihr Gerät, und platzieren Sie Ihre weiße Kugel. Bewegen Sie Ihren Kopf dann weiter, um Umgebungsdaten zu erfassen, bis sich die Farbe der Kugel in Gelb ändert. Ihr lokaler Anker wird hochgeladen, und Ihre Kugel nimmt die blaue Farbe an. Tippen Sie abschließend noch einmal auf Ihren Bildschirm, damit Ihr lokaler Anker entfernt wird. Anschließend führen wir eine Abfrage nach der Entsprechung in der Cloud durch. Fahren Sie mit dem Umherbewegen Ihres Geräts fort, bis Ihr Raumanker für die Cloud gefunden wurde. Eine grüne Kugel sollte an der richtigen Position angezeigt werden, und Sie können das gesamte Szenario noch einmal durchführen.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]