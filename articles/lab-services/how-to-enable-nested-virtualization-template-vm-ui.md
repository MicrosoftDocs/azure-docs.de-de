---
title: Aktivieren der geschachtelten Virtualisierung auf einer virtuellen Vorlagencomputer in Azure Lab Services (Benutzeroberfläche) | Microsoft-Dokumentation
description: 'Informationen zum Erstellen einer VM-Vorlage, die mehrere VMs enthält  Das heißt: Aktivieren Sie die geschachtelte Virtualisierung auf einer VM-Vorlage in Azure Lab Services.'
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: f8135e11fb7b7ddb588ab3a8ed01227712072fd2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647918"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Manuelles Aktivieren der geschachtelten Virtualisierung auf einem virtuellen Vorlagencomputer in Azure Lab Services

Eine geschachtelte Virtualisierung ermöglicht Ihnen, innerhalb einer Vorlage für virtuelle Computer in einem Lab eine Umgebung mit mehreren virtuellen Computern zu erstellen. Wenn diese Vorlage veröffentlicht wird, wird für jeden Benutzer im Lab ein eingerichteter virtueller Computer bereitgestellt, in dem weitere virtuelle Computer eingeschlossen sind.  Weitere Informationen zur geschachtelten Virtualisierung zu und Azure Lab Services finden Sie unter [Aktivieren der geschachtelten Virtualisierung auf einem virtuellen Vorlagencomputer in Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Dieser Artikel behandelt die Vorgehensweise zum Einrichten der geschachtelten Virtualisierung auf einem Vorlagencomputer in Azure Lab Services unter direkter Verwendung von Windows-Rollen und -Tools.  Es sind einige Schritte erforderlich, damit eine Klasse die geschachtelte Virtualisierung nutzen kann.  In den folgenden Schritten wird beschrieben, wie Sie eine Lab Services-Computervorlage mithilfe von Hyper-V manuell einrichten.  Die Schritte müssen unter Windows Server 2016 oder Windows Server 2019 ausgeführt werden.  

>[!IMPORTANT]
>Wählen Sie bei der Erstellung des Labs als Größe für die VM **Groß (geschachtelte Virtualisierung)** oder **Mittel (geschachtelte Virtualisierung)** aus.  Andernfalls funktioniert die verschachtelte Virtualisierung nicht.  

## <a name="enable-hyper-v-role"></a>Aktivieren der Hyper-V-Rolle

In den folgenden Schritten werden Aktionen beschrieben, die erforderlich sind, um Hyper-V unter Windows Server mit einem der beiden Server-Manager zu aktivieren.  Nach erfolgreicher Installation steht der Hyper-V-Manager zur Verfügung, um virtuelle Client-VMs hinzuzufügen, zu ändern und zu löschen.

1. Klicken Sie im **Server-Manager** auf der Seite „Dashboard“ auf **Rollen und Features hinzufügen**.
2. Klicken Sie auf der Seite **Voraussetzungen** auf **Weiter**.
3. Behalten Sie auf der Seite **Installationstyp auswählen** die Standardoption **Rollenbasierte oder featurebasierte Installation** bei, und klicken Sie anschließend auf „Weiter“.
4. Wählen Sie auf der Seite **Zielserver auswählen** die Option „Einen Server aus Serverpool auswählen“ aus.   Der aktuelle Server ist bereits ausgewählt.  Klicken Sie auf „Weiter“.
5. Wählen Sie auf der Seite **Serverrollen auswählen** die Option **Hyper-V** aus.  
6. Der **Assistent zum Hinzufügen von Rollen und Features** wird geöffnet.  Wählen Sie **Verwaltungstools einschließen (falls vorhanden)** aus.  Klicken Sie auf die Schaltfläche **Features hinzufügen**.
7. Klicken Sie auf der Seite **Serverrollen auswählen** auf **Weiter**.
8. Klicken Sie auf der Seite **Features auswählen** auf **Weiter**.
9. Klicken Sie auf der Seite **Hyper-V** auf **Weiter**.
10. Übernehmen Sie auf der Seite **Virtuelle Switches erstellen** die Standardeinstellungen, und klicken Sie auf **Weiter**.
11. Übernehmen Sie auf der Seite **Migration der virtuellen Maschine** die Standardeinstellungen, und klicken Sie auf **Weiter**.
12. Übernehmen Sie auf der Seite **Standardspeicher** die Standardeinstellungen, und klicken Sie auf **Weiter**.
13. Aktivieren Sie auf der Seite **Installationsauswahl bestätigen** das Kontrollkästchen **Zielserver bei Bedarf automatisch neu starten**.
14. Wenn der **Assistent zum Hinzufügen von Rollen und Features** angezeigt wird, klicken Sie auf **Ja**.
15. Klicken Sie auf **Installieren**.
16. Warten Sie, bis auf der Seite **Installationsstatus** angezeigt wird, dass die Hyper-V-Rolle vollständig eingerichtet ist.  Der Computer wird in der Mitte der Installation möglicherweise neu gestartet.
17. Klicken Sie auf **Schließen**.

## <a name="enable-dhcp-role"></a>Aktivieren der DHCP-Rolle

Alle erstellten virtuellen Hyper-V-Clientcomputer benötigen eine IP-Adresse im NAT-Netzwerk.  Wir erstellen das NAT-Netzwerk zu einem späteren Zeitpunkt.  Eine Möglichkeit zum Zuweisen von IP-Adressen besteht darin, den Host, in diesem Fall die Vorlage für virtuelle Maschinen des Labs, als DHCP-Server einzurichten.  Es folgen die zum Aktivieren der DHCP-Rolle erforderlichen Schritte.

1. Klicken Sie im **Server-Manager** auf der Seite **Dashboard** auf **Rollen und Features hinzufügen**.
2. Klicken Sie auf der Seite **Voraussetzungen** auf **Weiter**.
3. Wählen Sie auf der Seite **Installationstyp auswählen** die Option **Rollenbasierte oder featurebasierte Installation** aus, und klicken Sie anschließend auf **Weiter**.
4. Wählen Sie auf der Seite **Zielserver auswählen** einen Server aus dem Serverpool aus, und klicken Sie auf **Weiter**.
5. Wählen Sie auf der Seite **Serverrollen auswählen** die Option **DHCP-Server** aus.  
6. Der **Assistent zum Hinzufügen von Rollen und Features** wird geöffnet.  Wählen Sie **Verwaltungstools einschließen (falls vorhanden)** aus.  Klicken Sie auf **Features hinzufügen**.

    >[!NOTE]
    >Möglicherweise wird Ihnen der Überprüfungsfehler angezeigt, dass keine statischen IP-Adressen gefunden wurden.  Diese Warnung kann in unserem Szenario ignoriert werden.

7. Klicken Sie auf der Seite **Serverrollen auswählen** auf **Weiter**.
8. Klicken Sie auf der Seite **Features auswählen** auf **Weiter**.
9. Klicken Sie auf der Seite **DHCP-Server** auf **Weiter**.
10. Klicken Sie auf der Seite **Installationsauswahl bestätigen** auf **Installieren**.
11. Warten Sie, bis auf der Seite **Installationsstatus** angezeigt wird, dass die DHCP-Rolle vollständig eingerichtet ist.
12. Klicken Sie auf „Schließen“.

## <a name="enable-routing-and-remote-access-role"></a>Aktivieren der Rolle „Routing- und RAS“

1. Klicken Sie im **Server-Manager** auf der Seite **Dashboard** auf **Rollen und Features hinzufügen**.
2. Klicken Sie auf der Seite **Voraussetzungen** auf **Weiter**.
3. Wählen Sie auf der Seite **Installationstyp auswählen** die Option **Rollenbasierte oder featurebasierte Installation** aus, und klicken Sie anschließend auf **Weiter**.
4. Wählen Sie auf der Seite **Zielserver auswählen** einen Server aus dem Serverpool aus, und klicken Sie auf **Weiter**.
5. Wählen Sie auf der Seite **Serverrollen auswählen** die Option **Remotezugriff** aus. Klicken Sie auf **OK**.
6. Klicken Sie auf der Seite **Features auswählen** auf **Weiter**.
7. Klicken Sie auf der Seite **Remotezugriff** auf **Weiter**.
8. Wählen Sie auf der Seite **Rollendienste** die Option **Routing** aus.
9. Der **Assistent zum Hinzufügen von Rollen und Features** wird geöffnet.  Wählen Sie **Verwaltungstools einschließen (falls vorhanden)** aus.  Klicken Sie auf **Features hinzufügen**.
10. Klicken Sie auf **Weiter**.
11. Klicken Sie auf der Seite **Rolle „Webserver“ (IIS)** auf **Weiter**.
12. Klicken Sie auf der Seite **Rollendienste auswählen** auf **Weiter**.
13. Klicken Sie auf der Seite **Installationsauswahl bestätigen** auf **Installieren**.
14. Warten Sie, bis auf der Seite **Installationsstatus** angezeigt wird, dass die Rolle „Remotezugriff“ vollständig eingerichtet ist.  
15. Klicken Sie auf **Schließen**.

## <a name="create-virtual-nat-network"></a>Erstellen des virtuellen NAT-Netzwerks

Nachdem alle erforderlichen Rollen installiert wurden, ist es Zeit, das NAT-Netzwerk zu erstellen.  Der Erstellungsprozess umfasst das Erstellen eines Switches und des NAT-Netzwerks selbst.  Ein NAT-Netzwerk (Network Address Translation, Netzwerkadressübersetzung) weist einer Gruppe von VMs in einem privaten Netzwerk eine öffentliche IP-Adresse zu, um Konnektivität mit dem Internet zu ermöglichen.  In unserem Fall bildet die Gruppe der privaten VMs die geschachtelten VMs.  Das NAT-Netzwerk ermöglicht den geschachtelten VMs die Kommunikation untereinander. Ein Switch ist ein Netzwerkgerät, das den Empfang und das Routing des Datenverkehrs in einem Netzwerk übernimmt.

### <a name="create-a-new-virtual-switch"></a>Erstellen eines neuen virtuellen Switches

1. Öffnen Sie in der Windows-Verwaltung **Hyper-V-Manager**.
2. Wählen Sie im linken Navigationsmenü den aktuellen Server aus.
3. Klicken Sie auf der rechten Seite im **Hyper-V-Manager** im Menü **Aktionen** auf **Manager für virtuelle Switches…** .
4. Wählen Sie im Popupelement **Manager für virtuelle Switches** als Typ des zu erstellenden Switches **Intern** aus.  Klicken Sie auf **Virtuellen Switch erstellen**.
5. Legen Sie für den neu erstellten virtuellen Switch einen einprägsamen Namen fest.  In diesem Beispiel verwenden wir LabServicesSwitch.  Klicken Sie auf **OK**.
6. Ein neuer Netzwerkadapter wird erstellt.  Der Name ähnelt „vEthernet (LabServicesSwitch)“.  Öffnen Sie zur Überprüfung die **Systemsteuerung**. Klicken Sie auf **Netzwerk und Internet** und dann auf **Netzwerkstatus und -aufgaben anzeigen**.  Klicken Sie links auf **Adaptereinstellungen ändern**.

### <a name="create-a-nat-network"></a>Erstellen eines NAT-Netzwerks

1. Öffnen Sie in der Windows-Verwaltung das Tool **Routing und RAS**.
2. Wählen Sie im linken Navigationsbereich den lokalen Server aus.
3. Wählen Sie **Aktion** -> **Routing und RAS konfigurieren und aktivieren** aus.
4. Wenn der **RRAS-Setup-Assistent** angezeigt wird, klicken Sie auf **Weiter**.
5. Wählen Sie auf der Seite **Konfiguration** die Option **Netzwerkadressübersetzung (NAT)** aus.  Klicken Sie auf **Weiter**.

    >[!WARNING]
    >Wählen Sie nicht die Option „VPN-Zugriff und NAT“ aus.

6. Wählen Sie auf der Seite **NAT-Internetverbindung** die Option „Ethernet“ aus.  Wählen Sie nicht die Verbindung „vEthernet (LabServicesSwitch)“ aus, die wir im Hyper-V-Manager erstellt haben. Klicken Sie auf **Weiter**.
7. Klicken Sie auf der letzten Seite des Assistenten auf **Fertig stellen**.
8. Wenn das Dialogfeld **Starten des Diensts** angezeigt wird, klicken Sie auf **Dienst starten**.
9. Warten Sie das Starten des Diensts ab.

## <a name="update-network-adapter-settings"></a>Aktualisieren der Netzwerkadaptereinstellungen

Der Netzwerkadapter wird der IP-Adresse zugeordnet, die als standardmäßige Gateway-IP-Adresse für das zuvor erstellte NAT-Netzwerk verwendet wird.  In diesem Beispiel erstellen wir die IP-Adresse 192.168.0.1 mit der Subnetzmaske 255.255.255.0.  Wir verwenden den zuvor erstellten virtuellen Switch.

1. Öffnen Sie die **Systemsteuerung**. Klicken Sie auf **Netzwerk und Internet** und dann auf **Netzwerkstatus und -aufgaben anzeigen**.
2. Klicken Sie links auf **Adaptereinstellungen ändern**.  
3. Doppelklicken Sie im Fenster **Netzwerkverbindungen** auf „vEthernet (LabServicesSwitch)“, um das Dialogfeld **Status von vEthernet (LabServicesSwitch)** anzuzeigen.
4. Klicken Sie auf die Schaltfläche **Eigenschaften**.
5. Wählen Sie **Internetprotokoll, Version 4 (TCP/IPv4)** aus, und klicken Sie auf die Schaltfläche **Eigenschaften**.
6. Wählen Sie im Dialogfeld **Eigenschaften von Internetprotokoll, Version 4 (TCP/IPv4)** die Option **Folgende IP-Adresse verwenden** aus.  Geben Sie als IP-Adresse 192.168.0.1 ein. Geben Sie als Subnetzmaske 255.255.255.0 ein.  Lassen Sie „Standardgateway“ leer.  Lassen Sie „DNS-Server“ ebenfalls leer.

    >[!NOTE]
    > Der Bereich für unser NAT-Netzwerk in CIDR-Notation ist 192.168.0.0/24.  Dadurch wird ein Bereich verwendbarer IP-Adressen von 192.168.0.1 bis 192.168.0.254 erstellt.  Laut Konvention haben Gateways die erste IP-Adresse in einem Subnetzbereich.

7. Klicken Sie auf „OK“.

## <a name="create-dhcp-scope"></a>Erstellen des DHCP-Bereichs

Die folgenden Schritte sind Anweisungen zum Hinzufügen des DHCP-Bereichs.  In diesem Artikel ist unser NAT-Netzwerk in CIDR-Notation 192.168.0.0/24.  Dadurch wird ein Bereich verwendbarer IP-Adressen von 192.168.0.1 bis 192.168.0.254 erstellt.  Der erstellte Bereich muss in diesem Bereich nutzbarer Adressen liegen, wobei die bereits zuvor erstellte IP-Adresse ausgeschlossen ist.

1. Öffnen Sie **Verwaltung** und dann das Verwaltungstool **DHCP**.
2. Erweitern Sie im Tool **DHCP** den Knoten für den aktuellen Server, und wählen Sie **IPv4** aus.
3. Wählen Sie im Menü „Aktion“ die Option **Neuer Bereich...** .
4. Klicken Sie im eingeblendeten **Bereichserstellungs-Assistenten** auf der Seite **Willkommen** auf **Weiter**.
5. Geben Sie auf der Seite **Bereichsname** LabServicesDhcpScope oder etwas anderes Einprägsames als Namen ein.  Klicken Sie auf **Weiter**.
6. Geben Sie auf der Seite **IP-Adressbereich** die folgenden Werte ein.

    - Start-IP-Adresse: 192.168.0.100
    - End-IP-Adresse: 192.168.0.200
    - Länge: 24
    - Subnetzmaske: 255.255.255.0

7. Klicken Sie auf **Weiter**.
8. Klicken Sie auf der Seite **Ausschlüsse und Verzögerung hinzufügen** auf **Weiter**.
9. Klicken Sie auf der Seite **Leasedauer** auf **Weiter**.
10. Wählen Sie auf der Seite **DHCP-Optionen konfigurieren** die Option **Ja, diese Optionen jetzt konfigurieren** aus. Klicken Sie auf **Weiter**.
11. Auf der Seite **Router (Standardgateway)** :
12. Fügen Sie 192.168.0.1 hinzu, falls noch nicht geschehen. Klicken Sie auf **Weiter**.
13. Fügen Sie auf der Seite **Domänenname und DNS-Server** 168.63.129.16 als IP-Adresse des DNS-Servers hinzu, falls noch nicht geschehen.  168.63.129.16 ist die IP-Adresse für einen statischen DNS-Server in Azure. Klicken Sie auf **Weiter**.
14. Klicken Sie auf der Seite **WINS-Server** auf **Weiter**.
15. Wählen Sie auf der Seite **Bereich aktivieren** die Option **Ja, diesen Bereich jetzt aktivieren** aus.  Klicken Sie auf **Weiter**.
16. Klicken Sie auf der Seite **Fertigstellen des Assistenten** auf **Fertig stellen**.

## <a name="conclusion"></a>Zusammenfassung

Jetzt ist Ihr Vorlagencomputer bereit, um virtuelle Hyper-V-Computer zu erstellen.   Anweisungen zum Erstellen von virtuellen Hyper-V-Computern finden Sie unter [Erstellen eines virtuellen Computers in Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).  Verfügbare Betriebssysteme und Software finden Sie außerdem im [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/).

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Einrichtungsschritte sind für alle Labs gleich.

- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)