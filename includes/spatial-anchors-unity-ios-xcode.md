---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: d969b1639dbb39b920c985964abe1239f068d740
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185342"
---
Wählen Sie **Build** aus. Wählen Sie im dann geöffneten Bereich einen Ordner aus, in den das Xcode-Projekt exportiert wird.

   Nach Abschluss des Exports wird ein Ordner angezeigt, der das exportierte Xcode-Projekt enthält.

   > [!NOTE]
   > Wenn ein Fenster mit der Auswahl zwischen „Ersetzen“ und „Anfügen“ angezeigt wird, empfiehlt es sich, **Anfügen** auszuwählen, da dies schneller ist. Sie sollten nur dann **Ersetzen** auswählen, wenn Sie Ressourcen in Ihrer Szene ändern. Dies trifft beispielsweise zu, wenn Sie Beziehungen zwischen übergeordneten und untergeordneten Elementen hinzufügen, entfernen oder ändern oder wenn Sie Eigenschaften hinzufügen, entfernen oder ändern. Wenn Sie lediglich Änderungen am Quellcode vornehmen, sollte **Anfügen** ausreichend sein.

## <a name="open-the-xcode-project"></a>Öffnen des Xcode-Projekts

Nun können Sie Ihr Projekt `Unity-iPhone.xcodeproj` in Xcode öffnen. 

Sie können entweder Xcode starten und das exportierte `Unity-iPhone.xcodeproj`-Projekt öffnen oder das Projekt in Xcode starten, indem Sie den folgenden Befehl an dem Ort ausführen, an dem Sie das Projekt exportiert haben:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Wählen Sie den Hauptknoten **Unity-iPhone** aus, um die Projekteinstellungen anzuzeigen, und wählen Sie dann die Registerkarte **General** (Allgemein) aus.

Vergewissern Sie sich unter **Signing** (Signierung), dass **Automatically manage signing** (Signierung automatisch verwalten) aktiviert ist. Wenn dies nicht der Fall ist, aktivieren Sie diese Option, und setzen Sie dann die Buildeinstellungen zurück, indem Sie im angezeigten Bereich die Option **Enable Automatic** (Automatik aktivieren) auswählen.

Vergewissern Sie sich unter **Deployment Info** (Bereitstellungsinformationen), dass das Bereitstellungsziel (**Deployment Target**) auf **11.0** festgelegt ist.

## <a name="deploy-the-app-to-your-ios-device"></a>Bereitstellen der App auf Ihrem iOS-Gerät

Verbinden Sie das iOS-Gerät mit dem Mac, und legen Sie das **aktive Schema** auf Ihr iOS-Gerät fest.

   ![Screenshot: Schaltfläche „My iPhone“ (Mein iPhone) zum Auswählen des Geräts](./media/spatial-anchors-unity/select-device.png)

Wählen Sie **Build and then run the current scheme** (Aktuelles Schema erstellen und dann ausführen) aus.

   ![Screenshot der Pfeilschaltfläche zum Bereitstellen und Ausführen](./media/spatial-anchors-unity/deploy-run.png)
