---
title: 'Tutorial: Herstellen einer Verbindung mit einem Azure SQL-Server über einen privaten Azure-Endpunkt – Portal'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe des Azure-Portals einen Azure SQL-Server mit einem privaten Endpunkt erstellen.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: a7eca29dc9390306ac6ad4e66eec75a25c2d33ff
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522157"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Tutorial: Herstellen einer Verbindung mit einem Azure SQL-Server über einen privaten Azure-Endpunkt – Azure-Portal

Der private Azure-Endpunkt ist der grundlegende Baustein für Private Link in Azure. Mit ihm können Azure-Ressourcen wie virtuelle Computer (VMs) privat mit Private Link-Ressourcen kommunizieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts.
> * Erstellen Sie eine VM.
> * Erstellen eines Azure SQL-Servers und eines privaten Endpunkts
> * Testen der Konnektivität mit dem privaten Endpunkt für SQL Server

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.


## <a name="create-a-virtual-network-and-bastion-host"></a>Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk, ein Subnetz und einen Bastion-Host. 

Der Bastion-Host wird verwendet, um eine sichere Verbindung mit dem virtuellen Computer herzustellen, um den privaten Endpunkt zu testen.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Virtuelles Netzwerk** aus, oder suchen Sie über das Suchfeld nach **Virtuelles Netzwerk**.

2. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **CreateSQLEndpointTutorial-rg** aus. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **USA, Osten** aus. |

3. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

4. Geben Sie auf der Registerkarte **IP-Adressen** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | IPv4-Adressraum | Geben Sie **10.1.0.0/16** ein. |

5. Wählen Sie unter **Subnetzname** das Wort **Standard** aus.

6. Geben Sie unter **Subnetz bearbeiten** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Subnetzname | Geben Sie **mySubnet** ein. |
    | Subnetzadressbereich | Geben Sie **10.1.0.0/24** ein. |

7. Wählen Sie **Speichern** aus.

8. Wählen Sie die Registerkarte **Sicherheit** .

9. Wählen Sie unter **BastionHost** die Option **Aktivieren** aus. Geben Sie die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Bastion-Name | Geben Sie **myBastionHost** ein. |
    | AzureBastionSubnet-Adressraum | Geben Sie **10.1.1.0/24** ein. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie **myBastionIP** als **Name** ein. </br> Klicken Sie auf **OK**. |


8. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

9. Klicken Sie auf **Erstellen**.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

In diesem Abschnitt erstellen Sie einen virtuellen Computer zum Testen des privaten Endpunkts.

1. Wählen Sie oben links im Portal die Option **Ressource erstellen** > **Compute** > **Virtueller Computer** aus, oder suchen Sie über das Suchfeld nach **Virtueller Computer**.
   
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **CreateSQLEndpointTutorial** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **USA, Osten** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter – Gen1** aus. |
    | Azure Spot-Instanz | Wählen Sie **Nein** aus. |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | **myVNet** |
    | Subnet | **mySubnet** |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | **Grundlegend**|
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |
   
5. Klicken Sie auf **Überprüfen + erstellen**. 
  
6. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Erstellen eines Azure SQL-Servers und eines privaten Endpunkts

In diesem Abschnitt erstellen Sie einen SQL-Server in Azure. 

1. Wählen Sie oben links auf dem Bildschirm im Azure-Portal die Option **Ressource erstellen** > **Datenbanken** > **SQL-Datenbank** aus.

1. Geben Sie unter **SQL-Datenbank erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **CreateSQLEndpointTutorial** aus. Diese Ressourcengruppe wurde im vorherigen Abschnitt erstellt.|
    | **Datenbankdetails** |  |
    | Datenbankname  | Geben Sie **mysqldatabase** ein. Wenn dieser Name vergeben ist, erstellen Sie einen eindeutigen Namen. |
    | Server | Wählen Sie **Neu erstellen**. |

6. Geben Sie bei **Neuer Server** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Servername  | Geben Sie **mysqlserver** ein. Wenn dieser Name vergeben ist, erstellen Sie einen eindeutigen Namen.|
    | Serveradministratoranmeldung | Geben Sie einen Administratornamen Ihrer Wahl ein. |
    | Kennwort | Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens acht Zeichen lang sein und die festgelegten Anforderungen erfüllen. |
    | Standort | Wählen Sie **USA, Osten** aus. |
    
7. Klicken Sie auf **OK**.

8. Wählen Sie die Registerkarte **Netzwerk** oder die Schaltfläche **Weiter: Netzwerk**.

9. Geben Sie auf der Registerkarte **Netzwerk** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerkverbindungen** | |
    | Konnektivitätsmethode | Wählen Sie **Privater Endpunkt** aus. |
   
10. Wählen Sie unter **Private Endpunkte** die Option **+ Privaten Endpunkt hinzufügen** aus.

11. Geben Sie unter **Privaten Endpunkt erstellen** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **CreateSQLEndpointTutorial** aus. |
    | Standort | Wählen Sie **USA, Osten** aus. |
    | Name | Geben Sie **myPrivateSQLendpoint** ein. |
    | Zielunterressource | Wählen Sie **SQLServer** aus. |
    | **Netzwerk** |  |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **mySubnet** aus. |
    | **Private DNS-Integration** | |
    | Integration in eine private DNS-Zone | Übernehmen Sie den Standardwert **Ja**. |
    | Private DNS-Zone | Behalten Sie die Standardeinstellung **(Neu) privatelink.database.windows.net** bei. |

12. Klicken Sie auf **OK**. 

13. Klicken Sie auf **Überprüfen + erstellen**.

14. Klicken Sie auf **Erstellen**.

## <a name="test-connectivity-to-private-endpoint"></a>Testen der Verbindung mit dem privaten Endpunkt

In diesem Abschnitt verwenden Sie den virtuellen Computer, den Sie im vorherigen Schritt erstellt haben, um über den privaten Endpunkt eine Verbindung mit dem SQL-Server herzustellen.

1. Wählen Sie **Ressourcengruppen** im linken Navigationsbereich aus.

2. Wählen Sie **CreateSQLEndpointTutorial** aus.

3. Wählen Sie **myVM** aus.

4. Wählen Sie auf der Seite „Übersicht“ für **myVM** die Option **Verbinden** und dann **Bastion** aus.

5. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

6. Geben Sie Benutzernamen und Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben.

7. Öffnen Sie Windows PowerShell auf dem Server, nachdem Sie eine Verbindung hergestellt haben.

8. Geben Sie `nslookup <sqlserver-name>.database.windows.net` ein. Ersetzen Sie **\<sqlserver-name>** durch den Namen des SQL-Servers, den Sie in den vorherigen Schritten erstellt haben.  Sie erhalten eine Meldung ähnlich der folgenden:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Als Name für den SQL-Server wird die private IP-Adresse **10.1.0.5** zurückgegeben.  Diese Adresse befindet sich in dem Subnetz des virtuellen Netzwerks, das Sie zuvor erstellt haben.


9. Installieren Sie [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) auf **myVM**.

10. Öffnen Sie **SQL Server Management Studio**.

4. Geben Sie unter **Mit Server verbinden** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Servertyp | Wählen Sie **Datenbank-Engine** aus.|
    | Servername | Geben Sie **\<sqlserver-name>.database.windows.net** ein. |
    | Authentifizierung | Wählen Sie **SQL Server-Authentifizierung** aus. |
    | Benutzername | Geben Sie den Benutzernamen ein, den Sie bei der Erstellung des Servers eingegeben haben. |
    | Kennwort | Geben Sie das Kennwort ein, das Sie bei der Erstellung des Servers eingegeben haben. |
    | Kennwort speichern | Wählen Sie **Ja** aus. |

1. Wählen Sie **Verbinden**.
2. Durchsuchen Sie Datenbanken im linken Menü.
3. (Optional) Erstellen Sie die Informationen unter **mysqldatabase**, bzw. rufen Sie sie ab.
4. Schließen Sie die Remotedesktopverbindung mit **myVM**. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 
Wenn Sie Ihre Arbeit mit dem privaten Endpunkt, dem SQL-Server und dem virtuellen Computer abgeschlossen haben, löschen Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen: 
1. Geben Sie oben im Portal im Feld **Suche** den Suchbegriff **CreateSQLEndpointTutorial** ein, und wählen Sie in den Suchergebnissen dann **CreateSQLEndpointTutorial** aus. 
2. Klicken Sie auf **Ressourcengruppe löschen**. 
3. Geben Sie unter **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN** den Namen „CreateSQLEndpointTutorial“ ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes erstellt:

* virtuelles Netzwerk und Bastionhost
* Virtuellen Computer
* Azure SQL-Server mit privatem Endpunkt

Sie haben den virtuellen Computer verwendet, um die Konnektivität mit dem SQL-Server über den privaten Endpunkt auf sichere Weise zu testen.

Erfahren Sie mehr über den Private Link-Dienst:
> [!div class="nextstepaction"]
> [Erstellen eines Private Link-Diensts](create-private-link-service-portal.md)