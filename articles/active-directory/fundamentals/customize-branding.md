---
title: Hinzufügen von Branding zur Anmeldeseite Ihrer Organisation – Azure AD
description: Hier finden Sie Anweisungen dazu, wie Sie der Azure Active Directory-Anmeldeseite ein Branding Ihrer Organisation hinzufügen.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb138ade0f579a7545c0910646b6adfb7d5ac02a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996660"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Hinzufügen von Branding zur Azure Active Directory-Anmeldeseite Ihrer Organisation
Verwenden Sie das Logo und benutzerdefinierte Farbschemas Ihrer Organisation, um Ihren Azure Active Directory-Anmeldeseiten (Azure AD) ein konsistentes Aussehen und Verhalten zu verleihen. Ihre Anmeldeseiten werden angezeigt, wenn sich Benutzer bei webbasierten Apps Ihrer Organisation wie Microsoft 365 anmelden, die Azure AD als Identitätsanbieter verwenden.

>[!NOTE]
>Für das Hinzufügen eines benutzerdefinierten Brandings benötigen Sie eine Azure Active Directory Premium 1- oder Premium 2-Lizenz. Weitere Informationen zu Lizenzierung und Editionen finden Sie unter [Registrieren für Azure AD Premium](active-directory-get-started-premium.md).<br><br>Die Azure AD Premium-Editionen stehen für Kunden in China zur Verfügung, die mit der weltweit verfügbaren Instanz von Azure Active Directory arbeiten. Allerdings werden die Azure AD Premium-Editionen derzeit nicht durch den Azure-Dienst unterstützt, der in China von 21Vianet betrieben wird. Sollten Sie weitere Informationen benötigen, können Sie sich über das [Azure Active Directory-Forum](https://feedback.azure.com/forums/169401-azure-active-directory/) mit uns in Verbindung setzen.

## <a name="customize-your-azure-ad-sign-in-page"></a>Anpassen Ihrer Azure AD-Anmeldeseite
Sie können Ihre Azure AD-Anmeldeseiten anpassen, die angezeigt werden, wenn sich Benutzer bei mandantenspezifischen Apps Ihrer Organisation anmelden, z. B. `https://outlook.com/contoso.com`, oder bei der Übergabe einer Domänenvariablen, z. B. `https://passwordreset.microsoftonline.com/?whr=contoso.com`.

Ihr benutzerdefiniertes Branding wird nicht sofort angezeigt, wenn Ihre Benutzer zu Websites wie www\.office.com navigieren. Stattdessen muss sich der Benutzer anmelden, bevor Ihr benutzerdefiniertes Branding angezeigt wird. Nach der Anmeldung des Benutzers kann es 15 Minuten oder länger dauern, bis das Branding angezeigt wird. 

> [!NOTE]
> Alle Brandingelemente sind optional. Wenn Sie beispielsweise ein Bannerlogo ohne Hintergrundbild angeben, wird auf der Anmeldeseite Ihr Logo mit einem Standardhintergrundbild der Zielwebsite (z. B. Microsoft 365) angezeigt.<br><br>Darüber hinaus wird das Branding von Anmeldeseiten nicht für persönliche Microsoft-Konten übernommen. Wenn sich Ihre Benutzer oder Gäste des Unternehmens mit einem persönlichen Microsoft-Konto anmelden, wird auf der Anmeldeseite das Branding Ihrer Organisation nicht angezeigt.

### <a name="to-customize-your-branding"></a>So passen Sie Ihr Branding an
1. Melden Sie sich mit dem Konto eines globalen Administrators für das Verzeichnis beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie **Azure Active Directory** aus, dann **Unternehmensbranding** und schließlich **Konfigurieren**.

    ![Seite „Contoso – Unternehmensbranding“, Option „Konfigurieren“ hervorgehoben](media/customize-branding/company-branding-configure-button.png)

3. Geben Sie auf der Seite **Unternehmensbranding konfigurieren** einige oder alle der folgenden Informationen an.

    >[!IMPORTANT]
    >Alle benutzerdefinierten Bilder, die Sie auf dieser Seite hinzufügen, unterliegen Einschränkungen hinsichtlich der Bildgröße (in Pixel) und potenziell der Dateigröße (KB). Aufgrund dieser Einschränkungen müssen Sie höchstwahrscheinlich einen Foto-Editor verwenden, um die richtigen Bildgrößen zu erstellen.

    - **Allgemeine Einstellungen**

        ![Seite „Unternehmensbranding konfigurieren“ mit ausgefüllten allgemeinen Einstellungen](media/customize-branding/configure-company-branding-general-settings.png)

        - **Sprache.** Die Sprache wird automatisch als Ihr Standard festgelegt und kann nicht geändert werden.
        
        - **Hintergrundbild der Anmeldeseite.** Wählen Sie eine PNG- oder JPG-Bilddatei aus, die als Hintergrund für Ihre Anmeldeseiten angezeigt werden soll. Das Bild wird in der Mitte des Browsers verankert und auf die Größe des sichtbaren Bereichs skaliert. Ein Bild, das größer als 1920 x 1080 Pixel ist oder eine Dateigröße von mehr als 300 KB aufweist, können Sie nicht auswählen.
        
            Es wird empfohlen, Bilder zu verwenden, in denen sich die Bildschärfe nicht auf einen Bereich konzentriert (z. B. ein undurchsichtiges weißes Feld, das in der Mitte des Bildschirms angezeigt wird und je nach Abmessungen des sichtbaren Bereichs alle Teile des Bilds verdecken kann).

        - **Bannerlogo.** Wählen Sie eine PNG- oder JPG-Version Ihres Logos aus, das auf der Anmeldeseite angezeigt werden soll, nachdem der Benutzer einen Benutzernamen eingegeben hat, sowie auf der Portalseite **Meine Apps**.
            
            Das Bild darf nicht höher als 60 Pixel oder breiter als 280 Pixel sein. Wir empfehlen, ein transparentes Bild zu verwenden, weil der Hintergrund unter Umständen nicht mit Ihrem Logohintergrund übereinstimmt. Wir empfehlen ferner, um das Bild herum keine Auffüllung vorzunehmen, da Ihr Logo sonst klein wirken könnte.

        - **Hinweis auf den Benutzernamen.** Geben Sie den Hinweistext ein, der Benutzern angezeigt wird, wenn sie ihren Benutzernamen vergessen haben. Dieser Text muss im Unicode-Format sein, darf keine Links oder Code enthalten und darf maximal 64 Zeichen lang sein. Wenn sich Gäste bei Ihrer App anmelden, wird empfohlen, diesen Hinweis nicht hinzuzufügen.

        - **Text und Formatierung der Anmeldeseite.** Geben Sie den Text ein, der am unteren Rand der Anmeldeseite angezeigt wird. Sie können diesen Text verwenden, um zusätzliche Informationen, wie z.B. die Telefonnummer Ihres Helpdesks oder einen rechtlichen Hinweis, zu kommunizieren. Dieser Text muss im Unicode-Format sein und darf 1024 Zeichen nicht überschreiten.

           Sie können den für die Anmeldeseite eingegebenen Text anpassen. Um einen neuen Absatz zu beginnen, verwenden Sie zweimal die EINGABETASTE. Sie können den Text auch so ändern, dass fette und kursive Formatierungen, ein Unterstrich oder ein klickbarer Link enthalten sind. Verwenden Sie die folgende Syntax, um eine Textformatierung hinzuzufügen: 

          > Hyperlink: ```[text](link)``` 
          
          > Fett: ``` **text** ``` oder ``` __text__ ``` 
          
          > Kursiv: ``` *text* ``` oder ``` _text_ ``` 
          
          > Unterstrich: ``` ++text++ ``` 

    - **Erweiterte Einstellungen**
            
        ![Seite „Unternehmensbranding konfigurieren“ mit ausgefüllten erweiterten Einstellungen](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Hintergrundfarbe der Anmeldeseite.** Geben Sie die hexadezimale Farbe an (z. B. ist Weiß = #FFFFFF), die in Situationen mit niedriger Bandbreite anstelle Ihres Hintergrundbilds angezeigt werden soll. Wir empfehlen, dass Sie die primäre Farbe Ihres Bannerlogos oder die Farbe Ihrer Organisation verwenden.

        - **Bild für quadratisches Logo.** Wählen Sie ein PNG- (bevorzugt) oder JPG-Bild des Logos Ihrer Organisation aus, das Benutzern während des Installationsprozesses für neue Windows 10 Enterprise-Geräte angezeigt werden soll. Dieses Bild wird nur für die Windows-Authentifizierung verwendet und wird nur auf Mandanten angezeigt, die [Windows Autopilot]( /windows/deployment/windows-autopilot/windows-10-autopilot) für die Bereitstellung oder für Kennworteingabeseiten in anderen Windows 10-Erfahrungen verwenden. In einigen Fällen kann es auch im Zustimmungsdialogfeld angezeigt werden.
        
            Das Bild darf nicht größer als 240 x 240 Pixel sein und muss eine Dateigröße von weniger als 10 KB einhalten. Wir empfehlen, ein transparentes Bild zu verwenden, weil der Hintergrund unter Umständen nicht mit Ihrem Logohintergrund übereinstimmt. Wir empfehlen ferner, um das Bild herum keine Auffüllung vorzunehmen, da Ihr Logo sonst klein wirken könnte.
    
        - **Quadratisches Logobild, dunkles Design.** Identisch mit dem oben genannten Bild für quadratisches Logo. Dieses Logobild ersetzt das Bild für quadratisches Logo, wenn ein dunkler Hintergrund verwendet wird, z. B. bei Windows 10 Azure AD Join-Bildschirmen auf der Windows-Willkommensseite.  Wenn Ihr Logo auf weißen, dunkelblauen und schwarzen Hintergründen gut aussieht, müssen Sie dieses Bild nicht hinzufügen. 
        
        - **Option zum angemeldet bleiben anzeigen.** Sie können Ihren Benutzern gestatten, bei Azure AD bis zur expliziten Abmeldung angemeldet zu bleiben. Wenn Sie **Nein** auswählen, wird diese Option ausgeblendet, und Benutzer müssen sich jedes Mal anmelden, wenn der Browser geschlossen und erneut geöffnet wird.

            Diese Funktionalität steht nur im Standardbrandingobjekt und nicht in einem sprachspezifischen Objekt zur Verfügung. Weitere Informationen zum Konfigurieren der Option „Angemeldet bleiben“ und zur Problembehandlung finden Sie unter [Konfigurieren der Eingabeaufforderung „Angemeldet bleiben?“ für Azure AD-Konten](keep-me-signed-in.md).
        
            >[!NOTE]
            >Einige Features von SharePoint Online und Office 2010 setzen voraus, dass Benutzer wählen können, angemeldet zu bleiben. Wenn Sie diese Option auf **Nein** festlegen, werden Benutzern unter Umständen zusätzliche und unerwartete Anmeldeaufforderungen angezeigt.
   

3. Nachdem Sie mit dem Hinzufügen Ihres Brandings fertig sind, wählen Sie **Speichern** aus.

    Wenn dieser Prozess Ihre erste benutzerdefinierte Brandingkonfiguration erstellt, wird sie der Standard für Ihren Mandanten. Wenn Sie zusätzliche Konfigurationen haben, können Sie Ihre Standardkonfiguration auswählen.
    
    >[!IMPORTANT]
    >Um Ihrem Mandanten weitere Unternehmensbrandingkonfigurationen hinzuzufügen, müssen Sie **Neue Sprache** auf der Seite **Contoso – Unternehmensbranding** auswählen. Hierdurch wird die Seite **Unternehmensbranding konfigurieren** geöffnet, auf der Sie dieselben Schritte wie oben ausführen können.

## <a name="update-your-custom-branding"></a>Aktualisieren Ihres benutzerdefinierten Brandings
Nachdem Sie Ihr benutzerdefiniertes Branding erstellt haben, können Sie zurückgehen und alles nach Belieben ändern.

### <a name="to-edit-your-custom-branding"></a>So bearbeiten Sie Ihr benutzerdefiniertes Branding
1. Melden Sie sich mit dem Konto eines globalen Administrators für das Verzeichnis beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie **Azure Active Directory** aus, dann **Unternehmensbranding** und schließlich **Konfigurieren**.

    ![Seite „Contoso – Unternehmensbranding“ mit angezeigter Standardkonfiguration](media/customize-branding/company-branding-default-config.png)

3. Auf der Seite **Unternehmensbranding konfigurieren** fügen Sie Informationen hinzu, entfernen oder ändern diese, basierend auf den Beschreibungen im Abschnitt [Anpassen Ihrer Azure AD-Anmeldeseite](#customize-your-azure-ad-sign-in-page) dieses Artikels.

4. Wählen Sie **Speichern** aus.

   Es kann bis zu einer Stunde dauern, bis Änderungen übernommen werden, die Sie am Branding der Anmeldeseite vorgenommen haben.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Hinzufügen eines sprachspezifischen Unternehmensbrandings zu Ihrem Verzeichnis
Sie können die Sprache Ihrer ursprünglichen Konfiguration nicht in eine andere Sprache als Ihre Standardsprache ändern. Wenn Sie allerdings eine Konfiguration in einer anderen Sprache benötigen, können Sie eine neue Konfiguration erstellen.

### <a name="to-add-a-language-specific-branding-configuration"></a>So fügen Sie eine sprachspezifische Brandingkonfiguration hinzu

1. Melden Sie sich mit dem Konto eines globalen Administrators für das Verzeichnis beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie **Azure Active Directory** aus, dann **Unternehmensbranding** und schließlich **Neue Sprache**.

    ![Seite „Contoso – Unternehmensbranding“ mit hervorgehobener Option „Neue Sprache“](media/customize-branding/company-branding-new-language.png)

3. Auf der Seite **Unternehmensbranding konfigurieren** wählen Sie Ihre Sprache aus (z. B. Deutsch), und fügen Sie dann Ihre übersetzten Informationen hinzu, basierend auf den Beschreibungen im Abschnitt [Anpassen Ihrer Azure AD-Anmeldeseite](#customize-your-azure-ad-sign-in-page) dieses Artikels.

4. Wählen Sie **Speichern** aus.

    Die Seite **Contoso – Unternehmensbranding** wird mit Ihrer neuen deutschen Konfiguration aktualisiert.

    ![Contoso – Seite „Unternehmensbranding“ mit der neuen Sprachkonfiguration](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Hinzufügen Ihres benutzerdefinierten Brandings zu Seiten
Fügen Sie Ihr benutzerdefiniertes Branding zu Seiten hinzu, indem Sie das Ende der URL mit dem Text `?whr=yourdomainname` ändern. Diese Änderung funktioniert auf mehreren Seiten, einschließlich der Setupseite für mehrstufige Authentifizierung, der Setupseite für Self-Service-Kennwortzurücksetzung und der Anmeldeseite.

**Beispiele:**

**Original-URL:** https://aka.ms/MFASetup<br>
**Benutzerdefinierte URL:** `https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Original-URL:** https://aka.ms/SSPR<br>
**Benutzerdefinierte URL:** `https://passwordreset.microsoftonline.com/?whr=contoso.com`
