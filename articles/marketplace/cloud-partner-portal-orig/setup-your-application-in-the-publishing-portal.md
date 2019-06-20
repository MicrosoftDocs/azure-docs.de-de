---
title: Einrichten Ihrer Anwendung im Veröffentlichungsportal
description: Anleitung zur Einrichtung Ihrer Anwendung im Cloudveröffentlichungsportal.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6da80bd3b5fe487a44400f4f3c21e8fa49da75d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943562"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Einrichten Ihrer Anwendung im Veröffentlichungsportal

Sie sind jetzt bereit, Ihre Anwendung im Veröffentlichungsportal einzurichten.

## <a name="login-and-create-a-new-offer"></a>Anmelden und Erstellen eines neuen Angebots

1. Melden Sie sich beim [Cloudpartnerportal](https://cloudpartner.azure.com/) an.
2. Klicken Sie in der Navigationsleiste links auf „+ Neues Angebot“, und wählen Sie „Dynamics 365 for Customer Engagement“ aus.

![Auswählen eines neuen Angebots](./media/CRMScreenShot14.png)

1. Eine neue Editor-Ansicht für Angebote wird geöffnet, und Sie können nun mit der Erstellung beginnen.

![Bildschirm „Neues Angebot“](./media/CRMScreenShot15.png)

1. Die „Formulare“, die ausgefüllt werden müssen, werden in der „Editor“-Ansicht auf der linken Seite angezeigt. Jedes „Formular“ besteht aus einer Reihe von Feldern, die ausgefüllt werden müssen. Erforderliche Felder sind mit einem roten Sternchen gekennzeichnet (\*).

Es gibt vier Hauptformulare für die Erstellung von Angeboten für Dynamics 365 for Customer Engagement.

* Angebotseinstellungen
* Technische Informationen
* Storefrontdetails
* Kontakte

## <a name="fill-out-the-offer-settings-form"></a>Ausfüllen des Formulars „Angebotseinstellungen“

Das Formular für die Angebotseinstellungen ist ein grundlegendes Formular, in dem Sie die Angebotseinstellungen angeben. Die einzelnen Felder werden nachfolgend beschrieben.

### <a name="offer-id"></a>Angebots-ID

Dies ist ein eindeutiger Bezeichner für das Angebot innerhalb eines Herausgeberprofils. Diese ID wird in den Produkt-URLs angezeigt. Sie darf nur aus klein geschriebenen alphanumerischen Zeichen oder Bindestrichen (-) bestehen. Die ID darf nicht mit einem Bindestrich enden und darf höchstens 50 Zeichen umfassen. Beachten Sie, dass dieses Feld gesperrt wird, sobald ein Angebot online geschaltet wird.

Wenn z.B. der Verleger **„contoso“** ein Angebot mit der Angebots-ID **„sample-WebApp“** erstellt, wird dieses in AppSource wie folgt angezeigt: „https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview“.

### <a name="publisher-id"></a>Herausgeber-ID

In dieser Dropdownliste können Sie das Herausgeberprofil auswählen, unter dem das Angebot veröffentlicht werden soll. Beachten Sie, dass dieses Feld gesperrt wird, sobald ein Angebot online geschaltet wird.

### <a name="name"></a>NAME

Hierbei handelt es sich um den Anzeigenamen für Ihr Angebot. Dies ist der Name, der in [AppSource](https://appsource.microsoft.com/) angezeigt wird. Er darf aus höchstens 50 Zeichen bestehen.

Klicken Sie auf „Speichern“, um Ihre Eingaben zu speichern. Als Nächstes fügen Sie technische Informationen für Ihr Angebot hinzu.

## <a name="fill-out-the-technical-info-form"></a>Ausfüllen des Formulars „Technische Informationen“


Im Formular für technische Informationen füllen Sie die für Ihre Dynamics 365 for Customer Engagement-Lösung spezifischen Informationen aus. Wenn Sie die Maus über den Bereich bewegen, erhalten Sie weitere Informationen. Betrachten Sie das folgende Beispiel.

![Bildschirm „Technische Informationen“](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Anwendungsinfo

Die meisten Herausgeber belassen in diesen Feldern die Standardwerte: „Benutzer“, „Nein“, „Nein“ und eine leere URL für die Anwendungskonfiguration (gemäß dem obigen Screenshot).

### <a name="crm-package"></a>CRM-Paket

![Informationen zum CRM-Paket](./media/CRMScreenShot17.png)

Hier folgt eine Erläuterung zu diesen Feldern:

* Dateiname Ihres Pakets: Der Dateiname, den Sie im obigen Schritt beim Erstellen der ZIP-Datei erstellt haben, die Ihr CRM AppSource-Paket darstellt. Im obigen Beispiel ist es „Microsoft\_SamplePackage.zip“.
* URL Ihres Paketspeicherorts: Dies ist die URL zum Azure Storage-Konto, das den oben angegebenen Paketdateinamen enthält. Es ist die in Schritt 9 des obigen Abschnitts erstellte URL.
* Enthält Ihre Paketdatei mehrere CRM-Pakete: Wählen Sie **NUR** dann „Ja“ aus, wenn mehrere Versionen von CRM mit verschiedenen Paketen unterstützt werden. Für die meisten Partner lautet die Antwort „Nein“. Wenn Sie „Ja“ auswählen, müssen Sie AppSource-Pakete für jede Version Ihrer Lösung erstellen. _Hinweis: Dies ist KEINE Frage, wenn Sie über mehrere **ZIP**-Dateien verfügen. Wenn Sie über mehrere solution.zip-Dateien verfügen, aber nur eine Version besitzen, sollten Sie trotzdem „Nein“ auswählen. Das Verpackungstool führt diese automatisch zusammen._

### <a name="crm-package-availability"></a>Verfügbarkeit von CRM-Paketen

Wählen Sie in diesem Abschnitt aus, für welche CRM-Regionen Ihr Paket zur Verfügung gestellt werden soll. Informationen darüber, welche CRM-Regionen welche Länder/Regionen versorgen, finden Sie unter dem Link: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Hinweis: Die Bereitstellung von „Sovereign and US Gov Cloud“ in Deutschland ERFORDERT EINE BESONDERE BERECHTIGUNG UND VALIDIERUNG WÄHREND DER ZERTIFIZIERUNG.

## <a name="storefront-details"></a>Storefrontdetails

### <a name="offer-summary"></a>Angebotszusammenfassung

Dies ist eine Zusammenfassung des Nutzenversprechens Ihres Angebots. Die Zusammenfassung wird auf der Suchseite Ihres Angebots angezeigt. Sie darf maximal 100 Zeichen lang sein.

### <a name="offer-description"></a>Beschreibung des Angebots

Dies ist die Beschreibung, die auf der Detailseite Ihrer Anwendung angezeigt wird. Maximal sind 1300 Zeichen zulässig

### <a name="industries"></a>Branchen

Wählen Sie die Branchen aus, für die Ihre Anwendung (App) am besten geeignet ist. Wenn sich Ihre App auf mehrere Branchen bezieht, können Sie dieses Feld leer lassen.

### <a name="categories"></a>Categories

Wählen Sie die Kategorien aus, die für Ihre App relevant sind. Wählen Sie maximal drei aus.

### <a name="app-type"></a>App-Typ

Wählen Sie den Testtyp aus, der für Ihre App in AppSource ermöglicht wird. „Kostenlos“ bedeutet, dass Ihre App kostenlos ist. „Testversion“ bedeutet, dass Kunden Ihre App für einen kurzen Zeitraum in AppSource ausprobieren können. „Testversion anfordern“ wird für Dynamics 365 for Customer Engagement-Apps nicht unterstützt. Wählen Sie diese Option nicht aus.

### <a name="help-link-for-your-app"></a>Hilfe-Link für Ihre App

Geben Sie eine URL zu einer Seite ein, die hilfreiche Informationen zu Ihrer App enthält.

### <a name="supported-countriesregions"></a>Unterstützte Länder/Regionen

In diesem Feld werden die Länder/Regionen bestimmt, in denen Ihr Angebot für Testzwecke zur Verfügung steht.

### <a name="supported-languages"></a>Unterstützte Sprachen

Wählen Sie die Sprachen aus, die von Ihrer App unterstützt werden. Wenn Ihre App zusätzliche Sprachen unterstützt, die nicht in dieser Liste enthalten sind, fahren Sie mit der Veröffentlichung Ihres Angebots fort und senden Sie uns eine E-Mail an [appsource@microsoft.com](mailto:appsource@microsoft.com), um uns darüber zu informieren.

### <a name="app-version"></a>App-Version

Geben Sie die Versionsnummer Ihrer App ein.

### <a name="app-release-date"></a>Herausgabedatum der App

Geben Sie das Herausgabedatum für Ihre App ein.

### <a name="products-your-app-works-with-max-3"></a>Produkte, mit denen Ihre App zusammenarbeitet (maximal 3)

Listen Sie die bestimmten Produkte auf, mit denen Ihre App zusammenarbeitet. Sie können bis zu drei Produkte auflisten. Um ein Produkt aufzulisten, klicken Sie auf das Pluszeichen (neben „Neu“) und es wird ein neues offenes Textfeld erstellt, in dem Sie den Namen eines Produkts eingeben können, mit dem Ihre App arbeitet.

### <a name="search-keywords-max-3"></a>Suchbegriffe (maximal 3)

AppSource ermöglicht dem Kunden die Suche nach Schlüsselwörtern. Sie können die Schlüsselwörter eingeben, für die Ihre Anwendung den Kunden angezeigt wird.

Wenn die Anwendung z. B. „Mein E-Mail-Dienst“ ist, können „E-Mails“, „Mailing“, „E-Mail-Dienst“ geeignete Schlüsselwörter darstellen. Wählen Sie Wörter aus, die Benutzer wahrscheinlich für die Suche nach Ihrer App im AppSource-Suchfeld verwenden werden.

### <a name="hide-key"></a>Ausblendschlüssel

Dies ist ein Schlüssel, der mit der Vorschau-URL des Angebots kombiniert wird, um es aus der öffentlichen Ansicht auszublenden. Es ist kein Kennwort. Sie können hier eine beliebige Zeichenfolge eingeben.

### <a name="offer-logo-png-format-48x48"></a>Angebotslogo (PNG-Format, 48 x 48)

Dieses wird auf der Suchseite Ihrer App angezeigt. **Es ist nur das PNG-Format zulässig.** Laden Sie ein PNG-Bild mit einer Auflösung von 48PX\*48PX hoch.

### <a name="offer-logo-png-format-216x216"></a>Angebotslogo (PNG-Format, 216 x 216)

Dies wird auf der Detailseite Ihrer App angezeigt. **Es ist nur das PNG-Format zulässig.** Laden Sie ein PNG-Bild mit einer Auflösung von 216PX\*216PX hoch.

### <a name="videos"></a>Videos

Sie können bis zu vier Videos hochladen. Für jedes hochzuladende Video müssen Sie den Videonamen, die URL (nur YouTube oder Vimeo) und das Miniaturbild eingeben, das dem Video zugeordnet werden soll. Das Miniaturbild muss im PNG-Format vorliegen und eine Auflösung von 1280PX\*720PX aufweisen. Klicken Sie auf das Pluszeichen, um neue Videos hinzuzufügen. Die Miniaturbilder der Videos werden auf der Detailseite Ihrer App angezeigt.

### <a name="documents"></a>Dokumente

Sie können bis zu drei Dokumente im PDF-Format hochladen. Für jedes hochzuladende Dokument müssen Sie den Dokumentnamen eingeben und dann das Dokument hochladen. Das Dokument muss im PDF-Format vorliegen.

Klicken Sie auf das Pluszeichen, um neue Dokumente hinzuzufügen.

### <a name="screenshots"></a>Screenshots

Hierbei handelt es sich um Screenshots, die auf der AppSource-Detailseite für Ihre App angezeigt werden.

### <a name="privacy-policy"></a>Datenschutzrichtlinie

Geben Sie die URL für die Datenschutzrichtlinie Ihrer App ein.

### <a name="terms-of-use"></a>Nutzungsbedingungen

Geben Sie die Nutzungsbedingungen Ihrer App ein. AppSource-Kunden müssen diese Bedingungen akzeptieren, bevor sie Ihre App testen können.

### <a name="support-url"></a>Support-URL

Geben Sie die Support-URL für Ihre App ein.

### <a name="lead-destination"></a>Leadzielgruppe

Wählen Sie ein CRM-System aus, in dem Ihr Lead gespeichert wird. Wählen Sie hier „Azure-Tabelle“ aus, wenn Sie eines der folgenden CRM-Systeme haben: Salesforce, Marketo, Microsoft Dynamics CRM. Das hier von Ihnen ausgewählte CRM-System ist der Ort, an den wir Details zu den Endbenutzern schreiben, die Ihre App in AppSource testen (Leads). In Abhängigkeit von dem von Ihnen ausgewählten CRM-System klicken Sie unten auf die entsprechende URL, um Informationen darüber zu erhalten, wie Sie den nächsten Satz von Feldern ausfüllen können.

* [Azure-Tabelle](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Storefrontdetails

Die Kontaktinformationen werden nur für die interne Kommunikation zwischen dem Partner und Microsoft verwendet. Hinweis: Es ist wichtig, eine E-Mail-Adresse zu verwenden, die in diesen Feldern überwacht wird. Wir verwenden diese E-Mail, um mit Ihnen über Ihre Fortschritte bei der Veröffentlichung auf AppSource zu kommunizieren. Für die Kunden wird nur die Support-URL angezeigt.
