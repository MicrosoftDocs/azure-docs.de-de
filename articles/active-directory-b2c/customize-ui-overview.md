---
title: Informationen zur Anpassung der Benutzeroberfläche (UI) in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Benutzeroberfläche Ihrer Anwendungen anpassen, die Azure Active Directory B2C verwenden.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6c9109cf4d6d67d3d8001a9de1d54e24622a9286
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511173"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Informationen zur Anpassung der Benutzeroberfläche (UI) in Azure Active Directory B2C

Dass Sie die Möglichkeit haben, der Benutzeroberfläche (UI), die Azure Active Directory (Azure AD) B2C Ihren Anwendungen bereitstellt, ein Branding hinzuzufügen bzw. sie anzupassen, ist wichtig, um Ihrem Kunden eine nahtlose Benutzererfahrung bereitstellen zu können. Diese Erfahrungen umfassen Registrierung, Anmeldung, Profilbearbeitung und Kennwortzurücksetzung. Dieser Artikel enthält Informationen, um Ihnen bei der Anpassung der Benutzeroberfläche Ihrer Anwendungen zu helfen.

Je nach Ihren Anforderungen hinsichtlich dieser Erfahrungen passen Sie die Benutzeroberfläche Ihrer Anwendung auf unterschiedliche Weise an. Beispiel:

- Wenn Sie [Benutzerflows](active-directory-b2c-reference-policies.md) verwenden, um in Ihrer Anwendung Benutzeroberflächenfeatures für Registrierung oder Anmeldung, Kennwortzurücksetzung oder Profilbearbeitung bereitzustellen, verwenden Sie das [Azure-Portal zum Anpassen der Benutzeroberfläche](tutorial-customize-ui.md).
- Wenn Sie einen V2-Benutzerflow verwenden, können Sie mithilfe einer [Seitenlayoutvorlage](#page-layout-templates) das Aussehen Ihrer Benutzerflowseiten ohne weitere Anpassung ändern. Sie können beispielsweise das Design „Ozeanblau“ oder „Schiefergrau“ auf alle Seiten in Ihrem Benutzerflow anwenden.
- Wenn Sie nur eine Anmeldung mit der begleitenden Seite für die Kennwortzurücksetzung sowie Verifizierungs-E-Mails bereitstellen, verwenden Sie dieselben Anpassungsschritte, die für eine [Azure AD-Anmeldeseite](../active-directory/fundamentals/customize-branding.md) verwendet werden.
- Wenn Kunden versuchen, ihr Profil zu bearbeiten, bevor sie sich anmelden, werden sie zu einer Seite umgeleitet, die Sie mithilfe derselben Schritte anpassen, die zum Anpassen der Anmeldeseite von Azure AD verwendet werden.
- Wenn Sie [benutzerdefinierte Richtlinien](active-directory-b2c-overview-custom.md) verwenden, um in Ihrer Anwendung Registrierung bzw. Anmeldung, Kennwortzurücksetzung oder Profilbearbeitung bereitzustellen, verwenden Sie die [Richtliniendateien zum Anpassen der Benutzeroberfläche](active-directory-b2c-ui-customization-custom.md).
- Wenn Sie dynamische Inhalte auf Grundlage der Entscheidung eines Kunden bereitstellen müssen, verwenden Sie [benutzerdefinierte Richtlinien, die Seiteninhalte ändern können](active-directory-b2c-ui-customization-custom-dynamic.md), in Abhängigkeit von einem Parameter, der in einer Abfragezeichenfolge gesendet wird. So ändert sich beispielsweise das Hintergrundbild auf der Azure AD B2C-Registrierungs- oder Anmeldeseite auf Grundlage eines Parameters, den Sie aus Ihrer Web- oder Mobilanwendung übergeben.
- Sie können den clientseitigen JavaScript-Code in Ihren Azure AD B2C-[Benutzerflows](user-flow-javascript-overview.md) oder [benutzerdefinierten Richtlinien](page-contract.md) aktivieren.

Azure AD B2C führt den Code im Browser Ihres Kunden aus und verwendet einen modernen Ansatz namens [Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS, Ressourcenfreigabe zwischen verschiedenen Ursprüngen). Zur Laufzeit wird Inhalt über eine URL geladen, die Sie in einem Benutzerflow oder einer Richtlinie angeben. Sie geben verschiedene URLs für unterschiedliche Seiten an. Nachdem Inhalt über Ihr URL geladen wurde, wird er mit einem von Azure AD B2C eingefügten HTML-Fragment zusammengeführt und dann Ihrem Kunden angezeigt.

Wenn Sie zum Anpassen der Benutzeroberfläche Ihre eigenen HTML- und CSS-Dateien verwenden, überprüfen Sie vor dem Start die folgenden Hinweise:

- Azure AD B2C führt HTML-Inhalt in Ihre Seiten zusammen. Versuchen Sie nicht, den Standardinhalt zu kopieren oder zu ändern, den Azure AD B2C bereitstellt. Es wird empfohlen, eigene HTML-Inhalte von Grund auf neu zu erstellen und den Standardinhalt als Referenz zu verwenden.
- JavaScript kann nun in den benutzerdefinierten Inhalt einbezogen werden.
- Unterstützte Browserversionen sind: 
    - Internet Explorer 11, 10 und Microsoft Edge
    - Eingeschränkte Unterstützung für Internet Explorer 9 und 8
    - Google Chrome 42.0 und höher
    - Mozilla Firefox 38.0 und höher
- Stellen Sie sicher, dass Sie keine Form-Tags in Ihren HTML-Code einschließen, da dies die POST-Vorgänge beeinträchtigt, die mit dem aus Azure AD B2C eingefügten HTML-Code generiert werden.

## <a name="page-layout-templates"></a>Seitenlayoutvorlagen

Für V2-Benutzerflows können Sie eine vordefinierte Vorlage auswählen, die Ihren Standardseiten ein besseres Aussehen verleiht und als gute Grundlage für Ihre eigene Anpassung dienen kann.

Wählen Sie im linken Menü unter **Anpassen** die Option **Seitenlayouts** aus. Wählen Sie dann **Vorlage (Vorschau)** aus.

![Auswählen einer Seitenlayoutvorlage](media/customize-ui-overview/template.png)

Wählen Sie eine Vorlage aus der Liste aus. Mit der Vorlage **Ozeanblau** wird beispielsweise das folgende Layout auf Ihre Benutzerflowseiten angewendet:

![Vorlage „Ozeanblau“](media/customize-ui-overview/ocean-blue.png)

Wenn Sie eine Vorlage auswählen, wird das ausgewählte Layout auf alle Seiten in Ihrem Benutzerflow angewendet, und im Feld **Benutzerdefinierter Seiten-URI** wird der URI für jede Seite angezeigt.

## <a name="where-do-i-store-ui-content"></a>Wo speichere ich Inhalte der Benutzeroberfläche?

Wenn Sie zum Anpassen der Benutzeroberfläche Ihre eigenen HTML- und CSS-Dateien verwenden, können Sie Ihre Benutzeroberflächeninhalte überall hosten, z. B. im [Azure-BLOB-Speicher](../storage/blobs/storage-blobs-introduction.md), auf Webservern, in CDNs, AWS S3 oder Dateifreigabesystemen. Der wichtige Punkt hierbei ist, dass Sie die Inhalte auf einem öffentlich verfügbaren HTTPS-Endpunkt (mit aktiviertem CORS) hosten. Sie müssen eine absolute URL verwenden, wenn Sie ihn in Ihrem Inhalt angeben.

## <a name="how-do-i-get-started"></a>Wie fange ich an?

Gehen Sie wie folgt vor, um die Benutzeroberfläche anzupassen:

- Erstellen Sie an einer beliebigen Stelle in `<body>` wohlgeformten HTML-Inhalt mit einem leeren `<div id="api"></div>`-Element. Dieses Element markiert die Stelle, an der der Azure AD B2C-Inhalt eingefügt wird. Im folgenden Beispiel wird eine minimale Seite gezeigt:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Hosten Sie diese Inhalte auf einem HTTPS-Endpunkt (auf dem CORS zulässig ist). Bei der Konfiguration von CORS müssen die Anforderungsmethoden GET und OPTIONS aktiviert werden.
- Formatieren Sie die Elemente der Benutzeroberfläche, die von Azure AD B2C in Ihre Seite einfügt werden, mithilfe von CSS. Das folgende Beispiel zeigt eine einfachen CSS-Datei, die außerdem Einstellungen für die Registrierung eingefügter HTML-Elemente enthält:

    ```css 
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Erstellen oder bearbeiten Sie eine Richtlinie, um den Inhalt zu verwenden, den Sie erstellt haben.

In der folgenden Tabelle finden Sie die HTML-Fragmente, die Azure AD B2C im `<div id="api"></div>`-Element in Ihrem Inhalt zusammenführt.

| Eingefügte Seite | Beschreibung von HTML |
| ------------- | ------------------- |
| Auswahl des Identitätsanbieters | Enthält eine Liste mit Schaltflächen für Identitätsanbieter, aus denen der Benutzer bei der Registrierung oder Anmeldung auswählen kann. Bei diesen Schaltflächen handelt es sich um Identitätsanbieter in Form von sozialen Netzwerken wie Facebook, Google oder lokale Konten (basierend auf E-Mail-Adressen oder Benutzernamen). |
| Registrierung mit lokalem Konto | Enthält ein Formular für die Registrierung eines lokalen Kontos, das auf einer E-Mail-Adresse oder einem Benutzernamen basiert. Das Formular kann verschiedene Eingabesteuerelemente enthalten, z. B. Texteingabefelder, Kennworteingabefelder, Optionsfelder, Dropdownfelder mit einer Auswahlmöglichkeit und Kontrollkästchen mit mehreren Optionen. |
| Registrierung über ein Konto eines sozialen Netzwerks | Kann angezeigt werden, wenn die Registrierung mit einem vorhandenen Konto bei einem sozialen Netzwerk wie Facebook oder Google als Identitätsanbieter erfolgt. Wird verwendet, wenn zusätzliche Informationen vom Kunden mithilfe eines Registrierungsformulars erfasst werden müssen. |
| Einheitliche Registrierung oder Anmeldung | Verarbeitet sowohl die Registrierung als auch die Anmeldung von Kunden. Diese können als Identitätsanbieter soziale Netzwerke wie z. B. Facebook, Google oder lokale Konten verwenden. |
| Multi-Factor Authentication | Kunden können während der Registrierung oder Anmeldung ihre Telefonnummern verifizieren (per SMS oder Sprachnachricht). |
| Error | Stellt dem Kunden Fehlerinformationen bereit. |


## <a name="how-do-i-localize-content"></a>Gewusst wie: Lokalisieren von Inhalt

Sie lokalisieren Ihren HTML-Inhalt, indem Sie die [Sprachanpassung](active-directory-b2c-reference-language-customization.md) in Ihrem Azure AD B2C-Mandanten aktivieren. Wenn Sie dieses Feature aktivieren, kann Azure AD B2C den Open ID Connect-Parameter `ui-locales` an Ihren Endpunkt weiterleiten. Ihr Inhaltsserver kann diesen Parameter verwenden, um benutzerdefinierte, sprachspezifische HTML-Seiten bereitzustellen.

Inhalt kann auf Grundlage des verwendeten Gebietsschemas aus unterschiedlichen Quellen abgerufen werden. Richten Sie in Ihrem CORS-fähigen Endpunkt eine Ordnerstruktur zum Hosten von Inhalten für bestimmte Sprachen ein. Wenn Sie den Platzhalterwert „{Culture:RFC5646}“ verwenden, wird der passende Inhalt abgerufen. Beispielsweise könnte Ihr benutzerdefinierter Seiten-URI aussehen wie `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. Sie können die Seite in Französisch laden, indem Sie Inhalt aus `https://contoso.blob.core.windows.net/fr/myHTML/unified.html` abrufen.

## <a name="examples"></a>Beispiele

Beispiele für die Anpassung finden Sie in diesen [Beispielvorlagendateien](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip), die Sie herunterladen können.

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie Benutzerflows verwenden, bearbeiten Sie dieses Tutorial zum Einstieg in die Benutzeroberflächenanpassung: [Anpassen der Benutzeroberfläche Ihrer Anwendungen in Azure Active Directory B2C](tutorial-customize-ui.md).
- Wenn Sie benutzerdefinierte Richtlinien verwenden, lesen Sie diesen Artikel zum Einstieg in die Benutzeroberflächenanpassung: [Anpassen der Benutzeroberfläche einer Anwendung mithilfe einer benutzerdefinierten Richtlinie in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).

