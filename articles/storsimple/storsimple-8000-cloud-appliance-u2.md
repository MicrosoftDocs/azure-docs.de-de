---
title: StorSimple Cloud Appliance – Update 3| Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine StorSimple Cloud Appliance in einem virtuellen Microsoft Azure-Netzwerk erstellen, bereitstellen und verwalten. (Gilt für StorSimple ab Update 3).
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: alkohli
ms.openlocfilehash: d78051c1a5af82a986152c8244d25b68dd65d552
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968841"
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Bereitstellen und Verwalten einer StorSimple Cloud Appliance in Azure (ab Update 3)

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Übersicht

Das Cloudgerät der Serie StorSimple 8000 ist eine zusätzliche Funktion der Microsoft Azure StorSimple-Lösung. Die StorSimple Cloud Appliance (StorSimple-Cloudgerät) wird auf einem virtuellen Computer in einem virtuellen Microsoft Azure-Netzwerk ausgeführt und kann zum Sichern und Klonen der Daten von Hosts verwendet werden.

In diesem Artikel werden die einzelnen Bereitstellungs- und Verwaltungsschritte für eine StorSimple Cloud Appliance in Azure beschrieben. In diesem Artikel lernen Sie Folgendes:

* Unterschiede zwischen Cloudgerät und physischem Gerät
* Erstellen und Konfigurieren des Cloudgeräts
* Herstellen einer Verbindung mit dem Cloudgerät
* Verwenden des Cloudgeräts

Dieses Tutorial gilt für alle StorSimple Cloud Appliances ab Update 3.

#### <a name="cloud-appliance-model-comparison"></a>Cloudgerät-Modellvergleich

Die StorSimple Cloud Appliance steht in zwei Modellen zur Verfügung, dem Standardmodell 8010 (ehemals 1100) und dem in Update 2 eingeführten Premium-Modell 8020. Die folgende Tabelle enthält einen Vergleich der beiden Modelle.

| Gerätemodell | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Maximale Kapazität** |30 TB |64 TB |
| **Azure-VM** |Standard_A3 (4 Kerne, 7 GB Arbeitsspeicher)| Standard_DS3 (4 Kerne, 14 GB Arbeitsspeicher)|
| **Regionale Verfügbarkeit** |Alle Azure-Regionen |Azure-Regionen, die Storage Premium und DS3-Azure-VMs unterstützen<br></br>Verwenden Sie [diese Liste](https://azure.microsoft.com/regions/services/), um zu ermitteln, ob in Ihrer Region sowohl **Virtuelle Computer > DS-Serie** als auch **Storage > Disk Storage** verfügbar ist. |
| **Speichertyp** |Verwendet Azure Storage Standard für lokale Datenträger<br></br> Erfahren Sie, wie Sie ein [Storage Standard-Konto erstellen](../storage/common/storage-account-create.md) |Verwendet Azure Storage Premium für lokale Datenträger<sup>2</sup> <br></br> |
| **Informationen zu Workloads** |Abrufen von Dateien aus Sicherungskopien auf Elementebene |Entwicklungs- und Testszenarien für die Cloud <br></br>Workloads mit geringer Wartezeit und höherer Leistung<br></br>Sekundäres Gerät für die Notfallwiederherstellung |

<sup>1</sup> *Ehemals 1100*

<sup>2</sup> *Die Serien 8010 und 8020 verwenden Azure Storage Standard für die Cloud-Ebene. Einen Unterschied gibt es nur in der lokalen Ebene innerhalb des Geräts*.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>Unterschiede zwischen Cloudgerät und physischem Gerät

Die StorSimple Cloud Appliance ist eine reine Softwareversion von StorSimple, die auf einem einzelnen Knoten eines virtuellen Microsoft Azure-Computers ausgeführt wird. Das Cloudgerät unterstützt Notfallwiederherstellungsszenarien, in denen Ihr physisches Gerät nicht verfügbar ist. Das Cloudgerät kann zum Abrufen einzelner Elemente aus Sicherungen, bei der lokalen Notfallwiederherstellung und für Entwicklungs- und Testszenarien in der Cloud verwendet werden.

#### <a name="differences-from-the-physical-device"></a>Unterschiede im Vergleich zum physischen Gerät

In der folgenden Tabelle sind einige wichtige Unterschiede zwischen der StorSimple Cloud Appliance und dem physischen StorSimple-Gerät aufgeführt.

|  | Physisches Gerät | Cloudgerät |
| --- | --- | --- |
| **Location** |Befindet sich im Datencenter. |Wird in Azure ausgeführt. |
| **Netzwerkschnittstellen** |Besitzt sechs Netzwerkschnittstellen: DATA 0 bis DATA 5. |Besitzt nur eine Netzwerkschnittstelle: DATA 0. |
| **Registrierung** |Wird während des ersten Konfigurationsschritts registriert. |Die Registrierung ist eine separate Aufgabe. |
| **Verschlüsselungsschlüssel für Dienstdaten** |Generieren Sie den Schlüssel auf dem physischen Gerät erneut, und aktualisieren Sie dann das Cloudgerät mit dem neuen Schlüssel. |Die erneute Generierung über das Cloudgerät ist nicht möglich. |
| **Unterstützte Volumetypen** |Unterstützt sowohl lokale als auch mehrstufige Volumes. |Unterstützt nur mehrstufige Volumes. |

## <a name="prerequisites-for-the-cloud-appliance"></a>Voraussetzungen für das Cloudgerät

In den folgenden Abschnitten werden die Konfigurationsvoraussetzungen für die StorSimple Cloud Appliance beschrieben. Lesen Sie sich vor dem Bereitstellen eines Cloudgeräts die Sicherheitshinweise zur Verwendung eines Cloudgeräts durch.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Anforderungen für Azure

Bevor Sie das Cloudgerät bereitstellen, müssen Sie in Ihrer Umgebung die folgenden Vorbereitungen treffen:

* Vergewissern Sie sich, dass in Ihrem Datencenter ein physisches Gerät der StorSimple 8000-Serie (Modell 8100 oder 8600) bereitgestellt und aktiv ist. Registrieren Sie dieses Gerät mit dem gleichen StorSimple-Geräte-Manager-Dienst, für den Sie eine StorSimple Cloud Appliance erstellen möchten.
* [Konfigurieren Sie für das Cloudgerät ein virtuelles Netzwerk in Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Wenn Sie Storage Premium verwenden, müssen Sie ein virtuelles Netzwerk in einer Azure-Region erstellen, die Storage Premium unterstützt. Storage Premium-Regionen sind Regionen, die der Zeile „Disk Storage“ in der Liste [Azure-Produkte nach Region](https://azure.microsoft.com/regions/services/) entsprechen.
* Es ist ratsam, die von Azure bereitgestellten DNS-Standardserver zu verwenden, anstatt einen eigenen DNS-Servernamen anzugeben. Wenn Ihr DNS-Servername ungültig ist oder der DNS-Server IP-Adressen nicht richtig auflösen kann, ist die Erstellung des Cloudgeräts nicht möglich.
* Punkt-zu-Standort und Standort-zu-Standort sind optional, aber nicht erforderlich. Sie können diese Optionen gegebenenfalls für erweiterte Szenarios konfigurieren.
* Sie können [virtuelle Azure-Computer](../virtual-machines/windows/quick-create-portal.md) (Hostserver) im virtuellen Netzwerk erstellen, die die vom Cloudgerät verfügbar gemachten Volumes verwenden können. Diese Server müssen die folgenden Anforderungen erfüllen:

  * Es muss sich um virtuelle Windows- oder Linux-Computer mit iSCSI-Initiatorsoftware handeln.
  * Sie müssen im gleichen virtuellen Netzwerk wie das Cloudgerät ausgeführt werden.
  * Sie müssen über die interne IP-Adresse des Cloudgeräts eine Verbindung zum iSCSI-Ziel des Cloudgeräts herstellen können.
  * Stellen Sie sicher, dass die iSCSI-Unterstützung und der Clouddatenverkehr im gleichen virtuellen Netzwerk konfiguriert wurden.

#### <a name="storsimple-requirements"></a>StorSimple-Anforderungen

Führen Sie für Ihren StorSimple-Geräte-Manager-Dienst die folgenden Updates durch, bevor Sie ein Cloudgerät erstellen:

* Fügen Sie den virtuellen Computern, die als Hostserver für Ihr Cloudgerät fungieren sollen, [Zugriffssteuerungsdatensätze](storsimple-8000-manage-acrs.md) hinzu.
* Verwenden Sie ein [Speicherkonto](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) in derselben Region wie das Cloudgerät. Speicherkonten in anderen Regionen können zu Leistungseinbußen führen. Sie können ein Standard- oder Storage Premium-Konto für das Cloudgerät verwenden. Weitere Informationen zum Erstellen eines [Storage Standard-Kontos](../storage/common/storage-account-create.md).
* Verwenden Sie zum Erstellen des Cloudgeräts ein anderes Speicherkonto als das Konto, das für die Daten verwendet wird. Die Verwendung ein und desselben Speicherkontos kann zu Leistungseinbußen führen.

Stellen Sie vorab sicher, dass die folgenden Informationen vorliegen:

* Ein Konto für das Azure-Portal sowie die zugehörigen Anmeldeinformationen
* Eine Kopie des Dienstdatenverschlüsselungs-Schlüssels vom physischen Gerät, das für den StorSimple-Geräte-Manager-Dienst registriert ist

## <a name="create-and-configure-the-cloud-appliance"></a>Erstellen und Konfigurieren des Cloudgeräts

Stellen Sie vor der Durchführung dieser Schritte sicher, dass die [Voraussetzungen für das Cloudgerät](#prerequisites-for-the-cloud-appliance) erfüllt sind.

Führen Sie die folgenden Schritte aus, um eine StorSimple Cloud Appliance zu erstellen.

### <a name="step-1-create-a-cloud-appliance"></a>Schritt 1: Erstellen einer Cloudappliance

Führen Sie die folgenden Schritte aus, um die StorSimple Cloud Appliance zu erstellen.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

Falls das Cloudgerät in diesem Schritt nicht erfolgreich erstellt werden kann, besteht unter Umständen keine Internetverbindung. Weitere Informationen zum Behandeln von Internetverbindungsfehlern bei der Erstellung eines Cloudgeräts finden Sie [hier](#troubleshoot-internet-connectivity-errors).

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>Schritt 2: Konfigurieren und Registrieren der Cloudappliance

Stellen Sie vorab sicher, dass Sie über eine Kopie des Dienstdatenverschlüsselungs-Schlüssels verfügen. Der Dienstdatenverschlüsselungs-Schlüssel wird erstellt, wenn Sie Ihr erstes physisches StorSimple-Gerät beim StorSimple-Geräte-Manager-Dienst registriert haben. Sie wurden aufgefordert, ihn an einem sicheren Ort zu speichern. Wenn Sie über keine Kopie des Schlüssels für die Dienstdatenverschlüsselung verfügen, wenden Sie sich an den Microsoft-Support.

Führen Sie die folgenden Schritte aus, um die StorSimple Cloud Appliance zu konfigurieren und zu registrieren:

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Schritt 3: (Optional) Ändern der Konfigurationseinstellungen des Geräts

Der folgende Abschnitt beschreibt die Gerätekonfigurationseinstellungen, die für die StorSimple Cloud Appliance erforderlich sind, wenn Sie CHAP oder StorSimple Snapshot Manager verwenden oder das Geräteadministratorkennwort ändern möchten.

#### <a name="configure-the-chap-initiator"></a>Konfigurieren des CHAP-Initiators

Dieser Parameter enthält die Anmeldeinformationen, die Ihr Cloudgerät (Ziel) von den Initiatoren (Servern) erwartet, die auf die Volumes zugreifen. Die Initiatoren stellen einen CHAP-Benutzernamen und ein CHAP-Kennwort bereit, um sich bei der Authentifizierung bei Ihrem Gerät zu identifizieren. Ausführliche Schritte finden Sie unter [Konfigurieren von CHAP für Ihr Gerät](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Konfigurieren des CHAP-Ziels

Dieser Parameter enthält die Anmeldeinformationen, die Ihr Cloudgerät verwendet, wenn ein CHAP-fähiger Initiator eine wechselseitige oder bidirektionale Authentifizierung anfordert. Ihr Cloudgerät verwendet einen Benutzernamen und ein Kennwort für das Zurücksetzen von CHAP, um sich beim Authentifizierungsvorgang beim Initiator zu identifizieren.

> [!NOTE]
> Bei den CHAP-Zieleinstellungen handelt es sich um globale Einstellungen. Wenn diese Einstellungen angewendet werden, wird für alle Volumes, die mit dem Cloudgerät verbunden sind, die CHAP-Authentifizierung verwendet.

Ausführliche Schritte finden Sie unter [Konfigurieren von CHAP für Ihr Gerät](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Konfigurieren des StorSimple-Momentaufnahme-Managers

Der StorSimple-Momentaufnahme-Manager befindet sich auf dem Windows-Host und ermöglicht Administratoren die Verwaltung der Sicherungen Ihres StorSimple-Geräts in Form von lokalen und Cloudmomentaufnahmen.

> [!NOTE]
> Für das Cloudgerät handelt es sich beim Windows-Host um einen virtuellen Azure-Computer.

Beim Konfigurieren eines Geräts im StorSimple Snapshot Manager werden Sie aufgefordert, zur Authentifizierung des Speichergeräts die IP-Adresse und das Kennwort des StorSimple-Geräts anzugeben. Ausführliche Schritte finden Sie unter [Konfigurieren des StorSimple Snapshot Manager-Kennworts](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Ändern des Geräteadministratorkennworts

Wenn Sie über die Windows PowerShell-Benutzeroberfläche auf das Cloudgerät zugreifen, müssen Sie ein Geräteadministratorkennwort eingeben. Um für die Sicherheit der Daten zu sorgen, müssen Sie dieses Kennwort ändern, bevor das Cloudgerät verwendet werden kann. Ausführliche Schritte finden Sie unter [Ändern des Geräteadministratorkennworts](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-cloud-appliance"></a>Herstellen einer Remoteverbindung mit dem Cloudgerät

Der Remotezugriff auf Ihr Cloudgerät über die Windows PowerShell-Benutzeroberfläche ist standardmäßig nicht aktiviert. Sie müssen zuerst die Remoteverwaltung auf dem Cloudgerät und dann auf dem Client aktivieren, der zum Zugreifen auf das Cloudgerät verwendet wird.

Im folgenden Verfahren mit zwei Schritten wird beschrieben, wie Sie eine Remoteverbindung mit Ihrem Cloudgerät herstellen.

### <a name="step-1-configure-remote-management"></a>Schritt 1: Remoteverwaltung konfigurieren

Führen Sie die folgenden Schritte aus, um die Remoteverwaltung für Ihre StorSimple Cloud Appliance zu konfigurieren.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>Schritt 2: Zugreifen per Remoteverbindung auf die Cloudappliance

Nachdem Sie die Remoteverwaltung auf dem Cloudgerät aktiviert haben, können Sie Windows PowerShell-Remoting verwenden, um für das Gerät eine Verbindung von einem anderen virtuellen Computer in demselben virtuellen Netzwerk herzustellen. Beispielsweise können Sie eine Verbindung vom virtuellen Hostcomputer herstellen, die Sie konfiguriert und zum Herstellen der iSCSI-Verbindung verwendet haben. Bei den meisten Bereitstellungen öffnen Sie einen öffentlichen Endpunkt für den Zugriff auf den virtuellen Hostcomputer, der für den Zugriff auf das Cloudgerät verwendet werden kann.

> [!WARNING]
> **Aus Sicherheitsgründen wird dringend empfohlen, für Endpunktverbindungen HTTPS zu verwenden und die Endpunkte nach Abschluss der PowerShell-Remotesitzung zu löschen.**

Beim Einrichten von Remoting für Ihr Cloudgerät ist es erforderlich, die Schritte unter [Remoteverbindungen mit dem StorSimple-Gerät](storsimple-8000-remote-connect.md) auszuführen.

## <a name="connect-directly-to-the-cloud-appliance"></a>Herstellen einer direkten Verbindung mit dem Cloudgerät

Sie können auch eine direkte Verbindung mit dem Cloudgerät herstellen. Zum Herstellen einer direkten Verbindung mit dem Cloudgerät von einem anderen Computer außerhalb des virtuellen Netzwerks oder außerhalb der Microsoft Azure-Umgebung müssen Sie zusätzliche Endpunkte erstellen.

Führen Sie die folgenden Schritte aus, um auf dem Cloudgerät einen öffentlichen Endpunkt zu erstellen:

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

Es wird empfohlen, Verbindungen von einem anderen virtuellen Computer im gleichen virtuellen Netzwerk herzustellen, da so die Anzahl der öffentlichen Endpunkte im virtuellen Netzwerk minimiert wird. Stellen Sie die Verbindung mit dem virtuellen Computer in diesem Fall über eine Remotedesktopsitzung her, um anschließend diesen virtuellen Computer wie jeden anderen Windows-Client in einem lokalen Netzwerk zu konfigurieren. Sie müssen die öffentliche Portnummer nicht anfügen, da der Port bereits bekannt ist.

## <a name="get-private-ip-for-the-cloud-appliance"></a>Abrufen der privaten IP für das Cloudgerät

Damit das Cloudgerät eine Verbindung mit dem Hostserver in demselben virtuellen Netzwerk herstellen kann, benötigen Sie die interne oder private IP-Adresse des Cloudgeräts. Führen Sie die folgenden Schritte aus, um die private IP-Adresse des Cloudgeräts zu ermitteln:

1. Navigieren Sie zum zugrunde liegenden virtuellen Computer für Ihr Cloudgerät. Der virtuelle Computer hat denselben Namen wie Ihr Cloudgerät. Navigieren Sie zu **Alle Ressourcen**, geben Sie den Namen des Cloudgeräts und des Abonnements an, und wählen Sie als Typ „Virtuelle Computer“ aus. Wählen Sie in der angezeigten Liste mit den virtuellen Computern den passenden virtuellen Computer für das Cloudgerät aus, und klicken Sie darauf.

     ![Auswählen des virtuellen Computers für Ihr Cloudgerät](./media/storsimple-8000-cloud-appliance-u2/sca-vm.png)

2. Navigieren Sie zu **Einstellungen > Netzwerk**. Im rechten Bereich wird die private IP-Adresse des Cloudgeräts angezeigt. Notieren Sie sich die IP-Adresse.

    ![Abrufen der privaten IP-Adresse für Ihr Cloudgerät](./media/storsimple-8000-cloud-appliance-u2/sca-private-ip-vm-networking.png)

## <a name="work-with-the-storsimple-cloud-appliance"></a>Verwenden der StorSimple Cloud Appliance

Nachdem Sie die StorSimple Cloud Appliance erstellt und konfiguriert haben, können Sie es verwenden. Auf einem Cloudgerät können Sie mit Volumecontainern, Volumes und Sicherungsrichtlinien arbeiten, wie Sie dies von einem physischen StorSimple-Gerät kennen. Der einzige Unterschied besteht darin, dass Sie das Cloudgerät aus der Liste mit den Geräten auswählen müssen. Schritt-für-Schritt-Anleitungen für die verschiedenen Verwaltungsaufgaben des Cloudgeräts finden Sie unter [Use the StorSimple Device Manager service to manage a cloud appliance](storsimple-8000-manager-service-administration.md) (Verwalten Ihres Cloudgeräts mithilfe des StorSimple-Geräte-Manager-Diensts).

In den folgenden Abschnitten werden einige Unterschiede in Bezug auf die Verwendung des Cloudgeräts erläutert.

### <a name="maintain-a-storsimple-cloud-appliance"></a>Verwalten einer StorSimple Cloud Appliance

Da es sich um ein reines Softwaregerät handelt, ist der Verwaltungsaufwand für das Cloudgerät im Vergleich zu einem physischen Gerät minimal.

Sie können für ein Cloudgerät keine Updates durchführen. Verwenden Sie die aktuelle Version der Software, um ein neues Cloudgerät zu erstellen.


### <a name="storage-accounts-for-a-cloud-appliance"></a>Speicherkonten für ein Cloudgerät

Die erstellten Speicherkonten werden vom StorSimple-Geräte-Manager-Dienst, vom Cloudgerät und vom physischen Gerät verwendet. Für die Erstellung von Speicherkonten empfehlen wir Ihnen, im Anzeigenamen eine Regions-ID zu verwenden. So wird sichergestellt, dass die Region über alle Systemkomponenten hinweg einheitlich ist. Um Leistungsprobleme für Cloudgeräte auszuschließen, müssen sich alle Komponenten in derselben Region befinden.

Eine Schrittanleitung finden Sie unter [Hinzufügen eines Speicherkontos](storsimple-8000-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-cloud-appliance"></a>Deaktivieren einer StorSimple Cloud Appliance

Wenn Sie ein Cloudgerät deaktivieren, werden mit der Aktion der virtuelle Computer und die Ressourcen gelöscht, die während der Bereitstellung erstellt wurden. Nach der Deaktivierung kann das Cloudgerät nicht im vorherigen Zustand wiederhergestellt werden. Vor dem Deaktivieren des Cloudgeräts müssen alle davon abhängigen Clients und Hosts beendet oder gelöscht werden.

Das Deaktivieren eines Cloudgeräts führt zu den folgenden Aktionen:

* Das Cloudgerät wird entfernt.
* Der für das Cloudgerät erstellte Betriebssystemdatenträger und die Datenträger für Daten werden entfernt.
* Der bei der Bereitstellung erstellte gehostete Dienst und das virtuelle Netzwerk werden beibehalten. Wenn diese nicht verwendet werden, sollten sie manuell gelöscht werden.
* Cloudmomentaufnahmen, die für das Cloudgerät erstellt wurden, werden beibehalten.

Eine Schritt-für-Schritt-Anleitung finden Sie unter [Deaktivieren und Löschen eines StorSimple-Geräts](storsimple-8000-deactivate-and-delete-device.md).

Sobald das Cloudgerät auf dem Blatt des StorSimple-Geräte-Manager-Diensts als deaktiviert angezeigt wird, können Sie das Cloudgerät aus der Geräteliste auf dem Blatt **Geräte** löschen.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>Starten, Beenden und Neustarten eines Cloudgeräts
Im Gegensatz zu einem physischen StorSimple-Gerät verfügt die StorSimple Cloud Appliance nicht über einen Schalter zum Ein- und Ausschalten. Mitunter kann es aber vorkommen, dass Sie das Cloudgerät beenden und neu starten müssen.

Die einfachste Möglichkeit zum Starten, Beenden und Neustarten eines Cloudgeräts ist die Verwendung des Dienstblatts „Virtuelle Computer“. Navigieren Sie zum Dienst „Virtuelle Computer“. Identifizieren Sie in der Liste mit den VMs die VM, die Ihrem Cloudgerät entspricht (gleicher Name), und klicken Sie auf den VM-Namen. Auf dem Blatt für den virtuellen Computer sehen Sie, dass der Status des Cloudgeräts **Wird ausgeführt** lautet, da es nach der Erstellung standardmäßig gestartet wird. Sie können einen virtuellen Computer zu einem beliebigen Zeitpunkt starten, beenden und neu starten.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>Zurücksetzen auf Werkseinstellungen
Wenn Sie Ihr Cloudgerät neu einrichten möchten, deaktivieren und löschen Sie es und erstellen ein neues Cloudgerät.

## <a name="fail-over-to-the-cloud-appliance"></a>Durchführen eines Failovers zum Cloudgerät
Die Notfallwiederherstellung ist eines der wichtigsten Szenarios, für die die StorSimple Cloud Appliance entwickelt wurde. In diesem Szenario sind das physische StorSimple-Gerät oder das gesamte Datencenter möglicherweise nicht verfügbar. Glücklicherweise können Sie mit einem Cloudgerät den Betrieb an einem anderen Speicherort wiederherstellen. Bei der Notfallwiederherstellung wechseln die Volumecontainer vom Quellgerät den Eigentümer und werden auf das Cloudgerät übertragen.

Die Voraussetzungen für die Notfallwiederherstellung lauten:

* Das Cloudgerät wurde erstellt und konfiguriert.
* Alle Volumes im Volumecontainer sind offline.
* Der Volumecontainer, für den Sie das Failover durchführen, verfügt über eine zugeordnete Cloudmomentaufnahme.

> [!NOTE]
> * Wenn Sie ein Cloudgerät als sekundäres Gerät für die Notfallwiederherstellung einsetzen, sollten Sie bedenken, dass 8010 über 30 TB Standardspeicher und 8020 über 64 TB Storage Premium-Speicher verfügt. Das Cloudgerät mit der höheren Kapazität (8020) ist für ein Szenario zur Notfallwiederherstellung unter Umständen besser geeignet.

Eine Schritt-für-Schritt-Anleitung finden Sie unter [Failover zu einem Cloudgerät](storsimple-8000-device-failover-cloud-appliance.md).

## <a name="delete-the-cloud-appliance"></a>Löschen des Cloudgeräts
Wenn Sie bereits eine StorSimple Cloud Appliance konfiguriert und verwendet haben, aber jetzt erreichen möchten, dass keine weiteren Computegebühren für dessen Nutzung anfallen, müssen Sie das Cloudgerät beenden. Durch das Beenden des Cloudgeräts wird die Zuordnung für die VM aufgehoben. Mit dieser Aktion wird erreicht, dass hierfür keine Gebühren mehr für Ihr Abonnement anfallen. Die Speichergebühren für den Betriebssystemdatenträger und für die Datenträger für Daten werden Ihnen aber weiterhin berechnet.

Sie müssen das Cloudgerät löschen, damit alle Gebühren wegfallen. Sie können das Gerät deaktivieren oder löschen, um die vom Cloudgerät erstellten Sicherungen zu löschen. Weitere Informationen finden Sie unter [Deaktivieren und Löschen eines StorSimple-Geräts](storsimple-8000-deactivate-and-delete-device.md).

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Behandeln von Internetverbindungsfehlern
Wenn beim Erstellen eines Cloudgeräts keine Verbindung mit dem Internet besteht, kann der Erstellungsschritt nicht erfolgreich ausgeführt werden. Führen Sie im Azure-Portal die folgenden Schritte aus, um Probleme mit der Internetverbindung zu behandeln:

1. [Erstellen Sie einen virtuellen Windows-Computer im Azure-Portal.](../virtual-machines/windows/quick-create-portal.md) Für diesen virtuellen Computer müssen dasselbe Speicherkonto, virtuelle Netzwerk und Subnetz wie für Ihr Cloudgerät verwendet werden. Wenn in Azure ein Windows Server-Host vorhanden ist, für den dasselbe Speicherkonto, virtuelle Netzwerk und Subnetz verwendet werden, können Sie zum Behandeln von Internetverbindungsproblemen auch diesen Host verwenden.
2. Melden Sie sich remote bei dem virtuellen Computer an, den Sie im vorherigen Schritt erstellt haben.
3. Öffnen Sie auf dem virtuellen Computer ein Befehlsfenster. (Drücken Sie WINDOWS-TASTE+R, und geben Sie `cmd` ein.)
4. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus:

    `nslookup windows.net`
5. Sollte bei `nslookup` ein Fehler auftreten, kann das Cloudgerät aufgrund eines Internetverbindungsfehlers nicht beim StorSimple-Geräte-Manager-Dienst registriert werden.
6. Passen Sie Ihr virtuelles Netzwerk entsprechend an, um sicherzustellen, dass das Cloudgerät auf Azure-Websites wie _windows.net_ zugreifen kann.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie beim [Verwalten Ihres Cloudgeräts mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md) vorgehen.
* Erfahren Sie, wie Sie [StorSimple-Volumes aus einem Sicherungssatz wiederherstellen](storsimple-8000-restore-from-backup-set-u2.md).