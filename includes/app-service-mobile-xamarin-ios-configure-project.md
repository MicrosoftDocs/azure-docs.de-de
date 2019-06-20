---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178591"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Konfigurieren des iOS-Projekts in Xamarin Studio
1. Öffnen Sie in Xamarin.Studio die Datei **Info.plist**, und aktualisieren Sie die **Paket-ID** mit der Paket-ID, die Sie zuvor mit Ihrer neuen App-ID erstellt haben.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Scrollen Sie nach unten zu **Background Modes** (Hintergrundmodi). Aktivieren Sie die Kontrollkästchen **Enable Background Modes** (Hintergrundmodi aktivieren) und **Remote notifications** (Remotebenachrichtigungen).

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Doppelklicken Sie im Projektmappenbereich auf Ihr Projekt, um die **Projektoptionen**zu öffnen.
4. Wählen Sie unter **Build** die Option **iOS Bundle Signing** (iOS-Paketsignierung) sowie die Identität und das Bereitstellungsprofil aus, die Sie soeben für dieses Projekt eingerichtet haben.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Hierdurch wird sichergestellt, dass das Projekt das neue Profil für Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerätebereitstellung finden Sie unter [Xamarin Device Provisioning].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Konfigurieren des iOS-Projekts in Visual Studio
1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Eigenschaften**.
2. Klicken Sie auf den Eigenschaftenseiten auf die Registerkarte **iOS-Anwendung**, und aktualisieren Sie den **Bezeichner** mit der zuvor erstellten ID.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Wählen Sie auf der Registerkarte **iOS Bundle Signing** (iOS-Paketsignierung) die Identität und das Bereitstellungsprofil aus, die Sie gerade für dieses Projekt eingerichtet haben.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Hierdurch wird sichergestellt, dass das Projekt das neue Profil für Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerätebereitstellung finden Sie unter [Xamarin Device Provisioning].
4. Öffnen Sie „Info.plist“ per Doppelklick, und aktivieren Sie unter **Background Modes** (Hintergrundmodi) die Option **RemoteNotifications**.

[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
