---
title: Konfigurieren der in Microsoft Azure gehosteten VM für den Azure Marketplace
description: Es wird beschrieben, wie Sie für eine in Azure gehostete VM die Größe festlegen und sie aktualisieren und generalisieren.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 3d38efadfb8ad13d072056ef851be99d9540ad4b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938429"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurieren der in Azure gehosteten VM

In diesem Artikel wird beschrieben, wie Sie für einen in Azure gehosteten virtuellen Computer (VM) die Größe festlegen und ihn aktualisieren und generalisieren.  Diese Schritte sind erforderlich, um Ihre VM für die Bereitstellung über den Azure Marketplace vorzubereiten.


## <a name="sizing-the-vhds"></a>Festlegen der Größe von VHDs

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Wenn Sie einen der virtuellen Computer ausgewählt haben, die mit einem Betriebssystem (und optional weiteren Diensten) vorkonfiguriert sind, verfügen Sie bereits über eine Azure-VM mit einer Standardgröße. Eine Beschreibung finden Sie unter [Registerkarte für VM-SKUs](./cpp-skus-tab.md).  Ein Starten Ihrer Lösung mit einem vorkonfigurierten Betriebssystem ist die empfohlene Vorgehensweise.  Wenn Sie ein Betriebssystem aber manuell installieren, müssen Sie die Größe Ihrer primären VHD in Ihrem VM-Image festlegen:

- Für Windows sollte die Betriebssystem-VHD als virtuelle Festplatte mit 127 bis 128 GB und einem festen Format erstellt werden. 
- Für Linux sollte diese VHD als virtuelle Festplatte mit 30 bis 50 GB und einem festen Format erstellt werden.

Beträgt die physische Größe weniger als 127 bis 128 GB, sollte die VHD von geringer Dichte sein. Die bereitgestellten Windows- und SQL Server-Images erfüllen diese Anforderungen. Ändern Sie weder das Format noch die Größe der VHD. 

Datenträger können bis zu 1 TB groß sein. Bedenken Sie bei der Entscheidung über deren Größe, dass Kunden die Größe von VHDs in einem Image zum Zeitpunkt der Bereitstellung nicht verändern können. Datenträger-VHDs sollten als VHDs mit festem Format erstellt werden. Sie sollten außerdem eine geringe Dichte aufweisen. Datenträger können anfänglich leer sein oder Daten enthalten.


## <a name="install-the-most-current-updates"></a>Installieren der aktuellsten Updates

Die Basisimages von Betriebssystem-VMs enthalten die aktuellen Updates bis zum Veröffentlichungsdatum. Stellen Sie vor dem Veröffentlichen der von Ihnen erstellten Betriebssystem-VHD sicher, dass Sie das Betriebssystem und alle installierten Dienste mit allen aktuellen Sicherheits- und Wartungspatches aktualisieren.

Führen Sie für Windows Server 2016 den Befehl **Nach Updates suchen** aus.  Informationen zu älteren Versionen von Windows finden Sie unter [Beziehen von Updates über Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows Update installiert automatisch die aktuellen kritischen und wichtigen Sicherheitsupdates.

Für Linux-Distributionen werden Updates normalerweise mit einem Befehlszeilentool oder grafischen Hilfsprogramm heruntergeladen und installiert.  Unter Ubuntu Linux können beispielsweise der Befehl [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) und das Tool [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) zum Aktualisieren des Betriebssystems verwendet werden.


## <a name="perform-additional-security-checks"></a>Durchführen von zusätzlichen Sicherheitsüberprüfungen

Sie sollten für Ihre Lösungsimages im Azure Marketplace eine hohe Sicherheitsebene sicherstellen.  Der folgende Artikel enthält eine Checkliste zu den Sicherheitskonfigurationen und -verfahren als Hilfe beim Erreichen dieses Ziels: [Sicherheitsempfehlungen für Azure Marketplace-Images](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Einige dieser Empfehlungen gelten speziell für Linux-basierte Images, aber die meisten sind für beliebige VM-Images geeignet. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Durchführen der benutzerdefinierten Konfiguration und von geplanten Aufgaben

Falls zusätzliche Einstellungen konfiguriert werden müssen, ist der empfohlene Ansatz die Verwendung einer geplanten Aufgabe, die beim Start ausgeführt wird. So ist es möglich, nach der Bereitstellung des virtuellen Computers letzte Änderungen daran vorzunehmen.  Beachten Sie auch die folgenden Empfehlungen:
- Wenn es sich um eine Aufgabe mit einmaliger Ausführung handelt, wird empfohlen, dass die Aufgabe nach der erfolgreichen Durchführung automatisch gelöscht wird.
- Für Konfigurationen sollten keine anderen Laufwerke als C oder D verwendet werden, da nur für diese beiden Laufwerke garantiert ist, dass sie immer vorhanden sind. Laufwerk C ist der Betriebssystem-Datenträger, und Laufwerk D ist der temporäre lokale Datenträger.

Weitere Informationen zu Linux-Anpassungen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Generalisieren des Images

Alle Images im Azure Marketplace müssen allgemein wiederverwendbar sein. Zur Erreichung dieser Wiederverwendbarkeit muss die Betriebssystem-VHD *generalisiert* werden. Dies ist ein Vorgang, bei dem alle instanzspezifischen Bezeichner und Softwaretreiber von einer VM entfernt werden.

### <a name="windows"></a>Windows

Windows-Betriebssystemdatenträger werden mit dem [Sysprep-Tool](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) generalisiert. Wenn Sie das Betriebssystem später dann aktualisieren oder neu konfigurieren, müssen Sie Sysprep erneut ausführen. 

> [!WARNING]
>  Da Updates unter Umständen automatisch ausgeführt werden, sollten Sie die VM nach der Ausführung von Sysprep bis zur Bereitstellung herunterfahren.  Durch das Herunterfahren wird vermieden, dass nachfolgende Updates instanzspezifische Änderungen am VHD-Betriebssystem oder den installierten Diensten vornehmen.

Weitere Informationen zum Ausführen von Sysprep finden Sie unter [Schritte zum Generalisieren einer VHD](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#steps-to-generalize-a-vhd).

### <a name="linux"></a>Linux

Mit dem folgenden aus zwei Schritten bestehenden Prozess wird eine Linux-VM generalisiert und als separate VM erneut bereitgestellt.  Weitere Informationen finden Sie unter [Vorgehensweise zum Erstellen eines Image von einem virtuellen Computer oder einer VHD](../../../virtual-machines/linux/capture-image.md). 

#### <a name="remove-the-azure-linux-agent"></a>Entfernen des Azure Linux-Agents
1.  Stellen Sie mit einem SSH-Client eine Verbindung mit Ihrer Linux-VM her.
2.  Geben Sie im SSH-Fenster den folgenden Befehl ein: <br/>
    `sudo waagent -deprovision+user`
3.  Geben Sie `y` ein, um fortzufahren. (Sie können dem vorherigen Befehl den Parameter `-force` hinzufügen, um diesen Bestätigungsschritt zu vermeiden.)
4.  Geben Sie nach der Ausführung dieses Befehls den Befehl `exit` ein, um den SSH-Client zu schließen.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Erfassen des Images
1.  Navigieren Sie zum Azure-Portal, wählen Sie Ihre Ressourcengruppe (RG) aus, und heben Sie die Zuordnung der VM auf.
2.  Ihre VHD ist jetzt generalisiert, und Sie können mit dieser VHD eine neue VM erstellen.


## <a name="create-one-or-more-copies"></a>Erstellen von Kopien

Die Erstellung von Kopien einer VM ist häufig nützlich, um Sicherungen zu erstellen, Tests, benutzerdefinierte Failover oder einen Lastenausgleich durchzuführen, unterschiedliche Konfigurationen einer Lösung anzubieten usw. Informationen zum Duplizieren und Herunterladen einer primären VHD, um einen nicht verwalteten Klon zu verwalten, finden Sie unter:

- Linux-VM: [Herunterladen einer Linux-VHD von Azure](../../../virtual-machines/linux/download-vhd.md)
- Windows-VM: [Herunterladen einer Windows-VHD von Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Nächste Schritte

Nachdem Ihre VM konfiguriert wurde, können Sie [einen virtuellen Computer über eine virtuelle Festplatte bereitstellen](./cpp-deploy-vm-vhd.md).
