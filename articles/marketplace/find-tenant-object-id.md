---
title: Suchen der Mandanten-ID, der Objekt-ID und der Partnerzuordnungsdetails in Azure Marketplace
description: Suchen der Mandanten-ID, Objekt-ID und der Partnerzuordnungsdetails einer Abonnement-ID im Azure Marketplace.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: db09943085cb7934bca5d7f2dc24ba692613ee19
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426900"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Suchen der Mandanten-ID, der Objekt-ID und der Partnerzuordnungsdetails

In diesem Artikel wird beschrieben, wie Sie die Mandanten-ID, die Objekt-ID und die Partnerzuordnungsdetails zusammen mit den jeweiligen Abonnement-IDs finden.

Wenn Sie Screenshots dieser Elemente in Azure Cloud Shell für die Unterstützung beim Debuggen benötigen, springen Sie zu [Suchen der Mandant-, Objekt- und Partner-ID-Zuordnung für das Debuggen](#find-ids-for-debugging).

>[!Note]
> Nur der Besitzer eines Abonnements verfügt über die Berechtigungen zur Ausführung dieser Schritte.

## <a name="find-tenant-id"></a>Suchen der Mandanten-ID

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com/).
2. Wählen Sie **Azure Active Directory** aus.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Das Azure Active Directory-Symbol im Azure-Portal.":::

3. Wählen Sie **Gruppen** aus. Ihre Mandanten-ID befindet sich im Feld **Mandanteninformationen**.

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Wählen Sie Gruppen im Azure-Portal aus.":::

## <a name="find-subscriptions-and-roles"></a>Suchen von Abonnements und Rollen

1. Wechseln Sie zum Azure-Portal, und wählen Sie **Azure Active Directory** aus, wie oben in Schritt 1 und 2 beschrieben.
2. Wählen Sie **Abonnements**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Das Symbol für Abonnements im Azure-Portal.":::

3. Zeigen Sie Abonnements und Rollen an.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Der Bildschirm „Abonnements“ im Azure-Portal.":::

## <a name="find-partner-id"></a>Suchen der Partner-ID

1. Navigieren Sie zur Seite „Abonnements“, wie im vorherigen Abschnitt beschrieben.
2. Wählen Sie ein Abonnement aus.
3. Wählen Sie unter **Abrechnung** die Option **Partnerinformationen** aus.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Partnerinformationen im linken Navigationsmenü.":::

## <a name="find-user-object-id"></a>Suchen von Benutzern (Objekt-ID)

1. Melden Sie sich im [Office 365-Verwaltungsportal](https://portal.office.com/adminportal/home) mit einem Konto im gewünschten Mandanten mit den entsprechenden Administratorrechten an.
2. Erweitern Sie im linken Menü den Abschnitt **Admin Center** am unteren Rand, und wählen Sie dann die Option „Azure Active Directory“ aus, um die Verwaltungskonsole in einem neuen Browserfenster zu starten.
3. Wählen Sie **Benutzer** aus.
4. Navigieren Sie zum gewünschten Benutzer oder suchen Sie ihn, und wählen Sie dann den Kontonamen aus, um die Profilinformationen des Benutzerkontos anzuzeigen.
5. Die Objekt-ID befindet sich im Abschnitt „Identität“ auf der rechten Seite.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure Active Directory Admin Center.":::

6. Suchen Sie **Rollenzuweisungen** , indem Sie im linken Menü **Zugriffssteuerung (IAM)** und dann **Rollenzuweisungen** auswählen.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Rollenzuweisungen für Azure-Ressourcen":::

## <a name="find-ids-for-debugging"></a>Suchen von IDs zum Debuggen

In diesem Abschnitt wird beschrieben, wie Sie Mandanten-, Objekt- und Partner-ID-Zuordnungen zu Debuggingzwecken finden können.

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com/).
2. Öffnen Sie Azure Cloud Shell, indem Sie oben rechts das PowerShell-Symbol auswählen.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="PowerShell-Symbol oben rechts auf dem Bildschirm.":::

3. Wählen Sie **PowerShell** aus.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Wählen Sie den PowerShell-Link aus.":::

4. Wählen Sie das Feld **Abonnement** aus, um das Abonnement auszuwählen, mit dem der Partner verknüpft ist. Wählen Sie dann **Speicher erstellen** aus. Dies ist eine einmalige Aktion. Wenn Sie bereits Speicher eingerichtet haben, fahren Sie mit dem nächsten Schritt fort.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Wählen Sie die Schaltfläche „Speicher erstellen“ aus.":::

5. Beim Erstellen des Speichers (oder wenn er bereits erstellt wurde) wird das Azure Cloud Shell-Fenster geöffnet.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Das Azure Cloud Shell-Fenster.":::

6. Für Details zur Partnerzuordnung installieren Sie die Erweiterung mit diesem Befehl:<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell erkennt, ob die Erweiterung bereits installiert ist:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="Das Azure Cloud Shell-Fenster zeigt, dass die Erweiterung bereits installiert ist.":::

7. Überprüfen Sie die Partnerdetails mit diesem Befehl:<br>`az managementpartner show --partner-id xxxxxx`<br>Beispiel: `az managementpartner show --partner-id 4760962`.<br>Erstellen Sie einen Screenshot der Befehlsergebnisse, der ähnlich wie der folgende aussieht:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Beispielbildschirm mit den Ergebnissen des vorherigen Befehls zum Anzeigen der Partner-ID.":::

>[!NOTE]
>Wenn für mehrere Abonnements ein Screenshot erforderlich ist, verwenden Sie den folgenden Befehl, um zwischen den Abonnements zu wechseln:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Nächste Schritte

- [Unterstützte Länder und Regionen](sell-from-countries.md)