---
title: Steuern des Datenverkehrs mit Traffic Manager – Azure App Service
description: Dieser Artikel bietet zusammenfassende Informationen zu Azure Traffic Manager im Hinblick auf Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 79fe3bce558a8315f5fbf7dbc82a4979e8e24238
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831978"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Steuern des Azure App Service-Datenverkehrs mit Azure Traffic Manager
> [!NOTE]
> Dieser Artikel bietet zusammenfassende Informationen zu Microsoft Azure Traffic Manager im Hinblick auf Azure App Service. Weitere Informationen über Azure Traffic Manager selbst finden Sie unter den Links am Ende dieses Artikels.
> 
> 

## <a name="introduction"></a>Einführung
Mit Azure Traffic Manager können Sie steuern, wie Anforderungen von Webclients auf Apps in Azure App Service verteilt werden. Wenn einem Azure Traffic Manager-Profil App Service-Endpunkte hinzugefügt werden, verfolgt Azure Traffic Manager den Status Ihrer App Service-Apps (aktiv, angehalten oder gelöscht), sodass der gewünschte Endpunkt als Empfänger des Datenverkehrs ausgewählt werden kann.

## <a name="routing-methods"></a>Routingmethoden
Azure Traffic Manager verwendet vier verschiedene Routingmethoden. Diese Methoden werden in der folgenden Liste beschrieben, soweit sie Azure App Service betreffen.

* **[Priorität](../traffic-manager/traffic-manager-routing-methods.md#priority):** Verwenden einer primären App für den gesamten Datenverkehr und Bereitstellen von Sicherungen für den Fall, dass die primäre App oder die Sicherungs-Apps nicht verfügbar sind
* **[Gewichtet](../traffic-manager/traffic-manager-routing-methods.md#weighted):** Verteilen des Datenverkehrs auf eine Gruppe von Apps, entweder gleichmäßig oder gewichtet, gemäß Ihrer Definition
* **[Leistung](../traffic-manager/traffic-manager-routing-methods.md#performance):** Wenn Apps an unterschiedlichen geografischen Standorten genutzt werden, verwenden der „nächstgelegenen“ App im Hinblick auf die niedrigste Netzwerklatenz
* **[Geografisch](../traffic-manager/traffic-manager-routing-methods.md#geographic):** Weiterleiten von Benutzern basierend auf dem geografischen Standort, von dem ihre DNS-Abfrage stammt, zu bestimmten Apps 

Weitere Informationen finden Sie unter [Traffic Manager-Methoden für das Datenverkehrsrouting](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service und Traffic Manager-Profile
Für die Konfiguration zur Steuerung des App Service-App-Datenverkehrs erstellen Sie ein Profil in Azure Traffic Manager, das eine der vier zuvor beschriebenen Lastenausgleichsmethoden verwendet. Anschließend fügen Sie dem Profil die Endpunkte (in diesem Fall App Service) hinzu, für die Sie den Datenverkehr steuern möchten. Der App-Status (aktiv, angehalten oder gelöscht) wird regelmäßig an das Profil übermittelt, sodass Azure Traffic Manager den Datenverkehr entsprechend leiten kann.

Beachten Sie die folgenden Aspekte, wenn Sie Azure Traffic Manager mit Azure verwenden:

* Bei reinen App-Bereitstellungen innerhalb derselben Region bietet App Service bereits eine Failover- und Roundrobin-Funktion, die unabhängig vom App-Modus ist.
* Bei Bereitstellungen in derselben Region, die App Service zusammen mit anderen Azure-Clouddiensten verwenden, können Sie beide Endpunkttypen kombinieren, um Hybridszenarien zu ermöglichen.
* Sie können in einem Profil nur einen App Service-Endpunkt pro Region angeben. Wenn Sie eine App als Endpunkt für eine Region auswählen, stehen die verbleibenden Apps in dieser Region nicht mehr für dieses Profil zur Auswahl.
* Die App Service-Endpunkte, die Sie in einem Azure Traffic Manager-Profil festlegen, werden im Abschnitt **Domänennamen** auf der Konfigurationsseite für die App im Profil angezeigt, können dort jedoch nicht konfiguriert werden.
* Nachdem Sie einem Profil eine App hinzugefügt haben, wird in der **Website-URL** im Dashboard der Portalseite der App die benutzerdefinierte Domänen-URL der App angezeigt, sofern Sie eine eingerichtet haben. Anderenfalls wird die URL des Traffic Manager-Profils angezeigt (z.B. `contoso.trafficmanager.net`). Sowohl der direkte Domänenname der App als auch die Traffic Manager-URL werden auf der Konfigurationsseite der App im Abschnitt **Domänennamen** angezeigt.
* Ihre benutzerdefinierten Domänennamen funktionieren wie erwartet. Sie fügen sie Ihren Apps hinzu, müssen jedoch auch die DNS-Zuordnung konfigurieren, um auf die Traffic Manager-URL zu verweisen. Informationen zum Einrichten einer benutzerdefinierten Domäne für eine App Service-App finden Sie unter [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](app-service-web-tutorial-custom-domain.md).
* Sie können einem Azure Traffic Manager-Profil nur Apps im Standard- oder Premium-Modus hinzufügen.

## <a name="next-steps"></a>Nächste Schritte
Einen Überblick über die Konzepte und technischen Aspekte von Azure Traffic Manager finden Sie unter [Traffic Manager-Übersicht](../traffic-manager/traffic-manager-overview.md).

Weitere Informationen zur Verwendung von Traffic Manager mit App Service finden Sie in den Blogbeiträgen [Using Azure Traffic Manager with Azure Web Sites](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) (Verwenden von Azure Traffic Manager mit Azure-Websites) und [Azure Traffic Manager can now integrate with Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/) (Azure Traffic Manager kann jetzt in Azure-Websites integriert werden).

