---
title: Installieren der IBM ZD&T-Entwicklungs-/Testumgebung in Azure | Microsoft-Dokumentation
description: Bereitstellen von IBM Z Development and Test Environment (ZD&T) auf Azure-IaaS-VMs.
services: virtual-machines-linux
ms.service: virtual-machines-linux
ms.subservice: workloads
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: c12a812f66345a26701b2a1623487fd262ede4be
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968331"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Installieren der IBM ZD&T-Entwicklungs-/Testumgebung in Azure

Wenn Sie eine Entwicklungs-/Testumgebung für Mainframeworkloads auf IBM Z-Systemen erstellen möchten, können Sie IBM Z Development and Test Environment (ZD&T) auf Azure-IaaS-VMs bereitstellen.

Mit ZD&T profitieren Sie von Kosteneinsparungen für die x86-Plattform für weniger kritische Entwicklungs- und Testumgebungen und übertragen Updates anschließend mithilfe von Push zurück in eine Z-System-Produktionsumgebung. Weitere Informationen finden Sie in der [IBM ZD&T-Installationsanleitung](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure und Azure Stack unterstützen die folgenden Versionen:

- ZD&T Personal Edition
- ZD&T Parallel Sysplex
- ZD&T Enterprise Edition

Alle Editionen von ZD&T können nur auf x86-Linux-Systemen ausgeführt werden. Windows Server wird nicht unterstützt. Für die Enterprise Edition wird Red Hat Enterprise Linux (RHEL) oder Ubuntu/Debian unterstützt. Für Azure sind sowohl RHEL- als auch Debian-VM-Images verfügbar.

In diesem Artikel wird beschrieben, wie Sie die ZD&T Enterprise Edition in Azure einrichten, um mit dem zugehörigen Webserver Umgebungen zu erstellen und zu verwalten. Durch die Installation von ZD&T werden keine Umgebungen installiert. Sie müssen diese separat als Installationspakete erstellen. Application Developers Controlled Distributions (ADCDs) sind beispielsweise Volumeimages der Testumgebungen. Sie sind in ZIP-Images auf der Mediendistribution enthalten, die von IBM bezogen werden kann. Informationen dazu finden Sie unter [Einrichten einer ADCD-Umgebung in Azure](demo.md).

Weitere Informationen finden Sie in der [ZD&T-Übersicht](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) im IBM Knowledge Center.

In diesem Artikel wird beschrieben, wie Sie die ZD&T Enterprise Edition in Azure einrichten. Anschließend können Sie mit dem zugehörigen Webserver Z-basierte Umgebungen in Azure erstellen und verwalten.

## <a name="prerequisites"></a>Voraussetzungen

> [!NOTE]
> Die ZD&T Enterprise Edition von IBM kann ausschließlich in Entwicklungs-/Testumgebungen installiert werden. Der Einsatz in Produktionsumgebungen ist *nicht* möglich.

- Ein Azure-Abonnement. Sollten Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- Sie benötigen Zugriff auf die Medien. Diese stehen nur IBM-Kunden und -Partnern zur Verfügung. Wenden Sie sich an Ihren Ansprechpartner bei IBM, oder nutzen Sie die Kontaktinformationen auf der [ZD&T-Website](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment), um weitere Informationen zu erhalten.

- Ein [ Lizenzierungsserver](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Dieser ist für den Zugriff auf die Umgebungen erforderlich. Die Erstellungsart ist davon abhängig, wie Sie die Software von IBM lizenzieren:

     - Für einen **hardwarebasierten Lizenzierungsserver** ist ein USB-Hardwaregerät erforderlich, das die zum Zugriff auf alle Teile der Software erforderlichen Rational Tokens enthält. Dieses müssen Sie bei IBM anfordern.

     - Für einen **softwarebasierten Lizenzierungsserver** müssen Sie einen zentralen Server für die Verwaltung der Lizenzschlüssel einrichten. Diese Methode wird bevorzugt und erfordert, dass Sie die Schlüssel einrichten, die Sie von IBM auf dem Verwaltungsserver erhalten.

## <a name="create-the-base-image-and-connect"></a>Erstellen des Basisimages und Herstellen einer Verbindung

1. [Erstellen Sie im Azure-Portal eine VM](../../../linux/quick-create-portal.md) mit der gewünschten Betriebssystemkonfiguration. In diesem Artikel wird eine B4ms-VM (mit 4 vCPUs und 16 GB Arbeitsspeicher) unter Ubuntu 16.04 vorausgesetzt.

2. Öffnen Sie nach der Erstellung der VM die eingehenden Ports 22 für SSH, 21 für FTP und 9443 für den Webserver.

3. Rufen Sie auf dem Blatt **Übersicht** der VM über die Schaltfläche **Verbinden** die SSH-Anmeldeinformationen ab. Klicken Sie auf die Registerkarte **SSH**, und kopieren Sie den SSH-Anmeldebefehl in die Zwischenablage.

4. Melden Sie sich auf Ihrem lokalen PC bei einer [Bash-Shell](../../../../cloud-shell/quickstart.md) an, und fügen Sie den Befehl ein. Er weist folgendes Format auf: **ssh\<user id\>\@\<IP Address\>** . Geben Sie Ihre Anmeldeinformationen ein, wenn Sie dazu aufgefordert werden, um eine Verbindung mit dem Basisverzeichnis herzustellen.

## <a name="copy-the-installation-file-to-the-server"></a>Kopieren der Installationsdatei auf den Server

Die Installationsdatei für den Webserver heißt **ZDT\_Install\_EE\_V12.0.0.1.tgz**. Sie befindet sich in den Medien, die von IBM bereitgestellt werden. Sie müssen diese Datei auf Ihre Ubuntu-VM hochladen.

1. Geben Sie über die Befehlszeile den folgenden Befehl ein, um das neu erstellte Image zu aktualisieren:

    ```
    sudo apt-get update
    ```

2. Erstellen Sie das Installationsverzeichnis:

    ```
    mkdir ZDT
    ```

3. Kopieren Sie die Datei von Ihrem lokalen Computer auf die VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Mit diesem Befehl wird die Installationsdatei in das ZDT-Verzeichnis in Ihrem Basisverzeichnis kopiert. Dieses variiert je nachdem, ob der Client unter Windows oder Linux ausgeführt wird.

## <a name="install-the-enterprise-edition"></a>Installieren der Enterprise Edition

1. Führen Sie folgende Befehle aus, um zum ZDT-Verzeichnis zu wechseln und die Datei ZDT\_Install\_EE\_V12.0.0.1.tgz zu dekomprimieren:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Führen Sie den Installer aus:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Wählen Sie **1** aus, um Enterprise Server zu installieren.

4. Drücken Sie die **EINGABETASTE**, und lesen Sie sich die Lizenzvereinbarung sorgfältig durch. Geben Sie unter der Lizenzvereinbarung **Yes** ein, um fortzufahren.

5. Wenn Sie aufgefordert werden, das Kennwort für den neu erstellten Benutzer **ibmsys1** zu ändern, verwenden Sie den Befehl **sudo passwd ibmsys1**, und geben Sie das neue Kennwort ein.

6. Geben Sie Folgendes ein, um zu überprüfen, ob die Installation erfolgreich war:

    ```
    dpkg -l | grep zdtapp
    ```

7. Stellen Sie sicher, dass die Ausgabe die Zeichenfolge **zdtapp 12.0.0.0** enthält. Diese weist darauf hin, dass das Paket erfolgreich installiert wurde.

### <a name="starting-enterprise-edition"></a>Starten der Enterprise Edition

Beachten Sie, dass der Webserver beim Start unter der ZD&T-Benutzer-ID ausgeführt wird, die während des Installationsvorgangs erstellt wurde.

1. Verwenden Sie zum Starten des Webservers die Root-Benutzer-ID, um den folgenden Befehl auszuführen:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Kopieren Sie die URL-Ausgabe des Skripts. Diese sieht wie folgt aus:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Fügen Sie die URL in einen Webbrowser ein, um die Verwaltungskomponente für Ihre ZD&T-Installation zu öffnen.

## <a name="next-steps"></a>Nächste Schritte

[Einrichten einer Application Developers Controlled Distribution (ADCD) in IBM ZD&T v1](./demo.md)
