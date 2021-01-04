---
title: 'Schnellstart: Erstellen einer Azure VMware-VM in einer privaten Cloud – Azure VMware Solution by CloudSimple'
description: Hier erfahren Sie, wie eine VMware-VM in der privaten CloudSimple-Cloud erstellt wird. Greifen Sie über das Azure-Portal auf das CloudSimple-Portal zu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fd67a5a7f1bf665333e06c66f73c7f18727a3e12
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427766"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Erstellen von virtuellen VMware-Computern in Ihrer privaten Cloud

Um virtuelle Computer in Ihrer privaten Cloud zu erstellen, greifen Sie zuerst über das Azure-Portal auf das CloudSimple-Portal zu.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="access-the-cloudsimple-portal"></a>Zugriff auf das CloudSimple-Portal

1. Wählen Sie **Alle Dienste** aus.
2. Suchen Sie nach **CloudSimple Services**.
3. Wählen Sie den CloudSimple-Dienst aus, über den Sie Ihre private Cloud erstellen möchten.
4. Klicken Sie auf der Seite **Übersicht** auf **Go to the CloudSimple portal** , um eine neue Browserregisterkarte für das CloudSimple-Portal zu öffnen.  Melden Sie sich nach Aufforderung mit Ihren Azure-Anmeldeinformationen an.  

    ![Starten des CloudSimple-Portals](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Starten der vCenter-Webbenutzeroberfläche

Jetzt können Sie vCenter starten, um virtuelle Computer und Richtlinien einzurichten.

Um auf vCenter zuzugreifen, starten Sie im CloudSimple-Portal. Klicken Sie auf der Startseite unter **Common Tasks** auf **Launch vSphere Client**.  Wählen Sie die private Cloud aus, und klicken Sie dann auf **Launch vSphere Client** für die private Cloud.

   ![VSphere-Client starten](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Hochladen einer ISO- oder vSphere-Vorlage

  > [!WARNING]
  > Verwenden Sie für ISO-Uploads den vSphere HTML5-Client.  Die Verwendung eines Flash-Clients kann zu einem Fehler führen.

1. Rufen Sie die ISO- oder vSphere-Vorlage ab, die Sie auf vCenter hochladen möchten, um einen virtuellen Computer zu erstellen, damit er in Ihrem lokalen System zur Verfügung steht.
2. Klicken Sie in vCenter, auf das Symbol **Datenträger** , und wählen Sie **vsanDatastore** aus. Klicken Sie auf **Dateien** und dann auf **Neuer Ordner**.
    ![vCenter-ISO](media/vciso00.png)

3. Erstellen Sie einen Ordner mit dem Namen „ISOs and Templates“.

4. Navigieren Sie in „ISOs and Templates“ zum Ordner „ISOs“, und klicken Sie auf **Upload Files** (Dateien hochladen). Führen Sie die Anweisungen auf dem Bildschirm aus, um die ISO-Datei hochladen.

## <a name="create-a-virtual-machine-in-vcenter"></a>Erstellen eines virtuellen Computers in vCenter

1. Klicken Sie in vCenter auf das Symbol **Hosts und Cluster**.

2. Klicken Sie mit der rechten Maustaste auf **Workload** , und wählen Sie **Neuer virtueller Computer** aus.
    ![Screenshot, der die Menüoption „Neuer virtueller Computer“ hervorhebt.](media/vcvm01.png)

3. Wählen Sie **Neuen virtuellen Computer erstellen** aus, und klicken Sie auf **Weiter**.
    ![Screenshot, der die Option „Neuer virtueller Computer“ hervorhebt.](media/vcvm02.png)

4. Benennen Sie den Computer, wählen Sie den Ordner **Workload-VMs** aus, und klicken Sie auf **Weiter**.
    ![Screenshot, der die Option „Workload-VMs“ hervorhebt](media/vcvm03.png)

5. Wählen Sie die Computeressource **Workload** aus, und klicken Sie auf **Weiter**.
    ![Screenshot, der die Computeressource „Workload“ hervorhebt](media/vcvm04.png)

6. Wählen Sie **vsanDatastore** aus, und klicken Sie auf **Weiter**.
    ![Screenshot, der die Option vsanDatastore hervorhebt.](media/vcvm05.png)

7. Behalten Sie die Standardauswahl für ESXi 6.5-Kompatibilität bei, und klicken Sie auf **Weiter**.
    ![Screenshot mit der ausgewählten Option „ESXi 6.5-Kompatibilität“.](media/vcvm06.png)

8. Wählen Sie das Gastbetriebssystem der ISO-Datei für den virtuellen Computer aus, den Sie erstellen, und klicken Sie auf **Weiter**.
    ![Screenshot, der zeigt, wie das Gastbetriebssystem der ISO-Datei für die VM ausgewählt wird.](media/vcvm07.png)

9. Wählen Sie Optionen für Festplatte und Netzwerk aus. Wählen Sie für „Neues CD-/DVD-Laufwerk“ **Datastore-ISO-Datei** aus.  Wenn Sie Datenverkehr aus der öffentlichen IP-Adresse an diesen virtuellen Computer zulassen möchten, wählen Sie das Netzwerk als **vm-1** aus.
    ![Screenshot, der zeigt, wo Sie die ISO-Datei „Datastore“ auswählen.](media/vcvm08.png)

10. Ein Auswahlfenster wird geöffnet. Wählen Sie die Datei aus, die Sie zuvor in den Ordner „ISO-Dateien und Vorlagen“ hochgeladen haben, und klicken Sie auf **OK**.
    ![Neuer virtueller Computer](media/vcvm10.png)

11. Überprüfen Sie die Einstellungen, und klicken Sie auf **OK** , um den virtuellen Computer zu erstellen.
    ![Screenshot, der zeigt, wo Sie die Einstellungen überprüfen.](media/vcvm11.png)

Der virtuelle Computer wird jetzt den Workload-Computeressourcen hinzugefügt und ist einsatzbereit. 
![Screenshot, der die VM zeigt, die den Computeressourcen für die Workload hinzugefügt wurde.](media/vcvm12.png)

Die grundlegende Einrichtung ist jetzt abgeschlossen. Sie können mit der Verwendung Ihrer privaten Cloud ähnlich wie mit der Verwendung Ihrer lokalen Infrastruktur für virtuelle Computer beginnen.

Die folgenden Abschnitte enthalten optionale Informationen zum Einrichten von DNS- und DHCP-Servern für private Cloudworkloads und zum Ändern der Standardnetzwerkkonfiguration.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Hinzufügen von Benutzern und Identitätsquellen zu vCenter (optional)

CloudSimple weist ein vCenter-Standardbenutzerkonto mit dem Benutzernamen `cloudowner@cloudsimple.local` zu. Für Ihren Einstieg ist keine zusätzliche Kontoeinrichtung erforderlich.  Normalerweise weist CloudSimple Administratoren die Berechtigungen zu, die sie zum Ausführen normaler Vorgänge benötigen.  Richten Sie Ihr lokales Active Directory oder Azure AD als [zusätzliche Identitätsquelle](set-vcenter-identity.md) in Ihrer privaten Cloud ein.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Erstellen eines DNS- und DHCP-Servers (optional)

Anwendungen und Workloads, die in einer privaten Cloudumgebung ausgeführt werden, erfordern Namensauflösung und DHCP-Dienste für Nachschlagen und IP-Adresszuweisung. Zur Bereitstellung dieser Dienste ist eine ordnungsgemäße DHCP- und DNS-Infrastruktur erforderlich. Sie können einen virtuellen Computer in vCenter konfigurieren, um diese Dienste in Ihrer privaten Cloudumgebung bereitzustellen.

Voraussetzungen

* Eine verteilte Portgruppe mit konfiguriertem VLAN

* Routeneinrichtung zu lokalen oder internetbasierten DNS-Servern

* Vorlage für virtuelle Maschinen oder ISO zum Erstellen eines virtuellen Computers

Über die folgenden Links erhalten Sie Anleitungen zum Einrichten von DHCP- und DNS-Servern unter Linux und Windows.

#### <a name="linux-based-dns-server-setup"></a>Setup für Linux-basiertes DNS-Server

Linux bietet verschiedene Pakete für das Einrichten von DNS-Servern.  Hier ist ein Link zu Anleitungen, wie Sie einen Open-Source-BIND DNS-Server einrichten.

[Beispiel-Setup](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Windows-basiertes Setup

In diesen Microsoft-Themen wird beschrieben, wie Sie einen Windows-Server als DNS-Server und als DHCP-Server einrichten.

[Windows-Server als DNS-Server](/windows-server/networking/dns/dns-top)

[Windows-Server als DHCP-Server](/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Anpassen der Netzwerkkonfiguration (optional)

Über die Netzwerkseiten im CloudSimple-Portal können Sie die Konfiguration für Firewalltabellen und öffentliche IP-Adressen für virtuelle Computer angeben.

### <a name="allocate-public-ips"></a>Zuordnen von öffentlichen IP-Adressen

1. Navigieren Sie im CloudSimple-Portal zu **Netzwerk > Öffentliche IP-Adresse**.
2. Klicken Sie auf **Öffentliche IP-Adresse zuordnen**.
3. Geben Sie einen Namen ein, um den IP-Adresseintrag zu identifizieren.
4. Behalten Sie den Standardort bei.
5. Verwenden Sie den Schieberegler, um die Leerlaufzeitüberschreitung bei Bedarf zu ändern.
6. Geben Sie die lokale IP-Adresse ein, für die Sie eine öffentliche IP-Adresse zuweisen möchten.
7. Geben Sie bei Bedarf einen zugeordneten DNS-Namen ein.
8. Klicken Sie auf **Fertig**.

    ![Öffentliche IP-Adresse](media/quick-create-pc-public-ip.png)

Jetzt beginnt die Aufgabe des Zuordnens der öffentlichen IP-Adresse. Sie können den Status der Aufgabe auf der Seite **Aktivität > Aufgaben** überprüfen. Nach Abschluss der Zuordnung wird der neue Eintrag auf der Seite „Öffentliche IP-Adressen“ angezeigt.

Der virtuelle Computer, dem diese IP-Adresse zugeordnet werden muss, muss mit der oben angegebenen lokalen Adresse konfiguriert werden. Die Vorgehensweise zum Konfigurieren einer IP-Adresse ist für das Betriebssystem des virtuellen Computers spezifisch. Lesen Sie die Dokumentation zum Betriebssystem Ihres virtuellen Computers, um sich über die richtige Vorgehensweise zu informieren.

#### <a name="example"></a>Beispiel

Hier sind beispielsweise die Details für Ubuntu 16.04.

Fügen Sie die statische Methode der Konfiguration für die inet-Adressfamilie in der Datei „/etc/network/interfaces“ hinzu. Ändern Sie die Werte für Adresse, Netzmaske und Gateway. In diesem Beispiel verwenden wir die Schnittstelle "eth0", die interne IP-Adresse „192.168.24.10“, die Gateway-Adresse „192.168.24.1“ und die Netzmaske „255.255.255.0“. Für Ihre Umgebung werden die verfügbaren Subnetzinformationen in der Willkommens-E-Mail bereitgestellt.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Deaktivieren Sie die Schnittstelle manuell.

```
sudo ifdown eth0
```
Aktivieren Sie die Schnittstelle manuell erneut.

```
sudo ifup eth0
```

Der gesamte eingehende Datenverkehr aus dem Internet wird standardmäßig **verweigert**. Wenn Sie einen anderen Port öffnen möchten, erstellen Sie eine [Firewalltabelle](firewall.md).

Nachdem Sie eine interne IP-Adresse als statische IP-Adresse konfiguriert haben, überprüfen Sie, ob Sie das Internet über den virtuellen Computer erreichen können.

```
ping 8.8.8.8
```
Überprüfen Sie außerdem, ob Sie den virtuellen Computer aus dem Internet über die öffentliche IP-Adresse erreichen können.

Stellen Sie sicher, dass alle Regeln (iptable) auf dem virtuellen Computer Port 80 für eingehenden Datenverkehr nicht blockieren.
        
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

Mithilfe des Features „Firewallregeln“ können Sie auch die Art und Weise ändern, wie Ihr Datenverkehr geschützt wird. Weitere Informationen finden Sie unter [Einrichten von Firewalltabellen und -regeln](firewall.md).

## <a name="install-solutions-optional"></a>Installieren von Lösungen (optional)

Sie können Lösungen in Ihrer privaten CloudSimple-Cloud installieren, um ihre vCenter-Umgebung für die private Cloud voll nutzen zu können. Sie können Sicherung, Notfallwiederherstellung, Replizierung und andere Funktionen einrichten, um Ihre virtuellen Computer zu schützen. Beispiele hierfür sind VMware Site Recovery Manager (VMware SRM) und Veeam Backup & Replication.

Um eine Lösung zu installieren, müssen Sie zusätzliche Berechtigungen für einen begrenzten Zeitraum anfordern. Lesen Sie [Eskalieren von Berechtigungen](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Nächste Schritte

* [Nutzen von virtuellen VMware-Computern in Azure](quickstart-create-vmware-virtual-machine.md)
* [Connect from on-premises to CloudSimple using ExpressRoute](on-premises-connection.md)
* [Einrichten von VPN-Gateways für ein CloudSimple-Netzwerk](vpn-gateway.md)
