---
title: Erstellen des Dynamics 365 for Operations-Angebots über das Cloudpartnerportal
description: Erstellen des Dynamics 365 for Operations-Angebots über das Cloudpartnerportal
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a9ada25641e2a56beb9083b145a507c8fd41a46f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935098"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>Erstellen des Dynamics 365 for Operations-Angebots über das Cloudpartnerportal

Das Veröffentlichungsportal bietet rollenbasierten Zugriff auf das Portal, sodass mehrere Personen hinsichtlich der Veröffentlichung eines Angebots zusammenarbeiten können. Weitere Informationen finden Sie unter [Verwalten von Benutzern im Cloudpartnerportal](./cloud-partner-portal-manage-users.md).

Bevor ein Angebot im Auftrag eines Herausgeberkontos veröffentlicht werden kann, muss eine der Personen mit \"Besitzer\"-Rolle bestätigen, dass die [Nutzungsbedingungen](https://azure.microsoft.com/support/legal/website-terms-of-use/), die [Datenschutzerklärung von Microsoft](https://www.microsoft.com/privacystatement/default.aspx) und die [Microsoft Azure Certified-Programmvereinbarung](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/) eingehalten werden.

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>Erstellen eines neuen Angebots für Dynamics 365 for Operations

Wenn alle Voraussetzungen erfüllt sind, können Sie mit der Erstellung Ihres Angebots für Dynamics 365 for Operations beginnen.

1. Melden Sie sich beim [Cloudpartnerportal](https://cloudpartner.azure.com/) an.
2. Klicken Sie in der Navigationsleiste links auf \"+ Neues Angebot\", und wählen Sie \"Dynamics 365 for Operations\" aus.
3. Eine neue \"Editor\"-Ansicht für Angebote wird geöffnet, und Sie können nun mit der Erstellung beginnen.
4. Die \"Formulare\", die ausgefüllt werden müssen, werden in der \"Editor\"-Ansicht auf der linken Seite angezeigt. Jedes \"Formular\" besteht aus einer Reihe von Feldern, die ausgefüllt werden müssen. Erforderliche Felder sind mit einem roten Sternchen gekennzeichnet (\*).

Es gibt vier Hauptformulare für die Erstellung von Angeboten für Dynamics 365 for Operations:

- Angebotseinstellungen
- Technische Informationen
- Storefrontdetails
- Kontakte

## <a name="how-to-fill-out-the-offer-settings-form"></a>So füllen Sie das Formular „Angebotseinstellungen“ aus

Das Formular für die Angebotseinstellungen ist ein grundlegendes Formular, in dem Sie die Angebotseinstellungen angeben. Die einzelnen Felder werden nachfolgend beschrieben.

### <a name="offer-id"></a>Angebots-ID

Dies ist ein eindeutiger Bezeichner für das Angebot innerhalb eines Herausgeberprofils. Diese ID wird in den Produkt-URLs angezeigt. Sie darf nur aus klein geschriebenen alphanumerischen Zeichen oder Bindestrichen (-) bestehen. Die ID darf nicht mit einem Bindestrich enden und darf höchstens 50 Zeichen umfassen. Beachten Sie, dass dieses Feld gesperrt wird, sobald ein Angebot online geschaltet wird.

Wenn z. B. der Herausgeber „Contoso“ ein Angebot mit der Angebots-ID *sample-dynamics365 for operations* veröffentlicht, wird diese in AppSource als `https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-dynamics365 for operations*?tab=Overview\` angezeigt.

### <a name="publisher-id"></a>Herausgeber-ID

In dieser Dropdownliste können Sie das Herausgeberprofil auswählen, unter dem das Angebot veröffentlicht werden soll. Beachten Sie, dass dieses Feld gesperrt wird, sobald ein Angebot online geschaltet wird.

NAME

Hierbei handelt es sich um den Anzeigenamen für Ihr Angebot. Dies ist der Name, der in [AppSource](https://appsource.microsoft.com) angezeigt wird. Er darf aus höchstens 50 Zeichen bestehen.

Klicken Sie auf \"Speichern\", um Ihre Eingaben zu speichern. Als Nächstes füllen Sie technische Informationen für Ihr Angebot aus.

## <a name="how-to-fill-out-the-technical-info-form"></a>Ausfüllen des Formulars „Technische Informationen“

Das Formular für technische Informationen enthält Informationen, die auf Ihrer Angebotsseite angezeigt werden. Im Folgenden werden die Anweisungen für die verschiedenen Felder beschrieben.

![Bildschirm „Neues Angebot“](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>Lösungsbezeichner

Zuerst kommt Ihr Lösungsbezeichner.

1. Um diesen Bezeichner zu finden, wechseln Sie zu Life Cycle Services und wählen Sie „Lösungsverwaltung“ aus.
2. Nach der Auswahl der entsprechenden Lösung wird in der Paketübersicht der Lösungsbezeichner angezeigt. \*\*Wenn der Bezeichner leer ist, wählen Sie „Bearbeiten“ aus und veröffentlichen Sie Ihr Paket erneut, damit der Lösungsbezeichner angezeigt wird.

### <a name="validation-assets"></a>Validierungsressourcen

Laden Sie hier Ihren CAR (Customization Analysis Reports, Berichte zur Anpassungsanalyse) hoch.

### <a name="does-solution-enable-translations"></a>Ermöglicht die Lösung die Übersetzung(en)?

Wählen Sie \'Ja\' oder \'Nein\' aus.

### <a name="does-solution-include-localizations"></a>Umfasst die Lösung die Lokalisierung(en)?

Wählen Sie \'Ja\' oder \'Nein\' aus.

### <a name="product-version"></a>Produktversion

Wählen Sie „Neue AX“ aus. Klicken Sie abschließend auf „Speichern“.

## <a name="how-to-fill-out-storefront-details-form"></a>Ausfüllen des Formulars mit den Storefront-Details

Zuerst kommen Ihre Angebotsdetails.

1. **Angebotszusammenfassung**

    \- Geben Sie eine kurze Zusammenfassung Ihrer Lösung ein (maximal 100 Zeichen).

2. **Beschreibung des Angebots**

    \- Geben Sie eine kurze Beschreibung Ihrer Lösung ein. Ihre Beschreibung sollte den funktionalen Speicherbedarf Ihrer Lösung aufweisen und direkt mit Ihrer BPM-Bibliothek ausgerichtet sein. Wenn Sie z. B. angeben, dass Sie über die Features x,y,z in Ihren Marketinginhalten verfügen, werden wir bei der abschließenden Überprüfung sicherstellen, dass diese in der BPM-Bibliothek im LCS dokumentiert sind.

![Bildschirm „Storefront-Details“](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>Auflistungsdetails

![Bildschirm „Storefront-Details“](./media/publish_d365_new_offer/storefront_details.png)

1. **Branchen**: Überprüfen Sie maximal zwei Branchen aus den angegebenen Optionen.
2. **Kategorien**: Überprüfen Sie maximal drei Kategorien aus den angegebenen Optionen.
3. **App-Typ**: Wählen Sie aus den angegebenen Optionen aus.
4. **Hilfe-Link für Ihre App**: Geben Sie den Hilfe-Link für Ihre Lösung ein.
5. **Unterstützte Länder/Regionen**: Überprüfen Sie anhand der angegebenen Optionen.
6. **Unterstützte Sprachen**: Überprüfen Sie anhand der angegebenen Optionen.
7. **App-Version**: Geben Sie die Version Ihrer Lösung ein, die herausgegeben wird. (Beispiel: 1.0.0.0)
8. **App-Herausgabedatum**: Geben Sie das Herausgabedatum Ihrer Lösung ein (mm/tt/jjjj).
9. **Produkte, mit denen Ihre App zusammenarbeitet**: Listen Sie die bestimmten Produkte auf, mit denen Ihre App zusammenarbeitet. Sie können bis zu drei Produkte auflisten. Um ein Produkt aufzulisten, klicken Sie auf das Pluszeichen (neben „Neu“) und es wird ein neues offenes Textfeld erstellt, in dem Sie den Namen eines Produkts eingeben können, mit dem Ihre App arbeitet.
10. **Suchbegriffe**: Geben Sie allgemeine Begriffe ein, die Benutzer verwenden können, um Ihre Lösung während einer Suche zu finden. \*\*Diese Suchbegriffe werden nicht im Marketplace angezeigt.
11. **Ausblendschlüssel**: Dies ist ein Schlüssel, der mit der Vorschau-URL des Angebots kombiniert wird, um es aus der öffentlichen Ansicht auszublenden. Es ist kein Kennwort. Sie können hier eine beliebige Zeichenfolge eingeben.

### <a name="marketing-artifacts"></a>Marketingartefakte

1. Als nächstes werden Ihre **Logos**, **Screenshots** hochgeladen. \*\*Beachten Sie die Größen für die einzelnen Uploads, und alle Bilder sollten im PNG-Format vorliegen.
2. **Demovideos**: Klicken Sie auf \"+Neu\". Laden Sie ein Demovideo Ihrer Lösung hoch (nur YouTube- oder Vimeo-Links).\*\*. Beachten Sie, dass Sie ein Miniaturbild in der angegebenen Größe hochladen sollten, damit Ihr Video im Stagingprozess angezeigt wird.
3. **Dokumente**: Laden Sie alle Dokumente hoch, die sich auf Ihre Lösung beziehen, und vergessen Sie nicht, einen Namen für das Dokument einzugeben.

### <a name="legal"></a>Rechtliche Hinweise

Diese Informationen verweisen auf Ihre Datenschutzrichtlinie und die Nutzungsbedingungen. Geben Sie die URL der Lösung zur Datenschutzrichtlinie und Ihren Nutzungsbedingungen ein. \*\*Der Kunde kann diese Richtlinien im Portal anzeigen.

### <a name="customer-support"></a>Kundendienst

Die Support-URL wird im Portal nur für Ihre Benutzer angezeigt.

### <a name="leads-management"></a>Leadsverwaltung

Wählen Sie ein CRM-System aus, in dem Ihr Lead gespeichert wird. Wählen Sie hier \"Azure-Tabelle\" aus, wenn Sie eines der folgenden CRM-Systeme haben: Salesforce, Marketo, Microsoft Dynamics CRM. Das hier von Ihnen ausgewählte CRM-System ist der Ort, an den wir Details zu den Endbenutzern schreiben, die Ihre App in AppSource testen (Leads). In Abhängigkeit von dem von Ihnen ausgewählten CRM-System klicken Sie unten auf die entsprechende URL, um Informationen darüber zu erhalten, wie Sie den nächsten Satz von Feldern ausfüllen können.

![Details zur Leadverwaltung](./media/publish_d365_new_offer/leads.png)

1. Die Azure-Tabelle finden Sie [hier](https://aka.ms/leadsettingforazuretable)
2. Informationen zu Dynamics CRM Online finden Sie [hier](https://aka.ms/leadsettingfordynamicscrm)
3. Informationen zu Marketo finden Sie [hier](https://aka.ms/leadsettingformarketo)
4. Informationen zu Salesforce finden Sie [hier](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>Ausfüllen des Formulars für Kontakte

Diese Informationen werden für Microsoft und den Kundendienst verwendet. Geben Sie den Engineering-Kontakt und den Kundendienst für Ihr Unternehmen sowie die Support-URL für Ihre Lösung ein. Diese Informationen werden bei Fragen von Microsoft zu Ihrer Lösung als zentrale Kontaktstelle und auch für den Kundendienst verwendet.

![Bildschirm „Angebotskontakte“](./media/publish_d365_new_offer/Contacts.png)
