---
title: Einrichten des Failovers für mehrere Azure CDN-Endpunkte mit Azure Traffic Manager | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Traffic Manager mit Azure CDN-Endpunkten einrichten.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 956df17c821b86d95b1d87c3c8d8197bab7a95be
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955267"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Einrichten des Failovers für mehrere Azure CDN-Endpunkte mit Azure Traffic Manager

Wenn Sie Azure Content Delivery Network (CDN) konfigurieren, können Sie den optimalen Anbieter und Tarif für Ihre Anforderungen auswählen. Aufgrund seiner global verteilten Infrastruktur wird in Azure CDN standardmäßig lokale und geografische Redundanz und ein globaler Lastenausgleich erstellt, um die Dienstverfügbarkeit und Leistung zu verbessern. Wenn ein Standort nicht verfügbar ist und keine Inhalte bereitstellen kann, werden Anforderungen automatisch an einen anderen Speicherort geleitet, und das optimale POP (basierend auf Faktoren wie Anforderungsstandort und Serverauslastung) wird verwendet, um alle Clientanforderungen zu erfüllen. 
 
Wenn Sie über mehrere CDN-Profile verfügen, können Sie die Verfügbarkeit und Leistung mit Azure Traffic Manager weiter verbessern. Sie können Azure Traffic Manager mit Azure CDN verwenden, um einen Lastenausgleich zwischen mehreren CDN-Endpunkten für das Failover, den geografischen Lastenausgleich und andere Szenarien durchzuführen. In einem typischen Failoverszenario werden alle Clientanforderungen zuerst zum primären CDN-Profil umgeleitet. Wenn das Profil nicht verfügbar ist, werden die Anforderungen dann an das sekundäre CDN-Profil übergeben, bis das primäre CDN-Profil wieder online ist. Durch Verwendung von Azure Traffic Manager auf diese Weise wird sichergestellt, dass Ihre Webanwendung immer verfügbar ist. 

Dieser Artikel enthält Anweisungen und ein Beispiel zum Einrichten des Failovers mit den Profilen **Azure CDN Standard von Verizon** und **Azure CDN Standard von Akamai**.

## <a name="set-up-azure-cdn"></a>Einrichten von Azure CDN 
Erstellen Sie mehrere Azure CDN-Profile und Endpunkte mit verschiedenen Anbietern.

1. Erstellen Sie anhand der unter [Erstellen eines neuen CDN-Profils](cdn-create-new-endpoint.md#create-a-new-cdn-profile) beschriebenen Schritte das Profil **Azure CDN Standard von Verizon** und das Profil **Azure CDN Standard von Akamai**.
 
   ![Mehrere CDN-Profile](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Erstellen Sie in jedem der neuen Profile mindestens einen Endpunkt entsprechend den Schritten unter [Erstellen eines neuen CDN-Endpunkts](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Einrichten von Azure Traffic Manager
Erstellen Sie ein Azure Traffic Manager-Profil, und richten Sie den Lastenausgleich für Ihre CDN-Endpunkte ein. 

1. Erstellen Sie anhand der Schritte unter [Erstellen eines Traffic Manager-Profils](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile) ein Azure Traffic Manager-Profil. 

    Wählen Sie unter **Routingmethode** die Option **Priorität** aus.

2. Fügen Sie anhand der Schritte unter [Hinzufügen von Traffic Manager-Endpunkten](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints) die CDN-Endpunkte in Ihrem Traffic Manager-Profil ein.

    Wählen Sie unter **Typ** die Option **Externe Endpunkte** aus. Geben Sie unter **Priorität** eine Zahl ein.

    Erstellen Sie beispielsweise *cdndemo101akamai.azureedge.net* mit der Priorität *1* und *cdndemo101verizon.azureedge.net* mit der Priorität *2*.

   ![CDN-Endpunkte in Traffic Manager](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Einrichten einer benutzerdefinierten Domäne in Azure CDN und Azure Traffic Manager
Nachdem Sie Ihre CDN- und Traffic Manager-Profile eingerichtet haben, führen Sie die folgenden Schritte aus, um die DNS-Zuordnung hinzuzufügen und eine benutzerdefinierte Domäne für die CDN-Endpunkte zu registrieren. In diesem Beispiel lautet der Name der benutzerdefinierten Domäne *cdndemo101.dustydogpetcare.online*.

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


2.  Wählen Sie in Ihrem Azure CDN-Profil den ersten CDN-Endpunkt (Akamai) aus. Wählen Sie **Benutzerdefinierte Domäne hinzufügen** aus, und geben Sie *cdndemo101.dustydogpetcare.online* ein. Vergewissern Sie sich, dass das Häkchen zum Überprüfen der benutzerdefinierten Domäne grün ist. 

    Azure CDN verwendet die Unterdomäne *cdnverify*, um die DNS-Zuordnung zu überprüfen und den Registrierungsvorgang abzuschließen. Weitere Informationen finden Sie unter [Erstellen eines CNAME-DNS-Eintrags](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Durch diesen Schritt kann Azure CDN die benutzerdefinierte Domäne erkennen und in der Folge auf ihre Anforderungen antworten.
    
 > [!NOTE]
    > Um SSL für ein **Azure CDN aus Akamai**-Profil zu aktivieren, müssen Sie die benutzerdefinierte Domäne direkt für Ihren Endpunkt benennen. „cdnverify“ zum Aktivieren von SSL wird noch nicht unterstützt. 
    >

3.  Wechseln Sie wieder zu der Website des Domänenanbieters für Ihre benutzerdefinierte Domäne, und aktualisieren Sie die erste erstellte DNS-Zuordnung so, dass die benutzerdefinierte Domäne dem zweiten CDN-Endpunkt zugeordnet ist.
                             
    Beispiel: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Wählen Sie in Ihrem Azure CDN-Profil den zweiten CDN-Endpunkt (Verizon) aus, und wiederholen Sie Schritt 2. Wählen Sie **Benutzerdefinierte Domäne hinzufügen** aus, und geben Sie *cdndemo101.dustydogpetcare.online* ein.
 
Nachdem Sie diese Schritte abgeschlossen haben, ist der Multi-CDN-Dienst mit Failoverfunktionen in Azure Traffic Manager eingerichtet. Sie können nun in Ihrer benutzerdefinierten Domäne auf die Test-URLs zugreifen. Um die Funktionalität zu testen, deaktivieren Sie den primären CDN-Endpunkt, und überprüfen Sie, ob die Anforderung ordnungsgemäß zum sekundären CDN-Endpunkt verschoben wird. 

## <a name="next-steps"></a>Nächste Schritte
Sie können auch andere Routingmethoden einrichten, z.B. das geografische Routing, um die Last auf verschiedene CDN-Endpunkte zu verteilen. Weitere Informationen finden Sie unter [Konfigurieren der geografischen Routingmethode für Datenverkehr mithilfe von Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



