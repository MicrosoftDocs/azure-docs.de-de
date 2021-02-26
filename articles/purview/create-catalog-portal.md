---
title: 'Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal (Vorschauversion)'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie ein Azure Purview-Konto erstellen und Berechtigungen für dessen Verwendung konfigurieren.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: 82b72567a0db4dc9dbff9128402d0ee9f40f3512
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094150"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal

> [!IMPORTANT]
> Azure Purview ist derzeit als VORSCHAUVERSION verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In dieser Schnellstartanleitung wird ein Azure Purview-Konto erstellt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ihr eigener [Azure Active Directory-Mandant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Ihr Konto muss zum Erstellen von Ressourcen im Abonnement berechtigt sein.

* Wenn durch **Azure Policy** alle Anwendungen an der Erstellung eines **Speicherkontos** und eines **EventHub-Namespace** gehindert werden, benötigen Sie eine Richtlinienausnahme mit einem Tag, das im Rahmen der Erstellung eines Purview-Kontos eingegeben werden kann. Das liegt in erster Linie daran, dass für ein Purview-Konto jeweils eine verwaltete Ressourcengruppe mit einem darin enthaltenen Speicherkonto und einem EventHub-Namespace erstellt werden muss.

    > [!important]
    > Sie müssen diesen Schritt nicht ausführen, wenn Sie nicht über Azure Policy verfügen oder eine vorhandene Azure Policy-Instanz die Erstellung eines **Speicherkontos** und **EventHub-Namespaces** nicht blockiert.

    1. Suchen Sie im Azure-Portal nach **Policy**.
    1. Gehen Sie wie unter [Tutorial: Erstellen einer benutzerdefinierten Richtliniendefinition](../governance/policy/tutorials/create-custom-policy-definition.md) beschrieben vor, oder ändern Sie die vorhandene Richtlinie, um zwei Ausnahmen mit dem Operator `not` und dem Tag `resourceBypass` hinzuzufügen:

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > Das Tag kann einen beliebigen Wert (mit Ausnahme von `resourceBypass`) haben und von Ihnen später im Rahmen der Purview-Erstellung definiert werden. Es muss allerdings von der Richtlinie erkannt werden können.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="Screenshot: Erstellen der Richtliniendefinition":::

    1. [Erstellen Sie eine Richtlinienzuweisung](../governance/policy/assign-policy-portal.md) mit der erstellten benutzerdefinierten Richtlinie.

        [ ![Screenshot: Erstellen der Richtlinienzuweisung](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

## <a name="configure-your-subscription"></a>Konfigurieren Ihres Abonnements

Führen Sie bei Bedarf die folgenden Schritte aus, um Ihr Abonnement so zu konfigurieren, dass Azure Purview darunter ausgeführt werden kann:

   1. Suchen Sie im Azure-Portal nach dem Eintrag **Abonnements**, und wählen Sie ihn aus.

   1. Wählen Sie in der Abonnementliste das gewünschte Abonnement aus. Für das Abonnement wird Administratorzugriff benötigt.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Screenshot: Auswählen eines Abonnements im Azure-Portal":::

   1. Wählen Sie für Ihr Abonnement die Option **Ressourcenanbieter** aus. Suchen Sie im Bereich **Ressourcenanbieter** nach den drei folgenden Ressourcenanbietern, und registrieren Sie sie: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Sollten sie nicht registriert sein, wählen Sie **Registrieren** aus, um sie zu registrieren.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Screenshot: Registrieren des Ressourcenanbieters „Microsoft.Purview“ im Azure-Portal":::

## <a name="create-an-azure-purview-account-instance"></a>Erstellen einer Azure Purview-Kontoinstanz

1. Navigieren Sie im Azure-Portal zur Seite **Purview accounts** (Purview-Konten), und wählen Sie **Hinzufügen** aus, um ein neues Azure Purview-Konto zu erstellen. Alternativ können Sie auch zur Marketplace-Suche für **Purview-Konten** navigieren und **Erstellen** auswählen. Beachten Sie, dass immer nur jeweils ein Azure Purview-Konto hinzugefügt werden kann.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Screenshot: Erstellen einer Azure Purview-Kontoinstanz im Azure-Portal":::

    > [!Note] 
    > Das Konto von Azure Purview kann nicht in eine andere Region verschoben werden. Weitere Informationen hierzu finden Sie auf der [Seite mit den von Azure unterstützten Diensten](https://docs.microsoft.com/azure/azure-resource-manager/management/region-move-support).

1. Gehen Sie auf dem Blatt **Basiseinstellungen** wie folgt vor:
    1. Wählen Sie eine **Ressourcengruppe** aus.
    1. Geben Sie einen **Purview-Kontonamen** für Ihren Katalog ein. Leerzeichen und Symbole sind nicht zulässig.
    1. Wählen Sie einen **Standort** und anschließend **Weiter: Konfiguration** aus.
1. Wählen Sie auf der Registerkarte **Konfiguration** die gewünschte **Plattformgröße** aus. Zulässig sind vier Kapazitätseinheiten (Capacity Units, CUs) und 16 CUs. Klicken Sie auf **Weiter: Tags**.
1. Auf der Registerkarte **Tags** können Sie optional Tags hinzufügen. Diese Tags sind nur für die Verwendung im Azure-Portal vorgesehen, nicht in Azure Purview. 

    > [!Note] 
    > Wenn Sie über **Azure Policy** verfügen und wie in den **Voraussetzungen** erwähnt eine Ausnahme hinzufügen müssen, müssen Sie das richtige Tag hinzufügen. Sie können beispielsweise das Tag `resourceBypass` hinzufügen: :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Hinzufügen eines Tags zum Purview-Konto":::

1. Wählen Sie **Überprüfen und erstellen** und anschließend **Erstellen** aus. Die Erstellung dauert einige Minuten. Die neu erstellte Azure Purview-Kontoinstanz wird in der Liste auf der Seite **Purview accounts** (Purview-Konten) angezeigt.
1. Warten Sie, bis die Bereitstellung des neuen Kontos abgeschlossen ist, und wählen Sie dann **Zu Ressource wechseln** aus.

    > [!Note]
    > Sollte bei der Bereitstellung ein Fehler mit dem Status `Conflict` auftreten, wird Purview durch eine Azure-Richtlinie an der Erstellung eines **Speicherkontos** und eines **EventHub-Namespace** gehindert. Gehen Sie wie in den **Voraussetzungen** beschrieben vor, um Ausnahmen hinzuzufügen.
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Purview-Konfliktfehlermeldung":::

1. Wählen Sie **Launch purview account** (Purview-Konto starten) aus.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Screenshot: Auswählen der Option zum Starten des Azure Purview-Kontokatalogs":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Hinzufügen eines Sicherheitsprinzipals zu einer Datenebenenrolle

Damit Azure Purview von Ihnen selbst oder von Ihrem Team verwendet werden kann, muss einer der vordefinierten Datenebenenrollen mindestens ein Sicherheitsprinzipal hinzugefügt werden: **Datenleseberechtigter für Purview**, **Datenkurator für Purview** oder **Datenquellenadministrator für Purview**. Weitere Informationen zu Azure Purview-Datenkatalogberechtigungen finden Sie unter [Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview](catalog-permissions.md).

So fügen Sie der Datenebenenrolle **Datenkurator für Purview** in einem Azure Purview-Konto einen Sicherheitsprinzipal hinzu:

1. Navigieren Sie im Azure-Portal zur Seite [**Purview accounts**](https://aka.ms/purviewportal) (Purview-Konten).

1. Wählen Sie das Azure Purview-Konto aus, das Sie ändern möchten.

1. Wählen Sie auf der Seite **Purview account** (Purview-Konto) die Registerkarte **Zugriffssteuerung (IAM)** aus.

1. Klicken Sie auf **+ Hinzufügen**.

Sollten nach dem Klicken auf „Hinzufügen“ zwei Optionen angezeigt werden, die beide mit „(deaktiviert)“ gekennzeichnet sind, verfügen Sie nicht über die nötigen Berechtigungen, um jemanden einer Datenebenenrolle im Azure Purview-Konto hinzuzufügen. Wenden Sie sich in diesem Fall an einen Besitzer, an einen Benutzerzugriffsadministrator oder an eine andere Person, die über die nötige Autorität verfügt, um Rollen in Ihrem Azure Purview-Konto zuzuweisen. Sie können nach geeigneten Personen suchen, indem Sie auf der Registerkarte **Rollenzuweisungen** nach unten scrollen, um einen Besitzer oder Benutzerzugriffsadministrator zu finden, an den Sie sich wenden können.

1. Wählen Sie **Rollenzuweisung hinzufügen** aus.

1. Geben Sie für den Rollentyp der Rolle **Datenkurator für Purview** oder **Datenquellenadministrator für Purview** einen entsprechenden Wert an – abhängig davon, wofür der Sicherheitsprinzipal verwendet werden soll (weitere Informationen finden Sie unter [Katalogberechtigungen](catalog-permissions.md) und [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)).

1. Behalten Sie unter **Zugriff zuweisen zu** den Standardwert **Benutzer, Gruppe oder Dienstprinzipal** bei.

1. Geben Sie unter **Auswählen** den Namen des zuzuweisenden Benutzers, der zuzuweisenden Azure Active Directory-Gruppe oder des zuzuweisenden Dienstprinzipals ein, und klicken Sie anschließend im Ergebnisbereich auf den entsprechenden Namen.

1. Klicken Sie auf **Speichern**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie dieses Azure Purview-Konto nicht mehr benötigen, löschen Sie es wie folgt:

1. Navigieren Sie im Azure-Portal zur Seite **Purview accounts** (Purview-Konten).

2. Wählen Sie das Azure Purview-Konto aus, das Sie zu Beginn dieser Schnellstartanleitung erstellt haben. Wählen Sie **Löschen** aus, geben Sie den Namen des Kontos ein, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Purview-Konto erstellen.

Im nächsten Artikel erfahren Sie, wie Sie Benutzern Zugriff auf Ihr Azure Purview-Konto gewähren. 

> [!div class="nextstepaction"]
> [Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview](catalog-permissions.md)