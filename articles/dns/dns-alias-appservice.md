---
title: Hosten von Azure Web-Apps mit Lastenausgleich im Zonen-Apex
description: Verwenden eines Azure DNS-Aliasdatensatzes für Web-Apps mit Lastenausgleich im Zonen-Apex
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 11/3/2018
ms.author: victorh
ms.openlocfilehash: b08eae072c2fbe420401424baf97a25b4cbbe87b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790741"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hosten von Azure Web-Apps mit Lastenausgleich im Zonen-Apex

Das DNS-Protokoll sorgt dafür, dass im Zonen-Apex nur A- oder AAAA-Datensätze zugewiesen werden können. Ein Beispiel für einen Zonen-Apex ist contoso.com. Diese Einschränkung stellt Anwendungsbesitzer, die über Anwendungen mit Lastenausgleich hinter Traffic Manager verfügen, vor ein Problem. Über den Zonen-Apex-Datensatz kann nicht auf das Traffic Manager-Profil verwiesen werden. Anwendungsbesitzer müssen daher auf eine Problemumgehung zurückgreifen: eine Umleitung in der Anwendungsschicht vom Zonen-Apex zu einer anderen Domäne, beispielsweise von „contoso.com“ zu „www\.contoso.com“. Dies stellt einen Single Point of Failure für die Umleitungsfunktion dar.

Bei Aliaseinträgen besteht dieses Problem nicht mehr. Nun können Anwendungsbesitzer für Ihren Zonen-Apex-Datensatz einen Verweis auf ein Traffic Manager-Profil erstellen, das über externe Endpunkte verfügt. Anwendungsbesitzer können auf das gleiche Traffic Manager-Profil verweisen, das auch für andere Domänen innerhalb ihrer DNS-Zone verwendet wird.

So können beispielsweise „contoso.com“ und „www\.contoso.com“ jeweils auf das gleiche Traffic Manager-Profil verweisen. Voraussetzung ist allerdings, dass für das Traffic Manager-Profil nur externe Endpunkte konfiguriert sind.

In diesem Artikel erfahren Sie, wie Sie einen Alias-Datensatz für Ihren Domänen-Apex erstellen und Ihre Traffic Manager-Profilendpunkte für Ihre Web-Apps konfigurieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen über einen Domänennamen verfügen, den Sie zum Testen in Azure DNS hosten können. Sie müssen uneingeschränkte Kontrolle über diese Domäne haben. Das bedeutet, Sie müssen unter anderem die Namenservereinträge für die Domäne festlegen können.

Eine Anleitung zum Hosten Ihrer Domäne in Azure DNS finden Sie unter [Tutorial: Hosten Ihrer Domäne in Azure DNS](dns-delegate-domain-azure-dns.md).

Die für dieses Tutorial verwendete Beispieldomäne lautet „contoso.com“. Verwenden Sie aber ruhig Ihren eigenen Domänennamen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie eine Ressourcengruppe für die in diesem Artikel verwendeten Ressourcen.

## <a name="create-app-service-plans"></a>Erstellen von App Service-Plänen

Erstellen Sie zwei Web App Service-Pläne in Ihrer Ressourcengruppe anhand der folgenden Tabelle für Konfigurationsinformationen. Weitere Informationen zum Erstellen eines App Service-Plans finden Sie unter [Verwalten eines App Service-Plans in Azure](../app-service/app-service-plan-manage.md).


|NAME  |Betriebssystem  |Location  |Preisstufe  |
|---------|---------|---------|---------|
|ASP-01     |Windows|USA (Ost)|Dev/Test D1-Freigegeben|
|ASP-02     |Windows|USA (Mitte)|Dev/Test D1-Freigegeben|

## <a name="create-app-services"></a>Erstellen von App Services

Erstellen Sie zwei Web-Apps, jeweils eine in jedem App Service-Plan.

1. Klicken Sie im Azure-Portal links oben auf **Eine Ressource erstellen**.
2. Geben Sie **Web-App** in die Suchleiste ein, und drücken Sie auf die Eingabetaste.
3. Klicken Sie auf **Web-App**.
4. Klicken Sie auf **Create**.
5. Akzeptieren Sie die Standardeinstellungen, und verwenden Sie die folgende Tabelle, um die beiden Web-Apps zu konfigurieren:

   |NAME<br>(muss innerhalb von .azurewebsites.net eindeutig sein)|Ressourcengruppe |App Service-Plan/Standort
   |---------|---------|---------|
   |App-01|Vorhandene verwenden<br>Ressourcengruppe auswählen|ASP-01(USA, Osten)|
   |App-02|Vorhandene verwenden<br>Ressourcengruppe auswählen|ASP-02(USA, Mitte)|

### <a name="gather-some-details"></a>Erfassen von Details

Notieren Sie jetzt die IP-Adresse und den Hostnamen für die Apps.

1. Öffnen Sie Ihre Ressourcengruppe, und klicken Sie auf Ihre erste App (**App-01** in diesem Beispiel).
2. Klicken Sie in der linken Spalte auf **Eigenschaften**.
3. Notieren Sie die Adresse unter **URL** und unter **Ausgehenden IP-Adressen** die erste IP-Adresse in der Liste. Diese Informationen sind später beim Konfigurieren der Traffic Manager-Endpunkte erforderlich.
4. Wiederholen Sie den Schritt für **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

Erstellen Sie in Ihrer Ressourcengruppe ein Traffic Manager-Profil. Verwenden Sie die Standardeinstellungen, und geben Sie einen eindeutigen Namen innerhalb des trafficmanager.net-Namespace ein.

Informationen zum Erstellen eines Traffic Manager-Profils finden Sie unter [Schnellstart: Erstellen eines Traffic Manager-Profils für eine hochverfügbare Webanwendung](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Erstellen von Endpunkten

Jetzt können Sie die Endpunkte für die beiden Web-Apps erstellen.

1. Öffnen Sie Ihre Ressourcengruppe, und klicken Sie auf Ihr Traffic Manager-Profil.
2. Klicken Sie in der linken Spalte auf **Endpunkte**.
3. Klicken Sie auf **Hinzufügen**.
4. Verwenden Sie die folgende Tabelle zum Konfigurieren der Endpunkte:

   |Type  |NAME  |Ziel  |Location  |Benutzerdefinierte Headereinstellungen|
   |---------|---------|---------|---------|---------|
   |Externer Endpunkt     |End-01|IP-Adresse, die Sie für App-01 notiert haben|USA (Ost)|host:\<die URL, die Sie für App-01 notiert haben\><br>Beispiel: **host:app-01.azurewebsites.net**|
   |Externer Endpunkt     |End-02|IP-Adresse, die Sie für App-02 notiert haben|USA (Mitte)|host:\<die URL, die Sie für App-02 notiert haben\><br>Beispiel: **host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Erstellen einer DNS-Zone

Sie können entweder eine vorhandene DNS-Zone für Tests verwenden, oder Sie können eine neue Zone erstellen. Informationen zum Erstellen und Delegieren einer neuen DNS-Zone in Azure finden Sie unter [Tutorial: Hosten Ihrer Domäne in Azure DNS](dns-delegate-domain-azure-dns.md).

### <a name="add-the-alias-record-set"></a>Hinzufügen der Aliasdatensatzgruppe

Wenn Ihre DNS-Zone bereit ist, können Sie einen Alias-Datensatz für den Zonen-Apex hinzufügen.

1. Öffnen Sie die Ressourcengruppe, und klicken Sie auf die DNS-Zone.
2. Klicken Sie auf **Datensatzgruppe**.
3. Fügen Sie die Datensatzgruppe mithilfe der folgenden Tabelle hinzu:

   |NAME  |Type  |Alias-Datensatzgruppe  |Aliastyp  |Azure-Ressource|
   |---------|---------|---------|---------|-----|
   |@     |Eine Datei|Ja|Azure-Ressource|Traffic Manager – Ihr Profil|

## <a name="add-custom-hostnames"></a>Hinzufügen von benutzerdefinierten Hostnamen

Fügen Sie einen benutzerdefinierten Hostnamen für beide Web-Apps hinzu.

1. Öffnen Sie Ihre Ressourcengruppe, und klicken Sie auf Ihre erste Web-App.
2. Klicken Sie in der linken Spalte auf **Benutzerdefinierte Domäne**.
3. Klicken Sie auf **Hostname hinzufügen**.
4. Geben Sie unter „Hostname“ Ihren Domänennamen ein. Beispiel: contoso.com.

   Ihre Domäne muss die Überprüfung bestehen und muss neben **Verfügbarkeit des Hostnamens** und **Domänenbesitz** ein grünes Häkchen anzeigen.
5. Klicken Sie auf **Hostname hinzufügen**.
6. Um den neuen Hostnamen unter **Der Website zugewiesene Hostnamen** anzuzeigen, aktualisieren Sie Ihren Browser. Bei der Aktualisierung der Seite werden die Änderungen nicht immer direkt angezeigt.
7. Wiederholen Sie diese Schritte für Ihre zweite Web-App.

## <a name="test-your-web-apps"></a>Testen Ihrer Web-Apps

Sie können Ihre Web-App jetzt testen, um sicherzustellen, dass Sie sie erreichen und dass der Lastenausgleich ausgeführt wird.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu Ihrer Domäne. Beispiel: contoso.com. Es sollte die Web-App-Standardseite angezeigt werden.
2. Beenden Sie Ihre erste Web-App.
3. Schließen Sie Ihren Webbrowser, und warten Sie einige Minuten.
4. Starten Sie Ihren Webbrowser, und navigieren Sie zu Ihrer Domäne. Es sollte immer noch die Web-App-Standardseite angezeigt werden.
5. Beenden Sie Ihre zweite Web-App.
6. Schließen Sie Ihren Webbrowser, und warten Sie einige Minuten.
7. Starten Sie Ihren Webbrowser, und navigieren Sie zu Ihrer Domäne. Es sollte Fehler 403 angezeigt werden. Das bedeutet, dass die Web-App beendet wurde.
8. Starten Sie Ihre zweite Web-App.
9. Schließen Sie Ihren Webbrowser, und warten Sie einige Minuten.
10. Starten Sie Ihren Webbrowser, und navigieren Sie zu Ihrer Domäne. Es sollte wieder die Web-App-Standardseite angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Aliaseinträgen finden Sie in den folgenden Artikeln:

- [Tutorial: Konfigurieren eines Aliaseintrags, um auf eine öffentliche Azure-IP-Adresse zu verweisen](tutorial-alias-pip.md)
- [Tutorial: Konfigurieren eines Alias-Ressourceneintrags zur Unterstützung von Apex-Domänennamen mit Traffic Manager](tutorial-alias-tm.md)
- [Häufig gestellte Fragen zu DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
