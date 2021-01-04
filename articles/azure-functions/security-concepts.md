---
title: Absichern von Azure Functions
description: Erfahren Sie, wie Sie Ihren in Azure ausgeführten Funktionscode vor gängigen Angriffen schützen können.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: ee54ff8c1efaee00999888891e6de255060aa416
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491323"
---
# <a name="securing-azure-functions"></a>Absichern von Azure Functions

In vielerlei Hinsicht ist die Planung einer sicheren Entwicklung, Bereitstellung und Ausführung serverloser Funktionen vergleichbar mit der Planung einer webbasierten oder in der Cloud gehosteten Anwendung. [Azure App Service](../app-service/index.yml) stellt die Hostinginfrastruktur für Ihre Funktions-Apps bereit. In diesem Artikel werden Sicherheitsstrategien für die Ausführung Ihres Funktionscodes vorgestellt. Außerdem erfahren Sie, wie App Service Ihnen helfen kann, Ihre Funktionen abzusichern. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

Weitere Empfehlungen zur Sicherheit gemäß dem [Azure-Sicherheitsvergleichstest](../security/benchmarks/overview.md) finden Sie unter [Azure-Sicherheitsbaseline für Azure Functions](security-baseline.md).

## <a name="secure-operation"></a>Sichere Ausführung 

In diesem Abschnitt erfahren Sie, wie Sie Ihre Funktions-App so sicher wie möglich konfigurieren und ausführen können. 

### <a name="security-center"></a>Security Center

Security Center und Ihre Funktions-App lassen sich im Portal integrieren. Es ermöglicht kostenlos eine schnelle Bewertung potenzieller konfigurationsbezogener Sicherheitsrisiken. Funktions-Apps, die in einem dedizierten Plan ausgeführt werden, können gegen Aufpreis auch die Echtzeitsicherheitsfeatures von Security Center nutzen. Weitere Informationen finden Sie unter [Schützen Ihrer Azure App Service-Web-Apps und -APIs](../security-center/defender-for-app-service-introduction.md). 

### <a name="log-and-monitor"></a>Protokollieren und Überwachen

Eine Möglichkeit, Angriffe zu erkennen, besteht in der Aktivitätsüberwachung und Protokollanalyse. Azure Functions und Application Insights sind integriert, um Protokoll-, Leistungs- und Fehlerdaten für Ihre Funktions-App zu sammeln. Application Insights erkennt Leistungsanomalien automatisch und verfügt über leistungsstarke Analysetools, mit denen Sie Probleme untersuchen und nachvollziehen können, wie Ihre Funktionen verwendet werden. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md).

Zudem sind Azure Functions und Azure Monitor-Protokolle integriert, um Ihnen zu ermöglichen, Protokolle von Funktions-Apps mit Systemereignissen zur einfacheren Analyse zusammenzuführen. Sie können Diagnoseeinstellungen nutzen, um den Export von Plattformprotokollen und Metriken für Ihre Funktionen durch Streamen zum Ziel Ihrer Wahl festzulegen, z. B. einem Log Analytics-Arbeitsbereich. Weitere Informationen finden Sie unter [Überwachen von Azure Functions mit Azure Monitor-Protokollen](functions-monitor-log-analytics.md). 

Zur Erkennung von Bedrohungen auf Unternehmensebene und zur Automatisierung der Reaktion können Sie Ihre Protokolle und Ereignisse in einen Log Analytics-Arbeitsbereich streamen. Sie können anschließend Azure Sentinel mit diesem Arbeitsbereich verbinden. Weitere Informationen finden Sie unter [Was ist Azure Sentinel?](../sentinel/overview.md)  

Weitere Empfehlungen zu Einblicken in die Sicherheit finden Sie unter [Azure-Sicherheitsbaseline für Azure Functions](security-baseline.md#logging-and-monitoring). 

### <a name="require-https"></a>Erzwingen von HTTPS

Clients können sich standardmäßig über HTTP oder HTTPS mit Endpunkten von Funktionen verbinden. Sie sollten HTTP zu HTTPS umleiten, da für HTTPS das SSL-/TLS-Protokoll verwendet wird. Es ermöglicht eine sichere Verbindung, die zugleich verschlüsselt und authentifiziert ist. Weitere Informationen dazu finden Sie unter [Erzwingen von HTTPS](../app-service/configure-ssl-bindings.md#enforce-https).

Wenn Sie HTTPS erzwingen, sollten Sie auch die neueste TLS-Version erzwingen. Weitere Informationen finden Sie unter [Erzwingen von TLS-Versionen](../app-service/configure-ssl-bindings.md#enforce-tls-versions).

Weitere Informationen finden Sie unter [Sichere Verbindungen (TLS)](../app-service/overview-security.md#https-and-certificates).

### <a name="function-access-keys"></a>Funktionszugriffsschlüssel

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>Systemschlüssel 

Für bestimmte Erweiterungen kann ein vom System verwalteter Schlüssel für den Zugriff auf Endpunkte von Webhooks erforderlich sein. Systemschlüssel sind für erweiterungsspezifische Funktionsendpunkte vorgesehen, die von internen Komponenten aufgerufen werden. Beispielsweise erfordert der [Event Grid-Auslöser](functions-bindings-event-grid-trigger.md), dass das Abonnement beim Aufruf des Endpunkts des Auslösers einen Systemschlüssel nutzt. Das Feature „Durable Functions“ nutzt auch Systemschlüssel zum Aufrufen von [Durable Task-Erweiterungs-APIs](durable/durable-functions-http-api.md). 

Der Geltungsbereich von Systemschlüsseln wird von der Erweiterung bestimmt, gilt aber in der Regel für die gesamte Funktions-App. Systemschlüssel können nur von bestimmten Erweiterungen erstellt werden. Ihre Werte können nicht explizit festgelegt werden. Wie bei anderen Schlüsseln können Sie einen neuen Wert für den Schlüssel über das Portal oder mithilfe der Schlüssel-APIs generieren.

#### <a name="keys-comparison"></a>Vergleich der Schlüssel

In der folgenden Tabelle wird der Zweck der verschiedenen Arten von Zugriffsschlüsseln verglichen:

| Aktion                                        | `Scope`                    | Gültige Schlüssel         |
|-----------------------------------------------|--------------------------|--------------------|
| Funktion ausführen                            | Spezifische Funktion        | Funktion           |
| Funktion ausführen                            | Beliebige Funktion             | Funktion oder Host   |
| Administratorendpunkt aufrufen                        | Funktionen-App             | Host (nur Master) |
| Durable Task-Erweiterungs-APIs aufrufen              | Funktions-App<sup>1</sup> | System<sup>2</sup> |
| Erweiterungsspezifischen Webhook (intern) aufrufen | Funktions-App<sup>1</sup> | System<sup>2</sup> |

<sup>1</sup>Von der Erweiterung bestimmter Geltungsbereich.  
<sup>2</sup>Von der Erweiterung festgelegte spezifische Namen.

Weitere Informationen über Zugriffsschlüssel finden Sie im Artikel zur [Bindung von HTTP-Auslösern](functions-bindings-http-webhook-trigger.md#obtaining-keys).


#### <a name="secret-repositories"></a>Geheimnis-Repositorys

Standardmäßig werden Schlüssel in einem Blob Storage-Container in dem Konto gespeichert, das von der `AzureWebJobsStorage` Einstellung bereitgestellt wird. Sie können spezifische Anwendungseinstellungen verwenden, um dieses Verhalten außer Kraft zu setzen und Schlüssel an einem anderen Ort zu speichern.

|Standort  |Einstellung | Wert | BESCHREIBUNG  |
|---------|---------|---------|---------|
|Anderes Speicherkonto     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | Speichert Schlüssel in Blob-Speicher eines zweiten Speicherkontos, basierend auf der angegebenen SAS-URL. Schlüssel werden vor der Speicherung mit einem Geheimnis verschlüsselt, das für Ihre Funktions-App eindeutig ist. |
|Dateisystem   | `AzureWebJobsSecretStorageType`   |  `files`       | Schlüssel werden dauerhaft im Dateisystem gespeichert, wobei sie vor dem Speichern mit einem Geheimnis verschlüsselt werden, das für Ihre Funktions-App eindeutig ist. |
|Azure-Schlüsseltresor  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | Der Tresor muss über eine Zugriffsrichtlinie verfügen, die der systemseitig zugewiesenen verwalteten Identität der Hostingressource entspricht. Die Zugriffsrichtlinie sollte der Identität die folgenden Geheimnisberechtigungen gewähren: `Get`, `Set`, `List` und `Delete`. <br/>Bei lokaler Ausführung wird die Entwickleridentität verwendet, und Einstellungen müssen sich in der [local.settings.json](functions-run-local.md#local-settings-file)-Datei befinden. | 
|Kubernetes-Geheimnisse  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (optional) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Nur unterstützt, wenn die Functions-Laufzeit in Kubernetes ausgeführt wird. Wenn `AzureWebJobsKubernetesSecretName` nicht festgelegt ist, wird das Repository als schreibgeschützt betrachtet. In diesem Fall müssen die Werte vor der Bereitstellung generiert werden. Die Azure Functions Core Tools generieren die Werte automatisch bei der Bereitstellung auf Kubernetes.|

### <a name="authenticationauthorization"></a>Authentifizierung/Autorisierung

Funktionsschlüssel können zwar einen gewissen Schutz vor unerwünschtem Zugriff bieten. Doch die einzige Möglichkeit, Ihre Funktionsendpunkte wirksam abzusichern, ist die Implementierung einer positiven Authentifizierung von Clients, die auf Ihre Funktionen zugreifen. Sie können dann Autorisierungsentscheidungen auf Grundlage der Identität treffen.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>Berechtigungen

Wie bei allen Anwendungen oder Diensten besteht das Ziel darin, Ihre Funktions-App mit den geringstmöglichen Berechtigungen auszuführen. 

#### <a name="user-management-permissions"></a>Berechtigungen für die Benutzerverwaltung

Azure Functions unterstützt die integrierte [rollenbasierte Zugriffssteuerung (Azure RBAC) von Azure](../role-based-access-control/overview.md). Von Functions unterstützte Azure-Rollen sind [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor), [Besitzer](../role-based-access-control/built-in-roles.md#owner) und [Leser](../role-based-access-control/built-in-roles.md#owner). 

Berechtigungen gelten auf Funktions-App-Ebene. Die Rolle „Mitwirkender“ ist zur Ausführung der meisten Aufgaben auf Funktions-App-Ebene erforderlich. Nur die Rolle „Besitzer“ kann eine Funktions-App löschen. 

#### <a name="organize-functions-by-privilege"></a>Organisieren von Funktionen nach Berechtigungen 

Verbindungszeichenfolgen und andere in den Anwendungseinstellungen gespeicherte Anmeldeinformationen erteilen allen Funktionen in der Funktions-App die gleichen Berechtigungen in der zugehörigen Ressource. Erwägen Sie, die Anzahl der Funktionen mit Zugriff auf bestimmte Anmeldeinformationen zu minimieren, indem Sie Funktionen, die diese Anmeldeinformationen nicht nutzen, in eine separate Funktions-App verlagern. Sie können stets Techniken wie [Funktionsverkettung](/learn/modules/chain-azure-functions-data-using-bindings/) nutzen, um Daten in verschiedenen Funktions-Apps zwischen Funktionen zu übergeben.  

#### <a name="managed-identities"></a>Verwaltete Identitäten

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten für App Service und Azure Functions](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json).

#### <a name="restrict-cors-access"></a>Beschränken des CORS-Zugriffs

[CORS (Cross-Origin Resource Sharing)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) ist eine Möglichkeit, Web-Apps, die in einer anderen Domäne ausgeführt werden, das Stellen von Anforderungen an Ihre HTTP-Auslöserendpunkte zu ermöglichen. App Service bietet integrierte Unterstützung für die Übergabe der erforderlichen CORS-Header in HTTP-Anforderungen. CORS-Regeln werden auf Funktions-App-Ebene definiert.  

Es ist verlockend, einen Platzhalter zu nutzen, der allen Websites den Zugriff auf Ihren Endpunkt ermöglicht. Damit wird jedoch der Zweck von CORS verfehlt, der darin besteht, Cross-Site Scripting-Angriffe zu verhindern. Fügen Sie stattdessen einen separaten CORS-Eintrag für die Domäne jeder Web-App hinzu, die auf Ihren Endpunkt zugreifen muss. 

### <a name="managing-secrets"></a>Verwaltung geheimer Schlüssel 

Um eine Verbindung mit den verschiedenen Diensten und Ressourcen herstellen zu können, die für die Ausführung Ihres Codes benötigt werden, müssen Funktions-Apps auf Geheimnisse zugreifen können, wie z. B. Verbindungszeichenfolgen und Dienstschlüssel. In diesem Abschnitt wird beschrieben, wie Sie die für Ihre Funktionen erforderlichen Geheimnisse speichern können.

Speichern Sie Geheimnisse niemals in Ihrem Funktionscode. 

#### <a name="application-settings"></a>Anwendungseinstellungen

Standardmäßig speichern Sie Verbindungszeichenfolgen und -geheimnisse, die von Ihrer Funktions-App und Bindungen genutzt werden, als Anwendungseinstellungen. Dadurch stehen diese Anmeldeinformationen sowohl Ihrem Funktionscode als auch den verschiedenen von der Funktion genutzten Bindungen zur Verfügung. Der Name der Anwendungseinstellung (des Schlüssels) dient zum Abrufen des tatsächlichen Werts, der das Geheimnis darstellt. 

Beispielsweise benötigt jede Funktions-App ein zugehöriges Speicherkonto, das von der Runtime genutzt wird. Standardmäßig wird die Verbindung mit diesem Speicherkonto in der Anwendungseinstellung `AzureWebJobsStorage` festgelegt.

App-Einstellungen und Verbindungszeichenfolgen werden verschlüsselt in Azure gespeichert. Diese Angaben werden erst entschlüsselt, wenn sie beim Start der App in deren Prozessspeicher eingefügt werden. Die Verschlüsselungsschlüssel werden regelmäßig rotiert. Wenn Sie es stattdessen vorziehen, die sichere Speicherung Ihrer Geheimnisse zu verwalten, sollte die Anwendungseinstellung stattdessen aus Verweisen auf Azure Key Vault bestehen. 

Sie können Einstellungen in der Datei „local.settings.json“ auch standardmäßig verschlüsseln, wenn Sie Funktionen auf dem lokalen Computer entwickeln. Wenn Sie mehr erfahren möchten, sehen Sie sich die `IsEncrypted`-Eigenschaft unter [Datei für lokale Einstellungen](functions-run-local.md#local-settings-file) an.  

#### <a name="key-vault-references"></a>Key Vault-Verweise

Während Anwendungseinstellungen für die meisten Funktionen ausreichend sind, möchten Sie möglicherweise die gleichen Geheimnisse für mehrere Dienste gemeinsam nutzen. In diesem Fall führt die redundante Speicherung von Geheimnissen zu mehr potenziellen Sicherheitsrisiken. Ein sichererer Ansatz ist das Einrichten eines zentralen Geheimnisspeicherdiensts und das Arbeiten mit Verweisen auf diesen Dienst statt mit den Geheimnissen selbst.      

[Azure Key Vault](../key-vault/general/overview.md) ist ein Dienst, der eine zentralisierte Verwaltung von Geheimnissen mit voller Kontrolle über Zugriffsrichtlinien und Überprüfungsverlauf ermöglicht. Sie können in Ihren Anwendungseinstellungen einen Key Vault-Verweis anstelle einer Verbindungszeichenfolge oder eines Schlüssels nutzen. Weitere Informationen finden Sie unter [Verwenden von Key Vault-Verweisen in App Service und Azure Functions](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="set-usage-quotas"></a>Festlegen von Nutzungskontingenten

Erwägen Sie das Festlegen eines Nutzungskontingents für Funktionen, die in einem Verbrauchstarif ausgeführt werden. Wenn Sie einen täglichen Grenzwert für GB pro Sekunde für die gesamte Ausführung von Funktionen in Ihrer Funktions-App festlegen, wird die Ausführung bei Erreichen des Grenzwerts beendet. Dies könnte möglicherweise dazu beitragen, bösartigen Code zu entschärfen, der Ihre Funktionen ausführt. Weitere Informationen zum Abschätzen des Verbrauchs für Ihre Funktionen finden Sie unter [Abschätzen der Kosten des Verbrauchstarifs](functions-consumption-costs.md). 

### <a name="data-validation"></a>Datenüberprüfung

Die von Ihren Funktionen genutzten Auslöser und Bindungen bieten keine zusätzliche Datenüberprüfung. Ihr Code muss alle von einem Auslöser oder einer Eingabebindung empfangenen Daten überprüfen. Wenn ein vorgelagerter Dienst kompromittiert wird, möchten Sie nicht, dass ungeprüfte Eingaben durch Ihre Funktionen geleitet werden. Wenn Ihre Funktion z. B. Daten aus einer Azure Storage-Warteschlange in einer relationalen Datenbank speichert, müssen Sie die Daten überprüfen und Ihre Befehle parametrisieren, um Angriffe durch Einschleusung von SQL-Befehlen zu vermeiden. 

Gehen Sie nicht davon aus, dass die Daten, die in Ihre Funktion eingehen, bereits überprüft oder bereinigt wurden. Es ist auch eine gute Idee, zu prüfen, ob die Daten, die in Ausgabebindungen geschrieben werden, gültig sind. 

### <a name="handle-errors"></a>Fehlerbehandlung

Auch wenn es einfach erscheint, ist es wichtig, eine effiziente Fehlerbehandlung in Ihre Funktionen zu schreiben. Unbehandelte Fehler fließen in den Host und werden von der Runtime behandelt. Verschiedene Bindungen handhaben die Fehlerbehandlung unterschiedlich. Weitere Informationen finden Sie unter [Azure Functions – Fehlerbehandlung](functions-bindings-error-pages.md).

### <a name="disable-remote-debugging"></a>Deaktivieren des Remotedebuggen

Stellen Sie sicher, dass das Remotedebuggen deaktiviert ist, außer wenn Sie Ihre Funktionen aktiv debuggen. Sie können das Remotedebuggen auf der Registerkarte **Allgemeine Einstellungen** in der **Konfiguration** Ihrer Funktions-App im Portal festlegen. 

### <a name="restrict-cors-access"></a>Beschränken des CORS-Zugriffs

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Verwenden Sie keine Platzhalter in der Liste der zulässigen Ursprünge. Listen Sie stattdessen die spezifischen Domänen auf, von denen Sie voraussichtlich Anforderungen erhalten werden.

### <a name="store-data-encrypted"></a>Daten verschlüsselt speichern

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Sichere Bereitstellung

Die Azure Functions-Tools und -Integration machen es einfach, lokalen Projektcode von Funktionen in Azure zu veröffentlichen. Wichtig ist es zu verstehen, wie die Bereitstellung funktioniert, wenn es um die Sicherheit einer Azure Functions-Topologie geht.   

### <a name="deployment-credentials"></a>Anmeldeinformationen für Bereitstellung

App Service-Bereitstellungen erfordern eine Reihe von Anmeldeinformationen für die Bereitstellung. Diese Anmeldeinformationen für die Bereitstellung werden zur Absicherung Ihrer Funktions-App-Bereitstellungen genutzt. Anmeldeinformationen für die Bereitstellung werden von der App Service-Plattform verwaltet und im Ruhezustand verschlüsselt. 

Es gibt zwei Arten von Anmeldeinformationen für die Bereitstellung:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

Derzeit wird Key Vault nicht für Anmeldeinformationen für die Bereitstellung unterstützt. Weitere Informationen zum Verwalten von Anmeldeinformationen für die Bereitstellung finden Sie unter [Konfigurieren der Anmeldeinformationen für die Bereitstellung für App Service](../app-service/deploy-configure-credentials.md).

### <a name="disable-ftp"></a>Deaktivieren von FTP

Standardmäßig ist bei jeder Funktions-App ein FTP-Endpunkt aktiviert. Auf den FTP-Endpunkt wird mit Anmeldeinformationen für die Bereitstellung zugegriffen. 

FTP wird für die Bereitstellung Ihres Funktionscodes nicht empfohlen. FTP-Bereitstellungen erfolgen manuell und erfordern die Synchronisierung von Auslösern. Weitere Informationen finden Sie unter [FTP-Bereitstellung](functions-deployment-technologies.md#ftp). 

Wenn Sie nicht vorhaben, FTP zu nutzen, sollten Sie es im Portal deaktivieren. Wenn Sie FTP nutzen möchten, sollten Sie [FTPS erzwingen](../app-service/deploy-ftp.md#enforce-ftps).

### <a name="secure-the-scm-endpoint"></a>Absichern des SCM-Endpunkts

Jede Funktions-App hat einen entsprechenden `scm`-Dienstendpunkt, der vom Dienst „Erweiterte Tools (Kudu)“ für Bereitstellungen und andere App Service-[Websiteerweiterungen](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) genutzt wird. Der SCM-Endpunkt für eine Funktions-App ist stets eine URL der Form `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>`. Wenn Sie die Netzwerkisolation zur Absicherung Ihrer Funktionen nutzen, müssen Sie auch diesen Endpunkt berücksichtigen. 

Durch einen separaten SCM-Endpunkt können Sie die Bereitstellung und andere erweiterte Toolfunktionen für Funktions-Apps steuern, die isoliert sind oder in einem virtuellen Netzwerk ausgeführt werden. Der SCM-Endpunkt unterstützt sowohl die Standardauthentifizierung (unter Verwendung der Anmeldeinformationen für die Bereitstellung) als auch einmaliges Anmelden mit Ihren Anmeldeinformationen für das Azure-Portal. Weitere Informationen finden Sie unter [Zugreifen auf den Kudu-Dienst](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). 

### <a name="continuous-security-validation"></a>Laufende Sicherheitsüberprüfung

Da Sicherheit bei jedem Schritt im Entwicklungsprozess berücksichtigt werden muss, ist es sinnvoll, Sicherheitsüberprüfungen auch in einer Continuous Deployment-Umgebung umzusetzen. Dies wird auch als DevSecOps bezeichnet. Wenn Sie Azure DevOps für Ihre Bereitstellungspipeline nutzen, können Sie die Überprüfung in den Bereitstellungsprozess integrieren. Weitere Informationen finden Sie unter [Hinzufügen einer ständigen Sicherheitsüberprüfung zu Ihrer CI-/CD-Pipeline](/azure/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Netzwerksicherheit

Durch Einschränkung des Netzwerkzugriffs auf Ihre Funktions-App können Sie steuern, wer auf die Endpunkte Ihrer Funktionen zugreifen darf. Azure Functions nutzt die Infrastruktur von App Service, um Ihren Funktionen den Zugriff auf Ressourcen ohne Verwendung von im Internet routingfähigen Adressen zu ermöglichen oder den Internetzugriff auf einen Funktionsendpunkt zu beschränken. Weitere Informationen zu diesen Netzwerkoptionen finden Sie unter [Netzwerkoptionen von Azure Functions](functions-networking-options.md).

### <a name="set-access-restrictions"></a>Festlegen von Zugriffsbeschränkungen

Zugriffsbeschränkungen ermöglichen Ihnen, Listen mit Zulassungs-/Verweigerungsregeln zu definieren, um den Datenverkehr zu Ihrer App zu steuern. Regeln werden in der Reihenfolge ihrer Priorität ausgewertet. Wenn keine Regeln definiert sind, akzeptiert Ihre App Datenverkehr von jeder beliebigen Adresse. Weitere Informationen finden Sie unter [Azure App Service – Zugriffseinschränkungen](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="private-site-access"></a>Privater Websitezugriff

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>Isoliertes Bereitstellen Ihrer Funktions-App

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Verwenden eines Gatewaydiensts

Mit Gatewaydiensten wie z. B. [Azure Application Gateway](../application-gateway/overview.md) und [Azure Front Door](../frontdoor/front-door-overview.md) können Sie eine Web Application Firewall (WAF) einrichten. WAF-Regeln dienen der Überwachung oder Blockierung erkannter Angriffe, wodurch eine zusätzliche Schutzebene für Ihre Funktionen geschaffen wird. Um eine WAF einzurichten, muss Ihre Funktions-App in einer App Service-Umgebung laufen oder private Endpunkte (Vorschau) nutzen. Weitere Informationen finden Sie unter [Verwenden privater Endpunkte](../app-service/networking/private-endpoint.md).    

## <a name="next-steps"></a>Nächste Schritte

+ [Azure-Sicherheitsbaseline für Azure Functions](security-baseline.md)
+ [Azure Functions-Diagnose](functions-diagnostics.md)
