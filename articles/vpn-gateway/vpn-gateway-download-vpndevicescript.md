---
title: 'Herunterladen von VPN-Gerätekonfigurationsskripts für S2S-VPN-Verbindungen: Azure Resource Manager | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie Schritt für Schritt, wie Sie VPN-Gerätekonfigurationsskripts für S2S-VPN-Verbindungen mit Azure-VPN-Gateways unter Verwendung von Azure Resource Manager herunterladen.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: f7ee53c10c6597dbf98f8f85fc31fe789137471e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157606"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Herunterladen von VPN-Gerätekonfigurationsskripts für S2S-VPN-Verbindungen

In diesem Artikel erfahren Sie Schritt für Schritt, wie Sie VPN-Gerätekonfigurationsskripts für S2S-VPN-Verbindungen mit Azure-VPN-Gateways unter Verwendung von Azure Resource Manager herunterladen. Das folgende Diagramm zeigt eine Übersicht über den Workflow:

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Die folgenden Geräte haben verfügbare Skripts:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>Informationen zu VPN-Gerätekonfigurationsskripts

Eine standortübergreifende VPN-Verbindung besteht aus einem Azure-VPN-Gateway, einem lokalen VPN-Gerät und einem IPsec-S2S-VPN-Tunnel dazwischen. Der Workflow umfasst üblicherweise folgende Schritte:

1. Erstellen und Konfigurieren eines Azure-VPN-Gateways (Gateway für virtuelle Netzwerke)
2. Erstellen und Konfigurieren eines lokalen Azure-Netzwerkgateways, das Ihr lokales Netzwerk und Ihr lokales VPN-Gerät darstellt
3. Erstellen und Konfigurieren einer Azure-VPN-Verbindung zwischen dem Azure-VPN-Gateway und dem lokalen Netzwerkgateway
4. Konfigurieren des durch das lokale Netzwerkgateway dargestellten lokalen VPN-Geräts, um den eigentlichen S2S-VPN-Tunnel mit dem Azure-VPN-Gateway einzurichten

Für die Schritte 1 bis 3 können Sie das [Azure-Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) oder die [Befehlszeilenschnittstelle](vpn-gateway-howto-site-to-site-resource-manager-cli.md) verwenden. Im letzten Schritt müssen die lokalen VPN-Geräte außerhalb von Azure konfiguriert werden. Dieses Feature ermöglicht das Herunterladen eines Konfigurationsskripts für Ihr VPN-Gerät, in dem die Werte Ihres Azure-VPN-Gateways und Ihres virtuellen Netzwerks sowie die Adresspräfixe Ihres lokalen Netzwerks, die VPN-Verbindungseigenschaften und Ähnliches bereits angegeben sind. Sie können dieses Skript weiter bearbeiten oder es über die Konfigurationskonsole direkt auf Ihre lokalen VPN-Geräte anwenden.

> [!IMPORTANT]
> * Die Syntax des VPN-Gerätekonfigurationsskripts ist jeweils unterschiedlich und hängt von den Modellen und Firmwareversionen ab. Achten Sie bei den verfügbaren Vorlagen besonders auf die Angaben zu Gerätemodell und -version.
> * Einige Parameterwerte müssen auf dem Gerät eindeutig sein und können nur durch den Zugriff auf das Gerät bestimmt werden. Diese Werte werden zwar vorab in den von Azure generierten Konfigurationsskripts angegeben, müssen aber überprüft werden, um sicherzustellen, dass sie für Ihr Gerät gültig sind. Beispiele:
>    * Schnittstellennummern
>    * Nummern der Zugriffssteuerungsliste
>    * Richtliniennamen/-nummern usw.
> * Suchen Sie im Skript nach dem Schlüsselwort **REPLACE**, um die Parameter zu finden, die vor dem Anwenden des Skripts überprüft werden müssen.
> * Einige Vorlagen enthalten einen Abschnitt namens **CLEANUP**, den Sie zum Entfernen der Konfigurationen verwenden können. Bereinigungsabschnitte sind standardmäßig auskommentiert.

## <a name="download-the-configuration-script-from-azure-portal"></a>Herunterladen des Konfigurationsskripts über das Azure-Portal

Erstellen Sie ein Azure-VPN-Gateway, ein lokales Netzwerkgateway und eine Verbindungsressource, die die beiden verbindet. Eine entsprechende Anleitung finden Sie auf der folgenden Seite:

* [Erstellen einer Standort-zu-Standort-Verbindung im Azure-Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Führen Sie nach der Erstellung der Verbindungsressource die folgenden Schritte aus, um die VPN-Gerätekonfigurationsskripts herunterzuladen:

1. Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com), und melden Sie sich ggf. mit Ihrem Azure-Konto an.
2. Navigieren Sie zu der Verbindungsressource, die Sie erstellt haben. Klicken Sie auf „Alle Dienste“ > „NETZWERK“ > „Verbindungen“, um eine Liste mit allen Verbindungsressourcen anzuzeigen.

    ![connection-list](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klicken Sie auf die Verbindung, die Sie konfigurieren möchten.

    ![connection-overview](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klicken Sie auf den Link „Konfiguration herunterladen“ (in der Verbindungsübersicht rot hervorgehoben). Daraufhin wird die Seite „Konfiguration herunterladen“ angezeigt.

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Wählen Sie die Modellfamilie und die Firmwareversion für Ihr VPN-Gerät aus, und klicken Sie anschließend auf die Schaltfläche „Konfiguration herunterladen“.

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Daraufhin werden Sie in Ihrem Browser zum Speichern des heruntergeladenen Skripts (Textdatei) aufgefordert.
7. Öffnen Sie das heruntergeladene Konfigurationsskript mit einem Text-Editor, und suchen Sie nach dem Schlüsselwort „REPLACE“, um die Parameter zu überprüfen, die ggf. ersetzt werden müssen.

    ![edit-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Herunterladen des Konfigurationsskripts mithilfe von Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Das Konfigurationsskript kann auch mithilfe von Azure PowerShell heruntergeladen werden, wie im folgenden Beispiel gezeigt:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Anwenden des Konfigurationsskripts auf Ihr VPN-Gerät

Nachdem Sie das Konfigurationsskript heruntergeladen und überprüft haben, können Sie es auf Ihr VPN-Gerät anwenden. Die genaue Vorgehensweise hängt von der Marke und dem Modell Ihres VPN-Geräts ab. Entsprechende Informationen finden Sie im Handbuch oder auf den Anleitungsseiten für Ihre VPN-Geräte.

## <a name="next-steps"></a>Nächste Schritte

Setzen Sie die Konfiguration Ihrer [Site-to-Site-Verbindung](vpn-gateway-howto-site-to-site-resource-manager-portal.md) fort.
