---
title: Einrichten eines Prozessservers mit horizontaler Skalierung während der Notfallwiederherstellung von VMware-VMs und physischen Servern mit Azure Site Recovery | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie einen Prozessserver mit horizontaler Skalierung während der Notfallwiederherstellung von VMware-VMs und physischen Servern einrichten.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 40f912122e6ffb9cccbd32a747f6f0d46fd6c330
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019135"
---
# <a name="scale-with-additional-process-servers"></a>Skalieren mit zusätzlichen Prozessservern

Wenn Sie VMware-VMs oder physische Server mithilfe von [Site Recovery](site-recovery-overview.md) in Azure replizieren, wird standardmäßig ein Prozessserver auf dem Konfigurationsservercomputer installiert und zum Koordinieren der Datenübertragung zwischen Site Recovery und Ihrer lokalen Infrastruktur verwendet. Um die Kapazität zu erhöhen und die Replikationsbereitstellung aufzuskalieren, können Sie zusätzliche eigenständige Prozessserver hinzufügen. In diesem Artikel wird beschrieben, wie Sie einen Prozessserver mit horizontaler Skalierung einrichten.

## <a name="before-you-start"></a>Vorbereitung

### <a name="capacity-planning"></a>Kapazitätsplanung

Stellen Sie sicher, dass Sie eine [Kapazitätsplanung](site-recovery-plan-capacity-vmware.md) für die VMware-Replikation durchgeführt haben. Dadurch können Sie feststellen, wie und wann Sie zusätzliche Prozessserver bereitstellen sollten.

Ab Version 9.24 wird während der Auswahl des Prozessservers für neue Replikationen eine Anleitung hinzugefügt. Der Prozessserver wird auf der Grundlage bestimmter Kriterien als „Fehlerfrei“, „Warnung“ und „Kritisch“ gekennzeichnet. Informationen zu verschiedenen Szenarien, die den Zustand des Prozessservers beeinflussen können, finden Sie unter den [process server alerts (Warnungen zum Prozessserver)](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> Die Verwendung einer geklonten Prozessserverkomponente wird nicht unterstützt. Befolgen Sie die Schritte in diesem Artikel für jede PS-Hochskalierung.

### <a name="sizing-requirements"></a>Größenanforderungen 

Prüfen Sie die in der Tabelle zusammengefassten Größenanforderungen. Allgemein gilt: Wenn Sie Ihre Bereitstellung auf mehr als 200 Quellcomputer hochskalieren müssen oder Ihre gesamte tägliche Änderungsrate 2 TB übersteigt, benötigen Sie zusätzliche Prozessserver zur Bewältigung des Datenverkehrsaufkommens.

| **Zusätzlicher Prozessserver** | **Größe des Cachedatenträgers** | **Datenänderungsrate** | **Geschützte Computer** |
| --- | --- | --- | --- |
|4 vCPUs (2 Sockets * 2 Kerne \@ 2,5 GHz), 8 GB Arbeitsspeicher |300 GB |250 GB oder weniger |Bis zu 85 Computer replizieren. |
|8 vCPUs (2 Sockets * 4 Kerne \@ 2,5 GHz), 12 GB Arbeitsspeicher |600 GB |250 GB bis 1 TB |Zwischen 85 und 150 Computer replizieren. |
|12 vCPUs (2 Sockets * 6 Kerne \@ 2,5 GHz), 24 GB Arbeitsspeicher |1 TB |1 TB bis 2 TB |Zwischen 150 und 225 Computer replizieren. |

Jeder geschützte Quellcomputer ist mit drei Datenträgern von jeweils 100 GB konfiguriert.

### <a name="prerequisites"></a>Voraussetzungen

In der folgenden Tabelle sind die Voraussetzungen für den zusätzlichen Prozessserver zusammengefasst.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Herunterladen der Installationsdatei

Laden Sie die Installationsdatei für den Prozessserver wie folgt herunter:

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu Ihrem Recovery Services-Tresor.
2. Öffnen Sie **Site Recovery-Infrastruktur** > **VMware und physische Computer** > **Konfigurationsserver** (unter „Für VMware und physische Computer“).
3. Wählen Sie den Konfigurationsserver aus, um die Detailsseite des Servers aufzurufen. Klicken Sie dann auf **+ Prozessserver**.
4. Wählen Sie unter **Prozessserver hinzufügen** >  **Geben Sie an, wo Sie Ihren Prozessserver bereitstellen möchten** die Option **Prozessserver für horizontales Hochskalieren lokal bereitstellen** aus.

   ![Seite „Server hinzufügen“](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klicken Sie auf **Einheitliches Setup von Microsoft Azure Site Recovery herunterladen**. Dadurch wird die neueste Version der Installationsdatei heruntergeladen.

   > [!WARNING]
   > Bei der Installationsversion des Prozessservers sollte es sich um die gleiche oder eine frühere Version wie die von Ihnen ausgeführte Version des Konfigurationsservers handeln. Eine einfache Möglichkeit, die Versionskompatibilität sicherzustellen, ist die Verwendung des gleichen Installers, den Sie zuletzt zum Installieren oder Aktualisieren Ihres Konfigurationsservers verwendet haben.

## <a name="install-from-the-ui"></a>Installieren über die Benutzeroberfläche

Führen Sie die Installation auf folgende Weise aus. Nachdem Sie den Prozessserver eingerichtet haben, migrieren Sie Quellcomputer, damit diese ihn verwenden können.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Installieren über die Befehlszeile

Die Installation erfolgt, indem Sie den folgenden Befehl ausführen:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMware/NonVMware>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Die Befehlszeilenparameter lauten wie folgt:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Beispiel:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMware" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Erstellen einer Datei mit Proxyeinstellungen

Wenn Sie einen Proxy einrichten müssen, nimmt der ProxySettingsFilePath-Parameter eine Datei als Eingabe an. Sie können die Datei auf folgende Weise erstellen und als Eingabe an den ProxySettingsFilePath-Parameter übergeben.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Nächste Schritte
Informationen zum [Verwalten von Prozessservereinstellungen](vmware-azure-manage-process-server.md)
