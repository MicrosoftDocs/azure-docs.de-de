---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140618"
---
1. Melden Sie sich beim [Azure-Portal] an.
2. Wählen Sie **+ NEU** > **Web + Mobil** > **Mobile App** aus, und geben Sie einen Namen für Ihr Mobile Apps-Back-End an.
3. Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. (Verwenden Sie dabei den gleichen Namen wie für die App.) 
4. Für den **App Service-Plan** ist der Standardplan (im Tarif [Standard](https://azure.microsoft.com/pricing/details/app-service/)) ausgewählt. Sie können auch einen anderen Plan auswählen oder [einen neuen Plan erstellen](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Die Einstellungen für den App Service-Plan bestimmen [den Standort, die Funktionen, die Kosten und die Computeressourcen](https://azure.microsoft.com/pricing/details/app-service/) Ihrer App. Weitere Informationen zu App Service-Plänen sowie zum Erstellen eines neuen Plans in einem anderen Tarif und am gewünschten Standort finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../articles/app-service/overview-hosting-plans.md).
   
5. Klicken Sie auf **Erstellen**. In diesem Schritt wird das Back-End für Mobile Apps erstellt. 
6. Klicken Sie im Bereich **Einstellungen** für das neue Mobile Apps-Back-End auf **Schnellstart** > Ihre Client-App-Plattform > **Datenbank verbinden**. 
   
   ![Auswahl für die Verbindung einer Datenbank](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. Klicken Sie im Bereich **Datenverbindung hinzufügen** auf **SQL-Datenbank** > **Neue Datenbank erstellen**. Geben Sie den Datenbanknamen ein, und wählen Sie einen Tarif und dann **Server** aus. Sie können diese neue Datenbank wiederverwenden. Wenn Sie bereits eine Datenbank am gleichen Standort haben, können Sie stattdessen **eine vorhandene Datenbank verwenden**. Die Verwendung einer Datenbank an einem anderen Standort wird aufgrund der Kosten für die Bandbreite und höherer Latenz nicht empfohlen.
   
   ![Auswählen einer Datenbank](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. Geben Sie im Bereich **Neuer Server** im Feld **Servername** einen eindeutigen Servernamen ein, geben Sie einen Anmeldenamen und ein Kennwort an, wählen Sie **Azure-Diensten Zugriff auf den Server erlauben** aus, und klicken Sie anschließend auf **OK**. Mit diesem Schritt wird die neue Datenbank erstellt.
9. Klicken Sie im Bereich **Datenverbindung hinzufügen** auf **Verbindungszeichenfolge**, geben Sie den Anmeldenamen und das Kennwort für Ihre Datenbank ein, und wählen Sie **OK** aus. 

   Warten Sie einige Minuten, bis die Datenbank bereitgestellt wurde, bevor Sie fortfahren.

<!-- URLs. -->
[Azure-Portal]: https://portal.azure.com/
