---
title: Installieren von Azure Spatial Anchors für Unity
description: Konfigurieren eines Unity-Projekts für die Verwendung von Azure Spatial Anchors
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56532c17c91e6c703a6acd7990bbae47cd248165
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576564"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurieren von Azure Spatial Anchors in einem Unity-Projekt

In diesem Leitfaden lernen Sie die ersten Schritte mit dem Azure Spatial Anchors SDK in Ihrem Unity-Projekt kennen.

## <a name="project-requirements"></a>Anforderungen des Projekts

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Konfigurieren eines Projekts

Bevor Sie das Azure Spatial Anchors SDK in Ihr Unity-Projekt einschließen, stellen Sie sicher, dass Sie die [erforderlichen](#project-requirements) Pakete über den Unity Package Manager installieren.

### <a name="download-asa-packages"></a>Herunterladen von ASA-Paketen
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>Importieren von ASA-Paketen
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Nur Android: Konfigurieren der Datei „maintemplate. gradle“

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

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vorgehensweise: Erstellen und Suchen von Ankern in Unity](./create-locate-anchors-unity.md)
