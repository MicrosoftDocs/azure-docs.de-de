---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: bbcf38ab54632144920b729f433c222f426d96dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178319"
---
1. Melden Sie sich beim [Azure-Portal][Azure portal] an.
2. Klicken Sie im Menü auf der linken Seite auf **+ Ressource erstellen**. Wählen Sie dann **Integration** > **Relay** aus. Wenn **Relay** in der Liste nicht angezeigt wird, klicken Sie in der oberen rechten Ecke auf **Alle anzeigen**. 
3. Geben Sie unter **Namespace erstellen** einen Namespacenamen ein. Das System überprüft sofort, ob dieser Name verfügbar ist.
4. Wählen Sie im Feld **Abonnement** ein Azure-Abonnement aus, in dem der Namespace erstellt werden soll.
5. Wählen Sie im Feld [Ressourcengruppe](../articles/azure-resource-manager/manage-resource-groups-portal.md) eine vorhandene Ressourcengruppe für den Namespace aus, oder erstellen Sie eine neue Ressourcengruppe.  
6. Wählen Sie im Feld **Standort** das Land oder die Region aus, in dem bzw. in der Ihr Namespace gehostet werden soll.
   
    ![Namespace erstellen][create-namespace]
7. Klicken Sie auf **Erstellen**. Ihr Dienstnamespace wird erstellt und aktiviert. Nach einigen Minuten stellt das System Ressourcen für Ihr Konto bereit.

### <a name="get-management-credentials"></a>Abrufen von Anmeldeinformationen für die Verwaltung

1. Klicken Sie auf **Alle Ressourcen** und dann auf den neu erstellten Namespacenamen.
2. Klicken Sie unter dem Relaynamespace auf **Richtlinien für gemeinsamen Zugriff**.  
3. Klicken Sie unter **Richtlinien für gemeinsamen Zugriff** auf **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. Wählen Sie unter **Richtlinie: RootManageSharedAccessKey** die Schaltfläche **Kopieren** neben **Verbindungszeichenfolge – Primärschlüssel** aus. Dadurch wird die Verbindungszeichenfolge zur späteren Verwendung in die Zwischenablage kopiert. Fügen Sie diesen Wert in den Editor oder an einem anderen temporären Speicherort ein.
   
    ![connection-string][connection-string]

5. Wiederholen Sie den vorherigen Schritt, um den Wert von **Primärschlüssel** zu kopieren und zur späteren Verwendung an einem temporären Speicherort einzufügen.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
