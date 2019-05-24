---
title: Bekannte Probleme und Lösungen bei der Einhaltung des SCIM 2.0-Protokolls des Azure AD-Benutzerbereitstellungsdiensts | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie häufige Kompatibilitätsprobleme mit dem Protokoll beim Hinzufügen einer Anwendung, die nicht aus dem Katalog stammt und die SCIM 2.0 unterstützt, in Azure AD beheben.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9a0e595d2120d3cdccd42c502a83de9d5ed3ff4
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963180"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Bekannte Probleme und Lösungen bei der Einhaltung des SCIM 2.0-Protokolls des Azure AD-Benutzerbereitstellungsdiensts

Über Azure Active Directory (Azure AD) können Benutzer und Gruppen automatisch für alle Anwendungen oder Systeme bereitgestellt werden, denen ein Webdienst mit der in der [SCIM 2.0-Protokollspezifikation](https://tools.ietf.org/html/draft-ietf-scim-api-19) definierten Schnittstelle vorgelagert ist. 

Die Azure AD-Unterstützung für das SCIM 2.0-Protokoll wird unter [Automatisches Bereitstellen von Benutzern und Gruppen aus Azure Active Directory für Anwendungen mit SCIM (System for Cross-domain Identity Management)](use-scim-to-provision-users-and-groups.md) beschrieben. Dort werden auch die einzelnen Teile des Protokolls aufgeführt, die implementiert werden, um Benutzer und Gruppen aus Azure AD für Anwendungen, die SCIM 2.0 unterstützen, automatisch bereitzustellen.

In diesem Artikel werden aktuelle und ehemalige Probleme mit dem Bereitstellungsdienst für Azure AD-Benutzer im Zusammenhang mit der Einhaltung des SCIM 2.0-Protokolls und Möglichkeiten zur Umgehung dieser Probleme beschrieben.

> [!IMPORTANT]
> Das neueste Update des SCIM-Clients des Bereitstellungsdiensts für Azure AD-Benutzer wurde am 18. Dezember 2018 bereitgestellt. Dieses Update behebt die in der folgenden Tabelle aufgeführten bekannten Kompatibilitätsprobleme. In den häufig gestellten Fragen unten finden Sie weitere Informationen zu diesem Update.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0-Konformitätsprobleme und Status

| **SCIM 2.0-Konformitätsproblem** |  **Korrigiert?** | **Korrekturdatum**  |  
|---|---|---|
| Azure AD erfordert „/scim“ im Stamm der SCIM-Endpunkt-URL der Anwendung  | Ja  |  18. Dezember 2018 | 
| Erweiterungsattribute verwenden vor Attributnamen die Punktnotation „.“ anstelle der Doppelpunktnotation „:“. |  Ja  | 18. Dezember 2018  | 
|  Patchanforderungen für mehrwertige Attribute enthalten eine ungültige Syntax für Pfadfilter | Ja  |  18. Dezember 2018  | 
|  Anforderungen zur Erstellung von Gruppen enthalten einen ungültigen Schema-URI | Ja  |  18. Dezember 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Wurden die beschriebenen Dienstkorrekturen automatisch auf meine vorhandene SCIM-App angewandt?

 Nein. Da dies einen Breaking Change für SCIM-Apps, die mit dem älteren Verhalten programmiert wurden, bedeutet hätte, wurden die Änderungen nicht automatisch auf vorhandene Apps angewandt.

Die Änderungen gelten für alle neuen [SCIM-Apps, die nicht im Katalog enthalten sind](configure-single-sign-on-non-gallery-applications.md) und nach dem Datum der Korrektur im Azure-Portal konfiguriert werden.

Informationen zum Migrieren vorhandener Aufträge zur Benutzerbereitstellung zu den neuesten Fehlerbehebungen finden Sie im nächsten Abschnitt.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Kann ich einen vorhandenen Auftrag zur SCIM-basierten Benutzerbereitstellung migrieren, um die neuesten Fehlerbehebungen einzufügen?

Ja. Wenn Sie diese Anwendungsinstanz bereits für einmaliges Anmelden verwenden und den vorhandenen Bereitstellungsauftrag migrieren möchten, um die neuesten Fehlerbehebungen hinzuzufügen, gehen Sie wie im folgenden Verfahren beschrieben vor. Dieses Verfahren beschreibt die Verwendung von Microsoft Graph-API und Microsoft Graph-API-Explorer zum Entfernen Ihres alten Bereitstellungsauftrags aus Ihrer vorhandenen SCIM-App und zum Erstellen einer neuen mit dem neuen Verhalten.

> [!NOTE]
> Wenn sich die Anwendung noch in der Entwicklung befindet und bisher nicht für einmaliges Anmelden oder die Benutzerbereitstellung bereitgestellt wurde, ist es am einfachsten, den Anwendungseintrag im Azure-Portal im Abschnitt **Azure Active Directory > Unternehmensanwendungen** zu löschen und mit der Option **Anwendung erstellen > Nicht-Katalog** einfach einen neuen Eintrag für die Anwendung hinzuzufügen. Dies stellt eine Alternative zum Ausführen der folgenden Prozedur dar.
 
1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
2. Navigieren Sie im Azure-Portal im Abschnitt **Azure Active Directory > Unternehmensanwendungen** zu Ihrer SCIM-Anwendung, und wählen Sie sie aus.
3. Kopieren Sie im Abschnitt **Eigenschaften** Ihrer SCIM-App die **Objekt-ID**.
4. Wechseln Sie in einem neuen Webbrowserfenster zu https://developer.microsoft.com/graph/graph-explorer, und melden Sie sich als Administrator für den Azure AD-Mandanten an, unter dem Ihre App hinzugefügt wurde.
5. Führen Sie im Graph-Tester den unten stehenden Befehl aus, um die ID Ihres Bereitstellungsauftrags zu suchen. Ersetzen Sie „[object-id]“ durch die Dienstprinzipal-ID (Objekt-ID), die Sie im dritten Schritt kopiert haben.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Abrufen von Aufträgen](./media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Abrufen von Aufträgen") 


6. Kopieren Sie in den Ergebnissen die vollständige „ID“-Zeichenfolge, die mit „customappsso“ oder mit „scim“ beginnt.
7. Führen Sie den folgenden Befehl aus, um die Konfiguration für die Attributzuordnung abzurufen, damit Sie eine Sicherung erstellen können. Verwenden Sie dieselbe [object-id] wie zuvor, und ersetzen Sie die [job-id] durch die ID des Bereitstellungsauftrags, die Sie im letzten Schritt kopiert haben.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Abrufen des Schemas](./media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Abrufen des Schemas") 

8. Kopieren Sie die JSON-Ausgabe aus dem letzten Schritt, und speichern Sie sie in einer Textdatei. Diese Datei enthält alle benutzerdefinierten Attributzuordnungen, die Sie in Ihrer alten App hinzugefügt haben, und sollte einige Tausend Zeilen JSON umfassen.
9. Führen Sie den folgenden Befehl aus, um den Bereitstellungsauftrag zu löschen:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Führen Sie den folgenden Befehl aus, um einen neuen Bereitstellungsauftrag zu erstellen, der über die neuesten Fehlerbehebungen für den Dienst verfügt.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Kopieren Sie in den Ergebnissen des letzten Schritts die vollständige „ID“-Zeichenfolge, die mit „scim“ beginnt. Sie können optional auch Ihre alten Attributzuordnungen erneut anwenden. Führen Sie dazu den Befehl unten aus, und ersetzen Sie „[new-job-id]“ durch die neue Auftrags-ID, die Sie gerade kopiert haben. Geben Sie außerdem die JSON-Ausgabe aus Schritt 7 als Anforderungstext ein.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Wechseln Sie wieder zurück zum ersten Webbrowserfenster, und wählen Sie die Registerkarte **Bereitstellung** für Ihre Anwendung aus.
13. Überprüfen Sie Ihre Konfiguration, und starten Sie anschließend den Bereitstellungsauftrag. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Kann ich eine neue, nicht im Katalog enthaltene App hinzufügen, die das alte Bereitstellungsverhalten nutzt?

Ja. Wenn Sie eine Anwendung mit dem alten Verhalten vor den Fehlerkorrekturen programmiert haben und von dieser eine neue Instanz bereitstellen möchten, führen Sie das folgende Verfahren aus. Dieses Verfahren beschreibt die Verwendung von Microsoft Graph-API und Microsoft Graph-API-Explorer zum Erstellen eines SCIM-Bereitstellungsauftrags mit dem alten Verhalten.
 
1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
2. Erstellen Sie im Azure-Portal im Abschnitt **Azure Active Directory > Unternehmensanwendungen > Anwendung erstellen** eine neue Anwendung mit der Option **Non-gallery** (Nicht aus dem Katalog).
3. Kopieren Sie im Abschnitt **Eigenschaften** der neuen benutzerdefinierten App die **Objekt-ID**.
4. Wechseln Sie in einem neuen Webbrowserfenster zu https://developer.microsoft.com/graph/graph-explorer, und melden Sie sich als Administrator für den Azure AD-Mandanten an, unter dem Ihre App hinzugefügt wurde.
5. Führen Sie im Graph-Tester den folgenden Befehl aus, um die Konfiguration der Bereitstellung für Ihre App zu initialisieren.
   Ersetzen Sie „[object-id]“ durch die Dienstprinzipal-ID (Objekt-ID), die Sie im dritten Schritt kopiert haben.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Wechseln Sie wieder zurück zum ersten Webbrowserfenster, und wählen Sie die Registerkarte **Bereitstellung** für Ihre Anwendung aus.
7. Schließen Sie die Konfiguration der Benutzerbereitstellung wie gewohnt ab.


## <a name="next-steps"></a>Nächste Schritte
[Erfahren Sie mehr über die Bereitstellung und Bereitstellungsaufhebung für SaaS-Anwendungen.](user-provisioning.md)

