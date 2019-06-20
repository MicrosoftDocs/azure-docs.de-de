---
title: Erste Schritte mit der Azure AD-Berichterstellungs-API | Microsoft-Dokumentation
description: Vorgehensweise zum Einstieg in die Azure Active Directory-Berichterstellungs-API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ff3e530dae3a6db4b7c84292a25e83c11000baf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60286756"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Erste Schritte mit der Berichterstellungs-API von Azure Active Directory

Unter Azure Active Directory können Sie viele verschiedene [Berichte](overview-reports.md) nutzen, die nützliche Informationen für Anwendungen enthalten, z.B. SIEM-Systeme, Überwachungsberichte und Business Intelligence-Tools. 

Mit der Microsoft Graph-API für Azure AD-Berichte können Sie über eine Gruppe von REST-basierten APIs programmgesteuerten Zugriff auf die Daten erhalten. Sie können diese APIs über verschiedene Programmiersprachen und Tools aufrufen.

Dieser Artikel enthält eine Übersicht über die Berichterstellungs-API und beispielsweise auch Informationen zu den Zugriffsmöglichkeiten.

Wenn Probleme auftreten sollten, lesen Sie [Gewusst wie: Beziehen von Support für Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um – mit oder ohne Benutzereingriff – auf die Berichterstellungs-API zuzugreifen:

1. Zuweisen von Rollen (Sicherheitsleseberechtigter, Sicherheitsadministrator, globaler Administrator)
2. Registrieren einer Anwendung
3. Erteilen von Berechtigungen
4. Erfassen von Konfigurationseinstellungen

Ausführliche Anweisungen finden Sie in den [Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>API-Endpunkte 

Der Microsoft Graph-API-Endpunkt für Überwachungsprotokolle ist `https://graph.microsoft.com/beta/auditLogs/directoryAudits`, und für Anmeldungen lautet er `https://graph.microsoft.com/beta/auditLogs/signIns`. Weitere Informationen finden Sie in der [Referenz zur Überwachungs-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) und in der [Referenz zur Anmelde-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Darüber hinaus können Sie die [API für Identity Protection-Risikoereignisse](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) verwenden, um programmgesteuerten Zugriff auf Sicherheitserkennungen über Microsoft Graph zu erhalten. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Active Directory Identity Protection und Microsoft Graph](../identity-protection/graph-get-started.md). 

> [!NOTE]
>  Der Endpunkt **https:\/\/graph.windows.net\/\<Mandantenname\>\/reports\/** ist veraltet. Verwenden Sie die oben beschriebenen neuen API-Endpunkte für den programmgesteuerten Zugriff auf die Aktivitäts- und Sicherheitsberichte.
  
## <a name="apis-with-graph-explorer"></a>APIs mit Graph-Tester

Sie können den [MSGraph-Tester](https://developer.microsoft.com/graph/graph-explorer) verwenden, um Ihre Anmeldung und API-Daten zu überprüfen. Achten Sie darauf, bei der Anmeldung bei Ihrem Konto beide Anmeldeschaltflächen auf der Benutzeroberfläche des Graph-Testers zu verwenden und die Berechtigungen **AuditLog.Read.All** und **Directory.Read.All** wie dargestellt festzulegen.   

![Graph-Tester](./media/concept-reporting-api/graph-explorer.png)

![Benutzeroberfläche zum Ändern von Berechtigungen](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Verwenden von Zertifikaten für den Zugriff auf die Azure AD-Berichterstellungs-API 

Verwenden Sie die Azure AD-Berichterstellungs-API mit Zertifikaten, wenn Sie die Daten für die Berichterstellung ohne Benutzeraktion abrufen möchten.

Ausführliche Anweisungen finden Sie unter [Abrufen von Daten per Azure AD-Berichterstellungs-API mit Zertifikaten](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Nächste Schritte

 * [Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API](howto-configure-prerequisites-for-reporting-api.md) 
 * [Abrufen von Daten per Azure AD Reporting-API mit Zertifikaten](tutorial-access-api-with-certificates.md)
 * [Beheben von Fehlern in der Azure AD-Berichterstellungs-API](troubleshoot-graph-api.md)


