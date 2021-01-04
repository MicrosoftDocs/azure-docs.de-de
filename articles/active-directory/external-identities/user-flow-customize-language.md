---
title: Sprachanpassung in Azure AD-Benutzerflows
description: Erfahren Sie mehr über das Anpassen der Oberfläche für die Sprache in Ihren Benutzerflows in Azure Active Directory.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cddb40a6a43c38079906699d9fa4865c4daf558
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837089"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Sprachanpassung in Azure Active Directory (Vorschau)

> [!NOTE]
> Die Self-Service-Registrierung ist eine öffentliche Previewfunktion von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit der Sprachanpassung in Azure Active Directory (Azure AD) kann Ihr Benutzerflow verschiedene Sprachen abdecken, um die Anforderungen Ihrer Benutzer zu erfüllen. Microsoft stellt Übersetzungen für [36 Sprachen](#supported-languages) bereit. Auch wenn Ihre Benutzeroberfläche nur für eine einzelne Sprache bestimmt ist, können Sie die Attributnamen auf der Attributerfassungsseite anpassen.

## <a name="how-language-customization-works"></a>Funktionsweise der Sprachanpassung

Die Sprachanpassung ist standardmäßig für Benutzer aktiviert, die sich registrieren. Dadurch soll eine konsistente Registrierungserfahrung sichergestellt werden. Sie können mithilfe von Sprachen die Zeichenfolgen anpassen, die Benutzern während der Registrierung im Rahmen der Attributerfassung angezeigt werden.

> [!NOTE]
> Falls Sie benutzerdefinierte Benutzerattribute verwenden, müssen Sie Ihre eigenen Übersetzungen angeben. Weitere Informationen finden Sie unter [Anpassen von Zeichenfolgen](#customize-your-strings).

## <a name="customize-your-strings"></a>Anpassen von Zeichenfolgen

Mit der Sprachanpassung können Sie alle Zeichenfolgen Ihres Benutzerflows anpassen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure Active Directory** aus.
3. Wählen Sie im Menü auf der linken Seite **Externe Identitäten** aus.
4. Wählen Sie **Benutzerflows (Vorschau)** aus.
3. Wählen Sie den Benutzerflow aus, den Sie für Übersetzungen aktivieren möchten.
4. Wählen Sie **Sprachen** aus.
5. Wählen Sie auf der Seite **Sprachen** für den Benutzerflow die Sprache aus, die Sie anpassen möchten.
6. Erweitern Sie die **Attributerfassungsseite**.
7. Klicken Sie auf **Standardeinstellungen herunterladen** (oder **Außerkraftsetzungen herunterladen**, wenn Sie diese Sprache zuvor bearbeitet haben).

Bei diesen Schritten erhalten Sie eine JSON-Datei, die Sie nutzen können, um mit dem Bearbeiten Ihrer Zeichenfolgen zu beginnen.

### <a name="change-any-string-on-the-page"></a>Ändern einer beliebigen Zeichenfolge auf der Seite

1. Öffnen Sie die JSON-Datei, die Sie im Rahmen der obigen Anleitung heruntergeladen haben, in einem JSON-Editor.
1. Suchen Sie nach dem Element, das Sie ändern möchten. Sie können nach der `StringId` der gewünschten Zeichenfolge oder nach dem Attribut `Value` suchen, das Sie ändern möchten.
1. Aktualisieren Sie das `Value`-Attribut mit den Daten, die angezeigt werden sollen.
1. Ändern Sie `Override` für jede Zeichenfolge, die Sie ändern möchten, in `true`.
1. Speichern Sie die Datei, und laden Sie Ihre Änderungen hoch. (Das Steuerelement zum Hochladen befindet sich dort, wo Sie auch die JSON-Datei heruntergeladen haben.)

> [!IMPORTANT]
> Stellen Sie beim Überschreiben einer Zeichenfolge sicher, dass Sie den Wert für `Override` auf `true` festlegen. Wenn der Wert nicht geändert wird, wird der Eintrag ignoriert.

### <a name="change-extension-attributes"></a>Ändern von Erweiterungsattributen

Wenn Sie die Zeichenfolge für ein benutzerdefiniertes Benutzerattribut ändern oder dem JSON-Code eine Zeichenfolge hinzufügen möchten, verwenden Sie das folgende Format:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Ersetzen Sie `<ExtensionAttribute>` durch den Namen Ihres benutzerdefinierten Benutzerattributs.

Ersetzen Sie `<ExtensionAttributeValue>` durch die neue Zeichenfolge, die angezeigt werden soll.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Angeben einer Liste mit Werten mithilfe von „LocalizedCollections“

Wenn Sie eine feste Liste mit Werten für Antworten bereitstellen möchten, müssen Sie ein Attribut vom Typ `LocalizedCollections` erstellen. Bei `LocalizedCollections` handelt es sich um ein Array mit Paaren aus `Name` und `Value`. Die Reihenfolge der Elemente ist dieselbe, in der sie angezeigt werden. Verwenden Sie zum Hinzufügen von `LocalizedCollections` das folgende Format:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` ist das Benutzerattribut, für das dieses Attribut vom Typ `LocalizedCollections` eine Antwort darstellt.
* `Name` ist der Wert, der dem Benutzer angezeigt wird.
* `Value` wird im Anspruch zurückgegeben, wenn diese Option ausgewählt wird.

### <a name="upload-your-changes"></a>Hochladen von Änderungen

1. Kehren Sie nach dem Ändern der JSON-Datei wieder zu Ihrem Mandanten zurück.
1. Wählen Sie **Benutzerflows** aus, und klicken Sie auf den Benutzerflow, den Sie für Übersetzungen aktivieren möchten.
1. Wählen Sie **Sprachen** aus.
1. Wählen Sie die gewünschte Zielsprache für die Übersetzung aus.
1. Wählen Sie **Attributerfassungsseite** aus.
1. Klicken Sie auf das Ordnersymbol, und wählen Sie die hochzuladende JSON-Datei aus.

Die Änderungen werden automatisch in Ihrem Benutzerflow gespeichert.

## <a name="additional-information"></a>Zusätzliche Informationen

### <a name="page-ui-customization-labels-as-overrides"></a>Bezeichnungen der Seite für die Benutzeroberflächenanpassung als Außerkraftsetzungen

Wenn Sie die Sprachanpassung aktivieren, werden Ihre bisherigen Bearbeitungen für Bezeichnungen, die Sie auf der Seite für die Benutzeroberflächenanpassung vorgenommen haben, in einer JSON-Datei für Englisch (en) gespeichert. Sie können Ihre Bezeichnungen und anderen Zeichenfolgen weiterhin ändern, indem Sie Sprachressourcen in die Sprachanpassung hochladen.

### <a name="up-to-date-translations"></a>Aktuelle Übersetzungen

Microsoft ist bemüht, Ihnen möglichst aktuelle Übersetzungen zur Verfügung zu stellen. Die Übersetzungen werden kontinuierlich optimiert und auf ihre Konformität überprüft. Microsoft ermittelt Fehler und Änderungen für die globale Terminologie und nimmt geeignete Aktualisierungen in Ihrem Benutzerflow vor.

### <a name="support-for-right-to-left-languages"></a>Unterstützung für von rechts nach links geschriebene Sprachen

Von rechts nach links geschriebene Sprachen werden von Microsoft derzeit nicht unterstützt. Dies erreichen Sie, indem Sie benutzerdefinierte Gebietsschemas verwenden und mithilfe von CSS ändern, wie Zeichenfolgen angezeigt werden. Sollten Sie dieses Feature benötigen, stimmen Sie im [Azure-Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag) dafür ab.

### <a name="social-identity-provider-translations"></a>Übersetzungen für den Fall „Soziales Netzwerk als Identitätsanbieter“

Für Anmeldungen per sozialem Netzwerk stellt Microsoft den OIDC-Parameter `ui_locales` bereit. Einige soziale Netzwerke, die als Identitätsanbieter fungieren, erkennen diesen jedoch nicht an. Dazu zählen auch Facebook und Google.

### <a name="browser-behavior"></a>Browserverhalten

Von Chrome und Firefox wird jeweils die eigene festgelegte Sprache angefordert. Wenn es sich dabei um eine unterstützte Sprache handelt, wird sie vor der Standardsprache angezeigt. Für Microsoft Edge wird derzeit keine Sprache angefordert, sondern es wird gleich die Standardsprache verwendet.

## <a name="supported-languages"></a>Unterstützte Sprachen

Azure AD bietet Unterstützung für die folgenden Sprachen. Sprachen für Benutzerflows werden von Azure AD bereitgestellt. Die Sprachen für Benachrichtigungen von Multi-Factor Authentication (MFA) werden von [Azure AD MFA](../authentication/concept-mfa-howitworks.md) bereitgestellt.

| Sprache              | Sprachcode | Benutzerabläufe         | MFA-Benachrichtigungen  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabisch                | ar            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Bulgarisch             | bg            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Bengalisch                | bn            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) |
| Katalanisch               | ca            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Tschechisch                 | cs            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Dänisch                | da            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Deutsch                | de            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Griechisch                 | el            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Englisch               | en            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Spanisch               | es            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Estnisch              | et            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Baskisch                | eu            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Finnisch               | fi            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Französisch                | fr            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Galizisch              | gl            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Gujarati              | gu            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) |
| Hebräisch                | he            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Hindi                 | hi            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Kroatisch              | hr            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Ungarisch             | hu            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Indonesisch            | id            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Italienisch               | it            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Japanisch              | ja            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Kasachisch                | kk            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Kannada               | kn            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) |
| Koreanisch                | ko            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Litauisch            | lt            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Lettisch               | lv            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Malayalam             | ml            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) |
| Marathi               | mr            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) |
| Malaiisch                 | ms            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Norwegisch Bokmål      | nb            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) |
| Niederländisch                 | nl            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Norwegisch             | nein            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Pandschabi               | pa            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) |
| Polnisch                | pl            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Portugiesisch (Brasilien)   | pt-br         | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Portugiesisch (Portugal) | pt-pt         | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Rumänisch              | ro            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Russisch               | ru            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Slowakisch                | sk            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Slowenisch             | sl            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Serbisch (Kyrillisch)    | sr-cryl-cs    | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Serbisch (Lateinisch)       | sr-latn-cs    | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Schwedisch               | sv            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Tamilisch                 | ta            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) |
| Telugu                | te            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) |
| Thailändisch                  | th            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Türkisch               | tr            | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Ukrainisch             | uk            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Vietnamesisch            | vi            | ![„X“ für „Nein“.](./media/user-flow-customize-language/no.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Chinesisch (vereinfacht)  | zh-hans       | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |
| Chinesisch (traditionell) | zh-hant       | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) | ![Grünes Häkchen.](./media/user-flow-customize-language/yes.png) |