---
title: Azure AD Connect Health – Diagnostizieren von Synchronisierungsfehlern aufgrund doppelter Attribute | Microsoft-Dokumentation
description: In diesem Dokument werden der Diagnoseprozess für Synchronisierungsfehler aufgrund doppelter Attribute und die potenzielle Behebung von Szenarien mit verwaisten Objekten direkt über das Azure-Portal beschrieben.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbdeef7c591221756ad206bf2f3dd78ac3d26c4f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60349980"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnose und Behebung von Synchronisierungsfehlern aufgrund doppelter Attribute

## <a name="overview"></a>Übersicht
In Bezug auf Synchronisierungsfehler wird Azure Active Directory (Azure AD) Connect Health erweitert und eine Self-Service-Lösung eingeführt. Diese Lösung kann für die Problembehandlung bei Synchronisierungsfehlern aufgrund doppelter Attribute und zur Behebung von in Azure AD verwaisten Objekten verwendet werden.
Das Diagnosefeature hat folgende Vorteile:
- Bereitstellung eines Diagnoseverfahrens zum Eingrenzen von Synchronisierungsfehlern aufgrund doppelter Attribute sowie Angeben von spezifischen Fehlerbehebungen
- Anwenden einer Fehlerbehebung für dedizierte Szenarien aus Azure AD, um den Fehler in nur einem Schritt zu beheben
- Kein Upgrade- oder Konfigurationsaufwand zur Aktivierung dieses Features
Weitere Informationen zu Azure AD finden Sie unter [Identitätssynchronisierung und Resilienz bei doppelten Attributen](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Probleme
### <a name="a-common-scenario"></a>Allgemeines Szenario
Wenn die Synchronisierungsfehler **QuarantinedAttributeValueMustBeUnique** und **AttributeValueMustBeUnique** auftreten, kommt es in Azure AD häufig zu einem **UserPrincipalName**- oder **ProxyAddresses**-Konflikt. Sie können die Synchronisierungsfehler möglicherweise beheben, indem Sie das in Konflikt stehende Quellobjekt auf lokaler Seite aktualisieren. Der Synchronisierungsfehler wird nach der nächsten Synchronisierung behoben. In der folgenden Abbildung ist beispielsweise dargestellt, dass für zwei Benutzer ein Konflikt in Bezug auf den Benutzerprinzipalnamen (**UserPrincipalName**) besteht. Beide lauten **Joe.J\@contoso.com**. Die in Konflikt stehenden Objekte werden in Azure AD unter Quarantäne gestellt.

![Diagnostizieren von Synchronisierungsfehlern – allgemeines Szenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Szenario: Verwaistes Objekt
Gelegentlich kann es vorkommen, dass für einen vorhandenen Benutzer der **Quellanker** verloren geht. Das Löschen des Quellobjekts ist in der lokalen Active Directory-Instanz erfolgt. Die Änderung des Löschsignals wurde jedoch nicht mit Azure AD synchronisiert. Gründe für den Verlust können z.B. Probleme mit dem Synchronisierungsmodul oder die Migration einer Domäne sein. Wenn dasselbe Objekt wiederhergestellt oder neu erstellt wird, sollte folgerichtig ein vorhandener Benutzer der Benutzer sein, der die Synchronisierung vom **Quellanker** durchführt. 

Für einen vorhandenen Benutzer (als ein auf die Cloud beschränktes Objekt) ist es auch möglich, dass der in Konflikt stehende Benutzer mit Azure AD synchronisiert wird. Der Benutzer kann nicht synchron mit dem vorhandenen Objekt abgeglichen werden. Es gibt keine Möglichkeit, den **Quellanker** direkt neu zuzuordnen. Weitere Informationen dazu finden Sie in der [Knowledge Base](https://support.microsoft.com/help/2647098). 

Beispiel: Das vorhandene Objekt in Azure AD behält die Lizenz von Joe bei. Ein neu synchronisiertes Objekt mit einem anderen **Quellanker** tritt in einem Zustand mit doppeltem Attribut in Azure AD auf. Änderungen für Joe in der lokalen Active Directory-Instanz werden nicht auf den ursprünglichen Benutzer von Joe (vorhandenes Objekt) in Azure AD angewandt.  

![Diagnostizieren von Synchronisierungsfehlern – Szenario mit verwaistem Objekt](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Schritte für die Diagnose und Problembehandlung in Connect Health 
Das Diagnosefeature unterstützt Benutzerobjekte mit den folgenden doppelten Attributen:

| Attributname | Typen von Synchronisierungsfehlern|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique oder AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique oder AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique (Wert für Attribut muss eindeutig sein) | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique (Wert für Attribut muss eindeutig sein) |

>[!IMPORTANT]
> Für den Zugriff auf dieses Feature ist die Berechtigung **Globaler Administrator** oder **Mitwirkender** aus den RBAC-Einstellungen erforderlich.
>

Führen Sie die Schritte über das Azure-Portal aus, um die Details zu Synchronisierungsfehlern eingrenzen und spezifischere Lösungen bereitstellen zu können:

![Diagnoseschritte bei Synchronisierungsfehlern](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Führen Sie im Azure-Portal mehrere Schritte aus, um bestimmte behebbare Szenarien zu identifizieren:  
1.  Überprüfen Sie die Spalte mit dem **Diagnosestatus**. Der Status gibt an, ob eine Möglichkeit besteht, einen Synchronisierungsfehler direkt in Azure Active Directory zu beheben. Das heißt, in diesem Fall ist ein Problembehandlungsablauf verfügbar, mit dem der Fehler eingegrenzt und möglicherweise behoben werden kann.

| Status | Was bedeutet das? |
| ------------------ | -----------------|
| Nicht gestartet | Sie haben den Diagnoseprozess nicht aufgerufen. Je nach Diagnoseergebnis besteht unter Umständen die Möglichkeit, den Synchronisierungsfehler direkt über das Portal zu beheben. |
| Manuelle Korrektur erforderlich | Der Fehler erfüllt nicht die Kriterien für verfügbare Korrekturen über das Portal. Entweder sind die in Konflikt stehenden Objekttypen keine Benutzer, oder Sie haben die Diagnoseschritte bereits ausgeführt, und im Portal war keine Korrektur verfügbar. In letzterem Fall ist die Korrektur auf lokaler Seite weiterhin eine mögliche Lösung. [Erfahren Sie mehr zu lokalen Fehlerkorrekturen.](https://support.microsoft.com/help/2647098) | 
| Synchronisierung ausstehend | Eine Korrektur wurde angewandt. Im Portal wird auf den nächsten Synchronisierungszyklus gewartet, um den Fehler zu beheben. |

  >[!IMPORTANT]
  > Die Spalte mit dem Diagnosestatus wird nach jedem Synchronisierungszyklus zurückgesetzt. 
  >

1. Wählen Sie die Schaltfläche **Diagnose** unter den Fehlerdetails aus. Sie beantworten mehrere Fragen und identifizieren Details zu Synchronisierungsfehlern. Die Antworten auf die Fragen tragen zum Identifizieren eines verwaisten Objekts bei.

1. Wenn am Ende des Diagnosevorgangs die Schaltfläche **Schließen** angezeigt wird, steht basierend auf Ihren Antworten keine schnelle Fehlerbehebung über das Portal zur Verfügung. Weitere Informationen finden Sie unter der Lösung, die im letzten Schritt angezeigt wird. Lokale Fehlerkorrekturen gelten weiterhin als Lösung. Wählen Sie die Schaltfläche **Schließen** aus. Der Status des aktuellen Synchronisierungsfehlers wird in **Manuelle Korrektur erforderlich** geändert. Der Status wird während des aktuellen Synchronisierungszyklus beibehalten.

1. Nachdem der Fall eines verwaisten Objekts identifiziert wurde, können Sie Synchronisierungsfehler aufgrund doppelter Attribute direkt über das Portal beheben. Wählen Sie die Schaltfläche **Fix anwenden** aus, um den Prozess auszulösen. Der Status des aktuellen Synchronisierungsfehlers wird in **Synchronisierung ausstehend** aktualisiert.

1. Nach dem nächsten Synchronisierungszyklus sollte der Fehler aus der Liste entfernt werden.

## <a name="how-to-answer-the-diagnosis-questions"></a>Beantworten von Fragen zur Diagnose 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Ist der Benutzer in Ihrer lokalen Active Directory-Instanz vorhanden?

Mit dieser Frage wird versucht, das Quellobjekt des vorhandenen Benutzers in der lokalen Active Directory-Instanz zu identifizieren.  
1. Überprüfen Sie, ob Azure Active Directory ein Objekt mit dem angegebenen **UserPrincipalName** enthält. Wenn dies nicht der Fall ist, antworten Sie mit **Nein**.
2. Wenn dies der Fall ist, überprüfen Sie, ob das Objekt weiterhin im Bereich für die Synchronisierung enthalten ist.  
   - Suchen Sie im Azure AD-Connectorbereich unter Verwendung des DN.
   - Wenn das Objekt mit dem Status **Ausstehender Hinzufügevorgang** gefunden wird, antworten Sie mit **Nein**. Azure AD Connect kann für das Objekt keine Verbindung mit dem richtigen Azure AD-Objekt herstellen.
   - Wenn das Objekt nicht gefunden wird, antworten Sie mit **Ja**.

In diesen Beispielen soll mit der Frage ermittelt werden, ob **Joe Jackson** in der lokalen Active Directory-Instanz noch vorhanden ist.
Für das **allgemeine Szenario** ist sowohl der Benutzer **Joe Johnson** als auch der Benutzer **Joe Jackson** in der lokalen Active Directory-Instanz vorhanden. Bei den unter Quarantäne gestellten Objekten handelt es sich um zwei unterschiedliche Benutzer.

![Diagnostizieren von Synchronisierungsfehlern – allgemeines Szenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Für das **Szenario mit verwaistem Objekt** ist nur der Benutzer **Joe Johnson** in der lokalen Active Directory-Instanz vorhanden:

![Diagnostizieren von Synchronisierungsfehlern – Szenario mit verwaistem Objekt (Benutzer vorhanden?)](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Gehören beide Konten zu demselben Benutzer?
Mit dieser Frage werden in Konflikt stehende eingehende Benutzer und das vorhandene Benutzerobjekt in Azure AD überprüft, um zu ermitteln, ob sie zu demselben Benutzer gehören.  
1. Das in Konflikt stehende Objekt wird neu mit Azure Active Directory synchronisiert. Vergleichen Sie die Attribute der Objekte:  
   - Anzeigename
   - Benutzerprinzipalname
   - Object ID (Objekt-ID)
2. Wenn ein Vergleich in Azure AD nicht möglich ist, überprüfen Sie, ob in Active Directory Objekte mit den angegebenen **UserPrincipalNames** enthalten sind. Wenn beide vorhanden sind, antworten Sie mit **Nein**.

Im folgenden Beispiel gehören die beiden Objekte zu demselben Benutzer **Joe Johnson**.

![Diagnostizieren von Synchronisierungsfehlern – Szenario mit verwaistem Objekt (Benutzer identisch?)](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Nach der Anwendung der Korrektur im Szenario mit verwaistem Objekt
Basierend auf den Antworten auf die oben genannten Fragen wird die Schaltfläche **Fix anwenden** angezeigt, wenn über Azure AD eine Korrektur verfügbar ist. In diesem Fall wird das lokale Objekt mit einem unerwarteten Azure AD-Objekt synchronisiert. Die beiden Objekte werden über den **Quellanker** zugeordnet. Bei der Änderung über **Fix anwenden** werden folgende oder ähnliche Schritte ausgeführt:
1. Aktualisieren des **Quellankers** auf das richtige Objekt in Azure AD
2. Löschen des in Konflikt stehenden Objekts in Azure AD, falls vorhanden

![Diagnostizieren von Synchronisierungsfehlern – nach der Korrektur](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> Die Änderung über **Fix anwenden** gilt nur für die Fälle mit verwaisten Objekten.
>

Nach den oben beschriebenen Schritten kann der Benutzer auf die ursprüngliche Ressource zugreifen, die mit einem vorhandenen Objekt verknüpft ist. Der Wert zum **Diagnosestatus** in der Listenansicht wird in **Synchronisierung ausstehend** aktualisiert. Der Synchronisierungsfehler wird nach der nächsten Synchronisierung behoben. In Connect Health wird der behobene Synchronisierungsfehler nicht mehr in der Listenansicht angezeigt.

## <a name="failures-and-error-messages"></a>Fehler und Fehlermeldungen
**Der Benutzer mit dem konfliktverursachenden Attribut wird in Azure Active Directory vorläufig gelöscht. Stellen Sie sicher, dass der Benutzer dauerhaft gelöscht wird, bevor Sie den Vorgang wiederholen.**  
Der Benutzer mit dem konfliktverursachenden Attribut in Azure AD muss bereinigt werden, bevor Sie den Fix anwenden können. Lesen Sie die Informationen zum [endgültigen Löschen des Benutzers in Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore), bevor Sie die Anwendung des Fixes wiederholen. Darüber hinaus wird der Benutzer 30 Tage nach seiner vorläufigen Löschung automatisch endgültig gelöscht. 

**Die Aktualisierung des Quellankers für cloudbasierte Benutzer in Ihrem Mandanten wird nicht unterstützt.**  
Cloudbasierte Benutzer in Azure AD dürfen keinen Quellanker besitzen. Die Aktualisierung des Quellankers wird in diesem Fall nicht unterstützt. Ein manueller Fix aus der lokalen Umgebung ist erforderlich. 

## <a name="faq"></a>Häufig gestellte Fragen
**F.** Was passiert, wenn beim Anwenden der Änderung über **Fix anwenden** ein Fehler auftritt?  
**A.** Bei einer fehlerhaften Ausführung kann es sein, dass Azure AD Connect einen Export durchführt und der Fehler deshalb auftritt. Aktualisieren Sie die Portalseite, und wiederholen Sie den Vorgang nach der nächsten Synchronisierung. Der Synchronisierungszyklus dauert standardmäßig 30 Minuten. 


**F.** Was passiert, wenn das **vorhandene Objekt** das zu löschende Objekt ist?  
**A.** Wenn das **vorhandene Objekt** gelöscht werden soll, umfasst der Prozess keine Änderung des **Quellankers**. Normalerweise können Sie die Behebung über die lokale AD-Instanz durchführen. 


**F.** Welche Berechtigung benötigt ein Benutzer, um die Fehlerbehebung anwenden zu können?  
**A.** **Globaler Administrator** oder **Mitwirkender** aus den RBAC-Einstellungen sind die Berechtigungen, die den Zugriff auf den Diagnose- und Problembehandlungsprozess ermöglichen.


**F.** Muss ich Azure AD Connect konfigurieren oder den Azure AD Connect Health-Agent für dieses Feature aktualisieren?  
**A.** Nein. Der Diagnoseprozess ist ein ausschließlich cloudbasiertes Feature.


**F.** Wird für das Objekt während des Diagnoseprozesses wieder der aktive Zustand hergestellt, wenn das vorhandene Objekt vorläufig gelöscht wird?  
**A.** Nein. Bei der Korrektur werden über den **Quellanker** hinaus keine Objektattribute aktualisiert.
