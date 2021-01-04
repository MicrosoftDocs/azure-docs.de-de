---
title: Konfigurieren der leistungsorientierten Routingmethode für Datenverkehr mit Azure Traffic Manager | Microsoft Docs
description: In diesem Artikel wird erläutert, wie Traffic Manager so konfiguriert wird, dass Datenverkehr an den Endpunkt mit der kürzesten Wartezeit geleitet wird.
services: traffic-manager
manager: twooley
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 46d71a38b79f449084f7353527f2dfb05d5b92a4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994909"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurieren der leistungsorientierten Routingmethode für Datenverkehr

Mit der Routingmethode für Leistungsdatenverkehr können Sie Datenverkehr zu den Endpunkten leiten, mit der niedrigsten Latenz des Client-Netzwerks. Normalerweise stellt das Rechenzentrum mit der geringsten Wartezeit die kürzeste geografische Distanz dar. Diese Routingmethode für Datenverkehr kann keine Änderungen in Echtzeit in der Netzwerkkonfiguration oder im Lastenausgleich berücksichtigen.

##  <a name="to-configure-performance-routing-method"></a>So konfigurieren Sie die Routingmethode „Leistung“

1. Melden Sie sich im Browser beim [Azure-Portal](https://portal.azure.com) an. Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose einmonatige Testversion](https://azure.microsoft.com/free/) registrieren. 
2. Suchen Sie auf der Suchleiste des Portals die **Traffic Manager-Profile**, und klicken Sie dann auf den Namen des Profils, für das Sie die Routingmethode konfigurieren möchten.
3. Stellen Sie auf dem Blatt **Traffic Manager-Profil** sicher, dass die Clouddienste sowie die Websites, die Ihre Konfiguration beinhalten soll, vorhanden sind.
4. Klicken Sie im Abschnitt **Einstellungen** auf **Konfiguration**, und nehmen Sie auf dem Blatt **Konfiguration** die folgenden Einstellungen vor:
    1. Wählen Sie in den Einstellungen der **Routingmethode für Datenverkehr** für **Routingmethode** die Option **Leistung** aus.
    2. Legen Sie die **Überwachungseinstellungen für Endpunkt** für alle Endpunkte in diesem Profil wie folgt identisch fest:
        1. Wählen Sie das entsprechende **Protokoll** aus, und geben Sie die Nummer für den **Port** an. 
        2. Geben Sie unter **Pfad** einen Schrägstrich */* ein. Sie müssen einen Pfad und einen Dateinamen angeben, um Endpunkte zu überwachen. Ein Schrägstrich „/“ ist ein gültiger Eintrag für den relativen Pfad und bedeutet, dass sich die Datei im Stammverzeichnis (Standardwert) befindet.
        3. Klicken Sie oben auf der Seite auf **Speichern**.
5.  Testen Sie wie folgt die Änderungen in Ihrer Konfiguration:
    1.  Suchen Sie auf der Suchleiste des Portals nach dem Namen des Traffic Manager-Profils, und klicken Sie in den angezeigten Ergebnissen auf das Traffic Manager-Profil.
    2.  Klicken Sie auf dem Blatt **Traffic Manager-Profil** auf **Übersicht**.
    3.  Auf dem Blatt **Traffic Manager-Profil** wird der DNS-Name Ihres neu erstellten Traffic Manager-Profils angezeigt. Dieser kann von beliebigen Clients (etwa durch Navigation in einem Webbrowser) für das Routing zum richtigen Endpunkt nach Maßgabe des Routingtyps verwendet werden. In diesem Fall werden alle Anforderungen im Netzwerk des Clients an den Endpunkt mit der kürzesten Wartezeit geleitet.
6. Sobald das Traffic Manager-Profil funktionsfähig ist, bearbeiten Sie den DNS-Eintrag auf dem autoritativen DNS-Server, damit Ihre Unternehmensdomäne auf den Namen der Traffic Manager-Domäne verweisen kann.

![Konfigurieren der leistungsorientierten Routingmethode für Datenverkehr mit Traffic Manager][1]

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur [gewichteten Routingmethode für Datenverkehr](traffic-manager-configure-weighted-routing-method.md).
- Informationen zur [prioritätsbasierten Routingmethode](traffic-manager-configure-priority-routing-method.md).
- Informationen zur [geografischen Routingmethode](traffic-manager-configure-geographic-routing-method.md).
- Informationen zum [Testen von Traffic Manager-Einstellungen](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png