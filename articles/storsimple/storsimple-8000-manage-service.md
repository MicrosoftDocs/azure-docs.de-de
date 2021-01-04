---
title: Bereitstellen des StorSimple-Geräte-Manager-Diensts in Azure | Microsoft-Dokumentation
description: Hier werden die erforderlichen Schritte zum Erstellen, Löschen und Migrieren des Diensts und die Verwaltung des Dienstsregistrierungsschlüssels beschrieben.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 66244bd8e24ff62be41df72f7a39c0ce0ed13135
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360698"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Bereitstellen des StorSimple-Geräte-Manager-Diensts für Geräte der StorSimple 8000-Serie

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Übersicht

Der StorSimple-Geräte-Manager-Dienst wird in Microsoft Azure ausgeführt. Er verfügt über Verbindungen mit mehreren StorSimple-Geräten. Nachdem Sie den Dienst erstellt haben, können Sie alle Geräte, die mit den StorSimple-Geräte-Manager-Dienst verbunden sind, von einem zentralen Standort aus verwalten und damit den Verwaltungsaufwand minimieren.

In diesem Tutorial werden die erforderlichen Schritte zum Erstellen, Löschen und Migrieren des Diensts und die Verwaltung des Dienstsregistrierungsschlüssels beschrieben. Die Informationen in diesem Artikel gelten nur für Geräte der StorSimple 8000-Serie. Weitere Informationen zu StorSimple Virtual Arrays finden Sie unter [Bereitstellen eines StorSimple-Geräte-Manager-Diensts für das StorSimple Virtual Array](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Das Azure-Portal unterstützt Geräte mit ausgeführtem Update 5.0 oder höher. Wenn Ihr Gerät nicht auf dem neuesten Stand ist, installieren Sie Update 5 sofort. Weitere Informationen finden Sie unter [Installieren von Update 5](storsimple-8000-install-update-5.md). 
> - Wenn Sie eine StorSimple Cloud Appliance (8010/8020) verwenden, können Sie dieses Gerät nicht aktualisieren. Verwenden Sie die neueste Version der Software, um ein neues Cloudgerät mit Update 5.0 zu erstellen und dann ein Failover auf das neu erstellte Cloudgerät auszuführen. 
> - Alle Geräte, auf denen Update 4.0 oder eine frühere Version ausgeführt wird, weisen verminderte Verwaltungsfunktionalität auf. 

## <a name="create-a-service"></a>Erstellen von Diensten
Um einen StorSimple-Geräte-Manager-Dienst zu erstellen, benötigen Sie Folgendes:

* Ein Abonnement mit einem Enterprise Agreement
* Ein aktives Microsoft Azure-Speicherkonto
* Die Abrechnungsinformationen für die Zugriffsverwaltung

Nur Abonnements mit Enterprise Agreement sind zulässig. Sie können beim Erstellen des Diensts auch ein Standardspeicherkonto generieren.

Mit einem Dienst können mehrere Geräte verwaltet werden. Ein Gerät kann jedoch nicht mehrere Dienste umfassen. Große Unternehmen können mit mehreren Dienstinstanzen mit verschiedenen Abonnements, Organisationen oder sogar Bereitstellungsstandorten arbeiten. 

> [!NOTE]
> Sie benötigen separate Instanzen des StorSimple-Geräte-Manager-Diensts, um StorSimple-Geräte der 8000er-Serie und StorSimple Virtual Arrays zu verwalten.

Führen Sie die folgenden Schritte aus, um einen Dienst zu erstellen.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Für jeden StorSimple-Geräte-Manager-Dienst gibt es die folgenden Attribute:

* **Name:** Name, der dem StorSimple-Geräte-Manager-Dienst bei der Erstellung zugewiesen wurde. **Der Dienstname kann nicht mehr geändert werden, nachdem der Dienst erstellt wurde. Dies gilt auch für andere Entitäten wie z B. Geräte, Volumes, Volumecontainer und Sicherungsrichtlinien, die im Azure-Portal nicht umbenannt werden können.**
* **Status**: der Status des Diensts mit den möglichen Werten **Aktiv**, **Wird erstellt** oder **Online**.
* **Standort:** der geografische Standort, an dem das StorSimple-Gerät bereitgestellt wird.
* **Abonnement:** das Abonnement für die Abrechnung, das mit Ihrem Dienst verbunden ist.

## <a name="delete-a-service"></a>Löschen von Diensten

Bevor Sie einen Dienst löschen, stellen Sie sicher, dass er von keinen verbundenen Geräten verwendet wird. Wenn der Dienst verwendet wird, deaktivieren Sie die verbundenen Geräte. Der deaktivierte Vorgang trennt die Verbindung zwischen dem Gerät und dem Dienst, behält aber die Gerätedaten in der Cloud bei.

> [!IMPORTANT]
> Das Löschen eines Diensts kann nicht rückgängig gemacht werden. Jedes Gerät, das den Dienst verwendet hat, muss auf die Werkseinstellungen zurückgesetzt werden, bevor es mit einem anderen Dienst verwendet werden kann. In diesem Szenario gehen sowohl die lokalen Daten auf dem Gerät als auch die Konfiguration verloren.

Führen Sie die folgenden Schritte aus, um einen Dienst zu löschen.

### <a name="to-delete-a-service"></a>So löschen Sie einen Dienst

1. Suchen Sie den Dienst, den Sie löschen möchten. Klicken Sie auf das Symbol **Ressourcen**, und geben Sie dann die entsprechenden Suchbegriffe ein. Klicken Sie in den Suchergebnissen auf den Dienst, den Sie löschen möchten.

    ![Suchen des zu löschenden Diensts](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Sie gelangen zum Blatt „StorSimple-Geräte-Manager-Dienst“. Klicken Sie auf **Löschen**.

    ![Suchdienst löschen](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Klicken Sie in der Bestätigungsbenachrichtigung auf **Ja** . Es dauert einige Minuten, bis der Dienst gelöscht wird.

    ![Löschvorgang bestätigen](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Abrufen des Dienstregistrierungsschlüssels

Nachdem Sie einen Dienst erstellt haben, müssen Sie Ihr StorSimple-Gerät bei dem Dienst registrieren. Um Ihr erstes StorSimple-Gerät zu registrieren, benötigen Sie den Dienstregistrierungsschlüssel. Um zusätzliche Geräte bei einem vorhandenen StorSimple-Dienst zu registrieren, benötigen Sie den Registrierungsschlüssel und den Verschlüsselungsschlüssel für Dienstdaten (die während der Registrierung auf dem ersten Gerät generiert werden). Weitere Informationen zum Verschlüsselungsschlüssel für Dienstdaten finden Sie unter [StorSimple-Sicherheit](storsimple-8000-security.md). Sie können den Registrierungsschlüssel auf dem Blatt „StorSimple-Geräte-Manager“ unter **Schlüssel** abrufen.

Führen Sie die folgenden Schritte durch, um den Dienstregistrierungsschlüssel abzurufen.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Bewahren Sie den Dienstregistrierungsschlüssel an einem sicheren Ort auf. Sie benötigen diesen Schlüssel sowie den Verschlüsselungsschlüssel für Dienstdaten, um zusätzliche Geräte bei diesem Dienst zu registrieren. Nach dem Abrufen des Dienstregistrierungsschlüssels müssen Sie das Gerät mithilfe von Windows PowerShell für StorSimple konfigurieren.

Ausführliche Informationen zur Verwendung dieses Registrierungsschlüssels finden Sie unter [Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Neugenerieren des Dienstregistrierungsschlüssels
Sie müssen den Dienstregistrierungsschlüssel neu generieren, wenn Sie die Schlüsselrotation durchführen müssen oder wenn sich die Liste der Dienstadministratoren geändert hat. Wenn Sie den Schlüssel neu generieren, wird der neue Schlüssel nur für die Registrierung nachfolgender Geräte verwendet. Bereits registrierte Geräte sind von diesem Vorgang nicht betroffen.

Führen Sie die folgenden Schritte durch, um den Dienstregistrierungsschlüssel neu zu generieren.

### <a name="to-regenerate-the-service-registration-key"></a>So generieren Sie den Dienstregistrierungsschlüssel neu
1. Wechseln Sie auf dem Blatt **StorSimple-Geräte-Manager** zu **Management &gt;** **Schlüssel**.
    
    ![Navigieren zum Blatt „Schlüssel“](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Klicken Sie auf dem Blatt **Schlüssel** auf **Neu generieren**.

    ![Klicken Sie auf „Neu generieren“.](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Überprüfen Sie auf dem Blatt **Dienstregistrierungsschlüssel erneut generieren**, welche Aktion erforderlich ist, wenn die Schlüssel neu generiert werden. Alle weiteren Geräte, die bei diesem Dienst angemeldet werden, verwenden den neuen Registrierungsschlüssel. Klicken Sie zum Bestätigen auf **Regenerieren**. Sie werden benachrichtigt, sobald die Registrierung abgeschlossen ist.

    ![Bestätigen des erneuten Generierens](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Ein neuer Dienstregistrierungsschlüssel wird angezeigt.

5. Kopieren Sie diesen Schlüssel, und speichern Sie ihn für die Registrierung neuer Geräte bei diesem Dienst.



## <a name="change-the-service-data-encryption-key"></a>Ändern des Verschlüsselungsschlüssels für Dienstdaten
Dienstdaten-Verschlüsselungsschlüssel werden verwendet, um vertrauliche Kundendaten zu verschlüsseln, beispielsweise Anmeldeinformationen für das Speicherkonto, die vom StorSimple Manager-Dienst zum StorSimple-Gerät gesendet werden. Sie müssen diese Schlüssel regelmäßig ändern, wenn Ihre IT-Organisation über eine Richtlinie zur Schlüsselrotation für Speichergeräte verfügt. Das Verfahren zur Schlüsseländerung kann variieren, je nachdem, ob ein einzelnes Gerät oder mehrere Geräte vom StorSimple Manager-Dienst verwaltet werden. Weitere Informationen finden Sie unter [StorSimple-Sicherheit und -Datenschutz](storsimple-8000-security.md).

Das Ändern den Verschlüsselungsschlüssel für Dienstdaten wird in drei Schritten vollzogen:

1. Autorisieren Sie mithilfe von Windows PowerShell-Skripts für Azure Resource Manager ein Gerät, um den Verschlüsselungsschlüssel für Dienstdaten zu ändern.
2. Sie verwenden Windows PowerShell für StorSimple, um die Änderung des Verschlüsselungsschlüssels für Dienstdaten zu initialisieren.
3. Wenn Sie über mehrere StorSimple-Geräte verfügen, aktualisieren Sie den Verschlüsselungsschlüssel für Dienstdaten auf den anderen Geräten.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Schritt 1: Autorisieren eines Geräts mithilfe eines Windows PowerShell-Skripts, um den Verschlüsselungsschlüssel für Dienstdaten zu ändern
Üblicherweise fordert der Geräteadministrator den Dienstadministrator auf, ein Gerät zu autorisieren, um den Verschlüsselungsschlüssel für Dienstdaten zu ändern. Der Dienstadministrator autorisiert dann das Gerät, um den Schlüssel zu ändern.

Dieser Schritt wird mithilfe des Azure Resource Manager-basierten Skripts ausgeführt. Der Dienstadministrator kann ein Gerät auswählen, das für die Autorisierung geeignet ist. Das Gerät wird dann autorisiert und der Vorgang zur Änderung des Verschlüsselungsschlüssels für Dienstdaten wird gestartet. 

Weitere Informationen zum Verwenden des Skripts finden Sie unter [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1).

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Welche Geräte können zur Änderung des Verschlüsselungsschlüssels für Dienstdaten autorisiert werden?
Ein Gerät muss die folgenden Kriterien erfüllen, bevor es für die Initiierung des Änderungsvorgangs des Verschlüsselungsschlüssels für Dienstdaten autorisiert werden kann:

* Für die Autorisierung zum Ändern des Verschlüsselungsschlüssels für Dienstdaten muss das Gerät online sein.
* Wurde die Änderung des Schlüssels nicht initiiert, können Sie dasselbe Gerät nach 30 Minuten erneut autorisieren.
* Wurde die Änderung des Schlüssel nicht durch das zuvor autorisierte Gerät initialisiert, können Sie auch ein anderes Gerät autorisieren. Nachdem das neue Gerät autorisiert wurde, kann die Änderung nicht durch das alte Gerät initiiert werden.
* Während des Rollover-Prozesses des Verschlüsselungsschlüssel für Dienstdaten können Sie kein Gerät autorisieren.
* Eine Geräte-Autorisierung ist möglich, sobald einige der beim Dienst registrierten Geräte den Verschlüsselungs-Rollover abgeschlossen haben. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Schritt 2: Verwenden von Windows PowerShell für StorSimple, um die Änderung des Verschlüsselungsschlüssels für Dienstdaten zu initialisieren
Dieser Schritt wird in der Windows PowerShell für StorSimple-Schnittstelle auf dem autorisierten StorSimple-Gerät ausgeführt.

> [!NOTE]
> Bis zum Abschluss des Schlüsselrollovers kann im Azure-Portal des StorSimple Managers kein Vorgang erfolgen.


Wenn Sie die Verbindung zur Windows PowerShell-Schnittstelle über die serielle Gerätekonsole herstellen, führen Sie die folgenden Schritte durch.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Initiierung der Änderung des Verschlüsselungsschlüssels für Dienstdaten
1. Wählen Sie Option 1, um sich mit Vollzugriff anzumelden.
2. Geben Sie an der Eingabeaufforderung Folgendes ein:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Nachdem das Cmdlet erfolgreich abgeschlossen wurde, erhalten Sie einen neuen Verschlüsselungsschlüssel für Dienstdaten. Kopieren und speichern Sie diesen Schlüssel, da Sie ihn für Schritt 3 dieses Prozesses benötigen. Dieser Schlüssel wird zur Aktualisierung aller Geräte benötigt, die beim StorSimple Manager-Dienst registriert sind.
   
   > [!NOTE]
   > Dieser Prozess muss innerhalb von vier Stunden nach der Autorisierung eines StorSimple-Geräts initiiert werden.
   > 
   > 
   
   Der neue Schlüssel wird dann an den Dienst gesendet und an alle bei diesem Dienst registrierten Geräte übermittelt. Im Dienst-Dashboard wird eine Warnung angezeigt. Der Dienst deaktiviert alle Vorgänge auf den registrierten Geräten. Dann muss der Geräteadministrator den Verschlüsselungsschlüssel für Dienstdaten auf den anderen Geräten aktualisieren. E/A-Vorgänge (von Hosts an die Cloud übermittelte Daten) werden jedoch nicht unterbrochen.
   
   Wenn Sie nur ein Gerät beim Dienst registriert haben, ist der Rollover-Prozess nun abgeschlossen und Sie können den nächsten Schritt überspringen. Wenn Sie mehrere Geräte beim Dienst registriert haben, fahren Sie mit Schritt 3 fort.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Schritt 3: Aktualisieren des Verschlüsselungsschlüssels für Dienstdaten auf anderen StorSimple-Geräten
Wenn Sie mehrere Geräte bei Ihrem StorSimple Manager-Dienst registriert haben, müssen Sie die folgenden Schritte in der Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts durchführen. Der Schlüssel, den in Schritt 2 abgerufen haben, muss zum Aktualisieren aller verbleibenden StorSimple-Geräte genutzt werden, die beim StorSimple Manager-Dienst registriert sind.

Führen Sie die folgenden Schritte aus, um den Verschlüsselungsschlüssel für Dienstdaten auf Ihrem Gerät zu aktualisieren.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>So aktualisieren Sie den Dienstdatenverschlüsselungs-Schlüssel auf physischen Geräten
1. Verwenden Sie Windows PowerShell für StorSimple für die Verbindung mit der Konsole. Wählen Sie Option 1, um sich mit Vollzugriff anzumelden.
2. Geben Sie an der Eingabeaufforderung Folgendes ein: `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`.
3. Geben Sie den Dienstdatenverschlüsselungs-Schlüssel an, den Sie in [Schritt2: Verwenden von Windows PowerShell für StorSimple, um die Änderung des Verschlüsselungsschlüssels für Dienstdaten zu initialisieren](#to-initiate-the-service-data-encryption-key-change) abgerufen haben.

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>So aktualisieren Sie den Dienstdatenverschlüsselungs-Schlüssel auf allen 8010/8020 Cloud Appliances
1. Laden Sie das PowerShell-Skript [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) herunter, und richten Sie es ein. 
2. Öffnen Sie PowerShell, und geben Sie an der Eingabeaufforderung Folgendes ein: `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Mit diesem Skript wird sichergestellt, dass der Dienstdatenverschlüsselungs-Schlüssel in allen 8010/8020 Cloud Appliances unter dem Device Manager festgelegt wird.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Unterstützte Vorgänge auf Geräten mit einer Version vor Update 5.0
Im Azure-Portal werden nur die StorSimple-Geräte mit ausgeführtem Update 5.0 und höher unterstützt. Die Geräte mit früheren Versionen werden eingeschränkt unterstützt. In der folgenden Tabelle sind die Vorgänge aufgeführt, die auf Geräten mit Softwareversionen vor Update 5.0 unterstützt werden, nachdem die Umstellung auf das Azure-Portal erfolgt ist.

| Vorgang                                                                                                                       | Unterstützt      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrieren eines Geräts                                                                                                               | Ja            |
| Konfigurieren von Geräteeinstellungen (allgemein, Netzwerk und Sicherheit)                                                                | Ja            |
| Suchen nach, Herunterladen und Installieren von Updates                                                                                             | Ja            |
| Deaktivieren des Geräts                                                                                                               | Ja            |
| Löschen des Geräts                                                                                                                   | Ja            |
| Erstellen, Ändern und Löschen eines Volumecontainers                                                                                   | Nein             |
| Erstellen, Ändern und Löschen eines Volumes                                                                                             | Nein             |
| Erstellen, Ändern und Löschen einer Sicherungsrichtlinie                                                                                      | Nein             |
| Erstellen einer manuellen Sicherung                                                                                                            | Nein             |
| Erstellen einer geplanten Sicherung                                                                                                         | Nicht verfügbar |
| Wiederherstellen eines Sicherungssatzes                                                                                                        | Nein             |
| Klonen eines Gerät mit ausgeführtem Update 3.0 und höher <br> Auf dem Quellgerät wird eine Version vor Update 3.0 ausgeführt.                                | Ja            |
| Klonen eines Gerät mit ausgeführter Version vor Update 3.0                                                                          | Nein             |
| Failover als Quellgerät <br> (von einem Gerät mit Version vor Update 3.0 auf ein Gerät mit Update 3.0 und höher)                                                               | Ja            |
| Failover als Zielgerät <br> (auf ein Gerät mit Softwareversion vor Update 3.0)                                                                                   | Nein             |
| Erstellen einer Warnung                                                                                                                  | Ja            |
| Anzeigen von im klassischen Portal erstellten Sicherungsrichtlinien, Sicherungskatalogen, Volumes, Volumecontainern, Überwachungsdiagrammen, Aufträgen und Warnungen | Ja            |
| Aktivieren und Deaktivieren von Gerätecontrollern                                                                                              | Ja            |


## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum [StorSimple-Bereitstellungsprozess](storsimple-8000-deployment-walkthrough-u2.md)
* Weitere Informationen über das [Verwalten des StorSimple-Speicherkontos](storsimple-8000-manage-storage-accounts.md)
* Weitere Informationen über das [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md)
