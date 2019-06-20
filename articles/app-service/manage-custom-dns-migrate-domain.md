---
title: Migrieren eines aktiven DNS-Namens – Azure App Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen benutzerdefinierte DNS-Domänennamen, der bereits einer Livewebsite zugewiesen ist, ohne Ausfallzeiten zu Azure App Service migrieren.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6215230a52bcb5c44f54747b447dc5f64e6af650
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130380"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrieren eines aktiven DNS-Namens zu Azure App Service

In diesem Artikel erfahren Sie, wie Sie einen aktiven DNS-Namen ohne Ausfallzeiten zu [Azure App Service](../app-service/overview.md) migrieren.

Wenn Sie eine Livewebsite und den dazugehörigen DNS-Domänennamen zu App Service migrieren, wird über diesen DNS-Namen bereits Livedatenverkehr abgewickelt. Damit während der Migration keine Ausfallzeiten bei der DNS-Auflösung auftreten, können Sie den aktiven DNS-Namen vorab an Ihre App Service-App binden.

Wenn ein Ausfall der DNS-Auflösung kein Problem darstellt, finden Sie weitere Informationen unter [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie den folgenden Vorbereitungsschritt aus:

- [Vergewissern Sie sich, dass sich Ihre App Service-App nicht im FREE-Tarif befindet.](app-service-web-tutorial-custom-domain.md#checkpricing)

## <a name="bind-the-domain-name-preemptively"></a>Binden des Domänennamens bereits im Vorfeld

Wenn Sie eine benutzerdefinierte Domäne bereits im Vorfeld binden, haben Sie die folgenden beiden Vorgänge ausgeführt, bevor Sie Änderungen an Ihren DNS-Einträgen vornehmen:

- Überprüfen des Domänenbesitzes
- Aktivieren des Domänennamens für Ihre App

Wenn Sie dann Ihren benutzerdefinierten DNS-Namen von der alten Website zur App Service-App migrieren, treten bei der DNS-Auflösung keinerlei Ausfälle auf.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Erstellen eines Domänenüberprüfungseintrags

Fügen Sie zum Überprüfen des Domänenbesitzes einen TXT-Eintrag hinzu. Der TXT-Eintrag dient zur Zuordnung von _awverify.&lt;Unterdomäne>_ zu _&lt;App-Name>.azurewebsites.net_. 

Welchen TXT-Eintrag Sie benötigen, hängt vom zu migrierenden DNS-Eintrag ab. Beispiele finden Sie in der folgenden Tabelle. (`@` stellt in der Regel die Stammdomäne dar.)

| DNS-Beispieleintrag | TXT-Host | TXT-Wert |
| - | - | - |
| \@ (Stamm) | _awverify_ | _&lt;App-Name&gt;.azurewebsites.net_ |
| www (Unterdomäne) | _awverify.www_ | _&lt;App-Name&gt;.azurewebsites.net_ |
| \* (Platzhalter) | _awverify.\*_ | _&lt;App-Name&gt;.azurewebsites.net_ |

Beachten Sie auf der Seite mit den DNS-Einträgen den Eintragstyp des DNS-Namens, den Sie migrieren möchten. App Service unterstützt Zuordnungen von CNAME- und A-Einträgen.

> [!NOTE]
> Für bestimmte Anbieter, z.B. CloudFlare, ist `awverify.*` kein gültiger Datensatz. Verwenden Sie stattdessen nur `*`.

> [!NOTE]
> Platzhalterdatensätze (`*`) überprüfen keine Unterdomänen mit vorhandenem CNAME-Eintrag. Möglicherweise müssen Sie für jede Unterdomäne explizit einen TXT-Eintrag erstellen.


### <a name="enable-the-domain-for-your-app"></a>Aktivieren der Domäne für Ihre App

Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich der App-Seite die Option **Benutzerdefinierte Domänen** aus. 

![Menü „Benutzerdefinierte Domänen“](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Wählen Sie auf der Seite **Benutzerdefinierte Domänen** neben **Hostname hinzufügen** das Symbol **+** aus.

![Hinzufügen des Hostnamens](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Geben Sie den vollqualifizierten Domänennamen ein, für den Sie den TXT-Eintrag hinzugefügt haben (beispielsweise `www.contoso.com`). Für eine Platzhalterdomäne (Beispiel: \*.contoso.com), können Sie einen beliebigen DNS-Namen verwenden, der der Platzhalterdomäne entspricht. 

Wählen Sie **Überprüfen**.

Die Schaltfläche **Hostnamen hinzufügen** wird aktiviert. 

Vergewissern Sie sich, dass **Typ des Hostnamenseintrags** auf den DNS-Eintragstyp festgelegt ist, den Sie migrieren möchten.

Wählen Sie **Hostnamen hinzufügen**.

![Hinzufügen des DNS-Namens zur App](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Unter Umständen dauert es eine Weile, bis der neue Hostname auf der Seite **Benutzerdefinierte Domänen** der App angezeigt wird. Aktualisieren Sie den Browser, um die Daten zu aktualisieren.

![Hinzugefügter CNAME-Eintrag](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Ihr benutzerdefinierter DNS-Name ist nun in Ihrer Azure-App aktiviert. 

## <a name="remap-the-active-dns-name"></a>Neuzuordnen des aktiven DNS-Namens

Nun muss nur noch der aktive DNS-Eintrag neu zugeordnet werden, damit er auf App Service verweist. Momentan verweist er immer noch auf Ihre alte Website.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Kopieren der IP-Adresse der App (nur A-Eintrag)

Wenn Sie einen CNAME-Eintrag neu zuordnen, überspringen Sie diesen Abschnitt. 

Für die Neuzuordnung eines A-Eintrags benötigen Sie die externe IP-Adresse der App Service-App (zu finden auf der Seite **Benutzerdefinierte Domänen**).

Wählen Sie rechts oben das **X** aus, um die Seite **Hostname hinzufügen** zu schließen. 

Kopieren Sie auf der Seite **Benutzerdefinierte Domänen** die IP-Adresse der App.

![Portalnavigation zur Azure-App](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Aktualisieren des DNS-Eintrags

Wählen Sie auf der DNS-Eintragsseite Ihres Domänenanbieters den neu zuzuordnenden DNS-Eintrag aus.

Ordnen Sie für das Stammdomänenbeispiel `contoso.com` den A- oder CNAME-Eintrag wie bei den Beispielen in der folgenden Tabelle zu: 

| FQDN-Beispiel | Eintragstyp | Host | Wert |
| - | - | - | - |
| contoso.com (Stammdomäne) | Eine Datei | `@` | IP-Adresse aus dem Schritt [Kopieren der IP-Adresse der App](#info) |
| www\.contoso.com (Unterdomäne) | CNAME | `www` | _&lt;App-Name&gt;.azurewebsites.net_ |
| \*.contoso.com (Platzhalter) | CNAME | _\*_ | _&lt;App-Name&gt;.azurewebsites.net_ |

Speichern Sie die Einstellungen.

DNS-Abfragen werden umgehend nach der DNS-Verteilung zu Ihrer App Service-App aufgelöst.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie ein benutzerdefiniertes SSL-Zertifikat an App Service binden.

> [!div class="nextstepaction"]
> [Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure App Service](app-service-web-tutorial-custom-ssl.md)
