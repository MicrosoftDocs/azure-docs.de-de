---
title: Konfigurierbare Tokengültigkeitsdauer
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie die Gültigkeitsdauer für Token festlegen, die von Microsoft Identity Platform ausgestellt werden.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperfq1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 4dab75a4e95a7561bc86176816cb402c10de781e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077420"
---
# <a name="configurable-token-lifetimes-in-microsoft-identity-platform-preview"></a>Konfigurierbare Tokengültigkeitsdauer in Microsoft Identity Platform (Vorschau)

> [!IMPORTANT]
> Nach dem 30. Januar 2021 können Mandanten die Lebensdauer von Aktualisierungs- und Sitzungstoken nicht mehr konfigurieren, und die Konfiguration von Aktualisierungs- und Sitzungstoken in Richtlinien wird von Azure Active Directory nach diesem Datum nicht mehr berücksichtigt.
>
> Wenn Sie weiterhin definieren möchten, nach welcher Zeit ein Benutzer zur erneuten Anmeldung aufgefordert werden soll, können Sie die Anmeldehäufigkeit im bedingten Zugriff konfigurieren. Weitere Informationen zum bedingten Zugriff finden Sie auf der Seite [Azure Active Directory – Preise](https://azure.microsoft.com/en-us/pricing/details/active-directory/).
>
> Bei Mandanten, die nach dem Einstellungsdatum keinen bedingten Zugriff verwenden möchten, wird von Azure AD die im nächsten Abschnitt beschriebene Standardkonfiguration verwendet.

## <a name="configurable-token-lifetime-properties-after-the-retirement"></a>Konfigurierbare Eigenschaften der Tokenlebensdauer nach der Einstellung
Die folgenden Eigenschaften und die entsprechenden Werte haben Auswirkungen auf die Konfiguration von Aktualisierungs- und Sitzungstoken. Nach der Einstellung der Konfiguration von Aktualisierungs- und Sitzungstoken berücksichtigt Azure AD nur noch den unten angegebenen Standardwert, und zwar unabhängig davon, ob in Richtlinien benutzerdefinierte Werte konfiguriert wurden. Die Lebensdauer von Zugriffstoken kann jedoch auch nach der Einstellung weiterhin konfiguriert werden. 

|Eigenschaft   |Richtlinien-Eigenschaftszeichenfolge    |Betrifft |Standard |
|----------|-----------|------------|------------|
|Max. Zeit der Inaktivität für Aktualisierungstoken |MaxInactiveTime  |Aktualisierungstoken |90 Tage  |
|Max. Alter Single-Factor-Aktualisierungstoken  |MaxAgeSingleFactor  |Aktualisierungstoken (für alle Benutzer)  |Bis zum Widerruf  |
|Max. Alter Multi-Factor-Aktualisierungstoken  |MaxAgeMultiFactor  |Aktualisierungstoken (für alle Benutzer) |180 Tage  |
|Max. Alter Single-Factor-Sitzungstoken  |MaxAgeSessionSingleFactor |Sitzungstoken (beständig und nicht beständig)  |Bis zum Widerruf |
|Max. Alter Multi-Factor-Sitzungstoken  |MaxAgeSessionMultiFactor  |Sitzungstoken (beständig und nicht beständig)  |180 Tage |

## <a name="identify-configuration-in-scope-of-retirement"></a>Identifizieren der von der Einstellung betroffenen Konfiguration

Führen Sie die folgenden Schritte aus, um zu beginnen:

1. Laden Sie die aktuelle [öffentliche Vorschauversion des Azure AD PowerShell-Moduls](https://www.powershellgallery.com/packages/AzureADPreview) herunter.
1. Führen Sie den Befehl `Connect` aus, um sich an Ihrem Azure AD-Administratorkonto anzumelden. Führen Sie diesen Befehl bei jedem Start einer neuen Sitzung aus.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Führen Sie das Cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus, um alle Richtlinien anzuzeigen, die in Ihrer Organisation erstellt wurden.  Alle Ergebnisse mit definierten Eigenschaftswerten, die sich von den oben aufgeführten Standardwerten unterscheiden, sind von der Einstellung betroffen.

    ```powershell
    Get-AzureADPolicy -All
    ```

1. Führen Sie das im Anschluss angegebene Cmdlet [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) aus, und ersetzen Sie dabei **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** durch eine Ihrer Richtlinien-IDs, um zu sehen, welche Apps und Dienstprinzipale mit einer bestimmten Richtlinie verknüpft sind, die Sie identifiziert haben. Anschließend können Sie entscheiden, ob Sie die Anmeldehäufigkeit des bedingten Zugriffs konfigurieren oder die Azure AD-Standardwerte verwenden möchten.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Wenn Ihr Mandant über Richtlinien mit benutzerdefinierten Werten für die Konfigurationen von Aktualisierungs- und Sitzungstoken verfügt, empfiehlt Microsoft, diese Richtlinien auf Werte festzulegen, die den oben beschriebenen Standardwerten entsprechen. Wenn keine Änderungen vorgenommen werden, nutzt Azure AD automatisch die Standardwerte.  

## <a name="overview"></a>Übersicht

Sie können die Gültigkeitsdauer eines Tokens angeben, das von Microsoft Identity Platform ausgestellt wird. Die Tokengültigkeitsdauer können Sie für alle Apps Ihrer Organisation, für eine mehrinstanzenfähige Anwendung (Multiorganisationsanwendung) oder für einen bestimmten Dienstprinzipal in Ihrer Organisation festlegen. Allerdings unterstützen wir derzeit nicht das Konfigurieren der Tokenlebensdauer bei [Dienstprinzipalen für verwaltete Identitäten](../managed-identities-azure-resources/overview.md).

In Azure AD steht ein Richtlinienobjekt für eine Reihe von Regeln, die für einzelne Anwendungen oder alle Anwendungen in einer Organisation erzwungen werden. Jeder Richtlinientyp verfügt über eine eindeutige Struktur mit einem Satz von Eigenschaften, die auf Objekte angewendet werden, denen sie zugewiesen sind.

Sie können eine Richtlinie als Standardrichtlinie für Ihre Organisation festlegen. Die Richtlinie wird auf alle Anwendungen der Organisation angewendet, sofern sie nicht von einer Richtlinie mit einer höheren Priorität außer Kraft gesetzt wird. Sie können eine Richtlinie auch bestimmten Anwendungen zuweisen. Die Reihenfolge der Priorität variiert je nach Richtlinientyp.

Entsprechende Beispiele finden Sie unter [Beispiele zum Konfigurieren der Tokengültigkeitsdauer](configure-token-lifetimes.md).

> [!NOTE]
> Die konfigurierbare Richtlinie für die Tokengültigkeitsdauer gilt nur für mobile und Desktopclients, die auf SharePoint Online- und OneDrive for Business-Ressourcen zugreifen, und nicht für Webbrowsersitzungen.
> Zum Verwalten der Gültigkeitsdauer von Webbrowsersitzungen für SharePoint Online und OneDrive for Business verwenden Sie das Feature [Sitzungsdauer für bedingten Zugriff](../conditional-access/howto-conditional-access-session-lifetime.md). Im [SharePoint Online-Blog](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) finden Sie weitere Informationen zum Konfigurieren von Timeouts für Leerlaufsitzungen.

## <a name="token-types"></a>Tokentypen

Sie können die Tokenlebensdauer-Richtlinien für Aktualisierungstoken, Zugriffstoken, SAML-Token, Sitzungstoken und ID-Token festlegen.

### <a name="access-tokens"></a>Zugriffstoken

Clients nutzen Zugriffstoken, um auf eine geschützte Ressource zuzugreifen. Ein Zugriffstoken kann nur für eine bestimmte Kombination aus Benutzer, Client und Ressource verwendet werden. Zugriffstoken können nicht widerrufen werden und sind bis zu ihrem Ablauf gültig. Ein böswilliger Akteur, der ein Zugriffstoken abgerufen hat, kann es während seiner gesamten Lebensdauer verwenden. Das Anpassen der Gültigkeitsdauer eines Zugriffstokens erfordert einen Kompromiss. Hierbei steht eine Verbesserung der Systemleistung einer Verlängerung der Zeitspanne gegenüber, über die der Client weiterhin Zugriff hat, nachdem das Konto des Benutzers deaktiviert wurde. Eine verbesserte Systemleistung wird dadurch erzielt, dass ein Client weniger oft ein neues Zugriffstoken abrufen muss.  Die Standardeinstellung ist „1 Stunde“. Nach einer Stunde muss der Client das Aktualisierungstoken verwenden, um (meist im Hintergrund) ein neues Aktualisierungstoken und Zugriffstoken abzurufen. 

### <a name="saml-tokens"></a>SAML-Token

SAML-Token werden in vielen webbasierten SaaS-Anwendungen verwendet und über den SAML2-Protokollendpunkt von Azure Active Directory abgerufen. Sie werden auch in Anwendungen genutzt, in denen WS-Verbund verwendet wird. Die Standardlebensdauer des Tokens beträgt 1 Stunde. Für eine Anwendung wird die Lebensdauer des Tokens durch den Wert „NotOnOrAfter“ des `<conditions …>`-Elements im Token angegeben. Nach Ablauf der Lebensdauer des Tokens muss der Client eine neue Authentifizierungsanforderung initiieren, die häufig als Ergebnis des SSO-Sitzungstokens ohne interaktive Anmeldung erfüllt wird.

Der Wert von „NotOnOrAfter“ kann mithilfe des `AccessTokenLifetime`-Parameters in einer `TokenLifetimePolicy` geändert werden. Der Wert wird auf die in der Richtlinie konfigurierte Lebensdauer (sofern vorhanden) zuzüglich eines Zeitversatzfaktors von fünf Minuten festgelegt.

Die im Element `<SubjectConfirmationData>` angegebene Antragstellerbestätigung für „NotOnOrAfter“ ist von der Konfiguration der Tokenlebensdauer nicht betroffen. 

### <a name="refresh-tokens"></a>Aktualisierungstoken

Wenn ein Client ein Zugriffstoken für den Zugriff auf eine geschützte Ressource abruft, erhält er auch ein Aktualisierungstoken. Das Aktualisierungstoken wird verwendet, um neue Zugriffs-/Aktualisierungstoken-Paare abzurufen, wenn das aktuelle Zugriffstoken abläuft. Ein Aktualisierungstoken ist an eine Kombination aus Benutzer und Client gebunden. Ein Aktualisierungstoken kann [jederzeit widerrufen werden](access-tokens.md#token-revocation), und die Gültigkeit des Tokens wird bei jeder Verwendung des Tokens geprüft.  Aktualisierungstoken werden nicht widerrufen, wenn sie zum Abrufen neuer Zugriffstoken verwendet werden. Dies ist eine bewährte Methode, um das alte Token beim Abrufen eines neuen Tokens sicher zu löschen.

Es ist wichtig, zwischen vertraulichen Clients und öffentlichen Clients zu unterscheiden, da dies eine Auswirkung darauf hat, wie lange Aktualisierungstoken verwendet werden können. Weitere Informationen zu den verschiedenen Clienttypen finden Sie unter [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Tokengültigkeitsdauer bei Aktualisierungstoken von vertraulichen Clients
Vertrauliche Clients sind Anwendungen, die ein Clientkennwort (Geheimnis) sicher speichern können. Damit kann bewiesen werden, dass Anforderungen von der geschützten Clientanwendung stammen, und nicht von einem böswilligen Akteur. Eine Web-App ist beispielsweise ein vertraulicher Client, da sie ein Clientgeheimnis auf dem Webserver speichern kann. Sie ist daher nicht gefährdet. Da derartige Flows sicherer sind, lautet die Standardgültigkeitsdauer von Aktualisierungstoken, die für diese Flows ausgestellt werden, `until-revoked`. Sie kann nicht mithilfe einer Richtlinie geändert werden und wird bei der absichtlichen Kennwortzurücksetzung nicht zurückgesetzt.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tokengültigkeitsdauer bei Aktualisierungstoken von öffentlichen Clients

Öffentliche Clients können ein Clientkennwort (Geheimnis) nicht sicher speichern. Eine iOS- oder Android-App kann beispielsweise kein Geheimnis vor dem Ressourcenbesitzer verbergen und gilt daher als öffentlicher Client. Sie können Richtlinien für Ressourcen festlegen, um zu verhindern, dass Aktualisierungstoken aus öffentlichen Clients, deren Alter einen festgelegten Wert überschritten hat, ein neues Zugriffs-/Aktualisierungstoken-Paar abrufen können. Verwenden Sie hierfür die Eigenschaft [Max. Zeit der Inaktivität für Aktualisierungstoken](#refresh-token-max-inactive-time) (`MaxInactiveTime`). Sie können Richtlinien auch verwenden, um einen Zeitraum bis zum einem Punkt festzulegen, ab dem die Aktualisierungstoken nicht mehr akzeptiert werden. Verwenden Sie zu diesem Zweck eine der Eigenschaften [Max. Alter Single-Factor-Aktualisierungstoken](#single-factor-session-token-max-age) oder [Max. Alter Multi-Factor-Sitzungstoken](#multi-factor-refresh-token-max-age). Sie können die Gültigkeitsdauer eines Aktualisierungstokens anpassen, um zu steuern, wann und wie oft der Benutzer erneut Anmeldeinformationen eingeben muss, anstatt automatisch erneut authentifiziert zu werden, wenn er eine öffentliche Clientanwendung verwendet.

> [!NOTE]
> Die Eigenschaft „Max. Alter“ ist die Zeitspanne, in der ein Token verwendet werden kann. 

### <a name="id-tokens"></a>ID-Token
ID-Token werden an Websites und native Clients übergeben. ID-Token enthalten Profilinformationen zu einem Benutzer. Ein ID-Token ist an eine bestimmte Kombination von Benutzer und Client gebunden. ID-Token werden bis zu ihrem Ablaufdatum als gültig betrachtet. In der Regel passt eine Webanwendung die Gültigkeitsdauer der Sitzung eines Benutzers in der Anwendung an die Gültigkeitsdauer des für den Benutzer ausgegebenen ID-Tokens an. Sie können die Gültigkeitsdauer eines ID-Tokens anpassen, um zu steuern, wie oft die Webanwendung den Ablauf der Anwendungssitzung veranlasst und wie oft der Benutzer sich mit Microsoft Identity Platform erneut authentifizieren muss (entweder im Hintergrund oder interaktiv).

### <a name="single-sign-on-session-tokens"></a>Sitzungstoken für einmaliges Anmelden
Wenn sich ein Benutzer mit Microsoft Identity Platform authentifiziert, wird eine SSO-Sitzung (Single Sign-On, einmaliges Anmelden) im Browser des Benutzers und bei Microsoft Identity Platform hergestellt. Das SSO-Token stellt diese Sitzung dar (in Form eines Cookies). Das SSO-Sitzungstoken ist nicht an eine bestimmte Ressource/Clientanwendung gebunden. SSO-Sitzungstoken können widerrufen werden, und ihre Gültigkeit wird bei jeder Verwendung überprüft.

Microsoft Identity Platform verwendet zwei Arten von SSO-Sitzungstoken: beständig und nicht beständig. Beständige Sitzungstoken werden vom Browser als beständige Cookies gespeichert. Nicht beständige Sitzungstoken werden als Sitzungscookies gespeichert. (Sitzungscookies werden zerstört, wenn der Browser geschlossen wird.) In der Regel wird ein nicht persistentes Sitzungstoken gespeichert. Wenn der Benutzer jedoch während der Authentifizierung das Kontrollkästchen **Angemeldet bleiben** wählt, wird ein persistentes Sitzungstoken gespeichert.

Nicht beständige Sitzungstoken haben eine Gültigkeitsdauer von 24 Stunden. Beständige Token haben eine Gültigkeitsdauer von 90 Tagen. Jedes Mal, wenn ein SSO-Sitzungstoken innerhalb seiner Gültigkeitsdauer verwendet wird, verlängert sich die Gültigkeitsdauer je nach Tokentyp um weitere 24 Stunden bzw. 90 Tage. Wenn ein SSO-Sitzungstoken innerhalb seiner Gültigkeitsdauer nicht verwendet wird, wird es als abgelaufen erachtet und nicht mehr akzeptiert.

Sie können eine Richtlinie verwenden, um den Zeitpunkt nach dem Ausstellen des ersten Sitzungstokens festzulegen, nach dem das Sitzungstoken nicht mehr akzeptiert wird. (Verwenden Sie hierfür die Eigenschaft „Max. Alter Sitzungstoken“.) Sie können die Gültigkeitsdauer eines Sitzungstokens anpassen, um zu steuern, wann und wie oft ein Benutzer erneut Anmeldeinformationen eingeben muss, anstatt automatisch authentifiziert zu werden, wenn er eine Webanwendung verwendet.

### <a name="token-lifetime-policy-properties"></a>Eigenschaften von Tokengültigkeitsdauer-Richtlinien
Eine Tokengültigkeitsdauer-Richtlinie ist ein Richtlinienobjekt, das Regeln für die Tokengültigkeitsdauer enthält. Verwenden Sie die Eigenschaften der Richtlinie, um angegebene Tokengültigkeitsdauern zu steuern. Wenn keine Richtlinie festgelegt ist, erzwingt das System den Standardwert für die Gültigkeitsdauer.

### <a name="configurable-token-lifetime-properties"></a>Konfigurierbare Eigenschaften der Tokengültigkeitsdauer
| Eigenschaft | Richtlinien-Eigenschaftszeichenfolge | Betrifft | Standard | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Gültigkeitsdauer Zugriffstoken |AccessTokenLifetime<sup>2</sup> |Zugriffstoken, ID-Token, SAML2-Token |1 Stunde |10 Minuten |1 Tag |
| Max. Zeit der Inaktivität für Aktualisierungstoken |MaxInactiveTime |Aktualisierungstoken |90 Tage |10 Minuten |90 Tage |
| Max. Alter Single-Factor-Aktualisierungstoken |MaxAgeSingleFactor |Aktualisierungstoken (für alle Benutzer) |Bis zum Widerruf |10 Minuten |Bis zum Widerruf<sup>1</sup> |
| Max. Alter Multi-Factor-Aktualisierungstoken |MaxAgeMultiFactor |Aktualisierungstoken (für alle Benutzer) | 180 Tage |10 Minuten |180 Tage<sup>1</sup> |
| Max. Alter Single-Factor-Sitzungstoken |MaxAgeSessionSingleFactor |Sitzungstoken (beständig und nicht beständig) |Bis zum Widerruf |10 Minuten |Bis zum Widerruf<sup>1</sup> |
| Max. Alter Multi-Factor-Sitzungstoken |MaxAgeSessionMultiFactor |Sitzungstoken (beständig und nicht beständig) | 180 Tage |10 Minuten | 180 Tage<sup>1</sup> |

* <sup>1</sup>365 Tage ist die explizite Maximallänge, die für diese Attribute festgelegt werden kann.
* <sup>2</sup>Damit der Microsoft Teams-Webclient funktioniert, empfiehlt es sich, „AccessTokenLifetime“ für Microsoft Teams auf mehr als 15 Minuten festzulegen.

### <a name="exceptions"></a>Ausnahmen
| Eigenschaft | Betrifft | Standard |
| --- | --- | --- |
| Maximales Alter des Aktualisierungstokens (für Verbundbenutzer mit unzureichenden Widerrufsinformationen ausgestellt<sup>1</sup>) |Aktualisierungstoken (für Verbundbenutzer mit unzureichenden Widerrufsinformationen ausgestellt<sup>1</sup>) |12 Stunden |
| Max. Zeit der Inaktivität für Aktualisierungstoken (für vertrauliche Clients ausgestellt) |Aktualisierungstoken (für vertrauliche Clients ausgestellt) |90 Tage |
| Max. Alter Aktualisierungstoken (für vertrauliche Clients ausgestellt) |Aktualisierungstoken (für vertrauliche Clients ausgestellt) |Bis zum Widerruf |

* <sup>1</sup> Zu Verbundbenutzern mit unzureichenden Widerrufsinformationen zählen Benutzer, bei denen das Attribut „LastPasswordChangeTimestamp“ nicht synchronisiert wird. Für diese Benutzer gilt dieses kurze maximale Alter, weil Azure Active Directory nicht überprüfen kann, wann an alte Anmeldeinformationen (z. B. ein geändertes Kennwort) gebundene Token widerrufen werden müssen, und häufiger sicherstellen muss, dass es zwischen dem Benutzer und den zugeordneten Token keine Probleme gibt. Zum Verbessern dieses Verfahrens müssen Mandantenadministratoren sicherstellen, dass das Attribut „LastPasswordChangeTimestamp“ synchronisiert wird (dies kann für das Benutzerobjekt über PowerShell oder AADSync festgelegt werden).

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
> Um 12:00 Uhr startet der Benutzer eine neue Browsersitzung und versucht, auf Webanwendung A zuzugreifen. Der Benutzer wird an Microsoft Identity Platform umgeleitet und aufgefordert, sich anzumelden. Es wird ein Cookie erstellt, das über ein Sitzungstoken im Browser verfügt. Der Benutzer wird mit einem ID-Token an die Webanwendung A zurückgeleitet. Dieses ID-Token erlaubt dem Benutzer den Zugriff auf die Anwendung.
>
> Um 12:15 Uhr versucht der Benutzer, auf Webanwendung B zuzugreifen. Der Browser führt die Umleitung an Microsoft Identity Platform durch, wo das Sitzungscookie erkannt wird. Der Dienstprinzipal von Webanwendung B ist mit Tokengültigkeitsdauer-Richtlinie 2 verknüpft, ist aber zugleich Teil der übergeordneten Organisation mit der Tokengültigkeitsdauer-Standardrichtlinie 1. Tokengültigkeitsdauer-Richtlinie 2 wird wirksam, da mit Dienstprinzipalen verknüpfte Richtlinien eine höhere Priorität als Organisationsstandardrichtlinien haben. Das Sitzungstoken wurde innerhalb der letzten 30 Minuten erstmalig ausgegeben, weshalb es als gültig erachtet wird. Der Benutzer wird mit einem ID-Token, das ihm Zugriff gewährt, an die Webanwendung B zurückgeleitet.
>
> Um 13:00 Uhr versucht der Benutzer, auf Webanwendung A zuzugreifen. Er wird an Microsoft Identity Platform umgeleitet. Webanwendung A ist nicht mit Richtlinien verknüpft, aber da sie sich in einer Organisation mit Tokengültigkeitsdauer-Standardrichtlinie 1 befindet, ist diese Richtlinie wirksam. Das Sitzungscookie, das innerhalb der letzten acht Stunden ursprünglich ausgestellt wurde, wird erkannt. Der Benutzer wird automatisch zurück an Webanwendung A mit einem neuen ID-Token umgeleitet. Der Benutzer muss sich nicht authentifizieren.
>
> Unmittelbar danach versucht der Benutzer, auf Webanwendung B zuzugreifen. Er wird an Microsoft Identity Platform umgeleitet. Wie zuvor auch, ist Tokengültigkeitsdauer-Richtlinie 2 wirksam. Da das Token vor mehr als 30 Minuten ausgestellt wurde, wird der Benutzer aufgefordert, die Anmeldeinformationen erneut einzugeben. Ein ganz neues Sitzungstoken und ein ID-Token werden ausgestellt. Nun kann der Benutzer auf Webanwendung B zugreifen.
>
>

## <a name="configurable-policy-property-details"></a>Details zu konfigurierbaren Richtlinieneigenschaften
### <a name="access-token-lifetime"></a>Gültigkeitsdauer Zugriffstoken
**Zeichenfolge:** AccessTokenLifetime

**Betrifft:** Zugriffstoken, ID-Token, SAML-Token

**Zusammenfassung:** Diese Richtlinie steuert, wie lange Zugriffstoken und ID-Token für diese Ressource als gültig angesehen werden. Durch das Reduzieren des Werts für die Eigenschaft „Gültigkeitsdauer Zugriffstoken“ wird das Risiko verringert, dass ein Zugriffstoken oder ID-Token von einem böswilligen Akteur für einen längeren Zeitraum verwendet wird. (Diese Token können nicht widerrufen werden.) Der Nachteil hierbei ist, dass die Leistung beeinträchtigt wird, da die Token häufiger ersetzt werden müssen.

Ein Beispiel finden Sie unter [Erstellen einer Richtlinie für die Webanmeldung](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

### <a name="refresh-token-max-inactive-time"></a>Max. Zeit der Inaktivität für Aktualisierungstoken
**Zeichenfolge:** MaxInactiveTime

**Betrifft:** Aktualisierungstoken

**Zusammenfassung:** Diese Richtlinie steuert, wie alt ein Aktualisierungstoken sein darf, bevor ein Client es nicht mehr verwenden kann, um ein neues Zugriffs-/Aktualisierungstoken-Paar abzurufen, wenn versucht wird, auf diese Ressource zuzugreifen. Da bei Verwendung eines Aktualisierungstokens normalerweise ein neues Aktualisierungstoken zurückgegeben wird, wird mit dieser Richtlinie der Zugriff verhindert, wenn der Client versucht, während des angegeben Zeitraums mit dem aktuellen Aktualisierungstoken auf eine Ressource zuzugreifen.

Durch diese Richtlinie werden Benutzer, die nicht auf ihrem Client aktiv waren, gezwungen, sich erneut zu authentifizieren, um ein neues Aktualisierungstoken abzurufen.

Die Eigenschaft „Maximale Inaktivitätszeit Aktualisierungstoken“ muss auf einen niedrigeren Wert als die Eigenschaften „Maximales Alter Single-Factor-Token“ und „Maximales Alter Multi-Factor-Aktualisierungstoken“ festgelegt werden.

Ein Beispiel finden Sie unter [Erstellen einer Richtlinie für eine native App, die eine Web-API aufruft](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="single-factor-refresh-token-max-age"></a>Max. Alter Single-Factor-Aktualisierungstoken
**Zeichenfolge:** MaxAgeSingleFactor

**Betrifft:** Aktualisierungstoken

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer ein Aktualisierungstoken verwenden kann, um ein neues Zugriffs-/Aktualisierungstoken-Paar abzurufen, nachdem die letzte erfolgreiche Authentifizierung mit einem einzigen Faktor durchgeführt wurde. Nachdem sich ein Benutzer authentifiziert und ein neues Aktualisierungstoken erhalten hat, kann er den Aktualisierungstokenflow für den angegebenen Zeitraum verwenden. (Dies gilt, solange das aktuelle Aktualisierungstoken nicht widerrufen wird und nicht länger als für den Inaktivitätszeitraum ungenutzt bleibt.) An diesem Punkt wird der Benutzer zum erneuten Authentifizieren gezwungen, um ein neues Aktualisierungstoken zu erhalten.

Durch die Reduzierung des maximalen Alters müssen sich Benutzer häufiger authentifizieren. Da die Single-Factor Authentication als weniger sicher als die Multi-Factor Authentication erachtet wird, wird empfohlen, diese Eigenschaft auf einen Wert festzulegen, der kleiner oder gleich dem Wert für die Eigenschaft „Max. Alter Multi-Factor-Aktualisierungstoken“ ist.

Ein Beispiel finden Sie unter [Erstellen einer Richtlinie für eine native App, die eine Web-API aufruft](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="multi-factor-refresh-token-max-age"></a>Max. Alter Multi-Factor-Aktualisierungstoken
**Zeichenfolge:** MaxAgeMultiFactor

**Betrifft:** Aktualisierungstoken

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer ein Aktualisierungstoken verwenden kann, um ein neues Zugriffs-/Aktualisierungstoken-Paar abzurufen, nachdem die letzte erfolgreiche Authentifizierung mit mehreren Faktoren durchgeführt wurde. Nachdem sich ein Benutzer authentifiziert und ein neues Aktualisierungstoken erhalten hat, kann er den Aktualisierungstokenflow für den angegebenen Zeitraum verwenden. (Dies gilt, solange das aktuelle Aktualisierungstoken nicht widerrufen wird und nicht länger als für den Inaktivitätszeitraum ungenutzt bleibt.) An diesem Punkt werden Benutzer gezwungen, sich erneut zu authentifizieren, um ein neues Aktualisierungstoken zu erhalten.

Durch die Reduzierung des maximalen Alters müssen sich Benutzer häufiger authentifizieren. Da die Single-Factor Authentication als weniger sicher als die Multi-Factor Authentication erachtet wird, wird empfohlen, diese Eigenschaft auf einen Wert festzulegen, der größer oder gleich dem Wert für die Eigenschaft „Max. Alter Single-Factor-Aktualisierungstoken“ ist.

Ein Beispiel finden Sie unter [Erstellen einer Richtlinie für eine native App, die eine Web-API aufruft](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="single-factor-session-token-max-age"></a>Max. Alter Single-Factor-Sitzungstoken
**Zeichenfolge:** MaxAgeSessionSingleFactor

**Betrifft:** Sitzungstoken (beständig und nicht beständig)

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer ein Sitzungstoken verwenden kann, um ein neues ID- und Sitzungstoken abrufen, nachdem die letzte erfolgreiche Authentifizierung mit einem einzigen Faktor durchgeführt wurde. Nachdem sich ein Benutzer authentifiziert und ein neues Sitzungstoken erhalten hat, kann er den Sitzungstokenflow für den angegebenen Zeitraum verwenden. (Dies gilt, solange das aktuelle Sitzungstoken nicht widerrufen wird und nicht abgelaufen ist.) Nach dem angegebenen Zeitraum wird der Benutzer gezwungen, sich erneut zu authentifizieren, um ein neues Sitzungstoken zu erhalten.

Durch die Reduzierung des maximalen Alters müssen sich Benutzer häufiger authentifizieren. Da die Single-Factor Authentication als weniger sicher als die Multi-Factor Authentication erachtet wird, wird empfohlen, diese Eigenschaft auf einen Wert festzulegen, der kleiner oder gleich dem Wert für die Eigenschaft „Max. Alter Multi-Factor-Sitzungstoken“ ist.

Ein Beispiel finden Sie unter [Erstellen einer Richtlinie für die Webanmeldung](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

### <a name="multi-factor-session-token-max-age"></a>Max. Alter Multi-Factor-Sitzungstoken
**Zeichenfolge:** MaxAgeSessionMultiFactor

**Betrifft:** Sitzungstoken (beständig und nicht beständig)

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer ein Sitzungstoken verwenden kann, um ein neues ID- und Sitzungstoken abzurufen, nachdem die letzte erfolgreiche Authentifizierung mit mehreren Faktoren durchgeführt wurde. Nachdem sich ein Benutzer authentifiziert und ein neues Sitzungstoken erhalten hat, kann er den Sitzungstokenflow für den angegebenen Zeitraum verwenden. (Dies gilt, solange das aktuelle Sitzungstoken nicht widerrufen wird und nicht abgelaufen ist.) Nach dem angegebenen Zeitraum wird der Benutzer gezwungen, sich erneut zu authentifizieren, um ein neues Sitzungstoken zu erhalten.

Durch die Reduzierung des maximalen Alters müssen sich Benutzer häufiger authentifizieren. Da die Single-Factor Authentication als weniger sicher als die Multi-Factor Authentication erachtet wird, wird empfohlen, diese Eigenschaft auf einen Wert festzulegen, der größer oder gleich dem Wert für die Eigenschaft „Max. Alter Single-Factor-Sitzungstoken“ ist.

## <a name="cmdlet-reference"></a>Cmdlet-Referenz

Dies sind die Cmdlets im [Modul „Azure Active Directory PowerShell für Graph (Vorschau)](/powershell/module/azuread/?view=azureadps-2.0-preview#service-principals&preserve-view=true&preserve-view=true).

### <a name="manage-policies"></a>Verwalten von Richtlinien

Sie können die folgenden Cmdlets zum Verwalten von Richtlinien verwenden.

| Cmdlet | Beschreibung | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Erstellt eine neue Richtlinie. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Ruft alle Azure AD-Richtlinien oder eine angegebene Richtlinie ab. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Ruft alle Apps und Dienstprinzipale ab, die mit einer Richtlinie verknüpft sind. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Aktualisiert eine vorhandene Richtlinie. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Löscht die angegebene Richtlinie. |

### <a name="application-policies"></a>Anwendungsrichtlinien
Sie können die folgenden Cmdlets für Anwendungsrichtlinien verwenden.</br></br>

| Cmdlet | Beschreibung | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Verknüpft die angegebene Richtlinie mit einer Anwendung. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Ruft die Richtlinie ab, die einer Anwendung zugewiesen ist. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Entfernt eine Richtlinie aus einer Anwendung. |

### <a name="service-principal-policies"></a>Dienstprinzipalrichtlinien
Sie können die folgenden Cmdlets für Dienstprinzipalrichtlinien verwenden.

| Cmdlet | Beschreibung | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Verknüpft die angegebene Richtlinie mit einem Dienstprinzipal. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Ruft alle Richtlinien ab, die mit dem angegebenen Dienstprinzipal verknüpft sind.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Entfernt die Richtlinie aus dem angegebenen Dienstprinzipal.|

## <a name="license-requirements"></a>Lizenzanforderungen

Für die Verwendung dieses Features ist eine Azure AD Premium P1-Lizenz erforderlich. Um die richtige Lizenz für Ihre Anforderungen zu ermitteln, lesen Sie [Vergleich der allgemein verfügbaren Features der Editionen Free und Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Kunden mit [Microsoft 365 Business-Lizenzen](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) haben auch Zugriff auf Funktionen für bedingten Zugriff.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Beispiele zum Konfigurieren der Tokengültigkeitsdauer](configure-token-lifetimes.md).