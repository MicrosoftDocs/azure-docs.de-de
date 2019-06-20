---
title: Portalvorbereitung für StorSimple Virtual Array | Microsoft-Dokumentation
description: Erstes Tutorial zum Bereitstellen von virtuellen StorSimple-Arrays mit Vorbereitung des Azure-Portals
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c6f0a6371b38f0271237db0f7d80b831ecc145c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127139"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Bereitstellen von StorSimple Virtual Array – Vorbereiten des Azure-Portals

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Übersicht

Dies ist der erste Artikel einer Reihe von Tutorials, in denen die vollständige Bereitstellung Ihres virtuellen Arrays als Dateiserver oder iSCSI-Server über das Ressourcen-Manager-Modell erläutert wird. In diesem Artikel wird die Vorbereitung beschrieben, die für die Erstellung und Konfiguration Ihres StorSimple-Geräte-Manager-Diensts vor dem Bereitstellen eines virtuellen Arrays erforderlich ist. Außerdem enthält dieser Artikel Links zu einer Prüfliste für die Bereitstellungskonfiguration und die Konfigurationsvoraussetzungen.

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Es wird empfohlen, die Prüfliste für die Bereitstellungskonfiguration durchzugehen, bevor Sie beginnen. Die Vorbereitung des Portals erfordert weniger als 10 Minuten.

Die in diesem Artikel veröffentlichten Informationen gelten für die Bereitstellung von StorSimple Virtual Arrays im Azure-Portal und in der Microsoft Azure Government-Cloud.

### <a name="get-started"></a>Erste Schritte
Der Bereitstellungsworkflow umfasst das Vorbereiten des Portals, das Bereitstellen eines virtuellen Arrays in Ihrer virtualisierten Umgebung und das Durchführen des Setups. Für die StorSimple Virtual Array-Bereitstellung als Dateiserver oder iSCSI-Server benötigen Sie die folgenden Ressourcen in der angegebenen Tabelle.

#### <a name="deployment-articles"></a>Artikel zur Bereitstellung

Sehen Sie sich die folgenden Artikel in der angegebenen Reihenfolge an, um Ihr StorSimple Virtual Array bereitzustellen.

| **#** | **Schritt** | **Aktion** | **Verwendete Dokumente** |
| --- | --- | --- | --- |
| 1. |**Einrichten des Azure-Portals** |Erstellen und konfigurieren Sie Ihren StorSimple-Geräte-Manager-Dienst, bevor Sie ein StorSimple Virtual Array bereitstellen. |[Vorbereiten des Portals](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Bereitstellen des Virtual Array** |Stellen Sie für Hyper-V ein StorSimple Virtual Array auf einem Hostsystem mit Hyper-V unter Windows Server 2012 R2, Windows Server 2012 oder Windows Server 2008 R2 bereit, und stellen Sie die Verbindung her. <br></br> <br></br> Stellen Sie für VMware ein StorSimple Virtual Array auf einem Hostsystem mit VMware ESXi 5.0, 5.5, 6.0 oder 6.5 bereit, und stellen Sie eine Verbindung her.<br></br> |[Bereitstellen eines Virtual Arrays in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Bereitstellen eines virtuellen Arrays in VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Einrichten des Virtual Array** |Führen Sie für Ihren Dateiserver das anfängliche Setup durch, registrieren Sie Ihren StorSimple-Dateiserver, und führen Sie die Geräteinstallation durch. Anschließend können Sie die SMB-Freigaben bereitstellen. <br></br> <br></br> Führen Sie für Ihren iSCSI-Server das anfängliche Setup durch, registrieren Sie Ihren StorSimple-iSCSI-Server, und führen Sie die Geräteinstallation durch. Anschließend können Sie die iSCSI-Volumes bereitstellen. |[Einrichten des virtuellen Arrays als Dateiserver](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Einrichten des virtuellen Arrays als iSCSI-Server](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Sie können jetzt mit der Einrichtung des Azure-Portals beginnen.

## <a name="configuration-checklist"></a>Prüfliste für die Konfiguration

Die Prüfliste für die Konfiguration enthält die Informationen, die Sie vor der Konfiguration der Software auf dem StorSimple Virtual Array zusammenstellen müssen. Wenn Sie diese Informationen im Voraus sammeln, können Sie das StorSimple-Gerät in Ihrer Umgebung schneller bereitstellen. Welche der folgenden Prüflisten Sie benötigen, hängt davon ab, ob Sie das StorSimple Virtual Array als Dateiserver oder iSCSI-Server bereitstellen.

* Laden Sie die PDF-Datei [StorSimple Virtual Array File Server Configuration Checklist](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)(Konfigurationsprüfliste für StorSimple Virtual Array-Dateiserver) herunter.
* Laden Sie die PDF-Datei [StorSimple Virtual Array iSCSI Server Configuration Checklist](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)(Konfigurationsprüfliste für StorSimple Virtual Array-iSCSI-Server) herunter.

## <a name="prerequisites"></a>Voraussetzungen

Hier finden Sie die Konfigurationsvoraussetzungen für Ihren StorSimple-Geräte-Manager-Dienst, Ihr StorSimple Virtual Array und das Netzwerk des Rechenzentrums.

### <a name="for-the-storsimple-device-manager-service"></a>Voraussetzungen für den StorSimple-Geräte-Manager-Dienst

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie besitzen ein Microsoft-Konto mit Anmeldeinformationen für den Zugriff.
* Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.
* Ihr Microsoft Azure-Abonnement sollte für den StorSimple-Geräte-Manager-Dienst aktiviert sein.

### <a name="for-the-storsimple-virtual-array"></a>Voraussetzungen für das StorSimple Virtual Array

Stellen Sie Folgendes sicher, bevor Sie ein virtuelles Array bereitstellen:

* Sie haben Zugriff auf ein Hostsystem mit Hyper-V unter Windows Server 2008 R2 oder höher oder VMware (ESXi 5.0, 5.5, 6.0 oder 6.5), das zum Bereitstellen eines Geräts verwendet werden kann.
* Das Hostsystem verfügt für die Bereitstellung des virtuellen Arrays über die folgenden Ressourcen:
  
  * Mindestens 4 Kerne
  * Mindestens 8 GB RAM Wenn Sie planen, das virtuelle Array als Dateiserver zu konfigurieren, unterstützen 8 GB 2 Millionen Dateien. Sie benötigen 16 GB RAM, um 2–4 Millionen Dateien zu unterstützen.
  * Eine Netzwerkschnittstelle
  * Einen virtuellen Datenträger mit 500 GB für Systemdaten

### <a name="for-the-datacenter-network"></a>Für das Netzwerk des Rechenzentrums

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Das Netzwerk in Ihrem Rechenzentrum ist gemäß den Netzwerkanforderungen für das StorSimple-Gerät konfiguriert. Weitere Informationen finden Sie unter [Systemanforderungen für das StorSimple Virtual Array](storsimple-ova-system-requirements.md).
* Für das StorSimple Virtual Array steht jederzeit eine Internetbandbreite von 5 MBit/s (oder mehr) zur Verfügung. Diese Bandbreite sollte nicht gemeinsam mit anderen Anwendungen genutzt werden.

## <a name="step-by-step-preparation"></a>Schritt-für-Schritt-Vorbereitung

Verwenden Sie die folgende Schritt-für-Schritt-Anleitung, um Ihr Portal für den StorSimple-Geräte-Manager-Dienst vorzubereiten.

## <a name="step-1-create-a-new-service"></a>Schritt 1: Erstellen eines neuen Diensts

Mit einer einzelnen Instanz des StorSimple-Geräte-Manager-Diensts können mehrere StorSimple Virtual Arrays verwaltet werden. Führen Sie die folgenden Schritte aus, um eine Instanz des StorSimple-Geräte-Manager-Diensts zu erstellen. Falls Sie einen vorhandenen StorSimple-Geräte-Manager-Dienst zum Verwalten Ihrer virtuellen Arrays verwenden, können Sie diesen Schritt überspringen und mit Folgendem fortfahren: [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben, müssen Sie mindestens ein Speicherkonto erstellen, nachdem Sie einen Dienst erstellt haben.
> 
> * Wenn Sie nicht automatisch ein Speicherkonto erstellt haben, finden Sie unter [Konfigurieren eines neuen Speicherkontos für den Dienst](#optional-step-configure-a-new-storage-account-for-the-service) ausführliche Anweisungen.
> * Wenn Sie die automatische Erstellung eines Speicherkontos aktiviert haben, fahren Sie mit Folgendem fort: [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Schritt 2: Abrufen des Dienstregistrierungsschlüssels

Nachdem der StorSimple-Geräte-Manager-Dienst eingerichtet wurde und ausgeführt wird, müssen Sie den Dienstregistrierungsschlüssel abrufen. Dieser Schlüssel wird zum Registrieren Ihres StorSimple-Geräts sowie zum Herstellen einer Verbindung mit dem Dienst verwendet.

Führen Sie die folgenden Schritte im [Azure-Portal](https://portal.azure.com/) aus.

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Der Dienstregistrierungsschlüssel wird zur Registrierung aller StorSimple-Geräte-Manager-Geräte verwendet, die für den StorSimple-Geräte-Manager-Dienst registriert werden müssen.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Schritt 3: Herunterladen des Image des virtuellen Arrays

Nachdem Sie jetzt über den Dienstregistrierungsschlüssel verfügen, müssen Sie nun das passende Image des virtuellen Arrays herunterladen, um auf Ihrem Hostsystem ein virtuelles Array bereitzustellen. Die Images von virtuellen Arrays sind betriebssystemspezifisch und können im Azure-Portal von der Seite „Schnellstart“ heruntergeladen werden.

> [!IMPORTANT]
> Die Software, die auf dem StorSimple Virtual Array ausgeführt wird, kann nur in Verbindung mit dem StorSimple-Geräte-Manager-Dienst verwendet werden.
> 
> 

Führen Sie die folgenden Schritte im [Azure-Portal](https://portal.azure.com/) aus.

#### <a name="to-get-the-virtual-array-image"></a>So rufen Sie das Image des virtuellen Arrays ab

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. 
2. Klicken Sie im Azure-Portal auf **Durchsuchen > StorSimple-Geräte-Manager**.
3. Wählen Sie einen vorhandenen StorSimple-Geräte-Manager-Dienst aus. Klicken Sie auf dem Blatt **StorSimple-Geräte-Manager** auf **Schnellstart**. 
4. Klicken Sie auf den Link für das Image, das Sie aus dem Microsoft Download Center herunterladen möchten. Die Imagedateien sind ca. 4,8 GB groß.
   
   * VHDX für Hyper-V unter Windows Server 2012 und höher
   * VHD für Hyper-V unter Windows Server 2008 R2 und höher
   * VMDK für VMWare ESXi 5.0, 5.5, 6.0 oder 6.5
5. Laden Sie die Datei auf einen lokalen Datenträger herunter, und entzippen Sie sie. Notieren Sie den Speicherort der entzippten Datei.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Optionaler Schritt: Konfigurieren eines neuen Speicherkontos für den Dienst

Dies ist ein optionaler Schritt, der nur dann ausgeführt werden muss, wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben.

Wenn Sie ein Azure-Speicherkonto in einer anderen Region erstellen müssen, finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-quickstart-create-account.md) entsprechende Schrittanleitungen.

Führen Sie die folgenden Schritte im [Azure-Portal](https://ms.portal.azure.com/) auf der Seite für den StorSimple-Geräte-Manager-Dienst aus, um ein vorhandenes Microsoft Azure-Speicherkonto hinzuzufügen.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>So fügen Sie Anmeldeinformationen für ein Speicherkonto hinzu, das zum gleichen Azure-Abonnement wie der Geräte-Manager-Dienst gehört

1. Navigieren Sie zu Ihrem Geräte-Manager-Dienst, und wählen Sie ihn aus. Doppelklicken Sie anschließend darauf. Daraufhin wird das Blatt **Übersicht** geöffnet.
2. Wählen Sie die **Anmeldeinformationen des Speicherkontos** innerhalb des Abschnitts **Konfiguration** aus.
3. Klicken Sie auf **Hinzufügen**.
4. Gehen Sie auf dem Blatt **Hinzufügen eines Speicherkontos** wie folgt vor:
   
   1. Wählen Sie für **Abonnement** die Option **Aktuell** aus.
   
   2. Geben Sie den Namen Ihres Azure-Speicherkontos an.
   
   3. Wählen Sie **Aktivieren** aus, um einen sicheren Kanal für die Netzwerkkommunikation zwischen Ihrem StorSimple-Gerät und der Cloud zu erstellen. Wählen Sie **Deaktivieren** nur dann aus, wenn Sie in einer Private Cloud arbeiten.
   
   4. Klicken Sie auf **Hinzufügen**. Sie werden benachrichtigt, nachdem das Speicherkonto erfolgreich erstellt wurde.<br></br>
   
      ![Hinzufügen der Anmeldeinformationen für ein vorhandenes Speicherkonto](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Nächster Schritt

Im nächsten Schritt wird ein virtueller Computer für Ihr StorSimple Virtual Array bereitgestellt. Sehen Sie sich je nach Hostbetriebssystem die folgenden ausführlichen Anweisungen an:

* [Bereitstellen eines Virtual Array in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Bereitstellen eines Virtual Array in VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

