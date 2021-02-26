---
title: 'Schnellstart: Erstellen einer Any-to-Any-Konfiguration mit einer ARM-Vorlage'
titleSuffix: Azure Virtual WAN
description: In dieser Schnellstartanleitung wird gezeigt, wie Sie mit einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine Any-to-Any-Konfiguration erstellen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 02/02/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: bf892a45ee6df2ab29a1b326a663dc41057c7ea6
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509741"
---
# <a name="quickstart-create-an-any-to-any-configuration-using-an-arm-template"></a>Schnellstart: Erstellen einer Any-to-Any-Konfiguration mit einer ARM-Vorlage

In dieser Schnellstartanleitung wird beschrieben, wie Sie mit einer Azure Resource Manager-Vorlage (ARM-Vorlage) ein Any-to-Any-Szenario erstellen, bei dem Spokes beliebige andere Spokes erreichen können.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f201-virtual-wan-with-all-gateways%2fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
* Für diese Konfiguration werden Daten für ein Zertifikat mit öffentlichem Schlüssel benötigt. Beispieldaten werden im Artikel bereitgestellt. Die Beispieldaten werden hierbei nur bereitgestellt, um die Anforderungen der Vorlage für die Erstellung eines P2S-Gateways zu erfüllen. Nachdem der Vorlagenvorgang abgeschlossen und die Ressourcen bereitgestellt wurden, müssen Sie dieses Feld mit Ihren eigenen Zertifikatdaten aktualisieren, damit die Konfiguration funktioniert. Weitere Informationen finden Sie unter [Generieren und Exportieren von Zertifikaten für Benutzer-VPN-Verbindungen](certificates-point-to-site.md#cer).

## <a name="review-the-template"></a><a name="review"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/201-virtual-wan-with-all-gateways). Die Vorlage für diesen Artikel ist zu lang und kann hier nicht angezeigt werden. Die Vorlage können Sie sich in [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-virtual-wan-with-all-gateways/azuredeploy.json) ansehen.

In dieser Schnellstartanleitung erstellen Sie eine Azure Virtual WAN-Bereitstellung mit mehreren Hubs, die alle Gateways und VNET-Verbindungen umfasst. Die Liste mit den Eingabeparametern wurde absichtlich so kurz wie möglich gehalten. Das Schema für die IP-Adressierung kann geändert werden, indem die Variablen in der Vorlage modifiziert werden. Dieses Szenario wird weiter unten im Artikel [Szenario: Any-to-Any](scenario-any-to-any.md) beschrieben.

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Bereitstellungsarchitektur":::

Mit dieser Vorlage wird eine voll funktionsfähige Azure Virtual WAN-Umgebung mit den folgenden Ressourcen erstellt:

* Zwei einzelne Hubs in unterschiedlichen Regionen
* Vier virtuelle Azure-Netzwerke (VNETs)
* Zwei VNET-Verbindungen für jeden vWAN-Hub
* Ein Point-to-Site-VPN-Gateway (P2S) auf jedem Hub
* Ein Site-to-Site-VPN-Gateway (S2S) auf jedem Hub
* Ein ExpressRoute-Gateway auf jedem Hub

In der Vorlage sind mehrere Azure-Ressourcen definiert:

* [**Microsoft.Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft.Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft.Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft.Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft.Network/vpngateways**](/azure/templates/microsoft.network/vpngateways) 
* [**Microsoft.Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft.Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnserverconfigurations)

>[!NOTE]
> Mit dieser ARM-Vorlage werden nicht die Ressourcen für die Kundenseite erstellt, die für die Hybridkonnektivität erforderlich sind. Nach dem Bereitstellen der Vorlage müssen Sie noch die P2S-VPN-Clients und die VPN-Branches (lokale Standorte) erstellen bzw. konfigurieren und die Verbindung für die ExpressRoute-Leitungen herstellen.
>

Weitere Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a><a name="deploy"></a>Bereitstellen der Vorlage

Für die korrekte Bereitstellung dieser Vorlage müssen Sie anstelle von anderen Vorgehensweisen die Schaltfläche „Deploy to Azure“ (In Azure bereitstellen) und das Azure-Portal verwenden. Dies hat die folgenden Gründe:

* Zum Erstellen der P2S-Konfiguration müssen Sie die Daten des Stammzertifikats hochladen. Vom Datenfeld werden die Zertifikatdaten nicht akzeptiert, wenn PowerShell oder die CLI genutzt wird.
* Aufgrund des Uploads der Zertifikatdaten funktioniert diese Vorlage nicht richtig, wenn Cloud Shell verwendet wird.
* Darüber hinaus ist es für Sie leicht möglich, die Vorlage und die Parameter im Portal so zu ändern, dass die IP-Adressbereiche und andere Werte abgedeckt sind.

1. Klicken Sie auf Schaltfläche zum **Bereitstellen in Azure**.

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f201-virtual-wan-with-all-gateways%2fazuredeploy.json)
1. Klicken Sie zum Anzeigen der Vorlage auf **Vorlage bearbeiten**. Auf dieser Seite können Sie einige Werte anpassen, z. B. den Adressraum oder den Namen bestimmter Ressourcen. Wählen Sie **Speichern** aus, um Ihre Änderungen zu speichern, oder wählen Sie **Verwerfen** aus.
1. Geben Sie auf der Vorlagenseite die Werte ein. Für diese Vorlage werden die Daten des öffentlichen P2S-Zertifikats benötigt. Falls Sie diesen Artikel zu Übungszwecken nutzen, können Sie die folgenden Daten aus dieser CER-Datei als Beispieldaten für beide Hubs verwenden. Nachdem die Vorlage ausgeführt wurde und die Bereitstellung abgeschlossen ist, müssen Sie diese Informationen zur Verwendung der P2S-Konfiguration durch die [Daten des Zertifikats](certificates-point-to-site.md#cer) mit einem öffentlichen Schlüssel ersetzen, die für Ihre eigene Bereitstellung gelten.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. Wählen Sie nach dem Eingeben der Werte die Option **Überprüfen + erstellen** aus.
1. Wählen Sie nach bestandener Überprüfung auf der Seite **Überprüfen + erstellen** die Option **Erstellen** aus.
1. Es dauert ungefähr 75 Minuten, bis der Bereitstellungsvorgang abgeschlossen ist. Sie können den Status auf der Seite **Übersicht** der Vorlage verfolgen.  Wenn Sie das Portal schließen, wird der Bereitstellungsvorgang fortgesetzt.

   :::image type="content" source="./media/quickstart-any-to-any-template/template.png" alt-text="Beispiel für abgeschlossenen Bereitstellungsvorgang":::

## <a name="validate-the-deployment"></a><a name="validate"></a>Überprüfen der Bereitstellung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Bereich **Ressourcengruppen** aus.
1. Wählen Sie die Ressourcengruppe aus, die Sie im vorherigen Abschnitt erstellt haben. Die Anzeige auf der Seite **Übersicht** ähnelt dem folgenden Beispiel: :::image type="content" source="./media/quickstart-any-to-any-template/resources.png" alt-text="Beispiel für Ressourcen" lightbox="./media/quickstart-any-to-any-template/resources.png":::

1. Klicken Sie auf die Virtual WAN-Instanz, um die Hubs anzuzeigen. Klicken Sie auf der Virtual WAN-Seite auf die einzelnen Hubs, um Verbindungen und andere Hubinformationen anzuzeigen.
   :::image type="content" source="./media/quickstart-any-to-any-template/hub.png" alt-text="Beispiel für Hubs" lightbox="./media/quickstart-any-to-any-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>Durchführen der Hybridkonfiguration

Mit der Vorlage werden nicht alle Einstellungen konfiguriert, die für ein Hybridnetzwerk erforderlich sind. Sie müssen je nach Ihren Anforderungen die folgenden Konfigurationen und Einstellungen vornehmen.

* [Konfigurieren der VPN-Branches: Lokale Standorte](virtual-wan-site-to-site-portal.md#site)
* [Durchführen der P2S-VPN-Konfiguration](virtual-wan-point-to-site-portal.md)
* [Herstellen der ExpressRoute-Verbindungen](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>Bereinigen der Ressourcen

Wenn Sie die von Ihnen erstellten Ressourcen nicht mehr benötigen, können Sie sie löschen. Einige Virtual WAN-Ressourcen müssen aufgrund von Abhängigkeiten in einer bestimmten Reihenfolge gelöscht werden. Das Löschen kann bis zu 30 Minuten dauern.

1. Öffnen Sie die Virtual WAN-Instanz, die Sie erstellt haben.
1. Wählen Sie einen virtuellen Hub aus, der der Virtual WAN-Instanz zugeordnet ist, um die Seite für den Hub zu öffnen.
1. Klicken Sie auf **Löschen**. Löschen Sie alle Entitäten (Verbindungen, Gateways usw.) auf dem Hub. Dieser Vorgang kann bis zu 30 Minuten dauern.
1. Sie können den Hub entweder jetzt oder zu einem späteren Zeitpunkt löschen, wenn Sie die Ressourcengruppe löschen.
1. Wiederholen Sie diesen Schritt für alle Hubs, die der Virtual WAN-Instanz zugeordnet sind.
1. Navigieren Sie im Azure-Portal zur Ressourcengruppe.
1. Wählen Sie die Option **Ressourcengruppe löschen**. Hierdurch werden alle in der Ressourcengruppe enthaltenen Elemente gelöscht, einschließlich der Hubs und der Virtual WAN-Instanz.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Durchführen der P2S-VPN-Konfiguration](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [Konfigurieren der VPN-Branches: Lokale Standorte](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [Herstellen der ExpressRoute-Verbindungen](virtual-wan-expressroute-portal.md)