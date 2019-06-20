---
title: Installieren von Update 1.0 für StorSimple Virtual Array | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie die Webbenutzeroberfläche der StorSimple Virtual Array-Lösung verwenden, um Updates und Hotfixes über das Azure-Portal anzuwenden.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fa53213e577028628d48db91704578e23888f2a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61411575"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Installieren von Update 1.0 für Ihr StorSimple Virtual Array

## <a name="overview"></a>Übersicht

In diesem Artikel erfahren Sie, wie Sie Update 1.0 für Ihr StorSimple Virtual Array über die lokale Webbenutzeroberfläche und über das Azure-Portal installieren.

Sie installieren Softwareupdates oder -hotfixes, um Ihr StorSimple Virtual Array auf dem neuesten Stand zu halten. Außerdem wird empfohlen, die Volumes oder Freigaben vor dem Anwenden eines Updates auf dem Host und dann auf dem Gerät offline zu nehmen. Dadurch wird das Risiko einer Datenbeschädigung minimiert. Nachdem die Volumes oder Freigaben offline sind, sollten Sie auch eine manuelle Sicherung des Geräts durchführen.

> [!IMPORTANT]
> - Update 1.0 entspricht der Softwareversion **10.0.10296.0** auf Ihrem Gerät. Informationen zu Neuigkeiten in diesem Update finden Sie in den [Versionsanmerkungen zum Update 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Beachten Sie, dass das Gerät bei der Installation des Updates oder Hotfixes neu gestartet wird. Da es sich bei einem StorSimple Virtual Array um ein Gerät mit einem Einzelknoten handelt, werden alle aktuellen E/A-Vorgänge unterbrochen, was zu einer gewissen Ausfallzeit führt.
>
> - Update 1 ist nur über das Azure-Portal verfügbar, wenn das virtuelle Array über Update 0.6 verfügt. Für virtuelle Arrays mit einer Version vor Update 0.6 müssen Sie zunächst Update 0.6 und anschließend Update 1 installieren.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Wir empfehlen Ihnen bei Ausführung von Update 0.2 und höheren Versionen, Updates über das Azure-Portal zu installieren. Beim Verfahren über das Portal muss der Benutzer die Updates suchen, herunterladen und anschließend installieren. Die Vorgehensweise zum Anwenden des Updates über das Azure-Portal ist abhängig von der Softwareversion auf Ihrem virtuellen Array.

 - Wenn auf Ihrem virtuellen Array Update 0.6 ausgeführt wird, wird Update 1 (10.0.10296.0) direkt auf Ihrem Gerät installiert. Für dieses Verfahren werden ca. 7 Minuten benötigt.
 - Falls auf Ihrem virtuellen Array eine Version vor Update 0.6 ausgeführt wird, erfolgt die Aktualisierung in zwei Stufen. Das Azure-Portal installiert zunächst Update 0.6 (10.0.10293.0) auf Ihrem Gerät. Daraufhin wird das virtuelle Array neu gestartet, und das Portal installiert Update 1 (10.0.10296.0) auf Ihrem Gerät. Dieser Vorgang dauert etwa 15 Minuten.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Wechseln Sie nach Abschluss der Installation zum StorSimple-Geräte-Manager-Dienst. Wählen Sie **Geräte** und dann das Gerät aus, das Sie gerade aktualisiert haben. Wechseln Sie zu **Einstellungen > Verwaltung > Geräteupdates**. Als Softwareversion sollte **10.0.10296.0** angezeigt werden.

![Softwareversion nach dem Update](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Verwenden der lokalen Webbenutzeroberfläche

Die Verwendung der lokalen Webbenutzeroberfläche umfasst zwei Schritte:

* Herunterladen des Updates oder Hotfixes
* Installieren des Updates oder Hotfixes

> [!IMPORTANT] 
> **Fahren Sie mit dieser Aktualisierung nur fort, wenn Sie Update 0.6 (10.0.10293.0) verwenden. Sollten Sie eine ältere Version verwenden, [installieren Sie zunächst das Update 0.6](storsimple-virtual-array-install-update-06.md) auf Ihrem Gerät, und wenden Sie dann Update 1 an.**

### <a name="download-the-update-or-the-hotfix"></a>Herunterladen des Updates oder Hotfixes

Wenn auf Ihrem virtuellen Array Update 0.6 ausgeführt wird, führen Sie die folgenden Schritte aus, um Update 1 aus dem Microsoft Update-Katalog herunterzuladen.

#### <a name="to-download-the-update-or-the-hotfix"></a>So laden sie das Update oder den Hotfix herunter

1. Starten Sie Internet Explorer, und navigieren Sie zu [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

2. Falls Sie den Microsoft Update-Katalog zum ersten Mal auf diesem Computer verwenden, klicken Sie auf **Installieren**, wenn Sie zum Installieren des Add-Ons für den Microsoft Update-Katalog aufgefordert werden.

3. Geben Sie im Suchfeld des Microsoft Update-Katalogs die KB-Nummer (Knowledge Base) des Hotfixes ein, den Sie herunterladen möchten. Geben Sie **4047203** für Update 1.0 ein, und klicken Sie anschließend auf **Suchen**.
   
    Der Hotfixeintrag wird angezeigt (beispielsweise **StorSimple Virtual Array Update 1.0**).
   
    ![Katalog durchsuchen](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Klicken Sie auf **Download**.

5. Laden Sie die beiden Dateien in einen Ordner herunter. Sie können den Ordner auch in eine Netzwerkfreigabe kopieren, auf die vom Gerät aus zugegriffen werden kann.

6. Öffnen Sie den Ordner, in dem sich die Dateien befinden.

    ![Dateien im Paket](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Zwei Dateien werden angezeigt:
    -  Eine eigenständige Paketdatei von Microsoft Update (`WindowsTH-KB3011067-x64`). Diese Datei wird verwendet, um die Gerätesoftware zu aktualisieren.
    - Eine Datei mit kumulativen Updates für August (`windows8.1-kb4034681-x64`). Weitere Informationen zu den Inhalten dieses Rollups finden Sie im [monatlichen Sicherheitsrollup für August](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Installieren des Updates oder Hotfixes

Vergewissern Sie sich vor dem Installieren des Updates oder Hotfixes, dass Folgendes erfüllt ist:

 - Das Update oder der Hotfix wurde lokal auf Ihren Host heruntergeladen, oder auf das Update oder den Hotfix kann über eine Netzwerkfreigabe zugegriffen werden.
 - Auf dem virtuellen Array wird Update 0.6 (10.0.10293.0) ausgeführt. Falls Sie eine Version vor Update 0.6 verwenden, [installieren Sie zunächst Update 0.6](storsimple-virtual-array-install-update-06.md) und anschließend Update 1.

Dieser Vorgang dauert etwa vier Minuten. Führen Sie die folgenden Schritte aus, um Updates oder Hotfixes zu installieren.

#### <a name="to-install-the-update-or-the-hotfix"></a>So installieren Sie das Update oder den Hotfix

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** > **Softwareupdate**. Notieren Sie sich die Softwareversion, die Sie ausführen. **Fahren Sie mit dieser Aktualisierung nur fort, wenn Sie Update 0.6 (10.0.10293.0) verwenden. Sollten Sie eine ältere Version verwenden, [installieren Sie zunächst das Update 0.6](storsimple-virtual-array-install-update-06.md) auf Ihrem Gerät, und wenden Sie dann Update 1 an.**
   
    ![Gerät aktualisieren](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. Geben Sie in **Updatedateipfad**den Dateinamen für das Update oder den Hotfix ein. Sie können auch zur Installationsdatei des Updates oder Hotfixes navigieren, sofern sie auf einer Netzwerkfreigabe abgelegt wurde. Klicken Sie auf **Anwenden**.
   
    ![Gerät aktualisieren](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Es wird eine Warnung angezeigt. Da das virtuelle Array ein Gerät mit einem Einzelknoten ist, wird das Gerät nach dem Anwenden des Updates neu gestartet, und es kommt zu einer Ausfallzeit. Klicken Sie auf das Häkchensymbol.
   
   ![Gerät aktualisieren](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Das Update wird gestartet. Nachdem das Gerät erfolgreich aktualisiert wurde, wird es neu gestartet. Auf die lokale Benutzeroberfläche kann währenddessen nicht zugegriffen werden.
   
    ![Gerät aktualisieren](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Nach dem Neustart wird die Seite **Anmelden** angezeigt. Um sicherzustellen, dass die Gerätesoftware aktualisiert wurde, wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** >  **Softwareupdate**. Als Softwareversion sollte **10.0.0.0.0.10296** (für Update 1.0) angezeigt werden.
   
   > [!NOTE]
   > Die Softwareversionen werden in der lokalen Webbenutzeroberfläche und im Azure-Portal etwas unterschiedlich angegeben. Beispiel: Die lokale Webbenutzeroberfläche gibt **10.0.0.0.0.10296** an, das Azure-Portal aber **10.0.10296.0**, obwohl es sich um dieselbe Version handelt.
   
    ![Gerät aktualisieren](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Wiederholen Sie die Schritte 2 bis 4, um mithilfe der Dateien `windows8.1-kb4012213-x64` die Windows-Sicherheitsfixes zu installieren. Das virtuelle Array wird nach der Installation neu gestartet, und Sie müssen sich bei der lokalen Webbenutzeroberfläche neu anmelden.

> [!NOTE]
> Falls Sie Update 1 direkt auf ein Gerät mit einer Version vor Update 0.6 angewendet haben, fehlen einige Updates. Erkundigen Sie sich beim Support von Microsoft nach den weiteren Schritten.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [StorSimple Virtual Array verwalten](storsimple-ova-web-ui-admin.md).
