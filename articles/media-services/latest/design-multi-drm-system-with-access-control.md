---
title: Multi-DRM-Inhaltsschutzsystem
description: In diesem Artikel erhalten Sie eine detaillierte Beschreibung, wie Sie ein Multi-DRM-Inhaltsschutzsystem mit Azure Media Services entwerfen.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: e0104dd2761f74fbd84486aebbf8c3c4e128eb08
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954900"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Entwurf eines Multi-DRM-Inhaltsschutzsystems mit Zugriffssteuerung

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Das Entwerfen und Erstellen eines DRM-Subsystems (Digital Rights Management) für eine Over-The-Top-Lösung (OTT) oder eine Onlinestreaminglösung ist eine komplexe Aufgabe. Anbieter von Internet- oder Onlinevideodiensten übertragen diese Aufgabe in der Regel spezialisierten DRM-Dienstanbietern. Ziel dieses Dokuments ist es, einen Referenzentwurf und eine Referenzimplementierung eines kompletten DRM-Subsystems in einer OTT- oder Onlinestreaminglösung zu präsentieren.

Zielgruppe sind Entwickler, die an einem DRM-Subsystem für OTT- oder Onlinestreaming-/Multibildschirmlösungen arbeiten, oder Leser, die sich für ein DRM-Subsystem interessieren. Es wird angenommen, dass Leser mit mindestens einer der DRM-Technologien auf dem Markt vertraut sind, wie z.B. PlayReady, Widevine, FairPlay oder Adobe Access.

In dieser Beschreibung werden die drei DRMs behandelt, die von Azure Media Services unterstützt werden (Multi-DRM): Common Encryption (CENC) für PlayReady und Widevine, FairPlay sowie Verschlüsselung mit unverschlüsseltem AES-128-Schlüssel. Beim Onlinestreaming und in der OTT-Branche lässt sich immer mehr der Trend feststellen, dass native DRM-Systeme auf verschiedenen Clientplattformen verwendet werden. Dieser Trend löst die vorherige Vorgehensweise ab, bei der ein einzelnes DRM-System und das zugehörige Client-SDK für verschiedene Clientplattformen verwendet wurden. Bei Verwenden von CENC mit mehreren nativen DRM-Systemen sind PlayReady und Widevine gemäß der Spezifikation [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) verschlüsselt.

Die Verwendung eines nativen Multi-DRM-Systems für den Inhaltsschutz bietet folgende Vorteile:

* Die Kosten für die Verschlüsselung sinken, da ein einzelner Verschlüsselungsprozess für unterschiedliche Zielplattformen mit deren systemeigenen DRM-Systemen ausgeführt wird.
* Die Kosten für die Verwaltung von Medienobjekten sinken, da nur eine einzige Kopie des Medienobjekts im Speicher erforderlich ist.
* Die Kosten für die DRM-Clientlizenzierung entfallen, da native DRM-Clients in der Regel auf den entsprechenden nativen Plattformen kostenlos sind.

### <a name="goals-of-the-article"></a>Schwerpunkte des Artikels

Für diesen Artikel gelten die folgenden Zielsetzungen:

* Bereitstellung eines Referenzentwurfs für ein DRM-Subsystem, das alle drei DRM-Systeme (CENC für DASH, FairPlay für HLS und PlayReady für Smooth Streaming) nutzt
* Bereitstellung einer Referenzimplementierung auf einer Azure- und Azure Media Services-Plattform
* Erörterung verschiedener Entwurfs- und Implementierungsthemen

Die folgende Tabelle enthält eine Zusammenfassung der nativen DRM-Unterstützung auf verschiedenen Plattformen und der EME-Unterstützung in verschiedenen Browsern.

| **Clientplattform** | **Natives DRM-System** | **EME** |
| --- | --- | --- |
| **Smart-TVs, STBs** | PlayReady, Widevine und/oder andere | Eingebetteter Browser/EME für PlayReady und/oder Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 für PlayReady|
| **Android-Geräte (Telefon, Tablet, TV)** |Widevine |Chrome für Widevine |
| **iOS** | FairPlay | Safari für FairPlay (ab iOS 11.2) |
| **macOS** | FairPlay | Safari für FairPlay (ab Safari 9 und höher unter Mac OS X 10.11 und höher El Capitan)|
| **tvOS** | FairPlay | |

Da der Bereitstellungsstatus der einzelnen DRM-Systeme sehr unterschiedlich sein kann, implementiert ein Dienst in der Regel zwei oder drei DRM-Systeme, um sicherzustellen, dass alle Arten von Endpunkten optimal abgedeckt werden.

Ein Kompromiss zwischen der Komplexität der Dienstlogik und der Komplexität auf der Clientseite sorgt dafür, dass auf den verschiedenen Clients ein gewisses Maß an Benutzerfreundlichkeit erzielt wird.

Beachten Sie die folgenden Aspekte, wenn Sie Ihre Auswahl treffen:

* PlayReady ist auf jedem Windows-Gerät und auf einigen Android-Geräten nativ implementiert und auf nahezu jeder Plattform über Software-SDKs verfügbar.
* Widevine ist auf jedem Android-Gerät, in Chrome und auf einigen anderen Geräten nativ implementiert. Widevine wird über DASH auch in Firefox- und Opera-Browsern unterstützt.
* FairPlay ist unter iOS, macOS und tvOS verfügbar.


## <a name="a-reference-design"></a>Ein Referenzentwurf
Dieser Abschnitt zeigt einen Referenzentwurf, für den die für seine Implementierung verwendeten Technologien keine Rolle spielen.

Ein DRM-Subsystem kann die folgenden Komponenten enthalten:

* Schlüsselverwaltung
* DRM-Verschlüsselungspaketerstellung
* DRM-Lizenzbereitstellung
* Berechtigungsüberprüfung/Zugriffssteuerung
* Benutzerauthentifizierung/Autorisierung
* Player-App
* Ursprung/Content Delivery Network (CDN)

Das folgende Diagramm veranschaulicht die allgemeine Interaktion zwischen den Komponenten in einem DRM-Subsystem:

![DRM-Subsystem mit CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Identitätsanbieter** |Azure Active Directory (Azure AD) |
| **Secure Token Service (STS)** |Azure AD |
| **Workflow des DRM-Schutzes** |Dynamischer Schutz in Azure Media Services |
| **DRM-Lizenzbereitstellung** |* Media Services-Lizenzbereitstellung (PlayReady, Widevine, FairPlay) <br/>* Axinom-Lizenzserver <br/>* Benutzerdefinierter PlayReady-Lizenzserver |
| **Ursprung** |Azure Media Services-Streamingendpunkt |
| **Schlüsselverwaltung** |Für Referenzimplementierung nicht erforderlich |
| **Content Management** |C#-Konsolenanwendung |

Anders gesagt: Sowohl IDP als auch STS werden von Azure AD bereitgestellt. Für den Player wird die [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/). Sowohl Azure Media Services als auch der Azure Media Player unterstützen CENC über DASH, FairPlay über HLS, PlayReady über Smooth Streaming und AES-128-Verschlüsselung für DASH, HLS und Smooth.

Das folgende Diagramm zeigt die allgemeine Struktur und den allgemeinen Ablauf bei dieser Technologiezuordnung:

![CENC mit Media Services](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Zum Einrichten des DRM-Inhaltsschutzes verwendet das Content Management-Tool die folgenden Eingaben:

* Offene Inhalte
* Inhaltsschlüssel aus der Schlüsselverwaltung
* Lizenzerwerbs-URLs
* Eine Liste von Informationen aus Azure AD, beispielsweise Zielgruppe, Zertifikataussteller und Tokenansprüche

Die Ausgabe des Content Management-Tools sieht wie folgt aus:

* „ContentKeyPolicy“ beschreibt die DRM-Lizenzvorlage für jede verwendete Art von DRM-System.
* „ContentKeyPolicyRestriction“ beschreibt die Zugriffssteuerung vor dem Ausstellen einer DRM-Lizenz.
* „Streamingpolicy“ beschreibt die verschiedenen DRM-Kombinationen (Verschlüsselungsmodus, Streamingprotokoll, Containerformat) für das Streaming.
* „StreamingLocator“ beschreibt den für die Verschlüsselung verwendeten Inhaltsschlüssel/IV und Streaming-URLs. 

So sieht der Ablauf zur Laufzeit aus:

* Bei der Benutzerauthentifizierung wird ein JWT generiert.
* Einer der im JWT enthaltenen Ansprüche ist ein Gruppenanspruch, der die Gruppenobjekt-ID „EntitledUserGroup“ enthält. Dieser Anspruch wird verwendet, um die Berechtigungsüberprüfung zu bestehen.
* Der Player lädt das Clientmanifest von über CENC geschützten Inhalten herunter und identifiziert folgende Elemente:
   * Schlüssel-ID
   * Schutz des Inhalts durch DRM
   * Lizenzerwerbs-URLs
* Der Player sendet eine Lizenzerwerbsanforderung basierend auf dem unterstützten Browser/DRM-System. In der Lizenzerwerbsanforderung werden auch die Schlüssel-ID und das JWT übermittelt. Der Lizenzbereitstellungsdienst überprüft das JWT und die enthaltenen Ansprüche, bevor die benötige Lizenz ausgestellt wird.

## <a name="implementation"></a>Implementierung
### <a name="implementation-procedures"></a>Implementierungsverfahren
Die Implementierung umfasst die folgenden Schritte:

1. Bereiten Sie Testmedienobjekte vor. Codieren/packen Sie ein Testvideo in einer fragmentierten MP4-Datei mit mehreren Bitraten in Media Services. Dieses Medienobjekt ist *nicht* durch DRM geschützt. Der DRM-Schutz erfolgt später dynamisch.

2. Erstellen Sie eine Schlüssel-ID und einen Inhaltsschlüssel (optional aus einem Schlüsselausgangswert). An dieser Stelle wird das Schlüsselverwaltungssystem nicht benötigt, da für einige wenige Testmedienobjekte nur eine einzige Schlüssel-ID und ein einziger Inhaltsschlüssel erforderlich sind.

3. Verwenden Sie die Media Services-API, um für das Testmedienobjekt Lizenzbereitstellungsdienste für mehrere DRM-Systeme zu konfigurieren. Wenn Sie anstelle der Lizenzdienste in Media Services benutzerdefinierte Lizenzserver verwenden, die von Ihrem Unternehmen oder einem Partner Ihres Unternehmens bereitgestellt wurden, können Sie diesen Schritt überspringen. In diesem Fall können Sie Lizenzerwerbs-URLs beim Konfigurieren der Lizenzbereitstellung angeben. Die Media Services-API ist erforderlich, um einige detaillierte Konfigurationsoptionen anzugeben, z.B. die Einschränkung von Autorisierungsrichtlinien sowie Lizenzbereitstellungsvorlagen für verschiedene DRM-Lizenzdienste. Das Azure-Portal bietet derzeit nicht die für diese Konfiguration erforderliche Benutzeroberfläche. Informationen und Beispielcode für die API finden Sie unter [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](protect-with-drm.md).

4. Konfigurieren Sie mithilfe der Media Services-API die Bereitstellungsrichtlinie für das Testmedienobjekt. Informationen und Beispielcode für die API finden Sie unter [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](protect-with-drm.md).

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

* Informationen für Entwickler bietet das [Entwicklerhandbuch zu Azure Active Directory](../../active-directory/develop/v2-overview.md).
* Informationen für Administratoren finden Sie unter [Verwalten Ihres Azure AD-Verzeichnisses](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Mögliche Probleme bei der Implementierung

Falls bei der Implementierung Probleme auftreten, verwenden Sie die folgenden Informationen zur Behandlung dieser Probleme.

* Die Aussteller-URL muss auf „/“ enden. Die Zielgruppe muss die Anwendungsclient-ID des Players sein. Fügen Sie hier ebenfalls einen Schrägstrich („/“) am Ende der Aussteller-URL ein.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    Im [JWT-Decoder](http://jwt.calebb.net/) sollten **aud** und **iss** wie im JWT angezeigt werden:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Fügen Sie in Azure AD (auf der Registerkarte **Konfigurieren** der Anwendung) Berechtigungen für die Anwendung hinzu. Berechtigungen sind für jede Anwendung erforderlich, sowohl für lokale als auch für bereitgestellte Versionen.

    ![Berechtigungen](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

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

    `objTokenRestrictionTemplate.TokenType = TokenType.JWT;`

    Da Sie zusätzlich zu SWT (ACS) Unterstützung für JWT (Azure AD) hinzufügen, ist „TokenType.JWT“ der standardmäßige TokenType. Wenn Sie SWT/ACS verwenden, müssen Sie „TokenType.SWT“ festlegen.

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

![Benutzerdefiniertes Azure AD-Mandantendomänenkonto 1](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-Domänenkonto mit Smartcard**: Die von der IT-Abteilung von Microsoft angepasste Anmeldeseite mit zweistufiger Authentifizierung.

![Benutzerdefiniertes Azure AD-Mandantendomänenkonto 2](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-Konto**: Die Anmeldeseite des Microsoft-Kontos für Endbenutzer.

![Benutzerdefiniertes Azure AD-Mandantendomänenkonto 3](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Verwenden von Encrypted Media Extensions für PlayReady

In einem modernen Browser mit Encrypted Media Extensions (EME) für die PlayReady-Unterstützung, z.B. Internet Explorer 11 unter Windows 8.1 oder höher sowie Microsoft Edge unter Windows 10, ist PlayReady das zugrunde liegende DRM-System für EME.

![Verwenden von EME für PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Der dunkle Playerbereich ist darauf zurückzuführen, dass der PlayReady-Schutz verhindert, dass eine Bildschirmaufnahme von geschützten Videoinhalten erstellt wird.

Der folgende Screenshot zeigt die Player-Plug-Ins sowie die Unterstützung für Microsoft Security Essentials (MSE) und EME:

![Player-Plug-Ins für PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME in Microsoft Edge und Internet Explorer 11 unter Windows 10 erlaubt den Aufruf von [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) auf Windows 10-Geräten, die dies unterstützen. PlayReady SL3000 ermöglicht erweiterte Premiuminhalte (4K, HDR) und neue Modelle für die Inhaltsbereitstellung (für erweiterte Inhalte).

Bei Windows-Geräten ist PlayReady das einzige in die Hardware integrierte DRM-System, das auf Windows-Geräten verfügbar ist (PlayReady SL3000). Ein Streamingdienst kann PlayReady über EME oder über eine UWP-Anwendung (Universelle Windows-Plattform) nutzen und mit PlayReady SL3000 eine höhere Videoqualität als mit anderen DRM-Systemen bieten. Üblicherweise werden Inhalte bis 2K über Chrome oder Firefox und Inhalte bis 4K über Microsoft Edge/Internet Explorer 11 oder eine UWP-Anwendung auf dem gleichen Gerät gestreamt. Die Menge richtet sich nach Diensteinstellungen und Implementierung.

#### <a name="use-eme-for-widevine"></a>Verwenden von EME für Widevine

In einem modernen Browser mit EME/Widevine-Unterstützung, z. B. Chrome 41 und höher unter Windows 10, Windows 8.1, Mac OSX Yosemite und Chrome unter Android 4.4.4, ist Google Widevine das DRM-System hinter EME.

![Verwenden von EME für Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine verhindert nicht, dass Bildschirmaufnahmen von geschützten Videoinhalten erstellt werden.

![Player-Plug-Ins für Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Verwenden von EME für FairPlay

Auf ähnliche Weise können Sie durch FairPlay geschützte Inhalte in diesem Testplayer in Safari unter macOS oder iOS 11.2 und höher testen.

Achten Sie darauf, dass Sie „FairPlay“ als „protectionInfo.type“ und die richtige URL für Ihr Anwendungszertifikat als Zertifikatpfad der FairPlay-Streaminganwendung (FPS AC Path) angeben.

### <a name="unentitled-users"></a>Nicht berechtigte Benutzer

Wenn ein Benutzer kein Mitglied der Gruppe der berechtigten Benutzer ist, besteht er die Berechtigungsüberprüfung nicht. Der Lizenzdienst für mehrere DRM-Systeme stellt die angeforderte Lizenz nicht aus, wie unten gezeigt. In der Beschreibung wird angezeigt, dass die Lizenz nicht erfolgreich erworben werden konnte. Dies ist das gewünschte Verhalten.

![Nicht berechtigte Benutzer](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Ausführen einen benutzerdefinierten Sicherheitstokendiensts

Wenn Sie einen benutzerdefinierten Sicherheitstokendienst ausführen, wird das JWT von diesem Dienst entweder über einen symmetrischen oder einen asymmetrischen Schlüssel ausgestellt.

Der folgende Screenshot zeigt ein Szenario mit einem symmetrischen Schlüssel (in Chrome):

![Benutzerdefinierter Sicherheitstokendienst mit symmetrischem Schlüssel](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Der folgende Screenshot zeigt ein Szenario mit einem asymmetrischen Schlüssel über ein X.509-Zertifikat (in einem modernen Microsoft-Browser):

![Benutzerdefinierter Sicherheitstokendienst mit asymmetrischem Schlüssel](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

In beiden oben genannten Fällen ist die Benutzerauthentifizierung die gleiche. Sie erfolgt über Azure AD. Der einzige Unterschied ist, dass JWTs vom benutzerdefinierten Sicherheitstokendienst anstatt von Azure AD ausgestellt werden. Beim Konfigurieren des dynamischen CENC-Schutzes bestimmt die Einschränkung des Lizenzbereitstellungsdiensts, welche Art JWT verwendet wird: entweder ein symmetrischer oder ein asymmetrischer Schlüssel.

## <a name="next-steps"></a>Nächste Schritte

* [Häufig gestellte Fragen](frequently-asked-questions.md)
* [Übersicht über den Inhaltsschutz](content-protection-overview.md)
* [Schützen Ihrer Inhalte mit DRM](protect-with-drm.md)
