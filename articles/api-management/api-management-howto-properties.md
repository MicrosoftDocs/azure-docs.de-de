---
title: Verwenden von benannten Werten in Azure API Management-Richtlinien
description: Erfahren Sie, wie benannte Werte in Azure API Management-Richtlinien verwendet werden. Benannte Werte können Literalzeichenfolgen, Richtlinienausdrücke und Geheimnisse enthalten, die in Azure Key Vault gespeichert sind.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 12/14/2020
ms.author: apimpm
ms.openlocfilehash: 344500d5635f591b34a45130c7dd6b63659ad84d
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491010"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Verwenden von benannten Werten in Azure API Management-Richtlinien

[API Management-Richtlinien](api-management-howto-policies.md) sind eine leistungsfähige Funktion des Systems, mit der Herausgeber das Verhalten der API über eine Konfiguration ändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Richtlinienanweisungen können mithilfe von literalen Textwerten, Richtlinienausdrücken und benannten Werten erstellt werden.

*Benannte Werte* sind eine globale Sammlung von Name-Wert-Paaren in jeder API Management-Instanz. Es gibt keine Beschränkung für die Anzahl der Elemente in der Sammlung. Benannte Werte können zum Verwalten konstanter Zeichenfolgenwerte und Geheimnisse für alle API-Konfigurationen und -Richtlinien verwendet werden. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Benannte Werte im Azure-Portal":::

## <a name="value-types"></a>Werttypen

|type  |BESCHREIBUNG  |
|---------|---------|
|Plain     |  Literalzeichenfolge oder Richtlinienausdruck     |
|`Secret`     |   Literalzeichenfolge oder Richtlinienausdruck (durch API Management verschlüsselt)      |
|[Key vault](#key-vault-secrets)     |  Bezeichner eines Geheimnisses, das in Azure Key Vault gespeichert ist.      |

Plain-Werte oder -Geheimnisse können [Richtlinienausdrücke](./api-management-policy-expressions.md) enthalten. Der Ausdruck `@(DateTime.Now.ToString())` gibt z. B. eine Zeichenfolge zurück, die das aktuelle Datum und die Uhrzeit enthält.

Ausführliche Informationen zu den Attributen benannter Werte finden Sie in der [REST-API-Referenz](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate) von API Management.

## <a name="key-vault-secrets"></a>Key Vault-Geheimnisse

Geheimniswerte können entweder als verschlüsselte Zeichenfolgen in API Management (benutzerdefinierte Geheimnisse) oder durch Verweisen auf Geheimnisse in [Azure Key Vault](../key-vault/general/overview.md) gespeichert werden. 

Die Verwendung von Schlüsseltresorgeheimnissen wird empfohlen, da dadurch die Sicherheit von API Management verbessert wird:

* In Schlüsseltresoren gespeicherte Geheimnisse können für mehrere Dienste verwendet werden.
* Auf die Geheimnisse können präzise [Zugriffsrichtlinien](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) angewandt werden.
* Wenn Geheimnisse im Schlüsseltresor aktualisiert werden, erfolgt in API Management automatisch eine Schlüsselrotation. Nach der Aktualisierung im Schlüsseltresor wird ein benannter Wert innerhalb von 4 Stunden in API Management aktualisiert. Sie können das Geheimnis auch manuell im Azure-Portal oder über die Verwaltungs-REST-API aktualisieren.

### <a name="prerequisites-for-key-vault-integration"></a>Voraussetzungen für die Key Vault-Integration

1. Schritte zum Erstellen eines Schlüsseltresors finden Sie unter [Schnellstart: Erstellen eines Schlüsseltresors über das Azure-Portal](../key-vault/general/quick-create-portal.md).
1. Aktivieren Sie eine system- oder benutzerseitig zugewiesene [verwaltete Identität](api-management-howto-use-managed-service-identity.md) in der API Management-Instanz.
1. Weisen Sie der verwalteten Identität [Key Vault-Zugriffsrichtlinien](../key-vault/general/assign-access-policy-portal.md) mit Berechtigungen zum Abrufen und Auflisten von Geheimnissen im Tresor zu. So fügen Sie die Richtlinie hinzu
    1. Navigieren Sie im Portal zu Ihrem Schlüsseltresor.
    1. Wählen Sie **Einstellungen > Zugriffsrichtlinien > + Zugriffsrichtlinie hinzufügen** aus.
    1. Wählen Sie **Geheimnisberechtigungen** und dann **Abrufen** und **Auflisten** aus.
    1. Wählen Sie unter **Prinzipal auswählen** den Ressourcennamen der verwalteten Identität aus. Wenn Sie eine systemseitig zugewiesene Identität verwenden, ist der Prinzipal der Name der API Management-Instanz.
1. Erstellen Sie ein Geheimnis im Schlüsseltresor, oder importieren Sie es. Weitere Informationen finden Sie unter [Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe des Azure-Portals](../key-vault/secrets/quick-create-portal.md).

Um das Schlüsseltresorgeheimnis zu verwenden, müssen Sie [einen benannten Wert hinzufügen oder bearbeiten](#add-or-edit-a-named-value) und einen Typ von **Schlüsseltresor** angeben. Wählen Sie das Geheimnis im Schlüsseltresor aus.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Hinzufügen oder Bearbeiten benannter Werte

### <a name="add-a-key-vault-secret"></a>Hinzufügen von Schlüsseltresorgeheimnissen

Weitere Informationen finden Sie unter [Voraussetzungen für die Key Vault-Integration](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Wenn Sie in API Management ein Geheimnis aus einem Schlüsseltresor verwenden, dürfen Sie auf keinen Fall das Geheimnis, den Schlüsseltresor oder die verwaltete Identität löschen, die für den Zugriff auf den Schlüsseltresor verwendet wird.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie unter **APIs** die Optionen **Benannte Werte** >  **+ Hinzufügen** aus.
1. Geben Sie als Bezeichner einen **Namen** sowie einen **Anzeigenamen** ein, der zum Verweisen auf die Eigenschaft in Richtlinien verwendet wird.
1. Wählen Sie unter **Werttyp** die Option **Key Vault** aus.
1. Geben Sie den Bezeichner eines Schlüsseltresorgeheimnisses (ohne Version) ein, oder wählen Sie **Auswählen** aus, um ein Geheimnis aus einem Schlüsseltresor auszuwählen.
    > [!IMPORTANT]
    > Wenn Sie selbst einen Bezeichner für ein Schlüsseltresorgeheimnis eingeben, stellen Sie sicher, dass dieser keine Versionsinformationen enthält. Andernfalls wird das Geheimnis nach einer Aktualisierung im Schlüsseltresor nicht automatisch in API Management rotiert.
1. Wählen Sie unter **Clientidentität** eine systemseitig zugewiesene oder eine vorhandene benutzerseitig zugewiesene verwaltete Identität aus. Erfahren Sie, wie Sie [verwaltete Identitäten in Ihrem API Management-Dienst hinzufügen oder bearbeiten](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Die Identität benötigt Berechtigungen zum Abrufen und Auflisten von Geheimnissen im Schlüsseltresor. Wenn Sie den Zugriff auf den Schlüsseltresor noch nicht konfiguriert haben, werden Sie von API Management dazu aufgefordert. Der Dienst kann die Identität dann automatisch mit den erforderlichen Berechtigungen konfigurieren.
1. Fügen Sie ein (oder mehrere) optionales Tag hinzu, um die benannten Werte zu organisieren, und wählen Sie dann **Speichern** aus.
1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Hinzufügen eines Schlüsseltresorgeheimnisses":::

### <a name="add-a-plain-or-secret-value"></a>Hinzufügen eines Plain- oder Geheimniswerts

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie unter **APIs** die Optionen **Benannte Werte** >  **+ Hinzufügen** aus.
1. Geben Sie als Bezeichner einen **Namen** sowie einen **Anzeigenamen** ein, der zum Verweisen auf die Eigenschaft in Richtlinien verwendet wird.
1. Wählen Sie unter **Werttyp** entweder **Plain** oder **Geheimnis** aus.
1. Geben Sie unter **Wert** eine Zeichenfolge oder einen Richtlinienausdruck ein.
1. Fügen Sie ein (oder mehrere) optionales Tag hinzu, um die benannten Werte zu organisieren, und wählen Sie dann **Speichern** aus.
1. Klicken Sie auf **Erstellen**.

Nachdem der benannte Wert erstellt wurde, können Sie ihn bearbeiten, indem Sie den Namen auswählen. Wenn Sie den Anzeigenamen ändern, werden alle Richtlinien, die auf diesen benannten Wert verweisen, automatisch aktualisiert, sodass sie den neuen Anzeigenamen verwenden.

## <a name="use-a-named-value"></a>Verwenden benannter Werte

In den Beispielen in diesem Abschnitt werden benannte Werte aus der folgenden Tabelle verwendet.

| Name               | Wert                      | `Secret` | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Falsch  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Falsch  | 

Um einen benannten Wert in einer Richtlinie zu verwenden, setzen Sie seinen Anzeigenamen in ein doppeltes Paar geschweifter Klammern (z. B. `{{ContosoHeader}}`), wie im folgenden Beispiel gezeigt:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In diesem Beispiel dient `ContosoHeader` als Name eines Headers in einer `set-header`-Richtlinie und `ContosoHeaderValue` als Wert dieses Headers. Wenn diese Richtlinie in einer Anforderung oder Antwort an das API Management-Gateway ausgewertet wird, werden `{{ContosoHeader}}` und `{{ContosoHeaderValue}}` durch ihre jeweiligen Werte ersetzt.

Benannte Werte können als vollständige Attribut- oder Elementwerte verwendet werden, wie im vorherigen Beispiel gezeigt, sie können aber auch in einen literalen Textausdruck eingefügt oder mit einem Teil davon kombiniert werden, wie im folgenden Beispiel gezeigt wird:  

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Benannte Werte können auch Richtlinienausdrücke enthalten. Im folgenden Beispiel wird der Ausdruck `ExpressionProperty` verwendet.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Beim Auswerten dieser Richtlinie wird `{{ExpressionProperty}}` durch ihren Wert ersetzt: `@(DateTime.Now.ToString())`. Da der Wert ein Richtlinienausdruck ist, wird der Ausdruck ausgewertet, und die Ausführung der Richtlinie wird fortgesetzt.

Sie können dies im Azure-Portal oder im [Entwicklerportal](api-management-howto-developer-portal.md) durch Aufrufen eines Vorgangs testen, der eine Richtlinie mit entsprechenden benannten Werten aufweist. Im folgenden Beispiel wird ein Vorgang mit den beiden vorherigen `set-header`-Beispielrichtlinien mit benannten Werten aufgerufen. Beachten Sie, dass die Antwort zwei benutzerdefinierte Header enthält, die mithilfe von Richtlinien mit benannten Werten konfiguriert wurden.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="API-Antwort auf den Test":::

Beim Anzeigen der [API-Ablaufverfolgung](api-management-howto-api-inspector.md) für einen Aufruf, der die beiden vorherigen Beispielrichtlinien mit benannten Werten enthält, sehen Sie beide `set-header`-Richtlinien mit den eingefügten benannten Werten und die Auswertung des Richtlinienausdrucks für den benannten Wert, der den Richtlinienausdruck enthält.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="Verfolgung mit dem API-Inspektor":::

> [!CAUTION]
> Wenn eine Richtlinie auf ein Geheimnis in Azure Key Vault verweist, wird der Wert aus dem Schlüsseltresor Benutzern angezeigt, die Zugriff auf Abonnements haben, in denen die [API-Ablaufverfolgung für Anforderungen](api-management-howto-api-inspector.md) aktiviert ist.

Benannte Werte können zwar Richtlinienausdrücke, aber keine anderen benannten Werte enthalten. Wenn für einen Wert, z. B. `Text: {{MyProperty}}`, Text verwendet wird, der einen Verweis auf einen benannten Wert enthält, wird dieser Verweis nicht aufgelöst und ersetzt.

## <a name="delete-a-named-value"></a>Löschen benannter Werte

Wenn Sie einen benannten Wert löschen möchten, wählen Sie den Namen und dann im Kontextmenü ( **...** ) die Option **Löschen** aus.

> [!IMPORTANT]
> Wenn Richtlinien in API Management auf den benannten Wert verweisen, können Sie ihn erst löschen, nachdem Sie den benannten Wert aus allen Richtlinien entfernt haben, die ihn verwenden.

## <a name="next-steps"></a>Nächste Schritte

-   Weitere Informationen zum Arbeiten mit Richtlinien
    -   [Richtlinien in Azure API Management](api-management-howto-policies.md)
    -   [Gruppenrichtlinienreferenz](./api-management-policies.md)
    -   [Richtlinienausdrücke](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

