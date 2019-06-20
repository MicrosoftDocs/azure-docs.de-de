---
title: Schattenattribute für den Azure AD Connect-Synchronisierungsdienst | Microsoft-Dokumentation
description: Beschreibt die Funktionsweise von Schattenattributen im Azure AD Connect-Synchronisierungsdienst.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60384703"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Schattenattribute für den Azure AD Connect-Synchronisierungsdienst
Die meisten Attribute werden in Azure AD genauso wie in Ihrem lokalen Azure Directory dargestellt. Einige Attribute werden jedoch besonders verarbeitet, weshalb sich der Attributwert in Azure AD davon unterscheiden kann, was mit Azure AD Connect synchronisiert wird.

## <a name="introducing-shadow-attributes"></a>Was sind Schattenattribute?
Für einige Attribute gibt es in Azure AD zwei Darstellungen. Sowohl der lokale Wert als auch ein berechneter Wert werden gespeichert. Diese zusätzlichen Attribute werden Schattenattribute genannt. Die beiden gängigsten Attribute, bei denen dieses Verhalten erkennbar ist, heißen **userPrincipalName** und **proxyAddress**. Attributwerte werden geändert, wenn Werte in diesen Attributen vorhanden sind, die nicht bestätigte Domänen darstellen. Doch das Synchronisierungsmodul in Azure AD Connect liest den Wert im Schattenattribut aus seiner Perspektive dergestalt, dass Attribut von Azure AD bestätigt wurde.

Die Schattenattribute können nicht im Azure-Portal oder mit PowerShell angezeigt werden. Doch das Verstehen des Konzepts hilft, Probleme in bestimmten Szenarien zu beheben, in denen das Attribut lokal und in der Cloud unterschiedliche Werte aufweist.

Um das Verhalten besser zu verstehen, betrachten Sie dieses Beispiel für Fabrikam:  
![Domänen](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Das Unternehmen hat mehrere UPN-Suffixe in seinem lokalen Active Directory, von denen aber nur eines bestätigt ist.

### <a name="userprincipalname"></a>userPrincipalName
Ein Benutzer hat die folgenden Attributwerte in einer nicht bestätigten Domäne:

| Attribut | Wert |
| --- | --- |
| „userPrincipalName“ lokal | lee.sperry@fabrikam.com |
| „shadowUserPrincipalName“ in Azure AD | lee.sperry@fabrikam.com |
| „userPrincipalName“ in Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

Das Attribut „userPrincipalName“ ist der Wert, den Sie bei Verwenden von PowerShell sehen.

Da der tatsächliche lokale Attributwert in Azure AD gespeichert ist, aktualisiert Azure AD beim Bestätigen der Domäne „fabrikam.com“ das Attribut „userPrincipalName“ mit dem Wert von „shadowUserPrincipalName“. Sie müssen keine aus Azure AD Connect stammenden Änderungen synchronisieren, damit diese Werte aktualisiert werden.

### <a name="proxyaddresses"></a>proxyAddresses
Dasselbe Verfahren für ausschließlich bestätigte Domänen erfolgt auch für „proxyAddresses“, jedoch mit zusätzlicher Logik. Die Suche nach bestätigten Domänen erfolgt nur für Postfachbenutzer. Ein E-Mail-aktivierter Benutzer oder Kontakt stellt einen Benutzer in einer anderen Exchange-Organisation dar. In „proxyAddresses“ können Sie diesen Objekten beliebige Werte hinzufügen.

Für einen Postfachbenutzer, ob lokal oder in Exchange Online, werden nur Werte für bestätigte Domänen angezeigt. Diese können wie folgt aussehen:

| Attribut | Wert |
| --- | --- |
| „proxyAddresses“ lokal | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| „proxyAddresses“ in Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

In diesem Fall wurde **smtp:abbie.spencer\@fabrikam.com** entfernt, da diese Domäne nicht bestätigt wurde. Doch Exchange hat auch **SIP:abbie.spencer\@fabrikamonline.com** hinzugefügt. Fabrikam hat Lync/Skype nicht lokal genutzt, doch Azure AD und Exchange Online bereiten sich darauf vor.

Diese Logik für „proxyAddresses“ wird als **ProxyCalc** bezeichnet. „ProxyCalc“ wird bei jeder Änderung für einen Benutzer aufgerufen, wenn Folgendes gilt:

- Dem Benutzer wurde ein Serviceplan zugewiesen, der Exchange Online enthält, auch wenn der Benutzer nicht für Exchange lizenziert wurde. Beispiel: Dem Benutzer wurde die SKU Office E3 zugewiesen, doch ihm wurde nur SharePoint Online zugewiesen. Dies gilt auch, wenn Ihr Postfach weiterhin lokal ist.
- Das Attribut „msExchRecipientTypeDetails“ hat einen Wert.
- Sie nehmen eine Änderung an „proxyAddresses“ oder „userPrincipalName“ vor.

„ProxyCalc“ benötigt eine gewisse Zeit zum Verarbeiten einer Änderung für einen Benutzer und ist nicht synchron mit dem Azure AD Connect-Exportvorgang.

> [!NOTE]
> Die „ProxyCalc“-Logik weist verschiedene zusätzliche Verhaltensweisen für erweiterte Szenarien auf, die in diesem Artikel nicht dokumentiert werden. Dieses Thema dient zum Erläutern des Verhaltens und nicht zum Dokumentieren der gesamten internen Logik.

### <a name="quarantined-attribute-values"></a>Isolierte Attributwerte
Schattenattribute werden auch verwendet, wenn Attributwerte doppelt vorhanden sind. Weitere Informationen finden Sie unter [Resilienz bei doppelten Attributen](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Weitere Informationen
* [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
