---
title: 'Tutorial: Herstellen einer Verbindung mit einem Azure Cosmos-Konto mithilfe eines privaten Endpunkts in Azure'
titleSuffix: Azure Private Link
description: Beginnen Sie mit diesem Tutorial mit der Verwendung eines privaten Endpunkts in Azure, um sich privat mit einem Azure Cosmos-Konto zu verbinden.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 3a7e75641f6bb84b490231fcd06e04c3cbad06d3
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063466"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>Tutorial: Herstellen einer Verbindung mit einem Azure Cosmos-Konto mithilfe eines privaten Endpunkts in Azure

Der private Azure-Endpunkt ist der grundlegende Baustein für Private Link in Azure. Mit ihm können Azure-Ressourcen wie virtuelle Computer (VMs) privat mit Private Link-Ressourcen kommunizieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts.
> * Erstellen Sie eine VM.
> * Erstellen eines Cosmos DB-Kontos mit einem privaten Endpunkt
> * Testen der Verbindung mit dem privaten Endpunkt des Cosmos DB-Kontos

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-virtual-network-and-bastion-host"></a>Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk, ein Subnetz und einen Bastion-Host. 

Der Bastion-Host wird verwendet, um eine sichere Verbindung mit dem virtuellen Computer herzustellen, um den privaten Endpunkt zu testen.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Virtuelles Netzwerk** aus, oder suchen Sie über das Suchfeld nach **Virtuelles Netzwerk**.

2. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **myResourceGroup** aus. |
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
    | Ressourcengruppe | Wählen Sie **myResourceGroup** aus. |
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

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>Erstellen eines Cosmos DB-Kontos mit einem privaten Endpunkt

In diesem Abschnitt erstellen Sie ein Cosmos DB-Konto und konfigurieren den privaten Endpunkt.

1. Wählen Sie im linken Menü **Ressource erstellen** > **Datenbanken** > **Cosmos DB-Konto** aus, oder suchen Sie über das Suchfeld nach **Cosmos DB-Konto**.

2. Geben Sie auf der Registerkarte **Grundlagen** der Seite **Cosmos DB-Konto erstellen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Wählen Sie Ihr Azure-Abonnement. |
    | Ressourcengruppe | Wählen Sie **myResourceGroup** aus. |
    | **Instanzendetails** |  |
    | Kontoname | Geben Sie **mycosmosdb** ein. Wenn der Name nicht verfügbar ist, geben Sie einen eindeutigen Namen ein. |
    | API | Wählen Sie **Core (SQL)** aus. |
    | Standort | Wählen Sie **USA, Osten** aus. |
    | Kapazitätsmodus | Übernehmen Sie den Standardwert für **Bereitgestellter Durchsatz**. |
    | Tarifspezifischen Rabatt für den Free-Tarif anwenden | Übernehmen Sie die Standardeinstellung **Nicht anwenden**. |
    | Georedundanz | Übernehmen Sie die Standardeinstellung **Deaktivieren**. |
    | Schreibvorgänge in mehreren Regionen | Übernehmen Sie die Standardeinstellung **Deaktivieren**. |
   
3. Wählen Sie die Registerkarte **Netzwerk** oder die Schaltfläche **Weiter: Netzwerk**.

4. Geben Sie auf der Registerkarte **Netzwerk** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerkverbindungen** | |
    | Konnektivitätsmethode | Wählen Sie **Privater Endpunkt** aus. |
    | **Firewall konfigurieren** | |
    | Zugriff über das Azure-Portal zulassen | Übernehmen Sie den Standardwert **Zulassen**. |
    | Zugriff über meine IP-Adresse zulassen | Übernehmen Sie den Standardwert **Verweigern**. |

5. Wählen Sie unter **Privater Endpunkt** die Option **+ Hinzufügen** aus.

6. Geben Sie unter **Privaten Endpunkt erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **myResourceGroup** aus. |
    | Standort | Wählen Sie **USA, Osten** aus. |
    | Name | Geben Sie **myPrivateEndpoint** ein. |
    | Unterressource des Ziels | Übernehmen Sie den Standardwert **Core (SQL)** |
    | **Netzwerk** |  |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **mySubnet** aus. |
    | **Private DNS-Integration** |
    | Integration in eine private DNS-Zone | Übernehmen Sie den Standardwert **Ja**. |
    | Private DNS-Zone | Übernehmen Sie den Standardwert „(Neu) privatelink.documents.azure.com“. |

7. Klicken Sie auf **OK**.

8. Klicken Sie auf **Überprüfen + erstellen**.

9. Klicken Sie auf **Erstellen**.

### <a name="add-a-database-and-a-container"></a>Hinzufügen einer Datenbank und eines Containers

1. Wählen Sie **Zu Ressource wechseln** bzw. im linken Menü des Azure-Portals **Alle Ressourcen** > **mycosmosdb** aus.

2. Wählen Sie im Menü auf der linken Seite **Daten-Explorer** aus.

3. Wählen Sie im Fenster **Daten-Explorer** die Option **Neuer Container** aus.

4. Geben Sie unter **Container hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Datenbank-ID | Übernehmen Sie die Standardeinstellung **Neu erstellen**. </br> Geben Sie in das Textfeld **mydatabaseid** ein. |
    | Durchsatz (400 bis 100.000 RU/s) | Übernehmen Sie den Standardwert **Manuell**. </br> Geben Sie in das Textfeld **400** ein. |
    | Container-ID | Geben Sie **mycontainerid** ein. |
    | Partitionsschlüssel | Geben Sie **/mykey** ein. |

5. Klicken Sie auf **OK**.

6. Wählen Sie im Abschnitt **Einstellungen** des CosmosDB-Kontos die Option **Schlüssel** aus.

7. Wählen Sie „Kopieren“ für **PRIMÄRE VERBINDUNGSZEICHENFOLGE** aus.

## <a name="test-connectivity-to-private-endpoint"></a>Testen der Verbindung mit dem privaten Endpunkt

In diesem Abschnitt verwenden Sie den virtuellen Computer, den Sie im vorherigen Schritt erstellt haben, um über den privaten Endpunkt eine Verbindung mit dem Cosmos DB-Konto herzustellen.

1. Wählen Sie **Ressourcengruppen** im linken Navigationsbereich aus.

1. Wählen Sie **myResourceGroup** aus.

1. Wählen Sie **myVM** aus.

1. Wählen Sie auf der Seite „Übersicht“ für **myVM** die Option **Verbinden** und dann **Bastion** aus.

1. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

1. Geben Sie Benutzernamen und Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben.

1. Öffnen Sie Windows PowerShell auf dem Server, nachdem Sie eine Verbindung hergestellt haben.

1. Geben Sie `nslookup <cosmosdb-account-name>.documents.azure.com` ein, und überprüfen Sie die Namensauflösung. Ersetzen Sie **\<cosmosdb-account-name>** durch den Namen des Cosmos DB-Kontos, das Sie in den vorherigen Schritten erstellt haben. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```
    Als Name des Cosmos DB-Kontos wird die private IP-Adresse **10.1.0.5** zurückgegeben.  Diese Adresse befindet sich in dem Subnetz des virtuellen Netzwerks, das Sie zuvor erstellt haben.
    
1. Rufen Sie die primäre Azure Cosmos DB-Verbindungszeichenfolge über das Portal ab. Eine gültige Verbindungszeichenfolge hat das folgende Format:
   
   Für SQL-API-Konten: `https://<accountName>.documents.azure.com:443/;AccountKey=<accountKey>;` Für Azure Cosmos DB-API für MongoDB: `mongodb://<accountName>:<accountKey>@cdbmongo36.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false`

1. Installieren Sie die [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) auf dem virtuellen Computer.

1. Nachdem **Microsoft Azure Storage-Explorer** installiert wurde, wählen Sie **Fertigstellen** aus.  Lassen Sie das Kontrollkästchen aktiviert, um die Anwendung zu öffnen.

1. Wählen Sie auf dem Bildschirm **Verbindung mit Azure Storage herstellen** die Option **Abbrechen** aus.

1. Klicken Sie im Storage-Explorer mit der rechten Maustaste auf **Cosmos DB-Konten**, und wählen Sie **Mit Cosmos DB verbinden** aus.

1. Übernehmen Sie unter **API auswählen** die Standardeinstellung **SQL**.

1. Fügen Sie im Feld unter **Verbindungszeichenfolge** die in den vorherigen Schritten kopierte Verbindungszeichenfolge aus dem Cosmos DB-Konto ein.

1. Klicken Sie auf **Weiter**.

1. Überprüfen Sie, ob die Einstellungen in **Verbindungszusammenfassung** korrekt sind.  

1. Wählen Sie **Verbinden** aus.

1. Trennen Sie die Verbindung zu **myVM**.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie das virtuelle Netzwerk, den virtuellen Computer und das Cosmos DB-Konto wie folgt:

1. Wählen Sie im linken Menü die Option **Ressourcengruppen** aus.

2. Wählen Sie **myResourceGroup** aus.

3. Wählen Sie die Option **Ressourcengruppe löschen**.

4. Geben Sie unter **RESSOURCENGRUPPENNAMEN EINGEBEN** den Namen **myResourceGroup** ein.

5. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes erstellt:

* virtuelles Netzwerk und Bastionhost
* virtueller Computer
* Cosmos DB-Konto

Erfahren Sie mehr über den Private Link-Dienst:
> [!div class="nextstepaction"]
> [Erstellen eines Private Link-Diensts](create-private-link-service-portal.md)
