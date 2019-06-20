---
title: Automatisieren der Installation des Azure Site Recovery-Mobilitätsdiensts für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure mithilfe von System Center Configuration Manager | Microsoft-Dokumentation
description: Dieser Artikel unterstützt Sie beim Automatisieren der Installation des Mobilitätsdiensts mit System Center Configuration Manager für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure mithilfe von Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 35c317c4b73e9a22e3b0d6192abcfc2a596066b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60598265"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatisieren der Mobility Service-Installation mit System Center Configuration Manager

Mobility Service wird auf VMware-VMs und physischen Servern installiert, die Sie mit [Azure Site Recovery](site-recovery-overview.md) in Azure replizieren möchten.

Dieser Artikel zeigt Ihnen an einem Beispiel, wie System Center Configuration Manager verwendet wird, um Azure Site Recovery Mobility Service auf einer VMware-VM bereitzustellen. Das Verwenden eines Softwarebereitstellungstools wie Configuration Manager bietet Ihnen folgende Vorteile:

* Planen von neuen Installationen und Upgrades während eines geplanten Wartungsfensters für Softwareupdates
* Skalieren der gleichzeitigen Bereitstellung auf Hunderten von Servern

In diesem Artikel wird anhand von System Center Configuration Manager 2012 R2 die Bereitstellungsaktivität veranschaulicht. Es wird angenommen, dass Sie Version **9.9.4510.1** oder höher von Mobility Service verwenden.

Sie können die Mobility Service-Installation auch mit [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md) automatisieren.

## <a name="prerequisites"></a>Voraussetzungen

1. Ein Softwarebereitstellungstool wie Configuration Manager, das bereits in Ihrer Umgebung bereitgestellt wurde.
2. Sie sollten zwei [Gerätesammlungen](https://technet.microsoft.com/library/gg682169.aspx) erstellen: eine für alle **Windows-Server** und eine für alle **Linux-Server**, die mithilfe von Site Recovery geschützt werden sollen.
3. Ein Konfigurationsserver, der bereits im Recovery Services-Tresor registriert ist
4. Eine sichere Netzwerkdateifreigabe (SMB-Freigabe), auf die der Configuration Manager-Computer zugreifen kann

## <a name="deploy-on-windows-machines"></a>Bereitstellen auf Windows-Computern
> [!NOTE]
> Dieser Artikel setzt voraus, dass die IP-Adresse des Konfigurationsservers 192.168.3.121 und die sichere Netzwerkdateifreigabe \\\ContosoSecureFS\MobilityServiceInstallers ist.

### <a name="prepare-for-deployment"></a>Vorbereiten der Bereitstellung
1. Erstellen Sie einen Ordner auf der Netzwerkfreigabe, und nennen Sie ihn **MobSvcWindows**.
2. Melden Sie sich bei Ihrem Konfigurationsserver an, und öffnen Sie eine Administratoreingabeaufforderung.
3. Führen Sie die folgenden Befehle aus, um eine Passphrasedatei zu generieren:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopieren Sie die Datei **MobSvc.passphrase** in den Ordner **MobSvcWindows** auf der Netzwerkfreigabe.
5. Wechseln Sie zum Installerrepository auf dem Konfigurationsserver, indem Sie den folgenden Befehl ausführen:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Kopieren Sie **Microsoft ASR\_UA\_*Version*\_Windows\_GA\_*Datum*\_Release.exe** in den Ordner **MobSvcWindows** auf der Netzwerkfreigabe.
7. Kopieren Sie den folgenden Code, und speichern Sie ihn als **install.bat** im Ordner **MobSvcWindows**.

   > [!NOTE]
   > Ersetzen Sie den Platzhalter [CSIP] in diesem Skript durch die tatsächlichen Werte der IP-Adresse des Konfigurationsserver.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log


```

### <a name="create-a-package"></a>Erstellen eines Pakets

1. Melden Sie sich bei der Configuration Manager-Konsole an.
2. Navigieren Sie zu **Softwarebibliothek** > **Anwendungsverwaltung** > **Pakete**.
3. Klicken Sie mit der rechten Maustaste auf **Pakete**, und wählen Sie **Paket erstellen** aus.
4. Geben Sie Werte für Name, Beschreibung, Hersteller, Sprache und Version an.
5. Aktivieren Sie das Kontrollkästchen **Dieses Paket enthält Quelldateien**.
6. Klicken Sie auf die Schaltfläche **Durchsuchen**, und wählen Sie die Netzwerkfreigabe aus, in der der Installer gespeichert ist (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

   ![Screenshot des Assistenten zum Erstellen von Paketen und Programmen](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Wählen Sie auf der Seite **Typ des zu erstellenden Programms auswählen** die Option **Standardprogramm** aus, und klicken Sie auf **Weiter**.

   ![Screenshot des Assistenten zum Erstellen von Paketen und Programmen](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Geben Sie auf der Seite **Informationen zu diesem Standardprogramm angeben** die folgenden Eingaben an, und klicken Sie auf **Weiter**. (Bei den anderen Eingaben können Sie die Standardwerte übernehmen.)

   | **Parametername** | **Wert** |
   |--|--|
   | NAME | Microsoft Azure Mobility Service installieren (Windows) |
   | Befehlszeile | install.bat |
   | Programm kann ausgeführt werden | Unabhängig von Benutzeranmeldung |

   ![Screenshot des Assistenten zum Erstellen von Paketen und Programmen](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Wählen Sie auf der nächsten Seite die Zielbetriebssysteme aus. Mobility Service kann nur unter Windows Server 2012 R2, Windows Server 2012 und Windows Server 2008 R2 installiert werden.

   ![Screenshot des Assistenten zum Erstellen von Paketen und Programmen](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. Klicken Sie zweimal auf **Weiter**, um den Assistenten abzuschließen.


> [!NOTE]
> Das Skript unterstützt Neuinstallationen von Mobility Service-Agents und Updates bereits installierter Agents.

### <a name="deploy-the-package"></a>Bereitstellen des Pakets
1. Klicken Sie in der Configuration Manager-Konsole mit der rechten Maustaste auf das Paket, und wählen Sie **Inhalt verteilen** aus.
   ![Screenshot der Configuration Manager-Konsole](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Wählen Sie die **[Verteilungspunkte](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** aus, auf die die Pakete kopiert werden sollen.
3. Schließen Sie den Assistenten ab. Dann beginnt die Replikation des Pakets auf die angegebenen Verteilungspunkte.
4. Klicken Sie nach Abschluss der Paketverteilung mit der rechten Maustaste auf das Paket, und wählen Sie **Bereitstellen** aus.
   ![Screenshot der Configuration Manager-Konsole](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Wählen Sie die Windows Server-Gerätesammlung, die Sie im Abschnitt „Voraussetzungen“ erstellt haben, als Zielsammlung für die Bereitstellung aus.

   ![Screenshot des Assistenten zum Bereitstellen von Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Wählen Sie auf der Seite **Das Inhaltsziel angeben** Ihre **Verteilungspunkte** aus.
7. Stellen Sie auf der Seite **Geben Sie Einstellungen an, um die Bereitstellung dieser Software zu steuern** sicher, dass der gewünschte Zweck **Erforderlich** ist.

   ![Screenshot des Assistenten zum Bereitstellen von Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Geben Sie auf der Seite **Zeitplan für diese Bereitstellung angeben** einen Zeitplan an. Weitere Informationen finden Sie unter [Bereitstellen von Paketen und Programmen in Configuration Manager](https://technet.microsoft.com/library/gg682178.aspx).
9. Konfigurieren Sie die Eigenschaften auf der Seite **Verteilungspunkte** nach den Anforderungen Ihres Rechenzentrums. Schließen Sie dann den Assistenten ab.

> [!TIP]
> Um unnötige Neustarts zu vermeiden, planen Sie die Installation des Pakets während Ihrer monatlichen Wartungsfensters oder der Zeitfenster für Softwareupdates.

Sie können den Bereitstellungsstatus mithilfe der Configuration Manager-Konsole überwachen. Wechseln Sie zu **Überwachung** > **Bereitstellungen** >  *[Ihr Paketname]* .

  ![Screenshot der Configuration Manager-Option zum Überwachen von Bereitstellungen](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Bereitstellen auf Linux-Computern
> [!NOTE]
> Dieser Artikel setzt voraus, dass die IP-Adresse des Konfigurationsservers 192.168.3.121 und die sichere Netzwerkdateifreigabe \\\ContosoSecureFS\MobilityServiceInstallers ist.

### <a name="prepare-for-deployment"></a>Vorbereiten der Bereitstellung
1. Erstellen Sie einen Ordner auf der Netzwerkfreigabe, und nennen Sie ihn **MobSvcLinux**.
2. Melden Sie sich bei Ihrem Konfigurationsserver an, und öffnen Sie eine Administratoreingabeaufforderung.
3. Führen Sie die folgenden Befehle aus, um eine Passphrasedatei zu generieren:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopieren Sie die Datei **MobSvc.passphrase** in den Ordner **MobSvcLinux** auf der Netzwerkfreigabe.
5. Wechseln Sie zum Installerrepository auf dem Konfigurationsserver, indem Sie den folgenden Befehl ausführen:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Kopieren Sie die folgenden Dateien in den Ordner **MobSvcLinux** auf der Netzwerkfreigabe:
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Kopieren Sie den folgenden Code, und speichern Sie ihn als **install_linux.sh** im Ordner **MobSvcLinux**.
   > [!NOTE]
   > Ersetzen Sie den Platzhalter [CSIP] in diesem Skript durch die tatsächlichen Werte der IP-Adresse des Konfigurationsserver.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Erstellen eines Pakets

1. Melden Sie sich bei der Configuration Manager-Konsole an.
2. Navigieren Sie zu **Softwarebibliothek** > **Anwendungsverwaltung** > **Pakete**.
3. Klicken Sie mit der rechten Maustaste auf **Pakete**, und wählen Sie **Paket erstellen** aus.
4. Geben Sie Werte für Name, Beschreibung, Hersteller, Sprache und Version an.
5. Aktivieren Sie das Kontrollkästchen **Dieses Paket enthält Quelldateien**.
6. Klicken Sie auf die Schaltfläche **Durchsuchen**, und wählen Sie die Netzwerkfreigabe aus, in der der Installer gespeichert ist (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

   ![Screenshot des Assistenten zum Erstellen von Paketen und Programmen](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Wählen Sie auf der Seite **Typ des zu erstellenden Programms auswählen** die Option **Standardprogramm** aus, und klicken Sie auf **Weiter**.

   ![Screenshot des Assistenten zum Erstellen von Paketen und Programmen](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Geben Sie auf der Seite **Informationen zu diesem Standardprogramm angeben** die folgenden Eingaben an, und klicken Sie auf **Weiter**. (Bei den anderen Eingaben können Sie die Standardwerte übernehmen.)

    | **Parametername** | **Wert** |
   |--|--|
   | NAME | Microsoft Azure Mobility Service installieren (Linux) |
   | Befehlszeile | ./install_linux.sh |
   | Programm kann ausgeführt werden | Unabhängig von Benutzeranmeldung |

   ![Screenshot des Assistenten zum Erstellen von Paketen und Programmen](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. Wählen Sie auf der nächsten Seite **Dieses Programm kann auf jeder Plattform ausgeführt werden.**
   ![Screenshot des Assistenten zum Erstellen von Paketen und Programmen](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Klicken Sie zweimal auf **Weiter**, um den Assistenten abzuschließen.

> [!NOTE]
> Das Skript unterstützt Neuinstallationen von Mobility Service-Agents und Updates bereits installierter Agents.

### <a name="deploy-the-package"></a>Bereitstellen des Pakets
1. Klicken Sie in der Configuration Manager-Konsole mit der rechten Maustaste auf das Paket, und wählen Sie **Inhalt verteilen** aus.
   ![Screenshot der Configuration Manager-Konsole](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Wählen Sie die **[Verteilungspunkte](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** aus, auf die die Pakete kopiert werden sollen.
3. Schließen Sie den Assistenten ab. Dann beginnt die Replikation des Pakets auf die angegebenen Verteilungspunkte.
4. Klicken Sie nach Abschluss der Paketverteilung mit der rechten Maustaste auf das Paket, und wählen Sie **Bereitstellen** aus.
   ![Screenshot der Configuration Manager-Konsole](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Wählen Sie die Gerätesammlung für Linux-Server, die Sie im Abschnitt „Voraussetzungen“ erstellt haben, als die Zielsammlung für die Bereitstellung aus.

   ![Screenshot des Assistenten zum Bereitstellen von Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Wählen Sie auf der Seite **Das Inhaltsziel angeben** Ihre **Verteilungspunkte** aus.
7. Stellen Sie auf der Seite **Geben Sie Einstellungen an, um die Bereitstellung dieser Software zu steuern** sicher, dass der gewünschte Zweck **Erforderlich** ist.

   ![Screenshot des Assistenten zum Bereitstellen von Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Geben Sie auf der Seite **Zeitplan für diese Bereitstellung angeben** einen Zeitplan an. Weitere Informationen finden Sie unter [Bereitstellen von Paketen und Programmen in Configuration Manager](https://technet.microsoft.com/library/gg682178.aspx).
9. Konfigurieren Sie die Eigenschaften auf der Seite **Verteilungspunkte** nach den Anforderungen Ihres Rechenzentrums. Schließen Sie dann den Assistenten ab.

Mobility Service wird auf den Linux-Servern der Gerätesammlung entsprechend dem konfigurierten Zeitplan installiert.


## <a name="uninstall-the-mobility-service"></a>Deinstallieren von Mobility Service
Sie können Configuration Manager-Pakete erstellen, um Mobility Service zu deinstallieren. Führen Sie dazu folgendes Skript aus:

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>Nächste Schritte
Sie können jetzt für Ihre virtuellen Computer [Schutz aktivieren](vmware-azure-enable-replication.md).
