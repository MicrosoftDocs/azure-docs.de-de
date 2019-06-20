---
title: Erstellen eines virtuellen Linux-Computers mit SQL Server 2017 in Azure | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie über das Azure-Portal einen virtuellen Linux-Computer mit SQL Server 2017 erstellen.
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 12/5/2018
ms.topic: conceptual
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 468db9d62a98e079fbe6954843e23a518eaabd0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60478567"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Bereitstellen eines virtuellen SQL Server-Computers über das Azure-Portal

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

In diesem Schnellstarttutorial erstellen Sie über das Azure-Portal eine Linux-VM mit SQL Server 2017.

In diesem Tutorial lernen Sie Folgendes:

* [Erstellen eines virtuellen Linux-SQL-Computers aus dem Katalog](#create)
* [Herstellen einer Verbindung mit dem neuen virtuellen Computer per SSH](#connect)
* [Ändern des Systemadministratorkennworts](#password)
* [Konfigurieren für Remoteverbindungen](#remote)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a id="create"></a> Erstellen eines virtuellen Linux-Computers mit SQL Server

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie im linken Bereich **Ressource erstellen**.

1. Wählen Sie im Bereich **Ressource erstellen** die Option **Compute** aus.

1. Klicken Sie neben der Überschrift **Ausgewählte** auf **Alle anzeigen**.

   ![Alle VM-Images anzeigen](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Geben Sie **SQL Server 2017** in das Suchfeld ein, und starten Sie die Suche durch Drücken der **EINGABETASTE**.

1. Schränken Sie die Suchergebnisse ein, indem Sie **Betriebssystem** > **RedHat** auswählen. Wählen Sie dann unter **Herausgeber** die Option **Microsoft** aus.

    ![Suchfilter für VM-Images mit SQL Server 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Wählen Sie in den Suchergebnissen ein Linux-Image mit SQL Server 2017 aus. In diesem Tutorial wird **Free SQL Server License: SQL Server 2017 Developer on Red Hat Enterprise Linux 7.4** verwendet.

   > [!TIP]
   > Mit der Developer Edition können Sie für Entwicklungs- und Testaufgaben die Features der Enterprise Edition nutzen, ohne das SQL Server-Lizenzkosten anfallen. Sie zahlen nur für die Ausführung des virtuellen Linux-Computers.

1. Wählen Sie unter **Bereitstellungsmodell auswählen** ein für Ihre Workloadanforderungen geeignetes Bereitstellungsmodell aus.

    > [!Note]
    > Verwenden Sie für neue Workloads **Resource Manager**. Wenn Sie eine Verbindung mit einem vorhandenen virtuellen Netzwerk herstellen möchten, wählen Sie die Bereitstellungsmethode des virtuellen Netzwerks für Ihre Workload aus. Weitere Informationen zu Bereitstellungsmodellen finden Sie unter [Azure Resource Manager-Bereitstellungsmodell und klassische Bereitstellungsmodelle](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

1. Klicken Sie auf **Erstellen**.

### <a name="set-up-your-linux-vm"></a>Einrichten Ihrer Linux-VM

1. Wählen Sie auf der Registerkarte **Grundlagen** Ihr **Abonnement** und Ihre **Ressourcengruppe** aus. 

    ![Fenster „Grundlagen“](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. Geben Sie im Feld **Name des virtuellen Computers** einen Namen für die neue Linux-VM ein.
1. Geben Sie dann die folgenden Werte ein, oder wählen Sie sie aus:
   * **Region**: Wählen Sie die für Sie geeignete Azure-Region aus.
   * **Verfügbarkeitsoptionen**: Wählen Sie die Verfügbarkeits- und Redundanzoption aus, die für Ihre Apps und Daten am besten geeignet ist.
   * **Größe ändern**: Wählen Sie diese Option aus, um eine VM-Größe auszuwählen, und klicken Sie anschließend auf **Auswählen**. Weitere Informationen zu VM-Größen finden Sie unter [Größen für virtuelle Linux-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

     ![Auswählen einer VM-Größe](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Verwenden Sie für Entwicklungsaufgaben und Funktionstests mindestens die VM-Größe **DS2**. Für Leistungstests sollten Sie mindestens **DS13** verwenden.

   * **Authentifizierungstyp:** Wählen Sie **Öffentlicher SSH-Schlüssel** aus.

     > [!Note]
     > Für die Authentifizierung können Sie einen öffentlichen SSH-Schlüssel oder ein Kennwort verwenden. SSH ist sicherer. Wie Sie einen SSH-Schlüssel generieren, erfahren Sie unter [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

   * **Benutzername**: Geben Sie den Administratornamen für die VM ein.
   * **Öffentlicher SSH-Schlüssel**: Geben Sie Ihren öffentlichen RSA-Schlüssel ein.
   * **Öffentliche Eingangsports**: Wählen Sie **Ausgewählte Ports zulassen** aus, und wählen Sie dann in der Liste **Öffentliche Eingangsports hinzufügen** den Port **SSH (22)** aus. Dieser Schritt ist in diesem Schnellstart notwendig, um eine Verbindung herzustellen und die SQL Server-Konfiguration durchzuführen. Wenn Sie eine Remoteverbindung mit SQL Server herstellen möchten, aktivieren Sie auch **MS SQL (1433)** , um Port 1433 für Verbindungen über das Internet zu öffnen.

   ![Eingehende Ports](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Nehmen Sie auf den folgenden Registerkarten die gewünschten Änderungen an den Einstellungen vor, oder übernehmen Sie die Standardeinstellungen.
    * **Datenträger**
    * **Netzwerk**
    * **Verwaltung**
    * **Gastkonfiguration**
    * **Tags**

1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie im Bereich **Bewerten + erstellen** auf **Erstellen**.

## <a id="connect"></a> Herstellen einer Verbindung mit dem virtuellen Linux-Computer

Wenn Sie bereits über eine BASH-Shell verfügen, verwenden Sie den Befehl **ssh**, um eine Verbindung mit dem virtuellen Azure-Computer herzustellen. Ersetzen Sie im folgenden Befehl den VM-Benutzernamen und die IP-Adresse, um eine Verbindung mit Ihrem virtuellen Linux-Computer herzustellen.

```bash
ssh azureadmin@40.55.55.555
```

Die IP-Adresse Ihres virtuellen Computers finden Sie im Azure-Portal.

![IP-Adresse im Azure-Portal](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Wenn Sie mit Windows arbeiten und nicht über eine BASH-Shell verfügen, installieren Sie einen SSH-Client (beispielsweise PuTTY).

1. [Laden Sie PuTTY herunter, und installieren Sie es.](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. Führen Sie PuTTY aus.

1. Geben Sie im Konfigurationsbildschirm von PuTTY die öffentliche IP-Adresse Ihres virtuellen Computers ein.

1. Wählen Sie **Öffnen** aus, und geben Sie Ihren Benutzernamen und Ihr Kennwort ein.

Weitere Informationen zum Herstellen einer Verbindung mit virtuellen Linux-Computern finden Sie unter [Erstellen einer Linux-VM mit dem Azure-Portal](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal).

> [!Note]
> Falls eine PuTTY-Sicherheitswarnung angezeigt wird, dass der Hostschlüssel des Servers nicht in der Registrierung zwischengespeichert wird, wählen Sie eine der folgenden Optionen aus. Wenn Sie diesem Host vertrauen, wählen Sie **Ja** aus, um den Schlüssel dem Cache von PuTTy hinzuzufügen und die Verbindungsherstellung fortzusetzen. Wenn Sie die Verbindung nur einmal herstellen möchten, ohne den Schlüssel dem Cache hinzuzufügen, wählen Sie **Nein** aus. Wenn Sie diesem Host nicht vertrauen, wählen Sie **Abbrechen** aus, um die Verbindungsherstellung abzubrechen.

## <a id="password"></a> Ändern des Systemadministratorkennworts

Der neue virtuelle Computer installiert SQL Server mit einem zufälligen Systemadministratorkennwort. Setzen Sie dieses Kennwort zurück, bevor Sie mit der Systemadministratoranmeldung eine Verbindung mit SQL Server herstellen.

1. Öffnen Sie nach dem Herstellen einer Verbindung mit Ihrem virtuellen Linux-Computer ein neues Befehlsterminal.

1. Ändern Sie das Systemadministratorkennwort mithilfe der folgenden Befehle:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Geben Sie ein neues Systemadministratorkennwort und eine Kennwortbestätigung ein, wenn Sie dazu aufgefordert werden.

1. Starten Sie den SQL Server-Dienst neu.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Hinzufügen der Tools zu Ihrem Pfad (optional)

Einige [SQL Server-Pakete](sql-server-linux-virtual-machines-overview.md#packages) werden standardmäßig installiert. Hierzu zählt auch das Paket mit den SQL Server-Befehlszeilentools. Das Toolpaket enthält die Tools **sqlcmd** und **bcp**. Der Einfachheit halber können Sie optional den Toolpfad (`/opt/mssql-tools/bin/`) Ihrer **PATH**-Umgebungsvariablen hinzufügen.

1. Führen Sie die folgenden Befehle aus, um **PATH** sowohl für Anmeldesitzungen als auch für interaktive Sitzungen/Sitzungen ohne Anmeldung zu ändern:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a> Konfigurieren für Remoteverbindungen

Wenn Sie mit SQL Server auf dem virtuellen Azure-Computer eine Remoteverbindung herstellen möchten, müssen Sie in der Netzwerksicherheitsgruppe eine Eingangsregel konfigurieren. Die Regel lässt Datenverkehr an dem Port zu, an dem SQL Server lauscht (standardmäßig 1433). In den folgenden Schritten wird die Vorgehensweise mit dem Azure-Portal gezeigt:

> [!TIP]
> Wenn Sie bei der Bereitstellung den eingehenden Port **MS SQL (1433)** in den Einstellungen ausgewählt haben, sind diese Änderungen für Sie relevant. Sie können mit dem nächsten Abschnitt zum Konfigurieren der Firewall fortfahren.

1. Wählen Sie im Portal **Virtuelle Computer**, und wählen Sie dann die SQL Server-VM aus.
1. Wählen Sie im linken Navigationsbereich unter **Einstellungen** die Option **Netzwerk** aus.
1. Wählen Sie im Fenster „Netzwerk“ unter **Regeln für eingehende Ports** die Option **Eingehenden Port hinzufügen** aus.

   ![Regeln für eingehende Ports](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. Wählen Sie in der Liste **Dienst** die Option **MS SQL** aus.

    ![MS SQL-Sicherheitsgruppenregel](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Klicken Sie auf **OK** , um die Regel für den virtuellen Computer zu speichern.

### <a name="open-the-firewall-on-rhel"></a>Öffnen der Firewall in RHEL

In diesem Tutorial wurde die Erstellung eines virtuellen RHEL-Computers (Red Hat Enterprise Linux) erläutert. Wenn Sie mit virtuellen RHEL-Computern eine Remoteverbindung herstellen möchten, müssen Sie in der Linux-Firewall den Port 1433 öffnen.

1. [Stellen Sie eine Verbindung mit Ihrem virtuellen RHEL-Computer her.](#connect)

1. Führen Sie in der BASH-Shell die folgenden Befehle aus:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun über einen virtuellen Azure-Computer mit SQL Server 2017 verfügen, können Sie mit **sqlcmd** eine lokale Verbindung herstellen und Transact-SQL-Abfragen ausführen.

Wenn Sie die Azure-VM für SQL Server-Remoteverbindungen konfiguriert haben, sollte das Herstellen einer Remoteverbindung möglich sein. Ein Beispiel dafür, wie Sie unter Windows eine Remoteverbindung mit SQL Server unter Linux herstellen, finden Sie unter [Use SSMS on Windows to connect to SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms) (Verwenden von SSMS unter Windows, um eine Verbindung mit SQL Server unter Linux herzustellen). Informationen zum Herstellen einer Verbindung mit Visual Studio Code finden Sie unter [Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode) (Erstellen und Ausführen von Transact-SQL-Skripts für SQL Server mithilfe von Visual Studio Code).

Allgemeine Informationen zu SQL Server unter Linux finden Sie in der [Übersicht über SQL Server 2017 unter Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Ausführlichere Informationen zur Verwendung virtueller Linux-Computer mit SQL Server 2017 finden Sie in der [Übersicht über virtuelle SQL Server 2017-Computer in Azure](sql-server-linux-virtual-machines-overview.md).
