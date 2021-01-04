---
title: Einrichten eines SMT-Servers für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Einrichten eines SMT-Servers für SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 88b1cd2a5bf33d6401f0d2e15237400e27c72e54
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967651"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Einrichten eines SMT-Servers für SUSE Linux
SAP HANA-Einheiten (große Instanzen) haben keine direkte Verbindung mit dem Internet. Daher ist das Registrieren einer solchen Einheit beim Betriebssystemanbieter und das Herunterladen und Anwenden von Updates kein ganz einfacher Vorgang. Eine mögliche Lösung für SUSE Linux ist die Einrichtung eines SMT-Servers auf einem virtuellen Azure-Computer. Hosten Sie den virtuellen Computer in einem virtuellen Azure-Netzwerk, das mit der HANA-Einheit (große Instanzen) verbunden ist. Mit einem SMT-Server dieser Art kann die HANA-Einheit (große Instanzen) Updates registrieren und herunterladen. 

Weitere Informationen zu SUSE finden Sie in der Dokumentation zum [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Für die Installation eines SMT-Servers, der die Aufgabe für große HANA-Instanzen erfüllt, gelten folgende Voraussetzungen:

- Ein virtuelles Azure-Netzwerk, das per ExpressRoute-Leitung mit der großen HANA-Instanz verbunden ist.
- Ein SUSE-Konto, das einer Organisation zugeordnet ist. Die Organisation muss über ein gültiges SUSE-Abonnement verfügen.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Installieren des SMT-Servers auf einem virtuellen Azure-Computer

Melden Sie sich zunächst beim [SUSE Customer Center](https://scc.suse.com/) an.

Wechseln Sie zu to **Organization** > **Organization Credentials** (Organisation -> >Anmeldeinformationen für die Organisation). In diesem Abschnitt finden Sie die Anmeldeinformationen, die zum Einrichten des SMT-Servers erforderlich sind.

Danach installieren Sie eine SUSE Linux-VM im virtuellen Azure-Netzwerk. Verwenden Sie zum Bereitstellen des virtuellen Computers ein SLES 12 SP2-Katalogimage von Azure (wählen Sie das BYOS SUSE-Image aus). Definieren Sie während des Bereitstellungsprozesses keinen DNS-Namen, und verwenden Sie keine statischen IP-Adressen.

![Screenshot der VM-Bereitstellung für den SMT-Server](./media/hana-installation/image3_vm_deployment.png)

Der bereitgestellte virtuelle Computer ist kleiner und hat im virtuellen Azure-Netzwerk die interne IP-Adresse 10.34.1.4 erhalten. Der Name des virtuellen Computers lautet *smtserver*. Nach der Installation wird die Konnektivität mit der HANA-Einheit bzw. den HANA-Einheiten (große Instanzen) überprüft. Je nachdem, wie Sie die Namensauflösung organisiert haben, müssen Sie unter Umständen die Auflösung der HANA-Einheiten (große Instanzen) auf dem virtuellen Azure-Computer unter „etc/hosts“ konfigurieren. 

Fügen Sie einen Datenträger an den virtuellen Computer an. Sie benötigen diesen Datenträger zum Speichern der Updates, und der Startdatenträger ist dafür möglicherweise zu klein. Hier wurde der Datenträger unter „/srv/www/htdocs“ bereitgestellt, wie im folgenden Screenshot gezeigt. Ein Datenträger mit 100 GB sollte hierfür ausreichen.

![Screenshot: Hinzugefügter Datenträger im PuTTY-Fenster](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Melden Sie sich bei der HANA-Einheit bzw. den HANA-Einheiten (große Instanzen) an, verwalten Sie „/etc/hosts“, und überprüfen Sie, ob Sie den virtuellen Azure-Computer erreichen können, auf dem der SMT-Server über das Netzwerk ausgeführt werden soll.

Melden Sie sich nach dieser Überprüfung bei dem virtuellen Azure-Computer an, auf dem der SMT-Server ausgeführt werden soll. Wenn Sie putty für die Anmeldung beim virtuellen Computer verwenden, führen Sie diese Befehlssequenz in Ihrem Bash-Fenster aus:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Starten Sie Bash neu, um die Einstellungen zu aktivieren. Starten Sie anschließend YAST.

Verbinden Sie Ihren virtuellen Computer („smtserver“) mit der SUSE-Website.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Wenn die Verbindung des virtuellen Computers mit der SUSE-Website hergestellt ist, installieren Sie die SMT-Pakete. Verwenden Sie den folgenden putty-Befehl, um die SMT-Pakete zu installieren.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Sie können auch das YAST-Tool verwenden, um die SMT-Pakete zu installieren. Navigieren Sie in YAST zu **Software Maintenance**, und suchen Sie nach „smt“. Wählen Sie **smt** aus. Es erfolgt ein automatischer Wechsel zu „yast2-smt“.

![Screenshot von SMT in YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Übernehmen Sie die Auswahl für die Installation von smtserver. Wechseln Sie nach Abschluss der Installation zur Konfiguration des SMT-Servers. Geben Sie die Anmeldeinformationen für die Organisation aus dem SUSE Customer Center ein, die Sie weiter oben abgerufen haben. Geben Sie außerdem Ihren Azure-VM-Hostnamen als SMT-Server-URL ein. In dieser Demonstration lautet sie „https:\//smtserver“.

![Screenshot der Konfiguration des SMT-Servers](./media/hana-installation/image6_configuration_of_smtserver1.png)

Testen Sie jetzt, ob die Verbindung mit dem SUSE Customer Center funktioniert. Wie im folgenden Screenshot zu sehen ist, wurde die Verbindung für die Demonstration erfolgreich hergestellt.

![Screenshot des Tests der Verbindung mit dem SUSE Customer Center](./media/hana-installation/image7_test_connect.png)

Nach dem Start des SMT-Setups müssen Sie ein Datenbankkennwort angeben. Da es sich um eine neue Installation handelt, sollten Sie das Kennwort wie im folgenden Screenshot gezeigt definieren.

![Screenshot: Definieren eines Kennworts für die Datenbank](./media/hana-installation/image8_define_db_passwd.PNG)

Im nächsten Schritt erstellen Sie ein Zertifikat.

![Screenshot: Erstellen eines Zertifikats für den SMT-Server](./media/hana-installation/image9_certificate_creation.PNG)

Am Schluss der Konfiguration kann es einige Minuten dauern, bis die Synchronisierungsprüfung ausgeführt wurde. Nach der Installation und Konfiguration des SMT-Servers finden Sie das Verzeichnisrepository im Bereitstellungspunkt „/srv/www/htdocs/“. Im Repository befinden sich auch einige Unterverzeichnisse. 

Starten Sie den SMT-Server und die dazugehörigen Dienste mit diesen Befehlen neu.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Herunterladen von Paketen auf den SMT-Server

Wählen Sie nach dem Neustart aller Dienste mithilfe von YAST die entsprechenden Pakete unter „SMT Management“ aus. Die Paketauswahl richtet sich nach dem Betriebssystemimage des HANA-Servers (große Instanzen). Die Paketauswahl richtet sich nicht nach dem SLES-Release oder der Version des virtuellen Computers, auf dem der SMT-Server ausgeführt wird. Der folgende Screenshot zeigt ein Beispiel des Auswahlbildschirms.

![Screenshot: Auswählen der Pakete](./media/hana-installation/image10_select_packages.PNG)

Als Nächstes starten Sie den ersten Kopiervorgang der ausgewählten Pakete auf dem eingerichteten SMT-Server. Dieser Kopiervorgang wird in der Shell mit dem Befehl „smt-mirror“ ausgelöst.

![Screenshot: Herunterladen der Pakete auf den SMT-Server](./media/hana-installation/image11_download_packages.PNG)

Diese Pakete sollten in die Verzeichnisse kopiert werden, die unter dem Bereitstellungspunkt „/srv/www/htdocs“ erstellt wurden. Dieser Prozess kann eine Stunde oder länger dauern, je nachdem, wie viele Pakete Sie ausgewählt haben. Nachdem dieser Vorgang abgeschlossen ist, geht es mit dem Einrichten des SMT-Clients weiter. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Einrichten des SMT-Clients auf Einheiten von HANA (große Instanzen)

Die Clients sind in diesem Fall die HANA-Einheiten (große Instanzen). Beim Einrichten des SMT-Servers wurde das Skript „clientSetup4SMT.sh“ auf den virtuellen Azure-Computer kopiert. Kopieren Sie dieses Skript auf die Einheit von HANA (große Instanzen), für die Sie eine Verbindung mit Ihrem SMT-Server herstellen möchten. Starten Sie das Skript mit der Option „-h“, und geben Sie den Namen Ihres SMT-Servers als Parameter an. In diesem Beispiel lautet der Name *smtserver*.

![Screenshot: Konfigurieren des SMT-Clients](./media/hana-installation/image12_configure_client.PNG)

Es ist möglich, dass der Client das Zertifikat erfolgreich vom Server laden kann, bei der Registrierung aber ein Fehler auftritt, wie im folgenden Screenshot gezeigt.

![Screenshot: Fehler bei der Registrierung des Clients](./media/hana-installation/image13_registration_failed.PNG)

Falls bei der Registrierung ein Fehler auftritt, lesen Sie dieses [SUSE-Supportdokument](https://www.suse.com/de-de/support/kb/doc/?id=7006024) (in englischer Sprache), und führen Sie die dort beschriebenen Schritte aus.

> [!IMPORTANT] 
> Geben Sie als Servernamen den Namen des virtuellen Computers (hier *smtserver*) ohne den vollqualifizierten Domänennamen an. 

Wenn diese Schritte abgeschlossen sind, führen Sie den folgenden Befehl in der HANA-Einheit (große Instanzen) aus:

```
SUSEConnect –cleanup
```

> [!Note] 
> Warten Sie nach diesem Schritt einige Minuten. Wenn Sie „clientSetup4SMT.sh“ sofort ausführen, erhalten Sie möglicherweise einen Fehler.

Falls ein Problem auftritt, das Sie gemäß den Schritten im SUSE-Artikel beheben müssen, starten Sie „clientSetup4SMT.sh“ in der HANA-Einheit (große Instanzen) neu. Der Vorgang sollte jetzt erfolgreich abgeschlossen werden.

![Screenshot: Erfolgreiche Registrierung des Clients](./media/hana-installation/image14_finish_client_config.PNG)

Sie haben den SMT-Client für die HANA-Einheit (große Instanzen) so konfiguriert, dass die Verbindung mit dem SMT-Server hergestellt werden kann, den Sie auf dem virtuellen Azure-Computer installiert haben. Sie können nun „zypper up“ oder „zypper in“ verwenden, um Betriebssystemupdates für HANA (große Instanzen) oder weitere Pakete zu installieren. Sie können nur Updates abrufen, die Sie zuvor auf den SMT-Server heruntergeladen haben.

## <a name="next-steps"></a>Nächste Schritte
- [HANA-Installation unter HLI](hana-example-installation.md)











