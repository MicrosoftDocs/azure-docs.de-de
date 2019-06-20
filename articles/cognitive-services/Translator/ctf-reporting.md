---
title: Erstellen von Berichten mit dem Collaborative Translation Framework (CTF) – Textübersetzungs-API
titlesuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie die CTF-Berichterstellung (Collaborative Translation Framework) verwenden.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-pawal
ms.openlocfilehash: 166c152828a91889d7d1d7eb6f8c03dac48172f5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389388"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Verwenden der CTF-Berichterstellung (Collaborative Translation Framework)

> [!NOTE]
> Diese Methode ist veraltet. Sie ist unter V3.0 der Textübersetzungs-API nicht verfügbar.
> 
> Das Collaborative Translation Framework (CTF), das für V2.0 der Textübersetzungs-API verfügbar war, gilt ab dem 1. Februar 2018 als veraltet. Mit den Funktionen AddTranslation und AddTranslationArray konnten Benutzer Korrekturen über das Collaborative Translation Framework aktivieren. Nach dem 31. Januar 2018 werden für diese beiden Funktionen keine neuen Satzübermittlungen mehr akzeptiert, und Benutzer erhalten eine Fehlermeldung. Diese Funktionen wurden eingestellt und werden nicht durch neue Funktionen ersetzt.

Die Berichterstellungs-API für das Collaborative Translation Framework (CTF) gibt Statistiken und den tatsächlichen Inhalt im CTF-Speicher zurück. Diese API unterscheidet sich aus folgenden Gründen von der GetTranslations()-Methode:
* Gibt den übersetzten Inhalt und die zugehörige Gesamtzahl nur über Ihr Konto zurück (appId oder Azure Marketplace-Konto).
* Gibt den übersetzten Inhalt und die zugehörige Gesamtzahl zurück, ohne dass eine Übereinstimmung des Ausgangssatzes erforderlich ist.
* Gibt nicht die automatische Übersetzung (maschinelle Übersetzung) zurück.

## <a name="endpoint"></a>Endpunkt
Der Endpunkt der CTF-Berichterstellungs-API lautet https://api.microsofttranslator.com/v2/beta/ctfreporting.svc.


## <a name="methods"></a>Methoden
| NAME |    BESCHREIBUNG|
|:---|:---|
| GetUserTranslationCounts-Methode | Ruft die Anzahl von Übersetzungen ab, die vom Benutzer erstellt werden. |
| GetUserTranslations-Methode | Ruft die Übersetzungen ab, die vom Benutzer erstellt werden. |

Diese Methoden ermöglichen Ihnen Folgendes:
* Abrufen des vollständigen Satzes mit Benutzerübersetzungen und Korrekturen unter Ihrer Konto-ID als Download
* Abrufen der Liste mit den Mitwirkenden mit häufigen Beiträgen (Stellen Sie sicher, dass in AddTranslation() der richtige Benutzername angegeben wird.)
* Erstellen einer Benutzeroberfläche, über die Ihre vertrauenswürdigen Benutzer alle verfügbaren Kandidaten anzeigen können (ggf. basierend auf dem URI-Präfix auf einen Teilbereich Ihrer Site beschränkt)

> [!NOTE]
> Beide Methoden sind relativ langsam und teuer. Es wird empfohlen, sie eher selten zu verwenden.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts-Methode

Mit dieser Methode wird die Anzahl von Übersetzungen abgerufen, die vom Benutzer erstellt werden. Sie gibt die Liste mit der Übersetzungsanzahl gruppiert nach den Anforderungsparametern „uriPrefix“, „from“, „to“, „user“, „minRating“ und „maxRating“ an.

**Syntax**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parameter**

| Parameter | BESCHREIBUNG |
|:---|:---|
| appId | **Erforderlich** Lassen Sie das Feld „appId“ leer, wenn sie den Autorisierungsheader verwenden. Geben Sie andernfalls eine Zeichenfolge mit „Bearer“ + „ “ + Zugriffstoken an.|
| uriPrefix | **Optional** Eine Zeichenfolge, die das Präfix des URI für die Übersetzung enthält.|
| from | **Optional** Eine Zeichenfolge, die den Sprachcode des Übersetzungstexts darstellt. |
| zu | **Optional** Eine Zeichenfolge, die den Code der Sprache darstellt, in die der Text übersetzt werden soll.|
| minRating| **Optional** Ein Integerwert, der für den niedrigsten Qualitätsgrad des übersetzten Texts steht. Die gültigen Werte reichen hierbei von -10 bis 10. Der Standardwert ist 1.|
| maxRating| **Optional** Ein Integerwert, der für den höchsten Qualitätsgrad des übersetzten Texts steht. Die gültigen Werte reichen hierbei von -10 bis 10. Der Standardwert ist 1.|
| user | **Optional** Eine Zeichenfolge, die zum Filtern des Ergebnisses basierend auf dem Absender der Übermittlung verwendet wird. |
| category| **Optional** Eine Zeichenfolge, die die Kategorie oder Domäne der Übersetzung enthält. Dieser Parameter unterstützt nur die allgemeine Standardoption.|
| minDateUtc| **Optional** Das Datum, ab dem Sie die Übersetzungen abrufen möchten. Das Datum muss im UTC-Format angegeben werden. |
| maxDateUtc| **Optional** Das Datum, bis zu dem Sie die Übersetzungen abrufen möchten. Das Datum muss im UTC-Format angegeben werden. |
| skip| **Optional** Die Anzahl von Ergebnissen, um die Sie auf einer Seite weiterspringen möchten. Wenn Sie beispielsweise die ersten 20 Zeilen mit Ergebnissen überspringen und die Daten ab dem 21. Ergebniseintrag anzeigen möchten, geben Sie für diesen Parameter 20 an. Der Standardwert für diesen Parameter ist 0.|
| take | **Optional** Die Anzahl von Ergebnissen, die Sie abrufen möchten. Die maximale Anzahl für eine Anforderung beträgt 100. Der Standardwert ist 100.|

> [!NOTE]
> Mit den Anforderungsparametern „skip“ und „take“ können Sie die Paginierung für eine große Anzahl von Ergebniseinträgen durchführen.

**Rückgabewert**

Das Resultset enthält ein Array mit dem **UserTranslationCount**-Element. Jedes UserTranslationCount-Element enthält die folgenden Elemente:

| Feld | BESCHREIBUNG |
|:---|:---|
| Count| Anzahl von abgerufenen Ergebnissen|
| From | Quellsprache|
| Rating| Bewertung, die vom Übermittler im AddTranslation()-Methodenaufruf angewendet wird|
| To| Zielsprache|
| Uri| Im AddTranslation()-Methodenaufruf angewendeter URI|
| Benutzer| Benutzername|

**Ausnahmen**

| Ausnahme | `Message` | Bedingungen |
|:---|:---|:---|
| ArgumentOutOfRangeException | Der Parameter **maxDateUtc** muss größer oder gleich **minDateUtc** sein.| Der Wert des Parameters **maxDateUtc** ist kleiner als der Wert des Parameters **minDateUtc**.|
| TranslateApiException | Die IP-Adresse überschreitet das Kontingent.| <ul><li>Der Grenzwert für die Anzahl von Anforderungen pro Minute wurde erreicht.</li><li>Die Anforderungsgröße bleibt auf 10.000 Zeichen beschränkt.</li><li>Durch ein stündliches und tägliches Kontingent wird die Anzahl von Zeichen beschränkt, die von der Microsoft Translator-API akzeptiert werden.</li></ul>|
| TranslateApiException | AppId überschreitet das Kontingent.| Für die Anwendungs-ID wird das stündliche oder tägliche Kontingent überschritten.|

> [!NOTE]
> Das Kontingent wird angepasst, um für alle Benutzer des Diensts eine faire Verteilung sicherzustellen.

**Anzeigen von Codebeispielen auf GitHub**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations-Methode

Mit dieser Methode werden die Übersetzungen abgerufen, die vom Benutzer erstellt werden. Sie gibt die Übersetzungen gruppiert nach den Anforderungsparametern „uriPrefix“, „from“, „to“, „user“ und „minRating“ bzw. „maxRating“ an.

**Syntax**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parameter**

| Parameter | BESCHREIBUNG |
|:---|:---|
| appId | **Erforderlich** Lassen Sie das Feld „appId“ leer, wenn sie den Autorisierungsheader verwenden. Geben Sie andernfalls eine Zeichenfolge mit „Bearer“ + „ “ + Zugriffstoken an.|
| uriPrefix| **Optional** Eine Zeichenfolge, die das Präfix des URI für die Übersetzung enthält.|
| from| **Optional** Eine Zeichenfolge, die den Sprachcode des Übersetzungstexts darstellt.|
| zu| **Optional** Eine Zeichenfolge, die den Code der Sprache darstellt, in die der Text übersetzt werden soll.|
| minRating| **Optional** Ein Integerwert, der für den niedrigsten Qualitätsgrad des übersetzten Texts steht. Die gültigen Werte reichen hierbei von -10 bis 10. Der Standardwert ist 1.|
| maxRating| **Optional** Ein Integerwert, der für den höchsten Qualitätsgrad des übersetzten Texts steht. Die gültigen Werte reichen hierbei von -10 bis 10. Der Standardwert ist 1.|
| user| **Optional. Eine Zeichenfolge, die zum Filtern des Ergebnisses basierend auf dem Absender der Übermittlung verwendet wird.**|
| category| **Optional** Eine Zeichenfolge, die die Kategorie oder Domäne der Übersetzung enthält. Dieser Parameter unterstützt nur die allgemeine Standardoption.|
| minDateUtc| **Optional** Das Datum, ab dem Sie die Übersetzungen abrufen möchten. Das Datum muss im UTC-Format angegeben werden.|
| maxDateUtc| **Optional** Das Datum, bis zu dem Sie die Übersetzungen abrufen möchten. Das Datum muss im UTC-Format angegeben werden.|
| skip| **Optional** Die Anzahl von Ergebnissen, um die Sie auf einer Seite weiterspringen möchten. Wenn Sie beispielsweise die ersten 20 Zeilen mit Ergebnissen überspringen und die Daten ab dem 21. Ergebniseintrag anzeigen möchten, geben Sie für diesen Parameter 20 an. Der Standardwert für diesen Parameter ist 0.|
| take| **Optional** Die Anzahl von Ergebnissen, die Sie abrufen möchten. Die maximale Anzahl für eine Anforderung beträgt 100. Der Standardwert ist 50.|

> [!NOTE]
> Mit den Anforderungsparametern „skip“ und „take“ können Sie die Paginierung für eine große Anzahl von Ergebniseinträgen durchführen.

**Rückgabewert**

Das Resultset enthält ein Array mit dem **UserTranslation**-Element. Jedes UserTranslation-Element enthält die folgenden Elemente:

| Feld | BESCHREIBUNG |
|:---|:---|
| CreatedDateUtc| Erstellungsdatum des Eintrags mit AddTranslation()|
| From| Quellsprache|
| OriginalText| Quellsprachentext, der beim Übermitteln der Anforderung verwendet wird|
|Rating |Bewertung, die vom Übermittler im AddTranslation()-Methodenaufruf angewendet wird|
|To|    Zielsprache|
|TranslatedText|    Übersetzung wurde im AddTranslation()-Methodenaufruf übermittelt|
|Uri|   Im AddTranslation()-Methodenaufruf angewendeter URI|
|Benutzer   |Benutzername|

**Ausnahmen**

| Ausnahme | `Message` | Bedingungen |
|:---|:---|:---|
| ArgumentOutOfRangeException | Der Parameter **maxDateUtc** muss größer oder gleich **minDateUtc** sein.| Der Wert des Parameters **maxDateUtc** ist kleiner als der Wert des Parameters **minDateUtc**.|
| TranslateApiException | Die IP-Adresse überschreitet das Kontingent.| <ul><li>Der Grenzwert für die Anzahl von Anforderungen pro Minute wurde erreicht.</li><li>Die Anforderungsgröße bleibt auf 10.000 Zeichen beschränkt.</li><li>Durch ein stündliches und tägliches Kontingent wird die Anzahl von Zeichen beschränkt, die von der Microsoft Translator-API akzeptiert werden.</li></ul>|
| TranslateApiException | AppId überschreitet das Kontingent.| Für die Anwendungs-ID wird das stündliche oder tägliche Kontingent überschritten.|

> [!NOTE]
> Das Kontingent wird angepasst, um für alle Benutzer des Diensts eine faire Verteilung sicherzustellen.

**Anzeigen von Codebeispielen auf GitHub**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
