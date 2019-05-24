---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141243"
---
Aufgrund ständiger Weiterentwicklung stimmt die Version des in Android Studio installierten Android-SDK unter Umständen nicht mit der Version im Code überein. In diesem Tutorial wird von Version 26 des Android-SDK ausgegangen. Das war die neueste Version zum Zeitpunkt des Verfassens. Mit neuen Ausgaben des SDK kann die Versionsnummer größer werden. Es empfiehlt sich, die neueste verfügbare Version zu verwenden.

Zwei Symptome nicht übereinstimmender Versionen sind:

- Wenn Sie das Projekt erstellen oder erneut erstellen, erhalten Sie möglicherweise eine Gradle-Fehlermeldung mit einem Hinweis wie `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Standard-Android-Objekte im Code, die auf Grundlage von `import` -Ausdrücken aufgelöst werden sollten, können Fehlermeldungen verursachen.

Wenn eines dieser Symptome auftritt, stimmt die Version des in Android Studio installierten Android SDK möglicherweise nicht mit dem SDK-Ziel des heruntergeladenen Projekts überein. Zur Überprüfung der Version nehmen Sie die folgenden Änderungen vor:

1. Klicken Sie in Android Studio auf **Tools** > **Android** > **SDK Manager**. Wenn nicht die neueste Version der SDK-Plattform installiert ist, so klicken Sie zur Installation. Notieren Sie sich die Versionsnummer.

2. Öffnen Sie auf der Registerkarte **Projektexplorer** unter **Gradle Scripts** (Gradle-Skripts) die Datei **build.gradle (Module: app)**. Vergewissern Sie sich, dass für **compileSdkVersion** und **targetSdkVersion** die neueste installierte SDK-Version festgelegt ist. Die `build.gradle` kann etwa so aussehen:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
