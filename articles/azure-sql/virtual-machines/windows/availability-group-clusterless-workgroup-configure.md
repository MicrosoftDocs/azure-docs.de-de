---
title: Konfigurieren einer domänenunabhängigen Verfügbarkeitsgruppe für eine Arbeitsgruppe
description: Erfahren Sie, wie Sie eine domänenunabhängige Active Directory-Always On-Verfügbarkeitsgruppe für eine Arbeitsgruppe auf einem virtuellen SQL-Server-Computer in Azure konfigurieren.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 0f194101720481f71434709c467d0e3130a0f1f9
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359454"
---
# <a name="configure-a-workgroup-availability-group"></a>Konfigurieren einer Verfügbarkeitsgruppe für eine Arbeitsgruppe 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel werden die erforderlichen Schritte zum Erstellen eines domänenunabhängigen Active Directory-Clusters mit einer Always On-Verfügbarkeitsgruppe erläutert. Dies wird auch als Arbeitsgruppencluster bezeichnet. Dieser Artikel konzentriert sich auf die relevanten Schritte für das Vorbereiten und Konfigurieren der Arbeitsgruppe und der Verfügbarkeitsgruppe. Schritte, die in anderen Artikeln behandelt werden, wie z. B. das Erstellen des Clusters oder das Bereitstellen der Verfügbarkeitsgruppe, werden hier übergangen. 


## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren einer Verfügbarkeitsgruppe für eine Arbeitsgruppe benötigen Sie Folgendes:
- Mindestens zwei virtuelle Computer mit Windows Server 2016 (oder höher), auf denen SQL Server 2016 (oder höher) ausgeführt wird und die mit statischen IP-Adressen in derselben Verfügbarkeitsgruppe oder unterschiedlichen Verfügbarkeitszonen bereitgestellt werden. 
- Ein lokales Netzwerk mit mindestens vier freien IP-Adressen im Subnetz. 
- Ein Konto auf jedem Computer in der Administratorengruppe, das auch über Systemadministratorrechte in SQL Server verfügt. 
- Offene Ports: TCP 1433, TCP 5022, TCP 59999. 

In diesem Artikel werden die folgenden Parameter verwendet, die jedoch nach Bedarf geändert werden können: 

| **Name** | **Parameter** |
| :------ | :---------------------------------- |
| **Node1**   | AGNode1 (10.0.0.4) |
| **Node2**   | AGNode2 (10.0.0.5) |
| **Clustername** | AGWGAG (10.0.0.6) |
| **Listener** | AGListener (10.0.0.7) | 
| **DNS-Suffix** | ag.wgcluster.example.com | 
| **Name der Arbeitsgruppe** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>Festlegen eines DNS-Suffix 

In diesem Schritt konfigurieren Sie das DNS-Suffix für beide Server. Beispiel: `ag.wgcluster.example.com`. Auf diese Weise können Sie den Namen des Objekts, mit dem Sie eine Verbindung herstellen möchten, als vollqualifizierte Adresse in Ihrem Netzwerk verwenden, z. B. `AGNode1.ag.wgcluster.example.com`. 

Führen Sie zum Konfigurieren des DNS-Suffix diese Schritte aus:

1. Stellen Sie per RDP eine Verbindung mit Ihrem ersten Knoten her, und öffnen Sie Server-Manager. 
1. Wählen Sie **Lokaler Server** und dann unter **Computername** den Namen des virtuellen Computers aus. 
1. Wählen Sie unter **Klicken Sie auf „Ändern“, um den Computer umzubenennen** die Option **Ändern** aus. 
1. Ändern Sie den Namen der Arbeitsgruppe in einen aussagekräftigen Namen, z. B. `AGWORKGROUP`: 

   ![Ändern des Arbeitsgruppennamens](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Wählen Sie **Mehr** aus, um das Dialogfeld **DNS-Suffix und NetBIOS-Computername** zu öffnen. 
1. Geben Sie unter **Primäres DNS-Suffix des Computers** den Namen des DNS-Suffixes ein, z. B. `ag.wgcluster.example.com`, und wählen Sie dann **OK** aus: 

   ![Der Screenshot zeigt das Dialogfeld „DNS-Suffix und NetBIOS-Computername“, in dem Sie den Wert eingeben können.](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Vergewissern Sie sich, dass unter **Vollständiger Computername** nun das DNS-Suffix angezeigt wird, und wählen Sie dann **OK** aus, um die Änderungen zu speichern: 

   ![Der Screenshot zeigt, wo Ihr vollständiger Computername angezeigt wird.](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Starten Sie den Server neu, wenn Sie dazu aufgefordert werden. 
1. Wiederholen Sie diese Schritte für alle anderen Knoten, die für die Verfügbarkeitsgruppe verwendet werden sollen. 

## <a name="edit-a-host-file"></a>Bearbeiten einer Hostdatei

Da kein Active Directory vorhanden ist, gibt es keine Möglichkeit, Windows-Verbindungen zu authentifizieren. Weisen Sie daher eine Vertrauensstellung zu, indem Sie die Hostdatei mit einem Text-Editor bearbeiten. 

Gehen Sie folgendermaßen vor, um die Hostdatei zu bearbeiten:

1. Melden Sie sich per RDP beim virtuellen Computer an. 
1. Wechseln Sie im **Datei-Explorer** zu `c:\windows\system32\drivers\etc`. 
1. Klicken Sie mit der rechten Maustaste auf die Datei **hosts**, und öffnen Sie die Datei mit **Windows-Editor** (oder einem anderen Text-Editor).
1. Fügen Sie am Ende der Datei einen Eintrag für jeden Knoten, die Verfügbarkeitsgruppe und den Listener in der Form `IP Address, DNS Suffix #comment` wie folgt hinzu: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Hinzufügen von Einträgen für IP-Adresse, Cluster und Listener in der Hostdatei](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Festlegen von Berechtigungen

Da kein Active Directory zum Verwalten von Berechtigungen vorhanden ist, müssen Sie manuell einem lokalen Administratorkonto genehmigen, den Cluster zu erstellen. 

Führen Sie dazu das folgende PowerShell-Cmdlet in einer administrativen PowerShell-Sitzung auf jedem Knoten aus: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Erstellen des Failoverclusters

In diesem Schritt erstellen Sie den Failovercluster. Wenn Sie mit diesen Schritten nicht vertraut sind, können Sie sich im [Tutorial für Failovercluster](failover-cluster-instance-storage-spaces-direct-manually-configure.md) darüber informieren.

Wichtige Unterschiede zwischen dem Tutorial und den Aktionen, die für einen Arbeitsgruppencluster durchgeführt werden sollten:
- Deaktivieren Sie beim Ausführen der Clusterüberprüfung **Speicher** und **Direkte Speicherplätze**. 
- Fügen Sie beim Hinzufügen der Knoten zum Cluster den vollqualifizierten Namen hinzu, z. B.:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Deaktivieren Sie **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden**. 

Weisen Sie nach dem Erstellen des Clusters eine statische Cluster-IP-Adresse zu. Gehen Sie dazu folgendermaßen vor:

1. Öffnen Sie in einem der Knoten **Failovercluster-Manager**, wählen Sie den Cluster aus, und klicken Sie unter **Hauptressourcen des Clusters** mit der rechten Maustaste auf **Name: \<ClusterNam>** . Wählen Sie anschließend **Eigenschaften** aus. 

   ![Öffnen von „Eigenschaften“ für den Clusternamen](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. Wählen Sie unter **IP-Adressen** die IP-Adresse aus, und wählen Sie dann **Bearbeiten** aus. 
1. Wählen Sie **Statische verwenden** aus, geben Sie die IP-Adresse des Clusters an, und wählen Sie dann **OK** aus: 

   ![Angeben einer statischen IP-Adresse für den Cluster](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Vergewissern Sie sich, dass Ihre Einstellungen korrekt aussehen, und wählen Sie dann **OK** aus, um sie zu speichern:

   ![Überprüfen der Clustereigenschaften](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Erstellen eines Cloudzeugen 

In diesem Schritt konfigurieren Sie einen Cloudfreigabezeugen. Wenn Sie mit den Schritten nicht vertraut sind, finden Sie weitere Informationen unter [Bereitstellen eines Cloudzeugen für einen Failovercluster](/windows-server/failover-clustering/deploy-cloud-witness). 

## <a name="enable-the-availability-group-feature"></a>Aktivieren des Verfügbarkeitsgruppenfeatures 

In diesem Schritt aktivieren Sie das Feature für Verfügbarkeitsgruppen. Wenn Sie mit den Schritten nicht vertraut sind, finden Sie weitere Informationen im [Tutorial zu Verfügbarkeitsgruppen](availability-group-manually-configure-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificates"></a>Erstellen von Schlüsseln und Zertifikaten

In diesem Schritt erstellen Sie Zertifikate, die in einer SQL-Anmeldung beim verschlüsselten Endpunkt verwendet werden. Erstellen Sie auf jedem Knoten einen Ordner, in dem die Zertifikatsicherungen gespeichert werden sollen, z. B. `c:\certs`. 

Führen Sie zum Konfigurieren des ersten Knotens folgende Schritte durch: 

1. Öffnen Sie **SQL Server Management Studio**, und stellen Sie eine Verbindung mit Ihrem ersten Knoten her, z. B. `AGNode1`. 
1. Öffnen Sie ein **neues Abfragefenster**, und führen Sie die folgende T-SQL-Anweisung (Transact-SQL) aus, nachdem Sie das Kennwort komplexer und sicherer gestaltet haben:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Erstellen Sie als Nächstes den HADR-Endpunkt, und verwenden Sie das Zertifikat für die Authentifizierung, indem Sie diese T-SQL-Anweisung (Transact-SQL) ausführen:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Verwenden Sie den **Datei-Explorer**, um zu dem Dateispeicherort zu wechseln, an dem sich das Zertifikat befindet, z. B. `c:\certs`. 
1. Erstellen Sie manuell eine Kopie des Zertifikats (z. B. `AGNode1Cert.crt`) für den ersten Knoten, und übertragen Sie es an den gleichen Speicherort auf dem zweiten Knoten. 

Führen Sie zum Konfigurieren des zweiten Knotens folgende Schritte durch: 

1. Stellen Sie mit **SQL Server Management Studio** eine Verbindung mit dem zweiten Knoten her, z. B. `AGNode2`. 
1. Führen Sie in einem **neuen Abfragefenster** die folgende T-SQL-Anweisung (Transact-SQL) aus, nachdem Sie das Kennwort komplexer und sicherer gestaltet haben: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Erstellen Sie als Nächstes den HADR-Endpunkt, und verwenden Sie das Zertifikat für die Authentifizierung, indem Sie diese T-SQL-Anweisung (Transact-SQL) ausführen:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Verwenden Sie den **Datei-Explorer**, um zu dem Dateispeicherort zu wechseln, an dem sich das Zertifikat befindet, z. B. `c:\certs`. 
1. Erstellen Sie manuell eine Kopie des Zertifikats (z. B. `AGNode2Cert.crt`) für den zweiten Knoten, und übertragen Sie es an den gleichen Speicherort auf dem ersten Knoten. 

Wenn weitere Knoten im Cluster vorhanden sind, wiederholen Sie diese Schritte auch dort, und ändern Sie dabei die jeweiligen Zertifikatnamen. 

## <a name="create-logins"></a>Erstellen von Anmeldungen

Die Zertifikatauthentifizierung wird verwendet, um Daten knotenübergreifend zu synchronisieren. Um dies zuzulassen, erstellen Sie einen Anmeldenamen für den anderen Knoten, einen Benutzer für die Anmeldung und ein Zertifikat zur Verwendung des gesicherten Zertifikats für die Anmeldung. Erteilen Sie dann die Berechtigung zur Verbindung auf dem Spiegelungsendpunkt. 

Führen Sie dazu zuerst die folgende T-SQL-Abfrage (Transact-SQL) auf dem ersten Knoten aus, z. B. `AGNode1`: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Führen Sie als Nächstes die folgende T-SQL-Abfrage (Transact-SQL) auf dem zweiten Knoten aus, z. B. `AGNode2`: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Wenn weitere Knoten im Cluster vorhanden sind, wiederholen Sie diese Schritte auch dort, und ändern Sie dabei die jeweiligen Zertifikat- und Benutzernamen. 

## <a name="configure-an-availability-group"></a>Konfigurieren einer Verfügbarkeitsgruppe

In diesem Schritt konfigurieren Sie die Verfügbarkeitsgruppe und fügen ihr Ihre Datenbanken hinzu. Erstellen Sie zu diesem Zeitpunkt keinen Listener. Wenn Sie mit den Schritten nicht vertraut sind, finden Sie weitere Informationen im [Tutorial zu Verfügbarkeitsgruppen](availability-group-manually-configure-tutorial.md#create-the-availability-group). Initiieren Sie unbedingt ein Failover und ein Failback, um sicherzustellen, dass alles ordnungsgemäß funktioniert. 

   > [!NOTE]
   > Wenn während des Synchronisierungsvorgangs ein Fehler auftritt, müssen Sie möglicherweise `NT AUTHORITY\SYSTEM` vorübergehend Systemadmininistratorberechtigungen erteilen, um Clusterressourcen auf dem ersten Knoten zu erstellen, z. B. `AGNode1`. 

## <a name="configure-a-load-balancer"></a>Konfigurieren eines Lastenausgleichs

In diesem letzten Schritt konfigurieren Sie den Lastenausgleich im [Azure-Portal](availability-group-load-balancer-portal-configure.md) oder mit [PowerShell](availability-group-listener-powershell-configure.md).


## <a name="next-steps"></a>Nächste Schritte

Sie können auch die [Azure-Befehlszeilenschnittstelle auf der SQL-VM](./availability-group-az-commandline-configure.md) verwenden, um eine Verfügbarkeitsgruppe zu konfigurieren.