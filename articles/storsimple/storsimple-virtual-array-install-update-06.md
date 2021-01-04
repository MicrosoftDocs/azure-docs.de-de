---
title: Installieren von Update 0.6 in StorSimple Virtual Array | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie die Webbenutzeroberfläche der StorSimple Virtual Array-Lösung verwenden, um Update 0.6 über das Azure-Portal und die Hotfixmethode anzuwenden.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 4b379bb76eaf7587c53ec55972ccee3f54a55f22
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534244"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Installieren von Update 0.6 in StorSimple Virtual Array

## <a name="overview"></a>Übersicht

Dieser Artikel beschreibt die erforderlichen Schritte zum Installieren von Update 0.6 für StorSimple Virtual Array über die lokale Webbenutzeroberfläche und das Azure-Portal. Sie installieren Softwareupdates oder -hotfixes, um Ihr StorSimple Virtual Array auf dem neuesten Stand zu halten.

Außerdem wird empfohlen, die Volumes oder Freigaben vor dem Anwenden eines Updates auf dem Host und dann auf dem Gerät offline zu nehmen. Dadurch wird das Risiko einer Datenbeschädigung minimiert. Nachdem die Volumes oder Freigaben offline sind, sollten Sie auch eine manuelle Sicherung des Geräts durchführen.

> [!IMPORTANT]
>
> - Update 0.6 entspricht der Softwareversion **10.0.10293.0** auf Ihrem Gerät. Informationen zu Neuigkeiten in diesem Update finden Sie in den [Versionsanmerkungen zum Update 0.6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Wenn Sie Update 0.2 oder höher ausführen, empfehlen wir, die Updates über das Azure-Portal zu installieren. Wenn Sie Update 0.1 oder GA-Softwareversionen ausführen, müssen Sie die Hotfixmethode über die lokale Webbenutzeroberfläche verwenden, um Update 0.6 zu installieren.
>
> - Beachten Sie, dass das Gerät bei der Installation des Updates oder Hotfixes neu gestartet wird. Da es sich bei einem StorSimple Virtual Array um ein Gerät mit einem Einzelknoten handelt, werden alle aktuellen E/A-Vorgänge unterbrochen, was zu einer gewissen Ausfallzeit führt.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Wir empfehlen Ihnen bei Ausführung von Update 0.2 und höheren Versionen, Updates über das Azure-Portal zu installieren. Beim Verfahren über das Portal muss der Benutzer die Updates suchen, herunterladen und anschließend installieren. Für dieses Verfahren werden ca. 7 Minuten benötigt. Führen Sie die folgenden Schritte aus, um Updates oder Hotfixes zu installieren.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Wechseln Sie nach Abschluss der Installation zum StorSimple-Geräte-Manager-Dienst. Wählen Sie **Geräte** und dann das Gerät aus, das Sie gerade aktualisiert haben. Wechseln Sie zu **Einstellungen > Verwaltung > Geräteupdates**. Die angezeigte Softwareversion sollte **10.0.10293.0** lauten.

## <a name="use-the-local-web-ui"></a>Verwenden der lokalen Webbenutzeroberfläche

Die Verwendung der lokalen Webbenutzeroberfläche umfasst zwei Schritte:

* Herunterladen des Updates oder Hotfixes
* Installieren des Updates oder Hotfixes

### <a name="download-the-update-or-the-hotfix"></a>Herunterladen des Updates oder Hotfixes

Führen Sie die folgenden Schritte aus, um das Softwareupdate aus dem Microsoft Update-Katalog herunterzuladen.

#### <a name="to-download-the-update-or-the-hotfix"></a>So laden sie das Update oder den Hotfix herunter

1. Starten Sie Internet Explorer, und navigieren Sie zu [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

2. Falls Sie den Microsoft Update-Katalog zum ersten Mal auf diesem Computer verwenden, klicken Sie auf **Installieren**, wenn Sie zum Installieren des Add-Ons für den Microsoft Update-Katalog aufgefordert werden.

3. Geben Sie im Suchfeld des Microsoft Update-Katalogs die KB-Nummer (Knowledge Base) des Hotfixes ein, den Sie herunterladen möchten. Geben Sie **4023268** für Update 0.6 ein, und klicken Sie anschließend auf **Suchen**.
   
    Die Hotfixliste wird angezeigt, z.B. **StorSimple Virtual Array Update 0.6**.
   
    ![Katalog durchsuchen](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klicken Sie auf **Download**.

5. Es sollten fünf herunterzuladende Dateien angezeigt werden. Laden Sie diese Dateien in einen Ordner herunter. Der Ordner kann auch in eine Netzwerkfreigabe kopiert werden, auf die vom Gerät aus zugegriffen werden kann.

6. Öffnen Sie den Ordner, in dem sich die Dateien befinden.
    ![Dateien im Paket](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Folgendes wird angezeigt:
    -  Eine eigenständige Paketdatei von Microsoft Update (`WindowsTH-KB3011067-x64`). Diese Datei wird verwendet, um die Gerätesoftware zu aktualisieren.
    - Eine Paketdatei für den Geneva Monitoring Agent (`GenevaMonitoringAgentPackageInstaller`). Diese Datei wird verwendet, um den MDS-Agent (Monitoring and Diagnostics Service) zu aktualisieren. Doppelklicken Sie auf die CAB-Datei. Es wird eine _MSI_-Datei angezeigt. Wählen Sie die Datei aus, klicken Sie mit der rechten Maustaste darauf, und **extrahieren** Sie dann die Datei. Sie verwenden die _MSI_-Datei, um den Agent zu aktualisieren.

        ![Extrahieren der Updatedatei für den MDS-Agent](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Sie müssen den MDS-Agent nicht aktualisieren, wenn Sie StorSimple Update 0.5 (0.0.10293.0) ausführen.

    - Drei Dateien mit wichtigen Windows-Sicherheitsupdates: `windows8.1-kb4012213-x64`, `windows8.1-kb3205400-x64` und `windows8.1-kb4019213-x64`


### <a name="install-the-update-or-the-hotfix"></a>Installieren des Updates oder Hotfixes

Stellen Sie vor der Update- oder Hotfixinstallation sicher, dass das Update oder der Hotfix entweder lokal auf Ihren Host heruntergeladen wurde bzw. dass auf das Update oder den Hotfix über eine Netzwerkfreigabe zugegriffen werden kann.

Verwenden Sie diese Methode zum Installieren von Updates auf einem Gerät, auf dem GA- oder Update 0.1-Softwareversionen ausgeführt werden. Für dieses Verfahren werden ca. 12 Minuten benötigt. Führen Sie die folgenden Schritte aus, um Updates oder Hotfixes zu installieren.

#### <a name="to-install-the-update-or-the-hotfix"></a>So installieren Sie das Update oder den Hotfix

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** > **Softwareupdate**. Notieren Sie sich die Softwareversion, die Sie ausführen. Wenn Sie Version **10.0.10290.0** ausführen, müssen Sie den MDS-Agent in Schritt 6 nicht aktualisieren.
   
    ![Der Screenshot zeigt die im Menü „Wartung“ ausgewählte Option „Softwareupdate“.](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. Geben Sie in **Updatedateipfad** den Dateinamen für das Update oder den Hotfix ein. Sie können auch zur Installationsdatei des Updates oder Hotfixes navigieren, sofern sie auf einer Netzwerkfreigabe abgelegt wurde. Klicken Sie auf **Anwenden**.
   
    ![Der Screenshot zeigt auf der Seite „Softwareupdate“ das Textfeld „Updatedateipfad“.](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Es wird eine Warnung angezeigt. Da das virtuelle Array ein Gerät mit einem Einzelknoten ist, wird das Gerät nach dem Anwenden des Updates neu gestartet, und es kommt zu einer Ausfallzeit. Klicken Sie auf das Häkchensymbol.
   
   ![Der Screenshot zeigt ein Dialogfeld mit einer Warnung zur Ausfallzeit.](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Das Update wird gestartet. Nachdem das Gerät erfolgreich aktualisiert wurde, wird es neu gestartet. Auf die lokale Benutzeroberfläche kann währenddessen nicht zugegriffen werden.
   
    ![Der Screenshot zeigt eine Erfolgsmeldung für das Update.](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Nach dem Neustart wird die Seite **Anmelden** angezeigt. Um sicherzustellen, dass die Gerätesoftware aktualisiert wurde, wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** >  **Softwareupdate**. Die angezeigte Softwareversion sollte **10.0.0.0.0.10293** für Update 0.6 lauten.
   
   > [!NOTE]
   > Die Softwareversionen werden in der lokalen Webbenutzeroberfläche und im Azure-Portal etwas unterschiedlich angegeben. Beispiel: Die lokale Webbenutzeroberfläche gibt **10.0.0.0.0.10293** an, und das Azure-Portal **10.0.10293.0**, obwohl es sich um dieselbe Version handelt.
   
    ![Der Screenshot zeigt die Seite „Softwareupdate“ mit der aktuellen Softwareversion.](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Überspringen Sie diesen Schritt, wenn Sie vor diesem Update StorSimple Virtual Array Update 0.5 (**10.0.10290.0**) ausgeführt haben. Sie haben sich die Softwareversion in Schritt 1 notiert, bevor Sie mit dem Update begonnen haben. Wenn Sie Update 0.5 ausführen, ist der MDS-Agent bereits auf dem neuesten Stand.

    Wenn Sie eine Softwareversion vor Update 0.5 ausführen, müssen Sie im nächsten Schritt den MDS-Agent aktualisieren. Wechseln Sie auf der Seite **Softwareupdate** zum **Updatedateipfad**, und suchen Sie die Datei `GenevaMonitoringAgentPackageInstaller.msi`. Wiederholen Sie die Schritte 2–4. Melden Sie sich nach dem Neustart des virtuellen Arrays an der lokalen Webbenutzeroberfläche an.

7. Wiederholen Sie die Schritte 2 bis 4, um mithilfe der Dateien `windows8.1-kb4012213-x64`, `windows8.1-kb3205400-x64` und `windows8.1-kb4019213-x64` die Windows-Sicherheitsfixes zu installieren. Das Virtual Array wird nach jeder Installation neu gestartet, und Sie müssen sich bei der lokalen Webbenutzeroberfläche neu anmelden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [StorSimple Virtual Array verwalten](storsimple-ova-web-ui-admin.md).

