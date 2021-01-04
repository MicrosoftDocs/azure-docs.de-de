---
title: Verbinden von Beyond Security beSECURE-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Beyond Security beSECURE-Datenconnector beSECURE-Protokolle in Azure Sentinel pullen. Zeigen Sie beSECURE-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: f6fd6920fafe4c1080cb5539e4e0222d9d6e18cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102673"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Verbinden Ihrer Beyond Security beSECURE-Instanz mit Azure Sentinel

> [!IMPORTANT]
> Der Beyond Security beSECURE-Datenconnector in Azure Sentinel ist derzeit als öffentliche Vorschau verfügbar. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit dem Connector für beSECURE von Beyond Security können Sie alle Protokolle Ihrer beSECURE-Sicherheitslösungen einfach mit Azure Sentinel verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und Untersuchungen zu verbessern. Die Integration von beSECURE und Azure Sentinel basiert auf der REST-API.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="configure-and-connect-besecure"></a>Konfigurieren und Verbinden von beSECURE

beSECURE kann in Azure Sentinel integriert werden und Protokolle dann direkt in Azure Sentinel exportieren.

1. Klicken Sie im Azure Sentinel-Portal auf **Datenconnectors** , und wählen Sie **Beyond Security beSECURE (Vorschau)** und dann **Connectorseite öffnen** aus.

1. Führen Sie die folgenden Schritte aus, um Ihre beSECURE-Lösung zum Senden von Scanergebnissen, Scanstatus und Überwachungsprotokollen an Azure Sentinel zu konfigurieren.

    **Zugreifen auf das Menü „Integration“:**
    1. Klicken Sie auf die Menüoption „More“ (Weitere).

    1. Server auswählen

    1. Wählen Sie „Integration“ aus.

    1. Aktivieren von Azure Sentinel

    **Bereitstellen von Azure Sentinel-Einstellungen in beSECURE:**
      - Kopieren Sie die Werte für *Arbeitsbereichs-ID* und *Primärschlüssel* von der Connectorseite in Azure Sentinel, fügen Sie sie in die Konfiguration von beSECURE ein, und klicken Sie auf **Modify** (Ändern).

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine erfolgreiche Verbindung hergestellt wurde, werden die Daten im Abschnitt **CustomLogs** in **Logs** in einer der folgenden Tabellen angezeigt:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Wenn Sie die beSECURE-Protokolle in Log Analytics abfragen möchten, geben Sie oben im Abfragefenster einen der obigen Tabellennamen ein.

## <a name="validate-connectivity"></a>Überprüfen der Konnektivität
Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde beschrieben, wie Sie beSECURE mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
