---
title: Failover für mehrere Endpunkte mit Traffic Manager
titleSuffix: Azure Content Delivery Network
description: Hier erfahren Sie, wie Sie mit Azure Traffic Manager in mehreren Azure CDN-Endpunkten (Azure Content Delivery Network) Failover konfigurieren.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: d2d3bd43a0f17167e855d7e678a96cd79fe42237
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92777740"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Failover für mehrere Endpunkte mit Azure Traffic Manager

Wenn Sie Azure Content Delivery Network (CDN) konfigurieren, können Sie den optimalen Anbieter und Tarif für Ihre Anforderungen auswählen. 

Aufgrund seiner global verteilten Infrastruktur wird in Azure CDN standardmäßig lokale und geografische Redundanz und ein globaler Lastenausgleich erstellt, um die Dienstverfügbarkeit und Leistung zu verbessern. 

Wenn ein Speicherort für die Bereitstellung von Inhalten nicht verfügbar ist, werden Anforderungen automatisch an einen anderen Speicherort weitergeleitet. Zum Verarbeiten von Anforderungen der einzelnen Clients wird der jeweils optimale PoP (Point of Presence) verwendet. Das automatische Routing basiert auf Faktoren wie dem Anforderungsspeicherort und der Serverauslastung.
 
Wenn Sie über mehrere CDN-Profile verfügen, können Sie die Verfügbarkeit und Leistung mit Azure Traffic Manager weiter verbessern. 

Verwenden Sie Azure Traffic Manager mit Azure CDN, um einen Lastenausgleich zwischen mehreren CDN-Endpunkten für folgende Szenarios durchzuführen:
 
* Failover
* Geografischer Lastenausgleich 

In einem typischen Failoverszenario werden alle Clientanforderungen an das primäre CDN-Profil weitergeleitet. 

Wenn das Profil nicht verfügbar ist, werden Anforderungen an das sekundäre Profil weitergeleitet.  Anforderungen werden wieder an das primäre Profil gesendet, sobald es wieder online geschaltet ist.

Durch Verwendung von Azure Traffic Manager auf diese Weise wird sichergestellt, dass Ihre Webanwendung immer verfügbar ist. 

Dieser Artikel enthält einen Leitfaden und ein Beispiel für die Konfiguration des Failovers mit Profilen aus: 

* **Azure CDN Standard von Verizon**
* **Azure CDN Standard von Akamai**

**Azure CDN von Microsoft** wird ebenfalls unterstützt.

## <a name="create-azure-cdn-profiles"></a>Erstellen von Azure CDN-Profilen
Erstellen Sie mehrere Azure CDN-Profile und Endpunkte mit verschiedenen Anbietern.

1. Erstellen Sie zwei CDN-Profile:
    * **Azure CDN Standard von Verizon**
    * **Azure CDN Standard von Akamai** 

    Erstellen Sie die Profile, indem Sie die Schritte in [Erstellen eines neuen CDN-Profils](cdn-create-new-endpoint.md#create-a-new-cdn-profile) durchführen.
 
   ![Mehrere CDN-Profile](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Erstellen Sie in jedem der neuen Profile mindestens einen Endpunkt entsprechend den Schritten unter [Erstellen eines neuen CDN-Endpunkts](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="create-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils
Erstellen Sie ein Azure Traffic Manager-Profil, und konfigurieren Sie den Lastenausgleich für Ihre CDN-Endpunkte. 

1. Erstellen Sie anhand der Schritte unter [Erstellen eines Traffic Manager-Profils](../traffic-manager/quickstart-create-traffic-manager-profile.md) ein Azure Traffic Manager-Profil. 

    * Wählen Sie unter **Routingmethode** die Option **Priorität** aus.

2. Fügen Sie anhand der Schritte unter [Hinzufügen von Traffic Manager-Endpunkten](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints) die CDN-Endpunkte in Ihrem Traffic Manager-Profil ein.

    * Wählen Sie unter **Typ** die Option **Externe Endpunkte** aus.
    * Geben Sie unter **Priorität** eine Zahl ein.

    Erstellen Sie beispielsweise **cdndemo101akamai.azureedge.net** mit der Priorität **1** und **cdndemo101verizon.azureedge.net** mit der Priorität **2** .

   ![CDN-Endpunkte in Traffic Manager](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Konfigurieren einer benutzerdefinierten Domäne in Azure CDN und Azure Traffic Manager
Nachdem Sie Ihre CDN- und Traffic Manager-Profile konfiguriert haben, führen Sie die folgenden Schritte aus, um die DNS-Zuordnung hinzuzufügen und eine benutzerdefinierte Domäne für die CDN-Endpunkte zu registrieren. In diesem Beispiel lautet der Name der benutzerdefinierten Domäne **cdndemo101.dustydogpetcare.online** .

1. Wechseln Sie zu der Website des Domänenanbieters für Ihre benutzerdefinierte Domäne, z.B. GoDaddy, und erstellen Sie zwei DNS-CNAME-Einträge. 

    a. Ordnen Sie für den ersten CNAME-Eintrag die benutzerdefinierte Domäne mit der Unterdomäne „cdnverify“ Ihrem CDN-Endpunkt zu. Dieser Eintrag ist ein erforderlicher Schritt, um die benutzerdefinierte Domäne für den CDN-Endpunkt zu registrieren, den Sie Traffic Manager in Schritt 2 hinzugefügt haben.

      Beispiel: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Ordnen Sie für den zweiten CNAME-Eintrag die benutzerdefinierte Domäne ohne die Unterdomäne „cdnverify“ Ihrem CDN-Endpunkt zu. Mit diesem Eintrag wird die benutzerdefinierte Domäne Traffic Manager zugeordnet. 

      Beispiel: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Wenn Ihre Domäne derzeit live geschaltet ist und nicht unterbrochen werden soll, führen Sie diesen Schritt zuletzt aus. Überprüfen Sie, ob die CDN-Endpunkte und Traffic Manager-Domänen live geschaltet sind, bevor Sie das DNS der benutzerdefinierten Domäne in Traffic Manager aktualisieren.
    >


2.  Wählen Sie in Ihrem Azure CDN-Profil den ersten CDN-Endpunkt (Akamai) aus. Wählen Sie **Benutzerdefinierte Domäne hinzufügen** aus, und geben Sie **cdndemo101.dustydogpetcare.online** ein. Vergewissern Sie sich, dass das Häkchen zum Überprüfen der benutzerdefinierten Domäne grün ist. 

    Azure CDN verwendet die Unterdomäne **cdnverify** , um die DNS-Zuordnung zu überprüfen und den Registrierungsvorgang abzuschließen. Weitere Informationen finden Sie unter [Erstellen eines CNAME-DNS-Eintrags](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Durch diesen Schritt kann Azure CDN die benutzerdefinierte Domäne erkennen und in der Folge auf ihre Anforderungen antworten.
    
    > [!NOTE]
    > Um TLS für ein **Azure CDN von Akamai** -Profil zu aktivieren, müssen Sie in der benutzerdefinierten Domäne einen direkten CNAME-Eintrag für Ihren Endpunkt erstellen. „cdnverify“ zum Aktivieren von TLS wird noch nicht unterstützt. 
    >

3.  Kehren Sie zur Website des Domänenanbieters Ihrer benutzerdefinierten Domäne zurück. Aktualisieren Sie die erste von Ihnen erstellte DNS-Zuordnung. Ordnen Sie die benutzerdefinierte Domäne Ihrem zweiten CDN-Endpunkt zu.
                             
    Beispiel: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Wählen Sie in Ihrem Azure CDN-Profil den zweiten CDN-Endpunkt (Verizon) aus, und wiederholen Sie Schritt 2. Wählen Sie **Benutzerdefinierte Domäne hinzufügen** aus, und geben Sie **cdndemo101.dustydogpetcare.online** ein.
 
Nachdem Sie diese Schritte abgeschlossen haben, ist der Multi-CDN-Dienst mit Failoverfunktionen in Azure Traffic Manager konfiguriert. 

Sie können nun in Ihrer benutzerdefinierten Domäne auf die Test-URLs zugreifen. 

Um die Funktionalität zu testen, deaktivieren Sie den primären CDN-Endpunkt, und überprüfen Sie, ob die Anforderung ordnungsgemäß zum sekundären CDN-Endpunkt verschoben wird. 

## <a name="next-steps"></a>Nächste Schritte
Sie können auch andere Routingmethoden wie das geografische Routing konfigurieren, um die Last auf verschiedene CDN-Endpunkte zu verteilen. 

Weitere Informationen finden Sie unter [Konfigurieren der geografischen Routingmethode für Datenverkehr mithilfe von Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).