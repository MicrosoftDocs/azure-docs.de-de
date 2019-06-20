---
title: 'Tutorial: Herstellen einer Verbindung mit Azure Analysis Services unter Verwendung von Power BI Desktop | Microsoft-Dokumentation'
author: minewiskan
manager: kfile
description: Hier erfahren Sie, wie Sie eine Verbindung zwischen Azure Analysis Services und Power BI Desktop herstellen.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: ea2a6612a568d4080a8df2c569f79aaaa283e9c4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702838"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Tutorial: Herstellen einer Verbindung mit Power BI Desktop

In diesem Tutorial verwenden Sie Power BI Desktop, um eine Verbindung mit der Beispielmodelldatenbank „adventureworks“ auf Ihrem Server herzustellen. Die auszuführenden Aufgaben simulieren eine typische Benutzerverbindung mit dem Modell sowie die Erstellung eines einfachen Berichts auf der Grundlage von Modelldaten.

> [!div class="checklist"]
> * Abrufen Ihres Servernamens über das Portal
> * Herstellen einer Verbindung unter Verwendung von Power BI Desktop
> * Erstellen eines einfachen Berichts

## <a name="prerequisites"></a>Voraussetzungen

- [Fügen Sie Ihrem Server die Beispielmodelldatenbank „adventureworks“ hinzu.](../analysis-services-create-sample-model.md)
- Sie benötigen [*Leseberechtigungen*](../analysis-services-server-admins.md) für die Beispielmodelldatenbank „adventureworks“.
- [Installieren Sie die neueste Version von Power BI Desktop.](https://powerbi.microsoft.com/desktop)

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.
In diesem Tutorial melden Sie sich nur beim Portal an, um den Servernamen abzurufen. Benutzer erhalten den Servernamen üblicherweise vom Serveradministrator.

Melden Sie sich beim [Portal](https://portal.azure.com/) an.

## <a name="get-server-name"></a>Abrufen des Servernamens
Sie benötigen den Servernamen, um über Power BI Desktop eine Verbindung mit Ihrem Server herstellen zu können. Den Servernamen können Sie über das Portal abrufen.

Kopieren Sie den Servernamen über **Azure-Portal** > Server > **Übersicht** > **Servername**.
   
   ![Abrufen von Servernamen in Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Herstellen einer Verbindung in Power BI Desktop

1. Klicken Sie in Power BI Desktop auf **Daten abrufen** > **Azure** > **Azure Analysis Services-Datenbank**.

   ![Herstellen einer Verbindung in „Daten abrufen“](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. Fügen Sie unter **Server** den Servernamen ein, geben Sie unter **Datenbank** die Zeichenfolge **adventureworks** ein, und klicken Sie dann auf **OK**.

   ![Angeben von Servername und Modeldatenbank](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Geben Sie bei Aufforderung Ihre Anmeldeinformationen ein. Das eingegebene Konto muss mindestens über Leseberechtigungen für die Beispielmodelldatenbank „adventureworks“ verfügen.

    Das Modell „adventureworks“ wird in Power BI Desktop mit einem leeren Bericht in der Berichtsansicht geöffnet. In der Liste **Felder** werden alle nicht ausgeblendeten Modellobjekte angezeigt. Der Verbindungsstatus wird in der unteren rechten Ecke angezeigt.

4. Wählen Sie unter **VISUALISIERUNGEN** die Option **Balkendiagramm (gruppiert)** aus, klicken Sie auf **Format** (Farbrollersymbol), und aktivieren Sie **Datenbeschriftungen**. 

   ![Visualisierungen](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. Wählen Sie unter **FELDER** > **Internet Sales** (Tabelle) die Measures **Internet Sales Total** und **Margin** aus. Wählen Sie in der Tabelle **Product Category** die Option **Product Category Name** aus.

   ![Vervollständigen des Berichts](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Nehmen Sie sich etwas Zeit, um sich mit dem Beispielmodell „adventureworks“ vertraut zu machen. Erstellen Sie hierzu verschiedene Visualisierungen sowie Daten- und Metrikslices. Vergessen Sie nicht, Ihren Bericht zu speichern, wenn Sie mit dem Ergebnis zufrieden sind.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Bericht nicht mehr benötigen, speichern Sie ihn nicht (oder löschen Sie die Datei, falls Sie den Bericht bereits gespeichert haben).

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie gelernt, wie Sie unter Verwendung von Power BI Desktop eine Verbindung mit einem Datenmodell auf einem Server herstellen und einen einfachen Bericht erstellen. Informationen zur Erstellung eines Datenmodells finden Sie bei Bedarf im Tutorial [Adventure Works Internet Sales: tabellarische Datenmodellierung](https://docs.microsoft.com/sql/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) in der Dokumentation zu SQL Server Analysis Services.