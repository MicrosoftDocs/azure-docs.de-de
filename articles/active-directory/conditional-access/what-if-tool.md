---
title: What If-Tool für den bedingten Zugriff – Azure Active Directory
description: Erfahren Sie, welche Auswirkungen die Richtlinien für bedingten Zugriff auf Ihre Umgebung haben.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3ac799203cade3a907acbe28dee3a8023891db2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077641"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Beheben von Problemen mit dem What If-Tool beim bedingten Zugriff

[Bedingter Zugriff](./overview.md) ist eine Funktion von Azure Active Directory (Azure AD), mit der Sie den Zugriff autorisierter Benutzer auf Ihre Cloud-Apps steuern können. Wie wissen Sie, was Sie von den Richtlinien für bedingten Zugriff in Ihrer Umgebung erwarten können? Um diese Frage zu beantworten, können Sie das **Was-wäre-wenn-Tool für den bedingten Zugriff** verwenden.

In diesem Artikel wird erläutert, wie Sie dieses Tool zum Testen Ihrer Richtlinien für den bedingten Zugriff verwenden können.

## <a name="what-it-is"></a>Funktionsbeschreibung

Mit dem **Was-wäre-wenn-Tool für den bedingten Zugriff** erhalten Sie Einblicke in die Auswirkungen Ihrer Richtlinien für den bedingten Zugriff auf Ihre Umgebung. Anstelle von Tests Ihrer Richtlinien mithilfe mehrerer manueller Anmeldevorgänge ermöglicht dieses Tool die Auswertung simulierter Benutzeranmeldungen. Die Simulation schätzt die Auswirkungen dieser Anmeldungen auf Ihre Richtlinien ab und generiert einen Simulationsbericht. Der Bericht listet nicht nur die angewendeten Richtlinien für den bedingten Zugriff auf, sondern auch [klassische Richtlinien](policy-migration.md#classic-policies) (sofern vorhanden).    

Das **Was-wäre-wenn** -Tool stellt auch eine Möglichkeit dar, die Richtlinien zu ermitteln, die für einen bestimmten Benutzer gelten. Sie können diese Informationen z.B. nutzen, um ein Problem zu beheben.    

## <a name="how-it-works"></a>Funktionsweise

Sie müssen im **Was-wäre-wenn-Tool für den bedingten Zugriff** zunächst die Einstellungen des Anmeldeszenarios konfigurieren, das Sie simulieren möchten. Dies umfasst Folgendes:

- Den Benutzer, den Sie testen möchten 
- Die Cloud-Apps, auf die der Benutzer zuzugreifen versucht
- Die Bedingungen, unter denen der Zugriff auf die konfigurierten Cloud-Apps erfolgt
     
Im nächsten Schritt können Sie eine Simulation initiieren, die Ihre Einstellungen auswertet. Bei der Auswertung werden nur die aktivierten Richtlinien berücksichtigt.

Wenn die Auswertung abgeschlossen ist, generiert das Tool einen Bericht über die betroffenen Richtlinien. Weitere Informationen zu einer Richtlinie für bedingten Zugriff finden Sie auch in der [Arbeitsmappe für Erkenntnisse und Berichterstellung zum bedingten Zugriff](howto-conditional-access-insights-reporting.md), die zusätzliche Details zu Richtlinien im reinen Berichtsmodus und den derzeit aktivierten Richtlinien bereitstellt.

## <a name="running-the-tool"></a>Ausführen des Tools

Sie finden das **Was-wäre-wenn-Tool** im Azure-Portal auf der Seite **[Bedingter Zugriff – Richtlinien](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** .

Zum Starten des Tools klicken Sie auf der Symbolleiste über der Liste der Richtlinien auf **Was-wäre-wenn**.

:::image type="content" source="./media/what-if-tool/01.png" alt-text="Screenshot: Seite „Bedingter Zugriff – Richtlinien“ im Azure-Portal. Auf der Symbolleiste ist das Element „What If“ hervorgehoben." border="false":::

Bevor Sie eine Auswertung durchführen können, müssen Sie die Einstellungen konfigurieren.

## <a name="settings"></a>Einstellungen

Dieser Abschnitt enthält Informationen zu den Einstellungen für die Simulation.

:::image type="content" source="./media/what-if-tool/02.png" alt-text="Screenshot: Seite „What If“ im Azure-Portal mit Feldern für Benutzer, Cloud-Apps, IP-Adresse, Geräteplattform, Client-App und Anmelderisiko" border="false":::

### <a name="user"></a>Benutzer

Sie können nur einen Benutzer auswählen. Dies ist das einzige erforderliche Feld.

### <a name="cloud-apps"></a>Cloud-Apps

Der Standardwert für diese Einstellung ist **Alle Cloud-Apps**. Bei der Standardeinstellung wird eine Auswertung aller verfügbaren Richtlinien in Ihrer Umgebung durchgeführt. Sie können den Bereich auf Richtlinien begrenzen, die Auswirkungen auf bestimmte Cloud-Apps haben.

### <a name="ip-address"></a>IP-Adresse

Die IP-Adresse ist eine einzelne IPv4-Adresse zum Imitieren der [Standortbedingung](location-condition.md). Die Adresse stellt die Adresse des Geräts mit Internetzugang dar, die der Benutzer für die Anmeldung verwendet. Sie können die IP-Adresse eines Geräts überprüfen, indem Sie beispielsweise zu [WhatIsMyIPAddress.com](https://whatismyipaddress.com) navigieren.    

### <a name="device-platforms"></a>Geräteplattformen

Diese Einstellung imitiert die [Geräteplattformbedingung](concept-conditional-access-conditions.md#device-platforms) und stellt die Entsprechung von **Alle Plattformen (auch nicht unterstützte)** dar. 

### <a name="client-apps"></a>Client-Apps

Diese Einstellung imitiert die [Client-Apps-Bedingung](concept-conditional-access-conditions.md#client-apps).
Standardmäßig bewirkt diese Einstellung eine Auswertung aller Richtlinien, bei denen **Browser** und/oder **Mobile Apps und Desktopclients** aktiviert ist. Damit werden außerdem Richtlinien erkannt, die **Exchange ActiveSync (EAS)** erzwingen. Sie können diese Einstellung eingrenzen, indem Sie Folgendes auswählen:

- **Browser** , um alle Richtlinien auszuwerten, für die mindestens **Browser** ausgewählt wurde. 
- **Mobile Apps und Desktopclients** , um alle Richtlinien auszuwerten, für die mindestens **Mobile Apps und Desktopclients** ausgewählt wurde. 

### <a name="sign-in-risk"></a>Anmelderisiko

Diese Einstellung imitiert die [Anmelderisikobedingung](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Auswertung 

Sie starten eine Auswertung, indem Sie auf **Was-wäre-wenn** klicken. Das Auswertungsergebnis wird Ihnen in Form eines Berichts präsentiert, der Folgendes enthält: 

:::image type="content" source="./media/what-if-tool/03.png" alt-text="Screenshot: Auswertungsbericht. Im Text ist angegeben, dass mindestens eine klassische Richtlinie konfiguriert ist. Zum Anzeigen von Richtlinien stehen Registerkarten zur Verfügung." border="false":::

- Einen Indikator, ob klassische Richtlinien in Ihrer Umgebung vorhanden sind
- Richtlinien, die für den Benutzer gelten
- Richtlinien, die für den Benutzer nicht gelten

Wenn [klassische Richtlinien](policy-migration.md#classic-policies) für die ausgewählten Cloud-Apps vorhanden ist, wird ein Indikator angezeigt. Durch Klicken auf den Indikator werden Sie zur Seite „Klassische Richtlinien“ umgeleitet. Auf der Seite „Klassische Richtlinien“ können Sie eine klassische Richtlinie migrieren oder einfach deaktivieren. Schließen Sie diese Seite, um zu den Auswertungsergebnissen zurückzukehren.

In der Liste der Richtlinien, die für den ausgewählten Benutzer gelten, finden Sie auch eine Liste der [Gewährungssteuerelemente](concept-conditional-access-grant.md) und [Sitzungssteuerelemente](concept-conditional-access-session.md), die der Benutzer erfüllen muss.

In der Liste der Richtlinien, die nicht für den Benutzer gelten, finden Sie auch die Gründe dafür, warum diese Richtlinien nicht gelten. Für jede der aufgeführten Richtlinien entspricht der Grund der ersten Bedingung, die nicht erfüllt wurde. Falls eine Richtlinie nicht angewendet wird, könnte sie deaktiviert worden sein, da sie nicht weiter auswertet wird.   

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Richtlinie für bedingten Zugriff finden Sie unter [Erkenntnisse und Berichterstellung zum bedingten Zugriff](howto-conditional-access-insights-reporting.md), wenn Sie Richtlinien im reinen Berichtsmodus verwenden.
- Wenn Sie bereit sind, Richtlinien für bedingten Zugriff für Ihre Umgebung zu konfigurieren, lesen Sie den Artikel [Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md).
