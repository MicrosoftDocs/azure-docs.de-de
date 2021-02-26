---
title: Migrieren von Daten von einem Gerät der Serie StorSimple 5000 - 7000 zu einem Gerät der Serie 8000 | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Migrieren von Daten auf einem Gerät der StorSimple 5000-7000-Serie zu einem Gerät der 8000-Serie sowie die Voraussetzungen für den Migrationsprozess.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 58b7b67842e9ba385b34ea4d8fdbcac190076218
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428165"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrieren von Daten von einem Gerät der Serie StorSimple 5000 - 7000 zu einem Gerät der Serie 8000

> [!IMPORTANT]
> - Ab dem 31. Juli 2019 wird die StorSimple-Serie 5000/7000 nicht mehr unterstützt. Es wird empfohlen, dass Kunden der StorSimple-Serie 5000/7000 zu einer der in diesem Dokument beschriebenen Alternativen migrieren.
> - Die Migration ist derzeit ein unterstützter Vorgang. Wenn Sie Daten von Ihrem Gerät der Serie StorSimple 5000 - 7000 zu einem Gerät der Serie 8000 migrieren möchten, ist es erforderlich, dass Sie die Migration zusammen mit dem Microsoft-Support planen. Der Microsoft-Support aktiviert Ihr Abonnement dann für die Migration. Weitere Informationen finden Sie unter [Öffnen eines Supporttickets](storsimple-8000-contact-microsoft-support.md).
> - Nachdem Sie den Service Request gesendet haben, kann es einige Wochen dauern, bis der Migrationsplan ausgeführt und die eigentliche Migration gestartet wird.
> - Bevor Sie sich an den Microsoft-Support wenden, sollten Sie sich mit den [Voraussetzungen für die Migration](#migration-prerequisites) in diesem Artikel vertraut machen und sicherstellen, dass sie erfüllt sind.

## <a name="overview"></a>Übersicht

In diesem Artikel wird das Migrationsfeature vorgestellt, mit denen Kunden mit Geräten der Serie StorSimple 5000 - 7000 ihre Daten zu einem physischen Gerät der Serie StorSimple 8000 oder einem 8010/8020-Cloudgerät (Cloud Appliance) migrieren können. Außerdem enthält der Artikel Links zu einer herunterladbaren Schritt-für-Schritt-Anleitung zur Migration von Daten von einem Legacygerät der Serie 5000 - 7000 zu einem physischen Gerät oder Cloudgerät der Serie 8000.

Dieser Artikel gilt sowohl für das lokale Gerät der Serie 8000 als auch für die StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Vergleich von Migrationsfeature und hostseitiger Migration

Sie können Ihre Daten verschieben, indem Sie das Migrationsfeature verwenden oder eine hostseitige Migration durchführen. In diesem Abschnitt werden die Besonderheiten der einzelnen Methoden beschrieben, z.B. die Vor- und Nachteile. Verwenden Sie diese Informationen, um zu ermitteln, welche Methode Sie zum Migrieren Ihrer Daten nutzen möchten.

Das Migrationsfeature simuliert einen Prozess für die Notfallwiederherstellung von der Serie 7000/5000 zur Serie 8000. Mit diesem Feature können Sie die Daten aus dem Format der Serie 5000/7000 zum Format der Serie 8000 in Azure migrieren. Der Migrationsprozess wird mit dem StorSimple-Migrationstool initiiert. Das Tool startet den Download und die Konvertierung von Sicherungsmetadaten auf dem Gerät der Serie 8000 und verwendet anschließend die letzte Sicherung, um die Volumes auf dem Gerät verfügbar zu machen.

| Vorteile                                                                                                                                     |Nachteile                                                                                                                                                              |
|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Der Migrationsprozess behält den Verlauf der Sicherungen bei, die für die Serie 5000/7000 erstellt wurden.                                               | Wenn Benutzer versuchen, auf die Daten zuzugreifen, werden die Daten bei dieser Migration aus Azure heruntergeladen. Hierbei fallen Kosten für das Herunterladen der Daten an.                                     |
| Auf Hostseite werden keine Daten migriert.                                                                                                     | Für den Prozess kommt es zwischen dem Start der Sicherung und der letzten Sicherung für die Serie 8000 zu einer Ausfallzeit (Schätzung mit dem Migrationstool möglich). |
| Bei diesem Prozess werden alle Richtlinien, Bandbreitenvorlagen, die Verschlüsselung und andere Einstellungen auf Geräten der Serie 8000 beibehalten.                      | Beim Zugriff durch Benutzer werden nur die Daten abgerufen, die für den Zugriffsvorgang relevant sind, und es wird nicht das gesamte Dataset aktiviert.                                                  |
| Für diesen Prozess wird zusätzliche Zeit benötigt, um alle älteren Sicherungen in Azure zu konvertieren. Dies wird asynchron ohne Auswirkung auf die Produktion durchgeführt. | Die Migration kann nur auf einer Cloudkonfigurationsebene erfolgen.  Einzelne Volumes einer Cloudkonfiguration können nicht separat migriert werden                       |

Eine hostseitige Migration ermöglicht das unabhängige Einrichten der Serie 8000 und das Kopieren der Daten von einem Gerät der Serie 5000/7000 auf ein Gerät der Serie 8000. Dies entspricht dem Migrieren von Daten von einem Speichergerät zu einem anderen. Es werden verschiedene Tools, z.B. Diskboss und Robocopy, verwendet, um die Daten zu kopieren.

| Vorteile                                                                                                                      |Nachteile                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Die Migration kann in Phasen und Volume für Volume durchgeführt werden.                                               | Vorherige Sicherungen (für die Serie 5000/7000) sind auf dem Gerät der Serie 8000 nicht verfügbar.                                                                                                       |
| Daten können in Azure in einem Speicherkonto konsolidiert werden.                                                       | Die erste Sicherung in die Cloud für die Serie 8000 dauert länger als für alle Daten der Serie 8000 in Azure.                                                                     |
| Nach einer erfolgreichen Migration sind alle Daten lokal auf der Appliance vorhanden. Beim Zugreifen auf Daten kommt es nicht zu Wartezeiten. | Es wird mehr Azure-Speicher verbraucht, bis die Daten vom Gerät der Serie 5000/7000 gelöscht werden.                                                                                                        |
|                                                                                                                           | Wenn das Gerät der Serie 7000/5000 eine große Menge von Daten enthält, müssen diese Daten während der Migration aus Azure heruntergeladen werden. Hierfür fallen Kosten und Wartezeiten für das Herunterladen von Daten aus Azure an. |

In diesem Artikel geht es nur um das Feature für die Migration von einem Gerät der Serie 5000/7000 zu 8000. Weitere Informationen zur hostseitigen Migration finden Sie unter [Migration from other storage devices](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf) (Migration von anderen Speichergeräten).

## <a name="migration-prerequisites"></a>Voraussetzungen für die Migration

Hier werden die Voraussetzungen für die Migration Ihres Legacygeräts der Serie 5000 oder 7000 und des StorSimple-Geräts der Serie 8000 beschrieben.

> [!IMPORTANT]
> Lesen und erfüllen Sie die Voraussetzungen für die Migration, bevor Sie einen Service Request an den Microsoft-Support senden.

### <a name="for-the-50007000-series-device-source"></a>Für das Gerät der Serie 5000/7000 (Quelle)

Stellen Sie Folgendes sicher, bevor Sie mit der Migration beginnen:

* Sie verfügen über ein Quellgerät der Serie 5000 oder 7000. Das Gerät kann aktiv oder inaktiv sein.

    > [!IMPORTANT]
    > Es ist ratsam, dass Sie während des gesamten Migrationsprozesses seriellen Zugriff auf dieses Gerät haben. Falls es zu Geräteproblemen kommt, kann der serielle Zugriff für die Problembehandlung hilfreich sein.

* Auf Ihrem Quellgerät der Serie 5000 oder 7000 wird die Softwareversion v2.1.1.518 oder höher ausgeführt. Frühere Versionen werden nicht unterstützt.
* Prüfen Sie oben rechts auf Ihrer Webbenutzeroberfläche die Version, die für Ihr Gerät der Serie 5000 oder 7000 ausgeführt wird. An dieser Stelle sollte die Softwareversion des Geräts angezeigt werden. Für die Migration sollte auf dem Gerät der Serie 5000 oder 7000 die Version v2.1.1.518 ausgeführt werden.

    ![Überprüfen der Softwareversion auf dem Legacygerät](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Führen Sie für Ihr System ein Upgrade auf die erforderliche Mindestversion durch, wenn Ihr aktives Gerät nicht über v2.1.1.518 oder höher verfügt. Unter Umständen müssen Sie sich an den Microsoft-Support wenden, um das Upgrade auszuführen.
    * Wenn Version v2.1.1.518 ausgeführt wird, können Sie auf der Webbenutzeroberfläche nachsehen, ob Benachrichtigungen zu Wiederherstellungsfehlern für die Registrierung vorhanden sind. Führen Sie die Registrierungswiederherstellung aus, falls dieser Vorgang fehlgeschlagen ist. Unter Umständen müssen Sie sich an den Microsoft-Support wenden, um die Registrierung wiederherzustellen.
    * Wenn Sie über ein ausgefallenes Gerät verfügen, auf dem nicht v2.1.1.518 ausgeführt wird, können Sie ein Failover auf ein Ersatzgerät durchführen, auf dem v2.1.1.518 ausgeführt wird. Eine ausführliche Anleitung finden Sie unter den Informationen zur Notfallwiederherstellung Ihres StorSimple-Geräts der Serie 5000/7000.
    * Sichern Sie die Daten für Ihr Gerät, indem Sie eine Cloudmomentaufnahme erstellen.
    * Suchen Sie nach anderen aktiven Sicherungsaufträgen, die auf dem Quellgerät ausgeführt werden. Dies umfasst auch die Aufträge auf dem Host mit der Konsole für den StorSimple-Datenschutz. Warten Sie, bis die aktuellen Aufträge abgeschlossen sind.


### <a name="for-the-8000-series-physical-device-target"></a>Für das physische Gerät der Serie 8000 (Ziel)

Stellen Sie Folgendes sicher, bevor Sie mit der Migration beginnen:

* Das Zielgerät der Serie 8000 ist registriert und wird ausgeführt. Weitere Informationen finden Sie unter [Bereitstellen lokaler StorSimple-Geräte (Update 3 und höher)](storsimple-8000-deployment-walkthrough-u2.md).
* Auf Ihrem Gerät der Serie 8000 ist das aktuelle StorSimple 8000 Series Update 5 installiert und mindestens Version 6.3.9600.17845 vorhanden. Wenn auf Ihrem Gerät nicht die aktuellen Updates installiert sind, müssen Sie dies nachholen, bevor Sie mit der Migration beginnen können. Weitere Informationen finden Sie unter [Installieren von Update 5 auf Ihrem StorSimple-Gerät](storsimple-8000-install-update-5.md).
* Ihr Azure-Abonnement ist für die Migration aktiviert. Falls nicht, können Sie sich an den Microsoft-Support wenden, um Ihr Abonnement für die Migration aktivieren zu lassen.

### <a name="for-the-80108020-cloud-appliance-target"></a>Für das 8010/8020-Cloudgerät (Ziel)

Stellen Sie Folgendes sicher, bevor Sie mit der Migration beginnen:

* Ihr Zielcloudgerät (Cloud Appliance) ist registriert und wird ausgeführt. Weitere Informationen finden Sie unter [Bereitstellen und Verwalten einer StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* Ihr Cloudgerät wird unter der aktuellen Softwareversion 6.3.9600.17845 mit StorSimple 8000 Series Update 5 ausgeführt. Wenn für das Cloudgerät nicht Update 5 ausgeführt wird, sollten Sie vor dem Fortfahren mit der Migration ein neues Cloudgerät mit Update 5 erstellen. Weitere Informationen finden Sie unter [Bereitstellen und Verwalten einer StorSimple Cloud Appliance in Azure (ab Update 3)](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Für den Computer mit dem StorSimple-Migrationstool

Das StorSimple-Migrationstool ist ein UI-basiertes Tool, mit dem Sie Daten von einem StorSimple-Gerät der Serie 5000 - 7000 zu einem Gerät der Serie 8000 migrieren können. Verwenden Sie zum Installieren des StorSimple-Migrationstools einen Computer, der die unten angegebenen Anforderungen erfüllt.

Der Computer verfügt über eine Internetverbindung und:

* Folgendes Betriebssystem:
    * Windows 10
    * Windows Server 2012 R2 (oder höher) zum Installieren des StorSimple-Migrationstools
* .NET 4.5.2-Installation
* Mindestens 5 GB freien Speicherplatz zum Installieren und Verwenden des Tools

> [!TIP]
> Wenn Ihr StorSimple-Gerät über eine Verbindung mit einem Windows Server-Host verfügt, können Sie das Migrationstool auf dem Windows Server-Hostcomputer installieren.

#### <a name="to-install-storsimple-migration-tool"></a>So installieren Sie das StorSimple-Migrationstool

Führen Sie die folgenden Schritte aus, um das StorSimple-Migrationstool auf Ihrem Computer zu installieren.

1. Kopieren Sie den Ordner _StorSimple8000SeriesMigrationTool_ auf Ihren Windows-Computer. Stellen Sie sicher, dass auf dem Laufwerk, auf das die Software kopiert wird, genügend Speicherplatz vorhanden ist.

    Öffnen Sie im Ordner die Konfigurationsdatei _StorSimple8000SeriesMigrationTool.exe.config_ für das Tool. Hier ist der Codeausschnitt der Datei angegeben.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Bearbeiten Sie die entsprechenden Werte für die Schlüssel, und ersetzen Sie diese durch Folgendes:

    * `UserName`: Benutzername zum Anmelden am Azure-Portal.
    * `SubscriptionName and SubscriptionId`: Name und ID für Ihr Azure-Abonnement. Klicken Sie auf der Zielseite des StorSimple-Geräte-Manager-Diensts unter **Allgemein** auf **Eigenschaften**. Kopieren Sie den Abonnementnamen und die Abonnement-ID, die Ihrem Dienst zugeordnet ist.
    * `ResourceName`: Name Ihres StorSimple-Geräte-Manager-Diensts im Azure-Portal. Der Name wird auch unter den Diensteigenschaften angezeigt.
    * `ResourceGroup`: Name der Ressourcengruppe, die Ihrem StorSimple-Geräte-Manager-Dienst im Azure-Portal zugeordnet ist. Der Name wird auch unter den Diensteigenschaften angezeigt.
    ![Überprüfen der Diensteigenschaften für das Zielgerät](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`: Azure Active Directory-Mandanten-ID im Azure-Portal. Melden Sie an Microsoft Azure als Administrator an. Klicken Sie im Microsoft Azure-Portal auf **Azure Active Directory**. Klicken Sie unter **Verwalten** auf **Eigenschaften**. Die Mandanten-ID wird im Feld **Verzeichnis-ID** angezeigt.
    ![Überprüfen der Mandanten-ID für Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Speichern Sie die Änderungen, die an der Konfigurationsdatei vorgenommen wurden.
4.  Führen Sie _StorSimple8000SeriesMigrationTool.exe_ aus, um das Tool zu starten. Geben Sie nach der entsprechenden Aufforderung die Anmeldeinformationen ein, die Ihrem Abonnement im Azure-Portal zugeordnet sind. 
5.  Die Benutzeroberfläche des StorSimple-Migrationstools ist unten dargestellt.
  

## <a name="next-steps"></a>Nächste Schritte
Laden Sie die Schritt-für-Schritt-Anleitung unter [Microsoft Azure StorSimple 5000/7000 Series to 8000 Series Migration Guide](https://databoxbuilds.blob.core.windows.net/storsimpledocs/MicrosoftAzureStorSimple_Migration_Guide_20171115.pdf) (Microsoft Azure StorSimple – Anleitung zur Migration von der Serie 5000/7000 zur Serie 8000) herunter.
