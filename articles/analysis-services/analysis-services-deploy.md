---
title: Bereitstellen in Azure Analysis Services mithilfe von Visual Studio (SSDT) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein tabellarisches Modell mithilfe von SSDT auf einem Azure Analysis Services-Server bereitstellen.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b9740d74a25964286ea92b4238684db81a64c9e2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60497476"
---
# <a name="deploy-a-model-from-visual-studio"></a>Bereitstellen eines Modells mithilfe von Visual Studio

Nachdem Sie im Azure-Abonnement einen Server erstellt haben, können Sie eine tabellarische Modelldatenbank auf ihm bereitstellen. Sie können SQL Server Data Tools (SSDT) zum Erstellen und Bereitstellen eines tabellarischen Modellprojekts verwenden. 

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* **Analysis-Services-Server** in Azure. Weitere Informationen finden Sie unter [Erstellen eines Azure Analysis Services-Servers im Azure-Portal](analysis-services-create-server.md).
* **Tabellarisches Modellprojekt** in SSDT oder ein vorhandenes tabellarisches Modell mit dem Kompatibilitätsgrad 1200 oder höher. Haben Sie noch nie eines erstellt? Sehen Sie sich das [Tutorial zur Tabellenmodellierung für Adventure Works Internet Sales](/sql/analysis-services/tabular-modeling-adventure-works-tutorial) an.
* **Lokales Gateway** – Wenn eine oder mehrere Datenquellen lokal im Netzwerk der Organisation vorhanden sind, müssen Sie ein [lokales Datengateway](analysis-services-gateway.md) installieren. Das Gateway ist erforderlich, damit der Server in der Cloud eine Verbindung mit den lokalen Datenquellen herstellen kann, um Daten im Modell zu verarbeiten und zu aktualisieren.

> [!TIP]
> Stellen Sie vor dem Bereitstellen sicher, dass Sie die Daten in den Tabellen verarbeiten können. Klicken Sie in SSDT auf **Modell** > **Verarbeiten** > **Alle verarbeiten**. Wenn bei der Verarbeitung Fehler auftreten, kann die Bereitstellung nicht erfolgen.
> 
> 

## <a name="get-the-server-name"></a>Abrufen des Servernamens

Kopieren Sie den Servernamen über **Azure-Portal** > Server > **Übersicht** > **Servername**.
   
![Abrufen von Servernamen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>So führen Sie die Bereitstellung über SSDT aus

1. Klicken Sie in SSDT im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus. Fügen Sie dann in **Bereitstellung** > **Server** den Servernamen ein.   
   
    ![Einfügen des Servernamens in die Bereitstellungsserver-Eigenschaft](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Eigenschaften**, und klicken Sie dann auf **Bereitstellen**. Sie werden möglicherweise aufgefordert, sich bei Azure anzumelden.
   
    ![Auf Server bereitstellen](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Der Bereitstellungsstatus wird im Ausgabefenster und in „Bereitstellen“ angezeigt.
   
    ![Bereitstellungsstatus](./media/analysis-services-deploy/aas-deploy-status.png)

Das war es schon!


## <a name="troubleshooting"></a>Problembehandlung

Wenn das Bereitstellen von Metadaten fehlschlägt, konnte wahrscheinlich keine Verbindung von SSDT mit dem Server hergestellt werden. Stellen Sie sicher, dass mithilfe von SSMS eine Verbindung mit dem Server hergestellt werden kann. Stellen Sie dann sicher, dass die Bereitstellungsserver-Eigenschaft für das Projekt korrekt ist.

Wenn die Bereitstellung für eine Tabelle fehlschlägt, konnte wahrscheinlich der Server keine Verbindung mit einer Datenquelle herstellen. Wenn die Datenquelle lokal im Netzwerk der Organisation vorhanden ist, müssen Sie ein [lokales Datengateway](analysis-services-gateway.md) installieren.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie jetzt das tabellarische Modell auf dem Server bereitgestellt haben, können Sie eine Verbindung mit diesem herstellen. Sie können [per SSMS eine Verbindung mit diesem herstellen](analysis-services-manage.md), um es zu verwalten. Und Sie können [mithilfe eines Clienttools eine Verbindung mit diesem herstellen](analysis-services-connect.md) (z. B. mit Power BI, Power BI Desktop oder Excel) und mit dem Erstellen von Berichten beginnen.

