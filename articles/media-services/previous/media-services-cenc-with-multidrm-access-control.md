---
title: Entwerfen eines Inhaltsschutzsystems mit Zugriffssteuerung über Azure Media Services | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Lizenzierung des Microsoft Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: b98b66d8f0350c32e89d62d776ee1288d9271712
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010910"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Entwerfen eines Inhaltsschutzsystems mit Zugriffssteuerung über Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Übersicht

Das Entwerfen und Aufbauen eines DRM-Subsystems (Digital Rights Management) für eine Over-The-Top-Lösung (OTT) oder eine Onlinestreaminglösung ist eine komplexe Aufgabe. Anbieter von Internet- oder Onlinevideodiensten übertragen diese Aufgabe in der Regel spezialisierten DRM-Dienstanbietern. Ziel dieses Dokuments ist es, einen Referenzentwurf und eine Referenzimplementierung eines kompletten DRM-Subsystems in einer OTT- oder Onlinestreaminglösung zu präsentieren.

Zielgruppe sind Entwickler, die an einem DRM-Subsystem für OTT- oder Onlinestreaming-/Multibildschirmlösungen arbeiten, oder Leser, die sich für ein DRM-Subsystem interessieren. Es wird angenommen, dass Leser mit mindestens einer der DRM-Technologien auf dem Markt vertraut sind, wie z.B. PlayReady, Widevine, FairPlay oder Adobe Access.

Der vorliegende Artikel zu DRM bietet auch Informationen zur allgemeinen Verschlüsselung (Common Encryption, CENC) mit Multi-DRM. Bei Onlinestreaming und in der OTT-Branche lässt sich immer mehr der Trend feststellen, dass CENC mit mehreren nativen DRM-Systemen auf verschiedenen Clientplattformen verwendet wird. Dieser Trend löst die vorherige Vorgehensweise ab, bei der ein einzelnes DRM-System und das zugehörige Client-SDK für verschiedene Clientplattformen verwendet wurden. Bei Verwenden von CENC mit mehreren nativen DRM-Systemen sind PlayReady und Widevine gemäß der Spezifikation [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) verschlüsselt.

CENC mit mehreren DRM-Systemen bietet folgende Vorteile:

* Die Kosten für die Verschlüsselung sinken, da ein einzelner Verschlüsselungsprozess für unterschiedliche Zielplattformen mit deren systemeigenen DRM-Systemen ausgeführt wird.
* Die Kosten für die Verwaltung verschlüsselter Medienobjekte sinken, da nur eine einzige Kopie der verschlüsselten Medienobjekte erforderlich ist.
* Die Kosten für die DRM-Clientlizenzierung entfallen, da native DRM-Clients in der Regel auf den entsprechenden nativen Plattformen kostenlos sind.

Microsoft ist zusammen mit einigen anderen wichtigen Unternehmen der Branche ein aktiver Förderer von DASH und CENC. Azure Media Services bietet Unterstützung für DASH und CENC. Neue Ankündigungen finden Sie in den folgenden Blogs:

*  [Ankündigung von Google Widevine-Diensten zur Lizenzbereitstellung in Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services adds Google Widevine packaging for delivering a multi-DRM stream](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) (Azure Media Services bietet jetzt Google Widevine-Paketerstellung für Multi-DRM-Streams)  

### <a name="goals-of-the-article"></a>Schwerpunkte des Artikels

Für diesen Artikel gelten die folgenden Zielsetzungen:

* Bereitstellung eines Referenzentwurfs für DRM-Subsystem unter Verwendung von CENC mit mehreren DRM-Systemen
* Bereitstellung einer Referenzimplementierung auf einer Azure-/Media Services-Plattform
* Erörterung verschiedener Entwurfs- und Implementierungsthemen

In diesem Artikel bezieht sich „mehrere DRM-Systeme“ auf Folgendes:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

Die folgende Tabelle bietet eine Übersicht über die native Plattform, die native App sowie die Browser, die von den einzelnen DRM-Systemen unterstützt werden.

| **Clientplattform** | **Native DRM-Unterstützung** | **Browser/App** | **Streamingformate** |
| --- | --- | --- | --- |
| **Smart-TVs, Operator-STBs, OTT-STBs** |Primär PlayReady und/oder Widevine und/oder andere |Linux, Opera, WebKit und weitere |Verschiedene Formate |
| **Windows 10-Geräte (Windows-PC, Windows-Tablet, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Universelle Windows-Plattform |DASH (für HLS wird PlayReady nicht unterstützt)<br/><br/>DASH, Smooth Streaming (für HLS wird PlayReady nicht unterstützt) |
| **Android-Geräte (Telefon, Tablet, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X-Clients und Apple-TV** |FairPlay |Safari 8+/EME |HLS |

Da der Bereitstellungsstatus der einzelnen DRM-Systeme sehr unterschiedlich sein kann, implementiert ein Dienst in der Regel zwei oder drei DRM-Systeme, um sicherzustellen, dass alle Arten von Endpunkten optimal abgedeckt werden.

Ein Kompromiss zwischen der Komplexität der Dienstlogik und der Komplexität auf der Clientseite sorgt dafür, dass auf den verschiedenen Clients ein gewisses Maß an Benutzerfreundlichkeit erzielt wird.

Beachten Sie die folgenden Aspekte, wenn Sie Ihre Auswahl treffen:

* PlayReady ist auf jedem Windows-Gerät und auf einigen Android-Geräten nativ implementiert und auf nahezu jeder Plattform über Software-SDKs verfügbar.
* Widevine ist auf jedem Android-Gerät, in Chrome und auf einigen anderen Geräten nativ implementiert.
* FairPlay ist nur in iOS und Mac OS-Clients oder über iTunes verfügbar.

Für ein typisches Multi-DRM-System stehen zwei Optionen zur Verfügung:

* PlayReady und Widevine
* PlayReady, Widevine und FairPlay

## <a name="a-reference-design"></a>Ein Referenzentwurf
Dieser Abschnitt zeigt einen Referenzentwurf, für den die für seine Implementierung verwendeten Technologien keine Rolle spielen.

Ein DRM-Subsystem kann die folgenden Komponenten enthalten:

* Schlüsselverwaltung
* DRM-Verpackung
* DRM-Lizenzbereitstellung
* Berechtigungsüberprüfung
* Authentifizierung/Autorisierung
* Player
* Ursprung/Content Delivery Network (CDN)

Das folgende Diagramm veranschaulicht die allgemeine Interaktion zwischen den Komponenten in einem DRM-Subsystem:

![DRM-Subsystem mit CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Der Entwurf weist drei grundlegende Ebenen auf:

* Die Ebene „Backoffice“ (schwarz) wird nicht extern verfügbar gemacht.
* Die Ebene „DMZ“ (dunkelblau) enthält alle Endpunkte, die öffentlich zugänglich sind.
* Die Ebene „Öffentliches Internet“ (hellblau) enthält das CDN und die Player mit Datenverkehr über das öffentliche Internet.

Unabhängig von der Verschlüsselung (statisch oder dynamisch) sollte zudem ein Content Management-Tool zum Steuern des DRM-Schutzes vorhanden sein. Folgendes ist als Eingabe für die DRM-Verschlüsselung möglich:

* MBR-Videoinhalte
* Inhaltsschlüssel
* Lizenzerwerbs-URLs

Während der Wiedergabe gilt folgender allgemeiner Ablauf:

* Der Benutzer wird authentifiziert.
* Für den Benutzer wird ein Autorisierungstoken erstellt.
* Durch DRM geschützte Inhalte (Manifest) werden in den Player heruntergeladen.
* Der Player sendet zusammen mit der Schlüssel-ID und dem Autorisierungstoken eine Lizenzerwerbsanforderung an den Lizenzserver.

Im folgenden Abschnitt wird der Entwurf der Schlüsselverwaltung erläutert.

| **ContentKey-Medienobjekt-Verhältnis** | **Szenario** |
| --- | --- |
| 1:1 |Der einfachste Fall, der zwar die beste Kontrolle bietet, aber im Allgemeinen auch zu den höchsten Kosten für die Lizenzbereitstellung führt. Für jedes geschützte Medienobjekt ist mindestens eine Lizenzanforderung erforderlich. |
| 1:n |Derselbe Inhaltsschlüssel kann für mehrere Assets verwendet werden. Sie können beispielsweise für alle Medienobjekte in einer logischen Gruppe wie einem Genre oder Teilgenre (oder Filmgenre) einen einzigen Inhaltsschlüssel verwenden. |
| n:1 |Für jedes Asset sind mehrere Inhaltsschlüssel erforderlich. <br/><br/>Wenn Sie z.B. dynamischen CENC-Schutz mit mehreren DRM-Systemen für MPEG-DASH und dynamische AES-128-Verschlüsselung für HLS anwenden müssen, benötigen Sie zwei separate Inhaltsschlüssel. Jeder Inhaltsschlüssel benötigt ein eigenes ContentKeyType-Element. (Als Inhaltsschlüssel für den dynamischen CENC-Schutz verwenden Sie ContentKeyType.CommonEncryption. Als Inhaltsschlüssel für den dynamischen AES-128-Schutz verwenden Sie ContentKeyType.EnvelopeEncryption.)<br/><br/>Ein weiteres Beispiel: Zum CENC-Schutz von DASH-Inhalten können Sie einen Inhaltsschlüssel zum Schützen des Videostreams und einen anderen Inhaltsschlüssel zum Schützen des Audiostreams einsetzen. |
| n:n |Eine Kombination der beiden vorherigen Szenarien. Für jedes Medienobjekt in ein und derselben Medienobjektgruppe wird ein einziger Satz von Inhaltsschlüsseln verwendet. |

Ein weiterer wichtiger Faktor ist die Verwendung permanenter und nicht permanenter Lizenzen.

Dieser Faktor ist wichtig,

Wenn Sie eine öffentliche Cloud für die Lizenzbereitstellung nutzen, wirkt sich die Verwendung von permanenten und nicht permanenten Lizenzen direkt auf die Kosten für die Lizenzbereitstellung aus. Die beiden folgenden Entwurfsmodelle veranschaulichen dies:

* Monatliches Abonnement: Verwenden Sie eine permanente Lizenz mit einer 1:n-Zuordnung zwischen Inhaltsschlüssel und Medienobjekt. Beispielsweise wird für alle Kinderfilme ein einziger Inhaltsschlüssel für die Verschlüsselung verwendet. In diesem Fall:

    Gesamtanzahl der für alle Kinderfilme pro Gerät angeforderten Lizenzen = 1

* Monatliches Abonnement: Verwenden Sie eine nicht permanente Lizenz mit einer 1:1-Zuordnung zwischen Inhaltsschlüssel und Medienobjekt. In diesem Fall:

    Gesamtanzahl der für alle Kinderfilme pro Gerät angeforderten Lizenzen = [Anzahl der konsumierten Filme] × [Anzahl der Sitzungen]

Die beiden Entwürfe führen zu sehr unterschiedlichen Mustern für die Lizenzanforderung. Die unterschiedlichen Entwürfe ziehen unterschiedliche Kosten für die Lizenzbereitstellung nach sich, wenn der Lizenzbereitstellungsdienst über eine öffentliche Cloud wie Media Services bereitgestellt wird.

## <a name="map-design-to-technology-for-implementation"></a>Zuordnen des Entwurfs zur Technologie für die Implementierung
Als Nächstes wird der allgemeine Entwurf den Technologien auf der Azure-/Media Services-Plattform zugeordnet, indem angegeben wird, welche Technologie für jeden Baustein verwendet werden soll.

Die folgende Tabelle zeigt die Zuordnung.

| **Baustein** | **Technologie** |
| --- | --- |
| **Player** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identitätsanbieter (Identity Provider, IDP)** |Azure Active Directory (Azure AD) |
| **Sicherheitstokendienst (Security Token Service, STS)** |Azure AD |
| **Workflow des DRM-Schutzes** |Dynamischer Schutz in Media Services |
| **DRM-Lizenzbereitstellung** |* Media Services-Lizenzbereitstellung (PlayReady, Widevine, FairPlay) <br/>* Axinom-Lizenzserver <br/>* Benutzerdefinierter PlayReady-Lizenzserver |
| **Ursprung** |Media Services-Streamingendpunkt |
| **Schlüsselverwaltung** |Für Referenzimplementierung nicht erforderlich |
| **Content Management** |C#-Konsolenanwendung |

Anders gesagt: Sowohl IDP als auch STS werden mit Azure AD verwendet. Für den Player wird die [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/). Sowohl Media Services als auch Media Player unterstützen DASH und CENC mit mehreren DRM-Systemen.

Das folgende Diagramm zeigt die allgemeine Struktur und den allgemeinen Ablauf bei dieser Technologiezuordnung:

![CENC mit Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Zum Einrichten der dynamischen CENC-Verschlüsselung verwendet das Content Management-Tool die folgenden Eingaben:

* Offene Inhalte
* Inhaltsschlüssel aus Schlüsselerstellung/-verwaltung
* Lizenzerwerbs-URLs
* Eine Liste mit Informationen aus Azure AD

Die Ausgabe des Content Management-Tools sieht wie folgt aus:

* „ContentKeyAuthorizationPolicy“ enthält die Angabe, wie die Lizenzbereitstellung ein JWT (JSON-Webtoken) und die DRM-Lizenzspezifikationen überprüft.
* „AssetDeliveryPolicy“ enthält Angaben zu Streamingformat, DRM-Schutz und Lizenzerwerbs-URLs.

So sieht der Ablauf zur Laufzeit aus:

* Bei der Benutzerauthentifizierung wird ein JWT generiert.
* Einer der im JWT enthaltenen Ansprüche ist ein Gruppenanspruch, der die Gruppenobjekt-ID „EntitledUserGroup“ enthält. Dieser Anspruch wird verwendet, um die Berechtigungsüberprüfung zu bestehen.
* Der Player lädt das Clientmanifest von über CENC geschützten Inhalten herunter und identifiziert folgende Elemente:
   * Schlüssel-ID
   * Informationen zum Schutz des Inhalts durch CENC
   * Lizenzerwerbs-URLs
* Der Player sendet eine Lizenzerwerbsanforderung basierend auf dem unterstützten Browser/DRM-System. In der Lizenzerwerbsanforderung werden auch die Schlüssel-ID und das JWT übermittelt. Der Lizenzbereitstellungsdienst überprüft das JWT und die enthaltenen Ansprüche, bevor die benötige Lizenz ausgestellt wird.

## <a name="implementation"></a>Implementierung
### <a name="implementation-procedures"></a>Implementierungsverfahren
Die Implementierung umfasst die folgenden Schritte:

1. Bereiten Sie Testmedienobjekte vor. Codieren/packen Sie ein Testvideo in einer fragmentierten MP4-Datei mit mehreren Bitraten in Media Services. Dieses Medienobjekt ist *nicht* durch DRM geschützt. Der DRM-Schutz erfolgt später dynamisch.

2. Erstellen Sie eine Schlüssel-ID und einen Inhaltsschlüssel (optional aus einem Schlüsselausgangswert). An dieser Stelle wird das Schlüsselverwaltungssystem nicht benötigt, da für einige wenige Testmedienobjekte nur eine einzige Schlüssel-ID und ein einziger Inhaltsschlüssel erforderlich sind.

3. Verwenden Sie die Media Services-API, um für das Testmedienobjekt Lizenzbereitstellungsdienste für mehrere DRM-Systeme zu konfigurieren. Wenn Sie anstelle der Lizenzdienste in Media Services benutzerdefinierte Lizenzserver verwenden, die von Ihrem Unternehmen oder einem Partner Ihres Unternehmens bereitgestellt wurden, können Sie diesen Schritt überspringen. In diesem Fall können Sie Lizenzerwerbs-URLs beim Konfigurieren der Lizenzbereitstellung angeben. Die Media Services-API ist erforderlich, um einige detaillierte Konfigurationsoptionen anzugeben, z.B. die Einschränkung von Autorisierungsrichtlinien sowie Lizenzbereitstellungsvorlagen für verschiedene DRM-Lizenzdienste. Das Azure-Portal bietet derzeit nicht die für diese Konfiguration erforderliche Benutzeroberfläche. Informationen und Beispielcode für die API finden Sie unter [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-playready-widevine.md).

4. Konfigurieren Sie mithilfe der Media Services-API die Bereitstellungsrichtlinie für das Testmedienobjekt. Informationen und Beispielcode für die API finden Sie unter [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-playready-widevine.md).

5. Erstellen und konfigurieren Sie einen Azure AD-Mandanten in Azure.

6. Erstellen Sie einige Benutzerkonten und Gruppen in Ihrem Azure AD-Mandanten. Erstellen Sie mindestens eine Gruppe mit berechtigten Benutzern, und fügen Sie dieser Gruppe einen Benutzer hinzu. Benutzer in dieser Gruppe bestehen die Berechtigungsüberprüfung beim Lizenzerwerb. Benutzer, die sich nicht in dieser Gruppe befinden, bestehen die Authentifizierungsprüfung nicht und können keine Lizenz erwerben. Die Mitgliedschaft in dieser Gruppe mit berechtigten Benutzern ist ein erforderlicher Gruppenanspruch im von Azure AD ausgestellten JWT. Sie geben diese Anspruchsanforderung in dem Schritt an, in dem Sie die Dienste zur Bereitstellung mehrerer DRM-Lizenzen konfigurieren.

7. Erstellen Sie eine ASP.NET MVC-App, um Ihren Videoplayer zu hosten. Diese ASP.NET-App wird per Benutzerauthentifizierung beim Azure AD-Mandanten geschützt. Nach der Benutzerauthentifizierung bezogene Zugriffstoken enthalten ordnungsgemäße Ansprüche. Für diesen Schritt empfiehlt sich die OpenID Connect-API. Installieren Sie die folgenden NuGet-Pakete:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Erstellen Sie mithilfe der [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/) einen Player. Verwenden Sie die [ProtectionInfo-API von Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/), um anzugeben, welche DRM-Technologie auf unterschiedlichen DRM-Plattformen verwendet werden soll.

9. Die folgende Tabelle zeigt die Zuordnung für das Testsystem.

    | **DRM-System** | **Browser** | **Ergebnis für berechtigten Benutzer** | **Ergebnis für nicht berechtigten Benutzer** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge oder Internet Explorer 11 unter Windows 10 |Erfolg |Fehler |
    | **Widevine** |Chrome, Firefox, Opera |Erfolg |Fehler |
    | **FairPlay** |Safari unter macOS      |Erfolg |Fehler |
    | **AES-128** |Meisten modernen Browser  |Erfolg |Fehler |

Informationen zum Einrichten von Azure AD für einen ASP.NET MVC-Player finden Sie unter [Integrate an Azure Media Services OWIN MVC-based app with Azure Active Directory and restrict content key delivery based on JWT claims](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/) (Integrieren einer Azure Media Services-OWIN MVC-basierten App in Azure Active Directory und Einschränken der Übermittlung von Inhaltsschlüsseln auf Grundlage von JWT-Ansprüchen).

Weitere Informationen finden Sie unter [JWT token authentication in Azure Media Services and dynamic encryption](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/) (JWT-Authentifizierung in Azure Media Services und dynamische Verschlüsselung).  

Informationen zu Azure AD:

* Informationen für Entwickler bietet das [Entwicklerhandbuch zu Azure Active Directory](../../active-directory/azuread-dev/v1-overview.md).
* Informationen für Administratoren finden Sie unter [Verwalten Ihres Azure AD-Verzeichnisses](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Mögliche Probleme bei der Implementierung
Falls bei der Implementierung Probleme auftreten, verwenden Sie die folgenden Informationen zur Behandlung dieser Probleme.

* Die Aussteller-URL muss auf „/“ enden. Die Zielgruppe muss die Anwendungsclient-ID des Players sein. Fügen Sie hier ebenfalls einen Schrägstrich („/“) am Ende der Aussteller-URL ein.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    Im [JWT-Decoder](http://jwt.calebb.net/) sollten **aud** und **iss** wie im JWT angezeigt werden:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Fügen Sie in Azure AD (auf der Registerkarte **Konfigurieren** der Anwendung) Berechtigungen für die Anwendung hinzu. Berechtigungen sind für jede Anwendung erforderlich, sowohl für lokale als auch für bereitgestellte Versionen.

    ![Berechtigungen](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Verwenden Sie den richtigen Aussteller, wenn Sie den dynamischen CENC-Schutz einrichten.

    ```xml
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    ```

    Folgendes funktioniert nicht:

    ```xml
    <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    ```

    Bei der GUID handelt es sich um die Azure AD-Mandanten-ID. Sie finden die GUID im Azure-Portal im Popupmenü **Endpunkte**.

* Erteilen Sie Berechtigungen in Form von Gruppenmitgliedschaftsansprüchen. Vergewissern Sie sich, dass die Azure AD-Anwendungsmanifestdatei Folgendes enthält: 

    "groupMembershipClaims": "All"    (Standardwert ist „null“)

* Legen Sie beim Erstellen der Einschränkungsanforderungen den richtigen TokenType fest.

    ```csharp
    objTokenRestrictionTemplate.TokenType = TokenType.JWT;
    ```

    Da Sie zusätzlich zu SWT (ACS) Unterstützung für JWT (Azure AD) hinzufügen, ist „TokenType.JWT“ der standardmäßige TokenType. Wenn Sie SWT/ACS verwenden, müssen Sie „TokenType.SWT“ festlegen.

## <a name="additional-topics-for-implementation"></a>Zusätzliche Themen für die Implementierung
Dieser Abschnitt beleuchtet einige weitere Themen in Zusammenhang mit Entwurf und Implementierung.

### <a name="http-or-https"></a>HTTP oder HTTPS?
Die ASP.NET MVC-Playeranwendung muss Folgendes unterstützen:

* Die Benutzerauthentifizierung in Azure AD, die über HTTPS erfolgen muss.
* Den JWT-Austausch zwischen Client und Azure AD, der über HTTPS erfolgen muss.
* Den DRM-Lizenzerwerb durch den Client, der über HTTPS erfolgen muss, wenn Lizenzen von Media Services bereitgestellt werden. Bei der PlayReady-Produktsuite ist HTTPS für die Lizenzbereitstellung nicht zwingend vorgegeben. Wenn Ihr PlayReady-Lizenzserver sich außerhalb von Media Services befindet, können Sie HTTP oder HTTPS verwenden.

Die ASP.NET-Playeranwendung verwendet HTTPS als Best Practice, daher befindet der Media Player sich auf einer HTTPS-Seite. Für das Streaming wird jedoch HTTP bevorzugt, daher müssen Sie das Problem gemischter Inhalte berücksichtigen.

* Der Browser lässt keine gemischten Inhalte zu. Plug-Ins wie Silverlight und OSMF für Smooth Streaming und DASH lassen diese jedoch zu. Gemischte Inhalte sind ein Sicherheitsproblem, da sie die Möglichkeit eröffnen, dass schadhafter JavaScript-Code eingeschleust wird, wodurch Kundendaten gefährdet werden können. Browser blockieren diese Funktion standardmäßig. Die einzige Möglichkeit, dieses Problem zu begehen, besteht darin, auf Server- bzw. Ursprungsseite alle Domänen zuzulassen (egal ob HTTPS oder HTTP). Dies ist aber wahrscheinlich auch nicht sehr sinnvoll.
* Vermeiden Sie gemischte Inhalte. Sowohl die Playeranwendung als auch Media Player sollten HTTP oder HTTPS verwenden. Wenn Sie gemischte Inhalte wiedergeben, erfordert die Smooth Streaming-Technologie von Silverlight das Deaktivieren einer Warnung zu gemischten Inhalten. Die Smooth Streaming-Technologie von Adobe Flash verarbeitet gemischte Inhalte hingegen ohne entsprechende Warnung.
* Wenn Ihr Streamingendpunkt vor August 2014 erstellt wurde, wird HTTPS nicht unterstützt. Erstellen und verwenden Sie in diesem Fall einen neuen Streamingendpunkt für HTTPS.

In der Referenzimplementierung für durch DRM geschützte Inhalte erfolgen die Ausführung der Anwendung und des Streamings in HTTPS. Für offene Inhalte erfordert der Player keine Authentifizierung oder Lizenz, sodass Sie HTTP oder HTTPS verwenden können.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory-Rollover von Signaturschlüsseln
Das Rollover von Signaturschlüsseln ist ein wichtiger Punkt, den Sie bei Ihrer Implementierung berücksichtigen müssen. Wenn Sie diesen Punkt ignorieren, stellt das System spätestens nach sechs Wochen vollständig die Arbeit ein.

Azure AD verwendet Branchenstandards zum Einrichten einer Vertrauensstellung zwischen sich selbst und Anwendungen, die Azure AD verwenden. Azure AD verwendet einen Signaturschlüssel, der aus einem Paar mit einem öffentlichen und einem privaten Schlüssel besteht. Wenn Azure AD ein Sicherheitstoken erstellt, das Informationen über den Benutzer enthält, wird dieses Token von Azure AD mithilfe eines privaten Schlüssels signiert, bevor es zurück an die Anwendung gesendet wird. Um sicherzustellen, dass das Token gültig ist und aus Azure AD stammt, muss die Anwendung die Signatur des Tokens überprüfen. Die Anwendung verwendet einen öffentlichen Schlüssel, der von Azure AD verfügbar gemacht wurde und im Dokument mit den Verbundmetadaten des Mandanten enthalten ist. Dieser öffentliche Schlüssel – und der Signaturschlüssel, aus dem er abgeleitet ist –, ist für alle Mandanten in Azure AD gleich.

Weitere Informationen zum Azure AD-Schlüsselrollover finden Sie im Dokument [Rollover von Signaturschlüsseln in Azure Active Directory](../../active-directory/develop/active-directory-signing-key-rollover.md).

Im [Schlüsselpaar aus öffentlichem und privatem Schlüssel](https://login.microsoftonline.com/common/discovery/keys/) gilt Folgendes:

* Der private Schlüssel wird von Azure AD verwendet, um ein JWT zu generieren.
* Der öffentliche Schlüssel wird von einer Anwendung wie den DRM-Lizenzbereitstellungsdiensten in Media Services verwendet, um das JWT zu überprüfen.

Aus Sicherheitsgründen rotiert Azure AD das Zertifikat in regelmäßigen Abständen (alle sechs Wochen). Im Fall von Sicherheitslücken kann das Schlüsselrollover jederzeit erfolgen. Aus diesem Grund müssen die Lizenzbereitstellungsdienste in Media Services den verwendeten öffentlichen Schlüssel aktualisieren, sobald Azure AD das Schlüsselpaar rotiert. Andernfalls kann keine Tokenauthentifizierung in Media Services erfolgen, und es wird keine Lizenz ausgestellt.

Um diesen Dienst einzurichten, legen Sie beim Konfigurieren der DRM-Lizenzbereitstellungsdienste „TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument“ fest.

Hier sehen Sie den JWT-Ablauf:

* Azure AD stellt das JWT mit dem aktuellen privaten Schlüssel für einen authentifizierten Benutzer aus.
* Wenn ein Player mit CENC und mehreren DRM-Systemen geschützte Inhalte erkennt, wird zuerst das von Azure AD ausgestellte JWT gesucht.
* Der Player sendet die Lizenzerwerbsanforderung mit dem JWT an die Lizenzbereitstellungsdienste in Media Services.
* Die Lizenzbereitstellungsdienste in Media Services verwenden den aktuell gültigen öffentlichen Schlüssel aus Azure AD, um das JWT zu überprüfen, bevor Lizenzen ausgestellt werden.

Die DRM-Lizenzbereitstellungsdienste suchen immer nach dem aktuell gültigen öffentlichen Schlüssel aus Azure AD. Der von Azure AD angebotene öffentliche Schlüssel ist der Schlüssel, mit dem ein von Azure AD ausgestelltes JWT überprüft wird.

Was passiert, wenn das Schlüsselrollover ausgeführt wird, nachdem Azure AD ein JWT generiert hat, jedoch bevor das JWT von Playern zur Überprüfung an DRM-Lizenzbereitstellungsdienste in Media Services zur Überprüfung gesendet wird?

Da ein Schlüsselrollover jederzeit erfolgen kann, steht im Dokument mit den Verbundmetadaten immer mehr als ein gültiger öffentlicher Schlüssel zur Verfügung. Die Media Services-Lizenzbereitstellung kann jeden der im Dokument angegebenen Schlüssel verwenden. Da für einen Schlüssel möglicherweise bald ein Rollover ausgeführt wird, dient der andere als Ersatz.

### <a name="where-is-the-access-token"></a>Wo befindet sich das Zugriffstoken?
Wenn Sie sich unter [Anwendungsidentität mit OAuth 2.0-Clientanmeldeinformationen](../../active-directory/azuread-dev/web-api.md) ansehen, wie eine Web-App eine API-App aufruft, ist der Ablauf der Authentifizierung wie folgt:

* Ein Benutzer meldet sich in der Webanwendung bei Azure AD an. Weitere Informationen finden Sie unter [Webbrowser zu Webanwendung](../../active-directory/azuread-dev/web-app.md).
* Der Azure AD-Autorisierungsendpunkt leitet den Benutzer-Agent mit einem Autorisierungscode zurück zur Clientanwendung. Der Benutzer-Agent gibt den Autorisierungscode an den Umleitungs-URI der Clientanwendung zurück.
* Die Webanwendung muss ein Zugriffstoken abrufen, damit sie sich gegenüber der Web-API authentifizieren und die gewünschte Ressource abrufen kann. Sie sendet eine Anforderung an den Azure AD-Tokenendpunkt und gibt die Anmeldeinformationen, die Client-ID sowie den Anwendungs-ID-URI der Web-API an. Sie legt den Autorisierungscode vor, um zu belegen, dass der Benutzer zugestimmt hat.
* Azure AD authentifiziert die Anwendung und gibt ein JWT-Zugriffstoken zurück, das zum Aufrufen der Web-API verwendet wird.
* Die Webanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Dann überprüft die API das JWT. Wenn die Überprüfung erfolgreich verläuft, gibt sie die gewünschte Ressource zurück.

In diesem Anwendungsidentitätsablaufvertraut die Web-API darauf, dass die Webanwendung den Benutzer authentifiziert hat. Aus diesem Grund wird dieses Modell als vertrauenswürdiges Subsystem bezeichnet. Das [Diagramm des Autorisierungsablaufs](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) beschreibt, wie das Gewähren des Autorisierungscodes abläuft.

Der Lizenzerwerb mit Tokeneinschränkung folgt demselben Muster mit vertrauenswürdigem Subsystem. Der Lizenzbereitstellungsdienst in Media Services ist die Web-API-Ressource, d.h. die Back-End-Ressource, auf die eine Webanwendung zugreifen muss. Wo befindet sich also das Zugriffstoken?

Das Zugriffstoken wird aus Azure AD abgerufen. Nach erfolgreicher Benutzerauthentifizierung wird ein Autorisierungscode zurückgegeben. Der Autorisierungscode wird anschließend zusammen mit der Client-ID und dem App-Schlüssel zum Austausch für das Zugriffstoken verwendet. Das Zugriffstoken dient zum Zugriff auf eine „Zeigeranwendung“, die auf den Media Services-Lizenzbereitstellungsdienst zeigt bzw. diesen darstellt.

Um die Zeiger-App in Azure AD zu registrieren und zu konfigurieren, führen Sie folgende Schritte aus:

1. Gehen Sie im Azure AD-Mandanten so vor:

   * Fügen Sie eine Anwendung (Ressource) mit der folgenden Anmelde-URL hinzu: https://[Ressourcenname].azurewebsites.net/. 
   * Fügen Sie eine App-ID mit der folgenden URL hinzu: https://[AAD-Mandantenname].onmicrosoft.com/[Ressourcenname].

2. Fügen Sie einen neuen Schlüssel für die Ressourcen-App hinzu.

3. Aktualisieren Sie die Manifestdatei der App so, dass die groupMembershipClaims-Eigenschaft folgenden Wert aufweist: "groupMembershipClaims": "All".

4. Fügen Sie in der Azure AD-App, die auf die Player-Web-App zeigt, im Abschnitt **Berechtigungen für andere Anwendungen** die Ressourcen-App hinzu, die zuvor in Schritt 1 hinzugefügt wurde. Wählen Sie unter **Delegierte Berechtigung** die Option **Auf [Ressourcenname] zugreifen** aus. Mit dieser Option erhält die Web-App die Berechtigung zum Erstellen von Zugriffstoken für den Zugriff auf die Ressourcen-App. Dies muss sowohl für die lokale als auch für die bereitgestellte Version der Web-App erfolgen, wenn Sie mit Visual Studio und der Azure-Web-App entwickeln.

Das von Azure AD ausgestellte JWT ist das Zugriffstoken, das für den Zugriff auf die Zeigerressource verwendet wird.

### <a name="what-about-live-streaming"></a>Was gilt für Livestreaming?
In den bisherigen Abschnitten ging es um bedarfsgesteuerte Medienobjekte. Was gilt für Livestreaming?

Sie können genau den gleichen Entwurf und die gleiche Implementierung zum Schutz von Livestreams in Media Services verwenden, indem Sie das Medienobjekt, das einem Programm zugeordnet ist, als VOD-Medienobjekt (Video On Demand) behandeln.

Um Livestreaming in Media Services zu ermöglichen, müssen Sie zuerst einen Kanal und dann ein Programm in diesem Kanal erstellen. Um das Programm zu erstellen, müssen Sie ein Medienobjekt erstellen, das das Livearchiv für das Programm enthält. Um Live-Inhalte mit CENC und mehreren DRM-Systemen zu schützen, wenden Sie vor dem Start des Programms dieselben Einrichtungs- und Verarbeitungsschritte wie bei einem VOD-Medienobjekt an.

### <a name="what-about-license-servers-outside-media-services"></a>Was gilt für Lizenzserver außerhalb von Media Services?
In vielen Fällen haben Kunden in eine Lizenzserverfarm investiert, die sich entweder in ihrem eigenen Rechenzentrum befindet oder von DRM-Dienstanbietern gehostet wird. Mit Media Services Content Protection können Sie im Hybridmodus arbeiten. Inhalte können in Media Services gehostet und dynamisch geschützt werden, während DRM-Lizenzen von Servern außerhalb von Media Services bereitgestellt werden. Berücksichtigen Sie in diesem Fall die folgenden Änderungen:

* Der Sicherheitstokendienst (Security Token Service, STS) muss Token ausstellen, die zulässig sind und von der Lizenzserverfarm überprüft werden können. Beispielsweise erfordern die von Axinom bereitgestellten Widevine-Lizenzserver ein spezifisches JWT, das eine Berechtigungsnachricht enthält. Aus diesem Grund benötigen Sie einen Sicherheitstokendienst, um ein solches JWT auszustellen. Weitere Informationen zu dieser Art der Implementierung finden Sie in der [Azure-Dokumentation](https://azure.microsoft.com/documentation/) und unter [Bereitstellen von Widevine-Lizenzen für Azure Media Services mithilfe von Axinom](media-services-axinom-integration.md).
* Sie müssen den Lizenzbereitstellungsdienst (ContentKeyAuthorizationPolicy) nicht mehr in Media Services konfigurieren. Sie müssen die Lizenzerwerbs-URLs (für PlayReady, Widevine und FairPlay) bei der Konfiguration von „AssetDeliveryPolicy“ während der Einrichtung von CENC mit mehreren DRM-Systemen bereitstellen.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Was geschieht, wenn ich einen benutzerdefinierten STS verwenden möchte?
Möglicherweise möchte ein Kunde einen benutzerdefinierten Sicherheitstokendienst verwenden, um JWTs bereitzustellen. Hierfür kann es folgende Gründe geben:

* Der vom Kunden verwendete IDP-Dienst unterstützt den Sicherheitstokendienst nicht. In diesem Fall kann ein benutzerdefinierter Sicherheitstokendienst eine mögliche Option sein.
* Der Kunde benötigt möglicherweise eine flexiblere oder strengere Kontrolle bei der Integration des Sicherheitstokendiensts in das Abrechnungssystem des Kunden für Abonnenten. Ein MVPD-Anbieter (Multichannel Video Programming Distributor) stellt ggf. mehrere OTT-Abonnentenpakete bereit, z.B. „Premium“, „Standard“, „Sport“ usw. Der Programmanbieter möchte die Ansprüche in einem Token mit dem Paket eines Abonnenten abgleichen, sodass nur die Inhalte in einem bestimmten Paket zur Verfügung gestellt werden. In diesem Fall bietet ein benutzerdefinierter STS die erforderliche Flexibilität und Kontrolle.

Wenn Sie einen benutzerdefinierten Sicherheitstokendienst verwenden, müssen Sie zwei Änderungen vornehmen:

* Wenn Sie den Lizenzbereitstellungsdienst für ein Medienobjekt konfigurieren, müssen Sie anstelle des aktuellen Schlüssels aus Azure AD Sicherheitsschlüssel angeben, der vom benutzerdefinierten Sicherheitstokendienst zur Überprüfung verwendet wird. (Weitere Informationen folgen.) 
* Wenn ein JWT generiert wird, wird anstelle des privaten Schlüssels des aktuellen X.509-Zertifikats in Azure AD ein Sicherheitsschlüssel angegeben.

Es gibt zwei Arten von Sicherheitsschlüsseln:

* Symmetrischer Schlüssel: Um ein JWT zu generieren und zu überprüfen, wird ein und derselbe Schlüssel verwendet.
* Asymmetrischer Schlüssel: Ein Paar aus öffentlichem und privatem Schlüssel in einem X.509-Zertifikat wird mit einem privaten Schlüssel verwendet, um ein JWT zu verschlüsseln und zu generieren. Das gleiche Paar wird mit dem öffentlichen Schlüssel verwendet, um das Token zu überprüfen.

> [!NOTE]
> Bei Verwendung von .NET Framework/C# als Entwicklungsplattform muss das X.509-Zertifikat für einen asymmetrischen Sicherheitsschlüssel eine Schlüssellänge von mindestens 2048 aufweisen. Dies ist eine Voraussetzung für die „System.IdentityModel.Tokens.X509AsymmetricSecurityKey“--Klasse in .NET Framework. Andernfalls wird die folgende Ausnahme ausgelöst:
> 
> IDX10630: "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" für die Signierung darf nicht kleiner als 2.048s Bit sein.

## <a name="the-completed-system-and-test"></a>Das fertige System und Tests
Dieser Abschnitt beschreibt die folgenden Szenarien im fertig gestellten, vollständigen System, sodass Sie sich ein grundlegendes Bild des Verhaltens machen können, bevor Sie ein Anmeldekonto erhalten:

* Wenn Sie ein nicht integriertes Szenario benötigen:

    * Bei in Media Services gehosteten Videomedienobjekten, die entweder nicht geschützt oder per DRM, aber ohne Tokenauthentifizierung geschützt sind (hierbei wird eine Lizenz an jeden Anforderer ausgestellt), können Sie die Tests ohne Anmeldung durchführen. Wechseln Sie HTTP, wenn Ihr Videostreaming über HTTP erfolgt.

* Wenn Sie ein umfassendes, integriertes Szenario benötigen:

    * Bei Videomedienobjekten mit dynamischem DRM-Schutz in Media Services, bei denen die Tokenauthentifizierung und JWT-Generierung in Azure AD erfolgt, müssen Sie sich anmelden.

Informationen zur Player-Webanwendung und den entsprechenden Anmeldevoraussetzungen finden Sie [auf dieser Website](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Benutzeranmeldung
Um das vollständige, integrierte DRM-System zu testen, müssen Sie ein Konto erstellen oder hinzufügen.

Welches Konto wird benötigt?

Azure erlaubte ursprünglich nur den Zugriff durch Benutzer mit Microsoft-Konten. Inzwischen ist der Zugriff aus beiden Systemen zulässig. Alle Azure-Eigenschaften vertrauen bei der Authentifizierung jetzt Azure AD, und Azure AD authentifiziert Organisationsbenutzer. Es wurde eine Verbundbeziehung erstellt, bei der Azure AD das Endbenutzeridentitätssystem für Microsoft-Konten als vertrauenswürdig für die Authentifizierung der Endbenutzer einstuft. Daher kann Azure AD Microsoft-Gastkonten ebenso authentifizieren wie native Azure AD-Konten.

Da Azure AD der Microsoft-Kontodomäne vertraut, können Sie jedes Konto aus den folgenden Domänen zum benutzerdefinierten Azure AD-Mandanten hinzufügen und für die Anmeldung verwenden:

| **Domänenname** | **Domäne** |
| --- | --- |
| **Benutzerdefinierte Azure AD-Mandantendomäne** |somename.onmicrosoft.com |
| **Unternehmensdomäne** |microsoft.com |
| **Microsoft-Kontodomäne** |outlook.com, live.com, hotmail.com |

Sie können einen der Anbieter kontaktieren und bitten, ein Konto für Sie zu erstellen oder hinzuzufügen.

Die folgenden Screenshots zeigen verschiedene Anmeldeseiten, die von verschiedenen Domänenkonten verwendet werden:

**Benutzerdefiniertes Azure AD-Mandantendomänenkonto**: Die angepasste Anmeldeseite der benutzerdefinierten Azure AD-Mandantendomäne.

![Screenshot, der die angepasste Anmeldeseite der benutzerdefinierten Azure AD-Mandantendomäne zeigt.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-Domänenkonto mit Smartcard**: Die von der IT-Abteilung von Microsoft angepasste Anmeldeseite mit zweistufiger Authentifizierung.

![Screenshot, der die von der IT-Abteilung von Microsoft angepasste Anmeldeseite mit zweistufiger Authentifizierung zeigt.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-Konto**: Die Anmeldeseite des Microsoft-Kontos für Endbenutzer.

![Benutzerdefiniertes Azure AD-Mandantendomänenkonto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Verwenden von Encrypted Media Extensions für PlayReady
In einem modernen Browser mit Encrypted Media Extensions (EME) für die PlayReady-Unterstützung, z.B. Internet Explorer 11 unter Windows 8.1 oder höher sowie Microsoft Edge unter Windows 10, ist PlayReady das zugrunde liegende DRM-System für EME.

![Verwenden von EME für PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Der dunkle Playerbereich ist darauf zurückzuführen, dass der PlayReady-Schutz verhindert, dass eine Bildschirmaufnahme von geschützten Videoinhalten erstellt wird.

Der folgende Screenshot zeigt die Player-Plug-Ins sowie die Unterstützung für Microsoft Security Essentials (MSE) und EME:

![Player-Plug-Ins für PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME in Microsoft Edge und Internet Explorer 11 unter Windows 10 erlaubt den Aufruf von [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) auf Windows 10-Geräten, die dies unterstützen. PlayReady SL3000 ermöglicht erweiterte Premiuminhalte (4K, HDR) und neue Modelle für die Inhaltsbereitstellung (für erweiterte Inhalte).

Bei Windows-Geräten ist PlayReady das einzige in die Hardware integrierte DRM-System, das auf Windows-Geräten verfügbar ist (PlayReady SL3000). Ein Streamingdienst kann PlayReady über EME oder über eine UWP-Anwendung (Universelle Windows-Plattform) nutzen und mit PlayReady SL3000 eine höhere Videoqualität als mit anderen DRM-Systemen bieten. Üblicherweise werden Inhalte bis 2K über Chrome oder Firefox und Inhalte bis 4K über Microsoft Edge/Internet Explorer 11 oder eine UWP-Anwendung auf dem gleichen Gerät gestreamt. Die Menge richtet sich nach Diensteinstellungen und Implementierung.

#### <a name="use-eme-for-widevine"></a>Verwenden von EME für Widevine
In einem modernen Browser mit EME/Widevine-Unterstützung, z. B. Chrome 41 und höher unter Windows 10, Windows 8.1, Mac OSX Yosemite und Chrome unter Android 4.4.4, ist Google Widevine das DRM-System hinter EME.

![Verwenden von EME für Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine verhindert nicht, dass Bildschirmaufnahmen von geschützten Videoinhalten erstellt werden.

![Player-Plug-Ins für Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Nicht berechtigte Benutzer
Wenn ein Benutzer kein Mitglied der Gruppe der berechtigten Benutzer ist, besteht er die Berechtigungsüberprüfung nicht. Der Lizenzdienst für mehrere DRM-Systeme stellt die angeforderte Lizenz nicht aus, wie unten gezeigt. In der Beschreibung wird angezeigt, dass die Lizenz nicht erfolgreich erworben werden konnte. Dies ist das gewünschte Verhalten.

![Nicht berechtigte Benutzer](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Ausführen einen benutzerdefinierten Sicherheitstokendiensts
Wenn Sie einen benutzerdefinierten Sicherheitstokendienst ausführen, wird das JWT von diesem Dienst entweder über einen symmetrischen oder einen asymmetrischen Schlüssel ausgestellt.

Der folgende Screenshot zeigt ein Szenario mit einem symmetrischen Schlüssel (in Chrome):

![Benutzerdefinierter Sicherheitstokendienst mit symmetrischem Schlüssel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Der folgende Screenshot zeigt ein Szenario mit einem asymmetrischen Schlüssel über ein X.509-Zertifikat (in einem modernen Microsoft-Browser):

![Benutzerdefinierter Sicherheitstokendienst mit asymmetrischem Schlüssel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

In beiden oben genannten Fällen ist die Benutzerauthentifizierung die gleiche. Sie erfolgt über Azure AD. Der einzige Unterschied ist, dass JWTs vom benutzerdefinierten Sicherheitstokendienst anstatt von Azure AD ausgestellt werden. Beim Konfigurieren des dynamischen CENC-Schutzes bestimmt die Einschränkung des Lizenzbereitstellungsdiensts, welche Art JWT verwendet wird: entweder ein symmetrischer oder ein asymmetrischer Schlüssel.

## <a name="summary"></a>Zusammenfassung

Dieses Dokument erläuterte CENC mit mehreren nativen DRM-Systemen und Zugriffssteuerung per Tokenauthentifizierung. Zudem wurden der Entwurf und die Implementierung mithilfe von Azure, Media Services und Azure Media Player beschrieben.

* Es wurde ein Referenzentwurf vorgestellt, der alle in einem DRM-/CENC-Subsystem benötigten Komponenten enthält.
* Ebenfalls vorgestellt wurde eine Referenzimplementierung in Azure, Media Services und Media Player.
* Darüber hinaus wurden verschiedene Aspekte mit direktem Bezug auf Entwurf und Implementierung behandelt.

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
