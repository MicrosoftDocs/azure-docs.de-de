---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: c344d7e1aa1f6d45131295ba9aad1294c5ba548c
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930709"
---
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, um einen neuen App Configuration-Speicher zu erstellen. Wählen Sie links oben auf der Startseite die Option **Ressource erstellen** aus. Geben Sie im Feld **Marketplace durchsuchen** den Suchbegriff *App Configuration* ein, und drücken Sie die <kbd>EINGABETASTE</kbd>.

    ![Suchen nach App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Wählen Sie **App Configuration** in den Suchergebnissen und dann **Erstellen** aus.

    ![Wählen Sie „Erstellen“ aus.](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Geben Sie im Bereich **App Configuration erstellen** die folgenden Einstellungen ein:

    | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
    |---|---|---|
    | **Abonnement** | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie zum Testen von App Configuration verwenden möchten. Wenn das Konto nur ein einziges Abonnement umfasst, wird automatisch dieses Abonnement ausgewählt, und die Dropdownliste **Abonnement** wird nicht angezeigt. |
    | **Ressourcengruppe** | *AppConfigTestResources* | Wählen Sie eine Ressourcengruppe für Ihre App Configuration-Speicherressource aus, oder erstellen Sie eine Ressourcengruppe. Diese Gruppe ist beim Organisieren mehrerer Ressourcen hilfreich, die Sie möglicherweise zur gleichen Zeit löschen möchten, indem Sie die Ressourcengruppe löschen. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../articles/azure-resource-manager/management/overview.md). |
    | **Ressourcenname** | Global eindeutiger Name | Geben Sie einen eindeutigen Ressourcennamen ein, der für die App Configuration-Speicherressource verwendet werden soll. Der Name muss zwischen fünf und 50 Zeichen lang sein und darf nur Zahlen, Buchstaben und das Zeichen `-` enthalten. Der Name darf nicht mit `-` beginnen oder enden. |
    | **Location** | *USA, Mitte* | Verwenden Sie **Standort**, um den geografischen Standort anzugeben, an dem Ihr App-Konfigurationsspeicher gehostet wird. Erstellen Sie die Ressource in derselben Region wie andere Komponenten Ihrer Anwendung, um eine optimale Leistung zu erzielen. |
    | **Preisstufe** | *Free* | Wählen Sie den gewünschten Tarif aus. Weitere Informationen finden Sie auf der Seite [App Configuration – Preise](https://azure.microsoft.com/pricing/details/app-configuration). |

1. Wählen Sie zum Überprüfen Ihrer Einstellungen **Überprüfen + erstellen** aus.

1. Klicken Sie auf **Erstellen**. Die Bereitstellung kann einige Minuten dauern.

1. Navigieren Sie nach Abschluss der Bereitstellung zur App Configuration-Ressource. Klicken Sie auf **Einstellungen** > **Zugriffsschlüssel**. Notieren Sie sich die schreibgeschützte Primärschlüssel-Verbindungszeichenfolge. Diese Verbindungszeichenfolge verwenden Sie später zum Konfigurieren Ihrer Anwendung für die Kommunikation mit dem erstellten App Configuration-Speicher.