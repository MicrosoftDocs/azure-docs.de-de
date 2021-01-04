---
title: 'Tutorial: Hinzufügen einer benutzerdefinierten Domäne zu Ihrem Endpunkt'
titleSuffix: Azure Content Delivery Network
description: In diesem Tutorial erfahren Sie, wie Sie eine benutzerdefinierte Domäne zu einem Azure Content Delivery Network-Endpunkt hinzufügen, damit der Domänenname in Ihrer URL sichtbar ist.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 11/06/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 03ed47ee97f52aca708118f202fad583753549bf
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331203"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>Tutorial: Hinzufügen einer benutzerdefinierten Domäne zu Ihrem Endpunkt

In diesem Tutorial wird veranschaulicht, wie Sie einem Azure CDN-Endpunkt (Content Delivery Network) eine benutzerdefinierte Domäne hinzufügen. 

Der Endpunktname in Ihrem CDN-Profil ist eine Unterdomäne von „azureedge.net“. Bei der Inhaltsbereitstellung ist die CDN-Profildomäne standardmäßig in der URL enthalten.

Beispiel: **https://contoso.azureedge.net/photo.png**

Azure CDN bietet die Option, eine benutzerdefinierte Domäne einem CDN-Endpunkt zuzuordnen. Durch diese Option wird Inhalt mit einer benutzerdefinierten Domäne in Ihrer URL (anstelle des standardmäßigen Endpunktnamens) bereitgestellt.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> - Erstellen eines CNAME-DNS-Eintrags
> - Zuordnen der benutzerdefinierten Domäne zu Ihrem CDN-Endpunkt
> - Überprüfen der benutzerdefinierten Domäne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie ein CDN-Profil und mindestens einen CDN-Endpunkt, bevor Sie die Schritte in diesem Tutorial ausführen. 
    * Weitere Informationen finden Sie unter [Quickstart: Erstellen eines Azure CDN-Profils und -Endpunkts](cdn-create-new-endpoint.md).

* Sollten Sie über keine benutzerdefinierte Domäne verfügen, erwerben Sie eine solche Domäne bei einem Domänenanbieter. 
    * Weitere Informationen finden Sie unter [Kaufen eines benutzerdefinierten Domänennamens für Azure App Service](../app-service/manage-custom-dns-buy-domain.md).

* Wenn Sie Azure zum Hosten Ihrer [DNS-Domänen](../dns/dns-overview.md) verwenden, delegieren Sie Ihre benutzerdefinierte Domäne an Azure DNS. 
    * Weitere Informationen finden Sie unter [Delegieren einer Domäne an Azure DNS](../dns/dns-delegate-domain-azure-dns.md).

* Wenn Sie für Ihre DNS-Domäne einen Domänenanbieter verwenden, fahren Sie mit [Erstellen eines CNAME-DNS-Eintrags](#create-a-cname-dns-record) fort.


## <a name="create-a-cname-dns-record"></a>Erstellen eines CNAME-DNS-Eintrags

Bevor Sie eine benutzerdefinierte Domäne mit einem Azure CDN-Endpunkt verwenden können, müssen Sie zunächst einen CNAME-Eintrag (Canonical Name, kanonischer Name) bei Azure DNS oder bei Ihrem DNS-Anbieter erstellen, um auf Ihren CDN-Endpunkt zu verweisen. 

Ein CNAME-Eintrag ist ein DNS-Eintrag, der einen Quelldomänennamen einem Zieldomänennamen zuordnet. 

Für Azure CDN ist der Quelldomänenname Ihr benutzerdefinierter Domänenname, und der Zieldomänenname entspricht dem Hostnamen Ihres CDN-Endpunkts. 

Für die benutzerdefinierte Quelldomäne bestimmter Datenverkehr wird von Azure CDN nach Überprüfung des CNAME-Eintrags an den Hostnamen des CDN-Zielendpunkts geleitet.

Eine benutzerdefinierte Domäne und die zugehörige Unterdomäne können immer nur einem einzelnen Endpunkt zugeordnet werden. 

Verwenden Sie für unterschiedliche Azure-Dienste mehrere CNAME-Einträge für unterschiedliche Unterdomänen der gleichen benutzerdefinierten Domäne.

Eine benutzerdefinierte Domäne mit unterschiedlichen Unterdomänen kann dem gleichen CDN-Endpunkt zugeordnet werden.

> [!NOTE]
> In diesem Tutorial wird der CNAME-Eintragstyp verwendet. Wenn Sie die Eintragstypen A oder AAAA verwenden, führen Sie die gleichen unten gezeigten Schritte aus, und ersetzen Sie CNAME durch den Eintragstyp Ihrer Wahl.

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

Für Azure-Ressourcen innerhalb des gleichen Abonnements werden von Azure DNS Aliaseinträge verwendet.

So fügen Sie einen Aliaseintrag für Ihren Azure CDN-Endpunkt hinzu:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite die Option **Alle Ressourcen** und anschließend die Azure DNS-Zone für Ihre benutzerdefinierte Domäne aus.

3. Wählen Sie in der DNS-Zone für Ihre benutzerdefinierte Domäne die Option **+ Ressourceneintragssatz** aus.

4. Geben Sie unter **Datensatzgruppe hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name  | Geben Sie den Alias ein, den Sie für Ihren CDN-Endpunkt verwenden möchten. Beispiel: **www**. |
    | type  | Wählen Sie **CNAME** aus. |
    | Alias-Ressourceneintragssatz | Wählen Sie **Ja** aus. |
    | Aliastyp | Wählen Sie **Azure-Ressource** aus. |
    | Wählen Sie ein Abonnement. | Wählen Sie Ihr Abonnement aus. |
    | Azure-Ressource | Wählen Sie Ihren Azure CDN-Endpunkt aus. |

5. Legen Sie die Gültigkeitsdauer (**TTL**) für den Eintrag auf Ihren Wert fest.

6. Klicken Sie auf **OK**.

# <a name="dns-provider"></a>[**DNS-Anbieter**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Zuordnen der temporären Unterdomäne „cdnverify“

Es müssen bestimmte Aspekte berücksichtigt werden, wenn Sie eine vorhandene Domäne zuordnen, die sich in der Produktion befindet. 

Wenn Sie Ihre benutzerdefinierte Domäne im Azure-Portal registrieren, kann es für die Domäne zu einer kurzen Downtime kommen.

Um eine Unterbrechung des Webdatenverkehrs zu vermeiden, ordnen Sie Ihre benutzerdefinierte Domäne dem Hostnamen Ihres CDN-Endpunkts mit der Azure-Unterdomäne **cdnverify** zu. Dadurch wird eine temporäre CNAME-Zuordnung erstellt. 

Mit dieser Methode können Benutzer ohne Unterbrechung auf Ihre Domäne zugreifen, während die DNS-Zuordnung durchgeführt wird. 

Falls Produktionsausfälle für Sie nicht relevant sind, können Sie Ihre benutzerdefinierte Domäne direkt Ihrem CDN-Endpunkt zuordnen. Fahren Sie mit [Zuordnen der permanenten benutzerdefinierten Domäne](#map-the-permanent-custom-domain) fort.

Erstellen Sie wie folgt einen CNAME-Eintrag mit der Unterdomäne „cdnverify“:

1. Melden Sie sich bei der Website des DNS-Anbieters für Ihre benutzerdefinierte Domäne an.

2. Erstellen Sie einen CNAME-Eintrag für Ihre benutzerdefinierte Domäne, und füllen Sie die Felder wie in der folgenden Tabelle gezeigt aus (Feldnamen können variieren):

    | `Source`                    | type  | Destination                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify. www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Quelle: Geben Sie den Namen Ihrer benutzerdefinierten Domäne (einschließlich der Unterdomäne „cdnverify“) im folgenden Format ein: **cdnverify.\<custom-domain-name>**
        - Beispiel: **cdnverify. www.contoso.com**

    - Typ: Geben Sie **CNAME** ein, oder wählen Sie die entsprechende Option aus.

    - Ziel: Geben Sie den Hostnamen Ihres CDN-Endpunkts (einschließlich der Unterdomäne „cdnverify“) im folgenden Format ein: **cdnverify.\<endpoint-name>.azureedge.net**. 
        - Beispiel: **cdnverify.contoso.azureedge.net**

3. Speichern Sie die Änderungen.

## <a name="map-the-permanent-custom-domain"></a>Zuordnen der permanenten benutzerdefinierten Domäne

In diesem Abschnitt wird die permanente benutzerdefinierte Domäne dem CDN-Endpunkt zugeordnet. 

Erstellen Sie wie folgt einen CNAME-Eintrag für Ihre benutzerdefinierten Domäne:

1. Melden Sie sich an der Website des Domänenanbieters für Ihre benutzerdefinierte Domäne an.

2. Erstellen Sie einen CNAME-Eintrag für Ihre benutzerdefinierte Domäne, und füllen Sie die Felder wie in der folgenden Tabelle gezeigt aus (Feldnamen können variieren):

    | `Source`          | type  | Destination           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Quelle: Geben Sie den Namen Ihrer benutzerdefinierten Domäne ein.
        - Beispiel: **www.contoso.com**

    - Typ: Geben Sie **CNAME** ein, oder wählen Sie die entsprechende Option aus.

    - Ziel: Geben Sie den Hostnamen Ihres CDN-Endpunkts im folgenden Format ein: **\<endpoint-name>.azureedge.net**. 
        - Beispiel: **contoso.azureedge.net**

3. Speichern Sie die Änderungen.

4. Wenn Sie zuvor einen temporären CNAME-Eintrag für die Unterdomäne „cdnverify“ erstellt haben, sollten Sie ihn löschen. 

5. Falls Sie diese benutzerdefinierte Domäne zum ersten Mal in der Produktion verwenden, können Sie die Schritte unter [Zuordnen der benutzerdefinierten Domäne zu Ihrem CDN-Endpunkt](#associate-the-custom-domain-with-your-cdn-endpoint) und [Überprüfen der benutzerdefinierten Domäne](#verify-the-custom-domain) ausführen.

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Zuordnen der benutzerdefinierten Domäne zu Ihrem CDN-Endpunkt

Nachdem Sie Ihre benutzerdefinierte Domäne registriert haben, können Sie sie dem CDN-Endpunkt hinzufügen. 

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu dem CDN-Profil mit dem Endpunkt, den Sie einer benutzerdefinierten Domäne zuordnen möchten.
    
2. Wählen Sie auf der Seite **CDN-Profil** den CDN-Endpunkt aus, der der benutzerdefinierten Domäne zugeordnet werden soll.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="CDN-Endpunktauswahl" border="true":::
    
3. Wählen Sie **+ Benutzerdefinierte Domäne** aus. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="Schaltfläche zum Hinzufügen einer benutzerdefinierten Domäne" border="true":::

4. Unter **Benutzerdefinierte Domäne hinzufügen** ist der Wert für **Endpunkthostname** bereits ausgefüllt. Er wird von der URL Ihres CDN-Endpunkts abgeleitet: **\<endpoint-hostname>** .azureedge.net. Diese Einstellung kann nicht geändert werden.

5. Geben Sie unter **Benutzerdefinierter Hostname** Ihre benutzerdefinierte Domäne (einschließlich Unterdomäne) ein, die als Quelldomäne Ihres CNAME-Eintrags verwendet werden soll. 
    1. Beispiel: **www.contoso.com** oder **cdn.contoso.com**. **Verwenden Sie nicht den Namen der Unterdomäne „cdnverify“.**

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="Hinzufügen einer benutzerdefinierten Domäne" border="true":::

6. Wählen Sie **Hinzufügen**.

   Azure überprüft, ob der CNAME-Eintrag für den eingegebenen Namen der benutzerdefinierten Domäne vorhanden ist. Wenn der CNAME-Eintrag korrekt ist, wird Ihre benutzerdefinierte Domäne überprüft. 

   Es kann einige Zeit dauern, bis die neuen Einstellungen der benutzerdefinierten Domäne für alle CDN-Edgeknoten übernommen werden: 
    - Bei Profilen vom Typ **Azure CDN Standard von Microsoft** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen. 
    - Bei **Azure CDN Standard von Akamai**-Profilen ist die Weitergabe in der Regel in einer Minute abgeschlossen. 
    - Bei Profilen vom Typ **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen.   


## <a name="verify-the-custom-domain"></a>Überprüfen der benutzerdefinierten Domäne

Vergewissern Sie sich nach Abschluss der Registrierung der benutzerdefinierten Domäne, dass sie auf Ihren CDN-Endpunkt verweist.
 
1. Stellen Sie sicher, dass öffentliche Inhalte zum Zwischenspeichern auf dem Endpunkt verfügbar sind. Wenn der CDN-Endpunkt beispielsweise einem Speicherkonto zugeordnet ist, wird der Inhalt vom Azure CDN in einem öffentlichen Container zwischengespeichert. Konfigurieren Sie Ihren Container so, dass er öffentlichen Zugriff zulässt und mindestens eine Datei enthält, um die benutzerdefinierte Domäne zu testen.

2. Navigieren Sie in Ihrem Browser mithilfe der benutzerdefinierten Domäne zur Adresse der Datei. Wenn Ihre benutzerdefinierte Domäne beispielsweise `www.contoso.com` ist, sollte die URL für die zwischengespeicherte Datei der folgenden URL ähneln: `http://www.contoso.com/my-public-container/my-file.jpg`. Vergewissern Sie sich, dass das Ergebnis dem Ergebnis des direkten Zugriffs auf den CDN-Endpunkt unter „ **\<endpoint-hostname>** .azureedge.net“ entspricht.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Zuordnung Ihres Endpunkts zu einer benutzerdefinierten Domäne aufheben möchten, entfernen Sie die benutzerdefinierte Domäne:
 
1. Wählen Sie in Ihrem CDN-Profil den Endpunkt mit der benutzerdefinierten Domäne aus, die Sie entfernen möchten.

2. Klicken Sie auf der Seite **Endpunkt** unter „Benutzerdefinierte Domänen“ mit der rechten Maustaste auf die benutzerdefinierte Domäne, die Sie entfernen möchten, und wählen Sie im Kontextmenü dann die Option **Löschen**. Wählen Sie **Ja** aus.

   Die Zuordnung der benutzerdefinierten Domäne zum Endpunkt wird aufgehoben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> - Erstellen eines CNAME-DNS-Eintrags
> - Zuordnen der benutzerdefinierten Domäne zu Ihrem CDN-Endpunkt
> - Überprüfen der benutzerdefinierten Domäne

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie HTTPS für eine benutzerdefinierte Azure CDN-Domäne konfigurieren.

> [!div class="nextstepaction"]
> [Tutorial: Konfigurieren von HTTPS in einer benutzerdefinierten Azure CDN-Domäne](cdn-custom-ssl.md)