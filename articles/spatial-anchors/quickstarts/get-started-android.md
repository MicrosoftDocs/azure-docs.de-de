---
title: 'Schnellstart: Erstellen einer Android-App mit Azure Spatial Anchors | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mit Spatial Anchors eine Android-App erstellen.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 03589745e6e9b40b937c49162e99035ce6c81423
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800046"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Schnellstart: Erstellen einer Android-App mit Azure Spatial Anchors

In dieser Schnellstartanleitung wird beschrieben, wie Sie mit [Azure Spatial Anchors](../overview.md) in Java oder C++/NDK eine Android-App erstellen. Azure Spatial Anchors ist ein plattformübergreifender Entwicklerdienst, mit dem Sie Mixed Reality-Umgebungen mit Objekten erstellen können, die ihre Position im Zeitverlauf geräteübergreifend beibehalten. Nach Abschluss des Vorgangs verfügen Sie über eine ARCore-Android-App, mit der ein räumlicher Anker gespeichert und abgerufen werden kann.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines Spatial Anchors-Kontos
> * Konfigurieren des Bezeichners und Kontoschlüssels für das Spatial Anchors-Konto
> * Bereitstellen und Ausführen auf einem Android-Gerät

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie für diese Schnellstartanleitung sicher, dass Sie über Folgendes verfügen:

- Einen Windows- oder macOS-Computer mit <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
  - Unter Windows benötigen Sie auch <a href="https://git-scm.com/download/win" target="_blank">Git für Windows</a>.
  - Unter macOS muss Git über Homebrew installiert werden. Geben Sie den folgenden Befehl in einer einzelnen Zeile am Terminal ein: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Führen Sie dann `brew install git` aus.
  - Zum Erstellen des NDK-Beispiels müssen Sie auch NDK und die CMake 3.6 SDK Tools in Android Studio installieren.
- Ein <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">für Entwickler geeignetes</a> und <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-fähiges</a> Android-Gerät.
- Ihre App muss für ARCore 1.7 geeignet sein.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öffnen des Beispielprojekts

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Beim Erstellen des Android-NDK-Beispiels müssen Sie `arcore_c_api.h` [hier](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.7.0/libraries/include/arcore_c_api.h) herunterladen und in `Android\NDK\libraries\include` einfügen.

Öffnen Sie Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Wählen Sie **Open an existing Android Studio project** (Vorhandenes Android Studio-Projekt öffnen), und wählen Sie das Projekt unter `Android/Java/` aus.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Wählen Sie **Open an existing Android Studio project** (Vorhandenes Android Studio-Projekt öffnen), und wählen Sie das Projekt unter `Android/NDK/` aus.

***

## <a name="configure-account-identifier-and-key"></a>Konfigurieren des Kontobezeichners und -schlüssels

Im nächsten Schritt wird die App zur Verwendung des Kontobezeichners und Kontoschlüssels konfiguriert. Diese haben Sie beim [Einrichten der Spatial Anchors-Ressource](#create-a-spatial-anchors-resource) in einen Text-Editor kopiert.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Öffnen Sie `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`.

Suchen Sie nach dem Feld `SpatialAnchorsAccountKey`, und ersetzen Sie `Set me` durch den Kontoschlüssel.

Suchen Sie nach dem Feld `SpatialAnchorsAccountId`, und ersetzen Sie `Set me` durch den Kontobezeichner.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Öffnen Sie `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Suchen Sie nach dem Feld `SpatialAnchorsAccountKey`, und ersetzen Sie `Set me` durch den Kontoschlüssel.

Suchen Sie nach dem Feld `SpatialAnchorsAccountId`, und ersetzen Sie `Set me` durch den Kontobezeichner.

***

## <a name="deploy-the-app-to-your-android-device"></a>Bereitstellen der App auf Ihrem Android-Gerät

Schalten Sie das Android-Gerät ein, melden Sie sich an, und stellen Sie dafür per USB-Kabel eine Verbindung mit dem PC her.

Wählen Sie in der Android Studio-Symbolleiste die Option **Ausführen**.

![Bereitstellen und Ausführen von Android Studio](./media/get-started-android/android-studio-deploy-run.png)

Wählen Sie das Android-Gerät im Dialogfeld **Select Deployment Target** (Bereitstellungsziel auswählen) aus, und wählen Sie anschließend **OK**, um die App auf dem Android-Gerät auszuführen.

Befolgen Sie in der App die Anleitung zum Anordnen und Abrufen eines Ankers.

Beenden Sie die App, indem Sie in der Android Studio-Symbolleiste **Beenden** wählen.

![Android Studio: Beenden](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Freigeben für Sitzungen und Geräte mit Azure Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
