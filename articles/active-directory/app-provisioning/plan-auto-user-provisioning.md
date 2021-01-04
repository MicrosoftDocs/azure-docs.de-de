---
title: Planen einer automatischen Benutzerbereitstellung für Azure Active Directory
description: Leitfaden für die Planung und Ausführung einer automatischen Benutzerbereitstellung
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: e741bd6c87ea32b0d92b1dd53112a1bed3e4cd38
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174916"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planen einer automatischen Benutzerbereitstellung

Viele Organisationen nutzen Software-as-a-Service-Anwendungen (SaaS-Anwendungen) wie ServiceNow, Zscaler und Slack, um die Endbenutzerproduktivität zu steigern. In der Vergangenheit haben IT-Mitarbeiter auf manuelle Bereitstellungsmethoden wie das Hochladen von CSV-Dateien oder das Verwenden von benutzerdefinierten Skripts zur sicheren Verwaltung von Benutzeridentitäten in jeder SaaS-Anwendung gesetzt. Diese Prozesse sind jedoch fehleranfällig, unsicher und schwer zu verwalten.

Die automatische Benutzerbereitstellung von Azure Active Directory (Azure AD) vereinfacht diesen Prozess durch die sichere Automatisierung der Erstellung, Verwaltung und Entfernung von Benutzeridentitäten in SaaS-Anwendungen auf der Grundlage von Geschäftsregeln. Durch diese Automatisierung können Sie Ihre Identitätsverwaltungssysteme sowohl in reinen Cloudumgebungen als auch in Hybridumgebungen effektiv skalieren, wenn Sie die jeweilige Abhängigkeit von cloudbasierten Lösungen erweitern.

Lesen Sie [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md), um die Funktionalität besser zu verstehen.

## <a name="learn"></a>Lernen

Bei der Benutzerbereitstellung wird die Grundlage für eine fortlaufende Identitätsgovernance geschaffen und die Qualität von Geschäftsprozessen verbessert, die auf autoritativen Identitätsdaten basieren.

### <a name="key-benefits"></a>Hauptvorteile

Die Aktivierung der automatischen Benutzerbereitstellung bietet die folgenden Hauptvorteile:

* **Gesteigerte Produktivität:** Sie können Benutzeridentitäten in SaaS-Anwendungen mit einer einzigen Benutzeroberfläche für die Benutzerbereitstellungsverwaltung verwalten. Diese Oberfläche verfügt über einen einzelnen Satz von Bereitstellungsrichtlinien.

* **Risikomanagement:** Sie können die Sicherheit erhöhen, indem Sie Änderungen basierend auf dem Mitarbeiterstatus oder den Gruppenmitgliedschaften automatisieren, die Rollen und/oder den Zugriff definieren.

* **Erfüllung von Compliance und Governance**: Azure AD unterstützt systemeigene Überwachungsprotokolle für jede Benutzerbereitstellungsanforderung. Die Anforderungen werden sowohl im Quell- als auch im Zielsystem ausgeführt. Dadurch können Sie über einen einzigen Bildschirm nachverfolgen, wer Zugriff auf Anwendungen hat.

* **Kostensenkung:** Die automatische Benutzerbereitstellung reduziert Kosten, indem Ineffizienzen und menschliche Fehler vermieden werden, die bei einer manuellen Bereitstellung entstehen. Außerdem wird der Bedarf an benutzerdefinierten Benutzerbereitstellungslösungen, Skripts und Überwachungsprotokollen verringert.

### <a name="licensing"></a>Lizenzierung

Azure AD bietet die Self-Service-Integration einer beliebigen Anwendung mithilfe von Vorlagen im Anwendungskatalogmenü. Eine vollständige Liste der Lizenzanforderungen finden Sie auf der Seite [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Anwendungslizenzierung

Sie benötigen die entsprechenden Lizenzen für die Anwendung(en), die Sie automatisch bereitstellen möchten. Klären Sie mit den Anwendungsbesitzern, ob die der Anwendung zugewiesenen Benutzer über die entsprechenden Lizenzen für ihre Anwendungsrollen verfügen. Wenn Azure AD die automatische Bereitstellung auf der Grundlage von Rollen verwaltet, müssen die in Azure AD zugewiesenen Rollen den Anwendungslizenzen entsprechen. Falsche Lizenzen, die sich im Besitz der Anwendung befinden, können zu Fehlern bei der Bereitstellung/Aktualisierung eines Benutzers führen.

### <a name="terms"></a>Begriffe

In diesem Artikel werden die folgenden Begriffe verwendet:

* CRUD-Vorgänge – Aktionen, die für Benutzerkonten ausgeführt werden: Erstellen, Lesen, Aktualisieren, Löschen.

* Einmaliges Anmelden (Single Sign-On, SSO) – Die Möglichkeit eines Benutzers, sich einmal anzumelden und auf alle SSO-fähigen Anwendungen zuzugreifen. Im Kontext der Benutzerbereitstellung ist das einmalige Anmelden das Ergebnis von Benutzern, die über ein einzelnes Konto für den Zugriff auf alle Systeme verfügen, die die automatische Benutzerbereitstellung verwenden.

* Quellsystem – Das Repository von Benutzern, aus dem die Azure AD-Instanz bereitstellt. Azure AD ist das Quellsystem für die meisten vorintegrierten Bereitstellungsconnectors. Es gibt jedoch einige Ausnahmen für Cloudanwendungen wie SAP, Workday und AWS. Informationen hierzu finden Sie beispielsweise unter [Benutzerbereitstellung aus Workday in AD](../saas-apps/workday-inbound-tutorial.md).

* Zielsystem – Das Repository von Benutzern, in das die Azure AD-Instanz bereitstellt. Das Zielsystem ist in der Regel eine SaaS-Anwendung wie ServiceNow, Zscaler oder Slack. Bei dem Zielsystem kann es sich auch um ein lokales System wie AD handeln.

* System für die domänenübergreifende Identitätsverwaltung ([System for Cross-domain Identity Management, SCIM](https://aka.ms/scimoverview)) – Ein offener Standard für die Automatisierung der Benutzerbereitstellung. SCIM kommuniziert Benutzeridentitätsdaten zwischen Identitätsanbietern wie Microsoft und Dienstanbietern wie Salesforce oder anderen Saas-Apps, die Benutzeridentitätsinformationen benötigen.

### <a name="training-resources"></a>Schulungsressourcen

| Ressourcen| Link und Beschreibung |
| - | - |
| On-Demand-Webinare| [Verwalten von Unternehmensanwendungen mit Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>‎Erfahren Sie, wie Ihnen Azure AD das einmalige Anmelden bei Ihren SaaS-Unternehmensanwendungen ermöglicht, und lernen Sie bewährte Methoden zum Steuern des Zugriffs kennen. |
| Videos| [Was ist die Benutzerbereitstellung in Azure Active Directory?](https://youtu.be/_ZjARPpI6NI) <br> [Implementieren der Benutzerbereitstellung in Azure Active Directory](https://youtu.be/pKzyts6kfrw) <br> [Integrieren von Salesforce mit Azure AD: Automatisieren der Benutzerbereitstellung](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Onlinekurse| SkillUp Online:  [Verwalten von Identitäten](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Erfahren Sie, wie Sie Azure AD in viele SaaS-Anwendungen integrieren und den Benutzerzugriff auf diese Anwendungen schützen. |
| Bücher| [Modern Authentication with Azure Active Directory for Web Applications (Developer Reference) 1st Edition](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0) (Moderne Authentifizierung mit Azure Active Directory für Webanwendungen (Entwicklerreferenz), 1. Auflage).  <br> ‎Dies ist ein autoritativer, ausführlicher Leitfaden für die Entwicklung von Active Directory-Authentifizierungslösungen für diese neuen Umgebungen. |
| Tutorials| Weitere Informationen finden Sie in der [Liste der Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](../saas-apps/tutorial-list.md). |
| Häufig gestellte Fragen| [Häufig gestellte Fragen](../app-provisioning/user-provisioning.md) zur automatisierten Benutzerbereitstellung |

### <a name="solution-architectures"></a>Lösungsarchitekturen

Der Azure AD-Bereitstellungsdienst stellt Benutzer für SaaS-Apps und andere Systeme bereit, indem er eine Verbindung mit Endpunkten der Benutzerverwaltungs-API herstellt, die von den jeweiligen Anwendungsanbietern zur Verfügung gestellt werden. Diese Endpunkte der Benutzerverwaltungs-API ermöglichen Azure AD das programmgesteuerte Erstellen, Aktualisieren und Entfernen von Benutzern.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatische Benutzerbereitstellung für Hybridunternehmen

In diesem Beispiel werden Benutzer und/oder Gruppen in einer mit einem lokalen Verzeichnis verbundenen HR-Datenbank erstellt. Der Azure AD-Bereitstellungsdienst verwaltet die automatische Benutzerbereitstellung für die SaaS-Zielanwendungen.

 ![Benutzerbereitstellung](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Beschreibung des Workflows:**

1. Benutzer/Gruppen werden in einer lokalen HR-Anwendung/einem lokalen HR-System (z. B. SAP) erstellt. 

1. Der **Azure AD Connect-Agent** führt geplante Synchronisierungen von Identitäten (Benutzer und Gruppen) aus der lokalen AD-Instanz mit Azure AD aus.

1. Der **Azure AD-Bereitstellungsdienst** startet einen [ersten Zyklus](../app-provisioning/user-provisioning.md) für das Quellsystem und das Zielsystem. 

1. Der **Azure AD-Bereitstellungsdienst** fragt das Quellsystem nach Benutzern und Gruppen ab, die seit dem ersten Zyklus geändert wurden, und überträgt Änderungen mithilfe von Push in [inkrementellen Zyklen](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatische Benutzerbereitstellung für reine Cloudunternehmen

In diesem Beispiel erfolgt die Benutzererstellung in Azure AD, und der Azure AD-Bereitstellungsdienst verwaltet die automatische Benutzerbereitstellung für die SaaS-Zielanwendungen.

![Diagramm: Benutzer-/Gruppenerstellungsprozess einer lokalen Personalanwendung über den Azure-Bereitstellungsdienst für die SaaS-Zielanwendungen](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Beschreibung des Workflows:**

1. Benutzer/Gruppen werden in Azure AD erstellt.

1. Der **Azure AD-Bereitstellungsdienst** startet einen [ersten Zyklus](../app-provisioning/user-provisioning.md) für das Quellsystem und das Zielsystem. 

1. Der **Azure AD-Bereitstellungsdienst** fragt das Quellsystem nach Benutzern und Gruppen ab, die seit dem ersten Zyklus aktualisiert wurden, und führt [inkrementelle Zyklen](../app-provisioning/user-provisioning.md) aus.

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Automatische Benutzerbereitstellung für HR-Cloudanwendungen 

In diesem Beispiel werden die Benutzer und/oder Gruppen in einer HR-Cloudanwendung wie Workday oder SuccessFactors erstellt. Der Azure AD-Bereitstellungsdienst und der Azure AD Connect-Bereitstellungs-Agent stellen die Benutzerdaten aus der HR-Cloud-App in AD zur Verfügung. Sobald die Konten in AD aktualisiert wurden, erfolgt über Azure AD Connect die Synchronisierung mit Azure AD, und die E-Mail-Adressen und Benutzernamenattribute können an den Mandanten der HR-Cloud-App zurückgeschrieben werden.

![Abbildung 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  Das **HR-Team** führt die Transaktionen im Mandanten der HR-Cloud-App durch.
2.  Der **Azure AD-Bereitstellungsdienst** führt die geplanten Zyklen vom Mandanten der HR-Cloud-App aus und erkennt Änderungen, die zur Synchronisierung mit AD verarbeitet werden müssen.
3.  Der **Azure AD-Bereitstellungsdienst** ruft den Azure AD Connect-Bereitstellungs-Agent mit einer Anforderungsnutzlast auf, die die Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgänge für das AD-Konto enthält.
4.  Der **Azure AD Connect-Bereitstellungs-Agent** verwendet ein Dienstkonto zum Verwalten von AD-Kontodaten.
5.  **Azure AD Connect** führt eine Deltasynchronisierung aus, um Updates in AD zu pullen.
6.  **AD**-Updates werden mit Azure AD synchronisiert. 
7.  Der **Azure AD-Bereitstellungsdienst** schreibt E-Mail-Attribut und Benutzername aus Azure AD in den Mandanten der HR-Cloud-App zurück.

## <a name="plan-the-deployment-project"></a>Planen des Bereitstellungsprojekts

Berücksichtigen Sie die Anforderungen Ihrer Organisation, um die Strategie für die Implementierung der Benutzerbereitstellung in Ihrer Umgebung festzulegen.

### <a name="engage-the-right-stakeholders"></a>Einbeziehen der richtigen Beteiligten

Wenn Technologieprojekte nicht gelingen, ist dies in der Regel auf nicht erfüllte Erwartungen auf den Gebieten Auswirkungen, Ergebnisse und Zuständigkeiten zurückzuführen. Um diese Fallstricke zu vermeiden, stellen Sie sicher, dass Sie [die richtigen Projektbeteiligten einbeziehen](../fundamentals/active-directory-deployment-plans.md) und dass die Rollen der Projektbeteiligten gut verstanden werden, indem Sie die Beteiligten sowie deren Projektbeitrag und Zuständigkeiten dokumentieren.

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Kommunikation ist ein kritischer Faktor für den Erfolg jedes neuen Diensts. Kommunizieren Sie proaktiv mit Ihren Benutzern darüber, wie sich die Nutzung ändern wird, wann sie sich ändert und wie sie Unterstützung erhalten, wenn sie auf Probleme stoßen.

### <a name="plan-a-pilot"></a>Planen eines Pilotprojekts

Es wird empfohlen, die Erstkonfiguration der automatischen Benutzerbereitstellung in einer Testumgebung mit einer kleinen Teilmenge von Benutzern vorzunehmen, bevor die Skalierung auf alle Benutzer in der Produktionsumgebung erfolgt. Siehe hierzu [Bewährte Methoden](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) für das Ausführen eines Politprojekts.

#### <a name="best-practices-for-a-pilot"></a>Bewährte Methoden für einen Pilotversuch  

Bei einem Pilotversuch können Sie eine Funktion mit einer kleinen Gruppe testen, bevor Sie die Funktion für alle Benutzer bereitstellen. Sorgen Sie im Rahmen Ihrer Tests dafür, dass die einzelnen Anwendungsfälle in Ihrer Organisation sorgfältig getestet werden.

Zielen Sie in der ersten Phase auf IT und Benutzerfreundlichkeit ab, und wählen Sie zum Testen andere geeignete Benutzer aus, die Feedback bereitstellen können. Verwenden Sie dieses Feedback, um die Informationen und Anweisungen weiterzuentwickeln, die Sie an Ihre Benutzer senden, und Erkenntnisse über die Art von Problemen zu gewinnen, mit denen Ihre Supportmitarbeiter möglicherweise konfrontiert sind.

Erweitern Sie das Rollout auf größere Benutzergruppen, indem Sie die Zielgruppe(n) ausweiten. Dies kann über eine [dynamische Gruppenmitgliedschaft](../enterprise-users/groups-dynamic-membership.md) oder durch manuelles Hinzufügen von Benutzern zu den Zielgruppen erfolgen.

## <a name="plan-application-connections-and-administration"></a>Planen der Anwendungsverbindungen und -verwaltung

Verwenden Sie das Azure AD-Portal, um alle die Bereitstellung unterstützenden Anwendungen anzuzeigen und zu verwalten. Weitere Informationen finden Sie unter [Suchen Ihrer Apps im Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md).

### <a name="determine-the-type-of-connector-to-use"></a>Ermitteln des zu verwendenden Connectortyps

Die tatsächlichen Schritte, die zum Aktivieren und Konfigurieren der automatischen Bereitstellung erforderlich sind, variieren je nach Anwendung. Wenn die Anwendung, die Sie automatisch bereitstellen möchten, im [Azure AD-Katalog für Saas-Apps](../saas-apps/tutorial-list.md) aufgeführt ist, sollten Sie das [Tutorial zur Integration der entsprechenden Anwendung](../saas-apps/tutorial-list.md) auswählen, um den bereits integrierten Benutzerbereitstellungsconnector zu konfigurieren.

Führen Sie andernfalls die folgenden Schritte aus:

1. [Erstellen Sie eine Anforderung](../develop/v2-howto-app-gallery-listing.md) für einen bereits integrierten Benutzerbereitstellungsconnector. Unser Team arbeitet mit Ihnen und dem Anwendungsentwickler zusammen, um Ihre Anwendung in unsere Plattform zu integrieren, wenn sie SCIM unterstützt.

1. Verwenden Sie die allgemeine [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)-Benutzerbereitstellungsunterstützung für die App. Dies ist eine Voraussetzung für Azure AD, um Benutzer für die App ohne einen bereits integrierten Bereitstellungsconnector bereitstellen zu können.

1. Wenn die Anwendung den BYOA SCIM-Connector verwenden kann, lesen Sie die Informationen im [Tutorial zur BYOA SCIM-Integration](../app-provisioning/use-scim-to-provision-users-and-groups.md), um den BYOA SCIM-Connector für die Anwendung zu konfigurieren.

Weitere Informationen finden Sie unter [Welche Anwendungen und Systeme kann ich mit der automatischen Benutzerbereitstellung von Azure AD verwenden?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Erfassen von Informationen zum Autorisieren des Anwendungszugriffs

Das Einrichten der automatischen Benutzerbereitstellung erfolgt pro Anwendung. Für jede Anwendung müssen Sie [Administratoranmeldeinformationen](../app-provisioning/configure-automatic-user-provisioning-portal.md) angeben, um eine Verbindung mit dem Endpunkt der Benutzerverwaltung des Zielsystems herstellen zu können.

In der folgenden Abbildung ist eine Version der erforderlichen Administratoranmeldeinformationen dargestellt:

![Bildschirm „Bereitstellung“ zum Verwalten der Einstellungen für die Bereitstellung von Benutzerkonten](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Während einige Anwendungen den Administratorbenutzernamen und das Kennwort erfordern, benötigen andere möglicherweise ein Bearertoken.

## <a name="plan-user-and-group-provisioning"></a>Planen der Benutzer- und Gruppenbereitstellung

Wenn Sie die Benutzerbereitstellung für Unternehmens-Apps aktivieren, steuert das [Azure-Portal](https://portal.azure.com/) die zugehörigen Attributwerte durch die Attributzuordnung.

### <a name="determine-operations-for-each-saas-app"></a>Festlegen von Vorgängen für jede SaaS-App

Jede Anwendung verfügt möglicherweise über eindeutige Benutzer- oder Gruppenattribute, die den Attributen in Ihrer Azure AD-Instanz zugeordnet werden müssen. Möglicherweise sind für eine Anwendung nicht alle CRUD-Vorgänge verfügbar.

Dokumentieren Sie für jede Anwendung die folgenden Informationen:

* Die CRUD-Bereitstellungsvorgänge, die für die Benutzer- und/oder Gruppenobjekte der Zielsysteme ausgeführt werden sollen. Beispielsweise möchte ein Besitzer einer SaaS-App nicht alle möglichen Vorgänge ausführen.

* Die im Quellsystem verfügbaren Attribute

* Die im Zielsystem verfügbaren Attribute

* Die Zuordnung von Attributen zwischen den Systemen.

### <a name="choose-which-users-and-groups-to-provision"></a>Auswählen der bereitzustellenden Benutzer und Gruppen

Vor dem Implementieren der automatischen Benutzerbereitstellung müssen Sie die Benutzer und Gruppen festlegen, die für Ihre Anwendung bereitgestellt werden sollen.

* Verwenden Sie [Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md), um attributbasierte Regeln zu definieren, die festlegen, welche Benutzer für eine Anwendung bereitgestellt werden.

* Verwenden Sie dann nach Bedarf [Benutzer- und Gruppenzuweisungen](../manage-apps/assign-user-or-group-access-portal.md) für eine zusätzliche Filterung.

### <a name="define-user-and-group-attribute-mapping"></a>Definieren von Benutzer- und Gruppenattributzuordnungen

Zum Implementieren der automatischen Benutzerbereitstellung müssen Sie die für die Anwendung erforderlichen Benutzer- und Gruppenattribute definieren. Es ist eine vorkonfigurierte Sammlung von Attributen und [Attributzuordnungen](../app-provisioning/configure-automatic-user-provisioning-portal.md) zwischen Azure AD-Benutzerobjekten und den Benutzerobjekten der einzelnen SaaS-Anwendungen verfügbar. Nicht alle Saas-Apps unterstützen Gruppenattribute.

Azure AD unterstützt durch die direkte Zuordnung von Attribut zu Attribut, die Bereitstellung konstanter Werte oder das [Schreiben von Ausdrücken für Attributzuordnungen](../app-provisioning/functions-for-customizing-application-data.md). Durch diese Flexibilität können Sie genau steuern, was im Attribut des Zielsystems aufgefüllt wird. Mit der [Microsoft Graph-API](../app-provisioning/export-import-provisioning-configuration.md) und dem Graph-Tester können Sie die Attributzuordnungen und das Schema Ihrer Benutzerbereitstellung in eine JSON-Datei exportieren und wieder in Azure AD importieren.

Weitere Informationen finden Sie unter [Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Besondere Aspekte bei der Benutzerbereitstellung

Berücksichtigen Sie Folgendes, um Probleme nach der Bereitstellung zu minimieren:

* Stellen Sie sicher, dass die Attribute, mit denen Benutzer-/Gruppenobjekte zwischen Quell- und Zielanwendungen zugeordnet werden, resilient sind. Bei Änderungen der Attribute (beispielsweise wenn ein Benutzer in einen anderen Bereich des Unternehmens wechselt) sollten Benutzer/Gruppen nicht falsch bereitgestellt werden.

* Für Anwendungen gelten möglicherweise bestimmte Einschränkungen und/oder Anforderungen, die eingehalten/erfüllt werden müssen, damit die Benutzerbereitstellung ordnungsgemäß funktioniert. In Slack werden beispielsweise Werte für bestimmte Attribute abgeschnitten. Entsprechende Informationen finden Sie in den [Tutorials zur automatischen Benutzerbereitstellung](../saas-apps/tutorial-list.md) für die einzelnen Anwendungen.

* Stellen Sie Schemakonsistenz zwischen den Quell- und Zielsystemen sicher. Zu häufigen Problemen zählen z. B. nicht übereinstimmende Attribute wie UPN oder E-Mail. In Azure AD wird der UPN beispielsweise im Format *john_smith@contoso.com* und in der App im Format *jsmith@contoso.com* festgelegt. Weitere Informationen finden Sie unter [Referenz zum Benutzer- und Gruppenschema](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Planen von Tests und der Sicherheit

Stellen Sie in jeder Phase der Bereitstellung sicher, dass Sie testen, ob die Ergebnisse erwartungsgemäß ausfallen, und überwachen Sie die Bereitstellungszyklen.

### <a name="plan-testing"></a>Planen von Tests

Nachdem Sie die automatische Benutzerbereitstellung für die Anwendung konfiguriert haben, führen Sie Testfälle aus, um zu überprüfen, ob diese Lösung die Anforderungen Ihrer Organisation erfüllt.

| Szenarien| Erwartete Ergebnisse |
| - | - |
| Ein Benutzer wird einer dem Zielsystem zugewiesenen Gruppe hinzugefügt. | Das Benutzerobjekt wird im Zielsystem bereitgestellt. <br>Der Benutzer kann sich beim Zielsystem anmelden und die gewünschten Aktionen ausführen. |
| Ein Benutzer wird aus einer dem Zielsystem zugewiesenen Gruppe entfernt. | Die Bereitstellung des Benutzerobjekts wird im Zielsystem aufgehoben.<br>Der Benutzer kann sich nicht beim Zielsystem anmelden. |
| Die Benutzerinformationen werden in Azure AD mittels einer beliebigen Methode aktualisiert. | Aktualisierte Benutzerattribute werden im Zielsystem nach einem inkrementellen Zyklus reflektiert. |
| Ein Benutzer befindet sich außerhalb des gültigen Bereichs. | Das Benutzerobjekt wird deaktiviert oder gelöscht. <br>Hinweis: Dieses Verhalten wird bei der [Workday-Bereitstellung](skip-out-of-scope-deletions.md) außer Kraft gesetzt. |

### <a name="plan-security"></a>Planen der Sicherheit

Es ist üblich, dass im Rahmen einer Bereitstellung eine Sicherheitsüberprüfung erforderlich ist. Wenn Sie eine Sicherheitsüberprüfung benötigen, lesen Sie die vielen Azure AD-[Whitepaper](https://www.microsoft.com/download/details.aspx?id=36391), in denen Sie eine Übersicht über die Identität als Dienst finden.

### <a name="plan-rollback"></a>Planen eines Rollbacks

Wenn die Implementierung der automatischen Benutzerbereitstellung in der Produktionsumgebung nicht wie gewünscht funktioniert, können Sie die folgenden Rollbackschritte ausführen, um einen früheren bekannten fehlerfreien Zustand wiederherzustellen:

1. Überprüfen Sie den [Zusammenfassungsbericht für die Bereitstellung](../app-provisioning/check-status-user-account-provisioning.md) und die [Bereitstellungsprotokolle](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview), um zu ermitteln, welche fehlerhaften Vorgänge für die betroffenen Benutzer und/oder Gruppen aufgetreten sind.

1. Ermitteln Sie anhand der Bereitstellungsüberwachungsprotokolle den letzten bekannten fehlerfreien Status der betroffenen Benutzer und/oder Gruppen. Überprüfen Sie auch die Quellsysteme (Azure AD oder AD).

1. Arbeiten Sie mit dem Anwendungsbesitzer zusammen, um die betroffenen Benutzer und/oder Gruppen direkt in der Anwendung mit den letzten bekannten funktionierenden Statuswerten zu aktualisieren.

## <a name="deploy-automatic-user-provisioning-service"></a>Bereitstellen des automatischen Benutzerbereitstellungsdiensts

Wählen Sie die auszuführenden Schritte gemäß den Anforderungen Ihrer Lösung aus.

### <a name="prepare-for-the-initial-cycle"></a>Vorbereiten des ersten Zyklus

Bei der ersten Ausführung des Azure AD-Bereitstellungsdiensts wird beim ersten Zyklus für das Quellsystem und die Zielsysteme eine Momentaufnahme aller Benutzerobjekte für jedes Zielsystem erstellt.

Wenn Sie die automatische Bereitstellung für eine Anwendung aktivieren, kann der erste Zyklus 20 Minuten bis mehrere Stunden dauern. Die Dauer hängt von der Größe des Azure AD-Verzeichnisses und der Anzahl der Benutzer im Bereitstellungsbereich ab.

Der Bereitstellungsdienst speichert den Status beider Systeme nach dem ersten Zyklus, sodass die Leistung der nachfolgenden inkrementellen Zyklen verbessert wird.

### <a name="configure-automatic-user-provisioning"></a>Konfigurieren der automatischen Benutzerbereitstellung

Verwenden Sie das [Azure-Portal](https://portal.azure.com/), um die automatische Benutzerkontobereitstellung und das Aufheben der Bereitstellung für Anwendungen zu verwalten, die dies unterstützen. Führen Sie die unter [Wie richte ich die automatische Bereitstellung für eine Anwendung ein?](../app-provisioning/user-provisioning.md) aufgeführten Schritte aus.

Der Benutzerbereitstellungsdienst von Azure AD kann auch über die [Microsoft Graph-API](/graph/api/resources/synchronization-overview) konfiguriert und verwaltet werden.

## <a name="manage-automatic-user-provisioning"></a>Verwalten der automatischen Benutzerbereitstellung

Nach der Bereitstellung müssen Sie die Lösung verwalten.

### <a name="monitor-user-provisioning-operation-health"></a>Überwachen der Integrität des Benutzerbereitstellungsvorgangs

Nach einem erfolgreichen [ersten Zyklus](../app-provisioning/user-provisioning.md) führt der Azure AD-Bereitstellungsdienst so lange inkrementelle Aktualisierungen in jeweils anwendungspezifischen Intervallen aus, bis eines der folgenden Ereignisse eintritt:

* Der Dienst wird manuell beendet, und es wird über das [Azure-Portal](https://portal.azure.com/) oder mithilfe des entsprechenden [Microsoft Graph-API](/graph/api/resources/synchronization-overview)-Befehls ein neuer erster Zyklus ausgelöst.

* Ein neuer erster Zyklus wird durch eine Änderung in den Attributzuordnungen oder Bereichsfiltern ausgelöst.

* Der Bereitstellungsprozess wird aufgrund einer hohen Fehlerrate unter Quarantäne gestellt und bleibt mehr als vier Wochen lang in Quarantäne, bevor er automatisch deaktiviert wird.

Wenn Sie diese Ereignisse und alle anderen vom Bereitstellungsdienst ausgeführten Aktivitäten überprüfen möchten, ziehen Sie die Azure AD-[Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) zurate.

Wenn Sie verstehen möchten, wie lange die Bereitstellungszyklen dauern, und den Fortschritt des Bereitstellungsauftrags überwachen möchten, können Sie [den Status der Benutzerbereitstellung überprüfen](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Gewinnen von Erkenntnissen aus Berichten

Azure AD kann durch Überwachungsprotokolle und Berichte [zusätzliche Einblicke](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) in die Nutzung der Benutzerbereitstellung und in die Betriebsintegrität Ihrer Organisation bieten.

Administratoren sollten den Zusammenfassungsbericht für die Bereitstellung überprüfen, um die Betriebsintegrität des Bereitstellungsauftrags zu überwachen. Alle vom Bereitstellungsdienst ausgeführten Aktivitäten werden in den Azure AD-Überwachungsprotokollen erfasst. Siehe [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

Wir empfehlen Ihnen, den Besitz dieser Berichte zu übernehmen und die Berichte in regelmäßigen Abständen gemäß den Anforderungen Ihrer Organisation zu verwenden. In Azure AD werden die meisten Überwachungsdaten 30 Tage lang beibehalten.

### <a name="troubleshoot"></a>Problembehandlung

Unter den folgenden Links finden Sie Informationen zum Beheben von Problemen, die bei der Bereitstellung auftreten können:

* [Problem beim Konfigurieren der Benutzerbereitstellung für eine Azure AD-Kataloganwendung](../app-provisioning/application-provisioning-config-problem.md)

* [Synchronisieren eines Attributs aus lokalen Active Directory Domain Services mit Azure AD für die Bereitstellung einer Anwendung](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Problem saving administrator credentials while configuring user provisioning to an Azure Active Directory Gallery application](./user-provisioning.md) (Probleme beim Speichern von Administratoranmeldeinformationen während des Konfigurierens der Benutzerbereitstellung in einer Anwendung aus dem Azure Active Directory-Katalog)

* [Es werden keine Benutzer für eine Azure AD-Kataloganwendung bereitgestellt](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Der falsche Satz von Benutzern wird für eine Azure AD-Kataloganwendung bereitgestellt](../manage-apps/add-application-portal-assign-users.md)

### <a name="helpful-documentation"></a>Hilfreiche Dokumentation

* [Schreiben von Ausdrücken für Attributzuordnungen](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD synchronization API overview](/graph/api/resources/synchronization-overview) (Azure AD-Synchronisierung – API-Übersicht)

* [Überspringen des Löschens von Benutzerkonten außerhalb des gültigen Bereichs](skip-out-of-scope-deletions.md)

* [Azure AD Connect-Bereitstellungs-Agent: Verlauf der Versionsveröffentlichungen](provisioning-agent-release-version-history.md).

#### <a name="resources"></a>Ressourcen

* [Abgeben von Produktfeedback](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Informieren Sie sich über Neuerungen bei Azure AD, um immer auf dem neuesten Stand zu sein](https://azure.microsoft.com/updates/?product=active-directory)

* [Stack Overflow: Azure AD-Forum](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Nächste Schritte
* [Konfigurieren der automatischen Benutzerbereitstellung](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Exportieren oder Importieren Ihrer Bereitstellungskonfiguration mithilfe von Graph-API](../app-provisioning/export-import-provisioning-configuration.md)

* [Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md)