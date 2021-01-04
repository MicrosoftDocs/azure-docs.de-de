---
title: Verbinden von Azure Analysis Services mit Power BI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Power BI eine Verbindung mit einem Azure Analysis Services-Server herstellen. Nachdem die Verbindung hergestellt ist, können die Benutzer die Modelldaten untersuchen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ed0f3d379ca961c610958c073c27c6fc9583252b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500919"
---
# <a name="connect-with-power-bi"></a>Herstellen einer Verbindung mit Power BI

Sobald Sie in Azure einen Server erstellt, und ein tabellarisches Modell bereitgestellt haben, können Benutzer in Ihrer Organisation beginnen, eine Verbindung herzustellen und Daten zu durchsuchen. 

> [!TIP]
> Achten Sie darauf, dass Sie die neueste Version von [Power BI Desktop](https://powerbi.microsoft.com/desktop/) verwenden.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Herstellen einer Verbindung in Power BI Desktop

1. Klicken Sie in Power BI Desktop auf **Daten abrufen** > **Azure** > **Azure Analysis Services-Datenbank**.

2. Geben Sie unter **Server** den Namen des Servers ein. Geben Sie unbedingt die vollständige URL an, beispielsweise „asazure://westcentralus.asazure.windows.net/advworks“.

3. Wenn Sie den Namen der Datenbank für tabellarische Modelle oder die Perspektive kennen, mit der Sie sich verbinden wollen, fügen Sie ihn unter **Datenbanken** ein. Andernfalls können Sie dieses Feld leer lassen und später eine Datenbank oder Perspektive auswählen.

4. Wählen Sie eine Verbindungsoption aus, und klicken Sie dann auf **Verbinden**. 

    Sowohl **Live verbinden** als auch **Importieren** wird unterstützt. Es wird jedoch empfohlen, Liveverbindungen zu verwenden, da beim Importmodus einige Einschränkungen gelten. Insbesondere kann während des Imports die Serverleistung beeinträchtigt sein. Falls das Modell im Power BI-Dienst aktualisiert werden soll, wird darüber hinaus die Einstellung **Zugriff über Power BI zulassen** nur angewendet, wenn **Live verbinden** ausgewählt wird.

5. Geben Sie bei der entsprechenden Aufforderung Ihre Anmeldeinformationen ein. 

   > [!NOTE]
   > Konten mit Einmalkennung (One-Time Passcode, OTP) werden nicht unterstützt. 

6. Erweitern Sie in **Navigator** den Server, wählen Sie das Modell oder die Perspektive aus, mit dem/der Sie eine Verbindung herstellen möchten, und klicken Sie dann auf **Verbinden**. Klicken Sie auf ein Modell oder eine Perspektive, um alle Objekte für diese Ansicht anzuzeigen.

    Das Modell wird in Power BI Desktop mit einem leeren Bericht in der Berichtsansicht geöffnet. In der Felderliste werden alle nicht ausgeblendeten Modellobjekte angezeigt. Der Verbindungsstatus wird in der unteren rechten Ecke angezeigt.

## <a name="connect-in-power-bi-service"></a>Herstellen einer Verbindung in Power BI (Dienst)

1. Erstellen Sie eine Power BI Desktop-Datei, die über eine Liveverbindung zu Ihrem Modell auf dem Server verfügt.
2. Klicken Sie in [Power BI](https://powerbi.microsoft.com) auf **Daten abrufen** > **Dateien**, suchen Sie Ihre PBIX-Datei, und wählen Sie sie dann aus.

## <a name="see-also"></a>Weitere Informationen
[Herstellen einer Verbindung mit Azure Analysis Services](analysis-services-connect.md)   
[Clientbibliotheken](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)