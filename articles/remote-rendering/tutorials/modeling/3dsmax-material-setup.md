---
title: Einrichten von PBR-Materialien in 3ds Max
description: Es wird beschrieben, wie Sie PBR-Materialien (Physically Based Rendering, Rendern auf physikalischer Grundlage) in 3ds Max einrichten und im FBX-Format exportieren.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 12407d6344c69c747230e9db6fa4d53b4520dc82
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020278"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3ds-max"></a>Tutorial: Einrichten von Materialien für Rendern auf physikalischer Grundlage in 3ds Max

## <a name="overview"></a>Übersicht
In diesem Tutorial lernen Sie Folgendes:

>[!div class="checklist"]
>
> * Zuweisen von Materialien mit erweiterter Beleuchtung zu Objekten in einer Szene
> * Verarbeiten Sie die Instanziierung von Objekten und Materialien.
> * Exportieren Sie eine Szene im FBX-Format, und wählen Sie wichtige Optionen aus.

> [!Note]
> Die in diesem Tutorial beschriebene Vorgehensweise funktioniert in 3ds Max 2019 und 3ds Max 2020.
> Eine Änderung in der Weise, wie 3ds Max 2021 Bumpmaps exportiert, führt jedoch dazu, dass normale Maps vom Konvertierungsdienst nicht gefunden werden, wenn diese Version verwendet wird.

Das Erstellen von [PBR-Materialien](../../overview/features/pbr-materials.md) (Physically Based Rendering, Rendern auf physikalischer Grundlage) in 3ds Max ist unkompliziert. Der Vorgang ähnelt in vielerlei Hinsicht der PBR-Einrichtung in anderen Anwendungen zum Erstellen von Inhalten, wie z. B. Maya. Dieses Tutorial ist ein Leitfaden zur grundlegenden Einrichtung eines PBR-Shaders und für den FBX-Export für Azure Remote Rendering-Projekte.

Die Beispielszene in diesem Tutorial enthält eine Reihe von Polygonfeldobjekten. Ihnen werden verschiedene Materialien wie Holz, Metall, lackiertes Metall, Kunststoff und Gummi zugewiesen. Jedes Material enthält praktisch alle bzw. die meisten der folgenden Texturen:

* **Albedo**: Beschreibt die Farbzuordnung des Materials und wird auch als **Diffuse** (Streufarbe) und **BaseColor** bezeichnet.
* **Metallartigkeit**: bestimmt, ob ein Material metallisch ist bzw. welche Teile metallisch sind. 
* **Rauheit**: bestimmt, wie grob oder glatt eine Oberfläche ist.
Dies wirkt sich auch auf die Schärfe oder Unschärfe der Reflexionen und Highlights auf einer Oberfläche aus.
* **Normale**: Fügt einer Oberfläche Details hinzu, ohne dass weitere Polygone hinzugefügt werden. Beispiele für solche Details wären etwa Korrosion und Dellen bei einer Metalloberfläche oder die Maserung im Holz.
* **Umgebungsverdeckung**: dient zum Hinzufügen von Soft Shading und Kontaktschatten zu einem Modell. Hierbei handelt es sich um eine Graustufen-Map, mit der angegeben wird, welche Bereiche des Modells vollständige Beleuchtung (Weiß) bzw. vollständigen Schatten (Schwarz) erhalten.

## <a name="prepare-the-scene"></a>Vorbereiten der Szene
In 3ds Max wird zum Einrichten eines PBR-Materials der folgende Prozess verwendet.

Zunächst erstellen wir einige Feldobjekte, die jeweils für eine andere Art von Material stehen.

>[!TIP]
>Beachten Sie vor Beginn der Erstellung von Objekten für Remote Rendering, dass „Meter“ als Maßeinheit verwendet wird.  
>
>Daher ist es ratsam, die Systemeinheiten für Ihre Szene auf Meter festzulegen. Es ist auch ratsam, beim Exportieren einer Szene in den FBX-Exporteinstellungen **Units** (Einheiten) auf Meter festzulegen.

Im folgenden Screenshot sind die Schritte dargestellt, mit denen Sie die Systemeinheiten in 3ds Max auf Meter festlegen. 

1. Navigieren Sie im Hauptmenü zu **Customize** > **Units Setup** > **System Unit Setup** („Anpassen“ > „Einheiten einrichten“ > „System-Einheiten einrichten“). Wählen Sie unter **System Unit Scale** (System-Einheitenskala) die Option **Meters** (Meter) aus: ![Screenshot: Festlegen der Systemeinheiten](media/3dsmax/system-units.jpg)

1. Wir können nun mit dem Erstellen der Modelle beginnen. In der Beispielszene erstellen wir mehrere Feldobjekte, die jeweils für einen anderen Materialtyp stehen. Beispiele hierfür sind Metall, Gummi und Kunststoff. 

   >[!TIP]
   >Bei der Erstellung von Objekten besteht die bewährte Methode darin, diese jeweils mit aussagekräftigen Namen zu versehen. So können sie später leichter gefunden werden, wenn die Szene viele Objekte enthält.

1. Benennen Sie die Objekte so um, wie dies im folgenden Screenshot dargestellt ist: 

   ![Screenshot: Umbenennen von Objekten](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Zuweisen von Materialien

Nachdem unsere Szene nun einige Objekte enthält (in diesem Fall eine Reihe von Würfeln), können wir mit der PBR-Einrichtung beginnen:

1. Wählen Sie auf der Hauptsymbolleiste das Symbol **Material Editor** (Material-Editor) wie im folgenden Screenshot aus. Alternativ können Sie auch die Taste **M** auf der Tastatur drücken, um den Editor zu öffnen. Der Material-Editor verfügt über zwei Modi, die Sie in der Liste **Modes** (Modi) auswählen können: Modus **Compact Material Editor** (Kompakter Material-Editor) und Modus **Slate Material Editor** (Erweiterter Material-Editor). Da diese Szene relativ einfach ist, verwenden wir den kompakten Material-Editor.

1. Im Material-Editor werden einige Kugeln angezeigt. Diese Kugeln stellen die Materialien dar. Wir weisen jedem Objekt (Feld) der Szene eines dieser Materialien zu. Wählen Sie zum Zuweisen der Materialien zuerst eines der Objekte im Hauptanzeigebereich aus. Wählen Sie anschließend die erste Kugel im Material-Editor aus. Nach der Zuweisung zu einem Objekt wird das ausgewählte Material wie in der nächsten Abbildung dargestellt hervorgehoben.

1. Wählen Sie wie gezeigt die Option **Assign Material to Selection** (Material der Auswahl zuweisen) aus. Das Material wurde nun dem ausgewählten Objekt zugewiesen.

   ![Screenshot: Zuweisen von Materialien](media/3dsmax/assign-material.jpg)

    Im Material-Editor können Sie je nach Ihren Anforderungen zwischen vielen verschiedenen Materialtypen wählen. In der Regel wird der Materialtyp auf **Standard** festgelegt. Dies ist ein grundlegendes Material, das für die PBR-Einrichtung nicht geeignet ist. Daher müssen wir den Materialtyp in ein PBR-Material ändern. „Physical Material“ (Physikalisches Material) ist das bevorzugte 3ds Max-Material für Azure Remote Rendering-Projekte.

1. Wählen Sie im Material-Editor die Registerkarte **Standard** aus. Wählen Sie in **Material/Map Browser** (Material-/Map-Übersicht) die Option **Physical Material** (Physikalisches Material) aus. Mit dieser Aktion wird das zugewiesene Material **Standard** in ein „Physikalisches Material“ für PBR konvertiert.

   ![Screenshot: Ändern des Materials](media/3dsmax/physical-material.jpg)

    Im Material-Editor werden jetzt die Eigenschaften für „Physical Material“ (Physikalisches Material) angezeigt. Dies ist im folgenden Screenshot dargestellt. Sie können jetzt damit beginnen, dem Objekt Texturen zuzuweisen.

   ![Screenshot: Liste mit Texturen](media/3dsmax/textures-list.jpg)

Sie sehen, dass viele Maps und Texturen vorhanden sind, die Sie dem Material hinzufügen können. In diesem Tutorial verwenden wir nur fünf Texturfelder im Material.

>[!TIP]
>Es empfiehlt sich, Ihre Materialien entsprechend zu benennen (wie im obigen Screenshot dargestellt).

Die Generierung Ihrer Texturen kann je nach Präferenz oder Einsatzbereich variieren. Beispielsweise kann es sein, dass Sie Kacheltexturen nutzen möchten, die auf alle Objekte angewendet werden können. Es kann auch sein, dass bestimmte Teile eines Projekts oder Objekts eigene benutzerdefinierte Texturen aufweisen sollen. Hierfür kann die Verwendung von generischen Kacheltexturen ratsam sein, die online erhältlich sind. Sie können sie aber auch selbst in Apps wie Photoshop, Quixel Suite und Substance Suite erstellen.

Bevor wir mit dem Zuweisen von Texturen beginnen, müssen wir die Texturkoordinaten (UVW) des Objekts berücksichtigen. Es ist eine bewährte Methode, beim Anwendungen von Texturen auf ein Modell sicherzustellen, dass das Modell zugewiesen ist (Englisch: „unwrapped“). (Texturen werden ohne richtige UVW-Zuweisung nicht korrekt angezeigt.) Dies ist besonders wichtig für unsere Zwecke, weil wir in unserem Modell eine Umgebungsokklusion (UO) verwenden möchten. Im Gegensatz zum Stingray Shader in Maya verfügt „Physikalisches Material“ in 3ds Max nicht über ein dediziertes Texturfeld für die Umgebungsokklusion. Daher wenden wir die UO-Map auf ein anderes Feld an. Wir weisen einen eigenen UVW-Map-Kanal zu, damit die Verwendung separat von den anderen Texturen (z. B. Kacheltexturen) möglich ist. 

Zunächst weisen wir dem Modell den Modifikator „Unwrap UVW“ (UVW zuweisen) zu. Dies ist im folgenden Screenshot dargestellt. 

- Wählen Sie im ausgewählten Editor für die Objekteigenschaften die Liste mit den Modifikatoren aus. Scrollen Sie in der angezeigten Dropdownliste nach unten, und wählen Sie **Unwrap UVW** (UVW zuweisen) aus. Mit dieser Aktion wird der Modifikator „Unwrap UVW“ (UVW zuweisen) auf das Objekt angewendet.
![Screenshot: Auswählen von „Unwrap UVW“ (UVW zuweisen)](media/3dsmax/unwrap-modifier.jpg)

  Der Map-Kanal ist auf „1“ festgelegt. Sie führen den Großteil der Zuweisung normalerweise im Map-Kanal 1 durch. In diesem Fall wurde die Zuweisung für das Objekt ohne überlappende Texturkoordinaten (UV) durchgeführt.
![Screenshot: Zugewiesene Texturkoordinaten (UVW)](media/3dsmax/unwrapped-uvw.jpg)

Der nächste Schritt ist das Erstellen eines zweiten UV-Map-Kanals.

1. Schließen Sie den UV-Editor, falls er geöffnet ist. Ändern Sie im Menü **Edit UVs** (UVs bearbeiten) im Abschnitt **Channel** (Kanal) die Kanalnummer in **2**. Map-Kanal 2 ist der erwartete Kanal für UO-Maps. 

1. Im Dialogfeld **Channel Change Warning** (Warnung zur Änderung des Kanals) können Sie die vorhandenen UVs aus Kanal 1 in den neuen Kanal 2 **verschieben** (Move) oder die vorhandenen UVs **abbrechen** (Abandon), die automatisch einen neuen UV-Zuweisungsvorgang erstellen. Wählen Sie die Option **Abandon** (Abbrechen) nur aus, wenn Sie eine neue UV-Zuweisung für die UO-Map erstellen möchten, die sich von den UVs in Map-Kanal 1 unterscheidet. (Beispiel: Verwendung von Kacheltexturen in Kanal 1.) In diesem Tutorial verschieben wir die UVs aus Kanal 1 in Kanal 2, da wir den neuen UV-Kanal nicht bearbeiten müssen.

   >[!NOTE]
   >Selbst wenn Sie den UV-Zuweisungsvorgang aus Map-Kanal 1 in Map-Kanal 2 kopiert (verschoben) haben, können Sie an den UVs des neuen Kanals alle notwendigen Änderungen vornehmen, ohne den ursprünglichen Map-Kanal zu beeinträchtigen.

   ![Screenshot: Warnung zur Kanaländerung](media/3dsmax/channel-change.jpg)

Nachdem wir nun den neuen Map-Kanal erstellt haben, können wir zum physikalischen Material im Material-Editor zurückkehren und damit beginnen, unsere Texturen hinzuzufügen. Zuerst fügen wir die UO-Map hinzu, weil ein zusätzlicher Schritt erforderlich ist, damit sie richtig funktioniert. Nachdem die UO-Map mit unserem Material verknüpft wurde, müssen wir konfigurieren, dass Map-Kanal 2 verwendet werden soll.

Wie bereits erwähnt wurde, ist im physikalischen Material von 3ds Max kein dediziertes Feld für UO-Maps vorhanden. Stattdessen wenden wir die UO-Map auf das Feld **Diffuse Roughness** (Streufarbenrauheit) an.

1. Wählen Sie für „Physical Material“ (Physikalisches Material) in der Liste **Generic Maps** (Allgemeine Maps) das Feld **No Map** (Keine Map) neben **Diffuse Roughness** (Streufarbenrauheit) aus, und laden Sie Ihre UO-Map.

1. In den Eigenschaften der UO-Texturen ist der Map-Kanal standardmäßig auf **1** festgelegt. Ändern Sie diesen Wert in **2**. Mit dieser Aktion sind die Schritte zum Hinzufügen Ihrer UO-Map abgeschlossen.

   >[!IMPORTANT]
   >Dies ist ein wichtiger Schritt, und zwar vor allem, wenn sich Ihre UVs in Kanal 2 von denen in Kanal 1 unterscheiden. Die UO wird nämlich nicht richtig zugeordnet, wenn der falsche Kanal ausgewählt wird.

   ![Screenshot: Zuweisen einer UO-Map](media/3dsmax/assign-ao-map.jpg)

Wir weisen dem PBR-Material jetzt die Normalen-Map zu. Diese Aktion unterscheidet sich etwas vom Prozess in Maya. Die Normalen-Map wird nicht direkt auf das Feld für die Relief-Map angewendet. (Das physikalische Material von 3ds Max enthält kein Feld für eine Normalen-Map.) Stattdessen fügen Sie die Normalen-Map einem Modifikator vom Typ „Normalen-Map“ hinzu, der dem Feld der Normalen zugeordnet ist.

1. Wählen Sie in den Eigenschaften von „Physikalisches Material“ (im Material-Editor) im Abschnitt **Special Maps** (Spezielle Maps) das Feld **No Map** (Keine Map) neben **Bump Map** (Relief-Map) aus. 

1. Suchen Sie in **Material/Map Browser** (Material-/Map-Übersicht) nach **Normal Bump** (Normalen-Relief). Mit dieser Aktion wird dem Material der Modifikator **Normal Bump** (Normalen-Relief) hinzugefügt.

1. Wählen Sie im Modifikator **Normal Bump** (Normalen-Relief) neben **Normal** (Normale) die Option **No Map** (Keine Map) aus. Suchen und laden Sie Ihre Normalen-Map.

1. Stellen Sie sicher, dass die Methode auf **Tangent** (Tangente) festgelegt ist. (Dies sollte standardmäßig der Fall sein.) Wählen Sie bei Bedarf die Option **Flip Green (Y)** (Grün umkehren (Y)) aus.

   ![Screenshot: Auswählen von „Normal Bump“ (Normalen-Relief)](media/3dsmax/normal-bump.jpg)
   ![Screenshot: Laden der Normalen-Map](media/3dsmax/load-normal-map.jpg)

Nachdem die Normalen-Map richtig zugewiesen wurde, können wir die restlichen Texturen zuweisen, um die Einrichtung von „Physical Material“ (Physikalisches Material) abzuschließen. Der Prozess ist einfach. Es müssen keine besonderen Einstellungen beachtet werden. Im folgenden Screenshot sind alle Texturen dargestellt, die dem Material zugewiesen sind: 

![Screenshot: Alle Texturen, die dem Material zugewiesen sind](media/3dsmax/all-textures.jpg)

Nachdem die PBR-Materialien erstellt und eingerichtet wurden, können wir uns mit dem Instanziieren der Szene beschäftigen. In der Szene werden ähnliche Objekte instanziiert, z. B. Muttern, Bolzen, Schrauben und Scheiben. Alle Objekte, die gleich sind, können zu erheblichen Einsparungen in Bezug auf die Dateigröße führen. Instanzen eines Masterobjekts können über eigene Skalierungen, Drehungen und Transformationen verfügen und daher je nach Bedarf in Ihrer Szene angeordnet werden. In 3ds Max ist die Instanziierung ein einfacher Prozess.

1. Wählen Sie im Hauptanzeigebereich das bzw. die Objekte aus, die Sie exportieren möchten.

1. Halten Sie **UMSCHALT** gedrückt, und ziehen Sie die Objekte mithilfe des Transformationstools (Move (Verschieben)) nach oben. 

1. Legen Sie im Dialogfeld **Clone Options** (Klonoptionen) die Option **Object** (Objekt) auf **Instance** (Instanz) fest, und wählen Sie anschließend **OK** aus:

   ![Screenshot: Dialogfeld „Clone Options“ (Klonoptionen)](media/3dsmax/instance-object.jpg)

Bei dieser Aktion wird eine Instanz Ihres Objekts erstellt, die unabhängig vom übergeordneten Objekt und dessen anderen Instanzen verschoben, gedreht oder skaliert werden kann.

>[!IMPORTANT]
>Alle Änderungen, die Sie an einer Instanz vornehmen, während Sie sich im Modus für untergeordnete Objekte befinden, werden an alle Instanzen des Objekts übertragen. Wenn Sie also mit den Komponenten eines instanziierten Objekts arbeiten, z. B. Scheitelpunkten und Polygonflächen, sollten Sie überlegen, ob sich alle vorgenommenen Änderungen auch auf alle Instanzen auswirken sollen. Beachten Sie, dass jedes instanziierte Objekt jederzeit in ein eindeutiges Objekt transformiert werden kann. 

>[!TIP]
>Es ist ratsam, die Instanzen beim Instanziieren in Ihrer Szene zu erstellen. Kopien lassen sich später nur schwer durch instanziierte Objekte ersetzen. 

Bevor wir mit dem Exportvorgang fortfahren, sollten Sie zunächst noch die Szene bzw. das Objekt für die Freigabe verpacken. Wenn Sie das Objekt an einen Kunden oder ein Teammitglied weitergeben, sollte das Objekt im Idealfall mit so wenig Aufwand wie möglich geöffnet und wie gewünscht angezeigt werden können. Aus diesem Grund ist es wichtig, dass die Texturpfade der Objekte gemeinsam mit der Szenendatei aufbewahrt werden. Wenn die Texturpfade für Ihr Objekt auf ein lokales Laufwerk oder einen absoluten Pfad bzw. Speicherort verweisen, werden sie beim Öffnen auf einem anderen Computer nicht in die Szene geladen. Dies gilt auch, wenn sich die MAX-Datei im selben Ordner wie die Texturen befindet. Dieses Problem wird gelöst, indem die Texturpfade in 3ds Max relativ gemacht werden. Dies ist ein einfacher Vorgang.

1. Navigieren Sie auf der Hauptsymbolleiste zu **File** > **Reference** > **Asset Tracking Toggle** („Datei“ > „Referenzen“ > Umschalter „Medienverfolgung“). 

1. Im Fenster „Asset Tracking“ (Medienverfolgung) sind alle bzw. die meisten Texturen, die Sie auf Ihre PBR-Materialien angewendet haben, unter **Maps/Shaders** (Maps/Shader) angegeben.

1. Daneben in der Spalte **Full Path** (Vollständiger Pfad) wird der Pfad zum Speicherort Ihrer Texturen angezeigt (meist zum Speicherort auf dem lokalen Computer).

1. Darüber hinaus wird noch eine Spalte mit dem Namen **Status** angezeigt. In dieser Spalte ist angegeben, ob eine bestimmte Textur gefunden und auf Ihre Szene angewendet wurde. Eine Textur kann einen der folgenden Status haben: **OK**, **Found** (Gefunden) oder **File Missing** (Datei fehlt). Mit den ersten beiden Status wird angegeben, dass die Datei gefunden und geladen wurde. Der letzte Status bedeutet, dass die Datei von der Medienverfolgung nicht gefunden wurde.
 
   ![Screenshot: Fenster „Asset Tracking“ (Medienverfolgung)](media/3dsmax/texture-paths.jpg)

Unter Umständen stellen Sie fest, dass beim ersten Öffnen des Fensters „Asset Tracking“ (Medienverfolgung) nicht alle Texturen aufgeführt sind. Dies ist kein Problem. Wenn Sie den Prozess zum Suchen nach den Pfaden einmal oder zweimal ausführen, werden normalerweise alle Texturen einer Szene gefunden. Der Prozess für die Pfadsuche lautet wie folgt: 

1. Halten Sie im Fenster „Asset Tracking“ (Medienverfolgung) die Taste **UMSCHALT** gedrückt, und wählen Sie in der Liste **Maps/Shaders** (Maps/Sharder) die oberste Textur aus. Halten Sie **UMSCHALT** weiter gedrückt, und wählen Sie die letzte Textur der Liste aus. Mit dieser Aktion werden alle Texturen der Liste ausgewählt. Die ausgewählten Texturen werden blau hervorgehoben. (Siehe obigen Screenshot.)

1. Klicken Sie mit der rechten Maustaste auf die Auswahl, und wählen Sie **Set Path** (Pfad einstellen) aus.

1. Wählen Sie im Pfad **Specify Asset Path** (Medienpfad angeben) den lokalen Pfad zu Ihren Texturen aus, und ersetzen Sie ihn durch `.\`.  Klicken Sie auf **OK**. 

    Das Fenster „Asset Tracking“ (Medienverfolgung) wird aktualisiert. Dies ist im folgenden Screenshot dargestellt. Diese Aktualisierung kann eine Weile dauern. Die Dauer hängt davon ab, wie viele Texturen Ihre Szene enthält und wie umfangreich sie ist.
![Screenshot: Aktualisiertes Fenster „Asset Tracking“ (Medienverfolgung)](media/3dsmax/resolve-textures.jpg)

Beachten Sie, dass die Spalte **Vollständiger Pfad** jetzt leer ist. Dies bedeutet, dass die Szene nicht mehr an einem bestimmten (absoluten) Speicherort nach den relevanten Texturen sucht. Sie werden immer gefunden, sofern sich die MAX-Datei bzw. die zugehörige FBX-Datei im selben Ordner wie die Texturen befindet. 

>[!NOTE]
>Unter Umständen müssen Sie diesen Vorgang mehrmals wiederholen, damit alle Texturen und Pfade gefunden und aufgelöst werden können. Dies ist kein Problem. Wiederholen Sie den Vorgang einfach, bis alle relevanten Medien abgedeckt sind. In einigen Fällen können einige Dateien nicht gefunden werden. Wählen Sie in diesem Fall einfach alle Medien in der Liste und dann die Option **Remove Missing Paths** (Fehlende Pfade entfernen) aus. (Siehe obige Abbildung.)

## <a name="fbx-export"></a>FBX-Export

Nachdem wir die Texturpfade nun relativ gemacht haben, können wir uns dem FBX-Export zuwenden. Der Prozess ist auch hier einfach und kann auf verschiedene Arten durchgeführt werden. 

>[!TIP]
>Sofern Sie nicht Ihre gesamte Szene exportieren möchten, ist es ratsam, nur die benötigten Objekte für den Export auszuwählen. Bei ressourcenintensiven Szenen kann der Export sehr lange dauern.
>
>Falls Sie Modifikatoren wie TurboSmooth oder Open SubDiv verwendet haben, ist es ratsam, diese vor dem Export zu reduzieren (auszublenden), weil sie sonst beim Export zu Problemen führen können. Stellen Sie sicher, dass Sie Ihre Szene speichern, bevor Sie die Modifikatoren reduzieren. 

1. Wählen Sie in der Szene die Objekte aus, die Sie exportieren möchten. Navigieren Sie in der Hauptsymbolleiste zu **File** > **Export** > **Export Selected** („Datei“ > „Exportieren“ > „Auswahl exportieren“).

1. Geben Sie im Feld **Select File to Export** (Datei zum Exportieren auswählen) einen Namen für die Ausgabedatei ein, oder wählen Sie einen Namen aus. Wählen Sie in der Liste **Save as type** (Dateityp) die Option **Autodesk (*.fbx)** aus. Mit dieser Aktion wird das Fenster für den FBX-Export geöffnet.

  >[!IMPORTANT] 
  >Wenn Sie in Ihrer Szene Instanzen erstellt haben, ist es wichtig, dass Sie in den FBX-Exporteinstellungen die Option **Preserve Instances** (Instanzen beibehalten) auswählen. 

  ![Screenshot: FBX-Export](media/3dsmax/fbx-export.jpg)

  Beachten Sie, dass es mehrere Möglichkeiten für den Dateiexport gibt. Falls der FBX-Export zusammen mit den zugehörigen Texturdateien in einem Ordner bzw. Verzeichnis freigegeben werden soll, sind die im folgenden Screenshot dargestellten Einstellungen gut geeignet. 

   Wenn Sie umfangreiche Texturordner bzw. -verzeichnisse nicht gemeinsam mit der FBX-Datei freigeben möchten, können Sie die Texturen auch in die FBX-Datei einbetten. Falls Sie die Texturen einbetten, wird das gesamte Objekt, einschließlich Texturen, einer einzelnen FBX-Datei hinzugefügt. Hierbei wird Ihr Export zu einem einzelnen Objekt zusammengefasst, aber die FBX-Datei wird dadurch erheblich größer.

   >[!IMPORTANT]
   >Falls die sich ergebende FBX-Datei größer als 2,4 GB ist, sollte als Mindestversion in den FBX-Exporteinstellungen „2016“ oder höher angegeben sein. (Siehe obigen Screenshot.) Neuere Versionen verfügen über 64-Bit-Unterstützung und unterstützen daher größere Dateien.

1. Wählen Sie im Fenster für den FBX-Export die Option **Embed Media** (Medien einbetten) aus, wenn Sie die Szene mit den darin enthaltenen Texturen exportieren möchten. 

1. Wählen Sie die restlichen Einstellungen und dann **OK** aus:

    ![Screenshot: Einstellungen für FBX-Export](media/3dsmax/fbx-settings.jpg)


   Während eines FBX-Exports mit physikalischem Material wird nach dem Auswählen von **OK** im Fenster für den FBX-Export wahrscheinlich die folgende Warnung angezeigt: 

   ![Screenshot: Warnung zu Fehler bei Materialexport](media/3dsmax/export-warnings.jpg)

   In dieser Warnung werden Sie darauf hingewiesen, dass die exportierten Materialien unter Umständen nicht mit anderen Softwarepaketen kompatibel sind. Da „Physikalisches Material“ mit Azure Remote Rendering kompatibel ist, können Sie diese Warnung ignorieren. 

1. Wählen Sie **OK** aus, um den Prozess abzuschließen und das Fenster zu schließen.

## <a name="conclusion"></a>Zusammenfassung

Im Allgemeinen sehen diese Arten von Materialien realistischer aus, da sie auf den realen Gesetzen der Physik in Bezug auf das Licht basieren. Es entsteht ein zusätzlicher immersiver Effekt und der Eindruck, dass die Szene aus der realen Welt stammt.

## <a name="next-steps"></a>Nächste Schritte

Sie wissen jetzt, wie Sie Materialien mit erweiterter Beleuchtung für Objekte in einer Szene einrichten. Außerdem haben Sie gelernt, wie Sie Objekte in das von Azure Remote Rendering unterstützte FBX-Format exportieren. Der nächste Schritt besteht darin, die FBX-Datei zu konvertieren und in Azure Remote Rendering visuell darzustellen.

>[!div class="nextstepaction"]
>[Schnellstart: Konvertieren eines Modells für das Rendering](../../quickstarts\convert-model.md)
