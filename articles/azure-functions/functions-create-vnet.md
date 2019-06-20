---
title: Integrieren von Azure Functions in ein virtuelles Azure-Netzwerk
description: Ein Tutorial in einzelnen Schritten, in dem erläutert wird, wie Sie eine Funktion mit einem virtuellen Azure-Netzwerk verbinden.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 07c7d7fb682708bf813820440d9c790c28b1f3e5
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834579"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Tutorial: Integrieren von Functions in ein virtuelles Azure-Netzwerk

In diesem ausführlichen Tutorial wird erläutert, wie Sie mithilfe von Azure Functions eine Verbindung mit Ressourcen in einem virtuellen Azure-Netzwerk herstellen. Sie erstellen eine Funktion, die sowohl auf das Internet als auch auf eine VM zugreifen kann, auf der WordPress in einem virtuellen Netzwerk ausgeführt wird.

> [!div class="checklist"]
> * Erstellen einer Funktions-App im Premium-Plan
> * Bereitstellen einer WordPress-Website auf einer VM im virtuellen Netzwerk
> * Verbinden der Funktions-App mit dem virtuellen Netzwerk
> * Erstellen eines Funktionsproxys für den Zugriff auf WordPress-Ressourcen
> * Anfordern einer WordPress-Datei aus dem virtuellen Netzwerk

> [!NOTE]  
> In diesem Tutorial wird eine Funktions-App im Premium-Plan erstellt. Dieser Hostingplan befindet sich derzeit in der Vorschau. Weitere Informationen finden Sie unter [Premium-Plan].

## <a name="topology"></a>Topologie

Das folgende Diagramm zeigt die Architektur der Lösung, die Sie erstellen:

 ![Benutzeroberfläche für die Integration des virtuellen Netzwerks](./media/functions-create-vnet/topology.png)

Funktionen, die im Premium-Plan ausgeführt werden, bieten dieselben Hostingfunktionen wie Web-Apps in Azure App Service, wozu auch ein Feature zur VNET-Integration gehört. Weitere Informationen zur VNET-Integration, einschließlich Problembehandlung und erweiterter Konfiguration, finden Sie unter [Integrieren Ihrer App in ein Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial ist es wichtig, dass Sie die Funktionsweise und Verwendung von IP-Adressen und Subnetzen verstehen. Beginnen Sie mit [diesem Artikel zu den Grundlagen von Adressen und Subnetzen](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Viele weitere Artikel und Videos finden Sie online.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-a-function-app-in-a-premium-plan"></a>Erstellen einer Funktions-App in einem Premium-Tarif

Als Erstes erstellen Sie eine Funktions-App im [Premium-Plan]. Dieser Plan ermöglicht eine Skalierung ohne Server und unterstützt gleichzeitig die Integration in ein virtuelles Netzwerk.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Sie können die Funktions-App an das Dashboard anheften, indem Sie auf das Nadelsymbol in der oberen rechten Ecke klicken. Durch das Anheften können Sie nach dem Erstellen der VM einfacher zu dieser Funktions-App zurückkehren.

## <a name="create-a-vm-inside-a-virtual-network"></a>Erstellen eines virtuellen Computers in einem virtuellen Netzwerk

Als Nächstes erstellen Sie eine vorkonfigurierte VM, auf der WordPress in einem virtuellen Netzwerk ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) von Jetware) ausgeführt wird. Die WordPress-VM wird aufgrund ihrer geringen Kosten und einfachen Handhabung verwendet. Dieses Szenario funktioniert mit allen Ressourcen in einem virtuellen Netzwerk, z.B. REST-APIs, App Service-Umgebungen und anderen Azure-Diensten. 

1. Klicken Sie im Portal im linken Navigationsbereich auf **+ Ressource erstellen**, geben Sie im Suchfeld `WordPress LEMP7 Max Performance` ein, und drücken Sie die EINGABETASTE.

1. Wählen Sie **Wordpress LEMP Max Performance** aus den Suchergebnissen aus. Wählen Sie **Wordpress LEMP Max Performance for CentOS** als **Softwareplan** aus, und klicken Sie auf **Erstellen**.

1. Verwenden Sie auf der Registerkarte **Grundlagen** die VM-Einstellungen, die Sie in der Tabelle unterhalb der Abbildung finden:

    ![Registerkarte „Grundeinstellungen“ zum Erstellen eines virtuellen Computers](./media/functions-create-vnet/create-vm-1.png)

    | Einstellung      | Empfohlener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. | 
    | **[Ressourcengruppe](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | Wählen Sie `myResourceGroup` oder die Ressourcengruppe aus, die Sie mit Ihrer Funktions-App erstellt haben. Durch Verwenden derselben Ressourcengruppe für die Funktions-App, die WordPress-VM und den Hostingplan lassen sich die Ressourcen einfacher bereinigen, wenn Sie dieses Tutorial abgeschlossen haben. |
    | **Name des virtuellen Computers** | VNET-Wordpress | Der VM-Name muss in der Ressourcengruppe eindeutig sein. |
    | **[Region](https://azure.microsoft.com/regions/)** | (Europa) Europa, Westen | Wählen Sie eine Region in Ihrer Nähe oder in der Nähe der Funktionen aus, die auf die VMs zugreifen. |
    | **Größe** | B1s | Klicken Sie auf **Größe ändern**, und wählen Sie das Standardimage „B1s“ aus, das eine vCPU und 1 GB Arbeitsspeicher aufweist. |
    | **Authentifizierungstyp** | Kennwort | Um die Kennwortauthentifizierung zu verwenden, müssen Sie einen **Benutzernamen**, ein sicheres **Kennwort** sowie eine **Kennwortbestätigung** angeben. Für dieses Tutorial müssen Sie sich nur bei dem virtuellen Computer anmelden, wenn Sie Probleme beheben müssen. |

1. Klicken Sie auf die Registerkarte **Netzwerk**, und klicken Sie unter „Virtuelle Netzwerke konfigurieren“ auf **Neu erstellen**.

1. Verwenden Sie unter **Virtuelles Netzwerk erstellen** die Einstellungen in der Tabelle unterhalb der Abbildung:

    ![Registerkarte für Netzwerkeinstellungen in „Virtuelles Netzwerk erstellen“](./media/functions-create-vnet/create-vm-2.png)

    | Einstellung      | Empfohlener Wert  | Beschreibung      |
    | ------------ | ---------------- | ---------------- |
    | **Name** | myResourceGroup-vnet | Sie können den für Ihr virtuelles Netzwerk generierten Standardnamen verwenden. |
    | **Adressbereich** | 10.10.0.0/16 | Verwenden Sie einen einzelnen Adressbereich für das virtuelle Netzwerk. |
    | **Subnetzname** | Tutorial-Net | Der Name des Subnetzes. |
    | **Adressbereich** (Subnetz) | 10.10.1.0/24   | Die Subnetzgröße definiert, wie viele Schnittstellen zum Subnetz hinzugefügt werden können. Dieses Subnetz wird von der WordPress-Website verwendet.  Ein `/24`-Subnetz bietet 254 Hostadressen. |

1. Klicken Sie auf **OK**, um das virtuelle Netzwerk zu erstellen.

1. Wählen Sie auf der Registerkarte **Netzwerk** die Option **Keine** für die **öffentliche IP-Adresse** aus.

1. Klicken Sie auf die Registerkarte **Verwaltung**, und wählen Sie unter **Diagnosespeicherkonto** das Speicherkonto aus, das Sie mit Ihrer Funktions-App erstellt haben.

1. Klicken Sie auf **Überprüfen + erstellen**. Wenn die Überprüfung abgeschlossen ist, wählen Sie **Erstellen** aus. Der Vorgang der VM-Erstellung dauert einige Minuten. Die erstellte VM kann nur auf das virtuelle Netzwerk zugreifen.

1. Wählen Sie nach dem Erstellen der VM **Zu Ressource wechseln** aus, um die Seite für Ihre neue VM anzuzeigen, und klicken Sie dann unter **Einstellungen** auf **Netzwerk**.

1. Stellen Sie sicher, dass keine **öffentliche IP-Adresse** vorhanden ist. Notieren Sie sich die **private IP-Adresse**, die Sie zum Herstellen der Verbindung zwischen Ihrer Funktions-App und der VM verwenden.

    ![Netzwerkeinstellungen auf der VM](./media/functions-create-vnet/vm-networking.png)

Damit haben Sie eine WordPress-Website eingerichtet, die ausschließlich in Ihrem virtuellen Netzwerk bereitgestellt ist. Auf diese Website kann aus dem öffentlichen Internet nicht zugegriffen werden.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Verbinden Ihrer Funktions-App mit dem virtuellen Netzwerk

Über eine WordPress-Website, die auf einer VM in einem virtuellen Netzwerk ausgeführt wird, können Sie nun Ihre Funktions-App mit diesem virtuellen Netzwerk verbinden.

1. Klicken Sie in Ihrer neuen Funktions-App auf **Plattformfeatures** > **Netzwerk**.

    ![Auswählen von „Netzwerk“ in der Funktions-App](./media/functions-create-vnet/networking-0.png)

1. Wählen Sie unter **VNET-Integration** die Option **Zum Konfigurieren hier klicken** aus.

    ![Status für das Konfigurieren einer Netzwerkfunktion](./media/functions-create-vnet/Networking-1.png)

1. Wählen Sie auf der Seite zur Integration des virtuellen Netzwerks die Option **VNET hinzufügen (Vorschau)** aus.

    ![Hinzufügen der VNET-Integration (Vorschau)](./media/functions-create-vnet/networking-2.png)

1. Verwenden Sie unter **Netzwerkfeaturestatus** die Einstellungen in der Tabelle unterhalb der Abbildung:

    ![Definieren des virtuellen Netzwerks für die Funktions-App](./media/functions-create-vnet/networking-3.png)

    | Einstellung      | Empfohlener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | Das virtuelle Netzwerk ist dasjenige, das Sie zuvor erstellt haben. |
    | **Subnetz** | Neues Subnetz erstellen | Erstellen Sie ein Subnetz in dem virtuellen Netzwerk zur Verwendung durch Ihre Funktions-App. Die VNET-Integration muss so konfiguriert werden, dass ein leeres Subnetz verwendet wird. Es spielt keine Rolle, dass Ihre Funktionen ein anderes Subnetz verwenden als Ihre VM. Das virtuelle Netzwerk leitet den Datenverkehr automatisch zwischen den beiden Subnetzen weiter. |
    | **Subnetzname** | Function-Net | Name des neuen Subnetzes. |
    | **Adressblock des virtuellen Netzwerks** | 10.10.0.0/16 | Wählen Sie denselben Adressblock aus, der von der WordPress-Website verwendet wird. Es sollte nur ein Adressblock definiert sein. |
    | **Adressbereich** | 10.10.2.0/24   | Die Subnetzgröße begrenzt die Gesamtanzahl von Instanzen, auf die Ihre Funktions-App im Premium-Plan erweitert werden kann. Dieses Beispiel verwendet ein `/24`-Subnetz mit 254 verfügbaren Hostadressen. Dieses Subnetz ist überdimensioniert, aber einfach zu berechnen. |

1. Klicken Sie auf **OK**, um das Subnetz hinzuzufügen. Schließen Sie die Seiten „VNET-Integration“ und „Netzwerkfeaturestatus“, um zur Seite Ihrer Funktions-App zurückzukehren.

Die Funktions-App hat nun Zugriff auf das virtuelle Netzwerk, in dem die WordPress-Website ausgeführt wird. Als Nächstes verwenden Sie [Azure-Funktionsproxys](functions-proxies.md), um eine Datei von der WordPress-Website zurückzugeben.

## <a name="create-a-proxy-to-access-vm-resources"></a>Erstellen eines Proxys für den Zugriff auf VM-Ressourcen

Wenn die VNET-Integration aktiviert ist, können Sie einen Proxy in Ihrer Funktions-App erstellen, um Anforderungen an die VM weiterzuleiten, die im virtuellen Netzwerk ausgeführt wird.

1. Wählen Sie in Ihrer Funktions-App die Option **Proxys** >  **+** aus, und verwenden Sie dann die Proxyeinstellungen in der Tabelle unterhalb der Abbildung:

    ![Definieren der Proxyeinstellungen](./media/functions-create-vnet/create-proxy.png)

    | Einstellung  | Empfohlener Wert  | Beschreibung      |
    | -------- | ---------------- | ---------------- |
    | **Name** | Plant | Der Name kann ein beliebiger Wert sein. Er wird zum Identifizieren des Proxys verwendet. |
    | **Routenvorlage** | /plant | Die Route, die einer VM-Ressource zugeordnet ist. |
    | **Back-End-URL** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Ersetzen Sie `<YOUR_VM_IP>` durch die IP-Adresse der WordPress-VM, die Sie zuvor erstellt haben. Diese Zuordnung gibt eine einzelne Datei von der Website zurück. |

1. Klicken Sie auf **Erstellen**, um den Proxy zu Ihrer Funktions-App hinzuzufügen.

## <a name="try-it-out"></a>Ausprobieren

1. Versuchen Sie, in Ihrem Browser auf die URL zuzugreifen, die Sie als **Back-End-URL** verwendet haben. Wie erwartet tritt ein Timeout der Anforderung auf. Das Timeout tritt auf, weil die WordPress-Website nur mit Ihrem virtuellen Netzwerk und nicht mit dem Internet verbunden ist.

1. Kopieren Sie den Wert für die **Proxy-URL** von Ihrem neuen Proxy, und fügen Sie ihn in die Adressleiste Ihres Browsers ein. Das zurückgegebene Bild stammt von der WordPress-Website, die in Ihrem virtuellen Netzwerk ausgeführt wird.

    ![Von der WordPress-Website zurückgegebene Datei mit Pflanzenbild](./media/functions-create-vnet/plant.png)

Die Funktions-App ist sowohl mit dem Internet als auch mit dem virtuellen Netzwerk verbunden. Der Proxy empfängt einer Anforderung über das öffentliche Internet und fungiert dann als einfacher HTTP-Proxy, um die Anforderung an das verbundene virtuelle Netzwerk weiterzuleiten. Der Proxy leitet dann die Antwort über das öffentliche Internet an Sie zurück.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial dient die WordPress-Website als API, die durch Verwenden eines Proxys in der Funktions-App aufgerufen wird. Dieses Szenario eignet sich gut als Tutorial, da es sich einfach einrichten und visualisieren lässt. Sie könnten auch jede andere API verwenden, die in einem virtuellen Netzwerk bereitgestellt ist. Sie hätten auch eine Funktion mit Code erstellen können, der im virtuellen Netzwerk bereitgestellte APIs aufruft. Ein realistischeres Szenario ist eine Funktion, die Datenclient-APIs verwendet, um eine SQL Server-Instanz aufzurufen, die im virtuellen Netzwerk bereitgestellt ist.

Funktionen, die in einem Premium-Tarif ausgeführt werden, haben dieselbe zugrunde liegende App Service-Infrastruktur wie Web-Apps in PremiumV2-Tarifen. Die gesamte Dokumentation für [Web-Apps in Azure App Service](../app-service/overview.md) gilt daher auch für Funktionen im Premium-Plan.

> [!div class="nextstepaction"]
> [Weitere Informationen zu den Netzwerkoptionen in Functions](./functions-networking-options.md)

[Premium-Plan]: functions-scale.md#premium-plan-public-preview
