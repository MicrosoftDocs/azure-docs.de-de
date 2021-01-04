---
title: Integrieren einer Stamm- oder Apex-Domäne in eine vorhandene Front Door-Instanz – Azure-Portal
description: Informationen zum Integrieren einer Stamm- oder Apex-Domäne in eine Front Door-Instanz mithilfe des Azure-Portals
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/13/2020
ms.author: duau
ms.openlocfilehash: 55eefe7a7490df050aa7ebc2bb41fbadcc8d8279
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646337"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Integrieren einer Stamm- oder Apex-Domäne in Ihre Front Door-Instanz
Azure Front Door verwendet CNAME-Einträge zum Überprüfen des Domänenbesitzes beim Integrieren von benutzerdefinierten Domänen. Front Door legt die dem Front Door-Profil zugeordnete Front-End-IP-Adresse nicht offen. Daher können Sie Ihre Apex-Domäne nicht einer IP-Adresse zuordnen, wenn Sie beabsichtigen, ein Onboarding in Azure Front Door durchzuführen.

Das DNS-Protokoll verhindert die Zuweisung von CNAME-Einträgen am Zonen-Apex. Wenn die Domäne beispielsweise `contoso.com` heißt, können Sie CNAME-Einträge für `somelabel.contoso.com`, aber keinen CNAME-Eintrag für `contoso.com` selbst erstellen. Diese Einschränkung stellt Anwendungsbesitzer, die über Anwendungen mit Lastenausgleich über Azure Front Door verfügen, vor ein Problem. Da für die Verwendung eines Front Door-Profils die Erstellung eines CNAME-Eintrags erforderlich ist, kann vom Zonen-Apex aus nicht auf das Front Door-Profil verwiesen werden.

Dieses Problem kann mithilfe von Aliaseinträgen in Azure DNS gelöst werden. Anders als CNAME-Einträge werden Aliaseinträge im Zonen-Apex erstellt. Anwendungsbesitzer können diese verwenden, um ihren Zonen-Apex-Eintrag auf ein Front Door-Profil verweisen, das über öffentliche Endpunkte verfügt. Anwendungsbesitzer verweisen auf das gleiche Front Door-Profil, das auch für andere Domänen innerhalb ihrer DNS-Zone verwendet wird. Beispielsweise können `contoso.com` und `www.contoso.com` beide auf dasselbe Front Door-Profil verweisen. 

Wenn Sie Ihre Apex- oder Stammdomäne dem Front Door-Profil zuordnen, ist CNAME-Vereinfachung oder DNS-Verfolgung erforderlich. Dies ist ein Mechanismus, bei dem der DNS-Anbieter den CNAME-Eintrag rekursiv auflöst, bis er auf eine IP-Adresse trifft. Diese Funktion wird von Azure DNS für Front Door-Endpunkte unterstützt. 

> [!NOTE]
> Es gibt außerdem weitere DNS-Anbieter, die das Vereinfachen von CNAME oder das Nachverfolgen von DNS unterstützen. Allerdings wird den Azure Front Door-Kunden die Verwendung von Azure DNS für das Hosten von Domänen empfohlen.

Sie können das Azure-Portal verwenden, um ein Onboarding für eine Apex-Domäne in Ihrer Front Door-Instanz durchzuführen und HTTPS für sie zu aktivieren, indem Sie sie einem Zertifikat für die TLS-Terminierung zuordnen. Apex-Domänen werden auch als Stammdomänen oder Naked Domains bezeichnet.

## <a name="create-an-alias-record-for-zone-apex"></a>Erstellen eines Aliaseintrags für einen Zonen-Apex

1. Öffnen Sie die Konfiguration **Azure DNS**, damit die Domäne integriert werden kann.

1. Erstellen oder bearbeiten Sie den Eintrag für den Zonen-Apex.

1. Wählen Sie den Eintrag **Typ** als Eintrag *A* aus, und klicken Sie anschließend für **Alias record set** (Aliasdatensatz) auf *Ja*. Als **Aliastyp** sollte *Azure-Ressource* festgelegt sein.

1. Wählen Sie das Azure-Abonnement aus, in dem Ihr Front Door-Profil gehostet wird. Wählen Sie dann in der Dropdownliste **Azure-Ressource** die Front Door-Ressource aus.

1. Klicken Sie auf **OK**, um die Änderungen zu übermitteln.

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="Erstellen eines Aliaseintrags für einen Zonen-Apex":::

1. Wenn Sie den oben beschriebenen Schritt ausführen, werden ein Eintrag für den Zonen-Apex, der auf Ihre Front Door-Ressource zeigt, sowie ein CNAME-Eintrag erstellt, der „afdverify“ (z. B. `afdverify.contosonews.com`) zuordnet. Dies wird zum Onboarding der Domäne in Ihr Front Door-Profil verwendet.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Integrieren der benutzerdefinierten Domäne in Ihre Front Door-Instanz

1. Klicken Sie in Front Door auf der Registerkarte „Designer“ im Abschnitt „Front-End-Hosts“ auf das Symbol „+“, um eine neue benutzerdefinierte Domäne hinzuzufügen.

1. Geben Sie in das Feld „Benutzerdefinierter Hostname“ den Namen der Stamm- oder Apex-Domäne ein, z. B. `contosonews.com`.

1. Nachdem die CNAME-Zuordnung von der Domäne zu Ihrer Front Door-Instanz überprüft wurde, klicken Sie auf **Hinzufügen**, um die benutzerdefinierte Domäne hinzuzufügen.

1. Klicken Sie auf **Speichern**, um die Änderungen zu übermitteln.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="Menü „Benutzerdefinierte Domänen“":::

## <a name="enable-https-on-your-custom-domain"></a>Aktivieren von HTTPS für eine benutzerdefinierte Domäne

1. Klicken Sie auf die benutzerdefinierte Domäne, die hinzugefügt wurde, und ändern Sie den Status unter dem Abschnitt **HTTPS für benutzerdefinierte Domänen** in **Aktiviert**.

1. Wählen Sie unter **Zertifikatverwaltungstyp** die Option *Eigenes Zertifikat verwenden* aus.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="HTTPS-Einstellungen benutzerdefinierter Domänen":::    

   > [!WARNING]
   > Der Zertifikatverwaltungstyp „Front Door managed“ (Mit Front Door verwaltet) wird derzeit nicht für Apex- oder Stammdomänen unterstützt. Sie können nur Ihr eigenes benutzerdefiniertes TLS/SSL-Zertifikat verwenden, das in Azure Key Vault gehostet wird, um HTTPS auf einer Apex- oder Stammdomäne für Front Door zu aktivieren.

1. Vergewissern Sie sich, dass Sie die richtigen Berechtigungen für Front Door eingerichtet haben, um wie auf der Benutzeroberfläche angegeben auf Ihren Schlüsseltresor zugreifen zu können, bevor Sie mit dem nächsten Schritt fortfahren.

1. Wählen Sie ein **Key Vault-Konto** aus, das Ihrem aktuellen Abonnement zugeordnet ist, und wählen Sie anschließend das entsprechende **Geheimnis** einschließlich der jeweiligen **Geheimnisversion** aus, um das Konto dem richtigen Zertifikat zuzuordnen.

1. Klicken Sie auf **Aktualisieren**, um die Auswahl zu speichern, und klicken Sie dann auf **Speichern**.

1. Klicken Sie nach einigen Minuten auf **Aktualisieren**, und klicken Sie dann erneut auf die benutzerdefinierte Domäne, um den Status der Zertifikatbereitstellung anzuzeigen. 

> [!WARNING]
> Vergewissern Sie sich, dass Sie angemessene Routingregeln für Ihre Apex-Domäne erstellt oder die Domäne bereits vorhandenen Routingregeln zugeordnet haben.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
