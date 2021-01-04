---
title: 'Schnellstart: Erstellen eines privaten Endpunkts mit dem Azure-Portal'
description: In diesem Schnellstart erfahren Sie, wie Sie über das Azure-Portal einen privaten Endpunkt erstellen.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 395912ae70c5a01bd7de9a80cf8a507dd516028e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018068"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Schnellstart: Erstellen eines privaten Endpunkts mit dem Azure-Portal

Beginnen Sie mit Azure Private Link, indem Sie einen privaten Endpunkt verwenden, um eine Verbindung mit einer Azure-Web-App herzustellen.

In diesem Schnellstart erstellen Sie einen privaten Endpunkt für eine Azure-Web-App und stellen einen virtuellen Computer bereit, um die private Verbindung zu testen.  

Private Endpunkte können für verschiedene Arten von Azure-Diensten erstellt werden, z. B. Azure SQL und Azure Storage.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Eine Azure-Web-App mit einem App Service-Plan vom Typ **PremiumV2-Tarif** oder höher, die in Ihrem Azure-Abonnement bereitgestellt wird.  
    * Weitere Informationen und ein Beispiel finden Sie unter [Schnellstart: Erstellen von ASP.NET Core-Web-Apps in Azure](../app-service/quickstart-dotnetcore.md). 
    * Ein ausführliches Tutorial zum Erstellen einer Web-App und eines Endpunkts finden Sie unter [Tutorial: Herstellen einer Verbindung mit einer Web-App mithilfe eines privaten Azure-Endpunkts](tutorial-private-endpoint-webapp-portal.md).

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
    | Ressourcengruppe   | Wählen Sie **CreatePrivateEndpointQS-rg** aus. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **Europa, Westen** aus.|

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
    | Ressourcengruppe | Wählen Sie **CreatePrivateEndpointQS-rg** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **Europa, Westen** aus. |
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

## <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts

In diesem Abschnitt erstellen Sie einen privaten Endpunkt für die Web-App, die Sie im Abschnitt „Voraussetzungen“ erstellt haben.

1. Wählen Sie auf der oberen linken Seite des Bildschirms im Portal die Option **Ressource erstellen** > **Netzwerk** > **Private Link** aus, oder geben Sie im Suchfeld **Private Link** ein.

2. Wählen Sie **Erstellen** aus.

3. Wählen Sie in **Private Link-Center** im linken Menü **Private Endpunkte** aus.

4. Wählen Sie unter **Private Endpunkte** die Option **+ Hinzufügen** aus.

5. Geben Sie auf der Registerkarte **Grundlagen** unter **Privaten Endpunkt erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **CreatePrivateEndpointQS-rg** aus. Diese Ressourcengruppe wurde im vorherigen Abschnitt erstellt.|
    | **Instanzendetails** |  |
    | Name  | Geben Sie **myPrivateEndpoint** ein. |
    | Region | Wählen Sie **Europa, Westen** aus. |

6. Wählen Sie die Registerkarte **Ressource** oder unten auf der Seite **Weiter: Ressource** aus.
    
7. Geben Sie in **Ressource** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Verbindungsmethode | Wählen Sie **Hiermit wird eine Verbindung mit einer Azure-Ressource im eigenen Verzeichnis hergestellt** aus. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Ressourcentyp | Wählen Sie **Microsoft.Web/sites** aus. |
    | Resource | Klicken Sie auf **\<your-web-app-name>** (Zur Kasse). </br> Wählen Sie in den Voraussetzungen den Namen der von Ihnen erstellten Web-App aus. |
    | Zielunterressource | Wählen Sie **Sites** aus. |

8. Wählen Sie die Registerkarte **Konfiguration** oder die Schaltfläche **Weiter: Konfiguration** am unteren Bildschirmrand aus.

9. Geben Sie diese Informationen in **Konfiguration** ein oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerk** |  |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **mySubnet** aus. |
    | **Private DNS-Integration** |  |
    | Integration in eine private DNS-Zone | Übernehmen Sie den Standardwert **Ja**. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Private DNS-Zonen | Belassen Sie den Standardwert **(New) privatelink.azurewebsites.net**.
    

13. Klicken Sie auf **Überprüfen + erstellen**.

14. Klicken Sie auf **Erstellen**.

## <a name="test-connectivity-to-private-endpoint"></a>Testen der Verbindung mit dem privaten Endpunkt

In diesem Abschnitt verwenden Sie den virtuellen Computer, den Sie im vorherigen Schritt erstellt haben, um über den privaten Endpunkt eine Verbindung mit der Web-App herzustellen.

1. Wählen Sie **Ressourcengruppen** im linken Navigationsbereich aus.

2. Wählen Sie **CreatePrivateEndpointQS-rg** aus.

3. Wählen Sie **myVM** aus.

4. Wählen Sie auf der Seite „Übersicht“ für **myVM** die Option **Verbinden** und dann **Bastion** aus.

5. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

6. Geben Sie Benutzernamen und Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben.

7. Öffnen Sie Windows PowerShell auf dem Server, nachdem Sie eine Verbindung hergestellt haben.

8. Geben Sie `nslookup <your-webapp-name>.azurewebsites.net` ein. Ersetzen Sie **\<your-webapp-name>** durch den Namen der Web-App, die Sie in den vorherigen Schritten erstellt haben.  Sie erhalten eine Meldung ähnlich der folgenden:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Als Name der Web-App wird die private IP-Adresse **10.1.0.5** zurückgegeben.  Diese Adresse befindet sich in dem Subnetz des virtuellen Netzwerks, das Sie zuvor erstellt haben.

11. Öffnen Sie in der Bastionhostverbindung mit **myVM** den Internet Explorer.

12. Geben Sie die URL Ihrer Web-App ein: **https://\<your-webapp-name>.azurewebsites.net**.

13. Sie erhalten die Standardseite der Web-App, wenn Ihre Anwendung noch nicht bereitgestellt wurde:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Standardseite der Web-App." border="true":::

18. Trennen Sie die Verbindung zu **myVM**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die das virtuelle Netzwerk, den virtuellen Computer und die Web-App wie folgt:

1. Wählen Sie im linken Menü die Option **Ressourcengruppen** aus.

2. Wählen Sie **CreatePrivateEndpointQS-rg** aus.

3. Klicken Sie auf **Ressourcengruppe löschen**.

4. Geben Sie **CreatePrivateEndpointQS-rg** in **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN** ein.

5. Klicken Sie auf **Löschen**.


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Folgendes erstellt:

* virtuelles Netzwerk und Bastionhost
* Virtuellen Computer
* Privater Endpunkt für eine Azure-Web-App.

Sie haben den virtuellen Computer verwendet, um die Konnektivität mit der Web-App über den privaten Endpunkt auf sichere Weise zu testen.



Weitere Informationen zu den Diensten, die einen privaten Endpunkt unterstützen, finden Sie unter:
> [!div class="nextstepaction"]
> [Private Link-Verfügbarkeit](private-link-overview.md#availability)
