---
title: Einschränken des Zugriffs auf PaaS-Ressourcen – Tutorial – Azure-Portal
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe des Azure-Portals den Netzwerkzugriff auf Azure-Ressourcen wie Azure Storage und Azure SQL-Datenbank mit VNET-Dienstendpunkten einschränken können.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/23/2018
ms.author: kumud
ms.openlocfilehash: e6716d2ad4252169cfbbf611b0dadc5b077cd362
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042558"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Tutorial: Einschränken des Netzwerkzugriffs auf PaaS-Ressourcen mit VNET-Dienstendpunkten mithilfe des Azure-Portals

VNET-Dienstendpunkte ermöglichen es Ihnen, den Netzwerkzugriff auf einige Azure-Dienstressourcen auf ein Subnetz eines virtuellen Netzwerks einzuschränken. Sie können auch den Internetzugriff auf die Ressourcen entfernen. Dienstendpunkte ermöglichen eine direkte Verbindung zwischen Ihrem virtuellen Netzwerk und unterstützten Azure-Diensten, sodass Sie mithilfe des privaten Adressraums Ihres virtuellen Netzwerks auf die Azure-Dienste zugreifen können. Datenverkehr, der über Dienstendpunkte für Azure-Ressourcen bestimmt ist, verbleibt immer im Microsoft Azure-Backbonenetzwerk. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks mit einem Subnetz
> * Hinzufügen eines Subnetzes und Aktivieren eines Dienstendpunkts
> * Erstellen einer Azure-Ressource und Zulassen des Netzwerkzugriffs darauf ausschließlich aus einem Subnetz
> * Bereitstellen eines virtuellen Computers für jedes Subnetz
> * Bestätigen des Zugriffs auf eine Ressource aus einem Subnetz
> * Bestätigen, dass der Zugriff auf eine Ressource aus einem Subnetz und dem Internet verweigert wird

Dieser Artikel kann auch mit der [Azure-Befehlszeilenschnittstelle](tutorial-restrict-network-access-to-resources-cli.md) oder mit [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md) durchgearbeitet werden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie **Netzwerk** und anschließend **Virtuelles Netzwerk** aus.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie dann **Erstellen** aus:

   |Einstellung|Wert|
   |----|----|
   |Name| myVirtualNetwork |
   |Adressraum| 10.0.0.0/16|
   |Subscription| Wählen Sie Ihr Abonnement aus.|
   |Resource group | Klicken Sie auf **Neu erstellen**, und geben Sie *myResourceGroup* ein.|
   |Standort| Wählen Sie **USA, Osten** aus. |
   |Subnetzname| Öffentlich|
   |Subnetzadressbereich| 10.0.0.0/24|
   |DDoS-Schutz| Basic|
   |Dienstendpunkte| Disabled|
   |Firewall| Disabled|

   ![Eingeben grundlegender Informationen zu Ihrem virtuellen Netzwerk](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

## <a name="enable-a-service-endpoint"></a>Aktivieren eines Dienstendpunkts

Dienstendpunkte werden pro Dienst und pro Subnetz aktiviert. Erstellen Sie ein Subnetz, und fügen Sie einen Dienstendpunkt für das Subnetz hinzu.

1. Geben Sie oben im Portal im Feld **Ressourcen, Dienste und Dokumente durchsuchen** *myVirtualNetwork* ein. Wenn **myVirtualNetwork** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen.
2. Hinzufügen eines Subnetzes zum virtuellen Netzwerk Klicken Sie unter **EINSTELLUNGEN** auf **Subnetze** und anschließend auf **+ Subnetz**, wie in der folgenden Abbildung gezeigt:

    ![Hinzufügen des Subnetzes](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Wählen Sie unter **Subnetz hinzufügen** die folgenden Informationen aus, oder geben Sie sie ein, und klicken Sie auf **OK**:

    |Einstellung|Wert|
    |----|----|
    |Name| Privat |
    |Adressbereich| 10.0.1.0/24|
    |Dienstendpunkte| Wählen Sie unter **Dienste** **Microsoft.Storage** aus.|

> [!CAUTION]
> Lesen Sie vor der Aktivierung eines Dienstendpunkts für ein vorhandenes Subnetz, das Ressourcen enthält, die Informationen unter [Ändern von Subnetzeinstellungen](virtual-network-manage-subnet.md#change-subnet-settings).

## <a name="restrict-network-access-for-a-subnet"></a>Einschränken des Netzwerkzugriffs für ein Subnetz

Standardmäßig können alle virtuellen Computer in einem Subnetz mit allen Ressourcen kommunizieren. Sie können die bidirektionale Kommunikation mit allen Ressourcen in einem Subnetz einschränken, indem Sie eine Netzwerksicherheitsgruppe erstellen und dem Subnetz zuordnen.

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie die Option **Netzwerk** und dann **Netzwerksicherheitsgruppe** aus.
3. Geben Sie unter **Netzwerksicherheitsgruppe erstellen** die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie dann **Erstellen** aus:

    |Einstellung|Wert|
    |----|----|
    |Name| myNsgPrivate |
    |Subscription| Wählen Sie Ihr Abonnement aus.|
    |Resource group | Wählen Sie **Vorhandene verwenden** und dann *myResourceGroup* aus.|
    |Standort| Wählen Sie **USA, Osten** aus. |

4. Nachdem die Sicherheitsgruppe erstellt wurde geben Sie im Feld **Ressourcen, Dienste und Dokumente durchsuchen** oben im Portal *myNsgPrivate* ein. Wenn **myNsgPrivate** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
5. Wählen Sie unter **EINSTELLUNGEN** **Ausgangssicherheitsregeln** aus.
6. Wählen Sie **+ Hinzufügen**.
7. Erstellen Sie eine Regel, die ausgehende Kommunikation mit dem Azure Storage-Dienst zulässt. Geben Sie die folgenden Informationen ein (oder wählen Sie sie aus), und klicken Sie anschließend auf **Hinzufügen**:

    |Einstellung|Wert|
    |----|----|
    |`Source`| Wählen Sie **VirtualNetwork** aus. |
    |Source port ranges| * |
    |Destination | Wählen Sie **Diensttag** aus.|
    |Zieldiensttag | Wählen Sie **Storage** aus.|
    |Zielportbereiche| * |
    |Protocol|Any|
    |Aktion|Allow|
    |Priority|100|
    |Name|Allow-Storage-All|

8. Erstellen Sie eine weitere Ausgangssicherheitsregel, die Kommunikation mit dem Internet verweigert. Diese Regel überschreibt eine Standardregel in allen Netzwerksicherheitsgruppen, die ausgehende Internetkommunikation zulässt. Wiederholen Sie die Schritte 5–7 mit folgenden Werten:

    |Einstellung|Wert|
    |----|----|
    |`Source`| Wählen Sie **VirtualNetwork** aus. |
    |Source port ranges| * |
    |Destination | Wählen Sie **Diensttag** aus.|
    |Zieldiensttag| Wählen Sie **Internet** aus.|
    |Zielportbereiche| * |
    |Protocol|Any|
    |Aktion|Verweigern|
    |Priority|110|
    |Name|Deny-Internet-All|

9. Wählen Sie unter **EINSTELLUNGEN** **Eingangssicherheitsregeln** aus.
10. Wählen Sie **+ Hinzufügen**.
11. Erstellen Sie eine Eingangssicherheitsregel, die RDP-Datenverkehr (Remote Desktop Protocol) an das Subnetz von überall erlaubt. Die Regel setzt eine Standardsicherheitsregel außer Kraft, die jeglichen eingehenden Verkehr aus dem Internet abweist. Remotedesktopverbindungen in das Subnetz sind zulässig, sodass die Konnektivität in einem späteren Schritt getestet werden kann. Klicken Sie unter **EINSTELLUNGEN** auf **Eingangssicherheitsregeln** > **+ Hinzufügen**, geben Sie die folgenden Werte ein, und klicken Sie anschließend auf **Hinzufügen**:

    |Einstellung|Wert|
    |----|----|
    |`Source`| Any |
    |Source port ranges| * |
    |Destination | Wählen Sie **VirtualNetwork** aus.|
    |Zielportbereiche| 3389 |
    |Protocol|Any|
    |Aktion|Allow|
    |Priority|120|
    |Name|Allow-RDP-All|

12. Wählen Sie unter **EINSTELLUNGEN** die Option **Subnetze** aus.
13. Wählen Sie **+ Zuordnen** aus.
14. Wählen Sie unter **Subnetz zuordnen**, dann **Virtuelles Netzwerk** und dann unter **Virtuelles Netzwerk auswählen** **myVirtualNetwork** aus.
15. Wählen Sie unter **Subnetz auswählen** **Private** und dann **OK** aus.

## <a name="restrict-network-access-to-a-resource"></a>Einschränken des Netzwerkzugriffs auf eine Ressource

Die Schritte, die erforderlich sind, um den Netzwerkzugriff auf Ressourcen einzuschränken, die durch Azure-Dienste erstellt und für Dienstendpunkte aktiviert wurden, sind je nach Dienst unterschiedlich. Informationen zu den Schritten für einzelne Dienste finden Sie in der Dokumentation des jeweiligen Diensts. Im weiteren Verlauf dieses Tutorials wird als Beispiel der Netzwerkzugriff für ein Azure Storage-Konto eingeschränkt.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie die Option **Speicher** und anschließend **Speicherkonto – Blob, Datei, Tabelle, Warteschlange** aus.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die verbleibenden Standardeinstellungen, und wählen Sie dann **Erstellen** aus:

    |Einstellung|Wert|
    |----|----|
    |Name| Geben Sie einen Namen ein, der an allen Azure-Standorten eindeutig, zwischen 3 und 24 Zeichen lang ist und nur aus Ziffern und Kleinbuchstaben besteht.|
    |Kontoart|StorageV2 (allgemein, Version 2)|
    |Standort| Wählen Sie **USA, Osten** aus. |
    |Replikation| Lokal redundanter Speicher (LRS)|
    |Subscription| Wählen Sie Ihr Abonnement aus.|
    |Resource group | Wählen Sie **Vorhandene verwenden** und dann *myResourceGroup* aus.|

### <a name="create-a-file-share-in-the-storage-account"></a>Erstellen einer Dateifreigabe im Speicherkonto

1. Geben Sie nach dem Erstellen des Speicherkontos oben im Portal im Feld **Ressourcen, Dienste und Dokumente durchsuchen** den Namen des Speicherkontos ein. Wenn der Name Ihres Speicherkontos in den Suchergebnissen angezeigt wird, wählen Sie ihn aus.
2. Klicken Sie auf **Dateien**, wie in der folgenden Abbildung gezeigt:

   ![Speicherkonto](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Klicken Sie auf **+ Dateifreigabe**.
4. Geben Sie unter **Name** *my-file-share* ein, und wählen Sie dann **OK** aus.
5. Schließen Sie das Feld **Dateidienst**.

### <a name="restrict-network-access-to-a-subnet"></a>Einschränken des Netzwerkzugriffs auf ein Subnetz

Standardmäßig akzeptieren Speicherkonten Netzwerkverbindungen von Clients in allen Netzwerken, einschließlich des Internets. Verweigern Sie den Zugriff über das Internet sowie alle anderen Subnetze in allen virtuellen Netzwerken außer über das Subnetz *Private* im virtuellen Netzwerk *myVirtualNetwork*.

1. Wählen Sie unter **EINSTELLUNGEN** für das Speicherkonto **Firewalls und virtuelle Netzwerke** aus.
2. Klicken Sie auf **Ausgewählte Netzwerke**.
3. Klicken Sie auf **+Vorhandenes virtuelles Netzwerk hinzufügen**.
4. Wählen Sie unter **Netzwerke hinzufügen** die folgenden Werte und dann **Hinzufügen** aus:

    |Einstellung|Wert|
    |----|----|
    |Subscription| Wählen Sie Ihr Abonnement aus.|
    |Virtuelle Netzwerke|Wählen Sie unter **Virtuelle Netzwerke** **myVirtualNetwork** aus.|
    |Subnetze| Wählen Sie unter **Subnetze** **Private** aus.|

    ![Screenshot: Bereich „Netzwerke hinzufügen“, in dem Sie die angegebenen Werte eingeben können](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png)

5. Wählen Sie **Speichern** aus.
6. Schließen Sie das Feld **Firewalls und virtuelle Netzwerke**.
7. Wählen Sie unter **EINSTELLUNGEN** für das Speicherkonto **Zugriffsschlüssel** aus, wie in der folgenden Abbildung dargestellt:

      ![Screenshot, auf dem unter „Einstellungen“ die Option „Zugriffsschlüssel“ ausgewählt ist, unter der Sie einen Schlüssel abrufen können](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Notieren Sie den Wert des **Schlüssels**, da Sie ihn beim Zuordnen der Dateifreigabe zu einem Laufwerksbuchstaben in einer VM in einem späteren Schritt manuell eingeben müssen.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Zum Testen des Netzwerkzugriffs auf ein Speicherkonto stellen Sie einen virtuellen Computer für jedes Subnetz bereit.

### <a name="create-the-first-virtual-machine"></a>Erstellen des ersten virtuellen Computers

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie **Compute** und dann **Windows Server 2016 Datacenter**.
3. Geben Sie die folgenden Informationen ein (oder wählen Sie sie aus), und klicken Sie anschließend auf **OK**:

   |Einstellung|Wert|
   |----|----|
   |Name| myVmPublic|
   |Benutzername|Geben Sie den gewünschten Benutzernamen ein.|
   |Kennwort| Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
   |Subscription| Wählen Sie Ihr Abonnement aus.|
   |Resource group| Wählen Sie **Vorhandene verwenden** und dann **myResourceGroup** aus.|
   |Standort| Wählen Sie **USA, Osten** aus.|

   ![Eingeben von grundlegenden Informationen zu einem virtuellen Computer](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Wählen Sie eine Größe für den virtuellen Computer aus, und wählen Sie dann **Auswählen** aus.
5. Wählen Sie unter **Einstellungen** **Netzwerk** und dann **myVirtualNetwork** aus. Wählen Sie dann **Subnetz** und **Public** aus, wie in der folgenden Abbildung zu sehen:

   ![Auswählen eines virtuellen Netzwerks](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)

6. Wählen Sie unter **Netzwerksicherheitsgruppe** die Option **Erweitert**. Das Portal erstellt automatisch eine Netzwerksicherheitsgruppe, die den Port 3389 zulässt. Dieser Port muss geöffnet werden, um in einem späteren Schritt eine Verbindung mit dem virtuellen Computer herstellen zu können. Klicken Sie auf der Seite **Einstellungen** auf **OK**.
7. Wählen Sie auf der Seite **Zusammenfassung** die Option **Erstellen** aus, um die Bereitstellung des virtuellen Computers zu starten. Die Bereitstellung der VM nimmt einige Minuten in Anspruch, Sie können aber während der Erstellung der VM bereits mit dem nächsten Schritt fortfahren.

### <a name="create-the-second-virtual-machine"></a>Erstellen des zweiten virtuellen Computers

Wiederholen Sie die Schritte 1–7, nennen Sie den virtuellen Computer in Schritt 3 aber *myVmPrivate*, und wählen Sie in Schritt 5 das Subnetz **Private** aus.

Die Bereitstellung des virtuellen Computers dauert einige Minuten. Fahren Sie erst mit dem nächsten Schritt fort, nachdem die Erstellung abgeschlossen wurde und seine Einstellungen sich im Portal geöffnet haben.

## <a name="confirm-access-to-storage-account"></a>Bestätigen des Zugriffs auf das Speicherkonto

1. Sobald die Erstellung des virtuellen Computers *myVmPrivate* abgeschlossen ist, öffnet Azure seine Einstellungen. Stellen Sie eine Verbindung mit der VM her, indem Sie die Schaltfläche **Verbinden** auswählen, wie in der folgenden Abbildung gezeigt:

   ![Herstellen einer Verbindung mit einem virtuellen Computer](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Nachdem Sie die Schaltfläche **Verbinden** ausgewählt haben, wird eine RDP-Datei (Remotedesktopprotokoll) erstellt und auf Ihren Computer heruntergeladen.  
3. Öffnen Sie die heruntergeladene RDP-Datei. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben. Unter Umständen müssen Sie auf **Weitere Optionen** und anschließend auf **Anderes Konto verwenden** klicken, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben. 
4. Klicken Sie auf **OK**.
5. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wenn eine Warnung angezeigt wird, klicken Sie auf **Ja** bzw. **Weiter**, um mit dem Herstellen der Verbindung fortzufahren.
6. Ordnen Sie auf dem virtuellen Computer *myVmPrivate* mithilfe von PowerShell die Azure-Dateifreigabe dem Laufwerk Z zu. Ersetzen Sie vor dem Ausführen der nachfolgenden Befehle die Werte `<storage-account-key>` und `<storage-account-name>` durch Werte, die Sie unter [Erstellen eines Speicherkontos](#create-a-storage-account) angegeben und abgerufen haben.

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell gibt eine Ausgabe ähnlich der folgenden Beispielausgabe zurück:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Die Azure-Dateifreigabe wurde dem Laufwerk Z erfolgreich zugeordnet.

7. Bestätigen Sie an einer Eingabeaufforderung, dass der virtuelle Computer über keine ausgehende Verbindung mit dem Internet verfügt:

   ```
   ping bing.com
   ```

   Sie erhalten keine Antworten, da die dem Subnetz *Private* zugeordnete Netzwerksicherheitsgruppe keinen ausgehenden Zugriff auf das Internet zulässt.

8. Schließen Sie die Remotedesktopsitzung mit der VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bestätigen, dass der Zugriff auf das Speicherkonto verweigert wird

1. Geben Sie oben im Portal im Feld *Ressourcen, Dienste und Dokumente durchsuchen* **myVmPublic** ein.
2. Wenn **myVmPublic** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
3. Führen Sie in [Bestätigen des Zugriffs auf das Speicherkonto](#confirm-access-to-storage-account) für die VM *myVmPublic* die Schritte 1–6 aus.

   Nach einer kurzen Wartezeit tritt ein Fehler vom Typ `New-PSDrive : Access is denied` auf. Der Zugriff wird verweigert, da der virtuelle Computer *myVmPublic* im Subnetz *Public* bereitgestellt wird. Das Subnetz *Public* verfügt nicht über einen Dienstendpunkt, der für Azure Storage aktiviert ist. Das Speicherkonto lässt Netzwerkzugriff nur über das Subnetz *Private*, nicht jedoch über das Subnetz *Public* zu.

4. Schließen Sie die Remotedesktopsitzung für den virtuellen Computer *myVmPublic*.

5. Navigieren Sie auf Ihrem Computer zum Azure-[Portal](https://portal.azure.com).
6. Geben Sie den Namen des Speicherkontos ein, das Sie im Feld **Ressourcen, Dienste und Dokumente durchsuchen** erstellt haben. Wenn der Name Ihres Speicherkontos in den Suchergebnissen angezeigt wird, wählen Sie ihn aus.
7. Wählen Sie **Dateien** aus.
8. Sie erhalten den Fehler, der in der folgenden Abbildung dargestellt ist:

   ![Zugriffsfehler](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

   Der Zugriff wird verweigert, da sich Ihr Computer nicht im Subnetz *Private* des Netzwerks *MyVirtualNetwork* befindet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit allen ihren Ressourcen, wenn Sie sie nicht mehr benötigen:

1. Geben Sie im oben im Portal im Feld *Suche* die Zeichenfolge **myResourceGroup** ein. Wenn **myResourceGroup** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie für **Geben Sie den Ressourcengruppennamen ein:** den Namen *myResourceGroup* ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Dienstendpunkt für ein Subnetz eines virtuellen Netzwerks aktiviert. Sie haben erfahren, dass Dienstendpunkte für Ressourcen aktiviert werden können, die über mehrere Azure-Dienste bereitgestellt werden. Sie haben ein Azure Storage-Konto erstellt und den Netzwerkzugriff auf das Speicherkonto ausschließlich auf Ressourcen im Subnetz eines virtuellen Netzwerks eingeschränkt. Weitere Informationen zu Dienstendpunkten finden Sie unter [Dienstendpunkte im virtuellen Netzwerk](virtual-network-service-endpoints-overview.md) und [Hinzufügen, Ändern oder Löschen von Subnetzen virtueller Netzwerke](virtual-network-manage-subnet.md).

Wenn Sie mehrere virtuelle Netzwerke in Ihrem Konto verwenden, können Sie zwei virtuelle Netzwerke miteinander verbinden, damit die Ressourcen in jedem virtuellen Netzwerk miteinander kommunizieren können. Im nächsten Tutorial erfahren Sie, wie Sie Verbindungen zwischen virtuellen Netzwerken herstellen.

> [!div class="nextstepaction"]
> [Herstellen von Verbindungen zwischen virtuellen Netzwerken](./tutorial-connect-virtual-networks-portal.md)
