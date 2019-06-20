---
title: Konfigurieren der Benutzerbereitstellung für eine Azure AD-Kataloganwendung | Microsoft-Dokumentation
description: Schnelles Konfigurieren der umfassenden Benutzerkontobereitstellung und das Aufheben der Bereitstellung für Anwendungen, die bereits im Azure AD-Anwendungskatalog aufgeführt sind
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: be43f0e100bc96de2be916cbf52bca7d3ba51431
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784532"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Konfigurieren der Benutzerbereitstellung für eine Azure AD-Kataloganwendung

Die *Bereitstellung von Benutzerkonten* umfasst das Erstellen, Aktualisieren und/oder Deaktivieren der Benutzerkontodatensätze im lokalen Benutzerprofilspeicher einer Anwendung. Die meisten Cloud- und SaaS-Anwendungen speichern die Benutzerrolle und Berechtigungen im eigenen lokalen Benutzerprofilspeicher. Das Vorkommen eines solchen Benutzerdatensatzes im lokalen Speicher ist *erforderlich*, damit das einmalige Anmelden und der Zugriff funktionieren.

Die Registerkarte **Bereitstellung** im linken Navigationsbereich für eine Unternehmens-App im Azure-Portal zeigt an, welche Bereitstellungsmodi für diese App unterstützt werden. Dies kann einer von zwei Werten sein:

## <a name="configuring-an-application-for-manual-provisioning"></a>Konfigurieren einer Anwendung für die manuelle Bereitstellung

*Manuelle* Bereitstellung bedeutet, dass Benutzerkonten mithilfe der von dieser App bereitgestellten Methoden manuell erstellt werden müssen. Dies könnte das Anmelden bei einem Verwaltungsportal für diese App sowie das Hinzufügen von Benutzern über eine webbasierte Benutzeroberfläche bedeuten. Es könnte auch das Hochladen eines Arbeitsblatts mit Benutzerkontodetails bedeuten, bei dem ein von dieser Anwendung bereitgestellter Mechanismus verwendet wird. Die verfügbaren Mechanismen können Sie über die für die Anwendung bereitgestellte Dokumentation ermitteln, oder wenden Sie sich an den Entwickler der App.

Wenn für eine bestimmte Anwendung nur der Modus „Manuell“ angezeigt wird, bedeutet dies, dass für die App noch kein automatischer Azure AD-Bereitstellungsconnector erstellt wurde. Oder es bedeutet, dass die App die erforderliche Benutzerverwaltungs-API nicht unterstützt, über die ein automatisierter Bereitstellungsconnector erstellt wird.

Wenn Sie die Unterstützung für die automatische Bereitstellung für eine bestimmte App anfordern möchten, können Sie unter [Azure Active Directory Application Requests](https://aka.ms/aadapprequest) eine Anforderung ausfüllen.

## <a name="configuring-an-application-for-automatic-provisioning"></a>Konfigurieren einer Anwendung für die automatische Bereitstellung

*Automatisch* bedeutet, dass für diese Anwendung ein Azure AD-Bereitstellungsconnector entwickelt wurde. Weitere Informationen zum Azure AD-Bereitstellungsdienst und dessen Funktionsweise finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Weitere Informationen zum Bereitstellen einer Anwendung für bestimmte Benutzer und Gruppen finden Sie unter [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Die tatsächlichen Schritte, die zum Aktivieren und Konfigurieren der automatischen Bereitstellung erforderlich sind, variieren je nach Anwendung.

>[!NOTE]
>Sie sollten damit beginnen, das Setuptutorial zur Einrichtung der Bereitstellung für Ihre Anwendung zu finden und die Schritte zum Konfigurieren der App und von Azure AD befolgen, um die Bereitstellungsverbindung zu erstellen. 
>
>

App-Tutorials finden Sie unter [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Es ist beim Einrichten der Bereitstellung wichtig zu beachten, dass die Attributzuordnungen und Workflows überprüft und konfiguriert werden, die festlegen, welche Benutzereigenschaften (oder Gruppeneigenschaften) von Azure AD zur Anwendung gelangen. Dazu zählt das Festlegen der „übereinstimmenden Eigenschaft“, mit der Benutzer/Gruppen zwischen den beiden Systemen eindeutig identifiziert und abgeglichen werden. Weitere Informationen zu diesem wichtigen Vorgang.

## <a name="next-steps"></a>Nächste Schritte
[Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

