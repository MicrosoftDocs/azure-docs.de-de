---
title: Grundlegendes zur proaktiven Protokollsammlung auf Azure Stack Edge Pro-Geräten
description: Erfahren Sie, wie die proaktive Protokollsammlung auf einem Azure Stack Edge Pro-Gerät ausgeführt wird.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465399"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Proaktive Protokollsammlung auf Azure Stack Edge-Geräten

Sie können die Protokollsammlung auf Ihrem Azure Stack Edge-Gerät anhand von Indikatoren zur Systemintegrität aktivieren, um bei Geräteproblemen effiziente Maßnahmen zur Problembehandlung einleiten zu können. In diesem Artikel wird beschrieben, was eine proaktive Protokollsammlung ist, wie sie aktiviert wird und wie Daten verarbeitet werden, wenn die proaktive Protokollsammlung aktiviert ist.
   
Die Informationen in diesem Artikel beziehen sich auf Geräte vom Typ „Azure Stack Edge Pro – GPU“, „Azure Stack Edge Pro R“ und „Azure Stack Edge Mini R“.

## <a name="about-proactive-log-collection"></a>Informationen zur proaktiven Protokollsammlung

Die Kundendienst- und Technikerteams bei Microsoft nutzen die Systemprotokolle von Ihrem Azure Stack Edge-Gerät, um Probleme, die während des Betriebs auftreten können, effizient identifizieren und beheben zu können. Die proaktive Protokollsammlung stellt eine Methode dar, mit der Microsoft darüber benachrichtigt wird, dass auf der Azure Stack Edge-Appliance des Kunden ein Problem/Ereignis erkannt wurde. (Im Abschnitt „Indikatoren der proaktiven Protokollsammlung“ finden Sie die Ereignisse, die nachverfolgt werden.) Die Supportprotokolle zu diesem Problem werden automatisch in ein Azure Storage-Konto hochgeladen, das von Microsoft verwaltet und gesteuert wird. Der Microsoft-Support und Microsoft-Techniker prüfen diese Supportprotokolle, um die beste Vorgehensweise für das Beheben des Kundenproblems zu ermitteln.    

> [!NOTE]
> Diese Protokolle werden nur zu Debuggingzwecken verwendet und bieten Kunden Unterstützung bei Problemen. 


## <a name="enabling-proactive-log-collection"></a>Aktivieren der proaktiven Protokollsammlung

Sie können die proaktive Protokollsammlung aktivieren, während Sie das Gerät über die lokale Benutzeroberfläche aktivieren. 

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche des Geräts zur Seite **Erste Schritte**.
2. Wählen Sie auf der Kachel **Aktivierung** die Option **Aktivieren** aus. 

    ![Seite „Clouddetails“ auf der lokalen Webbenutzeroberfläche 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. Im Bereich **Aktivieren**:
    1. Geben Sie den **Aktivierungsschlüssel** ein, den Sie unter [Aktivierungsschlüssel für Azure Stack Edge Pro R abrufen](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key) abgerufen haben.

    1. Sie können die proaktive Protokollsammlung aktivieren, damit Microsoft Protokolle basierend auf dem Integritätsstatus des Geräts sammeln kann. Die auf diese Weise gesammelten Protokolle werden in ein Azure Storage-Konto hochgeladen.
    
    1. Wählen Sie **Übernehmen**.

    ![Seite „Clouddetails“ auf der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>Indikatoren der proaktiven Protokollsammlung

Nachdem die proaktive Protokollsammlung aktiviert wurde, werden Protokolle automatisch hochgeladen, wenn eines der folgenden Ereignisse auf dem Gerät erkannt wird:  


|Warnung/Fehler/Bedingung  |BESCHREIBUNG  |
|---------|---------|
|AcsUnhealthyCondition     |Azure-konsistente Dienste sind fehlerhaft.         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge-Agent wird nicht ausgeführt.         |
|UpdateInstallFailedEvent | Das Update konnte nicht installiert werden.        |

 
Microsoft wird dieser Liste in Zukunft noch weitere Ereignisse hinzufügen. Für neue Ereignisse wird keine erneute Einwilligung benötigt. Auf dieser Seite finden Sie Informationen zu den aktuellen Indikatoren der proaktiven Protokollsammlung.    
 

## <a name="other-log-collection-methods"></a>Andere Methoden der Protokollsammlung

Neben der proaktiven Protokollsammlung, bei der bestimmte Protokolle für ein bestimmtes ermitteltes Problem gesammelt werden, gibt es weitere Protokollsammlungen, die ein tiefer gehendes Verständnis der Systemintegrität und des Systemverhaltens bieten können. Solche Protokollsammlungen können normalerweise während einer Supportanfrage ausgeführt oder basierend auf Telemetriedaten vom Gerät durch Microsoft ausgelöst werden.  

## <a name="handling-data"></a>Verarbeiten der Daten

Wenn Kunden die proaktive Protokollsammlung aktivieren, erklären sie sich damit einverstanden, dass Microsoft Protokolle vom Azure Stack Edge-Gerät sammelt, wie in diesem Dokument beschrieben. Darüber hinaus erklären sich die Kunden damit einverstanden, dass diese Protokolle in ein von Microsoft verwaltetes und gesteuertes Azure Storage-Konto hochgeladen und dort aufbewahrt werden.

Microsoft nutzt die Daten ausschließlich zur Problembehandlung bei der Systemintegrität und bei Problemen. Die Daten werden nicht ohne Einwilligung der Kunden für Marketing-, Werbungs- oder andere kommerzielle Zwecke verwendet. 

Die von Microsoft gesammelten Daten werden gemäß unseren standardmäßigen Datenschutzbestimmungen behandelt. Sollte ein Kunde seine Einwilligung für die proaktive Protokollsammlung widerrufen, betrifft dies keine Daten, die mit Einwilligung vor dem Widerruf gesammelt wurden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [ein Supportpaket erhalten](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).