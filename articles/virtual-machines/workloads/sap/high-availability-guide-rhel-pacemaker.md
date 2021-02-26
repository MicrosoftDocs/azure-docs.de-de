---
title: Einrichten von Pacemaker unter RHEL in Azure | Microsoft-Dokumentation
description: Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2021
ms.author: radeltch
ms.openlocfilehash: 631ebcd41e50a6b8f9e049a646394e572c0b15f7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549319"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Lesen Sie zuerst die folgenden SAP-Hinweise und -Dokumente:

* SAP-Hinweis [1928533] mit folgenden Informationen:
  * Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
  * Wichtige Kapazitätsinformationen für Azure-VM-Größen
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * Erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure
* In SAP-Hinweis [2015553] sind die Voraussetzungen für Bereitstellungen von SAP-Software in Azure aufgeführt, die von SAP unterstützt werden.
* SAP-Hinweis [2002167] enthält empfohlene Betriebssystemeinstellungen für Red Hat Enterprise Linux.
* SAP-Hinweis [2009879] enthält SAP HANA-Richtlinien für Red Hat Enterprise Linux.
* SAP-Hinweis [2178632] enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden.
* SAP-Hinweis [2191498] enthält die erforderliche SAP Host Agent-Version für Linux in Azure.
* SAP-Hinweis [2243692] enthält Informationen zur SAP-Lizenzierung unter Linux in Azure.
* SAP-Hinweis [1999351] enthält Informationen zur Problembehandlung für die Azure-Erweiterung zur verbesserten Überwachung für SAP.
* Das [WIKI der SAP-Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) enthält alle erforderlichen SAP-Hinweise für Linux.
* [Azure Virtual Machines – Planung und Implementierung für SAP unter Linux][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP unter Linux (dieser Artikel)][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
* [SAP HANA-Systemreplikation in Pacemaker-Cluster](https://access.redhat.com/articles/3004101)
* Allgemeine RHEL-Dokumentation:
  * [Übersicht über das Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Verwaltung des Hochverfügbarkeits-Add-Ons](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenz zum Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Support-Richtlinien für RHEL High Availability Clusters – sbd und fence_sbd](https://access.redhat.com/articles/2800691)
* Azure-spezifische RHEL-Dokumentation:
  * [Unterstützungsrichtlinien für RHEL-Hochverfügbarkeitscluster – Virtuelle Microsoft Azure-Computer als Clustermitglieder](https://access.redhat.com/articles/3131341)
  * [Installieren und Konfigurieren eines Red Hat Enterprise Linux 7.4-Hochverfügbarkeitclusters (und höher) in Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Überlegungen zur Einführung von RHEL 8 – Hochverfügbarkeit und Cluster](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [Configure SAP S/4HANA ASCS/ERS with Standalone Enqueue Server 2 (ENSA2) in Pacemaker on RHEL 7.6](https://access.redhat.com/articles/3974941) (Konfigurieren von SAP S/4HANA ASCS/ERS mit eigenständigem Enqueue-Server 2 (ENSA2) in Pacemaker unter RHEL 7.6)
  * [RHEL for SAP-Angebote in Azure](https://access.redhat.com/articles/5456301)

## <a name="cluster-installation"></a>Clusterinstallation

![Übersicht über Pacemaker unter RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat unterstützt keinen von der Software emulierten Watchdog. Red Hat unterstützt SBD nicht auf Cloudplattformen. Weitere Informationen finden Sie unter [Support-Richtlinien für RHEL High Availability Clusters – sbd und fence_sbd](https://access.redhat.com/articles/2800691).
> Der einzige unterstützte Fencing-Mechanismus für Pacemaker Red Hat Enterprise Linux-Cluster in Azure ist Azure Fence Agent.  

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

1. **[A]** Registrieren Sie sich. Dieser Schritt ist nicht erforderlich, wenn Sie Images verwenden, die für RHEL SAP-Hochverfügbarkeit aktiviert sind.  

   Registrieren Sie Ihre virtuellen Computer, und ordnen Sie sie einem Pool zu, der Repositorys für RHEL 7 enthält.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Sie erhalten durch das Anfügen eines Pools an ein Azure Marketplace PAYG RHEL-Image effektiv eine doppelte Abrechnung für Ihre RHEL-Nutzung: einmal für das PAYG-Image und einmal für die RHEL-Berechtigung in dem Pool, den Sie anfügen. Azure bietet jetzt BYOS RHEL-Images an, um dies zu vermeiden. Weitere Informationen sind [hier](../redhat/byos.md) verfügbar.  

1. **[A]** Aktivieren Sie RHEL für SAP-Repositorys. Dieser Schritt ist nicht erforderlich, wenn Sie Images verwenden, die für RHEL SAP-Hochverfügbarkeit aktiviert sind.  

   Aktivieren Sie die folgenden Repositorys, um die erforderlichen Pakete zu installieren.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Installieren des RHEL-Hochverfügbarkeits-Add-Ons

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Wir empfehlen die folgenden Versionen des Azure Fence-Agent (oder höher), damit Kunden von einer schnelleren Failoverzeit profitieren können, wenn bei einem Ressourcenstopp ein Fehler auftritt oder die Clusterknoten nicht mehr miteinander kommunizieren können:  
   > RHEL 7.7 oder höher verwenden die neueste verfügbare Version des Fence-Agents-Pakets.  
   > RHEL 7.6: fence-agents-4.2.1-11.el7_6.8  
   > RHEL 7.5: fence-agents-4.0.11-86.el7_5.8  
   > RHEL 7.4: fence-agents-4.0.11-66.el7_4.12  
   > Weitere Informationen finden Sie unter [Azure VM running as a RHEL High Availability cluster member take a very long time to be fenced, or fencing fails / times-out before the VM shuts down](https://access.redhat.com/solutions/3408711) (Die Umgrenzung eines virtuellen Azure-Computers, der als Mitglied eines RHEL-Hochverfügbarkeitsclusters ausgeführt wird, dauert sehr lange, die Umgrenzung ist nicht erfolgreich, oder bei der Umgrenzung tritt ein Timeout auf, bevor der virtuelle Computer heruntergefahren wird).

   Überprüfen Sie die Version des Azure Fence-Agent. Aktualisieren Sie ggf. auf eine Version, die mindestens der oben genannten entspricht.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Wenn Sie den Azure Fence-Agent aktualisieren müssen, und wenn Sie eine benutzerdefinierte Rolle verwenden, müssen Sie die benutzerdefinierte Rolle so aktualisieren, dass sie die Aktion **powerOff** beinhaltet. Ausführliche Informationen finden Sie unter [Erstellen einer benutzerdefinierten Rolle für den Fence Agent](#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** Richten Sie die Hostnamensauflösung ein.

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen.  

   >[!IMPORTANT]
   > Wenn Sie Hostnamen in der Clusterkonfiguration verwenden, ist es wichtig, eine zuverlässige Hostnamensauflösung zu verwenden. Die Clusterkommunikation schlägt fehl, wenn die Namen nicht verfügbar sind. Dies kann zu Verzögerungen bei Clusterfailovern führen.
   > Durch die Verwendung von „/etc/hosts“ wird Ihr Cluster vom DNS (einem weiteren möglichen Single Point of Failure) unabhängig.  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Ändern Sie das Kennwort „hacluster“ in das gleiche Kennwort.

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Hinzufügen von Firewallregeln für Pacemaker

   Fügen Sie jeglicher Clusterkommunikation zwischen den Clusterknoten die folgenden Firewallregeln hinzu.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Aktivieren grundlegender Clusterdienste

   Führen Sie die folgenden Befehle aus, um den Pacemaker-Dienst zu aktivieren und zu starten.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Erstellen des Pacemaker-Clusters

   Führen Sie die folgenden Befehle aus, um die Knoten zu authentifizieren und den Cluster zu erstellen. Legen Sie das Token auf „30.000“ fest, um die Wartung mit Speicherbeibehaltung zu ermöglichen. Weitere Informationen finden Sie in [diesem Artikel für Linux][virtual-machines-linux-maintenance].  
   
   Wenn Sie einen Cluster auf **RHEL 7.x** aufbauen, verwenden Sie die folgenden Befehle:  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   Wenn Sie einen Cluster auf **RHEL 8.X** aufbauen, verwenden Sie die folgenden Befehle:  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   Überprüfen Sie den Clusterstatus, indem Sie den folgenden Befehl ausführen:  
   <pre><code> # Run the following command until the status of both nodes is online
   sudo pcs status
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** Legen Sie die erwarteten Stimmen fest. 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > Wenn Sie einen Cluster mit mehreren Knoten einrichten, also einen Cluster mit mehr als zwei Knoten, legen Sie die Stimmen nicht auf 2 fest.    

1. **[1]** Zulassen von gleichzeitigen Fencingaktionen

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>Erstellen des STONITH-Geräts

Das STONITH-Gerät verwendet einen Dienstprinzipal zur Autorisierung bei Microsoft Azure. Führen Sie die folgenden Schritte aus, um einen Dienstprinzipal zu erstellen.

1. Besuchen Sie <https://portal.azure.com>.
1. Öffnen Sie das Blatt „Azure Active Directory“.  
   Wechseln Sie zu „Eigenschaften“, und notieren Sie sich die Verzeichnis-ID. Dies ist die **Mandanten-ID**.
1. Klicken Sie auf „App-Registrierungen“.
1. Klicken Sie auf „Neue Registrierung“.
1. Geben Sie einen Namen ein, und wählen Sie „Nur Konten in diesem Organisationsverzeichnis“ aus. 
2. Wählen Sie den Anwendungstyp „Web-App“ aus, geben Sie eine Anmelde-URL ein (z.B. „http:\//localhost“), und klicken Sie auf „Hinzufügen“.  
   Die Anmelde-URL wird nicht verwendet und kann eine beliebige gültige URL sein.
1. Wählen Sie „Zertifikate und Geheimnisse“ aus, und klicken Sie auf „Neuer geheimer Clientschlüssel“.
1. Geben Sie eine Beschreibung für einen neuen Schlüssel ein, wählen Sie „Läuft nie ab“ aus, und klicken Sie auf „Hinzufügen“.
1. Legen Sie einen Knoten als Wert ab. Er dient als **Kennwort** für den Dienstprinzipal.
1. Wählen Sie „Übersicht“ aus. Notieren Sie sich die Anwendungs-ID. Sie wird als Benutzername (**Anmelde-ID** in den folgenden Schritten) des Dienstprinzipals verwendet.

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Erstellen einer benutzerdefinierten Rolle für den Fence Agent.

Der Dienstprinzipal hat standardmäßig keine Zugriffsberechtigungen für Ihre Azure-Ressourcen. Sie müssen dem Dienstprinzipal Berechtigungen zum Starten und Beenden (Ausschalten) aller virtuellen Computer des Clusters gewähren. Wenn Sie noch keine benutzerdefinierte Rolle erstellt haben, können Sie sie mit [PowerShell](../../../role-based-access-control/role-assignments-powershell.md) oder der [Azure-Befehlszeilenschnittstelle](../../../role-based-access-control/role-assignments-cli.md) erstellen.

Verwenden Sie folgenden Inhalt für die Eingabedatei. Sie müssen den Inhalt an Ihre Abonnements anpassen, d.h., Sie müssen „c276fc76-9cd4-44c9-99a7-4fd71546436e“ und „e91d47c4-76f3-4271-a796-21b4ecfe3624“ durch die IDs Ihres Abonnements ersetzen. Wenn Sie nur über ein Abonnement verfügen, entfernen Sie den zweiten Eintrag in AssignableScopes.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Weisen Sie dem Dienstprinzipal die benutzerdefinierte Rolle zu.

Weisen Sie dem Dienstprinzipal die benutzerdefinierte Rolle „Linux Fence Agent Role“ zu, die im letzten Abschnitt erstellt wurde. Verwenden Sie die Rolle „Owner“ nicht mehr.

1. Besuchen Sie https://portal.azure.com.
1. Öffnen Sie das Blatt „Alle Ressourcen“.
1. Wählen Sie den virtuellen Computer des ersten Clusterknotens aus.
1. Klicken Sie auf „Zugriffssteuerung (IAM)“.
1. Klicken Sie auf „Rollenzuweisung hinzufügen“.
1. Wählen Sie die Rolle „Linux Fence Agent Role“ aus.
1. Geben Sie den Namen der Anwendung ein, die Sie zuvor erstellt haben.
1. Klicken Sie auf Speichern.

Wiederholen Sie die oben genannten Schritte für den zweiten Clusterknoten.

### <a name="1-create-the-stonith-devices"></a>**[1]** Erstellen Sie die STONITH-Geräte.

Nachdem Sie die Berechtigungen für die virtuellen Computer bearbeitet haben, können Sie die STONITH-Geräte im Cluster konfigurieren.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> Die Option „pcmk_host_map“ wird im Befehl NUR benötigt, wenn die RHEL-Hostnamen und die Namen der Azure-VMs NICHT identisch sind. Geben Sie die Zuordnung im Format **hostname:vm-name** an.
> Beachten Sie den fett formatierten Bereich des Befehls. Weitere Informationen finden Sie unter [Welches Format sollte ich verwenden, um Knotenzuordnungen für stonith-Geräte in „pcmk_host_map“ anzugeben?](https://access.redhat.com/solutions/2619961).

Verwenden Sie für RHEL **7.X** den folgenden Befehl, um das Fencinggerät zu konfigurieren:    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

Verwenden Sie für RHEL **8.X** den folgenden Befehl, um das Fencinggerät zu konfigurieren.  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

> [!IMPORTANT]
> Die Überwachungs- und Fencingvorgänge sind deserialisiert. Wenn daher ein Überwachungsvorgang mit längerer Laufzeit und gleichzeitig ein Fencingereignis vorliegen, erfolgt das Clusterfailover ohne Verzögerung, da der Überwachungsvorgang bereits ausgeführt wird.  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Aktivieren Sie die Verwendung eines STONITH-Geräts.

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>Azure Fence Agent erfordert ausgehende Konnektivität mit öffentlichen Endpunkten, wie zusammen mit möglichen Lösungen in [Konnektivität öffentlicher Endpunkte für VMs, die Azure Load Balancer Standard in SAP-Hochverfügbarkeitsszenarien verwenden](./high-availability-guide-standard-load-balancer-outbound-connections.md) beschrieben.  

## <a name="next-steps"></a>Nächste Schritte

* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].
