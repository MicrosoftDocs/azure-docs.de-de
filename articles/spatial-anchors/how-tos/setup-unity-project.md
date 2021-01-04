---
title: Installieren von Azure Spatial Anchors für Unity
description: Konfigurieren eines Unity-Projekts für die Verwendung von Azure Spatial Anchors
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2330310b0bf4e165af71208477db128650a787cf
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95496941"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurieren von Azure Spatial Anchors in einem Unity-Projekt

In diesem Leitfaden lernen Sie die ersten Schritte mit dem Azure Spatial Anchors SDK in Ihrem Unity-Projekt kennen.

## <a name="requirements"></a>Requirements (Anforderungen)

Azure Spatial Anchors unterstützt derzeit Unity 2019.4 (LTS) mit den folgenden Konfigurationen:

* Unity 2019.4 mit AR Foundation 3.1 wird in Azure Spatial Anchors 2.4.0 und höheren Versionen unterstützt.

## <a name="configuring-a-project"></a>Konfigurieren eines Projekts

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Hinzufügen der Unity Package Manager-Pakete zu Ihrem Projekt](#tab/UPMPackage)

Azure Spatial Anchors für Unity wird derzeit mithilfe von UPM-Paketen (Unity Package Manager) verteilt. Diese Pakete sind in unserer [NPM-Registrierung](https://bintray.com/microsoft/AzureMixedReality-NPM) zu finden. Weitere Informationen über die Arbeit mit bereichsbezogenen Paketregistrierungen in einem Unity-Projekt finden Sie [hier](https://docs.unity3d.com/Manual/upm-scoped.html) in der offiziellen Unity-Dokumentation.

#### <a name="add-the-registry-to-your-unity-project"></a>Hinzufügen der Registrierung zum Unity-Projekt

1. Navigieren Sie im Datei-Explorer zum `Packages`-Ordner Ihres Unity-Projekts. Öffnen Sie die Projektmanifestdatei `manifest.json` in einem Texteditor.
2. Fügen Sie am Anfang der Datei auf der gleichen Ebene wie dem Abschnitt `dependencies` den folgenden Eintrag hinzu, um die Azure Spatial Anchors-Registrierung in Ihr Projekt einzubeziehen. Der Eintrag `scopedRegistries` teilt Unity mit, wo nach den Azure Spatial Anchors-SDK-Paketen gesucht werden soll.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Hinzufügen der SDK-Pakete zu Ihrem Unity-Projekt

| Plattform | Paketname                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows |

1. Fügen Sie für jede Plattform (Android/iOS/HoloLens), die Sie in Ihrem Projekt unterstützen möchten, einen Eintrag mit dem Paketnamen und der Paketversion zum Abschnitt `dependencies` in Ihrem Projektmanifest hinzu. Ein Beispiel finden Sie weiter unten.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Speichern und schließen Sie die `manifest.json`-Datei. Wenn Sie zu Unity zurückkehren, erkennt Unity automatisch die Änderung am Projektmanifest und ruft die angegebenen Pakete ab. Sie können den Ordner `Packages` in der Projektansicht erweitern, um zu überprüfen, ob die richtigen Pakete importiert wurden.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Nur Android: Konfigurieren der Datei „maintemplate. gradle“

1. Wechseln Sie zu **Bearbeiten** > **Projekteinstellungen** > **Player**.
2. Wählen Sie im **Inspector-Bereich** für **Playereinstellungen** das **Android**-Symbol aus.
3. Aktivieren Sie unter dem Abschnitt **Erstellen** das Kontrollkästchen **Custom Main Gradle Template** (Benutzerdefinierte Gradle-Hauptvorlage), um eine benutzerdefinierte Gradle-Vorlage unter `Assets\Plugins\Android\mainTemplate.gradle` zu erstellen.
4. Öffne in einem Text-Editor die Datei `mainTemplate.gradle`.
5. Fügen Sie im Abschnitt `dependencies` die folgenden Abhängigkeiten ein:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Wenn alles fertig ist, sollte Ihr `dependencies`-Abschnitt ungefähr wie folgt aussehen:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[Importieren des Ressourcenpakets](#tab/UnityAssetPackage)

> [!WARNING]
> Die Verteilung des Unity-Ressourcenpakets des Azure Spatial Anchors-SDK wurde in Version 2.5.0 als veraltet markiert und ist seit Version 2.6.0 nicht mehr verfügbar.

1. Laden Sie die Datei `AzureSpatialAnchors.unitypackage` für die Version, die Sie als Ziel verwenden möchten, aus den [GitHub-Releases](https://github.com/Azure/azure-spatial-anchors-samples/releases) herunter.
2. Befolgen Sie [diese Anweisungen](https://docs.unity3d.com/Manual/AssetPackagesImport.html), um das Unity-Ressourcenpaket in Ihr Projekt zu importieren.

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vorgehensweise: Erstellen und Suchen von Ankern in Unity](./create-locate-anchors-unity.md)
