---
title: Bereitstellen eines Modells in Azure Analysis Services mithilfe von Visual Studio | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein tabellarisches Modell mithilfe von Visual Studio auf einem Azure Analysis Services-Server bereitstellen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9df10760164dcd0d207663c14107f72c46b76d25
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501242"
---
# <a name="deploy-a-model-from-visual-studio"></a>Bereitstellen eines Modells mithilfe von Visual Studio

Nachdem Sie im Azure-Abonnement einen Server erstellt haben, können Sie eine tabellarische Modelldatenbank auf ihm bereitstellen. Sie können Visual Studio mit Analysis Services-Projekten verwenden, um ein tabellarisches Modellprojekt zu erstellen und bereitzustellen, an dem Sie arbeiten. 

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* **Analysis-Services-Server** in Azure. Weitere Informationen finden Sie unter [Erstellen eines Azure Analysis Services-Servers im Azure-Portal](analysis-services-create-server.md).
* **Tabellarisches Modellprojekt** in Visual Studio oder ein vorhandenes tabellarisches Modell mit dem Kompatibilitätsgrad 1200 oder höher. Haben Sie noch nie eines erstellt? Sehen Sie sich das [Tutorial zur Tabellenmodellierung für Adventure Works Internet Sales](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) an.
* **Lokales Gateway** – Wenn eine oder mehrere Datenquellen lokal im Netzwerk der Organisation vorhanden sind, müssen Sie ein [lokales Datengateway](analysis-services-gateway.md) installieren. Das Gateway ist erforderlich, damit der Server in der Cloud eine Verbindung mit den lokalen Datenquellen herstellen kann, um Daten im Modell zu verarbeiten und zu aktualisieren.

> [!TIP]
> Stellen Sie vor dem Bereitstellen sicher, dass Sie die Daten in den Tabellen verarbeiten können. Klicken Sie in Visual Studio auf **Modell** > **Verarbeiten** > **Alle verarbeiten**. Wenn bei der Verarbeitung Fehler auftreten, kann die Bereitstellung nicht erfolgen.
> 
> 

## <a name="get-the-server-name"></a>Abrufen des Servernamens

Kopieren Sie den Servernamen über **Azure-Portal** > Server > **Übersicht** > **Servername**.
   
![Abrufen von Servernamen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>So stellen Sie von Visual Studio aus bereit

1. Klicken Sie in Visual Studio im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus. Fügen Sie dann in **Bereitstellung** > **Server** den Servernamen ein.   
   
    ![Einfügen des Servernamens in die Bereitstellungsserver-Eigenschaft](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Eigenschaften**, und klicken Sie dann auf **Bereitstellen**. Sie werden möglicherweise aufgefordert, sich bei Azure anzumelden.
   
    ![Auf Server bereitstellen](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Der Bereitstellungsstatus wird im Ausgabefenster und in „Bereitstellen“ angezeigt.
   
    ![Bereitstellungsstatus](./media/analysis-services-deploy/aas-deploy-status.png)

Das war es schon!


## <a name="troubleshooting"></a>Problembehandlung

Wenn beim Bereitstellen von Metadaten ein Fehler auftritt, konnte wahrscheinlich von Visual Studio keine Verbindung mit dem Server hergestellt werden. Vergewissern Sie sich, dass Sie über SQL Server Management Studio (SSMS) eine Verbindung mit Ihrem Server herstellen können. Stellen Sie dann sicher, dass die Bereitstellungsserver-Eigenschaft für das Projekt korrekt ist.

Wenn die Bereitstellung für eine Tabelle fehlschlägt, konnte wahrscheinlich der Server keine Verbindung mit einer Datenquelle herstellen. Wenn die Datenquelle lokal im Netzwerk der Organisation vorhanden ist, müssen Sie ein [lokales Datengateway](analysis-services-gateway.md) installieren.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie jetzt das tabellarische Modell auf dem Server bereitgestellt haben, können Sie eine Verbindung mit diesem herstellen. Sie können [mit SQL Server Management Studio (SSMS) eine Verbindung damit herstellen](analysis-services-manage.md), um es zu verwalten. Und Sie können [mithilfe eines Clienttools eine Verbindung mit diesem herstellen](analysis-services-connect.md) (z. B. mit Power BI, Power BI Desktop oder Excel) und mit dem Erstellen von Berichten beginnen.   

Weitere Informationen zu erweiterten Bereitstellungsmethoden finden Sie unter [Bereitstellung von Tabellenmodelllösungen](/analysis-services/deployment/tabular-model-solution-deployment?view=azure-analysis-services-current&preserve-view=true).