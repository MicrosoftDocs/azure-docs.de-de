---
title: Häufig gestellte Fragen zu Azure DevTest Labs | Microsoft Docs
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2019
ms.author: spelluru
ms.openlocfilehash: a46d816c04d9f5629c2ee9538016d42c53f9a331
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244389"
---
# <a name="azure-devtest-labs-faq"></a>Häufig gestellte Fragen zu Azure DevTest Labs
Im Folgenden werden einige der am häufigsten gestellten Fragen zu Azure DevTest Labs beantwortet.

## <a name="blog-post"></a>Blogbeitrag
Unser DevTest Labs-Teamblog wurde am 20. März 2019 eingestellt. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Wo kann ich zukünftige Funktionsupdates nachverfolgen?
In Zukunft werden wir Funktionsupdates und informative Blogbeiträge im Azure-Blog und auf Azure-Updates bereitstellen. Diese Blogbeiträge werden auch, wo immer erforderlich, mit unserer Dokumentation verknüpft.

Abonnieren Sie [DevTest Labs-Azure-Blog](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) und [DevTest Labs-Azure-Updates](https://azure.microsoft.com/updates/?product=devtest-lab), um über neue Funktionen in DevTest Labs informiert zu bleiben.

### <a name="what-happens-to-the-existing-blog-posts"></a>Was geschieht mit den vorhandenen Blogbeiträgen?
Wir arbeiten momentan daran, vorhandene Blogbeiträge (mit Ausnahme von Updates zu Ausfällen) zu unserer [DevTest Labs-Dokumentation](devtest-lab-overview.md) zu migrieren. Wenn der MSDN-Blog eingestellt wird, wird er auf die Dokumentationsübersicht für DevTest Labs umgeleitet. Sobald die Umleitung eingerichtet ist, können Sie unter „Filtern nach“ nach dem gewünschten Artikel suchen. Wir haben noch nicht alle Beiträge migriert, bis zum Ende dieses Monats sollte dies jedoch abgeschlossen sein. 


### <a name="where-do-i-see-outage-updates"></a>Wo finde ich Updates zu Ausfällen?
In Zukunft werden wir Updates zu Ausfällen unter unserem Twitter-Handle bereitstellen. Folgen Sie uns auf Twitter, um die neuesten Updates zu Ausfällen und bekannten Fehlern zu erhalten.

### <a name="twitter"></a>Twitter
Unser Twitter-Handle: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Allgemein
### <a name="what-if-my-question-isnt-answered-here"></a>Was kann ich tun, wenn meine Frage hier nicht beantwortet wird?
Wenn Ihre Frage hier nicht aufgeführt wird, informieren Sie uns, damit wir Ihnen helfen können, eine Antwort zu finden.

- Veröffentlichen Sie eine Frage am Ende dieses Artikels zu häufig gestellten Fragen. Tauschen Sie sich mit dem Azure Cache-Team und anderen Communitymitgliedern über diesen Artikel aus.
- Um eine größere Zielgruppe zu erreichen, veröffentlichen Sie eine Frage im [Azure DevTest Labs MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Tauschen Sie sich mit dem Azure DevTest Labs-Team und anderen Communitymitgliedern aus.
- Um Funktionsanforderungen einzureichen, übermitteln Sie Ihre Anfragen und Ideen an [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Was ist ein Microsoft-Konto?
Ein Microsoft-Konto ist ein Konto, das Sie für nahezu jede Aktion verwenden, die Sie mit Geräten und Diensten von Microsoft ausführen. Es umfasst eine E-Mail-Adresse und ein Kennwort, mit dem Sie sich bei Skype, Outlook.com, OneDrive, Windows Phone, Azure und Xbox Live anmelden. Durch ein einzelnes Konto können Sie auf jedem Gerät auf Ihre Dateien, Fotos, Kontakte und Einstellungen zugreifen.
 
> [!NOTE]
> Früher wurde ein Microsoft-Konto als Windows Live ID bezeichnet.

### <a name="why-should-i-use-azure-devtest-labs"></a>Warum sollte ich Azure DevTest Labs verwenden?
Mit Azure DevTest Labs kann Ihr Team Zeit und Geld sparen. Entwickler können eigene Umgebungen auf unterschiedlichen Grundlagen erstellen. Zudem können sie mithilfe von Artefakten schnell Anwendungen bereitstellen und konfigurieren. Durch benutzerdefinierte Images und Formeln können Sie virtuelle Computer (VMs) als Vorlagen speichern und diese problemlos innerhalb des Teams reproduzieren. DevTest Labs bieten mehrere konfigurierbare Richtlinien, mit denen Labadministratoren die Vergeudung von Ressourcen reduzieren und die Umgebungen eines Teams verwalten können. Diese Richtlinien beinhalten u.a. das automatische Herunterfahren, Kostenschwellenwerte, eine maximale Anzahl von VMs pro Benutzer und eine maximale VM-Größe. Eine ausführlichere Erläuterung von DevTest Labs finden Sie in der [Übersicht](devtest-lab-overview.md) oder im [Einführungsvideo](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Was bedeutet „Self-Service-Bereitstellung für sorgenfreies Arbeiten“?
„Self-Service-Bereitstellung für sorgenfreies Arbeiten“ bedeutet, dass Entwickler und Tester ihrer eigenen Umgebungen nach Bedarf erstellen. Administratoren können sich dabei darauf verlassen, dass durch DevTest Labs der entsprechende Zugriff eingerichtet werden kann, die Verschwendung von Ressourcen reduziert werden kann und die Kosten unter Kontrolle behalten werden können. Administratoren können die zulässigen VM-Größen und die maximale Anzahl von VMs angeben und festlegen, wann VMs gestartet und beendet werden. DevTest Labs vereinfacht auch die Überwachung der Kosten und das Festlegen von Warnungen, sodass sie stets den Überblick darüber behalten, wie Ressourcen verwendet werden.

### <a name="how-can-i-use-devtest-labs"></a>Wie kann ich DevTest Labs verwenden?
DevTest Labs eignet sich, wenn Sie Entwicklungs- oder Testumgebungen benötigen und diese schnell reproduzieren oder mit kostensparenden Richtlinien verwalten möchten.
Unsere Kunden verwenden DevTest Labs beispielsweise für folgende Szenarien:

- Zentrale Verwaltung von Entwicklungs- und Testumgebungen. Senken Sie mithilfe von Richtlinien die Kosten, und erstellen Sie benutzerdefinierte Images, um Builds innerhalb des Teams freizugeben.
- Entwicklung von Anwendungen mit benutzerdefinierten Images, um den Datenträgerzustand in den Entwicklungsphasen zu speichern
- Nachverfolgung der Kosten im Verhältnis zum Status
- Erstellung von Massentestumgebungen für Qualitätssicherungstests
- Verwendung von Artefakten und Formeln zum einfachen Konfigurieren und Reproduzieren von Anwendungen in verschiedenen Umgebungen
- Verteilung von VMs für Hackathons (gemeinschaftliche Entwicklungs- oder Testarbeiten) und einfache Aufhebung der Bereitstellung nach Beendigung des Ereignisses

### <a name="how-am-i-billed-for-devtest-labs"></a>Wie wird die Nutzung von DevTest Labs abgerechnet?
DevTest Labs ist ein kostenloser Dienst. Für das Erstellen von Labs sowie das Konfigurieren von Richtlinien, Vorlagen und Artefakten in DevTest Labs fallen keinerlei Kosten an. Sie bezahlen nur für die Azure-Ressourcen, die Sie in den Labs verwenden, beispielsweise VMs, Speicherkonten und virtuelle Netzwerke. Unter [Preise von Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/) erfahren Sie mehr über die Kosten von Labressourcen.

## <a name="security"></a>Sicherheit

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Wie lauten die verschiedenen Sicherheitsstufen in DevTest Labs?
Der Sicherheitszugriff wird durch die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) bestimmt. Die Funktionsweise des Zugriffs ist leichter zu verstehen, wenn Sie die Unterschiede zwischen Berechtigungen, Rollen und Bereichen in der RBAC kennen.

- **Berechtigung**: Eine Berechtigung ermöglicht einen definierten Zugriff auf eine bestimmte Aktion. Zum Beispiel kann eine Berechtigung den Lesezugriff auf alle VMs ermöglichen.
- **Rolle**: Eine Rolle ist ein Satz von Berechtigungen, die gruppiert und einem Benutzer zugewiesen werden können. Zum Beispiel verfügt die Rolle „Abonnementbesitzer“ über Zugriff auf alle Ressourcen in einem Abonnement.
- **Bereich:** Ein Bereich ist eine Stufe innerhalb der Hierarchie einer Azure-Ressource. Zum Beispiel kann eine Ressourcengruppe, ein einzelnes Lab oder das gesamte Abonnement ein Bereich sein.

Innerhalb des Bereichs von DevTest Labs gibt es zwei Arten von Rollen zum Definieren von Benutzerberechtigungen:

- **Labbesitzer**: Ein Labbesitzer verfügt über Zugriff auf alle Ressourcen im Lab. Ein Labbesitzer kann u.a. Richtlinien sowie das virtuelle Netzwerk ändern und verfügt über Lese- und Schreibberechtigungen für alle VMs.
- **Labbenutzer**: Ein Labbenutzer kann alle Labressourcen wie VMs, Richtlinien und virtuelle Netzwerke einsehen. Allerdings kann ein Labbenutzer keine Richtlinien oder VMs ändern, die von anderen Benutzern erstellt wurden.

Sie können auch benutzerdefinierte Rollen in DevTest Labs erstellen. Informationen zum Erstellen benutzerdefinierter Rollen in DevTest Labs finden Sie im Artikel [Gewähren von Benutzerberechtigungen für bestimmte Labrichtlinien](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Da Bereiche hierarchisch sind, gelten die Berechtigungen, die einem Benutzer für einen bestimmten Bereich gewährt werden, automatisch für alle darin enthaltenen Bereiche auf niedrigerer Ebene. Ist einem Benutzer beispielsweise die Rolle „Abonnementbesitzer“ zugewiesen, hat der Benutzer Zugriff auf alle Ressourcen in einem Abonnement. Zu diesen Ressourcen zählen VMs, virtuelle Netzwerke und Labs. Ein Abonnementbesitzer erbt automatisch die Rolle des Labbesitzers. Umgekehrt ist dies jedoch nicht der Fall. Ein Labbesitzer hat Zugriff auf ein Lab, d.h. auf einen Bereich, der sich innerhalb der Hierarchie auf einer niedrigeren Ebene befindet als das Abonnement. Daher kann ein Labbesitzer keine VMs, virtuellen Netzwerke oder anderen Ressourcen außerhalb des Labs sehen.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Wie definiere ich die rollenbasierte Zugriffssteuerung für meine DevTest Labs-Umgebungen, um sicherzustellen, dass die IT-Abteilung die Steuerung übernimmt, während Entwickler und Tester ihre Arbeit erledigen können?
Es gibt ein allgemeines Muster, aber die Details sind von Ihrer Organisation abhängig.

Die IT-Zentrale sollte nur die benötigten Informationen kennen und den Projekt- und Anwendungsteams den erforderlichen Grad an Kontrolle ermöglichen. In der Regel bedeutet dies, dass die IT-Zentrale der Besitzer des Abonnements ist und die wichtigsten IT-Funktionen übernimmt, z.B. Netzwerkkonfigurationen. Die Gruppe von **Besitzern** eines Abonnement sollte klein sein. Diese Besitzer können weitere Besitzer vorschlagen, wenn dies erforderlich ist, oder Richtlinien auf Abonnementebene anwenden, z.B. „keine öffentliche IP-Adresse“.

Möglicherweise gibt es eine Teilmenge von Benutzern, die Zugriff auf ein Abonnement benötigen, z.B. der Support auf erster und zweiter Ebene. In diesem Fall wird empfohlen, diesen Benutzern Zugriff auf der Ebene **Mitwirkender** zu gewähren, sodass sie die Ressourcen verwalten können, aber keinen Benutzerzugriff erhalten oder Richtlinien anpassen können.

Die DevTest Labs-Ressource sollte im Besitz von Personen aus dem Umfeld des Projekt-/Anwendungsteams sein. Der Grund dafür ist, dass diesen Personen die Anforderungen an Computer und Software klar sind. In den meisten Organisationen ist der Besitzer dieser DevTest Labs-Ressource meist der Projekt-/Entwicklungsleiter. Dieser Besitzer kann Benutzer und Richtlinien in der Labumgebung und alle virtuellen Computer in der DevTest Labs-Umgebung verwalten.

Die Projekt-/Anwendungsteammitglieder sollten der Rolle **DevTest Lab-Benutzer** hinzugefügt werden. Diese Benutzer können virtuelle Computer erstellen (entsprechend den Richtlinien auf Lab- und Abonnementebene). Sie können auch ihre eigenen virtuellen Computer verwalten. Sie können keine virtuellen Computer verwalten, die anderen Benutzern gehören.

Weitere Informationen finden Sie in der Dokumentation [Azure-Unternehmensgerüst – präskriptive Abonnementgovernance](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Wie erstelle ich eine bestimmte Rolle, um Benutzern das Ausführen einer bestimmten Aufgabe zu ermöglichen?
Einen umfassenden Artikel zum Erstellen von benutzerdefinierten Rollen und Zuweisen von Berechtigungen zu einer Rolle finden Sie unter [Erteilen von Benutzerberechtigungen zu bestimmten Labrichtlinien](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Das folgende Beispielskript erstellt die Rolle **DevTest Labs Advanced User**, die über die Berechtigung zum Starten und Beenden alle VMs im Lab verfügt:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Wie kann eine Organisation sicherstellen, dass die Sicherheitsrichtlinien des Unternehmens eingehalten werden?
Eine Organisation kann dies mithilfe der folgenden Aktionen erreichen:

- Entwickeln und Veröffentlichen einer umfassenden Sicherheitsrichtlinie. Die Richtlinie definiert die Regeln der zulässigen Nutzung von Software und Cloudressourcen. Sie regelt auch, was eindeutig gegen die Richtlinie verstößt.
- Entwickeln eines benutzerdefinierten Images, benutzerdefinierten Artefakts und Bereitstellungsprozesses, der die Orchestrierung innerhalb des Sicherheitsbereichs ermöglicht, der mit Active Directory definiert ist. Dieser Ansatz setzt die Unternehmensgrenze durch und legt einen gemeinsamen Steuerelementsatz für die Umgebung fest. Entwickler können diese Umgebungssteuerelemente beim Entwickeln berücksichtigen und im Rahmen ihres Gesamtprozesses einen sicheren Entwicklungslebenszyklus verfolgen. Ziel ist es auch, eine Umgebung zu schaffen, die nicht zu restriktiv ist (was die Entwicklung behindern würde), sondern einen angemessenen Satz von Steuerelementen. Die Gruppenrichtlinien der Organisationseinheit (OE), die Lab-VMs enthält, können eine Teilmenge der gesamten Gruppenrichtlinien sein, die sich in der Produktion befinden. Alternativ können sie auch eine zusätzliche Menge sein, um identifizierte Risiken ordnungsgemäß zu minimieren.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Wie kann eine Organisation die Datenintegrität gewährleisten, damit Remotingentwickler weder Code entfernen noch Malware oder nicht genehmigte Software integrieren können?
Es gibt mehrere Kontrollebenen, um die Bedrohung durch externe Berater, Auftragnehmer und Mitarbeiter zu minimieren, die in DevTest Labs remote zusammenarbeiten.

Wie bereits erwähnt, muss im ersten Schritt eine Acceptable Use Policy ausgearbeitet und definiert werden, die die Folgen eines Verstoßes gegen die Richtlinie klar umreißt.

Die erste Kontrollebene für den Remotezugriff besteht darin, eine Remotezugriffsrichtlinie über eine VPN-Verbindung anzuwenden, die nicht direkt mit dem Lab verbunden ist.

Im Rahmen der zweiten Kontrollebene wird ein Satz von Gruppenrichtlinienobjekten angewendet, die das Kopieren und Einfügen über Remotedesktop verhindern. Sie können auch eine Netzwerkrichtlinie implementieren, um keine aus der Umgebung ausgehenden Dienste wie FTP- und RDP-Dienste zuzulassen. Benutzerdefiniertes Routing kann erzwingen, dass der gesamte Azure-Datenverkehr zurück an lokale Umgebungen übermittelt wird. Allerdings kann das Routing nicht alle URLs erfassen, die das Hochladen von Daten ermöglichen, es sei denn, die Steuerung erfolgt über einen Proxy zum Überprüfen von Inhalten und Sitzungen. Öffentliche IP-Adressen können im virtuellen Netzwerk eingeschränkt werden, das DevTest Labs unterstützt, um den Transfer einer externen Netzwerkressource zu untersagen.

Letztendlich muss für die gesamte Organisation der gleiche Einschränkungstyp gelten, der auch alle Methoden von Wechselmedien oder externen URLs berücksichtigt, die einen Beitrag von Inhalten akzeptieren können. Wenden Sie sich an Ihren Sicherheitsexperten, um eine Sicherheitsrichtlinie zu überprüfen und zu implementieren. Weitere Empfehlungen finden Sie unter [Microsoft-Cybersicherheit](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Labkonfiguration

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Wie erstelle ich ein Lab anhand einer Resource Manager-Vorlage?
Wir stellen ein [GitHub-Repository von Azure Resource Manager-Labvorlagen](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) zur Verfügung, die Sie in vorliegender oder geänderter Form zum Erstellen benutzerdefinierter Vorlagen für Ihre Labs bereitstellen können. Jede Vorlage enthält einen Link, um das Lab in Ihrem eigenen Azure-Abonnement bereitzustellen. Alternativ können Sie die Vorlage auch anpassen und [mithilfe von PowerShell oder der Azure CLI bereitstellen](../azure-resource-manager/resource-group-template-deploy.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Kann ich sämtliche VMs n einer gemeinsamen Ressourcengruppe erstellen lassen, anstatt für jede VM eine eigene Ressourcengruppe zu verwenden? 
Ja, als Labbesitzer können Sie entweder das Lab automatisch die Zuordnung von Ressourcengruppen vornehmen lassen oder sämtliche VMs in einer von Ihnen angegebenen gemeinsamen Ressourcengruppe erstellen. 

Szenario mit separaten Ressourcengruppen:
-   DevTest Labs erstellt eine neue Ressourcengruppe für jede von Ihnen eingerichtete VM mit öffentlicher/privater IP-Adresse.
-   DevTest Labs erstellt eine Ressourcengruppe für Computer mit freigegebenen IP-Adressen, die die gleiche Größe aufweisen.

Szenario mit gemeinsamer Ressourcengruppe:
-   Alle virtuellen Computer werden in der angegebenen gemeinsamen Ressourcengruppe erstellt und bereitgestellt. Erfahren Sie mehr über die [Ressourcengruppenzuordnung für das Lab](https://aka.ms/RGControl). 

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Wie halte ich eine übergreifende Benennungskonvention in meiner DevTest Labs-Umgebung aufrecht?
Es kann sinnvoll sein, aktuelle Benennungskonventionen im Unternehmen auf den Azure-Betrieb auszudehnen und sie über die gesamte DevTest Labs-Umgebung konsistent zu machen. Wir empfehlen, bei der Bereitstellung von DevTest Labs bestimmte Ausgangsrichtlinien zu implementieren. Sie stellen diese Richtlinien mit einem zentralen Skript und JSON-Vorlagen bereit, um Konsistenz durchzusetzen. Benennungsrichtlinien können mithilfe von Azure-Richtlinien implementiert werden, die auf Abonnementebene angewendet werden. JSON-Beispiele für Azure Policy finden Sie unter [Azure Policy-Beispiele](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Wie viele Labs kann ich unter demselben Abonnement erstellen?
Es gibt keine bestimmte Beschränkung für die Anzahl von Labs, die pro Abonnement erstellt werden können. Allerdings ist die Menge der pro Abonnement nutzbaren Ressourcen begrenzt. Weitere Informationen finden Sie bei Bedarf in den Artikeln zu den [Einschränkungen für Azure-Abonnements und Kontingente](../azure-subscription-service-limits.md) und zum [Erhöhen dieser Limits](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Wie viele VMs kann ich pro Lab erstellen?
Es gibt keine bestimmte Beschränkung für die Anzahl von virtuellen Computern (VMs), die pro Abonnement erstellt werden können. Die Menge an Ressourcen pro Abonnement (VM-Kerne, öffentliche IP-Adressen usw.) ist jedoch begrenzt. Weitere Informationen finden Sie bei Bedarf in den Artikeln zu den [Einschränkungen für Azure-Abonnements und Kontingente](../azure-subscription-service-limits.md) und zum [Erhöhen dieser Limits](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Wie lässt sich das Verhältnis von Benutzern pro Lab zur Gesamtzahl der innerhalb einer gesamten Organisation erforderlichen Labs bestimmen?
Wir empfehlen, dass Geschäftseinheiten und Entwicklungsgruppen, die dem gleichen Entwicklungsprojekt angehören, dem gleichen Lab zugeordnet werden. Dadurch können für beide Gruppen die gleichen Arten von Richtlinien, Images und Richtlinien zum Herunterfahren angewendet werden.

Ggf. müssen auch geografische Grenzen berücksichtigt werden. Beispielsweise benutzen Entwickler im Nordosten der USA möglicherweise ein Lab, das in USA Osten 2 bereitgestellt ist. Demgegenüber sind Entwickler in Dallas, Texas, und Denver, Colorado, gehalten, eine Ressource in USA Süden-Mitte zu verwenden. Wenn es zur einer Zusammenarbeit mit einem externen Drittanbieter kommt, werden beide möglicherweise einem Lab zugewiesen, das nicht von internen Entwicklern verwendet wird.

Sie können auch ein Lab für ein bestimmtes Projekt in Azure DevOps Projects verwenden. Anschließend wenden Sie Sicherheit in Form einer angegebenen Azure Active Directory-Gruppe an, was den Zugriff auf beide Ressourcensätze ermöglicht. Das dem Lab zugewiesene virtuelle Netzwerk kann eine weitere Grenze zur Konsolidierung der Benutzer bilden.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Wie lässt sich das Löschen von Ressourcen in einem Lab verhindern?
Es empfiehlt sich, ordnungsgemäße Berechtigungen auf Lab-Ebene festzulegen, so dass nur autorisierte Benutzer Ressourcen löschen oder Lab-Richtlinien ändern können. Entwickler sollten innerhalb der Gruppe **DevTest Labs-Benutzer** platziert werden. Der Entwicklungsleiter oder der Infrastruktur-Lead sollte als **DevTest Labs-Besitzer** festgelegt werden. Wir empfehlen, nur zwei Lab-Besitzer festzulegen. Diese Richtlinie wird auf das Coderepository ausgeweitet, um Beschädigungen zu vermeiden. Lab-Benutzer verfügen über Rechte zur Nutzung von Ressourcen, sie können aber keine Lab-Richtlinien aktualisieren. Der folgende Artikel listet die Rollen und Rechte auf, über die jede integrierte Gruppe innerhalb eines Labs verfügt: [Hinzufügen von Besitzern und Benutzern in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Wie gebe ich einen direkten Link zu meinem Lab frei?

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zum Lab.
2. Kopieren Sie die **Lab-URL** aus Ihrem Browser, und geben Sie sie dann für Ihre Labbenutzer frei.

> [!NOTE]
> Wenn es sich bei einem Labbenutzer um einen externen Benutzer handelt, der ein Microsoft-Konto besitzt, jedoch nicht Mitglied der Active Directory-Instanz Ihrer Organisation ist, wird dem Benutzer bei dem Versuch, auf den freigegebenen Link zuzugreifen, möglicherweise eine Fehlermeldung angezeigt. Wenn einem externen Benutzer eine Fehlermeldung angezeigt wird, fordern Sie den Benutzer auf, oben rechts im Azure-Portal zuerst seinen Namen auszuwählen. Dann kann der Benutzer im Abschnitt „Verzeichnis“ des Menüs das Verzeichnis auswählen, in dem sich das Lab befindet.

## <a name="virtual-machines"></a>Virtuelle Computer

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Warum kann ich auf der Seite „Virtuelle Computer“ nicht die VMs sehen, die in DevTest Labs angezeigt werden?
Beim Erstellen einer VM in DevTest Labs wird Ihnen eine Berechtigung für den Zugriff auf diese VM gewährt. Sie können die VM sowohl auf der Seite „Labs“ als auch auf der Seite **Virtuelle Computer** sehen. Benutzer, denen die **DevTest Labs-Labbesitzerrolle** zugewiesen ist, können alle im Lab erstellten VMs auf der Seite **Alle virtuellen Computer** des Labs sehen. Allerdings erhalten Benutzer mit der **DevTest Labs-Labbenutzerrolle** nicht automatisch Lesezugriff auf die VM-Ressourcen, die andere Benutzer erstellt haben. Daher werden diese VMs nicht auf der Seite **Virtuelle Computer** angezeigt.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Wie kann ich mehrere VMs gleichzeitig anhand derselben Vorlage erstellen?
Sie haben zwei Optionen, um gleichzeitig mehrere VMs anhand derselben Vorlage zu erstellen:

- Sie können die [Azure DevOps Tasks-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) verwenden.
- Sie können beim Erstellen einer VM [eine Resource Manager-Vorlage generieren](devtest-lab-add-vm.md#save-azure-resource-manager-template) und [die Resource Manager-Vorlage über Windows PowerShell bereitstellen](../azure-resource-manager/resource-group-template-deploy.md).
- Sie können auch angeben, dass während der Erstellung der VM mehrere Instanzen eines Computers erstellt werden sollen. Weitere Informationen zum Erstellen mehrerer Instanzen von VMs finden Sie in der Dokumentation zum [Erstellen einer Lab-VM](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Wie verschiebe ich meine vorhandenen Azure-VMs in mein DevTest Labs-Lab?
So kopieren Sie Ihre vorhandenen VMs in DevTest Labs

1.  Kopieren Sie die VHD-Datei Ihrer vorhandenen VM mithilfe eines [Windows PowerShell-Skripts](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Erstellen Sie das [benutzerdefinierte Image](devtest-lab-create-template.md) in Ihrem DevTest Labs-Lab.
3.  Erstellen Sie anhand des benutzerdefinierten Image eine VM im Lab.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Kann ich mehrere Datenträger an meine VMs anfügen?

Ja, Sie können mehrere Datenträger an Ihre VMs anfügen.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Wenn ich ein Windows-Betriebssystemimage für meine Tests verwenden möchte, muss ich dann ein MSDN-Abonnement erwerben?
Um Windows-Clientbetriebssystem-Images (Windows 7 oder höher) für Ihre Entwicklung oder Ihre Tests in Azure zu verwenden, müssen Sie einen der folgenden Schritte durchführen:

- [Ein MSDN-Abonnement erwerben](https://www.visualstudio.com/products/how-to-buy-vs).
- Wenn Sie über Enterprise Agreement verfügen, erstellen Sie mit dem [Enterprise Dev/Test-Angebot](https://azure.microsoft.com/offers/ms-azr-0148p) ein Azure-Abonnement.

Weitere Informationen zu den Azure-Gutschriften für die einzelnen MSDN-Angebote finden Sie unter [Monatliche Azure-Gutschrift für Visual Studio-Abonnenten](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Wie kann ich den Löschvorgang für alle VMs in meinem Lab automatisieren?
Als Labbesitzer können Sie VMs aus Ihrem Lab im Azure-Portal löschen. Mit einem PowerShell-Skript können Sie ebenfalls alle VMs im Lab löschen. Ändern Sie im folgenden Beispiel die Werte der Parameter unter dem Kommentar **values to change** (Zu ändernde Werte). Sie können die Werte für subscriptionId, labResourceGroup und labName aus dem Lab-Bereich im Azure-Portal abrufen.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Umgebungen 

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Wie können Resource Manager-Vorlagen in der DevTest Labs-Umgebung verwendet werden?
Sie stellen Ihre Resource Manager-Vorlagen mithilfe der im Artikel [Environments feature in DevTest labs](devtest-lab-test-env.md) (Umgebungsfeature in DevTest Labs) genannten Schritte in einer DevTest Labs-Umgebung bereit. Im Wesentlichen checken Sie Ihre Resource Manager-Vorlagen in ein Git-Repository ein (Azure Repos oder GitHub) und fügen dem Lab ein [privates Repository für Ihre Vorlagen](devtest-lab-test-env.md) hinzu. Dieses Szenario ist möglicherweise nicht nützlich, wenn Sie DevTest Labs zum Hosten von Entwicklungscomputern verwenden, es kann aber beim Erstellen einer Stagingumgebung nützlich sein, die eine Produktionsumgebung darstellen soll.

Außerdem ist erwähnenswert, dass die Option für die Anzahl der virtuellen Computer pro Lab oder pro Benutzer nur die Anzahl der nativ im Lab selbst erstellten Computer einschränkt, Umgebungsparameter (Resource Manager-Vorlagen) aber nicht betrifft.

## <a name="custom-images"></a>Custom Images

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Wie kann ich einen leicht wiederholbaren Prozess einrichten, um meine benutzerdefinierten organisationseigenen Images in einer DevTest Labs-Umgebung einzusetzen?
Sehen Sie sich dazu dieses [Video zum Image Factory-Muster](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4) an. Dabei handelt es sich um ein erweitertes Szenario, und die bereitgestellten Skripts stellen lediglich Beispielskripts dar. Wenn irgendwelche Änderungen erforderlich sind, müssen Sie die in Ihrer Umgebung verwendeten Skripts verwalten und warten.

Ausführliche Informationen zum Erstellen einer Image Factory finden Sie unter [Erstellen einer benutzerdefinierten Image Factory in Azure DevTest Labs](image-factory-create.md). 

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Was ist der Unterschied zwischen einem benutzerdefinierten Image und einer Formel?
Ein benutzerdefiniertes Image ist ein verwaltetes Image. Eine Formel ist ein Image, das Sie mit zusätzlichen Einstellungen konfigurieren und anschließend speichern und reproduzieren können. Ein benutzerdefiniertes Image kann vorteilhafter sein, wenn Sie schnell mehrere Umgebungen mit demselben grundlegenden, unveränderlichen Image erstellen möchten. Eine Formel eignet sich möglicherweise besser, wenn Sie die Konfiguration Ihrer VM mit den neuesten Komponenten als Teil eines virtuellen Netzwerks oder Subnetzes oder als VM mit einer bestimmten Größe reproduzieren möchten. Eine ausführlichere Erklärung finden Sie im Artikel [Vergleich zwischen benutzerdefinierten Images und Formeln in DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Wann sollte ein Formel- im Vergleich zu einem benutzerdefinierten Image bevorzugt werden?
In der Regel sind die entscheidenden Faktoren Kosten und Wiederverwendbarkeit. Wenn in Ihrem Szenario viele Benutzer/Labs ein Image mit viel Software zusätzlich zum Basisimage erfordern, können Sie durch Erstellen eines benutzerdefinierten Images möglicherweise die Kosten reduzieren. Dies bedeutet, dass das Image einmal erstellt wird. Dadurch verringern sich der Zeitbedarf für die Einrichtung des virtuellen Computers und die Kosten, die bei der Ausführung des virtuellen Computers während des Setups anfallen würden.

Ein weiterer Faktor, der Beachtung verdient, ist jedoch die Häufigkeit von Änderungen an Ihrem Softwarepaket. Wenn Sie täglich Builds ausführen und es erforderlich ist, dass diese Software auf den virtuellen Computern Ihrer Benutzer vorhanden ist, ziehen Sie die Verwendung eines Formel- anstelle eines benutzerdefinierten Images in Betracht.

Eine ausführlichere Erklärung finden Sie im Artikel [Vergleich zwischen benutzerdefinierten Images und Formeln in DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Wie automatisiere ich das Hochladen von VHD-Dateien zum Erstellen benutzerdefinierter Images?

Um das Hochladen von VHD-Dateien zum Erstellen benutzerdefinierter Images automatisieren, haben Sie zwei Optionen:

- Verwenden Sie [AzCopy](../storage/common/storage-use-azcopy-v10.md), um VHD-Dateien in das dem Lab zugeordneten Speicherkonto zu kopieren oder hochzuladen.
- Verwenden Sie den [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Der Storage-Explorer ist eine eigenständige App, die unter Windows, OS X und Linux ausgeführt wird.

So suchen Sie nach dem Zielspeicherkonto, das Ihrem Lab zugeordnet ist:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2.  Wählen Sie im Menü auf der linken Seite die Option **Ressourcengruppen** aus.
3.  Suchen Sie nach der Ressourcengruppe, die Ihrem Lab zugeordnet ist, und wählen Sie sie aus.
4.  Wählen Sie unter **Übersicht** eines der Speicherkonten aus.
5.  Wählen Sie **Blobs**aus.
6.  Suchen Sie in der Liste nach Uploads. Falls kein Upload vorhanden ist, kehren Sie zu Schritt 4 zurück, und versuchen Sie es mit einem anderen Speicherkonto.
7.  Verwenden Sie die **URL** im AzCopy-Befehl als Ziel.

Wann sollte einem Azure Marketplace-Image der Vorzug gegenüber einem eigenen, benutzerdefinierten Image der Organisation gegeben werden?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Wann sollte einem Azure Marketplace-Image der Vorzug gegenüber einem eigenen, benutzerdefinierten Image der Organisation gegeben werden?
Azure Marketplace sollte standardmäßig verwendet werden, sofern keine bestimmten Anliegen oder die Organisation betreffenden Anforderungen bestehen. Dies sind einige häufige Beispiele:

- Eine komplexe Softwareeinrichtung, bei der eine Anwendung als Teil des Basisimages enthalten sein muss.
- Installation und Einrichtung einer Anwendung können viele Stunden dauern, und dieses Hinzufügen zu einem Azure Marketplace-Image stellt keine effiziente Nutzung der Computezeit dar.
- Entwickler und Tester benötigen schnell Zugriff auf einen virtuellen Computer und möchten die erforderliche Zeit für die Einrichtung eines neuen virtuellen Computers minimieren.
- Compliance- oder behördliche Bestimmungen (z.B. Sicherheitsrichtlinien), die für alle Computer umgesetzt werden müssen.
- Die Verwendung benutzerdefinierter Images sollte nicht auf die leichte Schulter genommen werden. Sie bringen zusätzliche Komplexität ins Spiel, da Sie sich nun um die Verwaltung der VHD-Dateien für die zugrunde liegenden Basisimages kümmern müssen. Außerdem müssen Sie die Basisimages regelmäßig mit Softwareupdates patchen. Zu diesen Updates gehören neue Betriebssystemupdates und alle Updates oder Konfigurationsänderungen, die am Softwarepaket selbst erforderlich werden.

## <a name="artifacts"></a>Artefakte

### <a name="what-are-artifacts"></a>Was sind Artefakte?
Artefakte sind anpassbare Elemente, mit denen Sie die neuesten Komponenten oder Ihre Entwicklungstools auf einer VM bereitstellen können. Fügen Sie beim Erstellen der VM Artefakte an Ihre VM an. Nach der Bereitstellung der VM wird Ihre VM durch die Artefakte bereitgestellt und konfiguriert. In unserem [öffentlichen GitHub-Repository](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) stehen verschiedene bereits vorhandene Artefakte zur Verfügung. Sie können auch [eigene Artefakte erstellen](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Für mein Artefakt ist während der VM-Erstellung ein Fehler aufgetreten. Wie kann ich das Problem beheben?
Weitere Informationen zum Abrufen von Protokollen für das fehlerhafte Artefakt finden Sie unter [Diagnostizieren von Artefaktfehlern in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Wann sollte eine Organisation ein öffentliches und wann ein privates Artefaktrepository in DevTest Labs verwenden?
Das [öffentliche Artefaktrepository](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) stellt einen Anfangssatz von Softwarepaketen bereit, die am häufigsten verwendet werden. Dies hilft bei der schnellen Bereitstellung – ohne Zeitaufwand zum Reproduzieren gängiger Entwicklertools und Add-Ins. Sie können auch Ihr eigenes privates Repository bereitstellen. Sie haben die Möglichkeit, ein öffentliches und ein privates Repository parallel zu nutzen. Das öffentliche Repository lässt sich auch deaktivieren. Beachten Sie die folgenden Fragen und Überlegungen, bevor Sie ein privates Repository bereitstellen:

- Muss die Organisation im Rahmen ihres DevTest Labs-Angebots lizenzierte Unternehmenssoftware verwenden? Falls ja, sollte ein privates Repository erstellt werden.
- Entwickelt die Organisation benutzerdefinierte Software, die einen bestimmten Vorgang bereitstellt, der im Rahmen des gesamten Bereitstellungsprozesses erforderlich ist? Falls ja, sollte ein privates Repository bereitgestellt werden.
- Wenn die Governancerichtlinie der Organisation eine Isolierung erfordert und externe Repositorys nicht direkt der Konfigurationsverwaltung durch die Organisation unterliegen, sollte ein privates Artefaktrepository bereitgestellt werden. Im Rahmen dieses Prozesses kann eine Erstkopie des öffentlichen Repositorys kopiert und in das private Repository integriert werden. Dann kann das öffentliche Repository deaktiviert werden, sodass niemand mehr innerhalb der Organisation darauf zugreifen kann. Dieser Ansatz bewirkt, dass alle Benutzer innerhalb der Organisation nur über ein einziges Repository verfügen, das von der Organisation genehmigt wird, und Konfigurationsabweichungen minimiert werden.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Sollte eine Organisation ein einzelnes Repository planen oder mehrere Repositorys zulassen?
Im Rahmen der allgemeinen Governance- und Konfigurationsverwaltungsstrategie Ihrer Organisation wird empfohlen, ein zentrales Repository zu verwenden. Wenn Sie mehrere Repositorys verwenden, können diese im Laufe der Zeit zu Silos nicht verwalteter Software werden. Mit einem zentralen Repository können mehrere Teams Artefakte aus diesem Repository für ihre Projekte verwenden. Es bewirkt Standardisierung, Sicherheit sowie einfache Verwaltung und vermeidet doppelten Aufwand. Im Rahmen der Zentralisierung werden die folgenden Aktionen zur langfristigen Verwaltung und Nachhaltigkeit empfohlen:

- Ordnen Sie die Azure Repos demselben Azure Active Directory-Mandanten zu, den das Azure-Abonnement zur Authentifizierung und Autorisierung verwendet.
- Erstellen Sie in Azure Active Directory eine Gruppe mit dem Namen `All DevTest Labs Developers`, die zentral verwaltet wird. Jeder Entwickler, der zur Entwicklung von Artefakten beiträgt, sollte in diese Gruppe aufgenommen werden.
- Die gleiche Azure Active Directory-Gruppe kann verwendet werden, um Zugriff auf das Azure Repos-Repository und -Lab zu ermöglichen.
- In den Azure Repos sollten Verzweigungen oder Gabelungen verwendet werden, um ein Entwicklungsrepository vom primären Produktionsrepository zu trennen. Inhalte werden erst nach einem ordnungsgemäßen Code Review mit einem Pull Request dem Masterbranch hinzugefügt. Sobald der Codereviewer die Änderung genehmigt hat, führt ein leitender Entwickler, der für die Wartung des Masterbranchs verantwortlich ist, den aktualisierten Code zusammen.

## <a name="cicd-integration"></a>CI/CD-Integration

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Lässt sich DevTest Labs in meine CI/CD-Toolkette integrieren?
Wenn Sie Azure DevOps verwenden, können Sie Ihre Releasepipeline in DevTest Labs mithilfe einer [DevTest Labs-Erweiterung für Aufgaben](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) automatisieren. Mit dieser Erweiterung können Sie u.a. folgende Aufgaben durchführen:

- Automatisches Erstellen und Bereitstellen einer VM. Sie können die VM auch durch den Azure-Dateikopiervorgang oder durch PowerShell Azure DevOps Services-Aufgaben mit dem neuesten Build konfigurieren.
- Automatisches Erfassen des Zustands einer VM nach dem Testen, um zur weiteren Untersuchung einen Fehler auf derselben VM zu reproduzieren
- Löschen der VM am Ende der Releasepipeline, wenn sie nicht mehr benötigt wird

Die folgenden Blogbeiträge enthalten Anleitungen und Informationen zur Verwendung der Azure DevOps Services-Erweiterung:

- [DevTest Labs und die Azure DevOps-Erweiterung](integrate-environments-devops-pipeline.md)
- [Bereitstellen einer neuen VM in einem vorhandenen DevTest Labs-Lab über Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Verwenden der Azure DevOps Services-Releaseverwaltung für Continuous Deployments in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Für andere Continuous Integration(CI)/Continuous Delivery(CD)-Toolketten können Sie dieselben Szenarien erreichen, indem Sie [Azure Resource Manager-Vorlagen](https://azure.microsoft.com/resources/templates/) mit [Azure PowerShell-Cmdlets](../azure-resource-manager/resource-group-template-deploy.md) und [.NET SDKs](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/) bereitstellen. Sie können auch [REST-APIs für DevTest Labs](https://aka.ms/dtlrestapis) zur Integration in Ihre Toolkette verwenden.

## <a name="networking"></a>Netzwerk

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Wann sollte ich ein neues virtuelles Netzwerk für meine DevTest Labs-Umgebung erstellen und wann ein vorhandenes virtuelles Netzwerk nutzen?
Wenn Ihre VMs mit vorhandener Infrastruktur interagieren müssen, erwägen Sie die Verwendung eines vorhandenen virtuellen Netzwerks innerhalb Ihrer DevTest Labs-Umgebung. Wenn Sie ExpressRoute verwenden, kann es sinnvoll sein, die Menge der VNets/Subnetze zu minimieren, um die Fragmentierung Ihres IP-Adressraums zu vermeiden, der für die Verwendung in den Abonnements zugewiesen wird. 

Sie sollten hier auch die Verwendung des VNet-Peeringmusters ([Nabe-Speiche-Modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) in Erwägung ziehen. Dieser Ansatz ermöglicht die abonnementübergreifende VNet/Subnetz-Kommunikation. Andernfalls kann jede DevTest Labs-Umgebung über ein eigenen virtuelles Netzwerk verfügen. 

Die Anzahl der virtuellen Netzwerke pro Abonnement ist [begrenzt](../azure-subscription-service-limits.md). Standardmäßig sind 50 möglich, dieser Grenzwert kann jedoch auf 100 heraufgesetzt werden.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Wann sollte eine gemeinsame IP-Adresse, wann eine öffentliche, wann eine private verwendet werden?
 
Wenn Sie ein Standort-zu-Standort-VPN oder Express Route verwenden, ziehen Sie die Verwendung privater IPs in Erwägung, so dass auf Ihre Computer über Ihr internes Netzwerk zugegriffen werden kann, nicht aber aus dem öffentlichen Internet.

> [!NOTE]
> Labbesitzer können diese Subnetzrichtlinie ändern, um sicherzustellen, dass niemand versehentlich öffentliche IP-Adressen für seine VMs erstellt. Der Abonnementbesitzer sollte eine Abonnementrichtlinie einrichten, die das Erstellen öffentlicher IPs verhindert.

Bei der Verwendung gemeinsamer öffentlicher IPs teilen die virtuellen Computer in einem Lab eine öffentliche IP-Adresse. Dieser Ansatz kann nützlich sein, wenn Sie das Überschreiten des Grenzwerts für öffentliche IP-Adressen für ein bestimmtes Abonnement vermeiden müssen.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Wie kann ich sicherstellen, dass VMs für Entwicklung und Test das öffentliche Internet nicht erreichen können? Gibt es empfohlene Muster zum Einrichten der Netzwerkkonfiguration?

Ja. Zwei Aspekte müssen berücksichtigt werden: eingehender und ausgehender Datenverkehr.

- **Eingehender Datenverkehr**: Wenn der virtuelle Computer über keine öffentliche IP-Adresse verfügt, kann er vom Internet aus nicht erreicht werden. Ein gängiger Ansatz besteht darin, eine Richtlinie auf Abonnementebene festzulegen, die sicherstellt, dass kein Benutzer eine öffentliche IP-Adresse erstellen kann.
- **Ausgehender Datenverkehr**: Wenn Sie verhindern möchten, dass virtuelle Computer direkt auf das öffentliche Internet zugreifen und Datenverkehr durch eine Unternehmensfirewall senden können, können Sie den Datenverkehr lokal mithilfe von ExpressRoute oder VPN routen, indem Sie erzwungenes Routing einsetzen.

> [!NOTE]
> Wenn Sie über einen Proxyserver verfügen, der ohne Proxyeinstellungen Datenverkehr blockiert, vergessen Sie nicht, Ausnahmen für das Speicherkonto des Labs für Artefakte hinzuzufügen.

Sie können außerdem Netzwerksicherheitsgruppen für virtuelle Computer oder Subnetze verwenden. Dieser Schritt fügt eine zusätzliche Schutzschicht zum Zulassen/Blockieren von Datenverkehr hinzu.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Warum wird mein vorhandenes virtuelles Netzwerk nicht korrekt gespeichert?
Möglicherweise enthält der Name des virtuellen Netzwerks Punkte. Wenn dies der Fall ist, entfernen Sie die Punkte, oder ersetzen Sie sie durch Trennstriche. Versuchen Sie dann erneut, das virtuelle Netzwerk zu speichern.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Warum erhalte ich bei der Bereitstellung einer VM von PowerShell den Fehler „Übergeordnete Ressource wurde nicht gefunden“?
Wenn eine Ressource einer anderen übergeordnet ist, muss die übergeordnete Ressource vor dem Erstellen der untergeordneten Ressource bereits vorhanden sein. Wenn die übergeordnete Ressource nicht vorhanden ist, wird eine **ParentResourceNotFound**-Meldung angezeigt. Wenn Sie keine Abhängigkeit von der übergeordneten Ressource angeben, wird die untergeordnete Ressource möglicherweise vor der übergeordneten bereitgestellt.

VMs sind untergeordnete Ressourcen unter einem Lab in einer Ressourcengruppe. Wenn Sie Resource Manager-Vorlagen zur Bereitstellung von VMs über PowerShell verwenden, sollte der im PowerShell-Skript bereitgestellte Name der Ressourcengruppe der Name der Ressourcengruppe des Labs sein. Weitere Informationen finden Sie unter [Beheben verbreiteter Azure-Bereitstellungsfehler](../azure-resource-manager/resource-manager-common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Wo finde ich weitere Fehlerinformationen zu Fehlern bei einer VM-Bereitstellung?
VM-Bereitstellungsfehler werden in Aktivitätsprotokollen erfasst. Sie finden die Aktivitätsprotokolle von virtuellen Labcomputern unter **Überwachungsprotokolle** oder **Diagnose des virtuellen Computers** auf der VM-Seite des Labs im Ressourcenmenü (die Seite wird angezeigt, nachdem Sie die VM in der Liste „Meine virtuellen Computer“ ausgewählt haben).

Gelegentlich tritt der Bereitstellungsfehler vor Beginn der VM-Bereitstellung auf. Dies ist beispielsweise der Fall, wenn das Abonnementlimit für eine Ressource, die mit der VM erstellt wurde, überschritten wird. In diesem Fall werden die Fehlerdetails in den Aktivitätsprotokollen auf Labebene erfasst. Aktivitätsprotokolle befinden sich im unteren Bereich der Einstellungen von **Konfiguration und Richtlinien**. Weitere Informationen zum Verwenden von Aktivitätsprotokollen in finden Sie unter [Anzeigen von Aktivitätsprotokollen, um Aktionen an Ressourcen zu überwachen](../azure-resource-manager/resource-group-audit.md).




