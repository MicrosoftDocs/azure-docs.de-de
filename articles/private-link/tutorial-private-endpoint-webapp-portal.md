---
title: 'Tutorial: Herstellen einer Verbindung mit einer Web-App mithilfe eines privaten Azure-Endpunkts'
titleSuffix: Azure Private Link
description: Beginnen Sie mit diesem Tutorial mit der Verwendung eines privaten Endpunkts in Azure, um sich privat mit einer Web-App zu verbinden.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896972"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Tutorial: Herstellen einer Verbindung mit einer Web-App mithilfe eines privaten Azure-Endpunkts

Der private Azure-Endpunkt ist der grundlegende Baustein für Private Link in Azure. Mit ihm können Azure-Ressourcen wie virtuelle Computer (VMs) privat mit Private Link-Ressourcen kommunizieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts.
> * Erstellen Sie eine VM.
> * Erstellen einer Web-App.
> * Erstellen eines privaten Endpunkts.
> * Testen der Verbindung mit dem privaten Endpunkt der Web-App.

Wenn Sie kein Azure-Abonnement haben, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

> [!Note]
> Der private Endpunkt ist in öffentlichen Regionen für Windows- und Linux-Web-Apps mit den Tarifen PremiumV2 und PremiumV3 sowie dem Premium-Tarif für Azure Functions (auch als Elastic Premium-Tarif bezeichnet) verfügbar. 

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
    | Region           | Wählen Sie **Europa, Westen** aus. |

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

## <a name="create-web-app"></a>Erstellen einer Web-App

In diesem Abschnitt erstellen Sie eine Web-App.

1. Wählen Sie im linken Menü **Ressource erstellen** > **Speicher** > **Web-App** aus, oder suchen Sie über das Suchfeld nach **Web-App**.

2. Geben Sie auf der Registerkarte **Grundlagen** von **Web-App erstellen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **myResourceGroup** aus. |
    | **Instanzendetails** |  |
    | Name | Geben Sie **mywebapp** ein. Wenn der Name nicht verfügbar ist, geben Sie einen eindeutigen Namen ein. |
    | Veröffentlichen | Wählen Sie **Code** aus. |
    | Laufzeitstapel | Wählen Sie **.NET Core 3.1 (LTS)** aus. |
    | Betriebssystem | Wählen Sie **Windows** aus. |
    | Region | Wählen Sie **Europa, Westen** aus. |
    | **App Service-Plan** |  |
    | Windows-Plan (Europa, Westen) | Wählen Sie **Neu erstellen**. </br> Geben Sie **myServicePlan** in **Name** ein. |
    | SKU und Größe | Wählen Sie **Größe ändern** aus. </br> Wählen Sie **P2V2** im Bildschirm **Spezifikationsauswahl** aus. </br> Wählen Sie **Übernehmen**. |
   
3. Klicken Sie auf **Überprüfen + erstellen**.

4. Klicken Sie auf **Erstellen**.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Registerkarte „Grundlagen“ von „Web-App erstellen“ im Azure-Portal." border="true":::

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

1. Wählen Sie im linken Menü **Alle Ressourcen** > **mywebapp** oder den Namen aus, den Sie bei der Erstellung ausgewählt haben.

2. Wählen Sie in der Web-App-Übersicht **Einstellungen** > **Netzwerk** aus.

3. Wählen Sie in **Netzwerk** die Option **Verbindungen mit privaten Endpunkten konfigurieren** aus.

4. Wählen Sie im Bildschirm **Verbindungen mit privaten Endpunkten** die Option **+ Hinzufügen** aus.

5. Geben Sie im Bildschirm **Privaten Endpunkt hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **mywebappendpoint** ein. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **mySubnet** aus. |
    | Integration in eine private DNS-Zone | Wählen Sie **Ja** aus. |

6. Klicken Sie auf **OK**.
    

## <a name="test-connectivity-to-private-endpoint"></a>Testen der Verbindung mit dem privaten Endpunkt

In diesem Abschnitt verwenden Sie den virtuellen Computer, den Sie im vorherigen Schritt erstellt haben, um über den privaten Endpunkt eine Verbindung mit der Web-App herzustellen.

1. Wählen Sie **Ressourcengruppen** im linken Navigationsbereich aus.

2. Wählen Sie **myResourceGroup** aus.

3. Wählen Sie **myVM** aus.

4. Wählen Sie auf der Seite „Übersicht“ für **myVM** die Option **Verbinden** und dann **Bastion** aus.

5. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

6. Geben Sie Benutzernamen und Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben.

7. Öffnen Sie Windows PowerShell auf dem Server, nachdem Sie eine Verbindung hergestellt haben.

8. Geben Sie `nslookup <webapp-name>.azurewebsites.net` ein. Ersetzen Sie **\<webapp-name>** durch den Namen der Web-App, die Sie in den vorherigen Schritten erstellt haben.  Sie erhalten eine Meldung ähnlich der folgenden:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Als Name der Web-App wird die private IP-Adresse **10.1.0.5** zurückgegeben.  Diese Adresse befindet sich in dem Subnetz des virtuellen Netzwerks, das Sie zuvor erstellt haben.

9. Öffnen Sie einen Webbrowser auf Ihrem lokalen Computer, und geben Sie die externe URL Ihrer Web-App ein: **https://\<webapp-name>.azurewebsites.net**.

10. Vergewissern Sie sich, dass eine Seite **403** angezeigt wird. Diese Seite zeigt an, dass nicht von extern auf die Web-App zugegriffen werden kann.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="403-Seite für externe Web-App-Adresse." border="true":::

11. Öffnen Sie in der Bastionhostverbindung mit **myVM** den Internet Explorer.

12. Geben Sie die URL Ihrer Web-App ein: **https://\<webapp-name>.azurewebsites.net**.

13. Vergewissern Sie sich, dass die Standardseite der Web-App angezeigt wird.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Standardseite der Web-App." border="true":::

18. Trennen Sie die Verbindung zu **myVM**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die das virtuelle Netzwerk, den virtuellen Computer und die Web-App wie folgt:

1. Wählen Sie im linken Menü die Option **Ressourcengruppen** aus.

2. Wählen Sie **myResourceGroup** aus.

3. Wählen Sie die Option **Ressourcengruppe löschen**.

4. Geben Sie unter **RESSOURCENGRUPPENNAMEN EINGEBEN** den Namen **myResourceGroup** ein.

5. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den Private Link-Dienst:
> [!div class="nextstepaction"]
> [Erstellen eines Private Link-Diensts](create-private-link-service-portal.md)
