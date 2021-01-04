---
title: 'Schnellstart: Erstellen einer Android-App'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mit Spatial Anchors eine Android-App erstellen.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b7be4e257fc884c655fe380f69b08797a907fbbb
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96022648"
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

- Einen Windows- oder macOS-Computer mit <a href="https://developer.android.com/studio/" target="_blank">mindestens Android Studio 3.4</a>.
  - Unter Windows benötigen Sie auch <a href="https://git-scm.com/download/win" target="_blank">Git für Windows</a> und <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Unter macOS muss Git über Homebrew installiert werden. Geben Sie den folgenden Befehl in einer einzelnen Zeile am Terminal ein: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Führen Sie dann `brew install git` und `brew install git-lfs` aus.
  - Zum Erstellen des NDK-Beispiels müssen Sie auch NDK und die SDK Tools für CMake 3.6 oder höher in Android Studio installieren.
- Ein <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">für Entwickler geeignetes</a> und <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-fähiges</a> Android-Gerät.
  - Möglicherweise sind zusätzliche Gerätetreiber erforderlich, damit Ihr Computer mit Ihrem Android-Gerät kommunizieren kann. Weitere Informationen und Anweisungen finden Sie [hier](https://developer.android.com/studio/run/device.html).
- Ihre App muss für ARCore **1.11.0** geeignet sein.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öffnen des Beispielprojekts

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Laden Sie `arcore_c_api.h`[hier](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) herunter, und legen Sie das Projekt unter `Android\NDK\libraries\include` ab.

Initialisieren Sie im neu geklonten Repository Submodule, indem Sie den folgenden Befehl ausführen:

```console
git submodule update --init --recursive
```

---

Öffnen Sie Android Studio.

# <a name="java"></a>[Java](#tab/openproject-java)

Wählen Sie **Open an existing Android Studio project** (Vorhandenes Android Studio-Projekt öffnen), und wählen Sie das Projekt unter `Android/Java/` aus.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Wählen Sie **Open an existing Android Studio project** (Vorhandenes Android Studio-Projekt öffnen), und wählen Sie das Projekt unter `Android/NDK/` aus.

---

## <a name="configure-account-identifier-and-key"></a>Konfigurieren des Kontobezeichners und -schlüssels

Im nächsten Schritt wird die App zur Verwendung des Kontobezeichners und Kontoschlüssels konfiguriert. Diese haben Sie beim [Einrichten der Spatial Anchors-Ressource](#create-a-spatial-anchors-resource) in einen Text-Editor kopiert.

# <a name="java"></a>[Java](#tab/openproject-java)

Öffnen Sie `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Suchen Sie nach dem Feld `SpatialAnchorsAccountKey`, und ersetzen Sie `Set me` durch den Kontoschlüssel.

Suchen Sie nach dem Feld `SpatialAnchorsAccountId`, und ersetzen Sie `Set me` durch den Kontobezeichner.

Suchen Sie nach dem Feld `SpatialAnchorsAccountDomain`, und ersetzen Sie `Set me` durch die Kontodomäne.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Öffnen Sie `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Suchen Sie nach dem Feld `SpatialAnchorsAccountKey`, und ersetzen Sie `Set me` durch den Kontoschlüssel.

Suchen Sie nach dem Feld `SpatialAnchorsAccountId`, und ersetzen Sie `Set me` durch den Kontobezeichner.

Suchen Sie nach dem Feld `SpatialAnchorsAccountDomain`, und ersetzen Sie `Set me` durch die Kontodomäne.

---

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
