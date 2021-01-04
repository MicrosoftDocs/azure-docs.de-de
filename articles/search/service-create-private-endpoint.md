---
title: Erstellen eines privaten Endpunkts für sichere Verbindungen
titleSuffix: Azure Cognitive Search
description: Einrichten eines privaten Endpunkts in einem virtuellen Netzwerk für sichere Verbindungen mit einem Azure Cognitive Search-Dienst.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 6ee72a25fc8435159ae75ac3296742eda58617b6
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779939"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Erstellen eines privaten Endpunkts für sichere Verbindungen mit Azure Cognitive Search

In diesem Artikel verwenden Sie das Azure-Portal, um eine neue Azure Cognitive Search-Dienstinstanz zu erstellen, auf die nicht über das Internet zugegriffen werden kann. Anschließend konfigurieren Sie einen virtuellen Azure-Computer im selben Netzwerk und greifen damit über einen privaten Endpunkt auf den Suchdienst zu.

Private Endpunkte werden durch [Azure Private Link](../private-link/private-link-overview.md) als separater Dienst bereitgestellt. Weitere Informationen zu den Kosten finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/private-link/).

> [!Important]
> Die Unterstützung privater Endpunkte für Azure Cognitive Search kann mithilfe des Azure-Portals oder der [Verwaltungs-REST-API, Version 2020-03-13,](/rest/api/searchmanagement/) konfiguriert werden. Wenn der Dienstendpunkt privat ist, sind einige Portalfunktionen deaktiviert. Sie können Informationen auf Dienstebene anzeigen und verwalten, aber der Portalzugriff auf Indexdaten und die verschiedenen Komponenten im Dienst sind aus Sicherheitsgründen eingeschränkt. Dazu zählen beispielsweise Index-, Indexer- und Skillsetdefinitionen. Als Alternative zum Portal können Sie die [VS Code-Erweiterung](https://aka.ms/vscode-search) verwenden, um mit den verschiedenen Komponenten im Dienst zu interagieren.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Gründe für den sicheren Zugriff über einen privaten Endpunkt

[Private Endpunkte](../private-link/private-endpoint-overview.md) für Azure Cognitive Search ermöglichen, dass ein Client in einem virtuellen Netzwerk über eine [private Verbindung](../private-link/private-link-overview.md) sicher auf Daten in einem Suchindex zugreifen kann. Der private Endpunkt verwendet eine IP-Adresse aus dem [Adressraum des virtuellen Netzwerks](../virtual-network/private-ip-addresses.md) für Ihren Suchdienst. Der Netzwerkdatenverkehr zwischen dem Client und dem Suchdienst wird über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt. Eine Liste mit anderen PaaS-Diensten, bei denen Private Link unterstützt wird, finden Sie im Abschnitt [Verfügbarkeit](../private-link/private-link-overview.md#availability) in der Produktdokumentation.

Private Endpunkte für Ihren Suchdienst ermöglichen Folgendes:

- Blockieren aller Verbindungen am öffentlichen Endpunkt für den Suchdienst
- Erhöhen der Sicherheit für das virtuelle Netzwerk, indem Sie die Exfiltration von Daten aus dem virtuellen Netzwerk blockieren können
- Sicheres Verbinden mit dem Suchdienst aus lokalen Netzwerken, die eine Verbindung mit dem virtuellen Netzwerk über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoute](../expressroute/expressroute-locations.md) mit privatem Peering herstellen

## <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und das Subnetz zum Hosten des virtuellen Computers, der für den Zugriff auf den privaten Endpunkt des Suchdiensts verwendet wird.

1. Wählen Sie auf der Startregisterkarte des Azure-Portals die Option **Ressource erstellen** > **Netzwerk** > **Virtuelles Netzwerk** aus.

1. Geben Sie in **Virtuelles Netzwerk erstellen** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie Ihr Abonnement aus.|
    | Resource group | Wählen Sie **Neu erstellen** aus, geben Sie *myResourceGroup* ein, und wählen Sie dann **OK** aus. |
    | Name | Geben Sie *MyVirtualNetwork* ein. |
    | Region | Wählen Sie Ihre bevorzugte Region aus. |
    |||

1. Übernehmen Sie für den Rest der Einstellungen die Standardwerte. Klicken Sie auf **Überprüfen + erstellen** und danach auf **Erstellen**.

## <a name="create-a-search-service-with-a-private-endpoint"></a>Erstellen eines Suchdiensts mit einem privaten Endpunkt

In diesem Abschnitt erstellen Sie einen neuen Azure Cognitive Search-Dienst mit einem privaten Endpunkt. 

1. Wählen Sie oben links auf dem Bildschirm im Azure-Portal die Option **Ressource erstellen** > **Web** > **Azure Cognitive Search** aus.

1. Geben Sie unter **Neuer Suchdienst – Grundlagen** folgende Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **PROJEKTDETAILS** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. Diese haben Sie im vorherigen Abschnitt erstellt.|
    | **INSTANZDETAILS** |  |
    | URL | Geben Sie einen eindeutigen Namen ein. |
    | Standort | Wählen Sie Ihre bevorzugte Region aus. |
    | Tarif | Wählen Sie **Tarif ändern** und dann den gewünschten Tarif aus. (Keine Unterstützung im **Free**-Tarif. Muss **Basic**-Tarif oder höher sein.) |
    |||
  
1. Klicken Sie auf **Weiter: Skalieren**.

1. Übernehmen Sie die Standardwerte, und wählen Sie **Weiter: Netzwerk** aus.

1. Wählen Sie unter **Neuer Suchdienst – Netzwerk** die Option **Privat** für **Endpunktkonnektivität (Daten)** aus.

1. Wählen Sie unter **Neuer Suchdienst Netzwerk** unter **Privater Endpunkt** die Option **+ Hinzufügen** aus. 

1. Geben Sie unter **Privaten Endpunkt erstellen** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. Diese haben Sie im vorherigen Abschnitt erstellt.|
    | Standort | Wählen Sie **USA, Westen** aus.|
    | Name | Geben Sie *myPrivateEndpoint* ein.  |
    | Zielunterressource | Übernehmen Sie den Standardwert **searchService**. |
    | **NETZWERK** |  |
    | Virtuelles Netzwerk  | Wählen Sie *MyVirtualNetwork* in der Ressourcengruppe *myResourceGroup* aus. |
    | Subnet | Wählen Sie *mySubnet* aus. |
    | **PRIVATE DNS-INTEGRATION** |  |
    | Integration in eine private DNS-Zone  | Übernehmen Sie den Standardwert **Ja**. |
    | Private DNS-Zone  | Übernehmen Sie den Standardwert **(Neu) privatelink.search.windows.net**. |
    |||

1. Klicken Sie auf **OK**. 

1. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Seite **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft. 

1. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus. 

1. Navigieren Sie nach Abschluss der Bereitstellung des neuen Diensts zu der soeben erstellten Ressource.

1. Wählen Sie im linken Inhaltsmenü die Option **Schlüssel** aus.

1. Kopieren Sie den **primären Administratorschlüssel** für später, wenn Sie eine Verbindung mit dem Dienst herstellen.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Wählen Sie oben links auf dem Bildschirm im Azure-Portal die Option **Ressource erstellen** > **Compute** > **Virtueller Computer** aus.

1. Geben Sie in **Virtuellen Computer erstellen – Grundlagen** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **PROJEKTDETAILS** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. Diese haben Sie im vorherigen Abschnitt erstellt.  |
    | **INSTANZDETAILS** |  |
    | Name des virtuellen Computers | Geben Sie *myVm* ein. |
    | Region | Wählen Sie **USA, Westen** oder die verwendete Region aus. |
    | Verfügbarkeitsoptionen | Übernehmen Sie den Standardwert **Keine Infrastrukturredundanz erforderlich**. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter** aus. |
    | Size | Übernehmen Sie den Standardwert **Standard DS1 v2**. |
    | **ADMINISTRATORKONTO** |  |
    | Username | Geben Sie einen Benutzernamen Ihrer Wahl ein. |
    | Kennwort | Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **REGELN FÜR EINGEHENDE PORTS** |  |
    | Öffentliche Eingangsports | Übernehmen Sie die Standardeinstellung **Ausgewählte Ports zulassen**. |
    | Eingangsports auswählen | Übernehmen Sie den Standardwert **RDP (3389)** . |
    | **SPAREN SIE GELD** |  |
    | Windows-Lizenz bereits vorhanden? | Übernehmen Sie den Standardwert **Nein**. |
    |||

1. Klicken Sie auf **Weiter: Datenträger**.

1. Übernehmen Sie unter **Virtuellen Computer erstellen – Datenträger** die Standardwerte, und wählen Sie **Weiter: Netzwerk**.

1. Wählen Sie in **Virtuellen Computer erstellen – Netzwerk** diese Informationen aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Virtuelles Netzwerk | Übernehmen Sie den Standardwert **MyVirtualNetwork**.  |
    | Adressraum | Übernehmen Sie den Standardwert **10.1.0.0/24**.|
    | Subnet | Übernehmen Sie den Standardwert **mySubnet (10.1.0.0/24)** .|
    | Öffentliche IP-Adresse | Übernehmen Sie den Standardwert **(neu) myVm-ip**. |
    | Öffentliche Eingangsports | Wählen Sie **Ausgewählte Ports zulassen** aus. |
    | Eingangsports auswählen | Wählen Sie **HTTP** und **RDP** aus.|
    ||

   > [!NOTE]
   > IPv4-Adressen können im [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)-Format ausgedrückt werden. Denken Sie daran, den für private Netzwerke reservierten IP-Adressbereich zu vermeiden, wie in [RFC 1918](https://tools.ietf.org/html/rfc1918) beschrieben:
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Seite **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

1. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus. 

## <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

Laden Sie den virtuellen Computer *myVm* herunter, und stellen Sie dann wie folgt eine Verbindung mit ihm her:

1. Geben Sie in der Suchleiste des Portals *myVm* ein.

1. Wählen Sie die Schaltfläche **Verbinden** aus. Nach dem Auswählen der Schaltfläche **Verbinden** wird **Verbindung mit virtuellem Computer herstellen** geöffnet.

1. Wählen Sie **RDP-Datei herunterladen** aus. Azure erstellt eine Remotedesktopprotokoll-Datei (*RDP*) und lädt sie auf Ihren Computer herunter.

1. Öffnen Sie die heruntergeladene RDP*-Datei.

    1. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus.

    1. Geben Sie den Benutzernamen und das Kennwort ein, den/das Sie beim Erstellen des virtuellen Computers angegeben haben.

        > [!NOTE]
        > Unter Umständen müssen Sie **Weitere Optionen** > **Anderes Konto verwenden** auswählen, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.

1. Klicken Sie auf **OK**.

1. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wenn Sie eine Zertifikatwarnung erhalten, wählen Sie **Ja** oder **Weiter** aus.

1. Sobald der VM-Desktop angezeigt wird, minimieren Sie ihn, um zu Ihrem lokalen Desktop zurückzukehren.  

## <a name="test-connections"></a>Testen von Verbindungen

In diesem Abschnitt überprüfen Sie den Zugriff im privaten Netzwerk auf den Suchdienst und stellen über den privaten Endpunkt eine private Verbindung her.

Wenn der Suchdienst-Endpunkt privat ist, sind einige Portalfunktionen deaktiviert. Sie können Einstellungen auf Dienstebene anzeigen und verwalten, aber der Portalzugriff auf Indexdaten und verschiedene andere Komponenten im Dienst sind aus Sicherheitsgründen eingeschränkt. Dazu zählen beispielsweise Index-, Indexer- und Skillsetdefinitionen.

1. Öffnen Sie auf dem Remotedesktop von *myVM* PowerShell.

1. Geben Sie „nslookup [Suchdienstname].search.windows.net“ ein.

    Sie erhalten eine Meldung wie die folgende:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Stellen Sie über den virtuellen Computer eine Verbindung mit dem Suchdienst her, und erstellen Sie einen Index. Sie können die Anweisungen in diesem [Schnellstart](search-get-started-rest.md) befolgen, um einen neuen Suchdienst in Ihrem Dienst mithilfe der REST-API zu erstellen. Zum Einrichten von Anforderungen aus einem Web-API-Testtool sind der Endpunkt des Suchdiensts (https://[Suchdienstname].search.windows.net) und der Administrator-API-Schlüssel, den Sie in einem vorherigen Schritt kopiert haben, erforderlich.

1. Das Ausführen der Schritte des Schnellstarts über den virtuellen Computer dient zur Überprüfung, ob der Dienst voll funktionsfähig ist.

1. Schließen Sie die Remotedesktopverbindung mit *myVM*. 

1. Um sicherzustellen, dass der Dienst nicht über einen öffentlichen Endpunkt zugänglich ist, öffnen Sie Postman in Ihrer lokalen Arbeitsstation, und führen Sie die ersten Schritte im Schnellstart aus. Wenn eine Fehlermeldung darüber angezeigt wird, dass der Remoteserver nicht vorhanden ist, haben Sie erfolgreich einen privaten Endpunkt für den Suchdienst konfiguriert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 
Wenn Sie den privaten Endpunkt, den Suchdienst und den virtuellen Computer nicht mehr benötigen, löschen Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen:
1. Geben Sie oben im Portal die Zeichenfolge  *myResourceGroup* im Feld **Suchen** ein, und wählen Sie in den Suchergebnissen  *myResourceGroup* aus. 
1. Wählen Sie die Option **Ressourcengruppe löschen**. 
1. Geben Sie  *myResourceGroup* für **RESSOURCENGRUPPENNAMEN EINGEBEN** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie einen virtuellen Computer in einem virtuellen Netzwerk und einen Suchdienst mit einem privaten Endpunkt erstellt. Sie haben über das Internet eine Verbindung mit dem virtuellen Computer hergestellt und über Private Link sicher mit dem Suchdienst kommuniziert. Weitere Informationen zu privaten Endpunkten finden Sie unter  [Was ist privater Endpunkt in Azure?](../private-link/private-endpoint-overview.md).
