---
title: Steuern des externen Zugriffs auf Ressourcen in Azure Active Directory mit Vertraulichkeitsbezeichnungen.
description: Verwenden von Vertraulichkeitsbezeichnungen im Rahmen Ihres allgemeinen Sicherheitsplans für externen Zugriff.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac3141823eb72b17754a771cd63264fe851949d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365360"
---
# <a name="control-access-with-sensitivity-labels"></a>Steuern des Zugriffs mit Vertraulichkeitsbezeichnungen 

Mit [Vertraulichkeitsbezeichnungen](/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide) können Sie den Zugriff auf Ihre Inhalte in Office 365-Anwendungen und in Containern wie Microsoft Teams, Microsoft 365-Gruppen und SharePoint-Websites steuern. Dies ermöglicht den Schutz Ihrer Inhalte, ohne die Zusammenarbeit und Produktion Ihrer Benutzer zu behindern. Mit Vertraulichkeitsbezeichnungen können Sie die Inhalte Ihrer Organisation zwischen Geräten, Apps und Diensten versenden, während gleichzeitig für den Schutz Ihrer Daten und die Einhaltung Ihrer Konformitäts- und Sicherheitsrichtlinien gesorgt ist. 

Vertraulichkeitsbezeichnungen ermöglichen Ihnen Folgendes:

* **Klassifizieren von Inhalten ohne Hinzufügen von Schutzeinstellungen**: Sie können Inhalten eine Klassifizierung (z. B. einen Sticker) zuweisen, die erhalten bleibt, während die Inhalte genutzt und geteilt werden. Sie können diese Klassifizierung verwenden, um Nutzungsberichte zu generieren und Aktivitätsdaten für Ihre vertraulichen Inhalte anzuzeigen.

* **Erzwingen von Schutzeinstellungen, z. B. Verschlüsselung, Wasserzeichen und Zugriffseinschränkungen**: Benutzer können beispielsweise die Bezeichnung „Vertraulich“ auf ein Dokument oder eine E-Mail anwenden. Mit dieser Bezeichnung kann der [Inhalt verschlüsselt](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) und das Wasserzeichen „Vertraulich“ hinzugefügt werden. Darüber hinaus können Sie [eine Vertraulichkeitsbezeichnung auch auf einen Container anwenden](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide), z. B. eine SharePoint-Website, und festlegen, ob externe Benutzer auf die Inhalte zugreifen können.

Vertraulichkeitsbezeichnungen von E-Mails und anderen Inhalten bleiben fest zugeordnet. Mit Vertraulichkeitsbezeichnungen von Containern kann der Zugriff auf den Container eingeschränkt werden, aber die Bezeichnung wird nicht an die Inhalte des Containers vererbt. Beispielsweise kann ein Benutzer Inhalte von einer geschützten Website herunterladen und dann ohne Einschränkungen freigeben. Dies ist aber nicht möglich, wenn die Inhalte auch über eine Vertraulichkeitsbezeichnung verfügen.

 >[!NOTE]
>Zum Anwenden von Vertraulichkeitsbezeichnungen müssen Benutzer bei ihrem Geschäfts-, Schul- oder Unikonto von Microsoft angemeldet sein. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Erforderliche Berechtigungen zum Erstellen und Verwalten von Vertraulichkeitsstufen

Mitglieder Ihres Konformitätsteams benötigen Berechtigungen für Microsoft 365 Compliance Center, Microsoft 365 Security Center oder das Security & Compliance Center, wenn sie Vertraulichkeitsbezeichnungen erstellen sollen.

Standardmäßig haben globale Administratoren für Ihren Mandanten Zugriff auf diese Admin Centers und können den für die Konformität zuständigen Mitarbeitern und anderen Personen Zugriff gewähren, ohne ihnen alle Berechtigungen eines Mandantenadministrators zu erteilen. Fügen Sie die Benutzer den Rollengruppen „Compliancedatenadministrator“, „Complianceadministrator“ oder „Sicherheitsadministrator“ hinzu.

 

## <a name="determine-your-sensitivity-label-strategy"></a>Festlegen der Strategie für Vertraulichkeitsbezeichnungen

Ermitteln Sie in Bezug auf die Steuerung des externen Zugriffs auf Ihre Inhalte Folgendes:

**Für alle Inhalte und Container**

* Wie soll definiert werden, was als hohe (HBI), mittlere (MBI) oder geringe (LBI) geschäftliche Auswirkung deklariert wird? Erwägen Sie, welche Auswirkungen sich für Ihre Organisation ergeben, falls bestimmte Inhalte auf unerwünschte Weise freigegeben werden.

   * Inhalte mit bestimmten Arten von inhärent [vertraulichen Daten](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide), z. B. Kreditkarten oder Reisepassnummern

   * Inhalte, die von bestimmten Gruppen oder Personen erstellt werden (z. B. Mitarbeitern aus dem Konformitäts- oder Finanzbereich oder Führungskräften)

   * Inhalte bestimmter Bibliotheken oder Websites (Beispiel: Container, in denen Strategiedaten der Organisation oder private Finanzdaten gehostet werden)

   * Andere Kriterien

* Welche Kategorien von Inhalten (z. B. Inhalte mit hohen geschäftlichen Auswirkungen) sollten in Bezug auf den Zugriff durch externe Benutzer eingeschränkt werden?

   * Einschränkungen können Aktionen wie das Begrenzen des Zugriffs auf Container und das Verschlüsseln von Inhalten umfassen.

* Welche Standardeinstellungen sollten für Daten, Websites oder Microsoft 365-Gruppen gelten, die mit hohen Auswirkungen verbunden sind?

* Wo sollen Vertraulichkeitsbezeichnungen verwendet werden, um Inhalte zu [bezeichnen und zu überwachen](/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide), und wo sollen sie zum [Erzwingen der Verschlüsselung](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) oder [Erzwingen von Zugriffseinschränkungen für Container](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide) genutzt werden?

**Für E-Mails und Inhalte**

* Sollen [Vertraulichkeitsbezeichnungen automatisch auf Inhalte angewendet werden](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide), oder soll dies manuell durchgeführt werden?

   * Bei Verwendung des manuellen Ansatzes: Soll [Benutzern das Anwenden einer Bezeichnung empfohlen werden](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)?

**Für Container**

* Anhand welcher Kriterien soll bestimmt werden, ob für M365-Gruppen, Teams oder SharePoint-Websites der Zugriff mit Vertraulichkeitsbezeichnungen eingeschränkt werden soll?

* Sollen die Inhalte dieser Container nur ab dem entsprechenden Startzeitpunkt bezeichnet werden, oder sollen auch vorhandene Dateien unter SharePoint und OneDrive [automatisch bezeichnet](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) werden?

Weitere Ideen zur Nutzung von Vertraulichkeitsbezeichnungen finden Sie auf der Seite mit den [häufigen Szenarien für Vertraulichkeitsbezeichnungen](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide).

### <a name="sensitivity-labels-on-email-and-content"></a>Vertraulichkeitsbezeichnungen für E-Mails und Inhalte

Wenn Sie einem Dokument oder einer E-Mail eine Vertraulichkeitsbezeichnung zuweisen, entspricht dies einem anpassbaren, dauerhaften Klartextstempel, der auf die Inhalte angewendet wird. 

* **Anpassbar** bedeutet hierbei, dass Sie für Ihre Organisation geeignete Bezeichnungen erstellen und ermitteln können, was bei deren Anwendung passiert.

* **Klartext** bedeutet, dass die Bezeichnung Teil der Metadaten des Elements ist und von Anwendungen und Diensten gelesen werden kann, damit diese ihre eigenen Schutzmaßnahmen anwenden können.

* **Dauerhaft** bedeutet, dass die Bezeichnung und alle zugeordneten Schutzmaßnahmen beim Inhalt verbleiben und als Grundlage für das Anwenden und Erzwingen von Richtlinien dienen.

 

> [!NOTE]
> Jedem Inhaltselement kann jeweils eine Vertraulichkeitsbezeichnung zugeordnet werden.


### <a name="sensitivity-labels-on-containers"></a>Vertraulichkeitsbezeichnungen in Containern

Sie können Vertraulichkeitsbezeichnungen auf Container anwenden, z. B. [Microsoft 365-Gruppen](../enterprise-users/groups-assign-sensitivity-labels.md), [Microsoft Teams](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide) und [SharePoint-Websites](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide). Wenn Sie diese Vertraulichkeitsbezeichnung auf einen unterstützten Container anwenden, werden die Klassifizierungs- und Schutzeinstellungen von der Bezeichnung automatisch auf die verbundene Website oder Gruppe angewendet. Mit Vertraulichkeitsbezeichnungen können in diesen Containern die folgenden Aspekte gesteuert werden:

* **Datenschutz:** Sie können angeben, von wem die Website angezeigt werden kann: bestimmten Benutzern, allen internen Benutzern oder allen Benutzer.

* **Zugriff durch externe Benutzer:** Hiermit wird gesteuert, ob der Gruppenbesitzer die Möglichkeit hat, der Gruppe Gäste hinzuzufügen.

* **Zugriff von nicht verwalteten Geräten:** Hiermit wird angegeben, ob und wie nicht verwaltete Geräte auf Inhalte zugreifen können.

 

![Screenshot: Bearbeiten von Vertraulichkeitsbezeichnungen](media/secure-external-access/8-edit-label.png)

 

Wenn Sie eine Vertraulichkeitsbezeichnung auf einen Container, z. B. eine SharePoint-Website, anwenden, gilt die Anwendung nicht für dessen Inhalte. Mit Vertraulichkeitsbezeichnungen für Container wird der Zugriff auf die Inhalte des Containers gesteuert. 

* Falls Sie Bezeichnungen automatisch auf die Inhalte des Containers anwenden möchten, helfen Ihnen die Informationen unter [Automatisches Anwenden einer Vertraulichkeitsbezeichnung auf Inhalte](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) weiter.

* Wenn Sie möchten, dass Benutzer die Möglichkeit zum manuellen Anwenden von Bezeichnungen auf diese Inhalte haben sollen, sollten Sie sich vergewissern, dass Sie [Vertraulichkeitsbezeichnungen für Office-Dateien in SharePoint und OneDrive aktiviert haben](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide).

### <a name="plan-to-implement-sensitivity-labels"></a>Planen der Implementierung von Vertraulichkeitsbezeichnungen

Nachdem Sie ermittelt haben, wie Sie Vertraulichkeitsbezeichnungen einsetzen möchten und auf welche Inhalte und Websites diese angewendet werden sollen, helfen Ihnen die Artikel zu den folgenden Themen bei der Implementierung weiter.

1. [Erste Schritte mit Vertraulichkeitsbezeichnungen](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

2. [Erstellen einer Bereitstellungsstrategie](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

3. [Erstellen und Konfigurieren von Vertraulichkeitsbezeichnungen und deren Richtlinien](/microsoft-365/compliance/create-sensitivity-labels?view=o365-worldwide)

4. [Einschränken des Zugriffs auf Inhalte mithilfe von Vertraulichkeitsbezeichnungen zur Verschlüsselung](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide)

5. [Verwenden von Vertraulichkeitsbezeichnungen zum Schützen von Inhalten in Microsoft Teams, Microsoft 365-Gruppen und SharePoint-Websites](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)

6. [Aktivieren von Vertraulichkeitsbezeichnungen für Office-Dateien in SharePoint und OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)

### <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie Informationen zum Schützen des externen Zugriffs auf Ressourcen. Wir empfehlen Ihnen, die Aktionen in der angegebenen Reihenfolge durchzuführen.

1. [Ermitteln des gewünschten Sicherheitsstatus für externen Zugriff](1-secure-access-posture.md)

2. [Ermitteln des aktuellen Status](2-secure-access-current-state.md)

3. [Erstellen eines Governanceplans](3-secure-access-plan.md)

4. [Verwenden von Gruppen für die Sicherheit](4-secure-access-groups.md)

5. [Durchführen der Umstellung auf Azure AD B2B](5-secure-access-b2b.md)

6. [Schützen des Zugriffs mit der Berechtigungsverwaltung](6-secure-access-entitlement-managment.md)

7. [Schützen des Zugriffs mit Richtlinien für bedingten Zugriff](7-secure-access-conditional-access.md)

8. [Schützen des Zugriffs mit Vertraulichkeitsbezeichnungen](8-secure-access-sensitivity-labels.md) (dieser Artikel)

9. [Schützen des Zugriffs auf Microsoft Teams, OneDrive und SharePoint](9-secure-access-teams-sharepoint.md)