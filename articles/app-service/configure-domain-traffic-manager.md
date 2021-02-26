---
title: Konfigurieren von DNS-Namen mit Traffic Manager
description: Erfahren Sie, wie Sie eine benutzerdefinierte Domäne für eine Azure App Service-App konfigurieren, die für den Lastenausgleich mit Traffic Manager integriert ist.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: e4d4b7e01eb5799bee604c05e1660a7a45188763
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223339"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service mit Traffic Manager-Integration

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Informationen zu Clouddiensten finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Clouddienst](../cloud-services/cloud-services-custom-domain-name-portal.md)

Wenn Sie [Azure Traffic Manager](../traffic-manager/index.yml) für den Lastenausgleich des Datenverkehrs zum [Azure App Service](overview.md) verwenden, kann mithilfe von **\<traffic-manager-endpoint>.trafficmanager.net** auf die App Service-App zugegriffen werden. Sie können Ihrer App Service-App einen benutzerdefinierten Domänennamen wie „www\.contoso.com“ zuweisen, um Ihren Benutzern einen einprägsameren Domänennamen zur Verfügung zu stellen.

In diesem Artikel wird gezeigt, wie Sie einen benutzerdefinierten Domänenname mit einer Azure App Service-App konfigurieren, die in [Traffic Manager](../traffic-manager/traffic-manager-overview.md) integriert ist.

> [!NOTE]
> Nur [CNAME](https://en.wikipedia.org/wiki/CNAME_record)-Einträge werden unterstützt, wenn Sie einen Domänennamen mithilfe des Traffic Manager-Endpunkts konfigurieren. Da keine A-Einträge unterstützt werden, wird auch keine Stammdomänenzuordnung wie „contoso.com“ unterstützt.
> 

## <a name="prepare-the-app"></a>Vorbereiten der App

Um einer App, die in Azure Traffic Manager integriert ist, einen benutzerdefinierten DNS-Namen zuzuordnen, muss der [App Service-Plan](https://azure.microsoft.com/pricing/details/app-service/) der Web-App den Tarif **Standard** oder höher aufweisen. In diesem Schritt stellen Sie sicher, dass sich die App Service-App innerhalb des unterstützten Tarifs befindet.

### <a name="check-the-pricing-tier"></a>Überprüfen des Tarifs

Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App Services**, und wählen Sie den Eintrag aus.

Wählen Sie auf der Seite **App Services** den Namen Ihrer Azure-App aus.

![Portalnavigation zur Azure-App](./media/app-service-web-tutorial-custom-domain/select-app.png)

Wählen Sie im linken Navigationsbereich der App-Seite **Hochskalieren (App Service-Plan)** aus.

![Menü „Zentral hochskalieren“](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Der aktuelle Tarif der App wird durch einen blauen Rahmen hervorgehoben. Stellen Sie sicher, dass die App den Tarif **Standard** oder höher besitzt (jeder Tarif der Kategorie **Produktion** oder **Isoliert**). Wenn dies der Fall ist, schließen Sie die Seite **Hochskalieren**, und fahren Sie mit [Erstellen der CNAME-Zuordnung](#create-the-cname-mapping) fort.

![Überprüfen des Tarifs](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Hochskalieren des App Service-Plans

Wenn Sie Ihre App zentral hochskalieren müssen, wählen Sie einen der Tarife in der Kategorie **Produktion** aus. Klicken Sie auf **Alle Optionen anzeigen**, um weitere Optionen anzuzeigen.

Klicken Sie auf **Anwenden**.

## <a name="create-traffic-manager-endpoint"></a>Erstellen des Traffic Manager-Endpunkts

Befolgen Sie die Schritte unter [Hinzufügen und Löschen von Endpunkten](../traffic-manager/traffic-manager-manage-endpoints.md), um Ihre App Service-App dem Traffic Manager-Profil als Endpunkt hinzuzufügen.

Sobald Ihre App Service-App einen unterstützten Tarif aufweist, wird sie in der Liste der verfügbaren App Service-Ziele angezeigt, wenn Sie den Endpunkt hinzufügen. Wenn Ihre App nicht aufgeführt wird, [überprüfen Sie den Tarif Ihrer App](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>Erstellen der CNAME-Zuordnung
> [!NOTE]
> Um eine [App Service-Domäne zu konfigurieren, die Sie gekauft haben](manage-custom-dns-buy-domain.md), überspringen Sie diesen Abschnitt, und wechseln Sie zu [Aktivieren einer benutzerdefinierten Domäne](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Die Details variieren je nach Domänenanbieter. Generell erfolgt die Zuordnung jedoch *von* einem [benutzerdefinierten Nicht-Stammdomänennamen](#what-about-root-domains) (etwa **www.contoso.com**) *zum* Traffic Manager-Domänennamen (**contoso.trafficmanager.net**), der in Ihre App integriert ist. 

> [!NOTE]
> Wenn ein Eintrag bereits verwendet wird und Sie Ihre Apps präemptiv an ihn binden müssen, können Sie einen zusätzlichen CNAME-Eintrag erstellen. Um beispielsweise **www\.contoso.com** präemptiv an Ihre App zu binden, erstellen Sie einen CNAME-Eintrag zur Verknüpfung von **awverify.www** mit **contoso.trafficmanager.net**. Anschließend können Sie Ihrer App „www\.contoso.com“ hinzufügen, ohne den CNAME-Eintrag „www“ ändern zu müssen. Weitere Informationen finden Sie unter [Migrieren eines aktiven DNS-Namens zu Azure App Service](manage-custom-dns-migrate-domain.md).

Speichern Sie die Änderungen, sobald Sie die DNS-Datensätze bei Ihrem Domänenanbieter hinzugefügt oder geändert haben.

### <a name="what-about-root-domains"></a>Wie verhält es sich mit Stammdomänen?

Da Traffic Manager nur die Zuordnung benutzerdefinierter Domänen mit CNAME-Einträgen unterstützt und da die DNS-Standards keine CNAME-Einträge für die Zuordnung von Stammdomänen unterstützen (z. B. **contoso.com**), unterstützt Traffic Manager keine Zuordnung zu Stammdomänen. Um dieses Problem zu umgehen, verwenden Sie eine URL-Umleitung auf App-Ebene. In ASP.NET Core können Sie beispielsweise die [URL-Umschreibung](/aspnet/core/fundamentals/url-rewriting) verwenden. Verwenden Sie dann Traffic Manager, um einen Lastausgleich für die Unterdomäne (**www.contoso.com**) vorzunehmen. Ein anderer Ansatz besteht darin, [einen Aliaseintrag für den Domänennamen-Apex zu erstellen, um auf ein Azure Traffic Manager-Profil zu verweisen](https://docs.microsoft.com/azure/dns/tutorial-alias-tm). Ein Beispiel wäre etwa „contoso.com“. Statt einen Umleitungsdienst zu verwenden, können Sie Azure DNS so konfigurieren, dass direkt von Ihrer Zone aus auf ein Traffic Manager-Profil verwiesen wird. 

In Hochverfügbarkeitsszenarien können Sie eine DNS-Einrichtung mit Lastenausgleich ohne Traffic Manager implementieren, indem Sie mehrere *A-Einträge* erstellen, die von der Stammdomäne auf die IP-Adresse der einzelnen App-Kopien verweisen. Dann [ordnen Sie dieselbe Stammdomäne allen App-Kopien zu](app-service-web-tutorial-custom-domain.md#map-an-a-record). Da derselbe Domänenname nicht zwei verschiedenen Apps in derselben Region zugeordnet werden kann, funktioniert diese Einrichtung nur, wenn sich die App-Kopien in unterschiedlichen Regionen befinden.

## <a name="enable-custom-domain"></a>Aktivieren einer benutzerdefinierten Domäne
Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, verwenden Sie den Browser, um zu überprüfen, ob Ihr benutzerdefinierter Domänenname zu Ihrer App Service-App aufgelöst wird.

> [!NOTE]
> Es kann einige Zeit dauern, bis Ihr CNAME über das DNS-System weitergegeben wurde. Mithilfe eines Diensts wie <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> können Sie überprüfen, ob der CNAME verfügbar ist.
> 
> 

1. Sobald die Domänenauflösung erfolgreich ist, kehren Sie zur Seite Ihrer App im [Azure-Portal](https://portal.azure.com) zurück.
2. Wählen Sie im linken Navigationsbereich **Benutzerdefinierte Domänen** > **Hostnamen hinzufügen** aus.
4. Geben Sie den benutzerdefinierten Domänennamen ein, den Sie zuvor zugeordnet haben, und wählen Sie **Überprüfen** aus.
5. Stellen Sie sicher, dass der **Typ des Hostnamenseintrags** auf **CNAME (www\.beispiel.com oder eine beliebige Unterdomäne)** festgelegt ist.

6. Da die App Service-App jetzt in einen Traffic Manager-Endpunkt integriert ist, sollte der Traffic Manager-Domänenname unter **CNAME-Konfiguration** angezeigt werden. Wählen Sie ihn aus, und klicken Sie auf **Benutzerdefinierte Domäne hinzufügen**.

    ![Hinzufügen des DNS-Namens zur App](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schützen eines benutzerdefinierten DNS-Namens mit einer SSL-Bindung in Azure App Service](configure-ssl-bindings.md)
