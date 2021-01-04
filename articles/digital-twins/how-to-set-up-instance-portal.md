---
title: Einrichten einer Instanz und der Authentifizierung (Portal)
titleSuffix: Azure Digital Twins
description: Informationen zum Einrichten einer Instanz des Azure Digital Twins-Diensts mithilfe des Azure-Portals
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 90a425fe64424c946a02f3c113889b62b58fbeb4
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032319"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Einrichten einer Azure Digital Twins-Instanz und der Authentifizierung (Portal)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Dieser Artikel behandelt die Schritte zum **Einrichten einer neuen Azure Digital Twins-Instanz**, einschließlich des Erstellens der Instanz und des Einrichtens der Authentifizierung. Nachdem Sie diesen Artikel durchgearbeitet haben, verfügen Sie über eine Azure Digital Twins-Instanz, die für die Programmierung bereitsteht.

In dieser Version dieses Artikels werden diese Schritte manuell nacheinander mithilfe des Azure-Portals durchlaufen. Das Azure-Portal ist eine webbasierte, zentrale Konsole, die eine Alternative zu Befehlszeilentools darstellt.
* Wenn Sie diese Schritte manuell mithilfe der CLI durchlaufen möchten, finden Sie weitere Informationen in der CLI-Version dieses Artikels: [*Vorgehensweise: Einrichten einer Instanz und der Authentifizierung (CLI)*](how-to-set-up-instance-cli.md).
* Ein Beispiel zur Ausführung eines automatisierten Setups mit einem Bereitstellungsskript finden Sie in der Skriptversion dieses Artikels: [*Verwenden Einrichten einer Instanz und der Authentifizierung (über ein Skript)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Erstellen der Azure Digital Twins-Instanz

In diesem Abschnitt **erstellen Sie eine neue Azure Digital Twins-Instanz** über das [Azure-Portal](https://ms.portal.azure.com/). Navigieren Sie zum Portal, und melden Sie sich mit Ihren Anmeldeinformationen an.

Sobald Sie im Portal sind, wählen Sie im Menü auf der Homepage der Azure-Dienste _Ressource erstellen_ aus.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Auswählen von „Ressource erstellen“ auf der Homepage des Azure-Portals":::

Suchen Sie im Suchfeld nach *Azure Digital Twins*, und wählen Sie den Dienst **Azure Digital Twins** aus den Ergebnissen aus. Wählen Sie die Schaltfläche _Erstellen_ aus, um eine neue Instanz des Diensts zu erstellen.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Auswählen von „Erstellen“ auf der Seite „Azure Digital Twins-Dienst“":::

Geben Sie auf der folgenden Seite *Ressource erstellen* die unten angegebenen Werte ein:
* **Abonnement**: Das Azure-Abonnement, das Sie verwenden.
  - **Ressourcengruppe**: Eine Ressourcengruppe, in der die Instanz bereitgestellt werden soll. Wenn Sie nicht bereits eine vorhandene Ressourcengruppe in Erwägung ziehen, können Sie hier eine Ressourcengruppe erstellen, indem Sie den Link *Neu erstellen* auswählen und einen Namen für eine neue Ressourcengruppe eingeben
* **Standort**: Eine für Azure Digital Twins aktivierte Region für die Bereitstellung. Weitere Informationen zur regionalen Unterstützung finden Sie unter [*Verfügbare Azure-Produkte nach Region (Azure Digital Twins)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Ressourcenname**: Ein Name für Ihre Azure Digital Twins-Instanz. Der Name der neuen Instanz muss innerhalb der Region für Ihr Abonnement eindeutig sein (d. h. wenn Ihr Abonnement eine weitere Azure Digital Twins-Instanz in der Region aufweist, die bereits den von Ihnen ausgewählten Namen verwendet, werden Sie aufgefordert, einen anderen Namen auszuwählen).

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Eingeben der beschriebenen Werte zum Erstellen einer Azure Digital Twins-Ressource":::

Wenn Sie fertig sind, wählen Sie _Überprüfen und erstellen_ aus. Dadurch gelangen Sie zu einer Zusammenfassungsseite, auf der Sie die eingegebenen Instanzdetails überprüfen und _Erstellen_ auswählen können. 

### <a name="verify-success-and-collect-important-values"></a>Überprüfen des Erfolgs und Erfassen wichtiger Werte

Nachdem Sie *Erstellen* ausgewählt haben, können Sie den Status der Bereitstellung Ihrer Instanz in Ihren Azure-Benachrichtigungen auf der Symbolleiste des Portals anzeigen. In der Benachrichtigung wird angezeigt, wenn die Bereitstellung erfolgreich war, und Sie können die Schaltfläche _Zu Ressource wechseln_ auswählen, um Ihre erstellte Instanz anzuzeigen.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Ansicht von Azure-Benachrichtigungen mit einer erfolgreichen Bereitstellung und Hervorhebung der Schaltfläche „Zu Ressource wechseln“":::

Wenn bei der Bereitstellung ein Fehler auftritt, wird in der Benachrichtigung alternativ die Ursache angegeben. Beachten Sie die Ratschläge in der Fehlermeldung, und versuchen Sie dann erneut, die Instanz zu erstellen.

>[!TIP]
>Nachdem die Instanz erstellt wurde, können Sie jederzeit zu ihrer Seite zurückkehren, indem Sie in der Suchleiste des Azure-Portals nach dem Namen Ihrer Instanz suchen.

Notieren Sie sich von der Seite *Übersicht* der Instanz den *Namen*, die *Ressourcengruppe* und den *Hostnamen*. Dies sind alles wichtige Werte, die Sie möglicherweise benötigen, wenn Sie mit Ihrer Azure Digital Twins-Instanz weiterarbeiten. Wenn andere Benutzer in der Instanz programmieren, sollten Sie diese Werte mit ihnen teilen.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Hervorheben der wichtigen Werte von der Übersichtsseite der Instanz":::

Die Azure Digital Twins-Instanz ist jetzt einsatzbereit. Im nächsten Schritt stellen Sie die entsprechenden Azure-Benutzerberechtigungen für die Verwaltung der Instanz zur Verfügung.

## <a name="set-up-user-access-permissions"></a>Einrichten von Benutzerzugriffsberechtigungen

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Öffnen Sie zunächst die Seite für Ihre Azure Digital Twins-Instanz im Azure-Portal. Wählen Sie im Menü der Instanz *Zugriffssteuerung (IAM)* aus. Wählen Sie unter *Rollenzuweisung hinzufügen* die Schaltfläche *Hinzufügen* aus.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Auswählen der Option zum Hinzufügen einer Rollenzuweisung auf der Seite „Zugriffssteuerung (IAM)“":::

Geben Sie auf der folgenden Seite *Rollenzuweisung hinzufügen* die Werte ein (dies muss ein Benutzer im Azure-Abonnement mit [ausreichenden Berechtigungen](#prerequisites-permission-requirements) erledigen):
* **Rolle**: Wählen Sie im Dropdownmenü *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) aus.
* **Zugriff zuweisen zu**: Wählen Sie im Dropdownmenü *Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal* aus.
* **Select**: Suchen Sie nach dem Namen oder der E-Mail-Adresse des Benutzers, der zugewiesen werden soll. Wenn Sie das Ergebnis auswählen, wird der Benutzer im Abschnitt *Ausgewählte Mitglieder* angezeigt.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Eingeben der aufgelisteten Felder im Dialogfeld „Rollenzuweisung hinzufügen“":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Nachdem Sie die Werte eingegeben haben, klicken Sie auf die Schaltfläche *Speichern*.

### <a name="verify-success"></a>Überprüfen des erfolgreichen Abschlusses

Sie können die Rollenzuweisung, die Sie eingerichtet haben, unter *Zugriffssteuerung (IAM) > Rollenzuweisungen* anzeigen. Der Benutzer sollte in der Liste mit der Rolle *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) angezeigt werden. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Anzeigen der Rollenzuweisungen für eine Azure Digital Twins-Instanz im Azure-Portal":::

Sie verfügen nun über eine einsatzbereite Azure Digital Twins-Instanz und haben Berechtigungen zugewiesen, um diese zu verwalten.

## <a name="next-steps"></a>Nächste Schritte

Testen Sie einzelne REST-API-Aufrufe für Ihre Instanz mithilfe der Befehle der Azure Digital Twins-CLI: 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Gewusst wie: Verwenden der Azure Digital Twins-Befehlszeilenschnittstelle*](how-to-use-cli.md)

Sie können auch eine Verbindung zwischen Ihrer Clientanwendung und Ihrer Instanz herstellen, indem Sie Authentifizierungscode verwenden:
* [*Verwenden Schreiben von App-Authentifizierungscode*](how-to-authenticate-client.md)