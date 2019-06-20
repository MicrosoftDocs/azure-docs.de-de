---
title: Bereitstellen von Azure API Management-Diensten in mehreren Azure-Regionen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure API Management-Dienstinstanz für mehrere Azure-Regionen bereitstellen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: d22da92355616c208c7616b4b0e8c26b7f9e7006
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60658152"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen

Azure API Management unterstützt eine Bereitstellung für mehrere Regionen, wodurch API-Herausgeber einen einzelnen API-Verwaltungsdienst in einer beliebigen Anzahl von gewünschten Azure-Regionen zur Verfügung stellen können. Dies trägt dazu bei, die Anforderungslatenz bei geografisch verteilten API-Nutzern zu verringern, und verbessert gleichzeitig die Dienstverfügbarkeit, wenn eine Region offline geht.

Ein neuer Azure API Management-Dienst enthält zunächst nur eine [Einheit][unit] in einer Azure-Region (der primären Region). Mit dem Azure-Portal können auf einfache Weise weitere Regionen hinzugefügt werden. In jeder Region wird ein API Management-Gatewayserver bereitgestellt, und Datenverkehr durch Aufrufe wird an das nächstgelegene Gateway (das mit der geringsten Latenz) geroutet. Wenn eine Region offline geht, wird der Datenverkehr automatisch an das nächstgelegene Gateway umgeleitet.

> [!NOTE]
> Azure API Management repliziert nur die API-Gatewaykomponente regionsübergreifend. Die Dienstverwaltungskomponente wird nur in der primären Region gehostet. Im Falle eines Ausfalls in der primären Region ist es nicht möglich, Konfigurationsänderungen auf eine Azure API Management-Dienstinstanz anzuwenden (einschließlich Einstellungen oder Richtlinienupdates).

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"></a>Bereitstellen einer API Management-Dienstinstanz für eine neue Region

> [!NOTE]
> Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen unter [Erstellen einer API Management-Dienstinstanz][Create an API Management service instance].

Navigieren Sie im Azure-Portal zur Seite **Skalierung und Preise** für Ihre API Management-Dienstinstanz. 

![Registerkarte "Skalieren"][api-management-scale-service]

Klicken Sie auf der Symbolleiste auf **+ Region hinzufügen**, um die Bereitstellung in einer neuen Region auszuführen.

![Region hinzufügen][api-management-add-region]

Wählen Sie den Standort in der Dropdownliste aus, und legen Sie die Anzahl der Einheiten mit dem Schieberegler fest.

![Einheiten angeben][api-management-select-location-units]

Klicken Sie auf **Hinzufügen** um Ihre Auswahl in der Tabelle „Standorte“ zu platzieren. 

Wiederholen Sie diesen Vorgang, bis Sie alle Standorte konfiguriert haben, und klicken Sie auf der Symbolleiste auf **Speichern**, um den Bereitstellungsprozess zu starten.

## <a name="remove-region"></a>Löschen einer API Management-Dienstinstanz aus einem Standort

Navigieren Sie im Azure-Portal zur Seite **Skalierung und Preise** für Ihre API Management-Dienstinstanz. 

![Registerkarte "Skalieren"][api-management-scale-service]

Öffnen Sie für den Standort, den Sie entfernen möchten, das Kontextmenü mit der Schaltfläche **...** ganz rechts in der Tabelle. Wählen Sie die Option **Löschen** aus.

Bestätigen Sie den Löschvorgang, und klicken Sie auf **Speichern** um die Änderungen zu übernehmen.

## <a name="route-backend"> </a>Weiterleiten von API-Aufrufen an regionale Back-End-Dienste

Azure API Management bietet nur eine Back-End-Dienst-URL. Obwohl in verschiedenen Regionen Azure API Management-Instanzen vorhanden sind, leitet das API-Gateway Anforderungen dennoch an denselben Back-End-Dienst weiter, der nur in einer Region bereitgestellt wird. In diesem Fall wird der Leistungsgewinn nur durch Antworten erzielt, die innerhalb von Azure API Management in einer für die Anforderung spezifischen Region zwischengespeichert werden, aber die Kontaktaufnahme mit dem Back-End weltweit kann dennoch zu einer hohen Latenz führen.

Um die geografische Verteilung Ihres Systems voll auszuschöpfen, sollten Sie Back-End-Dienste in den gleichen Regionen wie die Azure API Management-Instanzen bereitstellen. Anschließend können Sie mithilfe von Richtlinien und der `@(context.Deployment.Region)`-Eigenschaft den Datenverkehr an lokale Instanzen Ihres Back-Ends weiterleiten.

1. Navigieren Sie zu Ihrer Azure API Management-Instanz, und klicken Sie im Menü auf der linken Seite auf **APIs**.
2. Wählen Sie die gewünschte API aus.
3. Klicken Sie in der Pfeildropdownliste in **Verarbeitung von eingehendem Datenverkehr** auf **Code-Editor**.

    ![API-Code-Editor](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Verwenden Sie `set-backend` in Kombination mit bedingten `choose`-Richtlinien zum Erstellen einer ordnungsgemäßen Routingrichtlinie im Abschnitt `<inbound> </inbound>` der Datei.

    Die unten gezeigte XML-Datei würde z.B. für die Regionen „USA, Westen“ und „Asien, Osten“ funktionieren:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Sie können Ihren Back-End-Diensten auch [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) vorschalten, die API-Aufrufe an Traffic Manager weiterleiten und diesem das automatische Routing überlassen.

## <a name="custom-routing"> </a>Verwenden von benutzerdefiniertem Routing an regionale API Management-Gateways

API Management leitet die Anforderungen basierend auf [der geringsten Latenz](../traffic-manager/traffic-manager-routing-methods.md#performance) an ein regionales *Gateway* weiter. Es ist zwar nicht möglich, diese Einstellung in API Management außer Kraft zu setzen, Sie können jedoch Ihre eigene Traffic Manager-Instanz mit benutzerdefinierten Routingregeln verwenden.

1. Erstellen Sie Ihren eigenen [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Bei Verwendung einer benutzerdefinierten Domäne [verwenden Sie sie mit Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) anstelle des API Management-Diensts.
1. [Konfigurieren Sie die regionalen API Management-Endpunkte in Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Die regionalen Endpunkte folgen dem URL-Muster `https://<service-name>-<region>-01.regional.azure-api.net`, z. B. `https://contoso-westus2-01.regional.azure-api.net`.
1. [Konfigurieren Sie die regionalen API Management-Statusendpunkte in Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Die regionalen Statusendpunkte folgen dem URL-Muster `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, z. B. `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Geben Sie die [Routingmethode](../traffic-manager/traffic-manager-routing-methods.md) von Traffic Manager an.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/
