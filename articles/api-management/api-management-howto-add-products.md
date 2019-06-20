---
title: Erstellen und Veröffentlichen eines Produkts in Azure API Management
description: Erfahren Sie, wie Sie Produkte in Azure API Management erstellen und veröffentlichen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: eff827937adbae7cfdfdd86392593df8d698053c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241830"
---
# <a name="create-and-publish-a-product"></a>Erstellen und Veröffentlichen eines Produkts  

Produkte in Azure API Management enthalten eine oder mehrere APIs sowie ein Nutzungskontingent und Nutzungsbedingungen. Nachdem ein Produkt veröffentlicht wurde, können Entwickler das Produkt abonnieren und die APIs des Produkts verwenden.  

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Veröffentlichen eines Produkts
> * Hinzufügen einer API zum Produkt

![Tutorial zum Hinzufügen von Produkten](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Voraussetzungen

+ Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
+ Absolvieren Sie die folgende Schnellstartanleitung: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ Absolvieren Sie außerdem das folgende Tutorial: [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Erstellen und Veröffentlichen eines Produkts

![Produkt hinzufügen](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. Klicken Sie im Menü auf der linken Seite auf **Produkte**, um die Seite **Produkte** anzuzeigen.
2. Klicken Sie auf **+ Hinzufügen**.

    Beim Hinzufügen eines Produkts müssen Sie die folgenden Informationen angeben: 

    | NAME                     | BESCHREIBUNG                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | `Display name`             | Der Name, wie er im **Entwicklerportal** angezeigt werden soll                                                                                                                                                                                                                                                        |
    | NAME                     | Ein beschreibender Name des Produkts                                                                                                                                                                                                                                                                                      |
    | BESCHREIBUNG              | Das Feld **Beschreibung** enthält detaillierte Daten zum Produkt wie etwa dessen Zweck, die bereitgestellten APIs und sonstige nützliche Informationen.                                                                                                                                               |
    | Zustand                    | Klicken Sie auf **Veröffentlicht**, wenn Sie das Produkt veröffentlichen möchten. Bevor die APIs eines Produkts aufgerufen werden können, müssen Sie das Produkt veröffentlichen. Standardmäßig sind neue Produkte nicht veröffentlicht und nur für die Gruppe **Administratoren** sichtbar.                                                                                      |
    | Abonnement erforderlich    | Aktivieren Sie **Abonnement erforderlich**, wenn ein Benutzer zur Verwendung des Produkts ein Abonnement benötigt.                                                                                                                                                                                                                                   |
    | Genehmigung erforderlich        | Aktivieren Sie die Option **Genehmigung anfordern**, wenn Sie möchten, dass ein Administrator Abonnements für dieses Produkt prüfen und ablehnen oder akzeptieren muss. Wenn das Kontrollkästchen nicht markiert ist, werden Abonnements automatisch genehmigt.                                                                                                                         |
    | Grenzwert für Abonnementanzahl | Geben Sie einen Grenzwert für Abonnements ein, um die Anzahl mehrerer gleichzeitiger Abonnements zu beschränken.                                                                                                                                                                                                                                |
    | Rechtliche Bedingungen              | Sie können die Nutzungsbedingungen für das Produkt hinzufügen, denen Abonnenten zustimmen müssen, um das Produkt verwenden zu können.                                                                                                                                                                                                             |
    | APIs                     | Bei Produkten handelt es sich um API-Zuordnungen. Sie können eine Reihe von APIs aufnehmen und sie Entwicklern über das Entwicklerportal zur Verfügung stellen. <br/> Sie können während der Produkterstellung eine vorhandene API hinzufügen. Eine API kann dem Produkt auch später hinzugefügt werden – und zwar entweder über die Seite mit den **Einstellungen** des Produkts oder beim Erstellen einer API. |

3. Klicken Sie auf **Erstellen**, um das neue Produkt zu erstellen.

### <a name="add-more-configurations"></a>Hinzufügen weiterer Konfigurationen

Nach dem Speichern des Produkts können Sie es weiter konfigurieren. Klicken Sie dazu auf die Registerkarte **Einstellungen**. 

Über die Registerkarte **Abonnements** können Sie Abonnenten anzeigen und zum Produkt hinzufügen.

Legen Sie die Sichtbarkeit eines Produkts für Entwickler oder Gäste auf der Registerkarte **Zugriffssteuerung** fest.

## <a name="add-apis"></a>Hinzufügen von APIs zu einem Produkt

Bei Produkten handelt es sich um API-Zuordnungen. Sie können eine Reihe von APIs aufnehmen und sie Entwicklern über das Entwicklerportal zur Verfügung stellen. Sie können während der Produkterstellung eine vorhandene API hinzufügen. Eine API kann dem Produkt auch später hinzugefügt werden – und zwar entweder über die Seite mit den **Einstellungen** des Produkts oder beim Erstellen einer API.

Entwickler müssen zuerst ein Produkt abonnieren, um Zugriff auf die API zu erhalten. Wenn sie ein Produkt abonnieren, erhalten sie einen Abonnementschlüssel, der für jede API in diesem Produkt gilt. Wenn Sie die APIM-Instanz erstellt haben, sind Sie bereits Administrator und haben dadurch standardmäßig alle Produkte abonniert.

### <a name="add-an-api-to-an-existing-product"></a>Hinzufügen einer API zu einem vorhandenen Produkt

![Hinzufügen einer Produkt-API](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. Wählen Sie auf der Registerkarte **Produkte** ein Produkt aus.
2. Navigieren Sie zur Registerkarte **APIs**.
3. Klicken Sie auf **+ Hinzufügen**.
4. Wählen Sie eine API aus, und klicken Sie auf **Auswählen**.

> [!TIP]
> Sie können ein Benutzerabonnement für ein *Produkt* unter Verwendung benutzerdefinierter Abonnementschlüssel über die [REST-API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate) oder einen PowerShell-Befehl erstellen oder aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen und Veröffentlichen eines Produkts
> * Hinzufügen einer API zum Produkt

Fahren Sie mit dem nächsten Tutorial fort:

> [!div class="nextstepaction"]
> [API-Modellantworten](mock-api-responses.md)
