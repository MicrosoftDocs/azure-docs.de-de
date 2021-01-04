---
title: 'Schnellstart: Konfigurieren von Azure Bastion und Herstellen einer Verbindung mit einer VM über eine private IP-Adresse und einen Browser'
titleSuffix: Azure Bastion
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie einen Azure Bastion-Host über einen virtuellen Computer erstellen und mit Ihrem Browser über eine private IP-Adresse eine sichere Verbindung mit der VM herstellen können.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021485"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Schnellstart: Herstellen einer sicheren Verbindung mit einer VM über einen Browser und eine private IP-Adresse

Sie können mit Ihrem Browser mithilfe des Azure-Portals und Azure Bastion eine Verbindung mit einem virtuellen Computer (VM) herstellen. Dieser Schnellstartartikel zeigt Ihnen, wie Sie Azure Bastion auf der Grundlage Ihrer VM-Einstellungen konfigurieren und dann über das Portal eine Verbindung mit Ihrer VM herstellen. Die VM benötigt weder öffentliche IP-Adressen, Clientsoftware, Agents noch eine spezielle Konfiguration. Nachdem der Dienst bereitgestellt wurde, ist die RDP/SSH-Umgebung für alle virtuellen Computer des jeweiligen virtuellen Netzwerks verfügbar. Weitere Informationen zu Azure Bastion finden Sie unter [Was ist Azure Bastion?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sollten Sie über keine Organisation verfügen, [können Sie kostenlos eine erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Damit Sie über Ihren Browser mithilfe von Bastion eine Verbindung mit einer VM herstellen können, müssen Sie sich am Azure-Portal anmelden können.

* Ein virtueller Windows-Computer in einem virtuellen Netzwerk Wenn Sie nicht über einen virtuellen Computer verfügen, erstellen Sie einen über [Schnellstart: Erstellen einer VM](../virtual-machines/windows/quick-create-portal.md).

  * Wenn Sie Beispielwerte benötigen, finden Sie diese in den bereitgestellten [Beispielwerten](#values).
  * Wenn Sie bereits über ein virtuelles Netzwerk verfügen, stellen Sie sicher, dass Sie es auf der Registerkarte „Netzwerk“ auswählen, wenn Sie Ihre VM erstellen.
  * Wenn Sie noch nicht über ein virtuelles Netzwerk verfügen, können Sie gleichzeitig mit der VM ein virtuelles Netzwerk erstellen.
  * Sie benötigen keine öffentliche IP-Adresse für diese VM, um eine Verbindung über Azure Bastion herstellen zu können.

* Erforderliche VM-Rollen:
  * Rolle „Leser“ für den virtuellen Computer
  * Rolle „Leser“ für den Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
  
* Erforderliche VM-Ports:
  * Eingehende Ports: RDP (3389)

### <a name="example-values"></a><a name="values"></a>Beispielwerte

Sie können beim Erstellen dieser Konfiguration die folgenden Beispielwerte verwenden oder Ihre eigenen Werte einsetzen.

**Grundlegende VNet- und VM-Werte:**

|**Name** | **Wert** |
| --- | --- |
| Virtueller Computer| TestVM, |
| Ressourcengruppe | TestRG |
| Region | USA, Osten |
| Virtuelles Netzwerk | TestVNet1 |
| Adressraum | 10.0.0.0/16 |
| Subnetze | Front-End: 10.0.0.0/24 |

**Azure Bastion-Werte:**

|**Name** | **Wert** |
| --- | --- |
| Name | TestVNet1-bastion |
| + Subnetzname | AzureBastionSubnet |
| AzureBastionSubnet-Adressen | Ein Subnetz innerhalb Ihres VNet-Adressraums mit einer /27-Subnetzmaske. Beispiel: 10.0.1.0/27.  |
| Öffentliche IP-Adresse |  Neu erstellen |
| Name der öffentlichen IP-Adresse | VNet1BastionPIP  |
| SKU der öffentlichen IP-Adresse |  Standard  |
| Zuweisung  | statischen |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Erstellen eines Bastion-Hosts

Es gibt verschiedene Möglichkeiten, einen Bastionhost zu konfigurieren. In den folgenden Schritten erstellen Sie direkt über Ihre VM einen Bastionhost im Azure-Portal. Wenn Sie einen Host über eine VM erstellen, werden verschiedene Einstellungen automatisch entsprechend Ihres virtuellen Computers und/oder Ihres virtuellen Netzwerks ausgefüllt.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zur VM, mit der Sie eine Verbindung herstellen möchten, und klicken Sie dann auf **Verbinden**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="VM-Einstellungen" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. Wählen Sie in der Dropdownliste die Option **Bastion** aus.
1. Wählen Sie auf der Seite **TestVM | Verbinden** die Option **Bastion verwenden** aus.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Auswählen von „Bastion“" border="false":::

1. Füllen Sie auf der Seite **Bastion** die folgenden Einstellungsfelder aus:

   * **Name**: Name des Bastionhosts.
   * **Subnetz**: Dies ist der virtuelle Netzwerkadressraum, in dem die Bastion-Ressource bereitgestellt wird. Das Subnetz muss mit dem Namen **AzureBastionSubnet** erstellt werden. Verwenden Sie mindestens ein Subnetz der Größe „/27“ oder ein größeres Subnetz („/27“, „/26“, „/25“ usw.).
   * Wählen Sie **Subnetzkonfiguration verwalten** aus.
1. Wählen Sie auf der Seite **Subnetze** die Option **+Subnetz** aus.

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="+ Subnetz":::
    
1. Geben Sie auf der Seite **Subnetz hinzufügen** für **Name** den Wert **AzureBastionSubnet** ein.
   * Wählen Sie als Subnetzadressbereich eine Subnetzadresse aus, die in Ihrem virtuellen Netzwerkadressraum liegt.
   * Passen Sie keine anderen Einstellungen an. Wählen Sie **OK** aus, um die Subnetzänderungen zu akzeptieren und zu speichern.

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="Hinzufügen des Subnetzes":::
1. Klicken Sie in Ihrem Browser auf die Schaltfläche „Zurück“, um zurück zur Seite **Bastion** zu navigieren, und fahren Sie mit dem Angeben der Werte fort.
   * **Öffentliche IP-Adresse:** Belassen Sie dies als **Neu erstellen**.
   * **Öffentliche IP-Adresse**: Der Name der öffentlichen IP-Adressressource.
   * **Zuweisung**: Die Standardeinstellung ist „Statisch“. Sie können keine dynamische Zuweisung für Azure Bastion verwenden.
   * **Ressourcengruppe**: Dieselbe Ressourcengruppe wie die VM.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="Erstellen des Bastionhosts":::
1. Wählen Sie **Erstellen** aus, um den Bastionhost zu erstellen. Azure überprüft Ihre Einstellungen und erstellt anschließend den Host. Die Erstellung und Bereitstellung des Hosts und der zugehörigen Ressourcen dauert ungefähr fünf Minuten.

## <a name="connect"></a><a name="connect"></a>Verbinden

Nachdem Bastion im virtuellen Netzwerk bereitgestellt wurde, wird die Seite „Verbinden“ angezeigt.

1. Geben Sie den Benutzernamen und das Kennwort für Ihren virtuellen Computer ein. Wählen Sie dann **Verbinden** aus.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="Screenshot: Dialogfeld „Verbindung über Azure Bastion herstellen“":::
1. Die RDP-Verbindung zu diesem virtuellen Computer wird direkt im Azure-Portal (über HTML5) über Port 443 und den Bastion-Dienst geöffnet.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="RDP-Verbindung":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie Ihre Arbeit mit dem virtuellen Netzwerk und den virtuellen Computern beendet haben, sollten Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen löschen:

1. Geben Sie oben im Portal den Namen Ihrer Ressourcengruppe im Suchfeld **Suche** ein, und wählen Sie ihn in den Suchergebnissen aus.

1. Wählen Sie die Option **Ressourcengruppe löschen**.

1. Geben Sie unter **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN** Ihre Ressourcengruppe ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Bastionhost für Ihr virtuelles Netzwerk erstellt und anschließend über Bastion eine sichere Verbindung mit einem virtuellen Computer hergestellt. Anschließend können Sie mit dem folgenden Schritt fortfahren, wenn Sie eine Verbindung mit einer VM-Skalierungsgruppe herstellen möchten.

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer VM-Skalierungsgruppe mit Azure Bastion](bastion-connect-vm-scale-set.md)
