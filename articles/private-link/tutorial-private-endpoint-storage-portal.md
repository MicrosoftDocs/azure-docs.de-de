---
title: 'Tutorial: Herstellen einer Verbindung mit einem Speicherkonto mithilfe eines privaten Endpunkts in Azure'
titleSuffix: Azure Private Link
description: Beginnen Sie mit diesem Tutorial mit der Verwendung eines privaten Endpunkts in Azure, um sich privat mit einem Speicherkonto zu verbinden.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: eefef0eb61df9aa0597206b537f3d58e8b8b3213
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484820"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>Tutorial: Herstellen einer Verbindung mit einem Speicherkonto mithilfe eines privaten Endpunkts in Azure

Der private Azure-Endpunkt ist der grundlegende Baustein für Private Link in Azure. Mit ihm können Azure-Ressourcen wie virtuelle Computer (VMs) privat mit Private Link-Ressourcen kommunizieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts.
> * Erstellen Sie eine VM.
> * Erstellen eines Speicherkontos mit einem privaten Endpunkt.
> * Testen der Verbindung mit dem privaten Endpunkt des Speicherkontos.

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

## <a name="create-storage-account-with-a-private-endpoint"></a>Erstellen eines Speicherkontos mit einem privaten Endpunkt

In diesem Abschnitt erstellen Sie ein Speicherkonto und konfigurieren den privaten Endpunkt.

1. Wählen Sie im linken Menü **Ressource erstellen** > **Speicher** > **Speicherkonto** aus, oder suchen Sie über das Suchfeld nach **Speicherkonto**.

2. Geben Sie auf der Registerkarte **Grundlagen** der Seite **Speicherkonto erstellen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **myResourceGroup** aus. |
    | **Instanzendetails** |  |
    | Speicherkontoname | Geben Sie **mystorageaccount** ein. Wenn der Name nicht verfügbar ist, geben Sie einen eindeutigen Namen ein. |
    | Standort | Wählen Sie **USA, Osten** aus. |
    | Leistung | Übernehmen Sie den Standardwert **Standard**. |
    | Kontoart | Übernehmen Sie den Standardwert **Storage (universell, Version 2)** . |
    | Replikation| Übernehmen Sie den Standardwert **Georedundanter Speicher mit Lesezugriff (RA-GRS)** . |
   
3. Wählen Sie die Registerkarte **Netzwerk** oder die Schaltfläche **Weiter: Netzwerk**.

4. Wählen Sie auf der Registerkarte **Netzwerk** unter **Konnektivitätsmethode** die Option **Privater Endpunkt** aus.

5. Wählen Sie unter **Privater Endpunkt** die Option **+ Hinzufügen** aus.

6. Geben Sie unter **Privaten Endpunkt erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **myResourceGroup** aus. |
    | Standort | Wählen Sie **USA, Osten** aus. |
    | Name | Geben Sie **myPrivateEndpoint** ein. |
    | Speicherunterressource | Übernehmen Sie den Standardwert **Blob**. |
    | **Netzwerk** |  |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **mySubnet** aus. |
    | **Private DNS-Integration** |
    | Integration in eine private DNS-Zone | Übernehmen Sie den Standardwert **Ja**. |
    | Private DNS-Zone | Übernehmen Sie den Standardwert „(Neu) privatelink.blob.core.windows.net“. |

7. Klicken Sie auf **OK**.

8. Klicken Sie auf **Überprüfen + erstellen**.

9. Klicken Sie auf **Erstellen**.

10. Wählen Sie **Ressourcengruppen** im linken Navigationsbereich aus.

11. Wählen Sie **myResourceGroup** aus.

12. Wählen Sie das Speicherkonto aus, das Sie im vorherigen Schritt erstellt haben.

13. Wählen Sie im Abschnitt **Einstellungen** des Speicherkontos die Option **Zugriffsschlüssel** aus.

14. Wählen Sie „Kopieren“ für die **Verbindungszeichenfolge** für **key1** aus.

## <a name="test-connectivity-to-private-endpoint"></a>Testen der Verbindung mit dem privaten Endpunkt

In diesem Abschnitt verwenden Sie den virtuellen Computer, den Sie im vorherigen Schritt erstellt haben, um über den privaten Endpunkt eine Verbindung mit dem Speicherkonto herzustellen.

1. Wählen Sie **Ressourcengruppen** im linken Navigationsbereich aus.

2. Wählen Sie **myResourceGroup** aus.

3. Wählen Sie **myVM** aus.

4. Wählen Sie auf der Seite „Übersicht“ für **myVM** die Option **Verbinden** und dann **Bastion** aus.

5. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

6. Geben Sie Benutzernamen und Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben.

7. Öffnen Sie Windows PowerShell auf dem Server, nachdem Sie eine Verbindung hergestellt haben.

8. Geben Sie `nslookup <storage-account-name>.blob.core.windows.net` ein. Ersetzen Sie **\<storage-account-name>** durch den Namen des Speicherkontos, das Sie in den vorherigen Schritten erstellt haben.  Sie erhalten eine Meldung ähnlich der folgenden:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    Als Name des Speicherkontos wird eine private IP-Adresse **10.1.0.5** zurückgegeben.  Diese Adresse befindet sich in dem Subnetz des virtuellen Netzwerks, das Sie zuvor erstellt haben.

9. Installieren Sie die [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) auf dem virtuellen Computer.

10. Nachdem **Microsoft Azure Storage-Explorer** installiert wurde, wählen Sie **Fertigstellen** aus.  Lassen Sie das Kontrollkästchen aktiviert, um die Anwendung zu öffnen.

11. Wählen Sie auf dem Bildschirm **Verbindung mit Azure Storage herstellen** die Option **Verbindungszeichenfolge verwenden** aus.

12. Klicken Sie auf **Weiter**.

13. Geben Sie den Namen Ihres Speicherkontos aus den vorherigen Schritten in **Anzeigename** ein.

14. Fügen Sie im Feld unter **Verbindungszeichenfolge** die in den vorherigen Schritten kopierte Verbindungszeichenfolge aus dem Speicherkonto ein.

15. Klicken Sie auf **Weiter**.

16. Überprüfen Sie, ob die Einstellungen in **Verbindungszusammenfassung** korrekt sind.  

17. Wählen Sie **Verbinden** aus.

18. Trennen Sie die Verbindung zu **myVM**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die das virtuelle Netzwerk, den virtuellen Computer und das Speicherkonto wie folgt:

1. Wählen Sie im linken Menü die Option **Ressourcengruppen** aus.

2. Wählen Sie **myResourceGroup** aus.

3. Wählen Sie die Option **Ressourcengruppe löschen**.

4. Geben Sie unter **RESSOURCENGRUPPENNAMEN EINGEBEN** den Namen **myResourceGroup** ein.

5. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den Private Link-Dienst:
> [!div class="nextstepaction"]
> [Erstellen eines Private Link-Diensts](create-private-link-service-portal.md)