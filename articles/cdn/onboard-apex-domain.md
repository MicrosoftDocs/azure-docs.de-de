---
title: Onboarding einer Stamm- oder Apex-Domäne an einem vorhandenen Azure CDN-Endpunkt – Azure-Portal
description: Erfahren Sie, wie Sie das Onboarding einer Stamm- oder Apex-Domäne an einem vorhandenen Azure CDN-Endpunkt über das Azure-Portal durchführen.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369836"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Onboarding einer Stamm- oder Apex-Domäne an einem vorhandenen Azure CDN-Endpunkt

Azure CDN verwendet CNAME-Einträge zum Überprüfen des Domänenbesitzes beim Onboarding von benutzerdefinierten Domänen. In CDN wird die dem CDN-Profil zugeordnete Front-End-IP-Adresse nicht verfügbar gemacht. Sie können Ihre Apex-Domäne nicht einer IP-Adresse zuordnen, wenn Sie ein Onboarding der Domäne in Azure CDN durchführen möchten.

Das DNS-Protokoll verhindert die Zuweisung von CNAME-Einträgen am Zonen-Apex. Wenn die Domäne beispielsweise `contoso.com` heißt, können Sie CNAME-Einträge für `somelabel.contoso.com`, aber keinen CNAME-Eintrag für `contoso.com` selbst erstellen. Diese Einschränkung stellt Anwendungsbesitzer, die über Anwendungen mit Lastenausgleich hinter Azure CDN verfügen, vor ein Problem. Da für die Verwendung eines CDN-Profils die Erstellung eines CNAME-Eintrags erforderlich ist, kann im Zonen-Apex-Eintrag nicht auf das CDN-Profil verwiesen werden.

Dieses Problem kann mithilfe von Aliaseinträgen in Azure DNS gelöst werden. Anders als CNAME-Einträge werden Aliaseinträge im Zonen-Apex erstellt. Anwendungsbesitzer können diese verwenden, um ihren Zonen-Apex-Eintrag auf ein CDN-Profil zu verweisen, das über öffentliche Endpunkte verfügt. Anwendungsbesitzer verweisen auf das gleiche CDN-Profil, das auch für andere Domänen innerhalb ihrer DNS-Zone verwendet wird. Beispielsweise können `contoso.com` und `www.contoso.com` beide auf dasselbe CDN-Profil verweisen. 

Wenn Sie Ihre Apex- oder Stammdomäne dem CDN-Profil zuordnen, ist CNAME-Vereinfachung oder DNS-Verfolgung erforderlich. Dies ist ein Mechanismus, bei dem der DNS-Anbieter den CNAME-Eintrag rekursiv auflöst, bis er auf eine IP-Adresse trifft. Diese Funktion wird in Azure DNS für CDN-Endpunkte unterstützt. 

> [!NOTE]
> Es gibt außerdem weitere DNS-Anbieter, die das Vereinfachen von CNAME oder das Nachverfolgen von DNS unterstützen. Allerdings wird Azure CDN-Kunden die Verwendung von Azure DNS für das Hosten von Domänen empfohlen.

Sie können das Azure-Portal verwenden, um ein Onboarding für eine Apex-Domäne in Ihrer CDN-Instanz durchzuführen und HTTPS für sie zu aktivieren, indem Sie sie einem Zertifikat für den TLS-Abschluss zuordnen. Apex-Domänen werden auch als Stammdomänen oder Naked Domains bezeichnet.

## <a name="create-an-alias-record-for-zone-apex"></a>Erstellen eines Aliaseintrags für einen Zonen-Apex

1. Öffnen Sie die Konfiguration **Azure DNS**, damit die Domäne integriert werden kann.

2. Klicken Sie auf **+ Datensatzgruppe**.

3. Geben Sie unter **Datensatzgruppe hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ------|
    | Name | Geben Sie **@** ein. |
    | type | Wählen Sie **A** aus. |
    | Alias-Ressourceneintragssatz | Wählen Sie **Ja** aus. |
    | Aliastyp | Wählen Sie **Azure-Ressource** aus. |
    | Wählen Sie ein Abonnement. | Wählen Sie Ihr Abonnement aus. |
    | Azure-Ressource | Wählen Sie Ihren CDN-Endpunkt aus. |

4. Geben Sie Ihren Wert für **TTL** ein.

5. Klicken Sie auf **OK**, um die Änderungen zu übermitteln.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Erstellen eines Aliaseintrags für einen Zonen-Apex":::

6. Mit dem Schritt oben wird ein Zonen-Apex-Eintrag erstellt, der auf Ihre CDN-Ressource verweist. Die Zuordnung **cdnverify** im CNAME-Eintrag wird für das Onboarding der Domäne in Ihrem CDN-Profil verwendet,
    1. z. B. **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>Onboarding der benutzerdefinierten Domäne in Ihrer CDN-Instanz

Nachdem Sie Ihre benutzerdefinierte Domäne registriert haben, können Sie sie dem CDN-Endpunkt hinzufügen. 

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu dem CDN-Profil mit dem Endpunkt, den Sie einer benutzerdefinierten Domäne zuordnen möchten.
    
2. Wählen Sie auf der Seite **CDN-Profil** den CDN-Endpunkt aus, der der benutzerdefinierten Domäne zugeordnet werden soll.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="CDN-Endpunktauswahl" border="true":::
    
3. Wählen Sie **+ Benutzerdefinierte Domäne** aus. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Schaltfläche zum Hinzufügen einer benutzerdefinierten Domäne" border="true":::

4. Unter **Benutzerdefinierte Domäne hinzufügen** ist der Wert für **Endpunkthostname** bereits ausgefüllt. Er wird von der URL Ihres CDN-Endpunkts abgeleitet: **\<endpoint-hostname>** .azureedge.net. Diese Einstellung kann nicht geändert werden.

5. Geben Sie unter **Benutzerdefinierter Hostname** Ihre benutzerdefinierte Stamm- oder Apex-Domäne ein, die als Quelldomäne des CNAME-Eintrags verwendet werden soll. 
    1. Beispiel: **contoso.com**. **Verwenden Sie nicht den Namen der Unterdomäne „cdnverify“.**

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Hinzufügen einer benutzerdefinierten Domäne" border="true":::

6. Wählen Sie **Hinzufügen**.

   Azure überprüft, ob der CNAME-Eintrag für den eingegebenen Namen der benutzerdefinierten Domäne vorhanden ist. Wenn der CNAME-Eintrag korrekt ist, wird Ihre benutzerdefinierte Domäne überprüft. 

   Es kann einige Zeit dauern, bis die neuen Einstellungen der benutzerdefinierten Domäne für alle CDN-Edgeknoten übernommen werden: 
    - Bei Profilen vom Typ **Azure CDN Standard von Microsoft** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen. 
    - Bei **Azure CDN Standard von Akamai**-Profilen ist die Weitergabe in der Regel in einer Minute abgeschlossen. 
    - Bei Profilen vom Typ **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen.   

## <a name="enable-https-on-your-custom-domain"></a>Aktivieren von HTTPS für eine benutzerdefinierte Domäne

Weitere Informationen zum Aktivieren von HTTPS in Ihrer benutzerdefinierten Domäne für Azure CDN finden Sie im [Tutorial: Konfigurieren von HTTPS in einer benutzerdefinierten Azure CDN-Domäne](cdn-custom-ssl.md)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum [Erstellen eines neuen CDN-Endpunkts](cdn-create-new-endpoint.md)
