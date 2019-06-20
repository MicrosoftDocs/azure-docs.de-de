---
title: 'Gewusst wie: Erstellen eines Supporttickets für Azure SQL Data Warehouse | Microsoft-Dokumentation'
description: Sie erfahren, wie Sie in Azure SQL Data Warehouse ein Supportticket erstellen.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b3ffc9794967f44708d8330d4dc5d5de11b794d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61474506"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Gewusst wie: Erstellen eines Supporttickets für SQL Data Warehouse
Bei Problemen mit Ihrer Instanz von SQL Data Warehouse ist es ratsam, ein Supportticket zu erstellen, damit Sie Hilfe von unserem Entwicklungssupportteam erhalten können.

## <a name="create-a-support-ticket"></a>Erstellen ein Supporttickets
1. Öffnen Sie das [Azure-Portal][Azure portal].
2. Klicken Sie auf der Startseite auf die Registerkarte **Hilfe und Support**.
   
    ![Hilfe und Support](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Klicken Sie auf dem Blatt „Hilfe und Support“ auf **Neue Supportanfrage**, und füllen Sie das Blatt **Grundlagen** aus.

   Wählen Sie Ihren [Azure-Supportplan][Azure support plan].
   
   * **Verwaltung von Abrechnung, Kontingenten und Abonnements** ist in allen Supportstufen verfügbar.
   * **Problemlösungssupport** wird in den Supportstufen [Entwickler][Developer], [Standard][Standard], [Professional Direct][Professional Direct] oder [Premier][Premier] bereitgestellt. Problemlösungssupport betrifft Probleme, die bei der Verwendung von Azure auftreten und bei denen Grund zu der Annahme besteht, dass das Problem durch Microsoft verursacht wurde.
   * **Anleitung für Entwickler** und **beratende Dienstleistungen** sind für die Supportstufen [Professional Direct][Professional Direct] und [Premier][Premier] verfügbar. 
     
     Wenn Sie über den Supportplan „Premier“ verfügen, können Sie Probleme im Zusammenhang mit SQL Data Warehouse auch im [Microsoft Premier-Onlineportal][Microsoft Premier online portal] melden.  Weitere Informationen zu den unterschiedlichen Supportplänen, z.B. zu Umfang, Reaktionszeiten, Preisen usw., finden Sie auf der Seite mit den [Azure-Supportplänen][Azure support plan].  Häufig gestellte Fragen zum Azure-Support finden Sie unter [Häufig gestellte Fragen zum Azure-Support][Azure support FAQs].  
        
     ![Blatt „Grundlagen“](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Blatt1 „Grundlagen“](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Füllen Sie das Blatt **Problem** aus.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Standardmäßig verfügt jeder SQL-Server (z.B. myserver.database.windows.net) über ein **DTU-Kontingent** in Höhe von 45.000. Bei diesem Kontingentwert handelt es sich einfach um ein Sicherheitslimit. Sie können Ihr Kontingent erhöhen, indem Sie ein Supportticket erstellen und als Anfragetyp *Kontingent* wählen. Multiplizieren Sie zum Berechnen Ihrer DTU-Anforderungen die Anzahl der insgesamt benötigten [DWUs][DWU] mit 7,5. Wenn Sie beispielsweise zwei DW6000-Einheiten auf einer SQL Server-Instanz hosten möchten, sollten Sie ein DTU-Kontingent in Höhe von 90.000 anfordern.  Sie können den aktuellen DTU-Verbrauch im Portal auf dem Blatt „SQL-Server“ anzeigen. Sowohl angehaltene als auch nicht angehaltene Datenbanken werden in das DTU-Kontingent eingerechnet. 
   > 
   > 
   
5. Geben Sie Ihre **Kontaktinformationen** ein.
   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Klicken Sie auf **Erstellen** , um die Supportanfrage zu übermitteln.

## <a name="monitor-a-support-ticket"></a>Überwachen eines Supporttickets
Nachdem Sie die Supportanfrage übermittelt haben, nimmt das Azure-Supportteam Kontakt mit Ihnen auf. Klicken Sie zum Überprüfen des Anfragestatus und der Details auf dem Dashboard auf **Alle Supportanfragen**.

![Status überprüfen](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Weitere Ressourcen
Außerdem können Sie sich an der SQL Data Warehouse-Community unter [Stack Overflow][Stack Overflow] oder im [Azure SQL Data Warehouse-MSDN-Forum][Azure SQL Data Warehouse MSDN forum] beteiligen.

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

