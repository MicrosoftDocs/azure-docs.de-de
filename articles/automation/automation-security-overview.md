---
title: Einführung in die Authentifizierung in Azure Automation
description: Dieser Artikel enthält eine Übersicht über die Automation-Sicherheit und die unterschiedlichen Authentifizierungsmethoden, die für Automation-Konten in Azure Automation verfügbar sind.
keywords: Automation-Sicherheit, sicher Automation; Automation-Authentifizierung
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ROBOTS: NOINDEX
ms.openlocfilehash: 60175a5b9309c08d5fdab997469a9d0526afaefe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60737022"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Einführen in die Authentifizierung in Azure Automation  
Mit Azure Automation können Sie Aufgaben für Ressourcen in Azure, lokal und mit anderen Cloudanbietern, z.B. Amazon Web Services (AWS), automatisieren.  Damit ein Runbook die erforderlichen Aktionen ausführen kann, muss es über Berechtigungen für den sicheren Zugriff auf die Ressourcen mit den geringstmöglichen Rechten im Abonnement verfügen.

In diesem Artikel werden die unterschiedlichen Authentifizierungsszenarien beschrieben, die von Azure Automation unterstützt werden. Außerdem wird gezeigt, wie Sie für die Umgebung bzw. Umgebungen, die Sie verwalten müssen, die ersten Schritte durchführen.  

## <a name="automation-account-overview"></a>Automation-Konto – Übersicht
Wenn Sie das erste Mal mit Azure Automation arbeiten, müssen Sie zunächst mindestens ein Automation-Konto erstellen. Mithilfe von Automation-Konten können Sie Ihre Automation-Ressourcen (Runbooks, Objekte, Konfigurationen) von den Ressourcen in anderen Automation-Konten isolieren. Sie können Automation-Konten dazu verwenden, um Ressourcen in separate logische Umgebungen zu trennen. Beispielsweise können Sie ein Konto für die Entwicklung, ein Konto für die Produktion und ein Konto für Ihre lokale Umgebung nutzen.  Ein Azure Automation-Konto unterscheidet sich von einem Microsoft-Konto, das unter Ihrem Azure-Abonnement erstellt wird.

Die Automation-Ressourcen für jedes Automation-Konto sind mit einer einzelnen Azure-Region verknüpft, aber die Automation-Konten können alle Ressourcen in Ihrem Abonnement verwalten. Der Hauptgrund für das Erstellen von Automation-Konten in unterschiedlichen Regionen ist der, dass Richtlinien eine Isolierung von Daten und Ressourcen innerhalb einer spezifischen Region vorsehen.

Alle Aufgaben, die Sie für Ressourcen mit dem Azure Resource Manager und den Azure-Cmdlets in Azure Automation durchführen, müssen gegenüber Azure mit Azure Active Directory basierend auf den Anmeldeinformationen für die Organisationsidentität authentifiziert werden.  Ursprünglich wurde die zertifikatbasierte Authentifizierungsmethode mit Azure (klassisch) verwendet, die Einrichtung war allerdings kompliziert.  Die Authentifizierung für Azure per Azure AD-Benutzer wurde 2014 nicht nur eingeführt, um den Konfigurationsprozess für ein Authentifizierungskonto zu vereinfachen. Es sollte auch die Möglichkeit der nicht interaktiven Authentifizierung für Azure mit einem einzelnen Benutzerkonto unterstützt werden, das sowohl für Azure Resource Manager als auch für klassische Ressourcen funktioniert.   

Beim Erstellen eines neuen Automation-Kontos im Azure-Portal wird automatisch Folgendes erstellt:

* Ein ausführendes Konto, das einen neuen Dienstprinzipal in Azure Active Directory und ein Zertifikat erstellt und die Rolle „Mitwirkender“ der rollenbasierten Zugriffssteuerung (RBAC) zuweist, die zum Verwalten von Resource Manager-Ressourcen mit Runbooks verwendet wird.
* Ein klassisches ausführendes Konto durch Hochladen eines Verwaltungszertifikats, das zum Verwalten klassischer Azure-Ressourcen mit Runbooks verwendet wird.  

Die rollenbasierte Zugriffssteuerung ist für Azure Resource Manager verfügbar, um einem Azure AD-Benutzerkonto und ausführenden Konto zulässige Aktionen zu gewähren und den Dienstprinzipal zu authentifizieren.  Weitere Informationen zur Entwicklung Ihres Modells zum Verwalten von Automation-Berechtigungen finden Sie im Artikel [Rollenbasierte Zugriffssteuerung in Azure Automation](automation-role-based-access-control.md) .  

Für Runbooks, die in Ihrem Rechenzentrum über einen Hybrid Runbook Worker oder für Computing-Dienste in AWS ausgeführt werden, kann nicht das gleiche Verfahren verwendet werden, das normalerweise für die Authentifizierung von Runbooks bei Azure-Ressourcen genutzt wird.  Der Grund: Diese Ressourcen werden außerhalb von Azure ausgeführt, weshalb für die Authentifizierung bei Ressourcen, auf die lokal zugegriffen wird, ihre in Automation definierten Sicherheitsanmeldeinformationen verwendet werden müssen.  

## <a name="authentication-methods"></a>Authentifizierungsmethoden
In der folgenden Tabelle sind die unterschiedlichen Authentifizierungsmethoden für die einzelnen Umgebungen zusammengefasst, die von Azure Automation unterstützt werden. Außerdem ist der Artikel angegeben, in dem beschrieben wird, wie Sie die Authentifizierung für Ihre Runbooks einrichten.

| Methode | Environment | Artikel |
| --- | --- | --- |
| Azure AD-Benutzerkonto |Azure Resource Manager und Azure klassisch |[Authentifizieren von Runbooks mit dem Azure AD-Benutzerkonto](automation-create-aduser-account.md) |
| Ausführendes Azure-Konto |Azure-Ressourcen-Manager |[Authentifizieren von Runbooks mit der Azure-Option „Ausführendes Konto“](automation-sec-configure-azure-runas-account.md) |
| Klassisches ausführendes Azure-Konto |Azure klassisch |[Authentifizieren von Runbooks mit der Azure-Option „Ausführendes Konto“](automation-sec-configure-azure-runas-account.md) |
| Windows-Authentifizierung |Lokales Rechenzentrum |[Authentifizieren von Runbooks für Hybrid-Runbook-Worker](automation-hybrid-runbook-worker.md) |
| AWS-Anmeldeinformationen |Amazon Web Services |[Authentifizieren von Runbooks mit Amazon Web Services (AWS)](automation-config-aws-account.md) |

