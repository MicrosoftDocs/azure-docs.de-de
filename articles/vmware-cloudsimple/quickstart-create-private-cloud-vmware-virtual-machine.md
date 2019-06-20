---
title: Schnellstart – Erstellen einer VMware-VM in einer privaten Cloud
description: Beschreibt, wie eine VMware-VM in der privaten CloudSimple-Cloud erstellt wird
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 217154578ad11755cd658ff3b106cfbe266277ab
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154629"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Erstellen von virtuellen VMware-Computern in Ihrer privaten Cloud

Um virtuelle Computer in Ihrer privaten Cloud zu erstellen, greifen Sie zuerst über das Azure-Portal auf das CloudSimple-Portal zu.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="access-the-cloudsimple-portal"></a>Zugriff auf das CloudSimple-Portal

1. Wählen Sie **Alle Dienste** aus.
2. Suchen Sie nach **CloudSimple Services**.
3. Wählen Sie den CloudSimple-Dienst aus, über den Sie Ihre private Cloud erstellen möchten.
4. Klicken Sie auf der Seite **Übersicht** auf **Go to the CloudSimple portal**, um eine neue Browserregisterkarte für das CloudSimple-Portal zu öffnen.  Melden Sie sich nach Aufforderung mit Ihren Azure-Anmeldeinformationen an.  

    ![Starten des CloudSimple-Portals](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Starten der vCenter-Webbenutzeroberfläche

Jetzt können Sie vCenter starten, um virtuelle Computer und Richtlinien einzurichten.

Um auf vCenter zuzugreifen, starten Sie im CloudSimple-Portal. Klicken Sie auf der Startseite unter **Common Tasks** auf **Launch vSphere Client**.  Wählen Sie die private Cloud aus, und klicken Sie dann auf **Launch vSphere Client** für die private Cloud.

   ![VSphere-Client starten](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Hochladen einer ISO- oder vSphere-Vorlage

> [!WARNING]
> Verwenden Sie für ISO-Uploads den vSphere HTML5-Client.  Die Verwendung des Flash-Clients kann zu einem Fehler führen.

1. Rufen Sie die ISO- oder vSphere-Vorlage ab, die Sie auf vCenter hochladen möchten, um einen virtuellen Computer zu erstellen, damit er in Ihrem lokalen System zur Verfügung steht.

2. Klicken Sie in vCenter, auf das Symbol **Datenträger**, und wählen Sie **vsanDatastore** aus. Klicken Sie auf **Dateien** und dann auf **Neuer Ordner**.

    ![vCenter-ISO](media/vcenter-create-folder.png)

3. Erstellen Sie einen Ordner zum Speichern von ISO-Dateien.

4. Navigieren Sie zum neu erstellten Ordner, und klicken Sie auf **Dateien hochladen**. Führen Sie die Anweisungen auf dem Bildschirm aus, um die ISO-Datei hochladen.

## <a name="create-a-virtual-machine-in-vcenter"></a>Erstellen eines virtuellen Computers in vCenter

1. Klicken Sie in vCenter auf das Symbol **Hosts und Cluster**.

2. Klicken Sie mit der rechten Maustaste auf **Workload**, und wählen Sie **Neuer virtueller Computer** aus.
    
    ![Erstellen eines virtuellen Computers](media/create-vcenter-virtual-machine-01.png)

3. Wählen Sie **Neuen virtuellen Computer erstellen** aus, und klicken Sie auf **Weiter**.

    ![Assistent für neuen virtuellen Computer](media/create-vcenter-virtual-machine-02.png)

4. Benennen Sie den Computer, wählen Sie den Ordner **Workload-VMs** aus, und klicken Sie auf **Weiter**.

    ![Auswählen von Name und Ordner](media/create-vcenter-virtual-machine-03.png)

5. Wählen Sie die Computeressource **Workload** aus, und klicken Sie auf **Weiter**.

    ![Auswählen der Computeressource](media/create-vcenter-virtual-machine-04.png)

6. Wählen Sie **vsanDatastore** aus, und klicken Sie auf **Weiter**.

    ![Auswählen von „Speicher“](media/create-vcenter-virtual-machine-05.png)

7. Behalten Sie die Standardauswahl für ESXi 6.5-Kompatibilität bei, und klicken Sie auf **Weiter**.

    ![Auswählen der Kompatibilität](media/create-vcenter-virtual-machine-06.png)

8. Wählen Sie das Gastbetriebssystem der ISO-Datei für den virtuellen Computer aus, und klicken Sie auf **Weiter**.

    ![Anpassen des Gastbetriebssystems](media/create-vcenter-virtual-machine-07.png)

9. Wählen Sie Optionen für Festplatte und Netzwerk aus. Wählen Sie für „Neues CD-/DVD-Laufwerk“ **Datastore-ISO-Datei** aus.  Wenn Sie Datenverkehr aus der öffentlichen IP-Adresse an diesen virtuellen Computer zulassen möchten, wählen Sie das Netzwerk als **vm-1-** aus.

    ![Auswählen der Hardwareanpassung](media/create-vcenter-virtual-machine-08.png)

10. Ein Auswahlfenster wird geöffnet. Wählen Sie die Datei aus, die Sie zuvor in den Ordner „ISO-Dateien und Vorlagen“ hochgeladen haben, und klicken Sie auf **OK**.

    ![Auswählen der ISO-Datei](media/create-vcenter-virtual-machine-10.png)

11. Überprüfen Sie die Einstellungen, und klicken Sie auf **OK**, um den virtuellen Computer zu erstellen.

    ![Überprüfen der Optionen](media/create-vcenter-virtual-machine-11.png)

Der virtuelle Computer wird jetzt den Workload-Computeressourcen hinzugefügt und ist einsatzbereit. 

![Neuer virtueller Computer in vCenter](media/create-vcenter-virtual-machine-12.png)

Die grundlegende Einrichtung ist jetzt abgeschlossen. Sie können mit der Verwendung Ihrer privaten Cloud ähnlich wie mit der Verwendung Ihrer lokalen Infrastruktur für virtuelle Computer beginnen.

Die nächsten Abschnitte enthalten optionale Informationen zum Einrichten von DNS- und DHCP-Servern für private Cloudworkloads und zum Ändern der Standardnetzwerkkonfiguration.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Hinzufügen von Benutzern und Identitätsquellen zu vCenter (optional)

CloudSimple weist ein vCenter-Standardbenutzerkonto mit dem Benutzernamen **cloudowner@cloudsimple.local** zu. Für Ihren Einstieg ist keine zusätzliche Kontoeinrichtung erforderlich.  Normalerweise weist CloudSimple Administratoren die Berechtigungen zu, die sie zum Ausführen normaler Vorgänge benötigen.  Richten Sie Ihr lokales Active Directory oder Azure AD als [zusätzliche Identitätsquelle](https://docs.azure.cloudsimple.com/set-vcenter-identity/) in Ihrer privaten Cloud ein.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Erstellen eines DNS- und DHCP-Servers (optional)

Anwendungen und Workloads, die in einer privaten Cloudumgebung ausgeführt werden, erfordern Namensauflösung und DHCP-Dienste für Nachschlagen und IP-Adresszuweisung. Zur Bereitstellung dieser Dienste ist eine ordnungsgemäße DHCP- und DNS-Infrastruktur erforderlich. Sie können einen virtuellen Computer in vCenter konfigurieren, um diese Dienste in Ihrer privaten Cloudumgebung bereitzustellen.

### <a name="prerequisites"></a>Voraussetzungen

* Eine verteilte Portgruppe mit konfiguriertem VLAN

* Routeneinrichtung zu lokalen oder internetbasierten DNS-Servern

* Vorlage für virtuelle Maschinen oder ISO zum Erstellen eines virtuellen Computers

Über die folgenden Links erhalten Sie Anleitungen zum Einrichten von DHCP- und DNS-Servern unter Linux und Windows.

### <a name="linux-based-dns-server-setup"></a>Linux-basiertes DNS-Server-Setup

Linux bietet verschiedene Pakete für das Einrichten von DNS-Servern.  Hier ist ein Link zu Anleitungen, wie Sie einen Open-Source-BIND DNS-Server einrichten.

[Beispiel-Setup](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Windows-basiertes Setup

In diesen Microsoft-Artikeln wird beschrieben, wie Sie einen Windows-Server als DNS-Server und als DHCP-Server einrichten.
<br>
[Windows-Server als DNS-Server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows-Server als DHCP-Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Anpassen der Netzwerkkonfiguration (optional)

Über die Netzwerkseiten im CloudSimple-Portal können Sie die Konfiguration für Firewalltabellen und öffentliche IP-Adressen für virtuelle Computer angeben.

### <a name="allocate-public-ips"></a>Zuordnen von öffentlichen IP-Adressen

1. Navigieren Sie im CloudSimple-Portal zu **Netzwerk > Öffentliche IP-Adresse**.
2. Klicken Sie auf **Öffentliche IP-Adresse zuordnen**.
3. Geben Sie einen Namen ein, um den IP-Adresseintrag zu identifizieren.
4. Wählen Sie den Standort Ihrer privaten Cloud aus.
5. Verwenden Sie den Schieberegler, um die Leerlaufzeitüberschreitung bei Bedarf zu ändern.
6. Geben Sie die lokale IP-Adresse ein, für die Sie eine öffentliche IP-Adresse zuweisen möchten.
7. Geben Sie bei Bedarf einen zugeordneten DNS-Namen ein.
8. Klicken Sie auf **Fertig**.

    ![Öffentliche IP-Adresse](media/quick-create-pc-public-ip.png)

Jetzt beginnt die Aufgabe des Zuordnens der öffentlichen IP-Adresse. Sie können den Status der Aufgabe auf der Seite **Aktivität > Aufgaben** überprüfen. Nach Abschluss der Zuordnung wird der neue Eintrag auf der Seite „Öffentliche IP-Adressen“ angezeigt.

Der virtuelle Computer, dem diese IP-Adresse zugeordnet werden muss, muss mit der oben angegebenen lokalen Adresse konfiguriert werden. Die Vorgehensweise zum Konfigurieren einer IP-Adresse ist für das Betriebssystem des virtuellen Computers spezifisch. Lesen Sie die Dokumentation zum Betriebssystem Ihres virtuellen Computers, um sich über die richtige Vorgehensweise zu informieren.

#### <a name="example"></a>Beispiel

Hier sind beispielsweise die Details für Ubuntu 16.04.

Fügen Sie die statische Methode der Konfiguration für die inet-Adressfamilie in der Datei ```/etc/network/interfaces``` hinzu. Ändern Sie die Werte für Adresse, Netzmaske und Gateway. In diesem Beispiel verwenden wir die Schnittstelle "eth0", die interne IP-Adresse „192.168.24.10“, die Gateway-Adresse „192.168.24.1“ und die Netzmaske „255.255.255.0“. 

Bearbeiten Sie die Datei ```interfaces``` .

```
sudo vi /etc/network/interfaces
```

Aktualisieren Sie den folgenden Abschnitt in der Datei ```interfaces```.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

Deaktivieren Sie die Schnittstelle.

```
sudo ifdown eth0
```

Aktivieren Sie die-Schnittstelle erneut.

```
sudo ifup eth0
```

Der gesamte eingehende Datenverkehr aus dem Internet wird standardmäßig **verweigert**. Wenn Sie einen anderen Port öffnen möchten, erstellen Sie eine [Firewalltabelle](https://docs.azure.cloudsimple.com/firewall/).

Nachdem Sie eine interne IP-Adresse als statische IP-Adresse konfiguriert haben, überprüfen Sie, ob Sie das Internet über den virtuellen Computer erreichen können.

```
ping 8.8.8.8
```

Überprüfen Sie, ob Sie den virtuellen Computer aus dem Internet über die öffentliche IP-Adresse erreichen können.

Stellen Sie sicher, dass alle Firewallregeln (iptable) auf dem virtuellen Computer den eingehenden Port 80 blockieren.

```
netstat -an | grep 80
```

Starten Sie einen http-Server, der an Port 80 lauscht.
       
```
python2.7 -m SimpleHTTPServer 80
```

oder

```
python3 -m http.server 80
```

Starten Sie einen Browser auf Ihrem Desktop, und verweisen Sie ihn an Port 80 für die öffentliche IP-Adresse, um die Dateien auf Ihrem virtuellen Computer zu durchsuchen. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>CloudSimple-Standardfirewallregeln für öffentliche IP-Adresse

* VPN-Datenverkehr: Der gesamte Datenverkehr zwischen (von/in) dem VPN und allen Workloadnetzwerken und dem Verwaltungsnetzwerk wird zugelassen.
* Interner privater Clouddatenverkehr: Der gesamte Ost-West-Datenverkehr zwischen (von/in) Workloadnetzwerken und dem Verwaltungsnetzwerk (siehe oben) wird zugelassen.
* Internet-Datenverkehr:
    * Der gesamte eingehende Datenverkehr aus dem Internet in Workloadnetzwerke und das Verwaltungsnetzwerk wird verweigert.
    * Der gesamte ausgehende Datenverkehr in das Internet aus Workloadnetzwerken oder dem Verwaltungsnetzwerk wird zugelassen.

Mithilfe des Features „Firewallregeln“ können Sie auch die Art und Weise ändern, wie Ihr Datenverkehr geschützt wird. Weitere Informationen finden Sie unter [Einrichten von Firewalltabellen und -regeln](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Installieren von Lösungen (optional)
Sie können Lösungen in Ihrer privaten CloudSimple-Cloud installieren, um ihre vCenter-Umgebung für die private Cloud voll nutzen zu können. Sie können Sicherung, Notfallwiederherstellung, Replizierung und andere Funktionen einrichten, um Ihre virtuellen Computer zu schützen. Beispiele hierfür sind VMware Site Recovery Manager (VMware SRM) und Veeam Backup & Replication.

Um eine Lösung zu installieren, müssen Sie zusätzliche Berechtigungen für einen begrenzten Zeitraum anfordern. Lesen Sie [Eskalieren von Berechtigungen](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Nächste Schritte

* [Nutzen von virtuellen VMware-Computern in Azure](quickstart-create-vmware-virtual-machine.md)
* [Connect from on-premises to CloudSimple using ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Einrichten von VPN-Gateways im CloudSimple-Netzwerk](https://docs.azure.cloudsimple.com/vpn-gateway)
