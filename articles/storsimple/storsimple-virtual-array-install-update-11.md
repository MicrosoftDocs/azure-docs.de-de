---
title: Installieren von Update 1.1 für StorSimple Virtual Array | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie das Azure-Portal und die lokale Webbenutzeroberfläche für StorSimple Virtual Array verwenden, um Updates anzuwenden.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2018
ms.author: alkohli
ms.openlocfilehash: 709794dcccf9b100cbb11381ac34cfb86238b22b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533990"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>Installieren von Update 1.1 für Ihr StorSimple Virtual Array

## <a name="overview"></a>Übersicht

In diesem Artikel erfahren Sie, wie Sie Update 1.1 für Ihr StorSimple Virtual Array über die lokale Webbenutzeroberfläche und das Azure-Portal installieren.

Sie installieren Softwareupdates oder -hotfixes, um Ihr StorSimple Virtual Array auf dem neuesten Stand zu halten. Außerdem wird empfohlen, die Volumes oder Freigaben vor dem Anwenden eines Updates auf dem Host und dann auf dem Gerät offline zu nehmen. Dadurch wird das Risiko einer Datenbeschädigung minimiert. Nachdem die Volumes oder Freigaben offline sind, sollten Sie auch eine manuelle Sicherung des Geräts durchführen.

> [!IMPORTANT]
> - Update 1.1 entspricht der Softwareversion **10.0.10307.0** auf Ihrem Gerät. Informationen zu Neuigkeiten in diesem Update finden Sie in den [Versionsanmerkungen zum Update 1.1](storsimple-virtual-array-update-11-release-notes.md).
>
> - Beachten Sie, dass das Gerät bei der Installation des Updates oder Hotfixes neu gestartet wird. Da es sich bei einem StorSimple Virtual Array um ein Gerät mit einem Einzelknoten handelt, werden alle aktuellen E/A-Vorgänge unterbrochen, was zu einer gewissen Ausfallzeit führt.
>
> - Update 1.1 ist über das Azure-Portal nur verfügbar, wenn auf dem virtuellen Array Update 1 ausgeführt wird. Für virtuelle Arrays mit Update 0.6-Versionen müssen Sie zunächst Update 1.0 installieren und anschließend Update 1.1 anwenden.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Wir empfehlen Ihnen bei Ausführung von Update 0.2 und höheren Versionen, Updates über das Azure-Portal zu installieren. Beim Verfahren über das Portal muss der Benutzer die Updates suchen, herunterladen und anschließend installieren. Die Vorgehensweise zum Anwenden des Updates über das Azure-Portal ist abhängig von der Softwareversion auf Ihrem virtuellen Array.

 - Wenn auf Ihrem virtuellen Array Update 1 ausgeführt wird, installiert das Azure-Portal Update 1.1 (10.0.10307.0) direkt auf Ihrem Gerät. Dieser Vorgang dauert etwa 10 Minuten.
 - Falls auf Ihrem virtuellen Array Update 0.6 ausgeführt wird, erfolgt die Aktualisierung in zwei Stufen. Das Azure-Portal installiert zunächst Update 1.0 (10.0.10296.0) auf Ihrem Gerät. Daraufhin wird das virtuelle Array neu gestartet, und das Portal installiert Update 1.1 (10.0.10307.0) auf Ihrem Gerät. Dieser Vorgang dauert etwa 15 Minuten.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

Wechseln Sie nach Abschluss der Installation zum StorSimple-Geräte-Manager-Dienst. Wählen Sie **Geräte** und dann das Gerät aus, das Sie gerade aktualisiert haben. Wechseln Sie zu **Einstellungen > Verwaltung > Geräteupdates**. Als Softwareversion sollte **10.0.10307.0** angezeigt werden.

![Softwareversion nach dem Update](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>Verwenden der lokalen Webbenutzeroberfläche

Die Verwendung der lokalen Webbenutzeroberfläche umfasst zwei Schritte:

* Herunterladen des Updates oder Hotfixes
* Installieren des Updates oder Hotfixes

> [!IMPORTANT] 
> **Fahren Sie mit dieser Aktualisierung nur fort, wenn Sie Update 1 (10.0.10296.0) verwenden. Falls Sie Update 0.6 verwenden, [installieren Sie zunächst Update 1](storsimple-virtual-array-install-update-1.md) auf Ihrem Gerät, und wenden Sie dann Update 1.1 an.**

### <a name="download-the-update-or-the-hotfix"></a>Herunterladen des Updates oder Hotfixes

Führen Sie die folgenden Schritte aus, um Update 1.1 aus dem Microsoft Update-Katalog herunterzuladen.

#### <a name="to-download-the-update-or-the-hotfix"></a>So laden sie das Update oder den Hotfix herunter

1. Starten Sie Internet Explorer, und navigieren Sie zu [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

2. Falls Sie den Microsoft Update-Katalog zum ersten Mal auf diesem Computer verwenden, klicken Sie auf **Installieren**, wenn Sie zum Installieren des Add-Ons für den Microsoft Update-Katalog aufgefordert werden.

3. Geben Sie im Suchfeld des Microsoft Update-Katalogs die KB-Nummer (Knowledge Base) des Hotfixes ein, den Sie herunterladen möchten. Geben Sie **4337628** für Update 1.1 ein, und klicken Sie anschließend auf **Suchen**.
   
    Der Hotfixeintrag wird angezeigt, z.B. **StorSimple Virtual Array Update 1.1**.
   
    ![Katalog durchsuchen](./media/storsimple-virtual-array-install-update-11/download1.png)

4. Klicken Sie auf **Download**.

5. Laden Sie die beiden Dateien in einen Ordner herunter. Sie können den Ordner auch in eine Netzwerkfreigabe kopieren, auf die vom Gerät aus zugegriffen werden kann.

6. Öffnen Sie den Ordner, in dem sich die Dateien befinden.

    ![Dateien im Paket](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    Zwei Dateien werden angezeigt:
    -  Eine eigenständige Paketdatei von Microsoft Update (`WindowsTH-KB3011067-x64`). Diese Datei wird verwendet, um die Gerätesoftware zu aktualisieren.
    - Eine Datei mit kumulativen Updates für Juni: `Windows8.1-KB4284815-x64`. Weitere Informationen zu den Inhalten dieses Rollups finden Sie im [monatlichen Sicherheitsrollup für Juni](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815).

### <a name="install-the-update-or-the-hotfix"></a>Installieren des Updates oder Hotfixes

Vergewissern Sie sich vor dem Installieren des Updates oder Hotfixes, dass Folgendes erfüllt ist:

 - Das Update oder der Hotfix wurde lokal auf Ihren Host heruntergeladen, oder auf das Update oder den Hotfix kann über eine Netzwerkfreigabe zugegriffen werden.
 - Auf dem virtuellen Array wird Update 1 (10.0.10296.0) ausgeführt. Falls Sie Update 0.6 ausführen, [installieren Sie zunächst Update 1](storsimple-virtual-array-install-update-1.md) und anschließend Update 1.1.

Dieser Vorgang dauert etwa vier Minuten. Führen Sie die folgenden Schritte aus, um Updates oder Hotfixes zu installieren.

#### <a name="to-install-the-update-or-the-hotfix"></a>So installieren Sie das Update oder den Hotfix

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** > **Softwareupdate**. Notieren Sie sich die Softwareversion, die Sie ausführen. **Fahren Sie mit dieser Aktualisierung nur fort, wenn Sie Update 1 (10.0.10296.0) verwenden. Falls Sie Update 0.6 verwenden, [installieren Sie zunächst Update 1](storsimple-virtual-array-install-update-1.md) auf Ihrem Gerät, und wenden Sie dann Update 1.1 an.**
   
    ![Screenshot: Menü „Wartung“ mit ausgewählter Option „Softwareupdate“](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. Geben Sie in **Updatedateipfad** den Dateinamen für das Update oder den Hotfix ein. Sie können auch zur Installationsdatei des Updates oder Hotfixes navigieren, sofern sie auf einer Netzwerkfreigabe abgelegt wurde. Klicken Sie auf **Anwenden**.
   
    ![Screenshot: Textfeld „Updatedateipfad“ auf der Seite „Softwareupdate“](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. Es wird eine Warnung angezeigt. Da das virtuelle Array ein Gerät mit einem Einzelknoten ist, wird das Gerät nach dem Anwenden des Updates neu gestartet, und es kommt zu einer Ausfallzeit. Klicken Sie auf das Häkchensymbol.
   
   ![Screenshot: Dialogfeld mit Downtimewarnung](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. Das Update wird gestartet. Nachdem das Gerät erfolgreich aktualisiert wurde, wird es neu gestartet. Auf die lokale Benutzeroberfläche kann währenddessen nicht zugegriffen werden.
   
    ![Screenshot: Erfolgsmeldung für das Update](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. Nach dem Neustart wird die Seite **Anmelden** angezeigt. Um sicherzustellen, dass die Gerätesoftware aktualisiert wurde, wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** >  **Softwareupdate**. Als Softwareversion sollte **10.0.0.0.0.10307** für Update 1.1 angezeigt werden.
   
   > [!NOTE]
   > Die Softwareversionen werden in der lokalen Webbenutzeroberfläche und im Azure-Portal etwas unterschiedlich angegeben. Beispiel: Die lokale Webbenutzeroberfläche gibt **10.0.0.0.0.10307** an, das Azure-Portal aber **10.0.10307.0**, obwohl es sich um dieselbe Version handelt.
   
    ![Screenshot: Seite „Softwareupdate“ mit der aktuellen Softwareversion](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. Wiederholen Sie die Schritte 2 bis 4, um mithilfe der Dateien `Windows8.1-KB4284815-x64` die Windows-Sicherheitsfixes zu installieren. Das virtuelle Array wird nach der Installation neu gestartet, und Sie müssen sich bei der lokalen Webbenutzeroberfläche neu anmelden.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [StorSimple Virtual Array verwalten](storsimple-ova-web-ui-admin.md).
