---
title: Löschen eines Azure Log Analytics-Arbeitsbereichs | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Log Analytics-Arbeitsbereich löschen, wenn Sie einen Arbeitsbereich in einem persönlichen Abonnement erstellt haben oder Ihr Arbeitsbereichsmodell neu strukturieren.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.openlocfilehash: a6542838acba3143123dc90d96746179a2b4469b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60236139"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Löschen eines Log Analytics-Arbeitsbereichs mit dem Azure-Portal
In diesem Artikel wird gezeigt, wie Sie mit dem Azure-Portal einen nicht mehr benötigten Log Analytics-Arbeitsbereich löschen. 

## <a name="to-delete-a-workspace"></a>So löschen Sie einen Arbeitsbereich 
Wenn Sie einen Log Analytics-Arbeitsbereich löschen, werden alle damit zusammenhängenden Daten innerhalb von 30 Tagen aus dem Dienst gelöscht.  Gehen Sie beim Löschen eines Arbeitsbereichs vorsichtig vor, da er unter Umständen wichtige Daten und Konfigurationen enthält, deren Löschung sich negativ auf Dienstvorgänge auswirken können. Berücksichtigen Sie die anderen Azure-Dienste und Quellen, die ihre Daten in Log Analytics speichern, z.B.:

* Application Insights
* Azure Security Center
* Azure-Automatisierung
* Auf virtuellen Windows- und Linux-Computern ausgeführte Agents
* Auf Windows- und Linux-Computern in Ihrer Umgebung ausgeführte Agents
* System Center Operations Manager
* Verwaltungslösungen 

Alle für die Berichterstattung an den Arbeitsbereich konfigurierten Agents und System Center Operations Manager-Verwaltungsgruppen fahren in einem verwaisten Status damit fort.  Machen Sie eine Bestandsaufnahme der Agents, Lösungen und anderen Azure-Diensten, die in den Arbeitsbereich integriert sind, bevor Sie fortfahren.   
 
Wenn Sie Administrator sind und mehrere Benutzer mit dem Arbeitsbereich verknüpft sind, wird die Zuordnung zwischen den Benutzern und dem Arbeitsbereich aufgehoben. Wenn die Benutzer anderen Arbeitsbereichen zugeordnet sind, können sie Log Analytics mit diesen Arbeitsbereichen weiter nutzen. Wenn sie jedoch keinen anderen Arbeitsbereichen zugeordnet sind, müssen sie einen Arbeitsbereich erstellen, um Log Analytics verwenden zu können. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
2. Klicken Sie im Azure-Portal unten links auf **Weitere Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics-Arbeitsbereiche** aus.
3. Wählen Sie im Log Analytics-Abonnementbereich einen Arbeitsbereich aus, und klicken Sie dann oben im mittleren Bereich auf **Löschen**.<br><br> ![Löschoption im Bereich mit den Arbeitsbereichseigenschaften](media/delete-workspace/log-analytics-delete-workspace.png)<br>  
4. Wenn das Fenster mit einer Meldung angezeigt wird, in der Sie zum Bestätigen der Löschung des Arbeitsbereichs aufgefordert werden, klicken Sie auf **Ja**.<br><br> ![Bestätigen des Löschvorgangs für den Arbeitsbereich](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

