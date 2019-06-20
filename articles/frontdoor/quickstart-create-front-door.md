---
title: 'Azure-Schnellstart: Erstellen eines Front Door-Profils für Hochverfügbarkeit von Anwendungen mithilfe des Azure-Portals'
description: In diesem Schnellstartartikel wird die Vorgehensweise zum Erstellen einer Front Door-Instanz für globale Webanwendungen mit Hochverfügbarkeit und hoher Leistung beschrieben.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 6bcd5bcc2463ec1ab9dcc97644d5046c31bfc78b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461985"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Schnellstart: Erstellen Sie eine „Front Door“ für eine hoch verfügbare globale Webanwendung.

In diesem Schnellstart wird beschrieben, wie Sie ein Front Door-Profil erstellen, das Hochverfügbarkeit und hohe Leistung für Ihre globale Webanwendung sicherstellt. 

Das in dieser Schnellstartanleitung beschriebene Szenario umfasst zwei Instanzen einer Webanwendung, die in verschiedenen Azure-Regionen ausgeführt werden. Es wird eine Front Door-Konfiguration auf der Grundlage von [Back-Ends mit gleicher Gewichtung und Priorität](front-door-routing-methods.md) erstellt, die dabei hilft, Benutzerdatenverkehr an die nächstgelegenen Standort-Back-Ends, auf denen die Anwendung ausgeführt wird, weiterzuleiten. Front Door überwacht kontinuierlich die Webanwendung und bietet automatisches Failover zum nächsten verfügbaren Back-End, wenn der nächstgelegene Standort nicht verfügbar ist.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure 
Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="prerequisites"></a>Voraussetzungen
Für diesen Schnellstart müssen Sie zwei Instanzen einer Webanwendung bereitgestellt haben, die in verschiedenen Azure-Regionen (*USA, Osten* und *Europa, Westen*) ausgeführt werden. Beide Webanwendungsinstanzen werden im Aktiv/Aktiv-Modus ausgeführt, d.h., jede von ihnen kann jederzeit Datenverkehr annehmen. Im Gegensatz dazu dient in einer Aktiv/Standby-Konfiguration ein Knoten als Failover.

1. Klicken Sie links oben auf dem Bildschirm auf **Ressource erstellen** > **Web** > **Web-App** > **Erstellen**.
2. Geben Sie unter **Web-App** die folgenden Informationen ein, oder wählen Sie sie aus, und geben Sie Standardeinstellungen ein, wenn noch keine Angaben gemacht wurden:

     | Einstellung         | Wert     |
     | ---              | ---  |
     | NAME           | Geben Sie einen eindeutigen Namen für Ihre Web-App ein.  |
     | Ressourcengruppe          | Wählen Sie **Neu** aus, und geben Sie *myResourceGroupFD1* ein. |
     | App Service-Plan/Standort         | Wählen Sie **Neu**aus.  Geben Sie als App Service-Plan *myAppServicePlanEastUS* ein, und klicken Sie dann auf **OK**. 
     |      Location  |   USA (Ost)        |
    |||

3. Klicken Sie auf **Erstellen**.
4. Eine Standardwebsite wird erstellt, wenn die Web-App erfolgreich bereitgestellt wurde.
5. Wiederholen Sie die Schritte 1 bis 3, um eine zweite Website in einer anderen Azure-Region mit den folgenden Einstellungen zu erstellen:

     | Einstellung         | Wert     |
     | ---              | ---  |
     | NAME           | Geben Sie einen eindeutigen Namen für Ihre Web-App ein.  |
     | Ressourcengruppe          | Wählen Sie **Neu** aus, und geben Sie *myResourceGroupFD2* ein. |
     | App Service-Plan/Standort         | Wählen Sie **Neu**aus.  Geben Sie als App Service-Plan *myAppServicePlanWestEurope* ein, und klicken Sie dann auf **OK**. 
     |      Location  |   Europa, Westen      |
    |||


## <a name="create-a-front-door-for-your-application"></a>Erstellen einer Front Door-Instanz für Ihre Anwendung
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Hinzufügen eines Front-End-Hosts für Front Door
Erstellen Sie eine Front Door-Konfiguration, die den Benutzerdatenverkehr basierend auf der geringsten Wartezeit zwischen zwei Back-Ends weiterleitet.

1. Klicken Sie links oben auf dem Bildschirm auf **Ressource erstellen** > **Netzwerk** > **Frontdoor** > **Erstellen**.
2. Beginnen Sie unter **Frontdoor-Instanz erstellen** damit, die grundlegenden Informationen hinzuzufügen und ein Abonnement anzugeben, in dem Sie die Front Door-Instanz konfigurieren möchten. Wie bei jeder anderen Azure-Ressource müssen Sie auch eine Ressourcengruppe und eine Ressourcengruppenregion bereitstellen, wenn Sie eine neue erstellen. Zuletzt müssen Sie einen Namen für Ihre Front Door-Instanz angeben.
3. Nachdem die grundlegenden Informationen ausgefüllt wurden, müssen Sie als Erstes den **Front-End-Host** für die Konfiguration definieren. Das Ergebnis muss ein gültiger Domänenname sein, z.B. `myappfrontend.azurefd.net`. Dieser Hostname muss global eindeutig sein, diese Validierung übernimmt jedoch Front Door. 

### <a name="b-add-application-backend-and-backend-pools"></a>B: Hinzufügen des Anwendung-Back-Ends und von Back-End-Pools

Als Nächstes müssen Sie Ihre Anwendungs-Back-Ends in einem Back-End-Pool konfigurieren, damit Front Door weiß, wo sich Ihre Anwendung befindet. 

1. Klicken Sie auf das Symbol „+“, um einen Back-End-Pool hinzuzufügen, und geben Sie dann einen **Namen** für den Back-End-Pool an, z.B. `myBackendPool`.
2. Klicken Sie anschließend auf „Back-Ends hinzufügen“, um die zuvor erstellten Websites hinzuzufügen.
3. Wählen Sie für **Zielhosttyp** die Option „App Service“ aus, wählen Sie das Abonnement aus, in dem Sie die Website erstellt haben, und wählen Sie dann die erste Website unter **Zielhostname** aus, d.h.  *myAppServicePlanEastUS.azurewebsites.net*.
4. Lassen Sie die übrigen Felder zunächst unverändert, und klicken Sie auf **Hinzufügen**.
5. Wiederholen Sie die Schritte 2 bis 4, um die andere Website (*myAppServicePlanWestEurope.azurewebsites.net*) hinzuzufügen.
6. Optional können Sie die Integritätstests und Lastenausgleichseinstellungen für den Back-End-Pool aktualisieren, aber die Standardwerte sollten ebenfalls funktionieren. Klicken Sie auf **Hinzufügen**.


### <a name="c-add-a-routing-rule"></a>C. Hinzufügen einer Routingregel
Klicken Sie abschließend auf das Symbol „+“ bei den Routingregeln, um eine Routingregel zu konfigurieren. Dies ist erforderlich, um die Front-End-Hosts dem Back-End-Adresspool zuzuordnen. Dabei wird letztlich konfiguriert, dass bei `myappfrontend.azurefd.net` eingehende Anforderungen an den Back-End-Pool `myBackendPool` weitergeleitet werden sollen. Klicken Sie auf **Hinzufügen**, um die Routingregel für Ihre Front Door-Instanz hinzuzufügen. Sie haben nun alle Schritte ausgeführt, um die Front Door-Instanz erstellen zu können. Klicken Sie daher auf **Review and Create** (Überprüfen und erstellen).

>[!WARNING]
> Sie **müssen** sicherstellen, dass jeder Front-End-Host in Ihrer Front Door-Instanz eine Routingregel mit einem Standardpfad („/\*“) aufweist. Das bedeutet, für alle Ihre Routingregeln muss mindestens eine Routingregel für jeden Ihrer Front-End-Hosts unter dem Standardpfad („/\*“) definiert sein. Andernfalls wird der Datenverkehr Ihrer Endbenutzer möglicherweise nicht richtig weitergeleitet.

## <a name="view-front-door-in-action"></a>Anzeigen von Front Door in Aktion
Nachdem Sie eine Front Door-Instanz erstellt haben, dauert es einige Minuten, bis die Konfiguration global bereitgestellt ist. Greifen Sie dann auf den erstellten Front-End-Host zu, d.h., wechseln Sie zu einem Webbrowser, und rufen Sie die URL `myappfrontend.azurefd.net` auf. Ihre Anforderung wird automatisch an das Ihnen nächstgelegene Back-End aus den angegebenen Back-Ends im Back-End-Pool weitergeleitet. 

### <a name="view-front-door-handle-application-failover"></a>Anzeigen des Anwendungsfailovers für das Front Door-Handle
Wenn Sie das sofortige globale Front Door-Failover in Aktion testen möchten, können Sie zu einer der erstellten Websites wechseln und diese beenden. Entsprechend den definierten Integritätstesteinstellungen für den Back-End-Pool findet unverzüglich ein Failover des Datenverkehrs zur anderen Websitebereitstellung statt. Sie können das Verhalten auch testen, indem Sie das Back-End in der Back-End-Poolkonfiguration für Ihre Front Door-Instanz deaktivieren. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie die Ressourcengruppen, Webanwendungen und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen.

## <a name="next-steps"></a>Nächste Schritte
In diesem Schnellstart haben Sie eine Front Door-Instanz erstellt, mit der Sie den Benutzerdatenverkehr für Webanwendungen, die Hochverfügbarkeit und maximale Leistung erfordern, weiterleiten können. Weitere Informationen zum Weiterleiten von Datenverkehr finden unter den von Front Door verwendeten [Routingmethoden](front-door-routing-methods.md).