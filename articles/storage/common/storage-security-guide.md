---
title: Azure Storage-Sicherheitsleitfaden | Microsoft Docs
description: Details der vielen Methoden zum Schützen von Azure Storage, einschließlich, aber nicht beschränkt auf RBAC, Storage Service Encryption, clientseitige Verschlüsselung, SMB 3.0 und Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3d5bfa2426d58fa5a09d2203272536eec7fa9c55
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789957"
---
# <a name="azure-storage-security-guide"></a>Azure Storage-Sicherheitsleitfaden

Azure Storage bietet umfassende Sicherheitsfunktionen, die Entwicklern das Erstellen sicherer Anwendungen ermöglichen:

- Alle Daten (einschließlich Metadaten) werden mit [Storage Service Encryption (SSE)](storage-service-encryption.md) automatisch verschlüsselt, wenn sie in Azure Storage geschrieben werden. Weitere Informationen finden Sie unter [Announcing Default Encryption for Azure Blobs, Files, Table and Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/) (Ankündigung: Standardverschlüsselung für Azure Blobs, Files, Table und Queue Storage).
- Azure Active Directory (Azure AD) und die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) werden für Azure Storage sowohl für Ressourcenverwaltungsvorgänge als auch für Datenvorgänge wie folgt unterstützt:   
    - Sie können RBAC-Rollen, die auf das Speicherkonto beschränkt sind, Dienstprinzipalen zuweisen und Azure AD verwenden, um Ressourcenverwaltungsvorgänge, z.B. die Schlüsselverwaltung, zu autorisieren.
    - Die Azure AD-Integration wird für Datenvorgänge in Blobs und Warteschlangen unterstützt. Sie können RBAC-Rollen, die auf ein Abonnement, eine Ressourcengruppe, ein Speicherkonto oder einen einzelnen Container oder eine Warteschlange bezogen sind, einem Sicherheitsprinzipal oder einer verwalteten Identität für Azure-Ressourcen zuweisen. Weitere Informationen finden Sie unter [Authentifizieren des Zugriffs auf Azure Storage mit Azure Active Directory](storage-auth-aad.md).   
- Daten können während der Übertragung zwischen einer Anwendung und Azure mit [clientseitiger Verschlüsselung](../storage-client-side-encryption.md), HTTPS oder SMB 3.0 geschützt werden.  
- Betriebssystemdatenträger und sonstige Datenträger, die von virtuellen Azure-Computern verwendet werden, können mit [Azure Disk Encryption](../../security/azure-security-disk-encryption.md) verschlüsselt werden. 
- Delegierter Zugriff auf die Datenobjekte in Azure Storage kann mit [Shared Access Signatures](../storage-dotnet-shared-access-signature-part-1.md)erteilt werden.

Dieser Artikel bietet eine Übersicht über alle Sicherheitsfunktionen, die mit Azure Storage verwendet werden können. Links führen Sie zu Artikeln, die weitere Informationen zu den einzelnen Funktionen enthalten. So können Sie Ihre Kenntnisse zu jedem Thema problemlos vertiefen.

Folgende Themen werden in diesem Artikel abgedeckt:

* [Sicherheit auf Verwaltungsebene](#management-plane-security) – Sichern Ihres Speicherkontos

  Die Verwaltungsebene besteht aus den Ressourcen, die zum Verwalten Ihres Speicherkonto verwendet werden. In diesem Abschnitt erfahren Sie mehr über das Azure Resource Manager-Bereitstellungsmodell und wie Sie mit der rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) den Zugriff auf Ihre Speicherkonten verwalten. Zudem erfahren Sie mehr über die Verwaltung Ihrer Speicherkontoschlüssel und deren erneute Generierung.
* [Sicherheit auf Datenebene](#data-plane-security) – Sichern des Zugriffs auf Ihre Daten

  In diesem Abschnitt betrachten wir die Gewährung des Zugriffs auf die tatsächlichen Datenobjekte in Ihrem Speicherkonto, z.B. Blobs, Dateien, Warteschlangen und Tabellen mit SAS und gespeicherten Zugriffsrichtlinien. Wir betrachten SAS sowohl auf Dienst- als auch auf Kontoebene. Wir behandeln auch die Beschränkung des Zugriffs auf eine bestimmte IP-Adresse (oder einen Bereich von IP-Adressen), die Beschränkung des verwendeten HTTPS-Protokolls und das Widerrufen einer SAS, ohne ihren Ablauf abzuwarten.
* [Verschlüsselung während der Übertragung](#encryption-in-transit)

  In diesem Abschnitt wird erläutert, wie Sie Daten sichern, wenn Sie sie in oder aus Azure Storage übertragen. Wir behandeln die empfohlene Verwendung von HTTPS und die Verschlüsselung, die von SMB 3.0 für Azure-Dateifreigaben verwendet wird. Wir werfen auch einen Blick auf die clientseitige Verschlüsselung, mit der Sie die Daten verschlüsseln können, bevor sie in einer Clientanwendung in den Speicher übertragen werden, und nach der Übertragung aus dem Speicher entschlüsseln können.
* [Verschlüsselung ruhender Daten](#encryption-at-rest)

  Zu den Themen zählt Storage Service Encryption (SSE). Dieses Feature ist jetzt automatisch für neue und vorhandene Speicherkonten aktiviert. Außerdem erläutern wie die Verwendung von Azure Disk Encryption und untersuchen die grundlegenden Anwendungsfälle von Azure Disk Encryption, SSE und clientseitiger Verschlüsselung sowie deren wesentlichen Unterschiede. Wir betrachten kurz die FIPS-Konformität für die US- Regierungscomputer.
* Verwenden der [Speicheranalyse](#storage-analytics) zum Überwachen des Zugriffs auf Azure Storage

  Dieser Abschnitt beschreibt, wie Sie in den Speicheranalyseprotokollen Informationen für eine Anforderung suchen. Wir betrachten reale Speicheranalyse-Protokolldaten und sehen, wie wir unterscheiden können, ob eine Anforderung mit dem Speicherkontoschlüssel, einer Shared Access Signature oder anonym erfolgt ist, und ob sie erfolgreich war oder nicht.
* [Aktivieren browserbasierter Clients über CORS](#cross-origin-resource-sharing-cors)

  Dieser Abschnitt behandelt, wie Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) ermöglicht wird. Wir sprechen über domänenübergreifenden Zugriff und wie er mit den in Azure Storage integrierten CORS-Funktionen durchgeführt wird.

## <a name="management-plane-security"></a>Sicherheit auf Verwaltungsebene
Auf der Verwaltungsebene finden Vorgänge statt, die sich auf das Speicherkonto selbst auswirken. Angenommen, Sie können ein Speicherkonto erstellen oder löschen, eine Liste von Speicherkonten in einem Abonnement abrufen, die Speicherkontoschlüssel abrufen oder die Speicherkontoschlüssel erneut generieren.

Wenn Sie ein neues Speicherkonto erstellen, wählen Sie ein Modell zur Bereitstellung – das klassische Modell oder das des Resource Manager. Das klassische Modell der Ressourcenerstellen in Azure lässt nur den uneingeschränkten Zugriff auf das Abonnement und damit auf das Speicherkonto zu.

Dieser Leitfaden konzentriert sich auf das Resource Manager-Modell, die empfohlene Methode zum Erstellen von Speicherkonten. Mit den Resource Manager-Speicherkonten können Sie, anstatt Zugriff auf das gesamte Abonnement zu gewähren, den Zugriff auf die Verwaltungsebene mit der rollenbasierten Zugriffssteuerung präziser steuern.

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Sichern Ihres Speicherkontos mit rollenbasierter Zugriffssteuerung (RBAC)
Was ist RBAC, und wie können Sie sie verwenden? Jedes Azure-Abonnement hat ein Azure Active Directory. Benutzern, Gruppen und Anwendungen aus diesem Verzeichnis kann Verwaltungszugriff auf Ressourcen im zugehörigen Azure-Abonnement gewährt werden, die das Resource Manager-Bereitstellungsmodell verwenden. Dieser Sicherheitstyp wird als rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) bezeichnet. Um diesen Zugriff zu verwalten, können Sie das [Azure-Portal](https://portal.azure.com/), die [Tools der Azure-Befehlszeilenschnittstelle](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) oder die [REST-APIs des Azure Storage-Ressourcenanbieters](https://msdn.microsoft.com/library/azure/mt163683.aspx) verwenden.

Mit dem Resource Manager-Modell platzieren Sie das Speicherkonto in einer Ressourcengruppe und steuern den Zugriff auf die Verwaltungsebene dieses bestimmten Speicherkontos mithilfe von Active Directory. Beispielsweise können Sie bestimmten Benutzern den Zugriff auf die Speicherkontoschlüssel gewähren, während andere Benutzer Informationen über das Speicherkonto anzeigen, jedoch nicht auf die Speicherkontoschlüssel zugreifen können.

#### <a name="granting-access"></a>Gewähren von Zugriff
Der Zugriff wird gewährt, indem Benutzern, Gruppen und Anwendungen die jeweils geeignete RBAC-Rolle auf der richtigen Ebene zugewiesen wird. Um Zugriff auf das gesamten Abonnement zu gewähren, weisen Sie eine Rolle auf Abonnementebene zu. Sie können den Zugriff auf alle Ressourcen in einer Ressourcengruppe gewähren, indem Sie Berechtigungen für die Ressourcengruppe selbst gewähren. Sie können auch bestimmten Ressourcen, z. B. Speicherkonten, bestimmte Rollen zuweisen.

Hier sind die wichtigsten Punkte, die Sie über die Verwendung von RBAC für den Zugriff auf die Verwaltungsvorgänge eines Azure Storage-Kontos wissen müssen:

* Wenn Sie Zugriff gewähren, weisen Sie im Grunde dem Konto, das Zugriff haben soll, eine Rolle zu. Sie können den Zugriff auf die Vorgänge, mit denen dieses Speicherkonto verwaltet wird, jedoch nicht auf die Datenobjekte im Konto steuern. Sie können z. B. die Berechtigung zum Abrufen der Eigenschaften des Speicherkontos (z. B. Redundanz) gewähren, aber keine Berechtigung für einen Container oder Daten in einem Container in Blob Storage.
* Um einer Person die Berechtigung zum Zugriff auf die Datenobjekte im Speicherkonto zu gewähren, können Sie ihr die Berechtigung zum Lesen der Speicherkontoschlüssel gewähren, und dieser Benutzer kann dann mit diesen Schlüsseln auf die Blobs, Warteschlangen, Tabellen und Dateien zugreifen.
* Rollen können einem bestimmten Benutzerkonto, einer Gruppe von Benutzern oder einer bestimmten Anwendung zugewiesen werden.
* Jede Rolle verfügt über eine Liste zulässiger Aktionen und nicht zulässiger Aktionen. Beispielsweise verfügt die Rolle „Mitwirkender für virtuelle Computer“ über die Aktion „listKeys“, die das Lesen der Speicherkontoschlüssel ermöglicht. Zu den nicht zulässigen Aktionen des Mitwirkenden zählt z.B. die Aktualisierung des Zugriffs für Benutzer in Active Directory.
* Zu den Rollen für Speicher zählen (unter anderem) die folgenden Rollen:

  * Besitzer – Sie können alles, einschließlich des Zugriffs, verwalten.
  * Mitwirkende – Sie können alles, was der Besitzer kann, abgesehen vom Zuweisen des Zugriffs. Benutzer mit dieser Rolle können Speicherkontoschlüssel anzeigen und erneut generieren. Mit den Speicherkontoschlüsseln können sie auf die Datenobjekte zugreifen.
  * Leser – Sie können Informationen über das Speicherkonto, mit Ausnahme geheimer Schlüssel, anzeigen. Wenn Sie z.B. einer Person eine Rolle mit Leseberechtigungen für das Speicherkonto zuweisen, kann sie die Eigenschaften des Speicherkontos anzeigen, aber nicht die Eigenschaften ändern oder den Schlüssel des Speicherkontos anzeigen.
  * Speicherkontomitwirkende – Sie können das Speicherkonto verwalten – Ressourcengruppen und Ressourcen des Abonnements lesen, außerdem Abonnementressourcengruppen-Bereitstellungen erstellen und verwalten. Sie können ebenfalls auf die Schlüssel des Speicherkontos zugreifen, was wiederum bedeutet, dass sie auf die Datenebene zugreifen können.
  * Benutzerzugriffsadministrator – Sie können den Benutzerzugriff auf das Speicherkonto verwalten. Beispielsweise können sie einem bestimmten Benutzer Leserzugriff gewähren.
  * Mitwirkende für virtuelle Computer – Sie können virtuelle Computer verwalten, jedoch nicht das Speicherkonto, mit dem sie verbunden sind. Diese Rolle kann den Schlüssel des Speicherkontos auflisten, was bedeutet, dass der Benutzer, dem Sie diese Rolle zuweisen, die Datenebene aktualisieren kann.

    Damit ein Benutzer einen virtuellen Computer erstellen kann, muss er die entsprechende VHD-Datei in einem Speicherkonto erstellen können. Zu diesem Zweck muss er den Speicherkontoschlüssel abrufen und der API übergeben können, die die VM erstellt. Daher benötigt er diese Berechtigung, damit er den Schlüssel des Speicherkontos auflisten kann.
* Die Möglichkeit zum Definieren benutzerdefinierter Rollen ist ein Feature, mit dem Sie aus einer Liste verfügbarer Aktionen einen Satz von Aktionen auswählen können, die an Azure-Ressourcen ausgeführt werden dürfen.
* Der Benutzer muss in Ihrem Azure Active Directory eingerichtet werden, damit Sie ihm eine Rolle zuweisen können.
* Sie können einen Bericht darüber erstellen, wer wem welche Art des Zugriffs – und in welchem Bereich – mit PowerShell oder der Azure-Befehlszeilenschnittstelle gewährt/entzogen hat.

#### <a name="resources"></a>Ressourcen
* [Rollenbasierte Zugriffssteuerung in Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  Dieser Artikel beschreibt die rollenbasierte Steuerung des Zugriffs auf Azure Active Directory, und wie sie funktioniert.
* [RBAC: Integrierte Rollen](../../role-based-access-control/built-in-roles.md)

  In diesem Artikel werden alle in RBAC verfügbaren integrierten Rollen ausführlich beschrieben.
* [Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung](../../azure-resource-manager/resource-manager-deployment-model.md)

  Dieser Artikel erläutert die Resource Manager-Bereitstellung sowie klassische Bereitstellungsmodelle und erläutert die Vorteile der Verwendung der Resource Manager- und Ressourcengruppen. Es wird erläutert, wie die Azure Compute-, Netzwerk- und Speicheranbieter im Resource Manager-Modell funktionieren.
* [Verwalten der rollenbasierten Zugriffssteuerung mit der REST-API](../../role-based-access-control/role-assignments-rest.md)

  Dieser Artikel beschreibt die Verwendung der REST-API zum Verwalten von RBAC.
* [Azure Storage Resource Provider REST-API-Referenz](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Diese API-Referenz beschreibt die APIs, die Sie zum programmgesteuerten Verwalten Ihres Speicherkontos verwenden können.
* [Verwenden der Resource Manager-Authentifizierungs-API für den Zugriff auf Abonnements](../../azure-resource-manager/resource-manager-api-authentication.md)

  In diesem Artikel wird die Authentifizierung mithilfe der Resource Manager-APIs gezeigt.
* [Role-Based Access Control for Microsoft Azure from Ignite (Rollenbasierte Zugriffssteuerung für Microsoft Azure über Ignite)](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Dies ist ein Link zu einem Video auf Channel 9 von der MS Ignite-Konferenz 2015. Thema dieser Sitzung sind die Zugriffsverwaltungs- und Berichtsfunktionen in Azure und die Untersuchung bewährter Verfahren für das Sichern des Zugriffs auf Azure-Abonnements mit Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Verwalten Ihres Speicherkontoschlüssels
Speicherkontoschlüssel sind von Azure erstellte 512-Bit-Zeichenfolgen, die zusammen mit dem Speicherkontonamen für den Zugriff auf die im Speicherkonto gespeicherten Datenobjekte, z.B. Blobs, Entitäten innerhalb einer Tabelle, Warteschlangennachrichten und Dateien in einer Azure-Dateifreigabe verwendet werden können. Mit dem Steuern des Zugriffs auf die Speicherkontoschlüssel wird der Zugriff auf die Datenebene für das Speicherkonto gesteuert.

Jedes Speicherkonto verfügt über zwei Schlüssel, die im [Azure-Portal](https://portal.azure.com/) und in den PowerShell-Cmdlets „Key 1“ und „Key 2“ genannt werden. Diese können mit verschiedenen Methoden manuell erneut generiert werden, beispielsweise im [Azure-Portal](https://portal.azure.com/), mithilfe von PowerShell oder über die Azure-Befehlszeilenschnittstelle. Die Neugenerierung kann auch programmgesteuert mithilfe der .NET-Speicherclientbibliothek bzw. der REST-API der Azure Storage-Dienste erfolgen.

Es gibt verschiedene Gründe, Ihre Speicherkontoschlüssel erneut zu generieren.

* Möglicherweise generieren Sie sie in regelmäßigen Abständen aus Sicherheitsgründen erneut.
* Sie würden Ihre Speicherkontoschlüssel z. B. dann erneut generieren, wenn jemand eine Anwendung gehackt und den Schlüssel abgerufen hat, der hartcodiert oder in einer Konfigurationsdatei gespeichert war, sodass er jetzt vollständigen Zugriff auf Ihr Speicherkonto hat.
* Ein weiterer Fall für die Neugenerierung des Schlüssels ist, wenn Ihr Team eine Storage-Explorer-Anwendung verwendet, die den Speicherkontoschlüssel beibehält, und eines der Mitglieder verlässt das Team. Die Anwendung würde nach seinem Ausscheiden auch weiterhin funktionieren und ihm den Zugriff auf Ihr Speicherkonto gewähren. Dies ist tatsächlich der Hauptgrund dafür, SAS auf Kontoebene zu erstellen – Sie können SAS auf Kontoebene verwenden, statt die Zugriffsschlüssel in einer Konfigurationsdatei zu speichern.

#### <a name="key-regeneration-plan"></a>Plan zur erneuten Schlüsselgenerierung
Sie sollten den Schlüssel, den Sie verwenden, nicht ohne Planung erneut generieren. Andernfalls besteht die Gefahr, dass Sie jeglichen Zugriff auf dieses Speicherkonto abschneiden und so eine größere Unterbrechung verursachen. Darum gibt es zwei Schlüssel. Sie sollten jeweils nur einen Schlüssel erneut generieren.

Bevor Sie Ihre Schlüssel erneut generieren, halten Sie eine Liste aller Anwendungen bereit, die auf das Speicherkonto angewiesen sind, sowie aller anderen Dienste, die Sie in Azure verwenden. Falls Sie z.B. Azure Media Services verwenden, die auf Ihr Speicherkonto angewiesen sind, müssen Sie die Zugriffsschlüssel nach dem erneuten Generieren der Schlüssel mit Ihrem Mediendienst erneut synchronisieren. Wenn Sie Anwendungen wie z. B. einen Storage-Explorer verwenden, müssen Sie die neuen Schlüssel diesen Anwendungen ebenfalls bereitstellen. Wenn Sie über VMs verfügen, deren VHD-Dateien im Speicherkonto gespeichert werden, bleiben sie vom erneuten Generieren der Speicherkontoschlüssel unbeeinflusst.

Sie können Ihre Schlüssel im Azure-Portal erneut generieren. Nachdem Schlüssel neu generiert wurden, kann es bis zu 10 Minuten dauern, bis sie in den Speicherdiensten synchronisiert sind.

Wenn Sie so weit sind, sollten Sie Ihre Schlüssel mit dem im Folgenden beschriebenen allgemeinen Verfahren ändern. In diesem Fall wird davon ausgegangen, dass Sie derzeit „Key 1“ verwenden, und Sie stattdessen alles zur Verwendung von „Key 2“ ändern möchten.

1. Generieren Sie „Key 2“ erneut, um sicherzustellen, dass es sicher ist. Dies kann im Azure-Portal erfolgen.
2. Ändern Sie in allen Anwendungen, in denen der Speicherschlüssel gespeichert ist, den Speicherschlüssel zur Verwendung des neuen Werts von „Key 2“. Testen und veröffentlichen Sie die Anwendung.
3. Wenn alle Anwendungen und Dienste erfolgreich ausgeführt werden, generieren Sie „Key 1“ erneut. Dadurch wird sichergestellt, dass jeder, dem Sie nicht ausdrücklich den neuen Schlüssel gegeben haben, nicht mehr auf das Speicherkonto zugreifen kann.

Wenn Sie derzeit „Key 2“ verwenden, verwenden Sie dieselbe Vorgehensweise, aber mit umgekehrten Schlüsselnamen.

Sie können ein paar Tage lang migrieren und jede Anwendung so ändern, dass sie den neuen Schlüssels verwendet und veröffentlicht. Anschließend sollten Sie den alten Schlüssel erneut generieren, damit er nicht mehr funktioniert.

Eine weitere Option ist, dass Sie den Speicherkontoschlüssel als geheimen Schlüssel in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) platzieren und Ihre Anwendungen den Schlüssel von dort abrufen lassen. Wenn Sie den Schlüssel dann erneut generieren und Azure Key Vault aktualisieren, müssen die Anwendungen nicht erneut bereitgestellt werden, da sie den neuen Schlüssel automatisch aus Azure Key Vault abrufen. Beachten Sie, dass Sie festlegen können, dass die Anwendung den Schlüssel jedes Mal liest, wenn Sie ihn benötigen, oder Sie können ihn im Arbeitsspeicher zwischenspeichern, und wenn bei seiner Verwendung ein Fehler auftritt, den Schlüssel erneut aus Azure Key Vault abrufen.

Die Verwendung von Azure Key Vault stellt auch eine weitere Sicherheitsstufe für Ihre Speicherschlüssel dar. Wenn Sie diese Methode verwenden, benötigen Sie keinen hartcodierten Speicherschlüssel in einer Konfigurationsdatei, sodass niemand mehr ohne ausdrückliche Berechtigung Zugriff auf die Schlüssel erhalten kann.

Ein weiterer Vorteil der Verwendung von Azure Key Vault ist, dass Sie auch den Zugriff auf die Schlüssel mithilfe von Azure Active Directory steuern können. Dies bedeutet, dass Sie Zugriff auf die Reihe von Anwendungen gewähren können, die die Schlüssel aus Azure Key Vault abrufen müssen, und wissen, dass andere Anwendungen nicht auf die Schlüssel zugreifen können, ohne dass Sie ihnen die ausdrückliche Berechtigung gewähren.

> [!NOTE]
> Es wird empfohlen, in allen Ihren Anwendungen jeweils nur einen Schlüssel gleichzeitig zu verwenden. Wenn Sie „Key 1“ an einigen Stellen und „Key 2“ an anderen verwenden, können Sie die Verwendung der Schlüssel nicht wechseln, ohne dass einige Anwendungen den Zugriff verlieren.

#### <a name="resources"></a>Ressourcen

* [Verwalten von Speicherkontoeinstellungen im Azure-Portal](storage-account-manage.md)
* [Azure Storage Resource Provider REST-API-Referenz](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Sicherheit auf Datenebene
Sicherheit auf Datenebene bezieht sich auf die Methoden zum Schützen der in Azure Storage gespeicherten Datenobjekte – Blobs, Warteschlangen, Tabellen und Dateien. Wir haben Methoden zum Verschlüsseln der Daten und die Sicherheit bei der Übertragung der Daten kennengelernt, aber wie steuern Sie den Zugriff auf die Objekte?

Sie haben drei Optionen zur Autorisierung des Zugriffs auf Datenobjekte in Azure Storage:

- Verwenden Sie Azure AD, um den Zugriff auf Container und Warteschlangen zu autorisieren. Azure AD bietet gegenüber anderen Ansätzen zur Autorisierung Vorteile, z.B. Wegfall der Speicherung von Geheimnissen in Ihrem Code. Weitere Informationen finden Sie unter [Authentifizieren des Zugriffs auf Azure Storage mit Azure Active Directory](storage-auth-aad.md). 
- Verwenden Sie Ihre Speicherkontoschlüssel, um den Zugriff per gemeinsam verwendetem Schlüssel zu autorisieren. Für die Autorisierung per gemeinsam verwendetem Schlüssel ist das Speichern Ihrer Speicherkontoschlüssel in Ihrer Anwendung erforderlich, und Microsoft empfiehlt stattdessen nach Möglichkeit die Nutzung von Azure AD.
- Verwenden Sie Shared Access Signatures, um bestimmten Datenobjekten für einen bestimmten Zeitraum kontrollierte Berechtigungen zu gewähren.

Für Blob Storage können Sie öffentlichen Zugriff auf Ihre Blobs zulassen, indem Sie die Zugriffsebene für den Container, der die Blobs enthält, entsprechend festlegen. Wenn Sie den Zugriff für einen Container auf Blob oder Container festlegen, entspricht dies öffentlichem Lesezugriff auf die Blobs im Container. Dies bedeutet, dass jeder Benutzer, dessen URL auf ein Blob in diesem Container zeigt, es in einem Browser öffnen kann, ohne eine SAS zu verwenden oder über die Schlüssel des Speicherkontos zu verfügen.

Zusätzlich zum Beschränken des Zugriffs durch Autorisierung können Sie auch [Firewalls und virtuelle Netzwerke](storage-network-security.md) verwenden, um den Zugriff auf das Speicherkonto basierend auf Netzwerkregeln einzuschränken.  Mit diesem Ansatz können Sie den Zugriff auf öffentlichen Internetdatenverkehr verweigern und nur den Zugriff auf bestimmte virtuelle Azure-Netzwerke oder IP-Adressbereiche im öffentlichen Internet gewähren.

### <a name="storage-account-keys"></a>Speicherkontoschlüssel
Speicherkontoschlüssel sind von Azure erstellte 512-Bit-Zeichenfolgen, die zusammen mit dem Speicherkontonamen für den Zugriff auf die im Speicherkonto gespeicherten Datenobjekte verwendet werden können.

Sie können z. B. Blobs lesen, in Warteschlangen schreiben, Tabellen erstellen und Dateien ändern. Viele dieser Aktionen können über das Azure-Portal oder mithilfe einer der vielen Storage-Explorer-Anwendungen ausgeführt werden. Sie können auch Code zum Verwenden der REST-API oder einer der Speicherclientbibliotheken für diese Operationen schreiben.

Wie im Abschnitt zur [Sicherheit auf Verwaltungsebene](#management-plane-security)beschrieben, können Sie den Zugriff auf die Speicherschlüssel für ein klassisches Speicherkonto gewähren, indem Sie Vollzugriff auf das Azure-Abonnement gewähren. Zugriff auf die Speicherschlüssel für ein Speicherkonto mit dem Azure Resource Manager-Modell kann über rollenbasierte Zugriffssteuerung (RBAC) gesteuert werden.

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Delegieren des Zugriffs auf Objekte in Ihrem Konto mithilfe von SAS und gespeicherter Zugriffsrichtlinien
Eine Shared Access Signature ist eine Zeichenfolge mit einem Sicherheitstoken, die an einen URI angefügt werden kann. Mit diesem URI können Sie den Zugriff auf Speicherobjekte delegieren und Einschränkungen festlegen, wie z.B. Berechtigungen und den Datums- und Uhrzeitbereich für den Zugriff.

Sie können den Zugriff auf Blobs, Container, Warteschlangennachrichten, Dateien und Tabellen gewähren. Für Tabellen können Sie die Berechtigung zum Zugriff auf eine Reihe von Entitäten in der Tabelle gewähren, indem Sie die Partitions- und Zeilenschlüsselbereiche angeben, auf die der Benutzer Zugriff haben soll. Beispiel: Wenn Sie Daten mit einem Partitionsschlüssel gespeichert haben, der geografischen Status hat, könnten Sie einer Person ausschließlich Zugriff auf die Daten für Kalifornien gewähren.

In einem anderen Beispiel könnten Sie einer Webanwendung ein SAS-Token übergeben, das ihr ermöglicht, Einträge in eine Warteschlange zu schreiben, und einer Workerrollenanwendung ein SAS-Token zum Abrufen von Nachrichten aus der Warteschlange und deren Verarbeitung übergeben. Oder Sie könnten einem Kunde ein SAS-Token übergeben, um Bilder in einen Container im Blobspeicher hochzuladen, und einer Webanwendung die Berechtigung gewähren, diese Bilder zu lesen. In beiden Fällen liegt eine Trennung der Belange vor: Jede Anwendung kann genau den Zugriff erhalten, den sie benötigt, um ihre Aufgabe auszuführen. Dies macht die Verwendung von Shared Access Signatures möglich.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Gründe für die Verwendung von Shared Access Signatures?
Warum sollten Sie eine SAS verwenden, anstatt einfach Ihren Speicherkontoschlüssel zu übergeben, was doch viel einfacher ist? Wenn Sie den Schlüssel Ihres Speicherkontos übergeben, ist dies so, als würden Sie die Schlüssel zu Ihrem „Speicherreich“ freigeben. Sie gewähren damit vollständigen Zugriff. Jemand könnte Ihre Schlüssel verwenden und seine gesamte Musikbibliothek in Ihr Speicherkonto hochladen. Er könnte auch Ihre Dateien durch virusinfizierte Versionen ersetzen oder Ihre Daten stehlen. Die Vergabe von uneingeschränktem Zugriff auf Ihr Speicherkonto dürfen Sie nicht auf die leichte Schulter nehmen.

Mit Shared Access Signatures können Sie einem Client genau die erforderlichen Berechtigungen für einen begrenzten Zeitraum gewähren. Wenn z. B. jemand ein Blob auf Ihr Konto hochlädt, können Sie ihm genau für die zum Hochladen des Blobs benötigte Zeit Schreibzugriff gewähren (natürlich der Größe des Blobs entsprechend). Und wenn Sie Ihre Meinung ändern, können Sie die Zugriffsberechtigung widerrufen.

Darüber hinaus können Sie angeben, dass Anforderungen, die mit einer SAS erfolgen, auf eine bestimmte IP-Adresse oder einen bestimmten IP-Adressbereich außerhalb von Azure beschränkt sind. Sie können auch fordern, dass die Anforderungen mit einem bestimmten Protokoll erfolgen (HTTPS oder HTTP/HTTPS). Dies bedeutet: Wenn Sie nur HTTPS-Datenverkehr zulassen möchten, können Sie HTTPS als erforderliches Protokoll festlegen, sodass HTTP-Datenverkehr blockiert wird.

#### <a name="definition-of-a-shared-access-signature"></a>Definition einer Shared Access Signature
Eine Shared Access Signature ist ein Satz von Abfrageparametern, die an die URL angehängt werden, die auf die Ressource verweist,

die Informationen über den erlaubten Zugriff und die Zeitspanne bieten, für die der Zugriff zulässig ist. Der URI in diesem Beispiel bietet für fünf Minuten Lesezugriff auf ein Blob. Beachten Sie, dass die SAS-Abfrageparameter URL-codiert sein müssen, d. h. % 3A steht für den Doppelpunkt (:) oder %20 für ein Leerzeichen.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Autorisierung der Shared Access Signature durch den Azure Storage-Dienst
Wenn der Speicherdienst die Anforderung empfängt, erstellt er aufgrund der Eingabeabfrageparameter mit der gleichen Methode wie das aufrufende Programm eine Signatur. Anschließend werden die zwei Signaturen verglichen. Wenn sie übereinstimmen, kann der Speicherdienst die Speicherdienstversion prüfen, um die Gültigkeit sicherzustellen sowie festzustellen, ob aktuelles Datum und aktuelle Uhrzeit im angegebenen Fenster liegen, ob der angeforderte Zugriff der Anforderung entspricht usw.

Würde z. B. die obige URL statt auf ein Blob auf eine Datei weisen, würde diese Anforderung fehlschlagen, weil sie angibt, dass die Shared Access Signature für ein Blob gilt. Wenn der aufgerufene REST-Befehl versuchen würde, ein Blob zu aktualisieren, würde er fehlschlagen, da die Shared Access Signature angibt, dass nur Lesezugriff zulässig ist.

#### <a name="types-of-shared-access-signatures"></a>Typen von Shared Access Signatures
* Eine SAS auf Dienstebene kann für den Zugriff auf bestimmte Ressourcen in einem Speicherkonto verwendet werden. Einige Beispiele hierfür sind das Abrufen einer Liste der Blobs in einem Container, das Herunterladen eines Blobs, das Aktualisieren einer Entität in einer Tabelle, das Hinzufügen von Nachrichten zu einer Warteschlange oder das Hochladen einer Datei in eine Dateifreigabe.
* Eine SAS auf Kontoebene kann für den Zugriff auf alles verwendet werden, für das eine SAS auf Dienstebene verwendet werden kann. Darüber hinaus kann sie Optionen für Ressourcen bieten, für die mit SAS auf Dienstebene keine Berechtigung besteht, z. B. die Fähigkeit zum Erstellen von Containern, Tabellen, Warteschlangen und Dateifreigaben. Sie können auch den gleichzeitigen Zugriff auf mehrere Dienste angeben. Beispielsweise könnten Sie jemand Zugriff sowohl auf Blobs als auch Dateien in Ihrem Speicherkonto gewähren.

#### <a name="creating-a-sas-uri"></a>Erstellen eines SAS-URIs
1. Sie können einen URI bei Bedarf erstellen und dabei jedes Mal alle Abfrageparameter definieren.

   Mit diesem Ansatz sind Sie flexibel, aber wenn Sie jedes Mal einen logischen Satz gleicher Parameter haben, sollten Sie besser eine SAS-Richtlinie verwenden.
2. Sie können eine gespeicherte Zugriffsrichtlinie für ganze Container, Dateifreigaben, Tabellen oder Warteschlangen erstellen. Dann können Sie diese als Grundlage für die SAS-URIs verwenden, die Sie erstellen. Auf gespeicherten Zugriffsrichtlinien basierende Berechtigungen können leicht widerrufen werden. Pro Container, Warteschlange, Tabelle oder Dateifreigabe können Sie bis zu fünf Richtlinien definieren.

   Wenn z.B. viele Personen die Blobs in einem bestimmten Container lesen, könnten Sie eine gespeicherte Zugriffsrichtlinie mit der Aussage „Lesezugriff gewähren“ und beliebigen anderen Einstellungen erstellen, die jedes Mal gleich sind. Anschließend können Sie mithilfe der Einstellungen der gespeicherten Zugriffsrichtlinie einen SAS-URI erstellen und das Datum und die Uhrzeit des Ablaufs angeben. Dies hat den Vorteil, dass Sie nicht jedes Mal alle Abfrageparameter angeben müssen.

#### <a name="revocation"></a>Widerruf
Stellen Sie sich vor, dass Ihre SAS kompromittiert wurde, oder Sie sie aus Gründen der Unternehmenssicherheit oder wegen gesetzlicher Vorschriften und Richtlinien ändern möchten. Wie widerrufen Sie den Zugriff auf eine Ressource, die diese SAS verwendet? Das hängt davon ab, wie Sie den SAS-URI erstellt haben.

Wenn Sie Ad-hoc-URIs verwenden, können Sie zwischen drei Optionen wählen. Sie können SAS-Token mit kurzen Ablaufrichtlinien ausgeben und warten, bis die SAS abläuft. Sie können die Ressource umbenennen oder löschen (sofern das Token für ein einzelnes Objekt definiert wurde). Sie können die Schlüssel des Speicherkontos ändern. Diese letzte Option kann weitreichende Auswirkungen haben, je nachdem, wie viele Dienste dieses Speicherkonto verwenden, und Sie sollten dies nicht ohne ausreichende Planung tun.

Wenn Sie eine SAS verwenden, die von einer gespeicherten Zugriffsrichtlinie abgeleitet ist, können Sie die Zugriffsrechte durch Widerrufen der gespeicherten Zugriffsrichtlinie aufheben – Sie können sie einfach so ändern, dass sie bereits abgelaufen ist, oder sie vollständig entfernen. Dies wird sofort wirksam und erklärt jede mithilfe dieser gespeicherten Zugriffsrichtlinie erstellte SAS für ungültig. Aktualisieren oder Entfernen der gespeicherten Zugriffsrichtlinie kann möglicherweise Konsequenzen für die Personen haben, die auf bestimmte Container, Dateifreigaben, Tabellen oder Warteschlangen über SAS zugreifen, doch wenn die Clients angeschrieben werden, sodass sie eine neue SAS anfordern, wenn die alte ungültig ist, funktioniert dies hervorragend.

Da Sie bei Verwendung einer SAS, die von einer gespeicherten Zugriffsrichtlinie abgeleitet ist, die SAS sofort widerrufen können, sollten Sie nach Möglichkeit immer die gespeicherte Zugriffsrichtlinie verwenden.

#### <a name="resources"></a>Ressourcen
Weitere ausführliche Informationen zur Verwendung von SAS und gespeicherten Zugriffsrichtlinien mit Beispielen finden Sie in den folgenden Artikeln:

* Dies sind die Referenzartikel.

  * [Beispiele für SAS (Shared Access Signatures)](https://msdn.microsoft.com/library/dn140256.aspx)

    Dieser Artikel enthält Beispiele für die Verwendung einer Dienstebenen-SAS mit Blobs, Warteschlangennachrichten, Tabellenbereichen und Dateien.
  * [Constructing a service SAS (Erstellen einer Dienstebenen-SAS)](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Constructing an account SAS (Erstellen einer Kontoebenen-SAS)](https://msdn.microsoft.com/library/mt584140.aspx)

* Dies ist ein Tutorial für die Verwendung der .NET-Clientbibliothek zum Erstellen von SAS und gespeicherten Zugriffsrichtlinien.
  * [Verwenden von Shared Access Signatures (SAS)](../storage-dotnet-shared-access-signature-part-1.md)

    Dieser Artikel enthält eine Erläuterung des SAS-Modells, Beispiele für SAS und Empfehlungen bewährter Methoden für die SAS-Verwendung. Auch der Widerruf der Berechtigung wird hier erörtert.

* Authentication

  * [Authentifizierung für Azure Storage-Dienste](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Erste-Schritte-Tutorial für Shared Access Signatures

  * [SAS-Erste-Schritte-Tutorial](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung
### <a name="transport-level-encryption--using-https"></a>Verschlüsselung auf Transportebene – mithilfe von HTTPS
Ein weiterer Schritt, mit dem Sie die Sicherheit Ihrer Azure Storage-Daten sicherstellen sollten, ist das Verschlüsseln der Daten bei der Übertragung zwischen dem Client und Azure Storage. Erstens sollten Sie immer das [HTTPS](https://en.wikipedia.org/wiki/HTTPS) -Protokoll verwenden, denn es gewährleistet die sichere Kommunikation über das öffentliche Internet.

Beim Abrufen von REST-APIs oder Zugreifen auf Objekte im Speicher sollten Sie immer HTTPS verwenden, um einen sicheren Kommunikationskanal zu gewährleisten. Mit **Shared Access Signatures**, die zum Delegieren des Zugriffs auf Azure Storage-Objekte verwendet werden können, können Sie außerdem festlegen, dass bei Verwendung von Shared Access Signatures nur das HTTPS-Protokoll verwendet werden darf. So können Sie sicherstellen, dass jeder, der Links mit SAS-Token sendet, das richtige Protokoll verwendet.

Sie können die Verwendung von HTTPS beim Aufruf von REST-APIs für den Zugriff auf Objekte in Speicherkonten erzwingen, indem Sie die Option [Sichere Übertragung erforderlich](../storage-require-secure-transfer.md) aktivieren. Sobald diese Option aktiviert ist, werden Verbindungen über HTTP abgelehnt.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Verwenden der Verschlüsselung während der Übertragung mit Azure-Dateifreigaben
[Azure Files](../files/storage-files-introduction.md) unterstützt die Verschlüsselung per SMB 3.0 und mit HTTPS, wenn die Datei-REST-API verwendet wird. Bei der Bereitstellung außerhalb der Azure-Region, in der sich die Azure-Dateifreigabe befindet, z.B. lokal oder in einer anderen Azure-Region, ist immer SMB 3.0 mit Verschlüsselung erforderlich. Für SMB 2.1 wird die Verschlüsselung nicht unterstützt. Standardmäßig sind Verbindungen in Azure daher nur innerhalb derselben Region zulässig, aber SMB 3.0 mit Verschlüsselung kann erzwungen werden, indem für das Speicherkonto [die sichere Übertragung obligatorisch gemacht wird](../storage-require-secure-transfer.md).

SMB 3.0 mit Verschlüsselung ist in [allen unterstützten Windows- und Windows Server-Betriebssystemen](../files/storage-how-to-use-files-windows.md) verfügbar – mit Ausnahme von Windows 7 und Windows Server 2008 R2, die nur SMB 2.1 unterstützen. SMB 3.0 wird auch für [macOS](../files/storage-how-to-use-files-mac.md) und [Linux](../files/storage-how-to-use-files-linux.md)-Distributionen mit Linux-Kernel 4.11 und höher unterstützt. Die Verschlüsselungsunterstützung für SMB 3.0 wurde für mehrere Linux-Distributionen auch auf ältere Versionen des Linux-Kernels zurückportiert. Informationen hierzu finden Sie unter [Grundlegendes zu SMB-Clientanforderungen](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Verwenden der clientseitigen Verschlüsselung zum Schützen von Daten, die Sie an den Speicher senden
Eine weitere Option, die Ihnen hilft, zu gewährleisten, dass Ihre Daten beim Übertragen zwischen einer Clientanwendung und dem Speicher sicher sind, ist die clientseitige Verschlüsselung. Die Daten werden verschlüsselt, bevor sie in Azure Storage übertragen werden. Beim Abrufen der Daten aus Azure Storage werden die Daten entschlüsselt, nachdem der Client sie empfangen hat. Obwohl die Daten verschlüsselt gesendet werden, sollten Sie wegen der integrierten Datenintegritätsprüfung HTTPS verwenden, um das Risiko zu reduzieren, dass Netzwerkfehler die Integrität der Daten beeinträchtigen.

Die clientseitige Verschlüsselung ist auch eine Methode zum Verschlüsseln ruhender Daten, da die Daten in verschlüsselter Form gespeichert werden. Dies behandeln wir im Abschnitt [Verschlüsselung ruhender Daten](#encryption-at-rest) noch ausführlicher.

## <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten
Drei Azure-Funktionen ermöglichen die Verschlüsselung ruhender Daten. Mit Azure Disk Encryption werden Betriebssystemdatenträger und sonstige Datenträger in virtuellen IaaS-Computern verschlüsselt. Die clientseitige Verschlüsselung und SSE werden zum Verschlüsseln von Daten in Azure Storage verwendet. 

Obwohl Sie die clientseitige Verschlüsselung zum Verschlüsseln der Daten (die auch in verschlüsselter Form in Azure Storage gespeichert werden) während der Übertragung verwenden können, sollten Sie während der Übertragung HTTPS verwenden und eine Möglichkeit zur automatischen Verschlüsselung der Daten bei der Speicherung einsetzen. Hierzu gibt es zwei Möglichkeiten – Azure Disk Encryption und SSE. Eine wird verwendet, um die Daten auf Betriebssystemdatenträgern und anderen Datenträgern, die von VMs verwendet werden, direkt zu verschlüsseln, und die andere dient zum Verschlüsseln von Daten, die in Azure Blob Storage geschrieben werden.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE ist für alle Speicherkonten aktiviert und kann nicht deaktiviert werden. SSE verschlüsselt Ihre Daten beim Schreiben in Azure Storage automatisch. Wenn Sie Daten aus Azure Storage lesen, werden sie von Azure Storage vor der Rückgabe entschlüsselt. Mit SSE können Sie Daten schützen, ohne dass Sie in Anwendungen Code ändern oder hinzufügen müssen.

Sie können von Microsoft verwaltete Schlüssel oder eigene benutzerdefinierte Schlüssel verwenden. Microsoft generiert verwaltete Schlüssel und verwaltet die sichere Speicherung der Schlüssel sowie die reguläre Rotation gemäß der internen Microsoft-Richtlinie. Weitere Informationen zur Verwendung benutzerdefinierter Schlüssel finden Sie unter [Speicherdienstverschlüsselung mit vom Kunden verwalteten Schlüsseln in Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

SSE verschlüsselt die Daten automatisch in allen Leistungsebenen (Standard oder Premium), allen Bereitstellungsmodellen (Azure Resource Manager und Classic) sowie allen Azure Storage-Diensten (Blob, Queue, Table und File). 

### <a name="client-side-encryption"></a>clientseitiger Verschlüsselung
Wir erwähnten die clientseitige Verschlüsselung bei der Erörterung der Verschlüsselung der Daten während der Übertragung. Mit diesem Feature können Sie Ihre Daten in einer Clientanwendung programmgesteuert verschlüsseln, bevor Sie sie an Azure Storage senden, und nach dem Abrufen aus Azure Storage programmgesteuert entschlüsseln.

Das Feature bietet so Verschlüsselung während der Übertragung, doch ermöglicht auch die Verschlüsselung ruhender Daten. Obwohl die Daten während der Übertragung verschlüsselt werden, sollten Sie dennoch HTTPS einsetzen, um den Vorteil der integrierten Datenintegritätsprüfungen zu nutzen, die Auswirkungen von Netzwerkfehlern auf die Integrität der Daten verringern.

Ein Anwendungsbeispiel: Sie haben eine Webanwendung, die Blobs speichert und abruft, und Anwendung und Daten sollen so sicher wie möglich sein. In diesem Fall verwenden Sie die clientseitige Verschlüsselung. Der Datenverkehr zwischen dem Client und dem Azure-Blob-Dienst enthält die verschlüsselte Ressource, und niemand kann die Daten während der Übertragung interpretieren und in seinen privaten Blobs wiederherstellen.

Die clientseitige Verschlüsselung wird in Java und .NET-Speicherclientbibliotheken erstellt, die wiederum die Azure Key Vault-APIs verwenden, sodass Ihnen die Implementierung leicht fällt. Der Prozess der Datenverschlüsselung und -entschlüsselung verwendet das Umhüllungsverfahren und speichert von der Verschlüsselung in jedem Speicherobjekt verwendete Metadaten. Für Blobs z. B. werden Blobmetadaten separat gespeichert, während Warteschlangenmetadaten jeder Warteschlangennachricht hinzugefügt werden.

Für die Verschlüsselung selbst können Sie Ihre eigenen Verschlüsselungsschlüssel generieren und verwalten. Sie können auch von der Azure Storage-Clientbibliothek generierte Schlüssel verwenden oder die Schlüssel von Azure Key Vault generieren lassen. Sie können Ihre Verschlüsselungsschlüssel in Ihrem lokalen Schlüsselspeicher oder in Azure Key Vault speichern. Azure Key Vault ermöglicht Ihnen, bestimmten Benutzern mit Active Directory Zugriff auf die geheimen Schlüssel in Azure Key Vault zu gewähren. Dies bedeutet, dass nicht jeder aus Azure Key Vault lesen und die Schlüssel abrufen kann, die Sie für die clientseitige Verschlüsselung verwenden.

#### <a name="resources"></a>Ressourcen
* [Verschlüsseln und Entschlüsseln von Blobs in Microsoft Azure Storage per Azure-Schlüsseltresor](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Dieser Artikel zeigt Ihnen die Verwendung der clientseitigen Verschlüsselung mit Azure Key Vault, z. B. zum Erstellen der KEK und deren Speicherung im Tresor mit PowerShell.
* [Clientseitige Verschlüsselung und Azure Key Vault für Microsoft Azure Storage](../storage-client-side-encryption.md)

  Dieser Artikel bietet eine Erklärung der clientseitigen Verschlüsselung und enthält Beispiele für die Verwendung von Speicherclientbibliotheken zum Verschlüsseln und Entschlüsseln von Ressourcen aus den vier Speicherdiensten. Er behandelt auch Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Verwenden von Azure Disk Encryption zum Verschlüsseln von Datenträgern, die Ihre virtuellen Computer verwenden
Mit Azure Disk Encryption können Sie die von einem virtuellen IaaS-Computer verwendeten Betriebssystemdatenträger und andere Datenträger verschlüsseln. Unter Windows werden Laufwerke mit branchenüblicher BitLocker-Verschlüsselung verschlüsselt. Unter Linux werden Datenträger mit der DM-Crypt-Technologie verschlüsselt. Diese ist in Azure Key Vault integriert, damit Sie die Datenträger-Verschlüsselungsschlüssel steuern und verwalten können.

Die Lösung unterstützt die folgenden Szenarien für virtuelle IaaS-Computer, wenn sie in Microsoft Azure aktiviert sind:

* Integration in Azure Key Vault
* Virtuelle Computer im Standard-Tarif: [IaaS-VMs der Serien A, D, DS, G, GS usw.](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Aktivieren der Verschlüsselung auf virtuellen Windows- und Linux-IaaS-Computern
* Deaktivieren der Verschlüsselung auf Betriebssystem- und Datenlaufwerken für virtuelle Windows-IaaS-Computer
* Deaktivieren der Verschlüsselung auf Datenlaufwerken für virtuelle Linux-IaaS-Computer
* Aktivieren der Verschlüsselung auf virtuellen IaaS-Computern mit dem Windows-Clientbetriebssystem
* Aktivieren der Verschlüsselung auf Volumes mit Bereitstellungspfaden
* Aktivieren der Verschlüsselung auf virtuellen Linux-Computern, die mithilfe von mdadm mit Datenträgerstriping (RAID) konfiguriert sind
* Aktivieren der Verschlüsselung auf virtuellen Linux-Computern mit LVM für Datenträger
* Aktivieren der Verschlüsselung auf virtuellen Windows-Computern, die mithilfe von Speicherplätzen konfiguriert sind
* Alle öffentlichen Azure-Regionen werden unterstützt.

Die Lösung unterstützt nicht die folgenden Szenarien, Features und Technologien:

* IaaS-VMs des Basic-Tarifs
* Deaktivieren der Verschlüsselung auf Betriebssystemlaufwerken für virtuelle Linux-IaaS-Computer
* Virtuelle IaaS-Computer, die mithilfe der klassischen Methode zum Erstellen von virtuellen Computern erstellt werden
* Integration in den lokalen Schlüsselverwaltungsdienst
* Azure Files (freigegebenes Dateisystem), Network File System (NFS), dynamische Volumes und virtuelle Windows-Computer, die mit softwarebasierten RAID-Systemen konfiguriert sind


> [!NOTE]
> Die Verschlüsselung von Linux-Betriebssystemdatenträgern wird derzeit für die folgenden Linux-Distributionen unterstützt: RHEL 7.2, CentOS 7.2n, Ubuntu 16.04.
>
>

Dieses Feature stellt sicher, dass alle Daten auf den Datenträgern Ihrer virtuellen Computer in Azure Storage ruhend verschlüsselt sind.

#### <a name="resources"></a>Ressourcen
* [Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Vergleich zwischen Azure Disk Encryption, SSE und clientseitiger Verschlüsselung

#### <a name="iaas-vms-and-their-vhd-files"></a>IaaS-VMs und ihre zugehörigen VHD-Dateien

Für Datenträger, die von IaaS-VMs verwendet werden, sollten Sie Azure Disk Encryption verwenden. Beim Erstellen eines virtuellen Computers mit nicht verwalteten Datenträgern mithilfe eines Images aus dem Azure Marketplace wird eine [flache Kopie](https://en.wikipedia.org/wiki/Object_copying) in Ihrem Azure Storage-Speicherkonto ausgeführt, und er wird auch dann nicht verschlüsselt, wenn Sie SSE aktiviert haben. SSE beginnt mit der Datenverschlüsselung, sobald der virtuelle Computer erstellt wurde und das Image aktualisiert wird. Aus diesem Grund sollten Sie Azure Disk Encryption auf VMs mit nicht verwalteten Datenträgern verwenden, die mit Images aus dem Azure Marketplace erstellt werden, wenn sie vollständig verschlüsselt werden sollen. Wenn Sie einen virtuellen Computer mit verwalteten Datenträgern erstellen, verschlüsselt SSE alle Daten standardmäßig mit plattformverwalteten Schlüsseln. 

Wenn Sie eine lokale, bereits verschlüsselte VM in Azure einbringen, können Sie die Verschlüsselungsschlüssel in Azure Key Vault hochladen und für diese VM weiterhin die lokale Verschlüsselung verwenden. Azure Disk Encryption ist für dieses Szenario aktiviert.

Wenn Sie über eine lokale, noch nicht verschlüsselte VHD-Datei verfügen, können Sie sie als benutzerdefiniertes Image in den Katalog hochladen und auf ihrer Basis eine VM bereitstellen. Wenn Sie hierzu die Resource Manager-Vorlagen verwenden, können Sie festlegen, dass Azure Disk Encryption beim Starten des virtuellen Computers aktiviert wird.

Wenn Sie einen Datenträger hinzufügen und ihn auf dem virtuellen Computer einbinden, können Sie Azure Disk Encryption für diesen Datenträger aktivieren. Dieser Datenträger wird zuerst lokal verschlüsselt, und dann führt das klassische Bereitstellungsmodell einen verzögerten Schreibvorgang im Speicher aus, damit der Speicherinhalt verschlüsselt wird.

#### <a name="client-side-encryption"></a>Clientseitige Verschlüsselung
Die clientseitige Verschlüsselung ist die sicherste Methode zum Verschlüsseln Ihrer Daten, da die Daten vor der Übertragung verschlüsselt werden.  Allerdings ist es hierzu erforderlich, dass Sie Ihren Anwendungen, die den Speicher benutzen, Code hinzuzufügen, und das möchten Sie vielleicht nicht. In diesen Fällen können Sie HTTPS verwenden, um Ihre Daten während der Übertragung zu schützen. Sobald Daten Azure Storage erreichen, werden sie von SSE verschlüsselt.

Mit der clientseitigen Verschlüsselung können Sie Tabellenentitäten, Warteschlangennachrichten und Blobs verschlüsseln. 

Die clientseitige Verschlüsselung wird vollständig von der Anwendung verwaltet. Dies ist die sicherste Möglichkeit, erfordert jedoch programmgesteuerte Änderungen an Ihrer Anwendung und die Platzierung von Schlüsselverwaltungsprozessen. Nutzen Sie diese Möglichkeit, wenn Sie zusätzliche Sicherheit während der Übertragung wünschen und die gespeicherten Daten verschlüsselt werden sollen.

Die clientseitige Verschlüsselung ist eine zusätzliche Belastung des Clients. Dies müssen Sie in Ihren Skalierbarkeitsplänen berücksichtigen, insbesondere dann, wenn Sie große Datenmengen verschlüsseln und übertragen müssen.

#### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE wird von Azure Storage verwaltet. SSE sorgt nicht für die Sicherheit der Daten während der Übertragung, verschlüsselt sie jedoch beim Schreiben in Azure Storage. SSE hat keine Auswirkungen auf die Leistung von Azure Storage.

Sie können jede Art von Daten für das Speicherkonto mithilfe von SSE verschlüsseln (Blockblobs, Anfügeblobs, Seitenblobs, Tabellendaten, Warteschlangendaten und Dateien).

Wenn Sie ein Archiv oder eine Bibliothek von VHD-Dateien besitzen, die Sie als Grundlage für die Erstellung neuer virtueller Computer verwenden, können Sie ein neues Speicherkonto erstellen und anschließend die VHD-Dateien in dieses Konto hochladen. Diese VHD-Dateien werden von Azure Storage verschlüsselt.

Wenn Sie Azure Disk Encryption für die Datenträger auf einem virtuellen Computer aktiviert haben, werden neu geschriebene Daten durch SSE und Azure Disk Encryption verschlüsselt.

## <a name="storage-analytics"></a>Speicheranalyse
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Verwenden der Speicheranalyse zur Überwachung des Autorisierungstyps
Für jedes Speicherkonto können Sie die Azure-Speicheranalyse zur Protokollierung und Speicherung von Metrikdaten aktivieren. Dieses Tool ist sehr hilfreich, wenn Sie die Leistungsmetriken für ein Speicherkonto überprüfen möchten oder die Problembehandlung für ein Speicherkonto durchführen müssen, weil Leistungsprobleme auftreten.

Außerdem sehen Sie in den Speicheranalyseprotokollen die Authentifizierungsmethode, die von einem Benutzer verwendet wird, wenn er auf den Speicher zugreift. Bei Blob Storage sehen Sie z. B., ob eine SAS oder die Schlüssel des Speicherkontos verwendet wurden, oder ob das Blob, auf das zugegriffen wurde, für den öffentlichen Zugriff verfügbar war.

Dies kann hilfreich sein, wenn Sie den Zugriff auf den Speicher sehr genau beobachten. In Blob Storage können Sie z. B. alle Container als privat festlegen und die Verwendung eines SAS-Diensts in allen Anwendungen implementieren. Anschließend können Sie die Protokolle regelmäßig überprüfen, um festzustellen, ob der Zugriff auf Ihre Blobs über die Schlüssel des Speicherkontos erfolgt, was auf eine Sicherheitslücke hinweisen könnte, oder ob die Blobs öffentlich sind, obwohl sie es nicht sein sollten.

#### <a name="what-do-the-logs-look-like"></a>Wie sind die Protokolle strukturiert?
Nachdem Sie die Speicherkontometriken und die Protokollierung über das Azure-Portal aktiviert haben, sammeln sich schnell Analysedaten an. Protokollierung und Metriken sind für jeden Dienst getrennt; die Protokollierung wird nur geschrieben, wenn Aktivitäten in diesem Speicherkonto stattfinden, während die Metriken – abhängig von Ihrer Konfiguration – minütlich, stündlich oder täglich protokolliert werden.

Die Protokolle werden im Speicherkonto in einem Container namens „$logs“ in Blockblobs gespeichert. Dieser Container wird automatisch erstellt, wenn die Speicheranalyse aktiviert ist. Sobald Sie diesen Container erstellt haben, können Sie ihn zwar nicht löschen, jedoch seinen Inhalt.

Unter dem Container „$logs“ befindet sich ein Ordner für jeden Dienst, und darin befinden sich Unterordner für Jahr/Monat/Tag/Stunde. Im Unterordner der Stunde sind die Protokolle nummeriert. So sieht die Verzeichnisstruktur aus:

![Anzeigen der Protokolldateien](./media/storage-security-guide/image1.png)

Jede Anforderung an Azure Storage wird protokolliert. Hier ist eine Momentaufnahme einer Protokolldatei, die die ersten paar Felder zeigt.

![Momentaufnahme einer Protokolldatei](./media/storage-security-guide/image2.png)

Sie sehen, dass Sie mit den Protokollen jede Art von Aufrufen eines Speicherkontos nachverfolgen können.

#### <a name="what-are-all-of-those-fields-for"></a>Wofür sind alle diese Felder bestimmt?
In den nachstehenden Ressourcen ist ein Artikel aufgeführt, der die Liste der vielen Felder in den Protokollen und Informationen zu ihrer Verwendung enthält. Hier sehen Sie die Liste der Felder in der richtigen Reihenfolge:

![Momentaufnahme von Feldern einer Protokolldatei](./media/storage-security-guide/image3.png)

Uns interessieren die Einträge für „GetBlob“ und deren Autorisierung. Darum müssen wir nach Einträgen mit „operation-type“ „Get-Blob“ suchen und überprüfen den „request-status“ (vierte</sup> Spalte) sowie den „authorization-type“ (achte</sup> Spalte).

In den ersten Zeilen der obigen Auflistung ist z.B. der „request-status“ „Success“ und der „authorization-type“ „authenticated“. Dies bedeutet, dass die Anforderung mithilfe des Speicherkontoschlüssels autorisiert wurde.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Wie wird der Zugriff auf meine Blobs autorisiert?
Drei Fälle interessieren uns.

1. Das Blob ist öffentlich, und der Zugriff erfolgt mithilfe einer URL ohne SAS. In diesem Fall ist der „request-status“ „AnonymousSuccess“ und der „authorization-type“ „anonymous“.

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. Das Blob ist privat und wurde mit einer SAS verwendet. In diesem Fall ist der „request-status“ „SASSuccess“ und der „authorization-type“ „sas“.

   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. Das Blob ist privat, und der Speicherschlüssel wurde verwendet, um darauf zuzugreifen. In diesem Fall wird **Success** für „request-status“ und **authenticated** für „authorization-type“ angezeigt.

   1.0;2015-11-16T18:32:24.3174537Z;GetBlob;**Success**;206;59;22;**authenticated**;mystorage…

Sie können diese Protokolle mit dem Microsoft Message Analyzer anzeigen und analysieren. Er enthält Such- und Filterfunktionen. Stellen Sie sich z.B. vor, Sie möchten nach Instanzen von „GetBlob“ suchen, um festzustellen, ob die Verwendung Ihren Erwartungen entspricht, d.h. um sicherzustellen, dass niemand unzulässig auf Ihr Speicherkonto zugreift.

#### <a name="resources"></a>Ressourcen
* [Speicheranalyse](../storage-analytics.md)

  Dieser Artikel bietet eine Übersicht über die Speicheranalyse und die Art, wie Sie sie aktivieren können.
* [Protokollformat der Speicheranalyse](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Dieser Artikel veranschaulicht das Protokollformat der Speicheranalyse und beschreibt ausführlich die darin verfügbaren Felder einschließlich „authentication-type“, was den für die Anforderung verwendeten Authentifizierungstyp angibt.
* [Überwachen eines Speicherkontos im Azure-Portal](../storage-monitor-storage-account.md)

  Dieser Artikel veranschaulicht, wie die Überwachung von Metriken und die Protokollierung für ein Speicherkonto konfiguriert werden.
* [End-to-End-Problembehandlung mit Azure-Speichermetriken und -Protokollen sowie AzCopy und Message Analyzer](../storage-e2e-troubleshooting.md)

  Dieser Artikel behandelt die Problembehandlung mithilfe der Speicheranalyse und veranschaulicht den Einsatz von Microsoft Message Analyzer.
* [Microsoft Message Analyzer Operating Guide (in englischer Sprache)](https://technet.microsoft.com/library/jj649776.aspx)

  Dieser Artikel ist die Referenz für den Microsoft Message Analyzer und enthält Links zu Tutorial, Schnellstart und Funktionsübersicht.

## <a name="cross-origin-resource-sharing-cors"></a>Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)
### <a name="cross-domain-access-of-resources"></a>Domänenübergreifender Ressourcenzugriff
Wenn ein Webbrowser, der in einer Domäne ausgeführt wird, eine HTTP-Anforderung für eine Ressource aus einer anderen Domäne sendet, wird dies als Ursprünge übergreifende HTTP-Anforderung bezeichnet. Beispiel: Eine HTML-Seite von „contoso.com“ sendet eine Anforderung für eine JPEG-Datei, die auf „fabrikam.blob.core.windows.net“ gehostet wird. Aus Sicherheitsgründen schränken Browser aus Skripts wie JavaScript heraus initiierte, Ursprünge übergreifende HTTP-Anforderungen ein. Dies bedeutet: Wenn JavaScript-Code auf einer Webseite von „contoso.com“ diese JPEG-Datei auf „fabrikam.blob.core.windows.net“ anfordert, lässt der Browser die Anforderung nicht zu.

Was hat dies mit Azure Storage zu tun? Wenn Sie statische Ressourcen wie z. B. JSON- oder XML-Datendateien mit einem Speicherkonto namens „Fabrikam“ in Blob Storage speichern, ist „fabrikam.blob.core.windows.net“ die Domäne für die Ressourcen, und die Webanwendung „contoso.com“ kann nicht mithilfe von JavaScript darauf zugreifen, da es sich um verschiedene Domänen handelt. Dies gilt auch, wenn Sie versuchen, einen der Azure Storage-Dienste – z.B. Table Storage – aufzurufen, die JSON-Daten zur Verarbeitung durch den JavaScript-Client zurückgeben.

#### <a name="possible-solutions"></a>Lösungsvorschläge
Eine Möglichkeit zur Lösung des Problems ist, „fabrikam.blob.core.windows.net“ eine benutzerdefinierte Domäne wie „storage.contoso.com“ zuzuweisen. Der Haken dabei ist, dass Sie diese benutzerdefinierte Domäne nur einem einzigen Speicherkonto zuordnen können. Was geschieht, wenn die Ressourcen in mehreren Speicherkonten gespeichert sind?

Eine weitere Möglichkeit zur Lösung des Problems ist, die Webanwendung als Proxy für die Speicheraufrufe einzusetzen. Dies bedeutet: Wenn Sie eine Datei in Blob Storage hochladen, schreibt die Webanwendung sie entweder lokal und kopiert sie in Blob Storage, oder sie liest alle Daten in den Arbeitsspeicher und schreibt sie anschließend in Blob Storage. Alternativ könnten Sie eine dedizierte Webanwendung (z. B. eine Web-API) schreiben, die die Dateien lokal hochlädt und in Blob Storage schreibt. In beiden Fällen müssen Sie diese Funktion berücksichtigen, wenn Sie die Skalierbarkeit bestimmen.

#### <a name="how-can-cors-help"></a>Wie kann CORS helfen?
Mit Azure Storage können Sie CORS aktivieren – Ressourcenfreigabe zwischen verschiedenen Ursprüngen. Für jedes Speicherkonto können Sie Domänen angeben, die auf die Ressourcen in diesem Speicherkonto zugreifen können. Beispielsweise können wir im oben genannten Fall CORS für das Speicherkonto auf „fabrikam.blob.core.windows.net“ aktivieren und es so konfigurieren, dass es auf „contoso.com“ zugreifen darf. Dann kann die Webanwendung „contoso.com“ direkt auf die auf „fabrikam.blob.core.windows.net“ gespeicherten Ressourcen zugreifen.

Dabei ist zu beachten, dass CORS Zugriff ermöglicht, aber keine Authentifizierung bietet, die für jeglichen nichtöffentlichen Zugriff auf Speicherressourcen erforderlich ist. Dies bedeutet, dass Sie nur dann auf Blobs zugreifen können, wenn sie öffentlich sind, oder Sie beziehen eine SAS ein, die Ihnen die entsprechende Berechtigung gewährt. Tabellen, Warteschlangen und Dateien haben keinen öffentlichen Zugriff und erfordern eine SAS.

CORS ist standardmäßig für alle Dienste deaktiviert. Sie können CORS mithilfe der REST-API oder der Speicherclientbibliothek für den Aufruf einer der Methoden zum Festlegen von Dienstrichtlinien aktivieren. Wenn Sie dies tun, beziehen Sie eine CORS-Regel im XML-Format ein. Hier ist ein Beispiel für eine CORS-Regel, die mithilfe des Set Service Properties-Vorgangs für den Blob-Dienst für ein Speicherkonto festgelegt wurde. Sie können diesen Vorgang mithilfe der Speicherclientbibliothek oder der REST-APIs für Azure Storage ausführen.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Die einzelnen Zeilen haben folgende Bedeutung:

* **AllowedOrigins** Informiert darüber, welche nicht übereinstimmenden Domänen Daten aus dem Speicherdienst anfordern und empfangen können. Dies bedeutet, dass sowohl „contoso.com“ als auch „fabrikam.com“ Daten aus Blob Storage für ein bestimmtes Speicherkonto anfordern kann. Sie können hier auch ein Platzhalterzeichen (\*) festlegen, damit alle Domänen Anforderungen senden können.
* **AllowedMethods** Dies ist die Liste der Methoden (HTTP-Anforderungsverben), die zur Anforderung verwendet werden können. In diesem Beispiel sind nur „PUT“ und „GET“ zulässig. Sie können hier auch ein Platzhalterzeichen (\*) festlegen, um die Verwendung aller Methoden zu ermöglichen.
* **AllowedHeaders** Diese Anforderungsheader können von der Ursprungsdomäne bei einer Anforderung angegeben werden. In diesem Beispiel sind alle Metadatenheader zulässig, die mit „x-ms-meta-data“, „x-ms-meta-target“ und „x-ms-meta-abc“ beginnen. Das Platzhalterzeichen (\*) zeigt an, dass beliebige Header, die mit dem angegebenen Präfix beginnen, zulässig sind.
* **ExposedHeaders** Diese Antwortheader sollten vom Browser für den Anforderungsaussteller verfügbar gemacht werden. In diesem Beispiel sind alle Header verfügbar, die mit „x-ms-meta-“ beginnen.
* **MaxAgeInSeconds** Der maximale Zeitraum, für den ein Browser die Anforderung „preflight OPTIONS“ zwischenspeichert. (Weitere Informationen zur Preflight-Anforderung finden Sie im ersten Artikel unten.)

#### <a name="resources"></a>Ressourcen
Weitere Informationen zu CORS und zur CORS-Aktivierung finden Sie in diesen Ressourcen.

* [Unterstützung von Cross-Origin Resource Sharing (CORS) für die Azure Storage-Dienste](../storage-cors-support.md)

  Dieser Artikel enthält eine Übersicht über CORS und zur Festlegung der Regeln für die verschiedenen Speicherdienste.
* [Cross-Origin Resource Sharing (CORS) Support for the Azure Storage Services on MSDN (Unterstützung von Cross-Origin Resource Sharing [CORS] für die Azure Storage-Dienste unter MSDN)](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Dies ist die Referenzdokumentation für die CORS-Unterstützung für die Azure Storage-Dienste. Sie enthält Links zu Artikeln, die sich auf die einzelnen Speicherdienste beziehen, zeigt ein Beispiel und erläutert jedes Element in der CORS-Datei.
* [Microsoft Azure Storage: Einführung in CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Dies ist ein Link zu dem ursprünglichen Blogartikel, der CORS ankündigt und Ihnen die Verwendung zeigt.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Häufig gestellte Fragen zur Azure Storage-Sicherheit
1. **Wie kann ich die Integrität der Blobs überprüfen, die ich in Azure Storage oder daraus heraus übertrage, wenn ich das HTTPS-Protokoll nicht verwenden kann?**

   Wenn Sie aus irgendeinem Grund HTTP statt HTTPS verwenden müssen und mit Blockblobs arbeiten, können Sie eine MD5-Prüfung einsetzen, um die Integrität der übertragenen Blobs zu überprüfen. Dies bietet Schutz vor Fehlern der Vermittlungs-/Transportschicht, aber nicht notwendigerweise vor zwischengeschalteten Angriffen.

   Wenn Sie HTTPS verwenden können, welches Schutz auf der Transportschicht bietet, ist eine MD5-Prüfung redundant und unnötig.

   Weitere Informationen finden Sie unter [Azure Blob MD5 Overview](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx)(Überblick zu Azure-Blob-MD5).
2. **Ist die FIPS-Konformität für die US-Regierung gegeben?**

   Der Federal Information Processing Standard (FIPS) der USA definiert kryptografische Algorithmen, die für die Verwendung in Computersystemen der US-Regierung für den Schutz sensibler Daten genehmigt sind. Bei Aktivieren des FIPS-Modus auf einem Windows-Server oder -Desktop wird dem Betriebssystem mitgeteilt, dass nur FIPS-kompatible kryptografische Algorithmen verwendet werden dürfen. Wenn Anwendungen nicht konforme Algorithmen verwendet, werden die Anwendungen unterbrochen. Ab .NET Framework Version 4.5.2 oder höher wechselt die Anwendung automatisch die Kryptografiealgorithmen, um FIPS-kompatible Algorithmen verwenden, wenn der Computer im FIPS-Modus ist.

   Microsoft überlässt dem einzelnen Kunden die Entscheidung, den FIPS-Modus zu aktivieren. Wir glauben, dass es für Kunden, die nicht den gesetzlichen Vorschriften der US-Regierung unterliegen, keinen zwingenden Grund gibt, den FIPS-Modus standardmäßig zu aktivieren.

### <a name="resources"></a>Ressourcen
* [Why We're Not Recommending "FIPS Mode" Anymore (Warum wir den „FIPS-Modus“ nicht mehr empfehlen)](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Dieser Blogartikel bietet einen Überblick über FIPS und erläutert, warum der FIPS-Modus nicht mehr standardmäßig aktiviert ist.
* [FIPS 140 Validation (FIPS 140-Validierung)](https://technet.microsoft.com/library/cc750357.aspx)

  Dieser Artikel erläutert, wie Microsoft-Produkte und kryptografische Module mit dem FIPS-Standard für die Bundesregierung der USA kompatibel sind.
* ["System cryptography: Use FIPS compliant algorithms for encryption, hashing, and signing" security settings effects in Windows XP and in later versions of Windows („Systemkryptografie: Verwenden von FIPS-konformen Algorithmen für Verschlüsselung, Hashing und Signatur“ – Effekte von Sicherheitseinstellungen in Windows XP und höheren Versionen von Windows)](https://support.microsoft.com/kb/811833)

  Dieser Artikel behandelt die Verwendung des FIPS-Modus auf älteren Windows-Computern.
