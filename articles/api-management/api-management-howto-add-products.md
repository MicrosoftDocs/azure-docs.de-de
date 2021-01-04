---
title: 'Tutorial: Erstellen und Veröffentlichen eines Produkts in Azure API Management'
description: In diesem Tutorial erstellen und veröffentlichen Sie ein Produkt in Azure API Management. Nachdem es veröffentlicht wurde, können Entwickler die APIs des Produkts verwenden.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 2f298f240d8aa7a38b42a8c78ee3c90fe3423d10
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993549"
---
# <a name="tutorial-create-and-publish-a-product"></a>Tutorial: Erstellen und Veröffentlichen eines Produkts  

[*Produkte*](api-management-terminology.md#term-definitions) in Azure API Management enthalten eine oder mehrere APIs sowie ein Nutzungskontingent und Nutzungsbedingungen. Nachdem ein Produkt veröffentlicht wurde, können Entwickler das Produkt abonnieren und die APIs des Produkts verwenden.  

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen und Veröffentlichen eines Produkts
> * Hinzufügen einer API zum Produkt

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="API Management-Produkte im Portal":::


## <a name="prerequisites"></a>Voraussetzungen

+ Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
+ Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ Absolvieren Sie außerdem das folgende Tutorial: [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Erstellen und Veröffentlichen eines Produkts

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu Ihrer API Management-Instanz.
1. Wählen Sie im linken Navigationsbereich **Produkte** >  **+ Hinzufügen** aus.
1.  Geben Sie im Fenster **Produkt hinzufügen** Werte ein, die in der folgenden Tabelle beschrieben sind, um Ihr Produkt zu erstellen.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Produkt im Portal hinzufügen":::

    | name                     | BESCHREIBUNG                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Anzeigename             | Der Name, wie er im [Entwicklerportal](api-management-howto-developer-portal.md) angezeigt werden soll                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Beschreibung              | Machen Sie Angaben zum Produkt wie etwa dessen Zweck, die zur Verfügung gestellten APIs und sonstige Details.                                                                                                                                               |
    | State                    | Wählen Sie **Veröffentlicht** aus, wenn Sie das Produkt veröffentlichen möchten. Bevor die APIs eines Produkts aufgerufen werden können, müssen Sie das Produkt veröffentlichen. Standardmäßig sind neue Produkte nicht veröffentlicht und nur für die Gruppe **Administratoren** sichtbar.                                                                                      |
    | Abonnement erforderlich    | Wählen Sie diese Option aus, wenn ein Benutzer zur Verwendung des Produkts ein Abonnement benötigt.                                                                                                                                                                                                                                   |
    | Genehmigung erforderlich        | Aktivieren Sie diese Option, wenn Sie möchten, dass ein Administrator Abonnements für dieses Produkt prüfen und ablehnen oder akzeptieren muss. Andernfalls werden Abonnements automatisch genehmigt.                                                                                                                         |
    | Grenzwert für Abonnementanzahl | Beschränken Sie optional die Anzahl mehrerer gleichzeitiger Abonnements.                                                                                                                                                                                                                                |
    | Rechtliche Bedingungen              | Sie können die Nutzungsbedingungen für das Produkt hinzufügen, denen Abonnenten zustimmen müssen, um das Produkt verwenden zu können.                                                                                                                                                                                                             |
    | APIs                     | Wählen Sie mindestens eine API aus. Sie können APIs auch nach dem Erstellen des Produkts hinzufügen. Weitere Informationen finden Sie weiter unten in diesem Artikel unter [Hinzufügen von APIs zu einem Produkt](#add-apis-to-a-product). |

3. Wählen Sie **Erstellen** aus, um das neue Produkt zu erstellen.

### <a name="add-more-configurations"></a>Hinzufügen weiterer Konfigurationen

Setzen Sie die Konfiguration des Produkts fort, nachdem Sie es gespeichert haben. Wählen Sie in der API Management-Instanz das Produkt im Fenster **Produkte** aus. Sie können Folgendes hinzufügen oder aktualisieren:


|Element   |Beschreibung  |
|---------|---------|
|Einstellungen     |    Produktmetadaten und -status     |
|APIs     |  Dem Produkt zugeordnete APIs       |
|[Richtlinien](api-management-howto-policies.md)     |  Auf Produkt-APIs angewendete Richtlinien      |
|Zugriffssteuerung     |  Produktsichtbarkeit für Entwickler oder Gäste       |
|[Abonnements](api-management-subscriptions.md)    |    Produktabonnenten     |

## <a name="add-apis-to-a-product"></a>Hinzufügen von APIs zu einem Produkt

Bei Produkten handelt es sich um API-Zuordnungen. Sie können eine Reihe von APIs einfügen und sie Entwicklern über das Entwicklerportal zur Verfügung stellen. Während der Erstellung des Produkts können Sie eine oder mehrere vorhandene APIs hinzufügen. Sie können eine API dem Produkt auch später hinzufügen – und zwar entweder über die Seite mit den **Einstellungen** des Produkts oder beim Erstellen einer API.

Entwickler müssen ein Produkt zunächst abonnieren, um Zugriff auf die API zu erhalten. Wenn sie ein Produkt abonnieren, erhalten sie einen Abonnementschlüssel, der für jede API in diesem Produkt gilt. Wenn Sie die APIM-Instanz erstellt haben, sind Sie bereits Administrator und haben dadurch standardmäßig alle Produkte abonniert.

### <a name="add-an-api-to-an-existing-product"></a>Hinzufügen einer API zu einem vorhandenen Produkt


1. Wählen Sie im linken Navigationsbereich der API Management-Instanz **Produkte** aus.
1. Wählen Sie ein Produkt und dann **APIs** aus.
1. Wählen Sie **+ Hinzufügen**.
1. Wählen Sie mindestens eine API und dann **Auswählen** aus.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="API zu vorhandenem Produkt hinzufügen":::

> [!TIP]
> Sie können ein Benutzerabonnement für ein Produkt unter Verwendung benutzerdefinierter Abonnementschlüssel über die [REST-API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) oder einen PowerShell-Befehl erstellen oder aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen und Veröffentlichen eines Produkts
> * Hinzufügen einer API zum Produkt

Fahren Sie mit dem nächsten Tutorial fort:

> [!div class="nextstepaction"]
> [API-Modellantworten](mock-api-responses.md)
