---
title: Aktivieren von privatem Websitezugriff für Azure Functions
description: Hier erfahren Sie, wie Sie den privaten Websitezugriff von virtuellen Azure-Netzwerken für Azure Functions einrichten.
author: craigshoemaker
ms.author: cshoe
ms.service: azure-functions
ms.topic: tutorial
ms.date: 06/17/2020
ms.openlocfilehash: 75e3886e31592b0672487bacd5ff2266e07e39cd
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182501"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Tutorial: Einrichten von privatem Websitezugriff für Azure Functions

In diesem Tutorial erfahren Sie, wie Sie den [privaten Websitezugriff](./functions-networking-options.md#private-endpoint-connections) mit Azure Functions aktivieren. Mit dem privaten Websitezugriff können Sie sicherstellen, dass Ihr Funktionscode nur von einem bestimmten virtuellen Netzwerk aus ausgelöst wird.

Der private Websitezugriff ist hilfreich in Szenarien, in denen der Zugriff auf die Funktions-App auf ein bestimmtes virtuelles Netzwerk beschränkt werden muss. Dies kann beispielsweise der Fall sein, wenn die Funktions-App nur für Mitarbeiter einer bestimmten Organisation oder nur für Dienste innerhalb des angegebenen virtuellen Netzwerks vorgesehen ist (etwa für eine andere Azure-Funktion, einen virtuellen Azure-Computer oder einen AKS-Cluster).

Wenn eine Funktions-App auf Azure-Ressourcen innerhalb des virtuellen Netzwerks zugreifen oder über [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) verbunden werden muss, ist eine [VNET-Integration](./functions-create-vnet.md) erforderlich.

In diesem Tutorial erfahren Sie, wie Sie den privaten Websitezugriff für Ihre Funktions-App konfigurieren:

> [!div class="checklist"]
> * Erstellen eines virtuellen Computers
> * Erstellen eines Azure Bastion-Diensts
> * Erstellen einer Azure Functions-App
> * Konfigurieren eines VNET-Dienstendpunkts
> * Erstellen und Bereitstellen einer Azure-Funktion
> * Aufrufen der Funktion außerhalb und innerhalb des virtuellen Netzwerks

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="topology"></a>Topologie

Das folgende Diagramm zeigt die Architektur der zu erstellenden Lösung:

![Allgemeines Architekturdiagramm der Lösung für privaten Websitezugriff](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial ist es wichtig, dass Sie die Funktionsweise und Verwendung von IP-Adressen und Subnetzen verstehen. Beginnen Sie mit [diesem Artikel zu den Grundlagen von Adressen und Subnetzen](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Viele weitere Artikel und Videos finden Sie online.

## <a name="sign-in-to-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Im ersten Schritt dieses Tutorials wird ein neuer virtueller Computer in einem virtuellen Netzwerk erstellt.  Der virtuelle Computer wird für den Zugriff auf Ihre Funktion verwendet, nachdem Sie den Zugriff so eingeschränkt haben, dass nur innerhalb des virtuellen Netzwerks darauf zugegriffen werden kann.

1. Wählen Sie die Schaltfläche **Ressource erstellen** aus.

1. Geben Sie in das Suchfeld **Windows Server** ein, und wählen Sie in den Suchergebnissen **Windows Server** aus.

1. Wählen Sie in der Liste mit den Windows Server-Optionen **Windows Server 2019 Datacenter** und anschließend die Schaltfläche **Erstellen** aus.

1. Verwenden Sie auf der Registerkarte _Grundlagen_ die VM-Einstellungen, die Sie in der Tabelle unterhalb der Abbildung finden:

    >[!div class="mx-imgBorder"]
    >![Registerkarte „Grundeinstellungen“ für einen neuen virtuellen Windows-Computer](./media/functions-create-private-site-access/create-vm-3.png)

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | _Abonnement_ | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. |
    | [_Ressourcengruppe_](../azure-resource-manager/management/overview.md) | myResourceGroup | Wählen Sie die Ressourcengruppe aus, die die Ressourcen für dieses Tutorial enthalten soll.  Die Verwendung der gleichen Ressourcengruppe vereinfacht die Bereinigung der Ressourcen am Ende des Tutorials. |
    | _Name des virtuellen Computers_ | myVM | Der VM-Name muss in der Ressourcengruppe eindeutig sein. |
    | [_Region_](https://azure.microsoft.com/regions/) | (US) „USA, Norden-Mitte“ | Wählen Sie eine Region in Ihrer Nähe oder in der Nähe der Funktionen aus, auf die zugegriffen werden soll. |
    | _Öffentliche Eingangsports_ | Keine | Wählen Sie **Keine** aus, um sicherzustellen, dass keine eingehende Internetkonnektivität für den virtuellen Computer vorhanden ist. Der Remotezugriff auf den virtuellen Computer wird über den Azure Bastion-Dienst konfiguriert. |

1. Wählen Sie die Registerkarte _Netzwerk_ und anschließend **Neu erstellen** aus, um ein neues virtuelles Netzwerk zu konfigurieren.

    >[!div class="mx-imgBorder"]
    >![Screenshot, der die Registerkarte „Netzwerk“ mit hervorgehobener Aktion „Neu erstellen“ im Abschnitt „Virtuelles Netzwerk“ zeigt.](./media/functions-create-private-site-access/create-vm-networking.png)

1. Verwenden Sie unter _Virtuelles Netzwerk erstellen_ die Einstellungen in der Tabelle unterhalb der Abbildung:

    >[!div class="mx-imgBorder"]
    >![Erstellen eines neuen virtuellen Netzwerks für den neuen virtuellen Computer](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Einstellung      | Vorgeschlagener Wert  | Beschreibung      |
    | ------------ | ---------------- | ---------------- |
    | _Name_ | myResourceGroup-vnet | Sie können den für Ihr virtuelles Netzwerk generierten Standardnamen verwenden. |
    | _Adressbereich_ | 10.10.0.0/16 | Verwenden Sie einen einzelnen Adressbereich für das virtuelle Netzwerk. |
    | _Subnetzname_ | Lernprogramm | Der Name des Subnetzes. |
    | _Adressbereich_ (Subnetz) | 10.10.1.0/24 | Die Subnetzgröße definiert, wie viele Schnittstellen zum Subnetz hinzugefügt werden können. Dieses Subnetz wird von dem virtuellen Computer verwendet. Ein /24-Subnetz bietet 254 Hostadressen. |

1. Klicken Sie auf **OK**, um das virtuelle Netzwerk zu erstellen.
1. Vergewissern Sie sich auf der Registerkarte _Netzwerk_, dass für _Öffentliche IP_ die Option **Keine** ausgewählt ist.
1. Wählen Sie die Registerkarte _Verwaltung_ und anschließend unter _Diagnosespeicherkonto_ die Option **Neu erstellen** aus, um ein neues Speicherkonto zu erstellen.
1. Behalten Sie für _Identität_, _Automatisch herunterfahren_ und _Sicherung_ die Standardwerte bei.
1. Klicken Sie auf _Überprüfen + erstellen_. Wenn die Überprüfung abgeschlossen ist, wählen Sie **Erstellen** aus. Der Vorgang der VM-Erstellung dauert einige Minuten.

## <a name="configure-azure-bastion"></a>Konfigurieren von Azure Bastion

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) ist ein vollständig verwalteter Azure-Dienst für sicheren RDP- und SSH-Zugriff auf virtuelle Computer direkt über das Azure-Portal. Bei Verwendung des Azure Bastion-Diensts müssen keine Netzwerkeinstellungen für den RDP-Zugriff konfiguriert werden.

1. Wählen Sie im Portal im oberen Bereich der Ressourcengruppenansicht die Option **Hinzufügen** aus.
1. Geben Sie in das Suchfeld **Bastion** ein.
1. Wählen Sie in den Suchergebnissen **Bastion** aus.
1. Wählen Sie **Erstellen** aus, um mit der Erstellung einer neuen Azure Bastion-Ressource zu beginnen. Im Abschnitt _Virtuelles Netzwerk_ wird eine Fehlermeldung angezeigt, da noch kein AzureBastionSubnet-Subnetz vorhanden ist. Das Subnetz wird in den folgenden Schritten erstellt. Verwenden Sie die Einstellungen aus der Tabelle unterhalb der Abbildung:

    >[!div class="mx-imgBorder"]
    >![Beginn der Azure Bastion-Erstellung](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Einstellung      | Vorgeschlagener Wert  | Beschreibung      |
    | ------------ | ---------------- | ---------------- |
    | _Name_ | myBastion | Der Name der neuen Bastion-Ressource. |
    | _Region_ | USA Nord Mitte | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe oder in der Nähe von anderen Diensten aus, auf die Ihre Funktionen zugreifen. |
    | _Virtuelles Netzwerk_ | myResourceGroup-vnet | Das virtuelle Netzwerk, in dem die Bastion-Ressource erstellt wird. |
    | _Subnetz_ | AzureBastionSubnet | Das Subnetz in Ihrem virtuellen Netzwerk, in dem die neue Bastion-Hostressource bereitgestellt wird. Sie müssen ein Subnetz mit dem Namen **AzureBastionSubnet** erstellen. Dadurch weiß Azure, in welchem Subnetz die Bastion-Ressourcen bereitgestellt werden sollen. Sie müssen ein Subnetz von mindestens **/27** oder größer („/27“, „/26“ usw.) verwenden. |

    > [!NOTE]
    > Eine ausführliche Schritt-für-Schritt-Anleitung zum Erstellen einer Azure Bastion-Ressource finden Sie im Tutorial [Erstellen eines Azure Bastion-Hosts](../bastion/tutorial-create-host-portal.md).

1. Erstellen Sie ein Subnetz, in dem Azure den Azure Bastion-Host bereitstellen kann. Wenn Sie **Subnetzkonfiguration verwalten** auswählen, wird ein neuer Bereich geöffnet, in dem Sie ein neues Subnetz definieren können.  Wählen Sie **+ Subnetz** aus, um ein neues Subnetz zu erstellen.
1. Das Subnetz muss den Namen **AzureBastionSubnet** haben, und das Subnetzpräfix muss mindestens **/27** sein.  Klicken Sie auf **OK**, um das Subnetz zu erstellen.

    >[!div class="mx-imgBorder"]
    >![Erstellen eines Subnetzes für einen Azure Bastion-Host](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

1. Wählen Sie auf der Seite _Bastion-Instanz erstellen_ in der Liste mit den verfügbaren Subnetzen das neu erstellte **AzureBastionSubnet**-Subnetz aus.

    >[!div class="mx-imgBorder"]
    >![Erstellen eines Azure Bastion-Hosts mit einem bestimmten Subnetz](./media/functions-create-private-site-access/create-bastion-basics-2.png)

1. Wählen Sie **Überprüfen + erstellen** aus. Wählen Sie nach Abschluss der Überprüfung **Erstellen** aus. Die Erstellung der Azure Bastion-Ressource dauert einige Minuten.

## <a name="create-an-azure-functions-app"></a>Erstellen einer Azure Functions-App

Im nächsten Schritt wird in Azure eine Funktions-App mit [Verbrauchsplan](functions-scale.md#consumption-plan) erstellt. Später in diesem Tutorial wird Ihr Funktionscode für diese Ressource bereitgestellt.

1. Wählen Sie im Portal im oberen Bereich der Ressourcengruppenansicht die Option **Hinzufügen** aus.
1. Wählen Sie **Compute > Funktions-App** aus.
1. Verwenden Sie im Abschnitt _Grundeinstellungen_ die Funktions-App-Einstellungen aus der folgenden Tabelle:

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | _Ressourcengruppe_ | myResourceGroup | Wählen Sie die Ressourcengruppe aus, die die Ressourcen für dieses Tutorial enthalten soll.  Die Verwendung der gleichen Ressourcengruppe für die Funktions-App und den virtuellen Computer vereinfacht die Bereinigung der Ressourcen am Ende des Tutorials. |
    | _Name der Funktions-App_ | Global eindeutiger Name | Der Name, der Ihre neue Funktionen-App bezeichnet Gültige Zeichen sind a–z (unabhängig von der Groß-/Kleinschreibung), 0–9 und -. |
    | _Veröffentlichen_ | Code | Option zum Veröffentlichen von Codedateien oder eines Docker-Containers. |
    | _Laufzeitstapel_ | Bevorzugte Sprache | Wählen Sie eine Runtime aus, die Ihre bevorzugte Programmiersprache für Funktionen unterstützt. |
    | _Region_ | USA Nord Mitte | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe oder in der Nähe von anderen Diensten aus, auf die Ihre Funktionen zugreifen. |

    Wählen Sie unten auf der Seite die Schaltfläche **Next: Hosting >** aus.
1. Wählen Sie im Abschnitt _Hosting_ die passenden Werte für _Speicherkonto_, _Betriebssystem_ und _Tarif_ aus. Orientieren Sie sich dabei an den Angaben in der folgenden Tabelle.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | _Speicherkonto_ | Global eindeutiger Name | Erstellen Sie ein Speicherkonto, das von Ihrer Funktions-App verwendet wird. Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Sie können auch ein vorhandenes Konto verwenden, das die [Anforderungen an das Speicherkonto](./functions-scale.md#storage-account-requirements) erfüllen muss. |
    | _Betriebssystem_ | Bevorzugtes Betriebssystem | Ein Betriebssystem ist für Sie basierend auf Ihrer Runtimestapelauswahl vorab ausgewählt, aber Sie können die Einstellung ggf. ändern. |
    | _Planen_ | Nutzung | Der [Hostingplan](./functions-scale.md) bestimmt die Skalierung der Funktions-App und die Ressourcenverfügbarkeit für die jeweilige Instanz. |
1. Wählen Sie **Überprüfen + erstellen** aus, um die App-Konfigurationsauswahl zu überprüfen.
1. Klicken Sie auf **Erstellen**, um die Funktionen-App bereitzustellen.

## <a name="configure-access-restrictions"></a>Konfigurieren der Zugriffseinschränkungen

Im nächsten Schritt werden [Zugriffseinschränkungen](../app-service/app-service-ip-restrictions.md) konfiguriert, um sicherzustellen, dass die Funktion nur von Ressourcen im virtuellen Netzwerk aufgerufen werden kann.

Zur Ermöglichung von [privatem Websitezugriff](functions-networking-options.md#private-endpoint-connections) wird ein Azure Virtual Network-[Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) zwischen der Funktions-App und dem angegebenen virtuellen Netzwerk erstellt. Zugriffseinschränkungen werden mithilfe von Dienstendpunkten implementiert. Dienstendpunkte sorgen dafür, dass nur von Datenverkehr aus dem angegebenen virtuellen Netzwerk auf die angegebene Ressource zugegriffen werden kann. In diesem Fall ist die Azure-Funktion die angegebene Ressource.

1. Wählen Sie in der Funktions-App den Link **Netzwerk** unter der Abschnittsüberschrift _Einstellungen_ aus.
1. Die Seite _Netzwerk_ ist der Ausgangspunkt für die Konfiguration von Azure Front Door, Azure CDN und Zugriffseinschränkungen.
1. Wählen Sie **Zugriffseinschränkungen konfigurieren** aus, um den privaten Websitezugriff zu konfigurieren.
1. Auf der Seite _Zugriffseinschränkungen_ ist zunächst nur die Standardeinschränkung vorhanden. Die Standardeinschränkung bewirkt keinerlei Zugriffseinschränkungen für die Funktions-App.  Wählen Sie **Regel hinzufügen** aus, um eine Einschränkungskonfiguration für den privaten Websitezugriff zu erstellen.
1. Geben Sie im Bereich _Zugriffsbeschränkung hinzufügen_ einen _Namen_, eine _Priorität_ und eine _Beschreibung_ für die neue Regel an.
1. Wählen Sie im Dropdownfeld _Typ_ die Option **Virtuelles Netzwerk** und anschließend das zuvor erstellte virtuelle Netzwerk und anschließend das **Tutorial**-Subnetz aus. 
    > [!NOTE]
    > Es kann einige Minuten dauern, bis der Dienstendpunkt aktiviert wird.
1. Auf der Seite _Zugriffseinschränkungen_ wird nun angezeigt, dass eine neue Einschränkung vorhanden ist. Es kann einige Sekunden dauern, bis der Wert von _Endpunktstatus_ von „Deaktiviert“ über „Bereitstellung“ zu „Aktiviert“ wechselt.

    >[!IMPORTANT]
    > Jede Funktions-App verfügt über eine [Kudu-Website](../app-service/app-service-ip-restrictions.md#restrict-access-to-an-scm-site) mit erweiterten Tools zur Verwaltung von Funktions-App-Bereitstellungen. Auf diese Website wird über eine URL wie die folgende zugegriffen: `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Durch das Aktivieren von Zugriffsbeschränkungen auf der Kudu-Website wird verhindert, dass der Projektcode von einer lokalen Entwicklerarbeitsstation aus bereitgestellt wird. Anschließend wird ein Agent innerhalb des virtuellen Netzwerks benötigt, um die Bereitstellung auszuführen.

## <a name="access-the-functions-app"></a>Zugreifen auf die Funktions-App

1. Kehren Sie zu der zuvor erstellten Funktions-App zurück.  Kopieren Sie im Abschnitt _Übersicht_ die URL.

    >[!div class="mx-imgBorder"]
    >![Abrufen der URL der Funktions-App](./media/functions-create-private-site-access/access-function-overview.png)

    Wenn Sie nun auf Ihrem Computer außerhalb des virtuellen Netzwerks versuchen, auf die Funktions-App zuzugreifen, wird eine HTTP 403-Seite mit dem Hinweis angezeigt, dass der Zugriff unzulässig ist.
1. Wechseln Sie zurück zur Ressourcengruppe, und wählen Sie den zuvor erstellten virtuellen Computer aus. Um von dem virtuellen Computer aus auf die Website zugreifen zu können, müssen Sie über den Azure Bastion-Dienst eine Verbindung mit dem virtuellen Computer herstellen.
1. Wählen Sie **Verbinden** und dann **Bastion** aus.
1. Geben Sie den erforderlichen Benutzernamen und das entsprechende Kennwort an, um sich bei dem virtuellen Computer anzumelden.
1. Wählen Sie **Verbinden**. Daraufhin wird ein neues Browserfenster angezeigt, in dem Sie mit dem virtuellen Computer interagieren können.
Es ist möglich, über den Webbrowser des virtuellen Computers auf die Website zuzugreifen, weil der virtuelle Computer über das virtuelle Netzwerk auf die Website zugreift.  Zwar kann nur innerhalb des angegebenen virtuellen Netzwerks zugegriffen werden, doch bleibt weiterhin ein öffentlicher DNS-Eintrag vorhanden.

## <a name="create-a-function"></a>Erstellen einer Funktion

Im nächsten Schritt dieses Tutorials wird eine Azure-Funktion mit HTTP-Trigger erstellt. Wenn diese Funktion über eine HTTP GET- oder POST-Anforderung aufgerufen wird, sollte „Hello {name}“ zurückgegeben werden.  

1. Verwenden Sie eine der folgenden Schnellstartanleitungen, um Ihre Azure Functions-App zu erstellen und bereitzustellen:

    * [Visual Studio Code](./create-first-function-vs-code-csharp.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Befehlszeile](./create-first-function-cli-csharp.md)
    * [Maven (Java)](./create-first-function-cli-java.md?tabs=bash,browser)

1. Wählen Sie beim Veröffentlichen Ihres Azure Functions-Projekts die Funktions-App-Ressource aus, die Sie weiter oben in diesem Tutorial erstellt haben.
1. Vergewissern Sie sich, dass die Funktion bereitgestellt wurde.

    >[!div class="mx-imgBorder"]
    >![Bereitgestellte Funktion in der Liste der Funktionen](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Direktes Aufrufen der Funktion

1. Um den Zugriff auf die Funktion zu testen, müssen Sie die URL der Funktion kopieren. Wählen Sie die bereitgestellte Funktion und anschließend **Funktions-URL abrufen** aus. Klicken Sie auf die Schaltfläche **Kopieren**, um die URL in die Zwischenablage zu kopieren.

    >[!div class="mx-imgBorder"]
    >![Kopieren Sie die Funktions-URL](./media/functions-create-private-site-access/get-function-url.png)

1. Fügen Sie die URL in den Webbrowser ein. Wenn Sie nun auf einem Computer außerhalb Ihres virtuellen Netzwerks versuchen, auf die Funktions-App zuzugreifen, wird eine HTTP 403-Antwort mit dem Hinweis angezeigt, dass der Zugriff auf die App unzulässig ist.

## <a name="invoke-the-function-from-the-virtual-network"></a>Aufrufen der Funktion innerhalb des virtuellen Netzwerks

Wenn Sie auf dem konfigurierten virtuellen Computer innerhalb des virtuellen Netzwerks über einen Webbrowser (unter Verwendung des Azure Bastion-Diensts) auf die Funktion zugreifen, ist der Zugriff erfolgreich.

>[!div class="mx-imgBorder"]
>![Zugreifen auf die Azure-Funktion über Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nächste Schritte


> [!div class="nextstepaction"]
> [Weitere Informationen zu den Netzwerkoptionen in Functions](./functions-networking-options.md)