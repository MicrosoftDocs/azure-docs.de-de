---
title: Intelligente Erkennung – Anormaler Anstieg in Ausnahmevolume in Azure Application Insights | Microsoft Docs
description: Überwachen Sie Anwendungsausnahmen mit Azure Application Insights auf ungewöhnliche Muster im Ausnahmevolume.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: a6e7e8e01ccb623a3ff340c318c9c238c919cb38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61298583"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Anormaler Anstieg in Ausnahmevolume (Vorschau)

Application Insights analysiert die in Ihrer Anwendung ausgelösten Ausnahmen automatisch und kann Sie vor ungewöhnlichen Mustern in Ihrer Ausnahmetelemetrie warnen.

Diese Funktion erfordert keine spezielle Einrichtung, abgesehen von der [Konfiguration von Ausnahmemeldungen](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) für Ihre App. Sie ist aktiv, wenn Ihre App genügend Ausnahmetelemetriedaten generiert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wann erhalte ich diese Art von Benachrichtigung der intelligenten Erkennung?
Sie können diese Art von Benachrichtigung erhalten, wenn Ihre App eine anormale Zunahme der Anzahl von Ausnahmen eines bestimmten Typs während eines Tages aufweist, im Vergleich zu einer Baseline, die über die letzten sieben Tage berechnet wurde.
Machine Learning-Algorithmen werden verwendet, um den Anstieg der Anzahl der Ausnahmen zu erkennen, während gleichzeitig ein natürliches Wachstum der Anwendungsnutzung berücksichtigt wird.

## <a name="does-my-app-definitely-have-a-problem"></a>Liegt wirklich ein Problem mit meiner App vor?
Nein, eine Benachrichtigung bedeutet nicht zwingend, dass ein Problem mit Ihrer App vorliegt. Obwohl eine übermäßige Anzahl von Ausnahmen in der Regel auf ein Anwendungsproblem hinweist, haben diese Ausnahmen möglicherweise keine Auswirkungen und werden von Ihrer Anwendung ordnungsgemäß verarbeitet.

## <a name="how-do-i-fix-it"></a>Wie behebe ich das Problem?
Die Benachrichtigungen umfassen Diagnoseinformationen zur Unterstützung des Diagnoseprozesses:
1. **Selektierung.** Diese Benachrichtigung zeigt an, wie viele Benutzer oder wie viele Anforderungen betroffen sind. Dadurch können Sie dem Problem eine Priorität zuweisen.
2. **Umfang.** Betrifft das Problem den gesamten Datenverkehr oder nur bestimmte Vorgänge? Diese Informationen können Sie der Benachrichtigung entnehmen.
3. **Diagnose.** Die Erkennung enthält Informationen über die Methode, von der die Ausnahme ausgelöst wurde, und den Ausnahmetyp. Zur weiteren Diagnose des Problems können Sie auch die entsprechenden Elemente und Berichte verwenden, die mit weiterführenden Informationen verknüpft sind.