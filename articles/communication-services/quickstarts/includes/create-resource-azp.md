---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 051f903c0b203045886b80f72d868adc1fa85f73
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886097"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Erstellen von Azure Communication-Ressourcen

Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com) an, um eine Azure Communication Services-Ressource zu erstellen. Wählen Sie links oben auf der Seite die Option **+ Ressource erstellen** aus. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Screenshot mit der hervorgehobenen Schaltfläche zum Erstellen einer Ressource im Azure-Portal":::

Geben Sie **Communication** in **Search the Marketplace** (Marketplace durchsuchen) oder in die Suchleiste oben im Portal ein.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Screenshot, der eine Suche nach Communication Services in der Suchleiste zeigt":::

Wählen Sie in den Ergebnissen **Communication Services** und anschließend **Hinzufügen** aus.

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="Screenshot, der das Azure-Panel mit der hervorgehobenen Schaltfläche „Hinzufügen“ zeigt":::

Sie können Ihre Communication Services-Ressource jetzt konfigurieren. Auf der ersten Seite des Erstellungsprozesses werden Sie aufgefordert, Folgendes anzugeben:

* Das Abonnement
* Die Ressourcengruppe (Sie können eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe auswählen)
* Den Namen der Communication Services-Ressource
* Die Geografie, der die Ressource zugeordnet wird

Im nächsten Schritt können Sie der Ressource Tags zuweisen. Tags können verwendet werden, um Azure-Ressourcen zu organisieren. Weitere Informationen zu Tags finden Sie in der [Dokumentation zum Anwenden von Tags auf Ressourcen](../../../azure-resource-manager/management/tag-resources.md).

Zuletzt können Sie Ihre Konfiguration überprüfen und die Ressource **erstellen**. Beachten Sie, dass die Bereitstellung einige Minuten dauert.

## <a name="manage-your-communication-services-resource"></a>Verwalten Ihrer Communication Services-Ressource

Wechseln Sie zum Verwalten Ihrer Communication Services-Ressourcen zum [Azure-Portal](https://portal.azure.com), und suchen Sie nach **Azure Communication Services**.

Wählen Sie auf der Seite **Communication Services** den Namen Ihrer Ressource aus.

Die Seite **Übersicht** für Ihre Ressource enthält Optionen für die grundlegende Verwaltung, z. B. durchsuchen, beenden, starten, neu starten und löschen. Weitere Konfigurationsoptionen finden Sie im linken Menü Ihrer Ressourcenseite.