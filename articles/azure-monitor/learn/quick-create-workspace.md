---
title: Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Log Analytics-Arbeitsbereich erstellen, um Verwaltungslösungen und die Datensammlung in Ihren cloudbasierten und lokalen Umgebungen im Azure-Portal zu aktivieren.
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
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: a68e40b7b1caf184fabb5df62d8b461fa2fa11e2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60539228"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal
Im Menü **Log Analytics-Arbeitsbereiche** können Sie einen Log Analytics-Arbeitsbereich über das Azure-Portal einrichten. Ein Log Analytics-Arbeitsbereich ist eine spezifische Umgebung für Azure Monitor-Protokolldaten. Jeder Arbeitsbereich verfügt über ein eigenes Datenrepository und eine eigene Konfiguration. Datenquellen und Lösungen sind so konfiguriert, dass die zugehörigen Daten in einem bestimmten Arbeitsbereich gespeichert werden. Ein Log Analytics-Arbeitsbereich ist erforderlich, wenn Sie Daten aus den folgenden Quellen erfassen möchten:

* Azure-Ressourcen im Abonnement
* Lokale Computer, die von System Center Operations Manager überwacht werden
* Gerätesammlungen aus System Center Configuration Manager 
* Diagnose- oder Protokolldaten aus dem Azure-Speicher

Informationen zu anderen Quellen in Ihrer Umgebung (etwa virtuelle Azure-Computer und virtuelle Windows- oder Linux-Computer) finden Sie in folgenden Themen:

*  [Sammeln von Daten von virtuellen Azure-Computern](../learn/quick-collect-azurevm.md) 
*  [Sammeln von Daten von Linux-Hybridcomputern](../learn/quick-collect-linux-computer.md)
*  [Sammeln von Daten von Windows-Hybridcomputern](quick-collect-windows-computer.md)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure-portal"></a>Anmelden beim Azure-Portal
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an. 

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs
1. Klicken Sie im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics-Arbeitsbereiche** aus.

    ![Azure-Portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Klicken Sie auf **Hinzufügen**, und wählen Sie dann Optionen für die folgenden Elemente aus:

   * Geben Sie einen Namen für den neuen **Log Analytics-Arbeitsbereich** ein, z.B. *DefaultLAWorkspace*. 
   * Wählen Sie ein **Abonnement** aus, mit dem eine Verknüpfung erstellt werden soll, indem Sie in der Dropdownliste einen anderen Eintrag auswählen, falls der Standardeintrag nicht geeignet ist.
   * Wählen Sie für **Ressourcengruppe** eine vorhandene und bereits eingerichtete Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe.  
   * Wählen Sie einen verfügbaren **Standort** aus.  Weitere Informationen finden Sie auf der Seite zur [Verfügbarkeit von Log Analytics in den einzelnen Regionen](https://azure.microsoft.com/regions/services/), und suchen Sie im Feld **Nach einem Produkt suchen** nach Azure Monitor.  
   * Wenn Sie einen Arbeitsbereich in einem neuen Abonnement erstellen, das nach dem 2. April 2018 erstellt wurde, wird automatisch der Tarif *Pro GB* verwendet. In diesem Fall ist keine Tarifauswahloption verfügbar.  Wenn Sie einen Arbeitsbereich für ein Abonnement erstellen, das vor dem 2. April erstellt oder mit einer vorhandenen Enterprise Agreement-Registrierung verknüpft wurde, wählen Sie Ihren bevorzugten Tarif aus.  Weitere Informationen zu den einzelnen Tarifen finden Sie unter [Log Analytics – Preise](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Blatt zum Erstellen von Log Analytics-Ressourcen](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Klicken Sie nach dem Bereitstellen der erforderlichen Informationen im Bereich **Log Analytics-Arbeitsbereich** auf **OK**.  

Die Informationen werden überprüft, und der Arbeitsbereich wird erstellt. Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen. 

## <a name="next-steps"></a>Nächste Schritte
Sie besitzen einen verfügbaren Arbeitsbereich und können nun die Erfassung von Überwachungstelemetrie konfigurieren, Protokollsuchen zum Analysieren dieser Daten ausführen und eine Verwaltungslösung hinzufügen, um weitere daten- und analysebasierte Einblicke zu gewinnen. 

* Informationen zum Aktivieren der Datensammlung aus Azure-Ressourcen mit Azure-Diagnose oder Azure Storage finden Sie unter [Sammeln von Azure-Dienstprotokollen und Metriken zur Verwendung in Log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Fügen Sie System Center Operations Manager als Datenquelle hinzu](../platform/om-agents.md), um Daten von Agents zu sammeln, die an die Operations Manager-Verwaltungsgruppe berichten, und sie im Log Analytics-Arbeitsbereich zu speichern. 
* Stellen Sie eine Verbindung mit [Configuration Manager](../platform/collect-sccm.md) her, um Computer zu importieren, die Sammlungen in der Hierarchie angehören.  
* Sehen Sie sich die verfügbaren [Überwachungslösungen](../insights/solutions.md) sowie Informationen dazu an, wie Sie eine Lösung Ihrem Arbeitsbereich hinzufügen bzw. daraus entfernen.
