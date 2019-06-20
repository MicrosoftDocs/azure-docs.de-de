---
title: Konfigurierbare Tokengültigkeitsdauern in Azure Active Directory | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Gültigkeitsdauern für Token festlegen, die von Azure AD ausgestellt werden.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2019
ms.author: ryanwi
ms.custom: aaddev, annaba
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc81f0a5c75d9aeee39f0633521d692c8d30c474
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823466"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Konfigurierbare Tokengültigkeitsdauern in Azure Active Directory (Vorschau)

Sie können die Gültigkeitsdauer eines Tokens angeben, das von Azure Active Directory (Azure AD) ausgestellt wird. Die Tokengültigkeitsdauer können Sie für alle Apps Ihrer Organisation, für eine mehrinstanzenfähige Anwendung (Multiorganisationsanwendung) oder für einen bestimmten Dienstprinzipal in Ihrer Organisation festlegen.

> [!IMPORTANT]
> Wir haben Feedback zur Vorschauversion von unseren Kunden erhalten und auf dessen Grundlage das Feature für eine konfigurierbare Tokengültigkeitsdauer durch [Funktionen zur Verwaltung von Authentifizierungssitzungen](https://go.microsoft.com/fwlink/?linkid=2083106) in Azure AD mit bedingtem Zugriff ersetzt. Ab 1. November 2019 gilt dieses Feature als veraltet. Wenn Sie die Richtlinie für eine konfigurierbare Tokengültigkeitsdauer verwenden, sollten Sie zum neuen Feature für den bedingten Zugriff wechseln. 

In Azure AD steht ein Richtlinienobjekt für eine Reihe von Regeln, die für einzelne Anwendungen oder alle Anwendungen in einer Organisation erzwungen werden. Jeder Richtlinientyp verfügt über eine eindeutige Struktur mit einem Satz von Eigenschaften, die auf Objekte angewendet werden, denen sie zugewiesen sind.

Sie können eine Richtlinie als Standardrichtlinie für Ihre Organisation festlegen. Die Richtlinie wird auf alle Anwendungen der Organisation angewendet, sofern sie nicht von einer Richtlinie mit einer höheren Priorität außer Kraft gesetzt wird. Sie können eine Richtlinie auch bestimmten Anwendungen zuweisen. Die Reihenfolge der Priorität variiert je nach Richtlinientyp.

> [!NOTE]
> Die Richtlinie für konfigurierbare Tokengültigkeitsdauer wird für SharePoint Online nicht unterstützt.  Sie haben zwar die Möglichkeit, diese Richtlinie über PowerShell zu erstellen, sie wird von SharePoint Online aber nicht akzeptiert. Im [SharePoint Online-Blog](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) finden Sie weitere Informationen zum Konfigurieren von Timeouts für Leerlaufsitzungen.
>* Die Standardlebensdauer für das SharePoint Online-Zugriffstoken beträgt eine Stunde. 
>* Die standardmäßige maximale Inaktivitätsdauer für das SharePoint Online-Aktualisierungstoken beträgt 90 Tage.

## <a name="token-types"></a>Tokentypen

Sie können die Tokengültigkeitsdauer-Richtlinien für Aktualisierungstoken, Zugriffstoken, Sitzungstoken und ID-Token festlegen.

### <a name="access-tokens"></a>Zugriffstoken

Clients nutzen Zugriffstoken, um auf eine geschützte Ressource zuzugreifen. Ein Zugriffstoken kann nur für eine bestimmte Kombination aus Benutzer, Client und Ressource verwendet werden. Zugriffstoken können nicht widerrufen werden und sind bis zu ihrem Ablauf gültig. Ein böswilliger Akteur, der ein Zugriffstoken abgerufen hat, kann es während seiner gesamten Lebensdauer verwenden. Das Anpassen der Gültigkeitsdauer eines Zugriffstokens erfordert einen Kompromiss. Hierbei steht eine Verbesserung der Systemleistung einer Verlängerung der Zeitspanne gegenüber, über die der Client weiterhin Zugriff hat, nachdem das Konto des Benutzers deaktiviert wurde. Eine verbesserte Systemleistung wird dadurch erzielt, dass ein Client weniger oft ein neues Zugriffstoken abrufen muss.  Die Standardeinstellung ist „1 Stunde“. Nach einer Stunde muss der Client das Aktualisierungstoken verwenden, um (meist im Hintergrund) ein neues Aktualisierungstoken und Zugriffstoken abzurufen. 

### <a name="refresh-tokens"></a>Aktualisierungstoken

Wenn ein Client ein Zugriffstoken für den Zugriff auf eine geschützte Ressource abruft, erhält er auch ein Aktualisierungstoken. Das Aktualisierungstoken wird verwendet, um neue Zugriffs-/Aktualisierungstoken-Paare abzurufen, wenn das aktuelle Zugriffstoken abläuft. Ein Aktualisierungstoken ist an eine Kombination aus Benutzer und Client gebunden. Ein Aktualisierungstoken kann [jederzeit widerrufen werden](access-tokens.md#token-revocation), und die Gültigkeit des Tokens wird bei jeder Verwendung des Tokens geprüft.  Aktualisierungstoken werden nicht widerrufen, wenn sie zum Abrufen neuer Zugriffstoken verwendet werden. Dies ist eine bewährte Methode, um das alte Token beim Abrufen eines neuen Tokens sicher zu löschen. 

Es ist wichtig, zwischen vertraulichen Clients und öffentlichen Clients zu unterscheiden, da dies eine Auswirkung darauf hat, wie lange Aktualisierungstoken verwendet werden können. Weitere Informationen zu den verschiedenen Clienttypen finden Sie unter [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Tokengültigkeitsdauer bei Aktualisierungstoken von vertraulichen Clients
Vertrauliche Clients sind Anwendungen, die ein Clientkennwort (Geheimnis) sicher speichern können. Damit kann bewiesen werden, dass Anforderungen von der geschützten Clientanwendung stammen, und nicht von einem böswilligen Akteur. Eine Web-App ist beispielsweise ein vertraulicher Client, da sie ein Clientgeheimnis auf dem Webserver speichern kann. Sie ist daher nicht gefährdet. Da derartige Flows sicherer sind, lautet die Standardgültigkeitsdauer von Aktualisierungstoken, die für diese Flows ausgestellt werden, `until-revoked`. Sie kann nicht mithilfe einer Richtlinie geändert werden und wird bei der absichtlichen Kennwortzurücksetzung nicht zurückgesetzt.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tokengültigkeitsdauer bei Aktualisierungstoken von öffentlichen Clients

Öffentliche Clients können ein Clientkennwort (Geheimnis) nicht sicher speichern. Eine iOS- oder Android-App kann beispielsweise kein Geheimnis vor dem Ressourcenbesitzer verbergen und gilt daher als öffentlicher Client. Sie können Richtlinien für Ressourcen festlegen, um zu verhindern, dass Aktualisierungstoken aus öffentlichen Clients, deren Alter einen festgelegten Wert überschritten hat, ein neues Zugriffs-/Aktualisierungstoken-Paar abrufen können. (Verwenden Sie hierfür die Eigenschaft „Max. Zeit der Inaktivität für Aktualisierungstoken“ (`MaxInactiveTime`).) Sie können Richtlinien auch verwenden, um einen Zeitraum bis zum einem Punkt festzulegen, ab dem die Aktualisierungstoken nicht mehr akzeptiert werden. (Verwenden Sie hierfür die Eigenschaft „Max. Alter Aktualisierungstoken“.) Sie können die Gültigkeitsdauer eines Aktualisierungstokens anpassen, um zu steuern, wann und wie oft der Benutzer erneut Anmeldeinformationen eingeben muss, anstatt automatisch erneut authentifiziert zu werden, wenn er eine öffentliche Clientanwendung verwendet.

### <a name="id-tokens"></a>ID-Token
ID-Token werden an Websites und native Clients übergeben. ID-Token enthalten Profilinformationen zu einem Benutzer. Ein ID-Token ist an eine bestimmte Kombination von Benutzer und Client gebunden. ID-Token werden bis zu ihrem Ablaufdatum als gültig betrachtet. In der Regel passt eine Webanwendung die Gültigkeitsdauer der Sitzung eines Benutzers in der Anwendung an die Gültigkeitsdauer des für den Benutzer ausgegebenen ID-Tokens an. Sie können die Gültigkeitsdauer eines ID-Tokens anpassen, um zu steuern, wie oft die Webanwendung den Ablauf der Anwendungssitzung veranlasst und wie oft der Benutzer für Azure AD erneut authentifiziert werden muss (entweder im Hintergrund oder interaktiv).

### <a name="single-sign-on-session-tokens"></a>Sitzungstoken für einmaliges Anmelden
Wenn sich ein Benutzer bei Azure AD authentifiziert, wird eine SSO-Sitzung (Single Sign-On, einmaliges Anmelden) im Browser des Benutzers und bei Azure AD hergestellt. Das SSO-Token stellt diese Sitzung dar (in Form eines Cookies). Das SSO-Sitzungstoken ist nicht an eine bestimmte Ressource/Clientanwendung gebunden. SSO-Sitzungstoken können widerrufen werden, und ihre Gültigkeit wird bei jeder Verwendung überprüft.

In Azure AD werden zwei Arten von SSO-Sitzungstoken verwendet: beständig und nicht beständig. Beständige Sitzungstoken werden vom Browser als beständige Cookies gespeichert. Nicht beständige Sitzungstoken werden als Sitzungscookies gespeichert. (Sitzungscookies werden zerstört, wenn der Browser geschlossen wird.) In der Regel wird ein nicht persistentes Sitzungstoken gespeichert. Wenn der Benutzer jedoch während der Authentifizierung das Kontrollkästchen **Angemeldet bleiben** wählt, wird ein persistentes Sitzungstoken gespeichert.

Nicht beständige Sitzungstoken haben eine Gültigkeitsdauer von 24 Stunden. Beständige Token haben eine Gültigkeitsdauer von 180 Tagen. Jedes Mal, wenn ein SSO-Sitzungstoken innerhalb seiner Gültigkeitsdauer verwendet wird, verlängert sich die Gültigkeitsdauer je nach Tokentyp um weitere 24 Stunden bzw. 180 Tage. Wenn ein SSO-Sitzungstoken innerhalb seiner Gültigkeitsdauer nicht verwendet wird, wird es als abgelaufen erachtet und nicht mehr akzeptiert.

Sie können eine Richtlinie verwenden, um den Zeitpunkt nach dem Ausstellen des ersten Sitzungstokens festzulegen, nach dem das Sitzungstoken nicht mehr akzeptiert wird. (Verwenden Sie hierfür die Eigenschaft „Max. Alter Sitzungstoken“.) Sie können die Gültigkeitsdauer eines Sitzungstokens anpassen, um zu steuern, wann und wie oft ein Benutzer erneut Anmeldeinformationen eingeben muss, anstatt automatisch authentifiziert zu werden, wenn er eine Webanwendung verwendet.

### <a name="token-lifetime-policy-properties"></a>Eigenschaften von Tokengültigkeitsdauer-Richtlinien
Eine Tokengültigkeitsdauer-Richtlinie ist ein Richtlinienobjekt, das Regeln für die Tokengültigkeitsdauer enthält. Verwenden Sie die Eigenschaften der Richtlinie, um angegebene Tokengültigkeitsdauern zu steuern. Wenn keine Richtlinie festgelegt ist, erzwingt das System den Standardwert für die Gültigkeitsdauer.

### <a name="configurable-token-lifetime-properties"></a>Konfigurierbare Eigenschaften der Tokengültigkeitsdauer
| Eigenschaft | Richtlinien-Eigenschaftszeichenfolge | Betrifft | Standard | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Gültigkeitsdauer Zugriffstoken |AccessTokenLifetime |Zugriffstoken, ID-Token, SAML2-Token |1 Stunde |10 Minuten |1 Tag |
| Max. Zeit der Inaktivität für Aktualisierungstoken |MaxInactiveTime |Aktualisierungstoken |90 Tage |10 Minuten |90 Tage |
| Max. Alter Single-Factor-Aktualisierungstoken |MaxAgeSingleFactor |Aktualisierungstoken (für alle Benutzer) |Bis zum Widerruf |10 Minuten |Bis zum Widerruf<sup>1</sup> |
| Max. Alter Multi-Factor-Aktualisierungstoken |MaxAgeMultiFactor |Aktualisierungstoken (für alle Benutzer) |Bis zum Widerruf |10 Minuten |Bis zum Widerruf<sup>1</sup> |
| Max. Alter Single-Factor-Sitzungstoken |MaxAgeSessionSingleFactor<sup>2</sup> |Sitzungstoken (beständig und nicht beständig) |Bis zum Widerruf |10 Minuten |Bis zum Widerruf<sup>1</sup> |
| Max. Alter Multi-Factor-Sitzungstoken |MaxAgeSessionMultiFactor<sup>3</sup> |Sitzungstoken (beständig und nicht beständig) |Bis zum Widerruf |10 Minuten |Bis zum Widerruf<sup>1</sup> |

* <sup>1</sup>365 Tage ist die explizite Maximallänge, die für diese Attribute festgelegt werden kann.

### <a name="exceptions"></a>Ausnahmen
| Eigenschaft | Betrifft | Standard |
| --- | --- | --- |
| Maximales Alter des Aktualisierungstokens (für Verbundbenutzer mit unzureichenden Widerrufsinformationen ausgestellt<sup>1</sup>) |Aktualisierungstoken (für Verbundbenutzer mit unzureichenden Widerrufsinformationen ausgestellt<sup>1</sup>) |12 Stunden |
| Max. Zeit der Inaktivität für Aktualisierungstoken (für vertrauliche Clients ausgestellt) |Aktualisierungstoken (für vertrauliche Clients ausgestellt) |90 Tage |
| Max. Alter Aktualisierungstoken (für vertrauliche Clients ausgestellt) |Aktualisierungstoken (für vertrauliche Clients ausgestellt) |Bis zum Widerruf |

* <sup>1</sup>Zu Verbundbenutzern mit unzureichenden Widerrufsinformationen zählen Benutzer, bei denen das Attribut „LastPasswordChangeTimestamp“ nicht synchronisiert ist. Für diese Benutzer gilt dieses kurze maximale Alter, da AAD nicht überprüfen kann, wann Token widerrufen werden müssen, die an alte Anmeldeinformationen (z.B. ein Kennwort, das geändert wurde) gebunden sind, und häufiger sicherstellen muss, dass zwischen dem Benutzer und den zugeordneten Token keine Probleme bestehen. Zum Verbessern dieses Verfahrens müssen Mandantenadministratoren sicherstellen, dass das Attribut „LastPasswordChangeTimestamp“ synchronisiert wird (dies kann für das Benutzerobjekt über Powershell oder AADSync festgelegt werden).

### <a name="policy-evaluation-and-prioritization"></a>Richtlinienauswertung und Priorisierung
Sie können eine Richtlinie für die Gültigkeitsdauer von Token erstellen und dann einer bestimmten Anwendung, Ihrer Organisation und Dienstprinzipalen zuweisen. Für eine bestimmte Anwendung können mehrere Richtlinien gelten. Folgende Regeln bestimmen, welche Tokengültigkeitsdauer-Richtlinie wirksam wird:

* Wenn eine Richtlinie explizit dem Dienstprinzipal zugewiesen ist, wird sie erzwungen.
* Wenn dem Dienstprinzipal nicht explizit eine Richtlinie zugewiesen ist, wird eine Richtlinie erzwungen, die explizit der übergeordneten Organisation des Dienstprinzipals zugewiesen ist.
* Wenn dem Dienstprinzipal oder der Organisation nicht explizit eine Richtlinie zugewiesen ist, wird die Richtlinie erzwungen, die der Anwendung zugewiesen ist.
* Wenn dem Dienstprinzipal, der Organisation oder dem Anwendungsobjekt keine Richtlinie zugewiesen ist, werden die Standardwerte erzwungen. (Siehe Tabelle unter [Konfigurierbare Eigenschaften der Tokengültigkeitsdauer](#configurable-token-lifetime-properties).)

Ausführliche Informationen zu den Beziehungen zwischen Anwendungsobjekten und Dienstprinzipalobjekten finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](app-objects-and-service-principals.md).

Die Gültigkeit des Tokens wird zum Zeitpunkt seiner Verwendung überprüft. Die Richtlinie mit der höchsten Priorität für die Anwendung, auf die zugegriffen wird, wird wirksam.

Alle hier verwendeten Zeiträume werden nach dem C#-Objekt [TimeSpan](/dotnet/api/system.timespan) (D.HH:MM:SS) formatiert.  Danach werden 80 Tage und 30 Minuten im Format `80.00:30:00` dargestellt.  Das führende D kann gelöscht werden, wenn der Wert 0 ist. Danach werden 90 Minuten im Format `00:90:00` dargestellt.  

> [!NOTE]
> Hier ist ein Beispielszenario angegeben.
>
> Ein Benutzer möchte auf zwei Webanwendungen zugreifen: Webanwendung A und Webanwendung B.
> 
> Faktoren:
> * Beide Webanwendungen gehören zu derselben übergeordneten Organisation.
> * Als Standard für die übergeordnete Organisation wird die Tokengültigkeitsdauer-Richtlinie 1 mit einem maximalen Alter für Sitzungstoken von acht Stunden festgelegt.
> * Webanwendung A ist eine regelmäßig verwendete Webanwendung, die nicht mit Richtlinien verknüpft ist.
> * Webanwendung B wird für hochsensible Prozesse verwendet. Der dazugehörige Dienstprinzipal ist mit der Tokengültigkeitsdauer-Richtlinie 2 verknüpft, die über ein maximales Alter für Sitzungstoken von 30 Minuten verfügt.
>
> Um 12:00 Uhr startet der Benutzer eine neue Browsersitzung und versucht, auf die Webanwendung A zuzugreifen. Der Benutzer wird an Azure AD umgeleitet und aufgefordert, sich anzumelden. Es wird ein Cookie erstellt, das über ein Sitzungstoken im Browser verfügt. Der Benutzer wird mit einem ID-Token an die Webanwendung A zurückgeleitet. Dieses ID-Token erlaubt dem Benutzer den Zugriff auf die Anwendung.
>
> Um 12:15 Uhr versucht der Benutzer, auf Webanwendung B zuzugreifen. Der Browser führt die Umleitung an Azure AD durch, wo das Sitzungscookie erkannt wird. Der Dienstprinzipal von Webanwendung B ist mit Tokengültigkeitsdauer-Richtlinie 2 verknüpft, ist aber zugleich Teil der übergeordneten Organisation mit der Tokengültigkeitsdauer-Standardrichtlinie 1. Tokengültigkeitsdauer-Richtlinie 2 wird wirksam, da mit Dienstprinzipalen verknüpfte Richtlinien eine höhere Priorität als Organisationsstandardrichtlinien haben. Das Sitzungstoken wurde innerhalb der letzten 30 Minuten erstmalig ausgegeben, weshalb es als gültig erachtet wird. Der Benutzer wird mit einem ID-Token, das ihm Zugriff gewährt, an die Webanwendung B zurückgeleitet.
>
> Um 13:00 Uhr versucht der Benutzer, auf Webanwendung A zuzugreifen. Er wird an Azure AD umgeleitet. Webanwendung A ist nicht mit Richtlinien verknüpft, aber da sie sich in einer Organisation mit Tokengültigkeitsdauer-Standardrichtlinie 1 befindet, ist diese Richtlinie wirksam. Das Sitzungscookie, das innerhalb der letzten acht Stunden ursprünglich ausgestellt wurde, wird erkannt. Der Benutzer wird automatisch zurück an Webanwendung A mit einem neuen ID-Token umgeleitet. Der Benutzer muss sich nicht authentifizieren.
>
> Unmittelbar danach versucht der Benutzer, auf Webanwendung B zuzugreifen. Er wird an Azure AD umgeleitet. Wie zuvor auch, ist Tokengültigkeitsdauer-Richtlinie 2 wirksam. Da das Token vor mehr als 30 Minuten ausgestellt wurde, wird der Benutzer aufgefordert, die Anmeldeinformationen erneut einzugeben. Ein ganz neues Sitzungstoken und ein ID-Token werden ausgestellt. Nun kann der Benutzer auf Webanwendung B zugreifen.
>
>

## <a name="configurable-policy-property-details"></a>Details zu konfigurierbaren Richtlinieneigenschaften
### <a name="access-token-lifetime"></a>Gültigkeitsdauer Zugriffstoken
**Zeichenfolge:** AccessTokenLifetime

**Betrifft:** Zugriffstoken, ID-Token

**Zusammenfassung:** Diese Richtlinie steuert, wie lange Zugriffstoken und ID-Token für diese Ressource als gültig angesehen werden. Durch das Reduzieren des Werts für die Eigenschaft „Gültigkeitsdauer Zugriffstoken“ wird das Risiko verringert, dass ein Zugriffstoken oder ID-Token von einem böswilligen Akteur für einen längeren Zeitraum verwendet wird. (Diese Token können nicht widerrufen werden.) Der Nachteil hierbei ist, dass die Leistung beeinträchtigt wird, da die Token häufiger ersetzt werden müssen.

### <a name="refresh-token-max-inactive-time"></a>Max. Zeit der Inaktivität für Aktualisierungstoken
**Zeichenfolge:** MaxInactiveTime

**Betrifft:** Aktualisierungstoken

**Zusammenfassung:** Diese Richtlinie steuert, wie alt ein Aktualisierungstoken sein darf, bevor ein Client es nicht mehr verwenden kann, um ein neues Zugriffs-/Aktualisierungstoken-Paar abzurufen, wenn versucht wird, auf diese Ressource zuzugreifen. Da bei Verwendung eines Aktualisierungstokens normalerweise ein neues Aktualisierungstoken zurückgegeben wird, wird mit dieser Richtlinie der Zugriff verhindert, wenn der Client versucht, während des angegeben Zeitraums mit dem aktuellen Aktualisierungstoken auf eine Ressource zuzugreifen.

Durch diese Richtlinie werden Benutzer, die nicht auf ihrem Client aktiv waren, gezwungen, sich erneut zu authentifizieren, um ein neues Aktualisierungstoken abzurufen.

Die Eigenschaft „Maximale Inaktivitätszeit Aktualisierungstoken“ muss auf einen niedrigeren Wert als die Eigenschaften „Maximales Alter Single-Factor-Token“ und „Maximales Alter Multi-Factor-Aktualisierungstoken“ festgelegt werden.

### <a name="single-factor-refresh-token-max-age"></a>Max. Alter Single-Factor-Aktualisierungstoken
**Zeichenfolge:** MaxAgeSingleFactor

**Betrifft:** Aktualisierungstoken

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer ein Aktualisierungstoken verwenden kann, um ein neues Zugriffs-/Aktualisierungstoken-Paar abzurufen, nachdem die letzte erfolgreiche Authentifizierung mit einem einzigen Faktor durchgeführt wurde. Nachdem sich ein Benutzer authentifiziert und ein neues Aktualisierungstoken erhalten hat, kann er den Aktualisierungstokenflow für den angegebenen Zeitraum verwenden. (Dies gilt, solange das aktuelle Aktualisierungstoken nicht widerrufen wird und nicht länger als für den Inaktivitätszeitraum ungenutzt bleibt.) An diesem Punkt wird der Benutzer zum erneuten Authentifizieren gezwungen, um ein neues Aktualisierungstoken zu erhalten.

Durch die Reduzierung des maximalen Alters müssen sich Benutzer häufiger authentifizieren. Da die Single-Factor Authentication als weniger sicher als die Multi-Factor Authentication erachtet wird, wird empfohlen, diese Eigenschaft auf einen Wert festzulegen, der kleiner oder gleich dem Wert für die Eigenschaft „Max. Alter Multi-Factor-Aktualisierungstoken“ ist.

### <a name="multi-factor-refresh-token-max-age"></a>Max. Alter Multi-Factor-Aktualisierungstoken
**Zeichenfolge:** MaxAgeMultiFactor

**Betrifft:** Aktualisierungstoken

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer ein Aktualisierungstoken verwenden kann, um ein neues Zugriffs-/Aktualisierungstoken-Paar abzurufen, nachdem die letzte erfolgreiche Authentifizierung mit mehreren Faktoren durchgeführt wurde. Nachdem sich ein Benutzer authentifiziert und ein neues Aktualisierungstoken erhalten hat, kann er den Aktualisierungstokenflow für den angegebenen Zeitraum verwenden. (Dies gilt, solange das aktuelle Aktualisierungstoken nicht widerrufen wird und nicht länger als für den Inaktivitätszeitraum ungenutzt bleibt.) An diesem Punkt werden Benutzer gezwungen, sich erneut zu authentifizieren, um ein neues Aktualisierungstoken zu erhalten.

Durch die Reduzierung des maximalen Alters müssen sich Benutzer häufiger authentifizieren. Da die Single-Factor Authentication als weniger sicher als die Multi-Factor Authentication erachtet wird, wird empfohlen, diese Eigenschaft auf einen Wert festzulegen, der größer oder gleich dem Wert für die Eigenschaft „Max. Alter Single-Factor-Aktualisierungstoken“ ist.

### <a name="single-factor-session-token-max-age"></a>Max. Alter Single-Factor-Sitzungstoken
**Zeichenfolge:** MaxAgeSessionSingleFactor

**Betrifft:** Sitzungstoken (beständig und nicht beständig)

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer ein Sitzungstoken verwenden kann, um ein neues ID- und Sitzungstoken abrufen, nachdem die letzte erfolgreiche Authentifizierung mit einem einzigen Faktor durchgeführt wurde. Nachdem sich ein Benutzer authentifiziert und ein neues Sitzungstoken erhalten hat, kann er den Sitzungstokenflow für den angegebenen Zeitraum verwenden. (Dies gilt, solange das aktuelle Sitzungstoken nicht widerrufen wird und nicht abgelaufen ist.) Nach dem angegebenen Zeitraum wird der Benutzer gezwungen, sich erneut zu authentifizieren, um ein neues Sitzungstoken zu erhalten.

Durch die Reduzierung des maximalen Alters müssen sich Benutzer häufiger authentifizieren. Da die Single-Factor Authentication als weniger sicher als die Multi-Factor Authentication erachtet wird, wird empfohlen, diese Eigenschaft auf einen Wert festzulegen, der kleiner oder gleich dem Wert für die Eigenschaft „Max. Alter Multi-Factor-Sitzungstoken“ ist.

### <a name="multi-factor-session-token-max-age"></a>Max. Alter Multi-Factor-Sitzungstoken
**Zeichenfolge:** MaxAgeSessionMultiFactor

**Betrifft:** Sitzungstoken (beständig und nicht beständig)

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer ein Sitzungstoken verwenden kann, um ein neues ID- und Sitzungstoken abzurufen, nachdem die letzte erfolgreiche Authentifizierung mit mehreren Faktoren durchgeführt wurde. Nachdem sich ein Benutzer authentifiziert und ein neues Sitzungstoken erhalten hat, kann er den Sitzungstokenflow für den angegebenen Zeitraum verwenden. (Dies gilt, solange das aktuelle Sitzungstoken nicht widerrufen wird und nicht abgelaufen ist.) Nach dem angegebenen Zeitraum wird der Benutzer gezwungen, sich erneut zu authentifizieren, um ein neues Sitzungstoken zu erhalten.

Durch die Reduzierung des maximalen Alters müssen sich Benutzer häufiger authentifizieren. Da die Single-Factor Authentication als weniger sicher als die Multi-Factor Authentication erachtet wird, wird empfohlen, diese Eigenschaft auf einen Wert festzulegen, der größer oder gleich dem Wert für die Eigenschaft „Max. Alter Single-Factor-Sitzungstoken“ ist.

## <a name="example-token-lifetime-policies"></a>Beispiel: Tokengültigkeitsdauer-Richtlinien
In Azure AD sind viele Szenarien möglich, was das Erstellen und Verwalten von Tokengültigkeitsdauern für Apps, Dienstprinzipale und Ihre gesamte Organisation betrifft. In diesem Abschnitt werden einige allgemeine Richtlinienszenarien beschrieben, die es ermöglichen, neue Regeln für folgende Festlegungen vorzugeben:

* Tokengültigkeitsdauer
* Max. Inaktivitätszeit von Token
* Max. Alter von Token

Anhand der Beispiele wird Folgendes veranschaulicht:

* Verwalten der Standardrichtlinie einer Organisation
* Erstellen einer Richtlinie für die Webanmeldung
* Erstellen einer Richtlinie für eine native App, die eine Web-API aufruft
* Verwalten einer erweiterten Richtlinie

### <a name="prerequisites"></a>Voraussetzungen
In den folgenden Beispielen wird gezeigt, wie Sie Richtlinien für Apps, Dienstprinzipale und Ihre gesamte Organisation erstellen, aktualisieren, verknüpfen und löschen. Wenn Azure AD neu für Sie ist, ist es ratsam, sich über das [Erhalten eines Azure AD-Mandanten](quickstart-create-new-tenant.md) zu informieren, bevor Sie mit diesen Beispielen fortfahren.  

Führen Sie die folgenden Schritte aus, um zu beginnen:

1. Laden Sie die aktuelle [öffentliche Vorschauversion des Azure AD PowerShell-Moduls](https://www.powershellgallery.com/packages/AzureADPreview) herunter.
2. Führen Sie den Befehl `Connect` aus, um sich an Ihrem Azure AD-Administratorkonto anzumelden. Führen Sie diesen Befehl bei jedem Start einer neuen Sitzung aus.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Führen Sie den folgenden Befehl aus, um alle Richtlinien anzuzeigen, die in Ihrer Organisation erstellt wurden. Führen Sie diesen Befehl nach den meisten Vorgängen in den folgenden Szenarien aus. Wenn Sie den Befehl ausführen, können Sie auch die ** ** Ihrer Richtlinien erhalten.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Beispiel: Verwalten der Standardrichtlinie einer Organisation
Anhand dieses Beispiels können Sie eine Richtlinie erstellen, die es den Benutzern ermöglicht, sich in Ihrer gesamten Organisation weniger häufig anmelden zu müssen. Erstellen Sie hierzu eine Richtlinie für die Tokengültigkeitsdauer für Single-Factor-Aktualisierungstoken, die auf Ihre gesamte Organisation angewendet wird. Die Richtlinie wird auf jede Anwendung in Ihrer Organisation und jeden Dienstprinzipal angewendet, für die bzw. den noch keine Richtlinie festgelegt wurde.

1. Erstellen Sie eine Tokengültigkeitsdauer-Richtlinie.

    1. Legen Sie das Single-Factor-Aktualisierungstoken auf „Bis auf Widerruf“ fest. Das Token läuft erst ab, nachdem der Zugriff widerrufen wurde. Erstellen Sie die folgende Richtliniendefinition:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. Führen Sie den folgenden Befehl aus, um die Richtlinie zu erstellen:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. Führen Sie den folgenden Befehl aus, um Ihre neue Richtlinie anzuzeigen und deren **ObjectId** abzurufen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Aktualisieren Sie die Richtlinie.

    Unter Umständen möchten Sie erreichen, dass die erste Richtlinie, die Sie in diesem Beispiel festlegen, nicht so streng ist, wie es für den Dienst eigentlich erforderlich ist. Führen Sie den folgenden Befehl aus, um für Ihr Single-Factor-Aktualisierungstoken festzulegen, dass es nach zwei Tagen abläuft:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Beispiel: Erstellen einer Richtlinie für die Webanmeldung

In diesem Beispiel erstellen Sie eine Richtlinie, bei der es erforderlich ist, dass sich Benutzer häufiger für Ihre Web-App authentifizieren. Mit dieser Richtlinie wird die Gültigkeitsdauer der Zugriffs-/ID-Token und das maximale Alter eines Multi-Factor-Sitzungstokens auf den Dienstprinzipal Ihrer Web-App festgelegt.

1. Erstellen Sie eine Tokengültigkeitsdauer-Richtlinie.

    Mit dieser Richtlinie für die Webanmeldung werden die Gültigkeitsdauer des Zugriffs-/ID-Tokens und das maximale Alter des Single-Factor-Sitzungstokens auf zwei Stunden festgelegt.

    1. Führen Sie diesen Befehl aus, um die Richtlinie zu erstellen:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Führen Sie den folgenden Befehl aus, um Ihre neue Richtlinie anzuzeigen und deren **ObjectId** abzurufen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Weisen Sie die Richtlinie Ihrem Dienstprinzipal zu. Rufen Sie außerdem die **ObjectId** Ihres Dienstprinzipals ab.

    1. Verwenden Sie das Cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal), um alle Dienstprinzipale Ihrer Organisation oder einen einzelnen Dienstprinzipal anzuzeigen.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. Führen Sie den folgenden Befehl aus, wenn Sie den Dienstprinzipal gefunden haben:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Beispiel: Erstellen einer Richtlinie für eine native App, die eine Web-API aufruft
In diesem Beispiel erstellen Sie eine Richtlinie, bei der sich Benutzer weniger häufig authentifizieren müssen. Mit der Richtlinie wird außerdem der Zeitraum verlängert, über den ein Benutzer inaktiv sein kann, bevor er sich erneut authentifizieren muss. Die Richtlinie wird auf die Web-API angewendet. Wenn die native App die Web-API als Ressource anfordert, wird diese Richtlinie angewendet.

1. Erstellen Sie eine Tokengültigkeitsdauer-Richtlinie.

    1. Führen Sie den folgenden Befehl aus, um eine strenge Richtlinie für eine Web-API zu erstellen:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Führen Sie den folgenden Befehl aus, um Ihre neue Richtlinie abzurufen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Weisen Sie die Richtlinie Ihrer Web-API zu. Außerdem müssen Sie die **ObjectId** Ihrer Anwendung abrufen. Verwenden Sie das Cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) oder das [Azure-Portal](https://portal.azure.com/), um die **ObjectId** Ihrer App zu finden.

    Rufen Sie wie im folgenden Codebeispiel die **ObjectId** Ihrer App ab, und weisen Sie die Richtlinie zu:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Beispiel: Verwalten einer erweiterten Richtlinie
Anhand dieses Beispiels können Sie einige Richtlinien erstellen, um zu erfahren, wie das Prioritätssystem funktioniert. Außerdem erfahren Sie, wie Sie mehrere Richtlinien verwalten, die auf verschiedene Objekte angewendet werden.

1. Erstellen Sie eine Tokengültigkeitsdauer-Richtlinie.

    1. Führen Sie den folgenden Befehl aus, um eine Standardrichtlinie für die Organisation zu erstellen, mit der die Gültigkeitsdauer des Single-Factor-Aktualisierungstokens auf 30 Tage festgelegt wird:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. Führen Sie den folgenden Befehl aus, um Ihre neue Richtlinie abzurufen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Weisen Sie die Richtlinie einem Dienstprinzipal zu.

    Sie verfügen jetzt über eine Richtlinie, die für die gesamte Organisation gilt. Es kann beispielsweise sein, dass diese 30-Tage-Richtlinie für einen bestimmten Dienstprinzipal beibehalten werden soll, während die Organisationsstandardrichtlinie so geändert wird, dass sie als Obergrenze für „Bis zum Widerruf“ fungiert.

    1. Verwenden Sie das Cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal), um alle Dienstprinzipale Ihrer Organisation abzurufen.

    2. Führen Sie den folgenden Befehl aus, wenn Sie den Dienstprinzipal gefunden haben:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. Legen Sie das Flag `IsOrganizationDefault` auf „false“ fest:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Erstellen Sie eine neue Organisationsstandardrichtlinie:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Sie haben jetzt die ursprüngliche Richtlinie mit Ihrem Dienstprinzipal verknüpft und die neue Richtlinie als Organisationsstandardrichtlinie festgelegt. Beachten Sie, dass Richtlinien für Dienstprinzipale Priorität vor Organisationsstandardrichtlinien haben.

## <a name="cmdlet-reference"></a>Cmdlet-Referenz

### <a name="manage-policies"></a>Verwalten von Richtlinien

Sie können die folgenden Cmdlets zum Verwalten von Richtlinien verwenden.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Erstellt eine neue Richtlinie.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Definition</code> |JSON-Array, dargestellt als Zeichenfolge, das alle Regeln der Richtlinie enthält. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Zeichenfolge mit dem Namen der Richtlinie. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Bei der Einstellung „true“ wird die Richtlinie als Standardrichtlinie der Organisation festgelegt. Bei „false“ wird nichts durchgeführt. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Der Typ der Richtlinie. Verwenden Sie für die Tokengültigkeitsdauer stets „TokenLifetimePolicy“. | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Optional] |Legt eine alternative ID für die Richtlinie fest. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Ruft alle Azure AD-Richtlinien oder eine angegebene Richtlinie ab.

```powershell
Get-AzureADPolicy
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Id</code> [Optional] |Die **ObjectId (ID)** der gewünschten Richtlinie. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Ruft alle Apps und Dienstprinzipale ab, die mit einer Richtlinie verknüpft sind.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Id</code> |Die **ObjectId (ID)** der gewünschten Richtlinie. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Aktualisiert eine vorhandene Richtlinie.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Id</code> |Die **ObjectId (ID)** der gewünschten Richtlinie. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Zeichenfolge mit dem Namen der Richtlinie. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Optional] |JSON-Array, dargestellt als Zeichenfolge, das alle Regeln der Richtlinie enthält. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Optional] |Bei der Einstellung „true“ wird die Richtlinie als Standardrichtlinie der Organisation festgelegt. Bei „false“ wird nichts durchgeführt. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Optional] |Der Typ der Richtlinie. Verwenden Sie für die Tokengültigkeitsdauer stets „TokenLifetimePolicy“. |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Optional] |Legt eine alternative ID für die Richtlinie fest. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Löscht die angegebene Richtlinie.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Id</code> |Die **ObjectId (ID)** der gewünschten Richtlinie. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Anwendungsrichtlinien
Sie können die folgenden Cmdlets für Anwendungsrichtlinien verwenden.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Verknüpft die angegebene Richtlinie mit einer Anwendung.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Id</code> |Die **ObjectId (Id)** der Anwendung. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |Die **ObjectId** der Richtlinie. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Ruft die Richtlinie ab, die einer Anwendung zugewiesen ist.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Id</code> |Die **ObjectId (Id)** der Anwendung. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Entfernt eine Richtlinie aus einer Anwendung.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Id</code> |Die **ObjectId (Id)** der Anwendung. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |Die **ObjectId** der Richtlinie. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Dienstprinzipalrichtlinien
Sie können die folgenden Cmdlets für Dienstprinzipalrichtlinien verwenden.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Verknüpft die angegebene Richtlinie mit einem Dienstprinzipal.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Id</code> |Die **ObjectId (Id)** der Anwendung. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |Die **ObjectId** der Richtlinie. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Ruft alle Richtlinien ab, die mit dem angegebenen Dienstprinzipal verknüpft sind.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Id</code> |Die **ObjectId (Id)** der Anwendung. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Entfernt die Richtlinie aus dem angegebenen Dienstprinzipal.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| <code>&#8209;Id</code> |Die **ObjectId (Id)** der Anwendung. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |Die **ObjectId** der Richtlinie. | `-PolicyId <ObjectId of Policy>` |
