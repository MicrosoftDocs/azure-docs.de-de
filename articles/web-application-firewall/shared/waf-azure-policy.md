---
title: Azure Web Application Firewall und Azure Policy
description: Azure Web Application Firewall (WAF) in Kombination mit Azure Policy kann dabei helfen, organisatorische Standards durchzusetzen und die Einhaltung von Richtlinien für WAF-Ressourcen zu bewerten.
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: fd474a32b4a517230a82615065d7815c04140045
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432967"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Web Application Firewall und Azure Policy

Azure-Web Application Firewall (WAF) in Kombination mit Azure Policy kann zur Durchsetzung von Organisationsstandards und zur Bewertung von Compliance für WAF-Ressourcen beitragen. Azure Policy ist ein Governancetool, das eine aggregierte Ansicht zur Bewertung des Gesamtzustands der Umgebung mit der Möglichkeit bietet, einen Drilldown zur Granularität pro Ressource und Richtlinie durchzuführen. Außerdem trägt Azure Policy durch Massenwartung für vorhandene Ressourcen und automatische Wartung dazu bei, dass Ihre Ressourcen Complianceanforderungen erfüllen.

## <a name="azure-policy-for-web-application-firewall"></a>Azure Policy für Web Application Firewall

Es gibt mehrere integrierte Azure Policy-Definitionen zum Verwalten von WAF-Ressourcen. Aufschlüsselung der Richtliniendefinitionen und ihrer Funktionalität:

1. **Web Application Firewall (WAF) muss für Azure Front Door Service aktiviert sein**: Azure Front Door Services werden ausgewertet, wenn für die Ressourcenerstellung eine WAF vorhanden ist. Die Richtlinie besitzt drei Auswirkungen: Audit (Überwachen), Deny (Verweigern) und Disable (Deaktivieren). „Audit“ (Überwachen) verfolgt nach, wenn ein Azure Front Door Service über keine WAF verfügt und zeigt Benutzern an, welcher Azure Front Door Service nicht konform ist. „Deny“ (Verweigern) verhindert, dass ein Azure Front Door Service erstellt wird, wenn keine WAF angefügt ist. „Disable“ (Deaktivieren) deaktiviert diese Richtlinie.

2. **Web Application Firewall (WAF) muss für Application Gateway aktiviert sein**: Application Gateways werden ausgewertet, wenn für die Ressourcenerstellung eine WAF vorhanden ist. Die Richtlinie besitzt drei Auswirkungen: Audit (Überwachen), Deny (Verweigern) und Disable (Deaktivieren). „Audit“ (Überwachen) verfolgt nach, wenn ein Application Gateway über keine WAF verfügt und zeigt Benutzern an, welches Application Gateway nicht konform ist. „Deny“ (Verweigern) verhindert, dass ein Application Gateway erstellt wird, wenn keine WAF angefügt ist. „Disable“ (Deaktivieren) deaktiviert diese Richtlinie.

3. **Web Application Firewall (WAF) muss den angegebenen Modus für Azure Front Door Service verwenden**: Legt fest, dass die Verwendung des Modus „Detection“ (Erkennung) oder „Prevention“ (Prävention) für alle Web Application Firewall-Richtlinien für Azure Front Door Service aktiv ist. Die Richtlinie besitzt drei Auswirkungen: Audit (Überwachen), Deny (Verweigern) und Disable (Deaktivieren). „Audit“ (Überwachen) verfolgt nach, wenn eine WAF nicht in den angegebenen Modus passt. „Deny“ (Verweigern) verhindert, dass eine WAF erstellt wird, wenn sie sich nicht im richtigen Modus befindet. „Disable“ (Deaktivieren) deaktiviert diese Richtlinie.

4. **Web Application Firewall (WAF) muss den angegebenen Modus für Application Gateway verwenden**: Legt fest, dass die Verwendung des Modus „Detection“ (Erkennung) oder „Prevention“ (Prävention) für alle Web Application Firewall-Richtlinien für Application Gateway aktiv ist. Die Richtlinie besitzt drei Auswirkungen: Audit (Überwachen), Deny (Verweigern) und Disable (Deaktivieren). „Audit“ (Überwachen) verfolgt nach, wenn eine WAF nicht in den angegebenen Modus passt. „Deny“ (Verweigern) verhindert, dass eine WAF erstellt wird, wenn sie sich nicht im richtigen Modus befindet. „Disable“ (Deaktivieren) deaktiviert diese Richtlinie.

## <a name="launch-an-azure-policy"></a>Starten von Azure Policy

1.  Geben Sie auf der Azure-Homepage „Policy“ (Richtlinie) in die Suchleiste ein, und klicken Sie auf das Symbol „Azure Policy“.

2.  Wählen Sie im Azure Policy-Dienst unter **Erstellung** die Option **Zuweisungen** aus.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Registerkarte „Zuweisungen“ innerhalb von Azure Policy":::

3.  Wählen Sie oben auf der Seite „Zuweisungen“ das Symbol **Richtlinie zuweisen** aus.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="Registerkarte „Grundlagen“ auf der Seite „Richtlinie zuweisen“":::

4.  Aktualisieren Sie auf der Registerkarte „Grundlagen“ der Seite „Richtlinie zuweisen“ die folgenden Felder:
    1.  **Bereich:** Wählen Sie aus, welche Azure-Abonnements und Ressourcengruppen von der Richtliniendefinition betroffen sein sollen.
    2.  **Ausschlüsse**: Wählen Sie alle Ressourcen im Bereich aus, die aus der Richtlinienzuweisung ausgeschlossen werden sollen.
    3.  **Richtliniendefinition**: Wählen Sie die Richtliniendefinition aus, die für den Bereich mit Ausschlüssen gelten soll. Geben Sie in der Suchleiste „Web Application Firewall“ ein, um die relevante Azure Policy-Definition für Web Application Firewall auszuwählen.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="Registerkarte „Grundlagen“ auf der Seite „Richtlinie zuweisen“":::

5.  Wählen Sie die Registerkarte **Parameter** aus, und aktualisieren Sie die Parameter der Richtlinienzuweisung. Um die Funktion des Parameters genauer zu verdeutlichen, zeigen Sie auf das Infosymbol neben dem Parameternamen, um weitere Informationen zu erhalten.

6.  Wählen Sie **Überprüfen und erstellen** aus, um Ihre Richtlinienzuweisung fertigzustellen. Es dauert ca. 15 Minuten, bis die Richtlinienzuweisung für neue Ressourcen gilt.
