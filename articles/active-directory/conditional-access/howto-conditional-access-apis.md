---
title: APIs für bedingten Zugriff und PowerShell – Azure Active Directory
description: Verwalten von Richtlinien (z. B. Code) mit den Azure AD-APIs für bedingten Zugriff und mithilfe von PowerShell
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4dd6e64053af41d66e3accbb7fc9b21d951fc6
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860212"
---
# <a name="conditional-access-programmatic-access"></a>Bedingter Zugriff: Programmgesteuerter Zugriff

Viele Unternehmen haben die Notwendigkeit geäußert, so viel wie möglich von ihren Umgebungen (z. B. Code) verwalten zu können. Mit Microsoft Graph können Sie Richtlinien für bedingten Zugriff wie jedes andere Codeelement in Ihrer Umgebung behandeln.

Microsoft Graph stellt ein einheitliches Programmierbarkeitsmodell bereit, mit dem Unternehmen mit den Daten in Microsoft 365, Windows 10 und Enterprise Mobility + Security arbeiten können. Weitere Informationen zu Microsoft Graph finden Sie im Artikel [Übersicht über Microsoft Graph](/graph/overview).

![Die Abbildung zeigt die primären Ressourcen und Beziehungen, die Bestandteil von Microsoft Graph sind](./media/howto-conditional-access-apis/microsoft-graph.png)

Die folgenden Beispiele werden unverändert und ohne Unterstützung bereitgestellt. Sie können diese Beispiele als Grundlage für die Toolbereitstellung in Ihrer Organisation verwenden. 

In vielen der folgenden Beispiele werden Tools wie [Verwaltete Identitäten](../managed-identities-azure-resources/overview.md), [Logic Apps](../../logic-apps/logic-apps-overview.md), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/) und [Azure Key Vault](../../key-vault/general/overview.md) verwendet.

## <a name="configure"></a>Konfigurieren

### <a name="powershell"></a>PowerShell

Vielen Administratoren ist PowerShell bereits als Tool zur Skripterstellung vertraut. Im folgenden Beispiel wird gezeigt, wie Sie das [Azure AD PowerShell-Modul](https://www.powershellgallery.com/packages/AzureAD) zum Verwalten von Richtlinien für bedingten Zugriff verwenden.

- [Konfigurieren von Richtlinien für bedingten Zugriff mit Azure AD PowerShell-Befehlen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>Graph-API

Dieses Beispiel zeigt die grundlegenden Optionen zum Erstellen, Lesen, Aktualisieren und Löschen, die in den Microsoft Graph-APIs für bedingten Zugriff zur Verfügung stehen. Das Beispiel enthält auch einige JSON-Vorlagen, mit denen Sie einige Beispielrichtlinien erstellen können.

- [Konfigurieren von Richtlinien für bedingten Zugriff mit Microsoft Graph-API-Aufrufen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Konfigurieren mithilfe von Vorlagen

Verwenden Sie die APIs für bedingten Zugriff, um in ihrer Vorproduktionsumgebung mithilfe einer Vorlage Richtlinien für bedingten Zugriff bereitzustellen.

- [Konfigurieren von Richtlinien für bedingten Zugriff mit Microsoft Graph-API-Vorlagen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Test

In diesem Beispiel werden sicherere Bereitstellungsmethoden mit Genehmigungsworkflows modelliert, mit denen Sie Richtlinien für bedingten Zugriff von einer Umgebung (z. B. Vorproduktionsumgebung) in eine andere Umgebung (z. B. Produktionsumgebung) kopieren können.

- [Hochstufen von Richtlinien für bedingten Zugriff aus Testumgebungen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Bereitstellen

In diesem Beispiel wird ein Mechanismus für die schrittweise und gestaffelte Bereitstellung von Richtlinien für bedingten Zugriff für die Benutzerschaft bereitgestellt, damit Sie frühzeitig Auswirkungen auf den Support verwalten und Probleme erkennen können.

- [Bereitstellen von Richtlinien für bedingten Zugriff in Produktionsumgebungen mit Genehmigungsworkflows](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Überwachen

In diesem Beispiel wird einen Mechanismus zum Überwachen von Änderungen an Richtlinien für bedingten Zugriff im Zeitverlauf bereitgestellt, der bei Änderungen von wichtigen Richtlinien Warnungen auslösen kann.

- [Überwachen bereitgestellter Richtlinien für bedingten Zugriff auf Änderungen und Auslösen von Warnungen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Verwalten

### <a name="backup-and-restore"></a>Sichern und Wiederherstellen

Automatisieren Sie mithilfe dieses Beispiels das Sichern und Wiederherstellen von Richtlinien für bedingten Zugriff mit Genehmigungen in Teams.

- [Verwalten des Sicherungs- und Wiederherstellungsprozesses von Richtlinien für bedingten Zugriff mithilfe von Microsoft Graph-API-Aufrufen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Konten für den Notfallzugriff

Mehrere Administratoren können Richtlinien für bedingten Zugriff erstellen und vergessen dabei möglicherweise, ihre [Konten für den Notfallzugriff](../roles/security-emergency-access.md) diesen Richtlinien als Ausschluss hinzuzufügen. In diesem Beispiel wird sichergestellt, dass alle Richtlinien so aktualisiert werden, dass sie die festgelegten Konten für den Notfallzugriff enthalten.

- [Verwalten der Zuweisung von Konten für den Notfallzugriff zu Richtlinien für bedingten Zugriff mithilfe von Microsoft Graph-API-Aufrufen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Notfallplanung

Dinge entwickeln sich nicht immer so, wie Sie das möchten. In diesem Fall brauchen Sie eine Möglichkeit, zu einem Zustand zurückzukehren, in dem die Arbeit fortgesetzt werden kann. Im folgenden Beispiel erfahren Sie, wie Sie Ihre Richtlinien auf einen als fehlerfrei bekannten Notfallplan zurücksetzen und andere Richtlinien für bedingten Zugriff deaktivieren können.

- [Verwalten der Aktivierung von Notfallplanrichtlinien für bedingten Zugriff mithilfe von Microsoft Graph-API-Aufrufen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Communitybeiträge

Diese Beispiele finden Sie in unserem [GitHub-Repository](https://github.com/Azure-Samples/azure-ad-conditional-access-apis). Wir freuen uns, Support für Communitybeiträge auf GitHub unter „Issues“ und „Pull Requests“ bereitstellen zu können.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Microsoft Graph](/graph/overview)

- [API für bedingten Zugriff](/graph/api/resources/conditionalaccesspolicy)

- [API für benannten Standort](/graph/api/resources/namedlocation)
