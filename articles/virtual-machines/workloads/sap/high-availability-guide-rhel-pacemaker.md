---
title: Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure | Microsoft-Dokumentation
description: Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: e082afb212be46c40566eb643d01bc37eababfa6
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992149"
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

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot

> [!NOTE]
> Pacemaker unter Red Hat Enterprise Linux verwendet den Azure Fence-Agent, um bei Bedarf einen Clusterknoten zu umgrenzen. Ein Failover kann bis zu 15 Minuten dauern, wenn beim Beenden einer Ressource ein Fehler auftritt oder keine Kommunikation mehr zwischen den Clusterknoten möglich ist. Weitere Informationen finden Sie unter [Azure VM running as a RHEL High Availability cluster member take a very long time to be fenced, or fencing fails / times-out before the VM shuts down](https://access.redhat.com/solutions/3408711) (Die Umgrenzung eines virtuellen Azure-Computers, der als Mitglied eines RHEL-Hochverfügbarkeitsclusters ausgeführt wird, dauert sehr lange, die Umgrenzung ist nicht erfolgreich, oder bei der Umgrenzung tritt ein Timeout auf, bevor der virtuelle Computer heruntergefahren wird).

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
* [SAP NetWeaver auf virtuellen Azure-Computern – Planungs- und Implementierungshandbuch][planning-guide]
* [Bereitstellung von Azure Virtual Machines für SAP unter Linux (dieser Artikel)][deployment-guide]
* [SAP NetWeaver auf virtuellen Azure-Computern – DBMS-Bereitstellungshandbuch][dbms-guide]
* [SAP HANA-Systemreplikation in Pacemaker-Cluster](https://access.redhat.com/articles/3004101)
* Allgemeine RHEL-Dokumentation:
  * [Übersicht über das Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Verwaltung des Hochverfügbarkeits-Add-Ons](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenz zum Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure-spezifische RHEL-Dokumentation:
  * [Unterstützungsrichtlinien für RHEL-Hochverfügbarkeitscluster – Virtuelle Microsoft Azure-Computer als Clustermitglieder](https://access.redhat.com/articles/3131341)
  * [Installieren und Konfigurieren eines Red Hat Enterprise Linux 7.4-Hochverfügbarkeitclusters (und höher) in Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="cluster-installation"></a>Clusterinstallation

![Übersicht über Pacemaker unter RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

1. **[A]** Registrieren

   Registrieren Sie Ihre virtuellen Computer, und ordnen Sie sie einem Pool zu, der Repositorys für RHEL 7 enthält.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Beachten Sie, dass Sie durch das Anfügen eines Pools an ein Azure Marketplace PAYG RHEL-Image effektiv eine doppelte Abrechnung für Ihre RHEL-Nutzung erhalten: einmal für das PAYG-Image und einmal für die RHEL-Berechtigung in dem Pool, den Sie anfügen. Azure bietet jetzt BYOS RHEL-Images an, um dies zu vermeiden. Weitere Informationen sind [hier](https://aka.ms/rhel-byos) verfügbar.

1. **[A]** Aktivieren von RHEL für SAP-Repositorys

   Aktivieren Sie die folgenden Repositorys, um die erforderlichen Pakete zu installieren.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable="rhel-sap-for-rhel-7-server-rpms"
   </code></pre>

1. **[A]** Installieren des RHEL-Hochverfügbarkeits-Add-Ons

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

1. **[A]** Richten Sie die Hostnamensauflösung ein.

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen. Durch die Verwendung von „/etc/hosts“ wird Ihr Cluster vom DNS (einem weiteren möglichen Single Point of Failure) unabhängig.

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

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
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
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** Festlegen der erwarteten Stimmen

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Erstellen des STONITH-Geräts

Das STONITH-Gerät verwendet einen Dienstprinzipal zur Autorisierung bei Microsoft Azure. Führen Sie die folgenden Schritte aus, um einen Dienstprinzipal zu erstellen.

1. Besuchen Sie <https://portal.azure.com>.
1. Öffnen Sie das Blatt „Azure Active Directory“. Wechseln Sie zu „Eigenschaften“, und notieren Sie sich die Verzeichnis-ID. Dies ist die **Mandanten-ID**.
1. Klicken Sie auf „App-Registrierungen“.
1. Klicken Sie auf "Hinzufügen".
1. Geben Sie einen Namen ein, wählen Sie den Anwendungstyp „Web-App/API“, geben Sie eine Anmelde-URL ein (z. B. „http:\//localhost“), und klicken Sie auf „Erstellen“.
1. Die Anmelde-URL wird nicht verwendet und kann eine beliebige gültige URL sein.
1. Wählen Sie die neue App aus, und klicken Sie auf der Registerkarte „Einstellungen“ auf „Schlüssel“.
1. Geben Sie eine Beschreibung für einen neuen Schlüssel ein, wählen Sie „Läuft nie ab“, und klicken Sie auf „Speichern“.
1. Notieren Sie sich den Wert. Er dient als **Kennwort** für den Dienstprinzipal.
1. Notieren Sie sich die Anwendungs-ID. Sie wird als Benutzername (**Anmelde-ID** in den folgenden Schritten) des Dienstprinzipals verwendet.

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Erstellen einer benutzerdefinierten Rolle für den Fence Agent.

Der Dienstprinzipal hat standardmäßig keine Zugriffsberechtigungen für Ihre Azure-Ressourcen. Sie müssen dem Dienstprinzipal Berechtigungen zum Starten und Beenden (Freigeben) aller virtuellen Computer des Clusters gewähren. Wenn Sie noch keine benutzerdefinierte Rolle erstellt haben, können Sie sie mit [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) oder der [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) erstellen.

Verwenden Sie folgenden Inhalt für die Eingabedatei. Sie müssen den Inhalt an Ihre Abonnements anpassen, d.h., Sie müssen „c276fc76-9cd4-44c9-99a7-4fd71546436e“ und „e91d47c4-76f3-4271-a796-21b4ecfe3624“ durch die IDs Ihres Abonnements ersetzen. Wenn Sie nur über ein Abonnement verfügen, entfernen Sie den zweiten Eintrag in AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
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

Verwenden Sie den folgenden Befehl, um das Umgrenzungsgerät zu konfigurieren.

> [!NOTE]
> Die Option „pcmk_host_map“ wird im Befehl nur benötigt, wenn die RHEL-Hostnamen und die Azure-Knotennamen NICHT identisch sind. Beachten Sie den fett formatierten Bereich des Befehls.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Aktivieren Sie die Verwendung eines STONITH-Geräts.

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Nächste Schritte

* [SAP NetWeaver auf virtuellen Azure-Computern – Planungs- und Implementierungshandbuch][planning-guide]
* [Bereitstellung von Azure Virtual Machines für SAP][deployment-guide]
* [SAP NetWeaver auf virtuellen Azure-Computern – DBMS-Bereitstellungshandbuch][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].
