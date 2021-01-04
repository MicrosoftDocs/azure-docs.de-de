---
title: Installieren von Updates auf einem Microsoft Azure StorSimple Virtual Array | Microsoft Docs
description: Hier wird beschrieben, wie Sie die Webbenutzeroberfläche des StorSimple Virtual Arrays verwenden, um Updates und Hotfixes über das Portal anzuwenden.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3bd6f298ad2bb01503492b52c2d50dec82ec0ca5
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534041"
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Installieren von Updates auf einem StorSimple Virtual Array – Azure-Portal

## <a name="overview"></a>Übersicht

Dieser Artikel beschreibt die erforderlichen Schritte zum Installieren von Updates auf Ihrem StorSimple Virtual Array über die lokale Webbenutzeroberfläche oder das Azure-Portal. Sie müssen Softwareupdates oder -hotfixes installieren, um Ihr StorSimple Virtual Array auf dem neuesten Stand zu halten. 

Beachten Sie, dass das Gerät bei der Installation des Updates oder Hotfixes neu gestartet wird. Da es sich bei einem StorSimple Virtual Array um ein Gerät mit einem Einzelknoten handelt, werden alle aktuellen E/A-Vorgänge unterbrochen, was zu einer gewissen Ausfallzeit führt. 

Außerdem wird empfohlen, die Volumes oder Freigaben vor dem Anwenden eines Updates auf dem Host und dann auf dem Gerät offline zu nehmen. Dadurch wird das Risiko einer Datenbeschädigung minimiert.

> [!IMPORTANT]
> Wenn Sie Update 0.1 oder GA-Softwareversionen ausführen, müssen Sie die Hotfixmethode über die lokale Webbenutzeroberfläche verwenden, um Update 0.3 zu installieren. Wenn Sie Update 0.2 ausführen, empfehlen wir, die Software- und Treiberupdates über das klassische Azure-Portal zu installieren.
 

## <a name="use-the-local-web-ui"></a>Verwenden der lokalen Webbenutzeroberfläche

Die Verwendung der lokalen Webbenutzeroberfläche umfasst zwei Schritte:

* Herunterladen des Updates oder Hotfixes
* Installieren des Updates oder Hotfixes

### <a name="download-the-update-or-the-hotfix"></a>Herunterladen des Updates oder Hotfixes

Führen Sie die folgenden Schritte aus, um das Softwareupdate aus dem Microsoft Update-Katalog herunterzuladen.

#### <a name="to-download-the-update-or-the-hotfix"></a>So laden sie das Update oder den Hotfix herunter

1. Starten Sie Internet Explorer, und navigieren Sie zu [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

2. Falls Sie den Microsoft Update-Katalog zum ersten Mal auf diesem Computer verwenden, klicken Sie auf **Installieren** , wenn Sie zum Installieren des Add-Ons für den Microsoft Update-Katalog aufgefordert werden.

3. Geben Sie im Suchfeld des Microsoft Update-Katalogs die KB-Nummer (Knowledge Base) des Hotfixes ein, den Sie herunterladen möchten. Geben Sie **3182061** für Update 0.3 ein, und klicken Sie anschließend auf **Suchen**.
   
    Die Hotfixliste wird angezeigt, z.B. **StorSimple Virtual Array Update 0.3**.
   
    ![Katalog durchsuchen](./media/storsimple-virtual-array-install-update/download1.png)

4. Klicken Sie auf **Hinzufügen**. Das Update wird dem Warenkorb hinzugefügt.

5. Klicken Sie auf **Warenkorb anzeigen**.

6. Klicken Sie auf **Download**. Geben Sie einen lokalen Speicherort an, oder klicken Sie auf **Durchsuchen** , und navigieren Sie zum gewünschten lokalen Speicherort für den Download. Die Updates werden heruntergeladen und am angegebenen Speicherort in einem nach dem Update benannten Unterordner gespeichert. Der Ordner kann auch in eine Netzwerkfreigabe kopiert werden, auf die vom Gerät aus zugegriffen werden kann.

7. Öffnen Sie den kopierten Ordner. Es sollte eine Datei des Typs „Eigenständiges Microsoft Update-Paket“ (`WindowsTH-KB3011067-x64`) angezeigt werden. Diese Datei wird verwendet, um das Update oder den Hotfix zu installieren.

### <a name="install-the-update-or-the-hotfix"></a>Installieren des Updates oder Hotfixes

Stellen Sie vor der Update- oder Hotfixinstallation sicher, dass das Update oder der Hotfix entweder lokal auf Ihren Host heruntergeladen wurde bzw. dass auf das Update oder den Hotfix über eine Netzwerkfreigabe zugegriffen werden kann. 

Verwenden Sie diese Methode zum Installieren von Updates auf einem Gerät, auf dem GA- oder Update 0.1-Softwareversionen ausgeführt werden. Dieses Verfahren dauert weniger als zwei Minuten. Führen Sie die folgenden Schritte aus, um Updates oder Hotfixes zu installieren.

#### <a name="to-install-the-update-or-the-hotfix"></a>So installieren Sie das Update oder den Hotfix

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** > **Softwareupdate**.
   
    ![Der Screenshot zeigt die im Menü „Wartung“ ausgewählte Option „Softwareupdate“.](./media/storsimple-virtual-array-install-update/update1m.png)

2. Geben Sie in **Updatedateipfad** den Dateinamen für das Update oder den Hotfix ein. Sie können auch zur Installationsdatei des Updates oder Hotfixes navigieren, sofern sie auf einer Netzwerkfreigabe abgelegt wurde. Klicken Sie auf **Anwenden**.
   
    ![Der Screenshot zeigt auf der Seite „Softwareupdate“ das Textfeld „Updatedateipfad“.](./media/storsimple-virtual-array-install-update/update2m.png)

3. Es wird eine Warnung angezeigt. Da es sich hierbei um ein Gerät mit einem Einzelknoten handelt, wird das Gerät neu gestartet, und es kommt zu Ausfallzeit. Klicken Sie auf das Häkchensymbol.
   
   ![Der Screenshot zeigt ein Dialogfeld mit einer Warnung zur Ausfallzeit.](./media/storsimple-virtual-array-install-update/update3m.png)

4. Das Update wird gestartet. Nachdem das Gerät erfolgreich aktualisiert wurde, wird es neu gestartet. Auf die lokale Benutzeroberfläche kann währenddessen nicht zugegriffen werden.
   
    ![Der Screenshot zeigt eine Erfolgsmeldung für das Update.](./media/storsimple-virtual-array-install-update/update5m.png)

5. Nach dem Neustart wird die Seite **Anmelden** angezeigt. Um sicherzustellen, dass die Gerätesoftware aktualisiert wurde, wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** >  **Softwareupdate**. Die angezeigte Softwareversion sollte **10.0.0.0.0.10288.0** für Update 0.3 lauten.
   
   > [!NOTE]
   > Die Softwareversionen werden in der lokalen Webbenutzeroberfläche und im Azure-Portal etwas unterschiedlich angegeben. Beispiel: Die lokale Webbenutzeroberfläche gibt **10.0.0.0.0.10288** an, und das Azure-Portal **10.0.10288.0**, obwohl es sich um die gleiche Version handelt.
   
    ![Der Screenshot zeigt die Seite „Softwareupdate“ mit der aktuellen Softwareversion.](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Wir empfehlen Ihnen bei Ausführung von Update 0.2, Updates über das Azure-Portal zu installieren. Beim Verfahren über das Portal muss der Benutzer die Updates suchen, herunterladen und anschließend installieren. Für dieses Verfahren werden ca. 7 Minuten benötigt. Führen Sie die folgenden Schritte aus, um Updates oder Hotfixes zu installieren.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

Wechseln Sie nach Abschluss der Installation (wenn der Auftragsstatus bei 100 % ist) zum StorSimple-Geräte-Manager-Dienst. Wählen Sie **Geräte** aus. Wählen Sie anschließend aus der Liste der mit dem Dienst verbundenen Geräte das Gerät aus, das Sie aktualisieren möchten, und klicken Sie darauf. Wechseln Sie auf dem Blatt **Einstellungen** zum Abschnitt **Verwalten**, und wählen Sie **Geräteupdates** aus. Die angezeigte Softwareversion sollte **10.0.10288.0** lauten.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [StorSimple Virtual Array verwalten](storsimple-ova-web-ui-admin.md).

