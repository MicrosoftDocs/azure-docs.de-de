---
title: Herstellen einer Verbindung mit Office 365 Video – Azure Logic Apps | Microsoft-Dokumentation
description: Verwalten von Videos mit Office 365 Video-REST-APIs und Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: c10a2aa097b63fd3751be01bbfeb6097080bbb9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105818"
---
# <a name="get-started-with-the-office365-video-connector"></a>Erste Schritte mit dem Office 365-Video-Connector
Stellen Sie eine Verbindung mit Office 365 Video her, um Informationen über ein Office 365-Video oder eine Liste von Videos abzurufen und vieles mehr. Mit Office 365 Video können Sie folgende Aktionen ausführen:

* Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus Office 365 Video abgerufen werden. 
* Verwenden Sie Aktionen zum Überprüfen des Videoportalstatus, zum Abrufen einer Liste mit allen Videos in einem Kanal und vieles mehr. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Sie können z.B. mit dem Bing-Suche-Connector nach Office 365-Videos suchen und anschließend mithilfe des Office 365-Video-Connectors Informationen zu diesem Video abrufen. Wenn das Video Ihre Anforderungen erfüllt, können Sie es auf Facebook veröffentlichen. 

Erstellen Sie zu Beginn eine Logik-App, wie unter [Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) beschrieben.

## <a name="create-a-connection-to-office365-video-connector"></a>Herstellen einer Verbindung mit dem Office 365-Video-Connector
Wenn Sie Ihren Logik-Apps diesen Connector hinzufügen, müssen Sie sich bei Ihrem Office 365-Video-Konto anmelden und den Logik-Apps das Herstellen einer Verbindung mit Ihrem Konto erlauben.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

Nachdem Sie eine Verbindung hergestellt haben, geben Sie die Office 365 Video-Eigenschaften ein, z. B. den Mandantennamen oder die Kanal-ID. 


## <a name="connector-specific-details"></a>Connectorspezifische Details

Zeigen Sie die in Swagger definierten Trigger und Aktionen sowie mögliche Beschränkungen in den [Connectordetails](/connectors/office365videoconnector/) an.

## <a name="more-connectors"></a>Weitere Connectors
Gehen Sie zur [Liste der APIs](apis-list.md)zurück.