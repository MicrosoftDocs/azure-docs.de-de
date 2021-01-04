---
title: 'Tutorial: Hinzufügen einer benutzerdefinierten Domäne zu Ihrer Azure Front Door Service-Konfiguration'
description: In diesem Tutorial erfahren Sie, wie Sie eine benutzerdefinierte Domäne in Azure Front Door Service integrieren.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: duau
ms.openlocfilehash: e153edd807dcb119c34f60dc34e33fed510916bb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011522"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Tutorial: Hinzufügen einer benutzerdefinierten Domäne für Ihre „Front Door“
In diesem Tutorial erfahren Sie, wie Sie Ihrer Azure Front Door Service-Konfiguration eine benutzerdefinierte Domäne hinzufügen. Wenn Sie Azure Front Door für die Anwendungsbereitstellung verwenden, ist eine benutzerdefinierte Domäne erforderlich, sofern Ihr eigener Domänenname in der Endbenutzeranforderung sichtbar sein soll. Die Verwendung eines sichtbaren Domänennamens kann für Ihre Kunden komfortabel und für Branding-Zwecke hilfreich sein.

Nachdem Sie eine Front Door-Instanz erstellt haben, wird der Front-End-Standardhost (eine Unterdomäne von `azurefd.net`) standardmäßig in die URL für die Übermittlung von Front Door-Inhalten von Ihrem Back-End eingeschlossen (z. B. „https:\//contoso-frontend.azurefd.net/activeusers.htm“). Zur Vereinfachung bietet Azure Front Door Service Ihnen die Möglichkeit, dem Standardhost eine benutzerdefinierte Domäne zuzuordnen. Bei Verwendung dieser Option stellen Sie Ihre Inhalte mit einer benutzerdefinierten Domäne in Ihrer URL bereit und nicht über einen Domänennamen im Besitz von Azure Front Door Service (z. B. „https:\//www.contoso.com/photo.png“). 

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> - Erstellen eines CNAME-DNS-Eintrags
> - Zuordnen der benutzerdefinierten Domäne zu Ihrer Azure Front Door Service-Konfiguration
> - Überprüfen der benutzerdefinierten Domäne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Für Front Door werden benutzerdefinierte Domänen mit [Punycode](https://en.wikipedia.org/wiki/Punycode)-Zeichen **nicht** unterstützt. 

## <a name="prerequisites"></a>Voraussetzungen

* Bevor Sie die Schritte in diesem Tutorial ausführen können, müssen Sie zunächst eine Azure Front Door Service-Konfiguration erstellen. Weitere Informationen finden Sie unter [Quickstart: Erstellen einer „Front Door“](quickstart-create-front-door.md).

* Wenn Sie nicht bereits über eine benutzerdefinierte Domäne verfügen, müssen Sie zunächst bei einem Domänenanbieter eine erwerben. Informationen hierzu finden Sie beispielsweise unter [Kaufen eines benutzerdefinierten Domänennamens für Azure-Web-Apps](../app-service/manage-custom-dns-buy-domain.md).

* Wenn Sie Azure zum Hosten Ihrer [DNS-Domänen](../dns/dns-overview.md) verwenden, müssen Sie das Domain Name System (DNS) des Domänenanbieters an eine Azure DNS-Instanz delegieren. Weitere Informationen finden Sie unter [Delegieren einer Domäne an Azure DNS](../dns/dns-delegate-domain-azure-dns.md). Fahren Sie andernfalls mit [Erstellen eines CNAME-DNS-Eintrags](#create-a-cname-dns-record) fort, wenn Sie für Ihre DNS-Domäne einen Domänenanbieter verwenden.


## <a name="create-a-cname-dns-record"></a>Erstellen eines CNAME-DNS-Eintrags

Bevor Sie eine benutzerdefinierte Domäne mit Ihrer Azure Front Door Service-Konfiguration verwenden können, müssen Sie zunächst einen kanonischen Namenseintrag (CNAME) bei Ihrem Domänenanbieter erstellen, der auf den Front-End-Standardhost von Azure Front Door Service verweist (z. B. „contoso.azurefd.net“). Ein CNAME-Eintrag ist eine Art von DNS-Eintrag, mit dem ein Quelldomänenname einem Zieldomänennamen zugeordnet wird. Für Azure Front Door ist der Quelldomänenname Ihr benutzerdefinierter Domänenname und der Zieldomänenname der Name Ihres Azure Front Door-Standardhosts. Nachdem der von Ihnen erstellte CNAME-Eintrag von Azure Front Door überprüft wurde, wird der für die benutzerdefinierte Quelldomäne (z. B. „www\.contoso.com“) bestimmte Datenverkehr an den angegebenen Ziel-Front-End-Standardhost von Azure Front Door Service geleitet (z. B. „contoso-frontend.azurefd.net“). 

Eine benutzerdefinierte Domäne und die zugehörige Unterdomäne können jeweils nur mit einer Azure Front Door-Konfiguration verknüpft werden. Sie können aber unterschiedliche Unterdomänen derselben benutzerdefinierten Domäne für unterschiedliche Azure Front Door-Konfigurationen verwenden, indem Sie mehrere CNAME-Einträge erstellen. Außerdem können Sie eine benutzerdefinierte Domäne mit unterschiedlichen Unterdomänen derselben Azure Front Door-Konfiguration zuordnen.


## <a name="map-the-temporary-afdverify-subdomain"></a>Zuordnen der temporären Unterdomäne „afdverify“

Es müssen bestimmte Aspekte berücksichtigt werden, wenn Sie eine vorhandene Domäne zuordnen, die sich in der Produktion befindet. Während Sie Ihre benutzerdefinierte Domäne im Azure-Portal registrieren, kann es für die Domäne zu einer kurzen Ausfallzeit kommen. Ordnen Sie Ihre benutzerdefinierte Domäne zuerst Ihrem Azure Front Door-Front-End-Standardhost mit der Azure-Unterdomäne „afdverify“ zu, um eine temporäre CNAME-Zuordnung zu erstellen und so eine Unterbrechung des Webdatenverkehrs zu verhindern. Mit dieser Methode können Benutzer ohne Unterbrechung auf Ihre Domäne zugreifen, während die DNS-Zuordnung durchgeführt wird.

Falls Sie Ihre benutzerdefinierte Domäne zum ersten Mal verwenden und kein Produktionsdatenverkehr in der Domäne stattfindet, können Sie die benutzerdefinierte Domäne auch direkt Ihrer Azure Front Door Service-Konfiguration zuordnen. Fahren Sie mit [Zuordnen der permanenten benutzerdefinierten Domäne](#map-the-permanent-custom-domain) fort.

So erstellen Sie einen CNAME-Eintrag mit der Unterdomäne „afdverify“:

1. Melden Sie sich an der Website des Domänenanbieters für Ihre benutzerdefinierte Domäne an.

2. Suchen Sie mithilfe der Dokumentation des Anbieters nach der Seite für die Verwaltung von DNS-Einträgen, oder suchen Sie nach Bereichen der Website mit der Bezeichnung **Domänenname**, **DNS** oder **Namensserververwaltung**. 

3. Erstellen Sie einen CNAME-Eintrag für Ihre benutzerdefinierte Domäne, und füllen Sie die Felder wie in der folgenden Tabelle gezeigt aus (Feldnamen können variieren):

    | `Source`                    | type  | Destination                     |
    |---------------------------|-------|---------------------------------|
    | afdverify. www.contoso.com | CNAME | afdverify.contoso-frontend.azurefd.net |

    - Quelle: Geben Sie den Namen Ihrer benutzerdefinierten Domäne einschließlich der Unterdomäne „afdverify“ im folgenden Format ein: afdverify. _&lt;Name der benutzerdefinierten Domäne&gt;_ . Beispiel: afdverify. www.contoso.com.

    - Typ: Geben Sie *CNAME* ein.

    - Ziel: Geben Sie Ihren Azure Front Door Service-Front-End-Standardhost einschließlich der Unterdomäne „afdverify“ im folgenden Format ein: afdverify. _&lt;Endpunktname&gt;_ .azurefd.net. Beispiel: afdverify.contoso-frontend.azurefd.net.

4. Speichern Sie die Änderungen.

Für die Domänenregistrierungsstelle GoDaddy gilt beispielsweise folgende Vorgehensweise:

1. Melden Sie sich an, und wählen Sie die gewünschte benutzerdefinierte Domäne aus.

2. Wählen Sie im Abschnitt „Domänen“ die Option **Manage All** (Alle verwalten) und dann **DNS** | **Manage Zones** (Zonen verwalten).

3. Geben Sie unter **Domänenname** Ihre benutzerdefinierte Domäne ein, und wählen Sie anschließend **Auswählen**.

4. Wählen Sie auf der Seite **DNS-Verwaltung** die Option **Hinzufügen** und dann in der Liste **Typ** den Eintrag **CNAME**.

5. Füllen Sie für den CNAME-Eintrag die folgenden Felder aus:

    - Typ: Übernehmen Sie *CNAME*.

    - Host: Geben Sie die gewünschte Unterdomäne Ihrer benutzerdefinierten Domäne ein (einschließlich des Unterdomänennamens „afdverify“). Beispiel: afdverify. www.

    - Points to (Verweist auf): Geben Sie den Hostnamen Ihres Azure Front Door Service-Front-End-Standardhosts ein (einschließlich des Unterdomänennamens „afdverify“). Beispiel: afdverify.contoso-frontend.azurefd.net. 

    - Gültigkeitsdauer: Lassen Sie die Auswahl von *Eine Stunde* unverändert.

6. Wählen Sie **Speichern** aus.
 
    Der CNAME-Eintrag wird der Tabelle mit den DNS-Einträgen hinzugefügt.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Zuordnen der benutzerdefinierten Domäne zu Ihrer Azure Front Door Service-Konfiguration

Nachdem Sie Ihre benutzerdefinierte Domäne registriert haben, können Sie sie Ihrer Azure Front Door Service-Konfiguration hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu der Azure Front Door Service-Konfiguration mit dem Front-End-Host, den Sie einer benutzerdefinierten Domäne zuordnen möchten.
    
2. Klicken Sie auf der Seite **Frontdoor-Designer** auf das Plussymbol (+), um eine benutzerdefinierte Domäne hinzuzufügen.
    
3. Wählen Sie **Benutzerdefinierte Domäne** aus. 

4. Für **Front-End-Host** wird der Front-End-Host, der als Zieldomäne Ihres CNAME-Eintrags verwendet werden soll, aus Ihrer Azure Front Door Service-Konfiguration abgeleitet und vorab ausgefüllt: *&lt;Name des Standardhosts&gt;* .azurefd.NET. Diese Einstellung kann nicht geändert werden.

5. Geben Sie unter **Benutzerdefinierter Hostname** Ihre benutzerdefinierte Domäne (einschließlich Unterdomäne) ein, die als Quelldomäne Ihres CNAME-Eintrags verwendet werden soll. Beispiel: „www\.contoso.com“ oder „cdn.contoso.com“. Verwenden Sie nicht den Unterdomänennamen „afdverify“.

6. Wählen Sie **Hinzufügen**.

   Azure überprüft, ob der CNAME-Eintrag für den eingegebenen Namen der benutzerdefinierten Domäne vorhanden ist. Wenn der CNAME-Eintrag korrekt ist, wird Ihre benutzerdefinierte Domäne überprüft.

>[!WARNING]
> Sie **müssen** sicherstellen, dass jedem Front-End-Host (einschließlich benutzerdefinierter Domänen) in Ihrer Azure Front Door Service-Konfiguration eine Routingregel mit einem Standardpfad („/\*“) zugeordnet ist. Das bedeutet, für alle Ihre Routingregeln muss mindestens eine Routingregel für jeden Ihrer Front-End-Hosts unter dem Standardpfad („/\*“) definiert sein. Andernfalls wird der Datenverkehr Ihrer Endbenutzer möglicherweise nicht richtig weitergeleitet.

## <a name="verify-the-custom-domain"></a>Überprüfen der benutzerdefinierten Domäne

Überprüfen Sie nach dem Registrieren Ihrer benutzerdefinierten Domäne, ob diese auf Ihren Azure Front Door Service-Front-End-Standardhost verweist.
 
Navigieren Sie in Ihrem Browser mithilfe der benutzerdefinierten Domäne zur Adresse der Datei. Wenn Ihre benutzerdefinierte Domäne beispielsweise „robotics.contoso.com“ ist, muss die URL zur zwischengespeicherten Datei in etwa wie folgt lauten: „http:\//robotics.contoso.com/my-public-container/my-file.jpg“. Vergewissern Sie sich, dass das Ergebnis mit dem Ergebnis beim direkten Zugriff auf den Azure Front Door Service-Front-End-Standardhost unter „ *&lt;Azure Front Door Service-Host&gt;* .azurefd.net“ identisch ist.


## <a name="map-the-permanent-custom-domain"></a>Zuordnen der permanenten benutzerdefinierten Domäne

Wenn Sie sichergestellt haben, dass die Unterdomäne „afdverify“ Ihrer Azure Front Door Service-Konfiguration erfolgreich zugeordnet wurde (oder wenn Sie eine neue benutzerdefinierte Domäne verwenden, die nicht für die Produktion bestimmt ist), können Sie die benutzerdefinierte Domäne dem Azure Front Door Service-Front-End-Standardhost direkt zuordnen.

Erstellen Sie wie folgt einen CNAME-Eintrag für Ihre benutzerdefinierten Domäne:

1. Melden Sie sich an der Website des Domänenanbieters für Ihre benutzerdefinierte Domäne an.

2. Suchen Sie die Seite für die Verwaltung von DNS-Einträgen mithilfe der Dokumentation des Anbieters, oder suchen Sie nach Bereichen der Website mit der Bezeichnung **Domänennamen**, **DNS**, oder **Namensserververwaltung**. 

3. Erstellen Sie einen CNAME-Eintrag für Ihre benutzerdefinierte Domäne, und füllen Sie die Felder wie in der folgenden Tabelle gezeigt aus (Feldnamen können variieren):

    | `Source`          | type  | Destination           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso-frontend.azurefd.net |

   - Quelle: Geben Sie den Namen Ihrer benutzerdefinierten Domäne ein (z. B. „www\.contoso.com“).

   - Typ: Geben Sie *CNAME* ein.

   - Ziel: Geben Sie Ihren Azure Front Door Service-Front-End-Standardhost ein. Der Host muss im folgenden Format angegeben werden: _&lt;hostname&gt;_ .azurefd.net. Beispiel: contoso-frontend.azurefd.net.

4. Speichern Sie die Änderungen.

5. Wenn Sie zuvor einen temporären CNAME-Eintrag für die Unterdomäne „afdverify“ erstellt haben, löschen Sie diesen Eintrag. 

6. Falls Sie diese benutzerdefinierte Domäne erstmals in der Produktion verwenden, führen Sie die Schritte unter [Associate the custom domain with your Front Door](#associate-the-custom-domain-with-your-front-door) (Zuordnen der benutzerdefinierten Domäne zu Ihrer Azure Front Door Service-Konfiguration) und [Verify the custom domain](#verify-the-custom-domain) (Überprüfen der benutzerdefinierten Domäne) aus.

Für die Domänenregistrierungsstelle GoDaddy gilt beispielsweise folgende Vorgehensweise:

1. Melden Sie sich an, und wählen Sie die gewünschte benutzerdefinierte Domäne aus.

2. Wählen Sie im Abschnitt „Domänen“ die Option **Manage All** (Alle verwalten) und dann **DNS** | **Manage Zones** (Zonen verwalten).

3. Geben Sie unter **Domänenname** Ihre benutzerdefinierte Domäne ein, und wählen Sie anschließend **Auswählen**.

4. Wählen Sie auf der Seite **DNS-Verwaltung** die Option **Hinzufügen** und dann in der Liste **Typ** den Eintrag **CNAME**.

5. Füllen Sie die Felder für den CNAME-Eintrag aus:

    - Typ: Übernehmen Sie *CNAME*.

    - Host: Geben Sie die Unterdomäne der gewünschten benutzerdefinierten Domäne ein. Beispiel: „www“ oder „profile“.

    - Points to (Verweist auf): Geben Sie den Namen des Standardhosts Ihrer Azure Front Door Service-Konfiguration ein. Beispiel: contoso.azurefd.net. 

    - Gültigkeitsdauer: Lassen Sie die Auswahl von *Eine Stunde* unverändert.

6. Wählen Sie **Speichern** aus.
 
    Der CNAME-Eintrag wird der Tabelle mit den DNS-Einträgen hinzugefügt.

7. Wenn Sie über einen CNAME-Eintrag für „afdverify“ verfügen, klicken Sie auf das Stiftsymbol neben dem Eintrag und dann auf das Papierkorbsymbol.

8. Wählen Sie **Löschen**, um den CNAME-Eintrag zu löschen.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den obigen Schritten haben Sie Ihrer Azure Front Door Service-Konfiguration eine benutzerdefinierte Domäne hinzugefügt. Wenn Sie die Zuordnung Ihrer Azure Front Door Service-Konfiguration zu einer benutzerdefinierten Domäne aufheben möchten, können Sie die benutzerdefinierte Domäne wie folgt entfernen:
 
1. Wählen Sie im Frontdoor-Designer die benutzerdefinierte Domäne aus, die Sie entfernen möchten.

2. Klicken Sie im Kontextmenü für die benutzerdefinierte Domäne auf „Löschen“.  

   Die Zuordnung der benutzerdefinierten Domäne zum Endpunkt wird aufgehoben.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

* Erstellen eines CNAME-DNS-Eintrags
* Zuordnen der benutzerdefinierten Domäne zu Ihrer Azure Front Door Service-Konfiguration
* Überprüfen der benutzerdefinierten Domäne

Im nächsten Tutorial erfahren Sie, wie Sie HTTPS für Ihre benutzerdefinierte Domäne aktivieren:

> [!div class="nextstepaction"]
> [Aktivieren von HTTPS für eine benutzerdefinierte Domäne](front-door-custom-domain-https.md)