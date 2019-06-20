---
title: Kompatibilitätsgrad von Datenmodellen in Azure Analysis Services | Microsoft-Dokumentation
description: Grundlegendes zum Kompatibilitätsgrad von tabellarischen Datenmodellen
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 67a6c99253c549f0b8d3b55809b35b81756843eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61032604"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Kompatibilitätsgrad für tabellarische Analysis Services-Modelle

Der *Kompatibilitätsgrad* bezieht sich auf releasespezifische Verhalten in der Analysis Services-Engine. Änderungen am Kompatibilitätsgrad werden üblicherweise im Zuge der Hauptreleases von SQL Server vorgenommen. Diese Änderungen werden auch in Azure Analysis Services implementiert, um die Parität zwischen beiden Plattformen aufrechtzuerhalten. Änderungen am Kompatibilitätsgrad wirken sich auch auf die in Ihren tabellarischen Modellen verfügbaren Funktionen aus. Beispielsweise weisen DirectQuery und tabellarische Objektmetadaten je nach Kompatibilitätsgrad verschiedene Implementierungen auf. Der Kompatibilitätsgrad wird im tabellarischen Modellprojekt in Visual Studio (SSDT) angegeben.

Azure Analysis Services unterstützt tabellarische Modelle mit den Kompatibilitätsgraden 1200 und 1400. Der aktuelle Kompatibilitätsgrad ist 1400. Der jeweilige Grad stimmt mit SQL Server 2017 Analysis Services überein. Zu den wesentlichen Features im Kompatibilitätsgrad 1400 zählen Folgende:

*  Neue Features für Datenkonnektivität und Import mit Unterstützung von TOM-APIs und TMSL-Skripts. 
*  Datentransformations- und Datenmashupfunktionen mithilfe von Get Data- und M-Ausdrücken
*  Measures bieten Unterstützung für eine Detailzeileneigenschaft mit einem DAX-Ausdruck. Diese Eigenschaft aktiviert Clienttools wie Microsoft Excel, um ein Drilldown auf detaillierte Daten aus einem aggregierten Bericht auszuführen. Wenn Benutzer beispielsweise den monatlichen Gesamtumsatz für eine Region anzeigen, können sie die zugehörigen Auftragsdetails einsehen. 
*  Sicherheit auf Objektebene für Tabellen- und Spaltennamen sowie die darin enthaltenen Daten
*  Verbesserte Unterstützung für unregelmäßige Hierarchien
*  Verbesserungen an der Leistung und Überwachung

> [!NOTE]
> Azure Analysis Services unterstützt importierte Power BI Desktop-Dateien mit dem Kompatibilitätsgrad 1465. Der Import von Power BI Desktop, der immer nur als Previewfunktion bereitgestellt wurde, wird jedoch nicht mehr unterstützt und wurde im März 2019 aus dem Dienst entfernt. Vorhandene Modelle mit dem Kompatibilitätsgrad 1465 werden weiterhin unterstützt.  


## <a name="set-compatibility-level"></a>Festlegen des Kompatibilitätsgrads

 Beim Erstellen eines neuen Projekts für tabellarische Modelle in SSDT können Sie im Dialogfeld **Designer für tabellarische Modelle** den Kompatibilitätsgrad angeben. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Wenn Sie die Option **Diese Meldung nicht mehr anzeigen** auswählen, wird bei allen nachfolgenden Projekten der als Standard angegebene Kompatibilitätsgrad verwendet. Sie können den Standardkompatibilitätsgrad in SSDT unter **Extras** > **Optionen** ändern.  
  
 Um für ein vorhandenes Projekt für tabellarische Modelle ein Upgrade in SSDT durchzuführen, legen Sie im Fenster **Eigenschaften** des Modells die Eigenschaft **Kompatibilitätsgrad** fest. Denken Sie daran, dass ein Upgrade des Kompatibilitätsgrads nicht rückgängig gemacht werden kann.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Überprüfen des Kompatibilitätsgrads einer tabellarischen Modelldatenbank in SQL Server Management Studio 

 Klicken Sie in SSMS mit der rechten Maustaste auf den Datenbanknamen und dann auf **Eigenschaften** > **Kompatibilitätsgrad**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Überprüfen des unterstützten Kompatibilitätsgrads eines Servers in SSMS  

 Klicken Sie in SSMS mit der rechten Maustaste auf den Servernamen und dann auf **Eigenschaften** > **Unterstützter Kompatibilitätsgrad**.  
  
 Diese Eigenschaft gibt den höchsten Kompatibilitätsgrad einer Datenbank an, die auf dem Server (ausgenommen der Vorschau) ausgeführt wird. Der unterstützte Kompatibilitätsgrad kann nicht geändert werden.  

> [!NOTE]
> In SSMS wird bei einer bestehenden Verbindung zu einem Azure Analysis Services-Server für die Eigenschaft **Unterstützter Kompatibilitätsgrad** der Wert **1200** angezeigt. Dies ist ein bekanntes Problem und wird in einem zukünftigen SSMS-Update gelöst. Nach der Lösung wird diese Eigenschaft den höchsten unterstützten Kompatibilitätsgrad anzeigen.

## <a name="next-steps"></a>Nächste Schritte

  [Verwalten von Analysis Services](analysis-services-manage.md)  
