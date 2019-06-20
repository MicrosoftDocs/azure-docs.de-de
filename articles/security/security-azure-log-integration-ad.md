---
title: Azure-Protokollintegration mit Azure Active Directory-Überwachungsprotokollen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Azure Log Integration-Dienst installieren und Azure-Überwachungsprotokolle integrieren.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 6e99553afc05fca0250607bfccd2799adaf471e5
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298163"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrieren von Azure Active Directory-Überwachungsprotokollen

Azure AD-Überwachungsereignisse (Azure Active Directory) helfen Ihnen, privilegierte Aktionen zu bestimmen, die in Azure Active Directory aufgetreten sind. Sie können die Typen von Ereignissen anzeigen, die Sie nachverfolgen können, indem Sie [Azure Active Directory-Überwachungsberichtereignisse](../active-directory/reports-monitoring/concept-audit-logs.md) überprüfen.


>[!IMPORTANT]
> Das Feature Azure Log Integration gilt ab dem 15.06.2019 als veraltet. AzLog-Downloads wurden am 27. Juni 2018 deaktiviert. Um Unterstützung bei der künftigen Vorgehensweise zu erhalten, lesen Sie den Beitrag [Use Azure Monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Verwenden von Azure Monitor für die Integration mit SIEM-Tools). 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Schritte zum Integrieren von Azure Active Directory-Überwachungsprotokollen

> [!NOTE]
> Zur Vorbereitung auf die Ausführung der in diesem Artikel beschriebenen Schritte müssen zunächst die relevanten Schritte im Artikel [Erste Schritte](security-azure-log-integration-get-started.md) ausgeführt werden.

1. Öffnen Sie die Eingabeaufforderung, und führen Sie diesen Befehl aus:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Führen Sie den folgenden Befehl aus: 
 
   ``azlog createazureid``

   Dabei werden Sie zur Angabe Ihrer Azure-Anmeldeinformationen aufgefordert. Anschließend wird in den Azure AD-Mandanten, die die Azure-Abonnements hosten, in denen der angemeldete Benutzer als Administrator, Co-Administrator oder Besitzer fungiert, ein Azure Active Directory-Dienstprinzipal erstellt. Handelt es sich bei dem angemeldeten Benutzer nur um einen Gastbenutzer des Azure AD-Mandanten, tritt bei dem Befehl ein Fehler auf. Die Authentifizierung gegenüber Azure wird über Azure AD durchgeführt. Beim Erstellen eines Dienstprinzipals für die Azure-Protokollintegration wird die Azure AD-Identität erstellt, die Lesezugriff auf Azure-Abonnements erhält.

3. Führen Sie den folgenden Befehl aus, um Ihre Mandanten-ID bereitzustellen. Zum Ausführen des Befehls müssen Sie Mitglied der Mandanten-Administratorrolle sein.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Beispiel:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Überprüfen Sie die folgenden Ordner, um sicherzustellen, dass die JSON-Dateien des Azure Active Directory-Überwachungsprotokolls in ihnen erstellt wurden:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Das folgende Video veranschaulicht die in diesem Artikel beschriebenen Schritte:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Sie müssen sich an Ihren SIEM-Anbieter wenden, um spezifische Anweisungen zum Übertragen der Informationen in den JSON-Dateien in Ihr SIEM-System (Security Information and Event Management) zu erhalten.

Unterstützung durch die Community ist über das [MSDN-Forum für die Azure-Protokollintegration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration) verfügbar. In diesem Forum kann sich die Community der Azure-Protokollintegration gegenseitig mit Fragen, Antworten, Tipps und Tricks unterstützen. Darüber hinaus überwacht das Team für die Azure-Protokollintegration dieses Forum und bietet nach Möglichkeit entsprechende Hilfe an.

Sie können zudem eine [Supportanfrage](../azure-supportability/how-to-create-azure-support-request.md) öffnen. Wählen Sie **Protokollintegration** als Dienst aus, für den Sie Support anfordern.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Azure-Protokollintegration finden Sie auf den folgenden Webseiten:

* [Microsoft Azure Log Integration for Azure logs](https://www.microsoft.com/download/details.aspx?id=53324) (Microsoft Azure Log Integration für Azure-Protokolle, in englischer Sprache): Diese Seite im Download Center bietet Details, Systemanforderungen und Installationsanweisungen für Azure Log Integration.
* [Einführung in Azure Log Integration](security-azure-log-integration-overview.md): Dieser Artikel stellt Azure Log Integration, die wichtigsten Funktionen sowie die Funktionsweise vor.
* [Häufig gestellte Fragen zu Azure Log Integration:](security-azure-log-integration-faq.md) In diesem Artikel werden Fragen zur Azure-Protokollintegration beantwortet.
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Neue Features für die Azure-Diagnose und Azure-Überwachungsprotokolle): In diesem Blogbeitrag werden Azure-Überwachungsprotokolle und andere Features vorgestellt, mit denen Sie sich einen Einblick in die Vorgänge Ihrer Azure-Ressourcen verschaffen können.
