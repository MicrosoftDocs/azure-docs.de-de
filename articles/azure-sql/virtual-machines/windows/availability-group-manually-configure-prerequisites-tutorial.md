---
title: 'Tutorial: Voraussetzungen für eine Verfügbarkeitsgruppe'
description: In diesem Tutorial erfahren Sie, wie Sie die Voraussetzungen zum Erstellen einer SQL Server Always On-Verfügbarkeitsgruppe in Azure Virtual Machines erfüllen.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 869c4ac5cde7d1e50be0f2f738d8a0ce6de5e625
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951714"
---
# <a name="tutorial-prerequisites-for-creating-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Tutorial: Voraussetzungen für die Erstellung von Verfügbarkeitsgruppen für SQL Server in Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Tutorial erfahren Sie, wie Sie die Voraussetzungen zum Erstellen einer [SQL Server Always On-Verfügbarkeitsgruppe in Azure Virtual Machines](availability-group-manually-configure-tutorial.md) erfüllen. Sobald die Voraussetzungen erfüllt sind, verfügen Sie in einer einzelnen Ressourcengruppe über einen Domänencontroller, zwei SQL Server-VMs und einen Zeugenserver.

In diesem Artikel wird die Umgebung der Verfügbarkeitsgruppen manuell konfiguriert. Diese Konfiguration kann aber auch über das [Azure-Portal](availability-group-azure-portal-configure.md), [PowerShell oder die Azure CLI](availability-group-az-commandline-configure.md) oder mithilfe von [Azure-Schnellstartvorlagen](availability-group-quickstart-template-configure.md) erfolgen. 

**Geschätzter Zeitaufwand**: Es dauert möglicherweise einige Stunden, um die Voraussetzungen zu erfüllen. Ein großer Teil dieser Zeit wird zum Erstellen virtueller Computer aufgewendet.

Das folgende Diagramm veranschaulicht, was Sie im Rahmen dieses Tutorials erstellen.

![Verfügbarkeitsgruppe](./media/availability-group-manually-configure-prerequisites-tutorial-/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Lesen der Dokumentation zu Verfügbarkeitsgruppen

Für dieses Tutorial werden Grundkenntnisse über SQL Server Always On-Verfügbarkeitsgruppen vorausgesetzt. Wenn Sie mit dieser Technologie nicht vertraut sind, lesen Sie die [Übersicht über AlwaysOn-Verfügbarkeitsgruppen (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).


## <a name="create-an-azure-account"></a>Erstellen eines Azure-Kontos

Sie benötigen ein Azure-Konto. Sie können entweder ein [kostenloses Azure-Konto erstellen](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic) oder [Visual Studio-Abonnementvorteile aktivieren](/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **+** aus, um im Portal ein neues Objekt zu erstellen.

   ![Neues Objekt](./media/availability-group-manually-configure-prerequisites-tutorial-/01-portalplus.png)

3. Geben Sie **Ressourcengruppe** in das **Marketplace**-Suchfenster ein.

   ![Resource group](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroupsymbol.png)

4. Wählen Sie **Ressourcengruppe** aus.
5. Klicken Sie auf **Erstellen**.
6. Geben Sie unter **Ressourcengruppenname** einen Namen für die Ressourcengruppe ein. Geben Sie beispielsweise **sql-ha-rg** ein.
7. Falls Sie über mehrere Azure-Abonnements verfügen, vergewissern Sie sich, dass es sich bei dem Abonnement um das Azure-Abonnement handelt, in dem Sie die Verfügbarkeitsgruppe erstellen möchten.
8. Wählen Sie einen Standort aus. Der Standort ist die Azure-Region, in der die Verfügbarkeitsgruppe erstellt werden soll. In diesem Artikel werden alle Ressourcen an einem Azure-Standort erstellt.
9. Vergewissern Sie sich, dass **An Dashboard anheften** aktiviert ist. Diese optionale Einstellung platziert eine Verknüpfung mit der Ressourcengruppe auf dem Dashboard des Azure-Portals.

   ![Verknüpfung mit der Ressourcengruppe im Azure-Portal](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroup.png)

10. Wählen Sie **Erstellen** aus, um die Ressourcengruppe zu erstellen.

Azure erstellt die Ressourcengruppe und heftet eine Verknüpfung mit der Ressourcengruppe im Portal an.

## <a name="create-the-network-and-subnets"></a>Erstellen des Netzwerks und der Subnetze

Im nächsten Schritt werden die Netzwerke und Subnetze in der Azure-Ressourcengruppe erstellt.

Die Lösung verwendet ein virtuelles Netzwerk mit zwei Subnetzen. Weitere Informationen zu Netzwerken in Azure finden Sie im [Überblick über virtuelle Netzwerke](../../../virtual-network/virtual-networks-overview.md).

So erstellen Sie das virtuelle Netzwerk im Azure-Portal

1. Wählen Sie in Ihrer Ressourcengruppe **+ Hinzufügen** aus. 

   ![Neues Element](./media/availability-group-manually-configure-prerequisites-tutorial-/02-newiteminrg.png)
2. Suchen Sie nach **Virtuelles Netzwerk**.

     ![Suchen eines virtuellen Netzwerks](./media/availability-group-manually-configure-prerequisites-tutorial-/04-findvirtualnetwork.png)
3. Wählen Sie **Virtuelles Netzwerk** aus.
4. Wählen Sie unter **Virtuelles Netzwerk** das Bereitstellungsmodell **Resource Manager** und anschließend **Erstellen** aus.

    Die folgende Tabelle enthält die Einstellungen für das virtuelle Netzwerk:

   | **Feld** | Wert |
   | --- | --- |
   | **Name** |autoHAVNET |
   | **Adressraum** |10.0.0.0/24 |
   | **Subnetzname** |Admin |
   | **Subnetzadressbereich** |10.0.0.0/29 |
   | **Abonnement** |Geben Sie das Abonnement an, das Sie verwenden möchten. Wenn Sie nur über ein einzelnes Abonnement verfügen, ist die Option **Abonnement** leer. |
   | **Ressourcengruppe** |Wählen Sie **Vorhanden** aus, und wählen Sie dann den Namen der Ressourcengruppe aus. |
   | **Location** |Geben Sie den Azure-Standort an. |

   Ihr Adressraum und Ihr Subnetzadressbereich können sich von den Angaben in der Tabelle unterscheiden. Abhängig von Ihrem Abonnement schlägt das Portal einen verfügbaren Adressraum und den entsprechenden Subnetzadressbereich vor. Ist kein geeigneter Adressraum verfügbar, verwenden Sie ein anderes Abonnement.

   Im Beispiel wird der Subnetzname **Admin** verwendet. Dieses Subnetz ist für die Domänencontroller bestimmt.

5. Klicken Sie auf **Erstellen**.

   ![Konfigurieren des virtuellen Netzwerks](./media/availability-group-manually-configure-prerequisites-tutorial-/06-configurevirtualnetwork.png)

Azure zeigt wieder das Portaldashboard an und benachrichtigt Sie, wenn das neue Netzwerk erstellt wurde.

### <a name="create-a-second-subnet"></a>Erstellen eines zweiten Subnetzes

Das neue virtuelle Netzwerk verfügt über ein Subnetz mit dem Namen **Admin**. Dieses Subnetz wird von den Domänencontrollern verwendet. Die SQL Server-VMs verwenden ein zweites Subnetz namens **SQL**. So konfigurieren Sie dieses Subnetz:

1. Wählen Sie in Ihrem Dashboard die zuvor erstellte Ressourcengruppe **SQL-HA-RG** aus. Suchen Sie das Netzwerk in der Ressourcengruppe unter **Ressourcen**.

    Wenn **SQL-HA-RG** nicht angezeigt wird, wählen Sie **Ressourcengruppen** aus, und filtern Sie nach dem Namen der Ressourcengruppe.

2. Wählen Sie in der Liste mit den Ressourcen **autoHAVNET** aus. 
3. Klicken Sie im virtuellen Netzwerk **autoHAVNET** unter **Einstellungen** auf **Subnetze**.

    Hier sehen Sie das bereits erstellte Subnetz.

   ![Das bereits erstellte Subnetz](./media/availability-group-manually-configure-prerequisites-tutorial-/07-addsubnet.png)

5. Um ein zweites Subnetz zu erstellen, wählen Sie **+ Subnetz** aus.
6. Konfigurieren Sie unter **Subnetz hinzufügen** das Subnetz, indem Sie unter **Name** die Zeichenfolge **sqlsubnet** eingeben. Azure gibt automatisch einen gültigen **Adressbereich** an. Vergewissern Sie sich, dass dieser Adressbereich mindestens zehn Adressen umfasst. In einer Produktionsumgebung werden unter Umständen weitere Adressen benötigt.
7. Klicken Sie auf **OK**.

    ![Konfigurieren des Subnetzes](./media/availability-group-manually-configure-prerequisites-tutorial-/08-configuresubnet.png)

In der folgenden Tabelle sind die Netzwerkkonfigurationseinstellungen zusammengefasst:

| **Feld** | Wert |
| --- | --- |
| **Name** |**autoHAVNET** |
| **Adressraum** |Dieser Wert richtet sich nach den verfügbaren Adressräumen in Ihrem Abonnement. Ein typischer Wert wäre etwa 10.0.0.0/16. |
| **Subnetzname** |**admin** |
| **Subnetzadressbereich** |Dieser Wert richtet sich nach den verfügbaren Adressbereichen in Ihrem Abonnement. Ein typischer Wert wäre etwa 10.0.0.0/24. |
| **Subnetzname** |**sqlsubnet** |
| **Subnetzadressbereich** |Dieser Wert richtet sich nach den verfügbaren Adressbereichen in Ihrem Abonnement. Ein typischer Wert wäre etwa 10.0.1.0/24. |
| **Abonnement** |Geben Sie das Abonnement an, das Sie verwenden möchten. |
| **Ressourcengruppe** |**SQL-HA-RG** |
| **Location** |Geben Sie den Speicherort an, den Sie auch für die Ressourcengruppe ausgewählt haben. |

## <a name="create-availability-sets"></a>Erstellen von Verfügbarkeitsgruppen

Vor dem Erstellen virtueller Computer müssen zunächst Verfügbarkeitsgruppen erstellt werden. Verfügbarkeitsgruppen verringern die Ausfallzeit bei geplanten oder ungeplanten Wartungsereignissen. Eine Azure-Verfügbarkeitsgruppe ist eine logische Gruppe von Ressourcen, die Azure in physischen Fehlerdomänen und Updatedomänen platziert. Eine Fehlerdomäne stellt sicher, dass die Mitglieder der Verfügbarkeitsgruppe über eine separate Stromversorgung sowie über separate Netzwerkressourcen verfügen. Eine Updatedomäne stellt sicher, dass die Mitglieder der Verfügbarkeitsgruppe nicht gleichzeitig zu Wartungszwecken heruntergefahren werden. Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](../../../virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Sie benötigen zwei Verfügbarkeitsgruppen: Eine für die Domänencontroller Eine für die SQL Server-VMs

Navigieren Sie zum Erstellen einer Verfügbarkeitsgruppe zur Ressourcengruppe, und wählen Sie **Hinzufügen** aus. Filtern Sie die Ergebnisse, indem Sie **Verfügbarkeitsgruppe** eingeben. Wählen Sie in den Ergebnissen **Verfügbarkeitsgruppe** und dann **Erstellen** aus.

Konfigurieren Sie die beiden Verfügbarkeitsgruppen mit den Parametern in der folgenden Tabelle:

| **Feld** | Verfügbarkeitsgruppe für Domänencontroller | Verfügbarkeitsgruppe für SQL Server |
| --- | --- | --- |
| **Name** |adavailabilityset |sqlavailabilityset |
| **Ressourcengruppe** |SQL-HA-RG |SQL-HA-RG |
| **Fehlerdomänen** |3 |3 |
| **Updatedomänen** |5 |3 |

Kehren Sie nach Erstellung der Verfügbarkeitsgruppen zur Ressourcengruppe im Azure-Portal zurück.

## <a name="create-domain-controllers"></a>Erstellen von Domänencontrollern

Nach dem Erstellen des Netzwerks, der Subnetze und der Verfügbarkeitsgruppen können Sie die virtuellen Computer für die Domänencontroller erstellen.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Erstellen der virtuellen Computer für die Domänencontroller

Kehren Sie zum Erstellen und Konfigurieren der Domänencontroller zur Ressourcengruppe **SQL-HA-RG** zurück.

1. Wählen Sie **Hinzufügen**. 
2. Geben Sie **Windows Server 2016 Datacenter** ein.
3. Wählen Sie **Windows Server 2016 Datacenter** aus. Vergewissern Sie sich unter **Windows Server 2016 Datacenter**, dass das Bereitstellungsmodell **Resource Manager** lautet, und wählen Sie dann **Erstellen** aus. 

Wiederholen Sie die obigen Schritte, um zwei virtuelle Computer zu erstellen. Benennen Sie die beiden virtuellen Computer wie folgt:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > Der virtuelle Computer **ad-secondary-dc** ist optional und soll die Hochverfügbarkeit von Active Directory Domain Services gewährleisten.
  >

Die folgende Tabelle enthält die Einstellungen für die beiden Computer:

| **Feld** | Wert |
| --- | --- |
| **Name** |Erster Domänencontroller: *ad-primary-dc*.</br>Zweiter Domänencontroller: *ad-secondary-dc*. |
| **VM-Datenträgertyp** |SSD |
| **Benutzername** |DomainAdmin |
| **Kennwort** |Contoso!0000 |
| **Abonnement** |*Ihr Abonnement* |
| **Ressourcengruppe** |SQL-HA-RG |
| **Location** |*Ihr Standort* |
| **Größe** |DS1_V2 |
| **Storage** | **Verwaltete Datenträger verwenden** - **Ja** |
| **Virtuelles Netzwerk** |autoHAVNET |
| **Subnetz** |admin |
| **Öffentliche IP-Adresse** |*Gleicher Name wie der VM* |
| **Netzwerksicherheitsgruppe** |*Gleicher Name wie der VM* |
| **Verfügbarkeitsgruppe** |adavailabilityset </br>**Fehlerdomänen**: 2 </br>**Updatedomänen**: 2|
| **Diagnose** |Aktiviert |
| **Diagnosespeicherkonto** |*Automatisch erstellt* |

   >[!IMPORTANT]
   >Sie können einen virtuellen Computer nur bei seiner Erstellung in einer Verfügbarkeitsgruppe platzieren. Die Verfügbarkeitsgruppe für einen virtuellen Computer kann nach dessen Erstellung nicht mehr geändert werden. Siehe [Verwalten der Verfügbarkeit virtueller Computer](../../../virtual-machines/manage-availability.md).

Azure erstellt die virtuellen Computer.

Konfigurieren Sie nach der Erstellung der virtuellen Computer den Domänencontroller.

### <a name="configure-the-domain-controller"></a>Konfigurieren des Domänencontrollers

In den folgenden Schritten konfigurieren Sie den Computer **ad-primary-dc** als Domänencontroller für „corp.contoso.com“.

1. Öffnen Sie im Portal die Ressourcengruppe **SQL-HA-RG**, und wählen Sie den Computer **ad-primary-dc** aus. Wählen Sie unter **ad-primary-dc** den Befehl **Verbinden** aus, um eine RDP-Datei für den Remotedesktopzugriff zu öffnen.

    ![Herstellen einer Verbindung mit einem virtuellen Computer](./media/availability-group-manually-configure-prerequisites-tutorial-/20-connectrdp.png)

2. Melden Sie sich mit Ihrem konfigurierten Administratorkonto ( **\DomainAdmin**) und Kennwort (**Contoso!0000**) an.
3. Standardmäßig sollte das Dashboard **Server-Manager** angezeigt werden.
4. Wählen Sie im Dashboard den Link **Rollen und Features hinzufügen** aus.

    ![Server Manager – Hinzufügen von Rollen](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

5. Wählen Sie **Weiter** aus, bis Sie zum Abschnitt **Serverrollen** gelangen.
6. Wählen Sie die Rollen **Active Directory Domain Services** und **DNS-Server** aus. Wenn Sie dazu aufgefordert werden, fügen Sie alle für diese Rollen erforderlichen zusätzlichen Funktionen hinzu.

   > [!NOTE]
   > In Windows wird die Warnung angezeigt, dass keine statische IP-Adresse vorhanden ist. Wenn Sie die Konfiguration testen, wählen Sie **Weiter** aus. Legen Sie in Produktionsszenarien die IP-Adresse über das Azure-Portal als statische Adresse fest, oder [verwenden Sie PowerShell, um die statische IP-Adresse des Domänencontrollercomputers festzulegen](/previous-versions/azure/virtual-network/virtual-networks-reserved-private-ip).
   >

    ![Dialogfeld „Rollen hinzufügen“](./media/availability-group-manually-configure-prerequisites-tutorial-/23-addroles.png)

7. Wählen Sie **Weiter** aus, bis Sie zum Abschnitt **Bestätigung** gelangen. Aktivieren Sie das Kontrollkästchen **Zielserver bei Bedarf automatisch neu starten**.
8. Wählen Sie **Installieren** aus.
9. Nachdem die Installation der Funktionen abgeschlossen ist, kehren Sie zum Dashboard **Server-Manager** zurück.
10. Wählen Sie die neue Option **AD DS** im linken Bereich aus.
11. Wählen Sie auf der gelben Warnleiste den Link **Mehr** aus.

    ![AD DS-Dialogfeld auf dem virtuellen DNS-Servercomputer](./media/availability-group-manually-configure-prerequisites-tutorial-/24-addsmore.png)
    
12. Wählen Sie im Dialogfeld **Alle Serveraufgabendetails** in der Spalte **Aktion** den Eintrag **Server zu einem Domänencontroller heraufstufen** aus.
13. Verwenden Sie im **Konfigurations-Assistenten für Active Directory-Domänendienste** die folgenden Werte:

    | **Seite** | Einstellung |
    | --- | --- |
    | **Bereitstellungskonfiguration** |**Neue Gesamtstruktur hinzufügen**<br/> **Rootdomänenname** = corp.contoso.com |
    | **Domänencontrolleroptionen** |**DSRM-Kennwort** = Contoso!0000<br/>**Kennwort bestätigen** = Contoso!0000 |

14. Wählen Sie **Weiter** aus, um die anderen Seiten des Assistenten zu durchlaufen. Vergewissern Sie sich, dass auf der Seite **Prüfung der erforderlichen Komponenten** die folgende Meldung angezeigt wird: **Alle erforderlichen Komponenten wurden erfolgreich überprüft.** Sie können alle zutreffenden Warnmeldungen überprüfen, aber es ist möglich, die Installation fortzusetzen.
15. Wählen Sie **Installieren** aus. Der virtuelle Computer **ad-primary-dc** wird automatisch neu gestartet.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Notieren der IP-Adresse des primären Domänencontrollers

Verwenden Sie den primären Domänencontroller für den DNS. Notieren Sie die IP-Adresse des primären Domänencontrollers.

Eine Möglichkeit, die IP-Adresse des primären Domänencontrollers abzurufen, ist der Weg über das Azure-Portal.

1. Öffnen Sie im Azure-Portal die Ressourcengruppe.

2. Wählen Sie den primären Domänencontroller aus.

3. Wählen Sie auf der Seite mit dem primären Domänencontroller **Netzwerkschnittstellen** aus.

![Netzwerkschnittstellen](./media/availability-group-manually-configure-prerequisites-tutorial-/25-primarydcip.png)

Notieren Sie die private IP-Adresse für diesen Server.

### <a name="configure-the-virtual-network-dns"></a>Konfigurieren des DNS für das virtuelle Netzwerk

Nachdem Sie den ersten Domänencontroller erstellt und DNS auf dem ersten Server aktiviert haben, konfigurieren Sie das virtuelle Netzwerk für die Verwendung dieses Servers für DNS.

1. Wählen Sie im Azure-Portal das virtuelle Netzwerk aus.

2. Wählen Sie unter **Einstellungen** die Option **DNS-Server** aus.

3. Wählen Sie **Benutzerdefiniert** aus, und geben Sie die private IP-Adresse des primären Domänencontrollers ein.

4. Wählen Sie **Speichern** aus.

### <a name="configure-the-second-domain-controller"></a>Konfigurieren des zweiten Domänencontrollers

Nach dem Neustart des primären Domänencontrollers können Sie den zweiten Domänencontroller konfigurieren. Dieser optionale Schritt dient zur Gewährleistung der Hochverfügbarkeit. Gehen Sie wie folgt vor, um den zweiten Domänencontroller zu konfigurieren:

1. Öffnen Sie im Portal die Ressourcengruppe **SQL-HA-RG**, und wählen Sie den Computer **ad-secondary-dc** aus. Wählen Sie unter **ad-secondary-dc** den Befehl **Verbinden** aus, um eine RDP-Datei für den Remotedesktopzugriff zu öffnen.
2. Melden Sie sich mit Ihrem konfigurierten Administratorkonto (**BUILTIN\DomainAdmin**) und Kennwort (**Contoso!0000**) bei dem virtuellen Computer an.
3. Legen Sie die Adresse des Domänencontrollers als bevorzugte DNS-Serveradresse fest.
4. Wählen Sie in **Netzwerk- und Freigabecenter** die Netzwerkschnittstelle aus.

   ![Netzwerkschnittstelle](./media/availability-group-manually-configure-prerequisites-tutorial-/26-networkinterface.png)

5. Wählen Sie **Eigenschaften** aus.
6. Wählen Sie **Internetprotokoll Version 4 (TCP/IPv4)** und dann **Eigenschaften** aus.
7. Wählen Sie **Folgende DNS-Serveradressen verwenden** aus, und geben Sie unter **Bevorzugter DNS-Server** die Adresse des primären Domänencontrollers an.
8. Wählen Sie **OK** und anschließend **Schließen** aus, um die Änderungen zu übernehmen. Sie können den virtuellen Computer jetzt zu **corp.contoso.com** beitreten lassen.

   >[!IMPORTANT]
   >Wenn Sie die Verbindung mit dem Remotedesktop nach dem Ändern der DNS-Einstellung verlieren, wechseln Sie zum Azure-Portal, und starten Sie den virtuellen Computer neu.

9. Öffnen Sie über den Remotedesktop auf dem sekundären Domänencontroller das Dashboard **Server-Manager**.
10. Wählen Sie im Dashboard den Link **Rollen und Features hinzufügen** aus.

    ![Server Manager – Hinzufügen von Rollen](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
11. Wählen Sie **Weiter** aus, bis Sie zum Abschnitt **Serverrollen** gelangen.
12. Wählen Sie die Rollen **Active Directory Domain Services** und **DNS-Server** aus. Wenn Sie dazu aufgefordert werden, fügen Sie alle für diese Rollen erforderlichen zusätzlichen Funktionen hinzu.
13. Nachdem die Installation der Funktionen abgeschlossen ist, kehren Sie zum Dashboard **Server-Manager** zurück.
14. Wählen Sie die neue Option **AD DS** im linken Bereich aus.
15. Wählen Sie auf der gelben Warnleiste den Link **Mehr** aus.
16. Wählen Sie im Dialogfeld **Alle Serveraufgabendetails** in der Spalte **Aktion** den Eintrag **Server zu einem Domänencontroller heraufstufen** aus.
17. Wählen Sie unter **Bereitstellungskonfiguration** die Option **Domänencontroller vorhandener Domäne hinzufügen**.

    ![Bereitstellungskonfiguration](./media/availability-group-manually-configure-prerequisites-tutorial-/28-deploymentconfig.png)

18. Klicken Sie auf **Auswählen**.
19. Verwenden Sie das Administratorkonto (**CORP.CONTOSO.COM\domainadmin**) und Kennwort (**Contoso!0000**), um eine Verbindung herzustellen.
20. Wählen Sie unter **Domäne aus der Gesamtstruktur auswählen** Ihre Domäne und dann **OK** aus.
21. Verwenden Sie unter **Domänencontrolleroptionen** die Standardwerte, und legen Sie ein DSRM-Kennwort fest.

    >[!NOTE]
    >Auf der Seite **DNS-Optionen** wird möglicherweise eine Warnung angezeigt, dass für diesen DNS-Server keine Delegierung erstellt werden kann. Sie können diese Warnung in nicht produktiven Umgebungen ignorieren.
    >

22. Wählen Sie **Weiter** aus, bis das Dialogfeld die **Voraussetzungsprüfung** erreicht. Wählen Sie dann **Installieren** aus.

Nachdem der Server die Änderungen an der Konfiguration abgeschlossen hat, starten Sie den Server neu.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Hinzufügen der privaten IP-Adresse des zweiten Domänencontrollers zum DNS-Server des VPN

Ändern Sie im Azure-Portal unter „Virtuelles Netzwerk“ den DNS-Server, sodass die IP-Adresse des sekundären Domänencontrollers eingeschlossen ist. Diese Einstellung ermöglicht einen redundanten DNS-Dienst.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a> Konfigurieren der Domänenkonten

In den nächsten Schritten konfigurieren Sie die Active Directory-Konten. Die folgende Tabelle zeigt die Konten:

| |Installationskonto<br/> |sqlserver-0 <br/>SQL Server- und SQL Agent-Dienstkonto |sqlserver-1<br/>SQL Server- und SQL Agent-Dienstkonto
| --- | --- | --- | ---
|**Vorname** |Installieren |SQLSvc1 | SQLSvc2
|**SamAccountName von Benutzer** |Installieren |SQLSvc1 | SQLSvc2

Führen Sie zum Erstellen jedes Kontos die folgenden Schritte aus.

1. Melden Sie sich beim Computer **ad-primary-dc** an.
2. Wählen Sie im **Server-Manager** die Option **Tools** und dann **Active Directory-Verwaltungscenter** aus.   
3. Wählen Sie **corp (local)** im linken Bereich.
4. Wählen Sie rechts im Bereich **Aufgaben** die Option **Neu** und dann **Benutzer** aus.

   ![Active Directory-Verwaltungscenter](./media/availability-group-manually-configure-prerequisites-tutorial-/29-addcnewuser.png)

   >[!TIP]
   >Legen Sie ein komplexes Kennwort für jedes Konto fest.<br/> Legen Sie für nicht produktive Umgebungen fest, dass das Benutzerkonto nie abläuft.
   >

5. Wählen Sie **OK** aus, um den Benutzer zu erstellen.
6. Wiederholen Sie die vorherigen Schritte für jedes der drei Konten.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Erteilen der erforderlichen Berechtigungen für das Installationskonto

1. Wählen Sie im **Active Directory-Verwaltungscenter** im linken Bereich die Option **corp (lokal)** aus. Wählen Sie dann rechts im **Aufgabenbereich** die Option **Eigenschaften** aus.

    ![CORP-Benutzereigenschaften](./media/availability-group-manually-configure-prerequisites-tutorial-/31-addcproperties.png)

2. Wählen Sie **Erweiterungen** und dann auf der Registerkarte **Sicherheit** die Schaltfläche **Erweitert** aus.
3. Wählen Sie im Dialogfeld **Erweiterte Sicherheitseinstellungen für corp** den Befehl **Hinzufügen** aus.
4. Klicken Sie auf **Prinzipal auswählen**, suchen Sie nach **CORP\Install**, und wählen Sie dann **OK** aus.
5. Aktivieren Sie das Kontrollkästchen **Alle Eigenschaften lesen**.

6. Aktivieren Sie das Kontrollkästchen **Computerobjekte erstellen**.

     ![Corp-Benutzerberechtigungen](./media/availability-group-manually-configure-prerequisites-tutorial-/33-addpermissions.png)

7. Klicken Sie auf **OK** und anschließend erneut auf **OK**. Schließen Sie das Eigenschaftenfenster von **corp**.

Nachdem Sie die Konfiguration von Active Directory und den Benutzerobjekten abgeschlossen haben, erstellen Sie jetzt zwei virtuelle SQL Server-Computer und einen virtuellen Computer als Zeugenserver. Führen Sie für alle drei Computer anschließend den Beitritt zur Domäne durch.

## <a name="create-sql-server-vms"></a>Erstellen der virtuellen SQL Server-Computer

Erstellen Sie drei zusätzliche virtuelle Computer. Die Lösung erfordert zwei virtuelle Computer mit SQL Server-Instanzen. Ein dritter virtueller Computer fungiert als Zeuge. Windows Server 2016 kann einen [Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness) verwenden. Aus Gründen der Konsistenz mit älteren Betriebssystemen wird in diesem Artikel jedoch als Zeuge ein virtueller Computer verwendet.  

Bevor Sie fortfahren, sollten Sie die folgenden Entwurfsentscheidungen treffen.

* **Speicher – Azure Managed Disks**

   Verwenden Sie als Speicher für virtuelle Computer Azure Managed Disks. Microsoft empfiehlt Managed Disks für virtuelle SQL Server-Computer. Managed Disks verwaltet den Speicher im Hintergrund. Wenn sich virtuelle Computer mit verwalteten Datenträgern in derselben Verfügbarkeitsgruppe befinden, verteilt Azure darüber hinaus die Speicherressourcen so, dass eine ausreichende Redundanz bereitgestellt wird. Weitere Informationen finden Sie in der [Übersicht über Azure Managed Disks](../../../virtual-machines/managed-disks-overview.md). Genauere Informationen zu verwalteten Datenträgern in einer Verfügbarkeitsgruppe finden Sie unter [Verwenden von verwalteten Datenträgern für virtuelle Computer in einer Verfügbarkeitsgruppe](../../../virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Netzwerk – private IP-Adressen in der Produktion**

   Für die virtuellen Computer werden in diesem Tutorial öffentliche IP-Adressen verwendet. Eine öffentliche IP-Adresse ermöglicht eine direkte Remoteverbindung mit dem virtuellen Computer über das Internet. Die Konfigurationsschritte werden somit vereinfacht. In Produktionsumgebungen empfiehlt Microsoft private IP-Adressen, um die Sicherheitsrisiken der VM-Ressource für die SQL Server-Instanz zu verringern.

* **Netzwerk: Empfehlung für eine einzelne NIC pro Server** 

Verwenden Sie eine einzelne NIC pro Server (Clusterknoten) und ein einzelnes Subnetz. Azure-Netzwerktechnologie bietet physische Redundanz, die zusätzliche Netzwerkkarten und Subnetze in einem Azure-VM-Gastcluster überflüssig macht. Der Clusterüberprüfungsbericht weist Sie darauf hin, dass die Knoten nur in einem einzigen Netzwerk erreichbar sind. Diese Warnung kann für Azure-VM-Gastfailovercluster ignoriert werden.

### <a name="create-and-configure-the-sql-server-vms"></a>Erstellen und Konfigurieren der SQL Server-VMs

Im nächsten Schritt erstellen Sie drei VMs: zwei SQL Server-VMs und eine VM für einen zusätzlichen Clusterknoten. Um die einzelnen VMs zu erstellen, kehren Sie zur Ressourcengruppe **SQL-HA-RG** zurück und wählen dann **Hinzufügen** aus. Suchen Sie das entsprechende Katalogelement, wählen Sie **Virtueller Computer** und dann **Aus Katalog** aus. Verwenden Sie die Informationen in der folgenden Tabelle, die Sie bei der Erstellung der virtuellen Computer unterstützen:


| Seite | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Wählen Sie das passende Katalogelement aus. |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise unter Windows Server 2016** |**SQL Server 2016 SP1 Enterprise unter Windows Server 2016** |
| **Grundlagen** |**Name** = cluster-fsw<br/>**Benutzername** = DomainAdmin<br/>**Kennwort** = Contoso!0000<br/>**Abonnement** = Ihr Abonnement<br/>**Ressourcengruppe** = SQL-HA-RG<br/>**Standort** = Ihr Azure-Standort |**Name** = sqlserver-0<br/>**Benutzername** = DomainAdmin<br/>**Kennwort** = Contoso!0000<br/>**Abonnement** = Ihr Abonnement<br/>**Ressourcengruppe** = SQL-HA-RG<br/>**Standort** = Ihr Azure-Standort |**Name** = sqlserver-1<br/>**Benutzername** = DomainAdmin<br/>**Kennwort** = Contoso!0000<br/>**Abonnement** = Ihr Abonnement<br/>**Ressourcengruppe** = SQL-HA-RG<br/>**Standort** = Ihr Azure-Standort |
| Konfiguration des virtuellen Computers: **Größe** |**Größe** = DS1\_V2 (1 vCPU, 3,5 GB) |**Größe** = DS2\_V2 (2 vCPUs, 7 GB)</br>Die Größe muss SSD-Speicher (Premium-Datenträger) unterstützen. )) |**Größe** = DS2\_V2 (2 vCPUs, 7 GB) |
| Konfiguration des virtuellen Computers: **Einstellungen** |**Storage**: Verwaltete Datenträger verwenden.<br/>**Virtuelles Netzwerk** = autoHAVNET<br/>**Subnetz** = sqlsubnet(10.1.1.0/24)<br/>**Öffentliche IP-Adresse:** automatisch generiert.<br/>**Netzwerksicherheitsgruppe** = Keine<br/>**Überwachung und Diagnose** = Aktiviert<br/>**Diagnosespeicherkonto** = Automatisch generiertes Speicherkonto verwenden<br/>**Verfügbarkeitsgruppe** = sqlAvailabilitySet<br/> |**Storage**: Verwaltete Datenträger verwenden.<br/>**Virtuelles Netzwerk** = autoHAVNET<br/>**Subnetz** = sqlsubnet(10.1.1.0/24)<br/>**Öffentliche IP-Adresse:** automatisch generiert.<br/>**Netzwerksicherheitsgruppe** = Keine<br/>**Überwachung und Diagnose** = Aktiviert<br/>**Diagnosespeicherkonto** = Automatisch generiertes Speicherkonto verwenden<br/>**Verfügbarkeitsgruppe** = sqlAvailabilitySet<br/> |**Storage**: Verwaltete Datenträger verwenden.<br/>**Virtuelles Netzwerk** = autoHAVNET<br/>**Subnetz** = sqlsubnet(10.1.1.0/24)<br/>**Öffentliche IP-Adresse:** automatisch generiert.<br/>**Netzwerksicherheitsgruppe** = Keine<br/>**Überwachung und Diagnose** = Aktiviert<br/>**Diagnosespeicherkonto** = Automatisch generiertes Speicherkonto verwenden<br/>**Verfügbarkeitsgruppe** = sqlAvailabilitySet<br/> |
| Konfiguration des virtuellen Computers: **SQL Server-Einstellungen** |Nicht verfügbar |**SQL-Konnektivität** = Privat (innerhalb von Virtual Network)<br/>**Port** = 1433<br/>**SQL-Authentifizierung** = Deaktiviert<br/>**Speicherkonfiguration** = Allgemein<br/>**Automatisiertes Patchen** = Sonntags, 2:00 Uhr<br/>**Automatisierte Sicherung** = Deaktiviert</br>**Azure Key Vault-Integration** = Deaktiviert |**SQL-Konnektivität** = Privat (innerhalb von Virtual Network)<br/>**Port** = 1433<br/>**SQL-Authentifizierung** = Deaktiviert<br/>**Speicherkonfiguration** = Allgemein<br/>**Automatisiertes Patchen** = Sonntags, 2:00 Uhr<br/>**Automatisierte Sicherung** = Deaktiviert</br>**Azure Key Vault-Integration** = Deaktiviert |

<br/>

> [!NOTE]
> Die hier vorgeschlagenen VM-Größen sind für das Testen von Verfügbarkeitsgruppen in Azure Virtual Machines vorgesehen. Die beste Leistung für Produktionsarbeitslasten finden Sie unter den Empfehlungen für die Größe und Konfiguration der SQL Server-Computer in [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](performance-guidelines-best-practices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

Nachdem die drei virtuellen Computer vollständig bereitgestellt wurden, müssen Sie sie in die Domäne **corp.contoso.com** einbinden und „CORP\Install“ Administratorrechte für die Computer gewähren.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Einbinden der Server in die Domäne

Sie können die virtuellen Computer jetzt in **corp.contoso.com** einbinden. Führen Sie folgende Schritte für die SQL Server-VMs sowie die Dateifreigabe-Zeugenserver aus:

1. Stellen Sie mit **BUILTIN\DomainAdmin** eine Remoteverbindung mit dem virtuellen Computer her.
2. Wählen Sie im **Server-Manager** die Option **Lokaler Server**.
3. Wählen Sie den Link **WORKGROUP** aus.
4. Wählen Sie im Abschnitt **Computername** die Option **Ändern** aus.
5. Aktivieren Sie das Kontrollkästchen **Domäne**, und geben Sie in das Textfeld die Zeichenfolge **corp.contoso.com** ein. Klicken Sie auf **OK**.
6. Geben Sie im Popupdialogfeld **Windows-Sicherheit** die Anmeldeinformationen für das standardmäßige Domänenadministratorkonto (**CORP\DomainAdmin**) und das Kennwort (**Contoso!0000**) an.
7. Wenn die Meldung „Willkommen in der Domäne ‚corp.contoso.com‘“ angezeigt wird, wählen Sie **OK** aus.
8. Wählen Sie **Schließen** und dann im Popupdialogfeld **Jetzt neu starten** aus.

## <a name="add-accounts"></a>Hinzufügen von Konten

Fügen Sie das Installationskonto als Administrator auf allen virtuellen Computern hinzu, erteilen Sie innerhalb von SQL Server die Berechtigung für das Installationskonto und die lokalen Konten, und aktualisieren Sie das SQL Server-Dienstkonto. 

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Hinzufügen des Benutzers „Corp\Install“ als Administrator auf jedem virtuellen Clustercomputer

Fügen Sie nach dem Neustart aller virtuellen Computer als Mitglied der Domäne **CORP\Install** als Mitglied der lokalen Administratorengruppe hinzu.

1. Warten Sie, bis der virtuelle Computer neu gestartet wurde, und starten Sie die RDP-Datei erneut über den primären Domänencontroller, um sich bei **sqlserver-0** mit dem Konto **CORP\DomainAdmin** anzumelden.

   >[!TIP]
   >Stellen Sie sicher, dass Sie sich mit dem Domänenadministratorkonto anmelden. In den vorherigen Schritten haben Sie das BUILT IN-Administratorkonto verwendet. Nun, wo der Server sich in der Domäne befindet, verwenden Sie das Domänenkonto. Geben Sie in Ihrer RDP-Sitzung *DOMAIN*\\*Benutzername* an.
   >

2. Wählen Sie in **Server-Manager** die Option **Tools** und anschließend **Computerverwaltung** aus.
3. Erweitern Sie im Fenster **Computerverwaltung** die Option **Lokale Benutzer und Gruppen**, und wählen Sie dann **Gruppen** aus.
4. Doppelklicken Sie auf die Gruppe **Administratoren** .
5. Wählen Sie im Dialogfeld **Administratoreigenschaften** die Schaltfläche **Hinzufügen** aus.
6. Geben Sie den Benutzer **CORP\Install** ein, und wählen Sie dann **OK** aus.
7. Wählen Sie **OK** aus, um das Dialogfeld **Administratoreigenschaften** zu schließen.
8. Wiederholen Sie die oben genannten Schritte für **sqlserver-1** und **cluster-fsw**.


### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Erstellen einer Anmeldung für das Installationskonto auf jedem virtuellen SQL Server-Computer

Verwenden Sie das Installationskonto (CORP\install), um die Verfügbarkeitsgruppe zu konfigurieren. Dieses Konto muss auf jedem virtuellen SQL Server-Computer ein Mitglied der festen Serverrolle **sysadmin** sein. Mit folgenden Schritten erstellen Sie eine Anmeldung für das Installationskonto:

1. Stellen Sie über das RDP (Remote Desktop Protocol) und unter Verwendung des Kontos *\<MachineName\>\DomainAdmin* eine Verbindung mit dem Server her.

1. Öffnen Sie SQL Server Management Studio, und stellen Sie eine Verbindung mit der lokalen SQL Server-Instanz her.

1. Wählen Sie im **Objekt-Explorer** die Option **Sicherheit** aus.

1. Klicken Sie mit der rechten Maustaste auf **Anmeldungen**. Wählen Sie **Neue Anmeldung** aus.

1. Wählen Sie in **Anmeldung – Neu** die Option **Suche** aus.

1. Wählen Sie **Standorte** aus.

1. Geben Sie die Netzwerkanmeldeinformationen des Domänenadministrators ein.

1. Verwenden Sie das Installationskonto (CORP\install).

1. Legen Sie die Anmeldung als Mitglied der festen Serverrolle **sysadmin** fest.

1. Klicken Sie auf **OK**.

Wiederholen Sie die vorhergehenden Schritte für den anderen virtuellen SQL Server-Computer.

### <a name="configure-system-account-permissions"></a>Konfigurieren von Systemkontoberechtigungen

Um ein Kontos für das Systemkonto zu erstellen und entsprechende Berechtigungen zu gewähren, führen Sie die folgenden Schritte für jede SQL Server-Instanz durch:

1. Erstellen Sie für jede SQL Server-Instanz ein Konto für `[NT AUTHORITY\SYSTEM]`. Das folgende Skript erstellt dieses Konto:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Erteilen Sie `[NT AUTHORITY\SYSTEM]` die folgenden Berechtigungen für jede SQL Server-Instanz:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Das folgende Skript gewährt die folgenden Berechtigungen:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Festlegen der SQL Server-Dienstkonten

Legen Sie das SQL Server-Dienstkonto auf jedem virtuellen SQL Server-Computer fest. Verwenden Sie die Konten, die Sie beim Konfigurieren der Domänenkonten erstellt haben.

1. Öffnen Sie den **SQL Server-Konfigurations-Manager**.
2. Klicken Sie mit der rechten Maustaste auf den SQL Server-Dienst, und wählen Sie **Eigenschaften** aus.
3. Legen Sie Konto und Kennwort fest.
4. Wiederholen Sie diese Schritte auf dem anderen virtuellen SQL Server-Computer.  

Bei SQL Server-Verfügbarkeitsgruppen muss jeder virtuelle SQL Server-Computer als Domänenkonto ausgeführt werden.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Hinzufügen von Failovercluster-Features zu beiden virtuellen SQL Server-Computern

Um Failoverclusteringfeatures hinzuzufügen, führen Sie die folgenden Schritte auf beiden SQL Server-VMs aus:

1. Stellen Sie über RDP (Remote Desktop Protocol) und unter Verwendung des Kontos *CORP\install* eine Verbindung mit dem virtuellen Computer für SQL Server her. Öffnen Sie das **Server-Manager-Dashboard**.
2. Wählen Sie im Dashboard den Link **Rollen und Features hinzufügen** aus.

    ![Server Manager – Hinzufügen von Rollen](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

3. Wählen Sie **Weiter** aus, bis Sie zum Abschnitt **Serverfeatures** gelangen.
4. Wählen Sie in **Features** die Option **Failoverclustering**.
5. Fügen Sie zusätzliche erforderliche Features hinzu.
6. Wählen Sie **Installieren** aus, um die Features hinzuzufügen.

Wiederholen Sie diese Schritte auf dem anderen virtuellen SQL Server-Computer.

  >[!NOTE]
  > Dieser Schritt kann jetzt zusammen mit dem tatsächlichen Verknüpfen von SQL Server-VMs mit dem Failovercluster über die [Azure SQL-VM-Befehlszeilenschnittstelle](./availability-group-az-commandline-configure.md) und [Azure-Schnellstartvorlagen](availability-group-quickstart-template-configure.md) automatisiert werden.
  >

### <a name="tuning-failover-cluster-network-thresholds"></a>Optimieren von Failovercluster-Netzwerkschwellenwerten

Wenn Sie Windows-Failoverclusterknoten auf Azure-VMs mit SQL Server-Verfügbarkeitsgruppen ausführen, ändern Sie die Clustereinstellung in einen weniger strengen Überwachungsstatus.  Dadurch wird der Cluster deutlich stabiler und zuverlässiger.  Weitere Informationen hierzu finden Sie unter [IaaS mit SQL Server: Optimieren der Failovercluster-Netzwerkschwellenwerte](/windows-server/troubleshoot/iaas-sql-failover-cluster).


## <a name="configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"></a> Konfigurieren der Firewall auf jeder SQL Server-VM

Für die Lösung müssen die folgenden TCP-Ports in der Firewall geöffnet sein:

- **SQL Server-VM**: Port 1433 für eine Standardinstanz von SQL Server
- **Azure-Lastenausgleichstest**: Ein beliebiger verfügbarer Port. Für Beispiele wird häufig 59999 verwendet.
- **Datenbankspiegelungs-Endpunkt**: Ein beliebiger verfügbarer Port. Für Beispiele wird häufig 5022 verwendet.

Die Firewallports müssen auf beiden virtuellen SQL-Server-Computern geöffnet sein.

Die Methode zum Öffnen der Ports richtet sich nach der Firewalllösung, die Sie verwenden. Im nächsten Abschnitt wird erklärt, wie Sie die Ports in der Windows-Firewall öffnen. Öffnen Sie die erforderlichen Ports auf jedem virtuellen SQL Server-Computer.

### <a name="open-a-tcp-port-in-the-firewall"></a>Öffnen eines TCP-Ports in der Firewall

1. Starten Sie auf dem **Startbildschirm** der ersten SQL Server-Instanz die **Windows-Firewall mit erweiterter Sicherheit**.
2. Klicken Sie im linken Bereich auf **Eingehende Regeln**. Wählen Sie im rechten Bereich **Neue Regel** aus.
3. Wählen Sie Für **Regeltyp** die Option **Port**.
4. Geben Sie für den Port **TCP** an, und geben Sie die entsprechenden Portnummern ein. Sehen Sie sich folgendes Beispiel an:

   ![SQL-Firewall](./media/availability-group-manually-configure-prerequisites-tutorial-/35-tcpports.png)

5. Wählen Sie **Weiter** aus.
6. Lassen Sie auf der Seite **Aktion** die Option **Verbindung zulassen** aktiviert, und wählen Sie dann **Weiter** aus.
7. Akzeptieren Sie auf der Seite **Profil** die Standardeinstellungen, und wählen Sie dann **Weiter** aus.
8. Geben Sie auf der Seite **Name** im Textfeld **Name** einen Regelnamen an (Beispiel: **Azure-Lastenausgleichstest**), und wählen Sie dann **Fertig stellen** aus.

Wiederholen Sie diese Schritte auf dem zweiten virtuellen SQL Server-Computer.


## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer SQL Server Always On-Verfügbarkeitsgruppe auf Azure Virtual Machines](availability-group-manually-configure-tutorial.md)
