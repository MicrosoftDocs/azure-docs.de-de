---
title: Installieren von Updates auf Geräten der Azure Data Box Gateway-Serie | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie über das Azure-Portal und die lokale Webbenutzeroberfläche auf einem Gerät der Azure Data Box Gateway-Serie Updates anwenden.
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 7fda10310a4bac085b248248d80d708dfa7f3ff7
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581049"
---
# <a name="update-your-azure-data-box-gateway"></a>Aktualisieren des Azure Data Box Gateway

In diesem Artikel sind die erforderlichen Schritte zum Installieren von Updates auf Ihrem Azure Data Box Gateway über die lokale Webbenutzeroberfläche oder das Azure-Portal beschrieben. Sie installieren Softwareupdates oder -hotfixes, um Ihr Data Box Gateway auf dem neuesten Stand zu halten.

> [!IMPORTANT]
>
> - Update **1911** entspricht der Softwareversion **1.6.1049.786** auf Ihrem Gerät. Informationen zu diesem Update finden Sie in den [Versionshinweisen](data-box-gateway-1911-release-notes.md).
>
> - Beachten Sie, dass das Gerät bei der Installation des Updates oder Hotfixes neu gestartet wird. Da es sich beim Data Box Gateway um ein Gerät mit einem Einzelknoten handelt, werden beim Update der Gerätesoftware alle laufenden E/A-Vorgänge unterbrochen, und es kommt zu einer Gerätedowntime von bis zu 30 Minuten.

Die einzelnen Schritte werden in den folgenden Abschnitten beschrieben.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Die Installation von Updates über das Azure-Portal wird empfohlen. Das Gerät sucht einmal täglich automatisch nach Updates. Sobald Updates verfügbar sind, wird im Portal eine Benachrichtigung angezeigt. Sie können die Updates dann herunterladen und installieren.

> [!NOTE]
> Stellen Sie sicher, dass das Gerät fehlerfrei ist und als Status **Online** angezeigt wird, bevor Sie mit der Installation der Updates fortfahren.

1. Sobald die Updates für Ihr Gerät verfügbar sind, wird eine Benachrichtigung angezeigt. Wählen Sie die Benachrichtigung aus, oder klicken Sie auf der oberen Befehlsleiste auf **Gerät aktualisieren**. Auf diese Weise können Sie Updates der Gerätesoftware anwenden.

    ![Screenshot der Startseite von Azure Data Box Gateway mit Hervorhebung der Optionen „Übersicht“ und „Gerät aktualisieren“.](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. Bestätigen Sie auf dem Blatt **Geräteupdates**, dass Sie die Lizenzbedingungen für neue Features in den Versionshinweisen gelesen haben.

    Sie können auswählen, ob Sie die Updates **herunterladen und installieren** oder nur **herunterladen** möchten. Sie können diese Updates dann später installieren.

    ![Screenshot des Dialogfelds „Geräteupdates“ mit Hervorhebung der Optionen „Akzeptieren“, „Verstehen“ und „Herunterladen und installieren“.](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    Wenn Sie die Updates herunterladen und installieren möchten, aktivieren Sie die Option zum automatischen Installieren der Updates nach Abschluss des Downloads.

    ![Screenshot des Dialogfelds „Geräteupdates“ mit Hervorhebung der Optionen „Akzeptieren“ und „Herunterladen“.](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. Der Download der Updates wird gestartet. Es wird eine Benachrichtigung angezeigt, dass der Download läuft.

    ![Screenshot der Benachrichtigung „Updates werden heruntergeladen und installiert“.](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    Außerdem wird im Azure-Portal ein Benachrichtigungsbanner angezeigt. Dies zeigt den Downloadstatus an.

    ![Screenshot der Startseite von Azure Data Box Gateway mit Hervorhebung der Option „Übersicht“ und des Benachrichtigungsbanners „Updates werden heruntergeladen“.](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    Sie können diese Benachrichtigung oder **Gerät aktualisieren** auswählen, um den ausführlichen Status des Updates anzuzeigen.

    ![Screenshot des Dialogfelds „Updates herunterladen und installieren“ mit Hervorhebung der Nachrichten „Status: In Bearbeitung“ und „Updates herunterladen“.](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. Nachdem der Download abgeschlossen ist, wird das Benachrichtigungsbanner aktualisiert, um den Abschluss anzuzeigen. Wenn Sie ausgewählt haben, dass die Updates heruntergeladen und installiert werden sollen, wird die Installation automatisch gestartet.

    ![Screenshot der Startseite von Azure Data Box Gateway mit Hervorhebung der Option „Übersicht“ und des Benachrichtigungsbanners „Updates wurden heruntergeladen“.](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    Wenn Sie Updates nur herunterladen möchten, wählen Sie die Benachrichtigung aus, um das Blatt **Geräteupdates** zu öffnen. Wählen Sie **Installieren** aus.
  
    ![Screenshot des Dialogfelds „Geräteupdates“ mit Hervorhebung der Option „Installieren“.](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. Es wird eine Benachrichtigung angezeigt, dass die Installation läuft.

    ![Screenshot der Startseite von Azure Data Box Gateway mit Hervorhebung der Option „Übersicht“ und der Nachricht „Updates werden heruntergeladen und installiert“.](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    Im Portal wird außerdem eine Informationsmeldung angezeigt, um darauf hinzuweisen, dass die Installation ausgeführt wird. <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. Da es sich um ein Gerät mit Einzelknoten handelt, wird das Gerät nach der Installation der Updates neu gestartet. Die kritische Warnung während des Neustarts weist darauf hin, dass das Gerät keinen Heartbeat mehr ausgibt.

    ![Screenshot der Startseite von Azure Data Box Gateway mit Hervorhebung der Option „Übersicht“ und des Benachrichtigungsbanners zu kritischen Warnungen.](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    Wählen Sie die Warnung aus, um das entsprechende Geräteereignis anzuzeigen.

    ![Screenshot des Abschnitts „Geräteereignisse“ mit Hervorhebung des Ereignisses „Von Ihrem Gerät wird kein Heartbeat mehr empfangen“.](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. Nachdem die Updates installiert wurden, ändert sich der Gerätestatus in **Online**.

    ![Screenshot der Startseite von Azure Data Box Gateway mit Hervorhebung der Option „Übersicht“ und des Onlinestatus.](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    Wählen Sie auf der oberen Befehlsleiste **Geräteupdates** aus. Vergewissern Sie sich, dass das Update erfolgreich installiert wurde und die entsprechende Version der Gerätesoftwareversion angezeigt wird.

    ![Screenshot des Dialogfelds „Geräteupdates“ mit Hervorhebung des Abschnitts „Installierte Softwareversion“.](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>Verwenden der lokalen Webbenutzeroberfläche

Die Verwendung der lokalen Webbenutzeroberfläche umfasst zwei Schritte:

- Herunterladen des Updates oder Hotfixes
- Installieren des Updates oder Hotfixes

Jeder dieser Schritte wird in den folgenden Abschnitten ausführlich erläutert.

### <a name="download-the-update-or-the-hotfix"></a>Herunterladen des Updates oder Hotfixes

Führen Sie die folgenden Schritte aus, um das Update herunterzuladen. Sie können das Update von einem von Microsoft bereitgestellten Speicherort oder aus dem Microsoft Update-Katalog herunterladen.

Führen Sie die folgenden Schritte aus, um das Softwareupdate aus dem Microsoft Update-Katalog herunterzuladen.

1. Starten Sie einen Browser, und navigieren Sie zu [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

   ![Screenshot des Microsoft Update-Katalogs in einem Browserfenster, in dessen Suchtextfeld „Data Box Gateway“ eingegeben ist. Die Adressleiste des Browsers und das Suchtextfeld sind hervorgehoben.](./media/data-box-gateway-apply-updates/download-update-1.png)

2. Geben Sie im Suchfeld des Microsoft Update-Katalogs die KB-Nummer (Knowledge Base) des Hotfixes oder der Nutzungsbedingungen des Updates ein, das Sie herunterladen möchten. Geben Sie z. B. **Azure Data Box Gateway** ein, und klicken Sie dann auf **Suchen**.

   Der Updateeintrag lautet **Azure Data Box Gateway 1911**.

   ![Screenshot des Microsoft Update-Katalogs in einem Browserfenster, in dem die Suchergebnisse für „Data Box Gateway“ angezeigt werden und hervorgehoben sind.](./media/data-box-gateway-apply-updates/download-update-2.png)

3. Wählen Sie **Herunterladen** aus. Als Download wird eine Datei mit dem Namen *SoftwareUpdatePackage.exe* angeboten, die dem Gerätesoftwareupdate entspricht. Laden Sie die Datei in einen Ordner auf dem lokalen System herunter. Sie können den Ordner auch in eine Netzwerkfreigabe kopieren, auf die vom Gerät aus zugegriffen werden kann.

   ![Screenshot des Dialogfelds „Herunterladen“ mit Hervorhebung des Links zur Datei „SoftwareUpdatePackage.exe“.](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>Installieren des Updates oder Hotfixes

Vergewissern Sie sich vor dem Installieren des Updates oder Hotfixes, dass Folgendes erfüllt ist:

- Das Update oder der Hotfix wurde lokal auf Ihren Host heruntergeladen, oder auf das Update oder den Hotfix kann über eine Netzwerkfreigabe zugegriffen werden.
- Der Gerätestatus ist fehlerfrei, wie auf der Seite **Übersicht** der lokalen Webbenutzeroberfläche angezeigt wird.

   ![Screenshot der lokalen Webbenutzeroberfläche von Data Box Gateway mit Hervorhebung von Option „Dashboard“ und Nachricht „Softwarestatus: Fehlerfrei“.](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

Dieser Vorgang dauert etwa 20 Minuten. Führen Sie die folgenden Schritte aus, um Updates oder Hotfixes zu installieren.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** > **Softwareupdate**. Notieren Sie sich die Softwareversion, die Sie ausführen.

   ![Screenshot der lokalen Webbenutzeroberfläche von Data Box Gateway mit Hervorhebung von Option „Softwareupdate“ und Nachricht „Aktuelle Softwareversion“.](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. Geben Sie den Pfad zur Updatedatei an. Sie können auch zur Installationsdatei des Updates navigieren, sofern sie auf einer Netzwerkfreigabe platziert wurde. Wählen Sie die Softwareupdatedatei mit dem Suffix *SoftwareUpdatePackage.exe* aus.

   ![Screenshot des Datei-Explorers mit Hervorhebung der Datei „SoftwareUpdatePackage.exe“.](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. Wählen Sie **Übernehmen**.

   ![Screenshot der lokalen Webbenutzeroberfläche von Data Box Gateway mit Hervorhebung der Option „Softwareupdate“, des Textfelds „Updatedateipfad“ und der Option „Update anwenden“.](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**, um fortzufahren. Da es sich um ein Gerät mit einem Einzelknoten handelt, wird das Gerät neu gestartet, und es kommt zu einer Downtime.

   ![Screenshot des Dialogfelds „Softwareupdate“ mit hervorgehobener Option „Ja“.](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. Das Update wird gestartet. Nachdem das Gerät erfolgreich aktualisiert wurde, wird es neu gestartet. Auf die lokale Benutzeroberfläche kann währenddessen nicht zugegriffen werden.

6. Nach dem Neustart wird die Seite **Anmelden** angezeigt. Um sicherzustellen, dass die Gerätesoftware aktualisiert wurde, wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** >  **Softwareupdate**. Als Softwareversion sollte **1.6.1049.786** angezeigt werden.

   ![Screenshot der lokalen Webbenutzeroberfläche von Data Box Gateway mit Hervorhebung von Option „Softwareupdate“ und aktualisierter Nachricht „Aktuelle Softwareversion“.](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über die [Verwaltung des Azure Data Box Gateway](data-box-gateway-manage-users.md)
