---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 74a9764187b15bddf1dc48fa2b7988217d31abce
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149586"
---
## <a name="troubleshooting-update-failures"></a>Problembehandlung bei Update-Fehlern
**Was geschieht, wenn über eine Meldung angezeigt wird, dass die Prüfungen vor dem Upgrade fehlgeschlagen sind?**

Wenn eine Vorabprüfung fehlschlägt, beachten Sie unbedingt die ausführliche Benachrichtigungsleiste unten auf der Seite. Dort erfahren Sie, welche Vorabprüfung fehlgeschlagen ist. Die folgende Abbildung zeigt ein Beispiel, in dem eine solche Benachrichtigung angezeigt wird. In diesem Fall sind die Integritätsprüfungen für Controller und für Hardware-Komponenten fehlgeschlagen. Im Abschnitt **Hardwarestatus** können Sie sehen, dass **Controller 0**- und **Controller 1**-Komponenten Ihre Aufmerksamkeit erfordern.

  ![Fehler bei der Vorabprüfung](./media/storsimple-install-troubleshooting/HCS_PreUpdateCheckFailed-include.png)

Sie müssen sicherstellen, dass beide Controller fehlerfrei und online sind. Sie müssen außerdem sicherstellen, dass alle Hardwarekomponenten im StorSimple-Gerät auf der Seite "Wartung" als fehlerfrei angezeigt werden. Sie können dann versuchen, Updates zu installieren. Wenn Sie Probleme mit Hardware-Komponenten nicht beheben können, müssen Sie sich an den Microsoft Support wenden, um die nächsten Schritte durchzuführen.

**Was geschieht, wenn Sie die Fehlermeldung "Updates konnten nicht installiert werden" erhalten und empfohlen wird, die Anleitung zur Update-Problembehandlung hinzuzuziehen, um die Fehlerursache zu bestimmen?**

Eine wahrscheinliche Ursache könnte sein, dass Sie nicht mit den Microsoft Update-Servern verbunden sind. In diesem Fall muss eine manuelle Überprüfung erfolgen. Wenn die Verbindung mit dem Updateserver unterbrochen wird, schlägt der Aktualisierungsauftrag fehl. Sie können das folgende Cmdlet in der Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts ausführen, um die Konnektivität zu prüfen:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Führen Sie das Cmdlet auf beiden Controllern aus.

Wenn Sie sichergestellt haben, dass die Verbindung vorhanden ist, und dieses Problem weiter besteht, wenden Sie sich an den Microsoft Support, um die nächsten Schritte durchzuführen.

**Was ist zu tun, wenn beim Aktualisieren Ihres Geräts auf Update 4 ein Fehler auftritt und auf beiden Controllern Update 4 ausgeführt wird?**

Ab Update 4 gilt: Wenn auf beiden Controllern die gleiche Softwareversion ausgeführt wird und ein Updatefehler auftritt, wechseln die Controller nicht in den Wiederherstellungsmodus. Dieser Fall kann eintreten, wenn der Gerätesoftware-Hotfix (Update erster Ordnung) erfolgreich auf beide Controller angewendet wurde, aber die Anwendung anderer Hotfixes (zweiter und dritter Ordnung) noch aussteht. Ab Update 4 wechseln die Controller nur dann in den Wiederherstellungsmodus, wenn auf den beiden Controllern unterschiedliche Softwareversionen ausgeführt werden. 

Sollte beim Benutzer ein Updatefehler auftreten, wenn auf beiden Controllern Update 4 ausgeführt wird, empfiehlt es sich, den Updatevorgang nach einer Wartezeit von mehreren Minuten erneut auszuführen. Besteht das Problem weiterhin, sollte sich der Benutzer an den Support von Microsoft wenden.
