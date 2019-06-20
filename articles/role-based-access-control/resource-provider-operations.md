---
title: Vorgänge für Azure Resource Manager-Ressourcenanbieter | Microsoft-Dokumentation
description: Hier werden die verfügbaren Vorgängen für Microsoft Azure Resource Manager-Ressourcenanbieter aufgeführt.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 07c1a726e33eb8287634b63ef2e309483c05c3f3
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962118"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Vorgänge für Azure Resource Manager-Ressourcenanbieter

In diesem Artikel werden die Vorgänge aufgeführt, die jedem Azure Resource Manager-Ressourcenanbieter zur Verfügung stehen. Die Vorgänge können in [benutzerdefinierten Rollen](custom-roles.md) verwendet werden, um eine präzise [rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)](overview.md) für Ressourcen in Azure bereitzustellen. Die Vorgangszeichenfolgen weisen folgendes Format auf: `{Company}.{ProviderName}/{resourceType}/{action}`

Die Vorgänge für Ressourcenanbieter werden ständig weiterentwickelt. Verwenden Sie [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) oder [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list), um die neuesten Vorgänge abzurufen.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.AAD/domainServices/delete | Löscht Domänendienste. |
> | Aktion | Microsoft.AAD/domainServices/oucontainer/delete | Dient zum Löschen des Ou-Containers. |
> | Aktion | Microsoft.AAD/domainServices/oucontainer/read | Dient zum Lesen des Ou-Containers. |
> | Aktion | Microsoft.AAD/domainServices/oucontainer/write | Dient zum Schreiben in den Ou-Container. |
> | Aktion | Microsoft.AAD/domainServices/read | Dient zum Lesen der Domänendienste. |
> | Aktion | Microsoft.AAD/domainServices/ReplicaSets/delete | Löscht einen Clusterstandort |
> | Aktion | Microsoft.AAD/domainServices/ReplicaSets/read | Liest einen Clusterstandort |
> | Aktion | Microsoft.AAD/domainServices/ReplicaSets/write | Schreibt einen Clusterstandort |
> | Aktion | Microsoft.AAD/domainServices/write | Schreibt Domänendienste. |
> | Aktion | Microsoft.AAD/locations/operationresults/read |  |
> | Aktion | Microsoft.AAD/Operations/read |  |
> | Aktion | Microsoft.AAD/register/action | Registriert einen Domänendienst. |
> | Aktion | Microsoft.AAD/unregister/action | Hebt die Registrierung des Domänendiensts auf. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | microsoft.aadiam/diagnosticsettings/delete | Löscht eine Diagnoseeinstellung. |
> | Aktion | microsoft.aadiam/diagnosticsettings/read | Liest eine Diagnoseeinstellung. |
> | Aktion | microsoft.aadiam/diagnosticsettings/write | Schreibt eine Diagnoseeinstellung. |
> | Aktion | microsoft.aadiam/diagnosticsettingscategories/read | Liest die Kategorien einer Diagnoseeinstellung. |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Addons/operations/read | Ruft unterstützte Vorgänge für Ressourcenanbieter ab. |
> | Aktion | Microsoft.Addons/register/action | Registriert das angegebene Abonnement in Microsoft.Addons. |
> | Aktion | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Listet die aktuellen Informationen für den Supportplan für das angegebene Abonnement auf. |
> | Aktion | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Entfernt den angegebenen kanonischen Supportplan. |
> | Aktion | Microsoft.Addons/supportProviders/supportPlanTypes/read | Ruft den Status des angegebenen kanonischen Supportplans ab. |
> | Aktion | Microsoft.Addons/supportProviders/supportPlanTypes/write | Fügt den angegebenen Typ des kanonischen Supportplans hinzu. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/action | Erstellt eine neue Gesamtstruktur für den Mandanten. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Ruft alle Server für den angegebenen Dienstnamen ab. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/alerts/read | Ruft Warnungsdetails für die Gesamtstruktur ab, z.B. die Warnungs-ID, das Datum der Warnung, die letzte erkannte Warnung, die Beschreibung der Warnung, den Zeitpunkt des letzten Updates, den Warnungsstatus und Links zur Problembehandlung der Warnung. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/configuration/read | Ruft die Dienstkonfiguration für die Gesamtstruktur ab. Beispiel: Name der Gesamtstruktur, Funktionsebene, FSMO-Rolle des Domänennamenmasters, FSMO-Rolle des Schemamasters usw. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/delete | Löscht einen Dienst und dessen Server zusammen mit den Integritätsdaten. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Ruft die Informationen zu Domänen und Standorten für die Gesamtstruktur ab. Beispiel: Integritätsstatus, aktive Warnungen, behobene Warnungen, Eigenschaften wie die Funktionsebene der Domäne, die Gesamtstruktur, der Infrastrukturmaster, PDC, RID-Master usw.  |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Ruft die benutzerdefinierte Einstellung für die Gesamtstruktur ab.<br>Beispiel: Namen von metrischen Zählern wie ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes und ldapsearches.<br>Einstellungen für die Diagramme der Benutzeroberfläche (unter anderem). |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Ruft die Zusammenfassung für die angegebene Gesamtstruktur ab, z.B. den Namen der Gesamtstruktur, die Anzahl der Domänen in dieser Gesamtstruktur, die Anzahl der Standorte, Informationen zu Standorten usw. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Ruft die Liste der unterstützten Metriken für einen bestimmten Dienst ab.<br>Für den AD-Verbunddienst können dies z.B. Sperrungen von Extranet-Konten, die Gesamtanzahl der fehlgeschlagenen Anforderungen, ausstehende Tokenanforderungen (Proxy) oder Tokenanforderungen/Sekunde sein.<br>Für den AD-Domänendienst können dies z.B. NTLM-Authentifizierungen/Sekunde, erfolgreiche LDAP-Bindungen/Sekunde, die Bindungszeit für LDAPs, aktive LDAP-Threads, Kerberos-Authentifizierungen/Sekunde oder die Gesamtanzahl der ATQ-Threads sein.<br>Für den ADSync-Dienst können dies z.B. die Latenz des Ausführungsprofil, hergestellte TCP-Verbindungen, private Bytes des Insights-Agents oder Statistiken über Exporte in Azure AD sein. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Diese API ruft die Metrikinformationen für einen bestimmten Dienst ab.<br>Für den AD-Verbunddienst kann die API beispielsweise verwendet werden, um Informationen zu Folgendem abzurufen: Sperrungen von Extranet-Konten, Gesamtanzahl nicht erfolgreicher Anforderungen, ausstehende Tokenanforderungen (Proxy), Tokenanforderungen/Sekunde und Ähnliches.<br>Für den AD-Domänendienst können dies z.B. NTLM-Authentifizierungen/Sekunde, erfolgreiche LDAP-Bindungen/Sekunde, die Bindungszeit für LDAPs, aktive LDAP-Threads, Kerberos-Authentifizierungen/Sekunde oder die Gesamtanzahl der ATQ-Threads sein.<br>Für den Sync-Dienst können dies z.B. die Latenz des Ausführungsprofil, hergestellte TCP-Verbindungen, private Bytes des Insights-Agents oder Statistiken über Exporte in Azure AD sein. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Diese API ruft die Liste aller eingebundenen AD-Domänendienste für einen Premium-Mandanten ab. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/read | Ruft Dienstdetails für den angegebenen Dienstnamen ab. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Ruft Replikationsdetails für alle Server für den angegebenen Dienstnamen ab. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Ruft die Anzahl der Domänencontroller und deren Replikationsfehler (falls vorhanden) ab. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Ruft die vollständige Liste der Domänencontroller und die Replikationsdetails für die angegebene Gesamtstruktur ab. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Fügt eine Serverinstanz zum Dienst hinzu. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Während der Serverregistrierung des AD-Domänendiensts wird diese API aufgerufen, um die Anmeldeinformationen für das Onboarding neuer Server abzurufen. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Löscht einen Server für einen bestimmten Dienst und Mandanten. |
> | Aktion | Microsoft.ADHybridHealthService/addsservices/write | Erstellt oder aktualisiert die Instanz des AD-Domänendiensts für den Mandanten. |
> | Aktion | Microsoft.ADHybridHealthService/configuration/action | Aktualisiert die Mandantenkonfiguration. |
> | Aktion | Microsoft.ADHybridHealthService/configuration/read | Liest die Mandantenkonfiguration. |
> | Aktion | Microsoft.ADHybridHealthService/configuration/write | Erstellt eine Mandantenkonfiguration. |
> | Aktion | Microsoft.ADHybridHealthService/logs/contents/read | Ruft den Inhalt der Agent-Installation und die im Blob gespeicherten Registrierungsprotokolle ab. |
> | Aktion | Microsoft.ADHybridHealthService/logs/read | Ruft Protokolle für die Agent-Installation und die Registrierung für den Mandanten ab. |
> | Aktion | Microsoft.ADHybridHealthService/operations/read | Ruft die Liste der vom System unterstützten Vorgänge ab. |
> | Aktion | Microsoft.ADHybridHealthService/register/action | Registriert den Ressourcenanbieter für AD Hybrid Health Service und aktiviert die Erstellung von AD Hybrid Health Service-Ressourcen. |
> | Aktion | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Ruft die Liste der verfügbaren Regionen ab, die von DevOps verwendet werden, um Incidents von Benutzern zu unterstützen. |
> | Aktion | Microsoft.ADHybridHealthService/reports/badpassword/read | Ruft die Liste ungültiger Kennworteingabeversuche für alle Benutzer des Active Directory-Verbunddiensts ab. |
> | Aktion | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Ruft den SAS-URI von Blobs ab, der den Zustand und das mögliche Ergebnis für neu in die Warteschlange eingereihte Berichtsaufträge für die Häufigkeit von ungültigen Eingabeversuchen für Benutzername oder Kennwort pro Benutzer-ID, IP-Adresse und Tag für einen bestimmten Mandanten enthält. |
> | Aktion | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Ruft die Liste der für DevOps genehmigten Mandanten ab. Diese wird üblicherweise für den Kundendienst verwendet. |
> | Aktion | Microsoft.ADHybridHealthService/reports/isdevops/read | Ruft einen Wert ab, der angibt, ob der Mandant für DevOps genehmigt ist. |
> | Aktion | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Aktualisiert die Benutzer-ID (Objekt-ID) für den ausgewählten DevOps-Mandanten. |
> | Aktion | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Ruft die ausgewählte Bereitstellung für den angegebenen Mandanten ab. |
> | Aktion | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Ruft den Speicherort eines Mandanten anhand der Mandanten-ID ab. |
> | Aktion | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Ruft den geografischen Standort ab, von dem aus auf die Daten zugegriffen wird. |
> | Aktion | Microsoft.ADHybridHealthService/services/action | Aktualisiert eine Dienstinstanz im Mandanten. |
> | Aktion | Microsoft.ADHybridHealthService/services/alerts/read | Liest die Warnungen für einen Dienst. |
> | Aktion | Microsoft.ADHybridHealthService/services/alerts/read | Liest die Warnungen für einen Dienst. |
> | Aktion | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Überprüft anhand eines Featurenamens, ob ein Dienst die Voraussetzungen für die Verwendung dieses Features erfüllt. |
> | Aktion | Microsoft.ADHybridHealthService/services/delete | Löscht eine Dienstinstanz im Mandanten. |
> | Aktion | Microsoft.ADHybridHealthService/services/exporterrors/read | Ruft die Exportfehler für einen bestimmten Synchronisierungsdienst ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/exportstatus/read | Ruft den Exportstatus für einen bestimmten Dienst ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Ruft Feedback zu Warnungen für einen bestimmten Dienst und Server ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/metricmetadata/read | Ruft die Liste der unterstützten Metriken für einen bestimmten Dienst ab.<br>Für den AD-Verbunddienst können dies z.B. Sperrungen von Extranet-Konten, die Gesamtanzahl der fehlgeschlagenen Anforderungen, ausstehende Tokenanforderungen (Proxy) oder Tokenanforderungen/Sekunde sein.<br>Für den AD-Domänendienst können dies z.B. NTLM-Authentifizierungen/Sekunde, erfolgreiche LDAP-Bindungen/Sekunde, die Bindungszeit für LDAPs, aktive LDAP-Threads, Kerberos-Authentifizierungen/Sekunde oder die Gesamtanzahl der ATQ-Threads sein.<br>Für den ADSync-Dienst können dies z.B. die Latenz des Ausführungsprofil, hergestellte TCP-Verbindungen, private Bytes des Insights-Agents oder Statistiken über Exporte in Azure AD sein. |
> | Aktion | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Diese API ruft anhand eines Diensts den Durchschnitt der Metriken für einen bestimmten Dienst ab.<br>Für den AD-Verbunddienst kann die API beispielsweise verwendet werden, um Informationen zu Folgendem abzurufen: Sperrungen von Extranet-Konten, Gesamtanzahl nicht erfolgreicher Anforderungen, ausstehende Tokenanforderungen (Proxy), Tokenanforderungen/Sekunde und Ähnliches.<br>Für den AD-Domänendienst können dies z.B. NTLM-Authentifizierungen/Sekunde, erfolgreiche LDAP-Bindungen/Sekunde, die Bindungszeit für LDAPs, aktive LDAP-Threads, Kerberos-Authentifizierungen/Sekunde oder die Gesamtanzahl der ATQ-Threads sein.<br>Für den Sync-Dienst können dies z.B. die Latenz des Ausführungsprofil, hergestellte TCP-Verbindungen, private Bytes des Insights-Agents oder Statistiken über Exporte in Azure AD sein. |
> | Aktion | Microsoft.ADHybridHealthService/services/metrics/groups/read | Diese API ruft die Metrikinformationen für einen bestimmten Dienst ab.<br>Für den AD-Verbunddienst kann die API beispielsweise verwendet werden, um Informationen zu Folgendem abzurufen: Sperrungen von Extranet-Konten, Gesamtanzahl nicht erfolgreicher Anforderungen, ausstehende Tokenanforderungen (Proxy), Tokenanforderungen/Sekunde und Ähnliches.<br>Für den AD-Domänendienst können dies z.B. NTLM-Authentifizierungen/Sekunde, erfolgreiche LDAP-Bindungen/Sekunde, die Bindungszeit für LDAPs, aktive LDAP-Threads, Kerberos-Authentifizierungen/Sekunde oder die Gesamtanzahl der ATQ-Threads sein.<br>Für den Sync-Dienst können dies z.B. die Latenz des Ausführungsprofil, hergestellte TCP-Verbindungen, private Bytes des Insights-Agents oder Statistiken über Exporte in Azure AD sein. |
> | Aktion | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Diese API ruft anhand eines Diensts die aggregierte Ansicht der Metriken für einen bestimmten Dienst ab.<br>Für den AD-Verbunddienst kann die API beispielsweise verwendet werden, um Informationen zu Folgendem abzurufen: Sperrungen von Extranet-Konten, Gesamtanzahl nicht erfolgreicher Anforderungen, ausstehende Tokenanforderungen (Proxy), Tokenanforderungen/Sekunde und Ähnliches.<br>Für den AD-Domänendienst können dies z.B. NTLM-Authentifizierungen/Sekunde, erfolgreiche LDAP-Bindungen/Sekunde, die Bindungszeit für LDAPs, aktive LDAP-Threads, Kerberos-Authentifizierungen/Sekunde oder die Gesamtanzahl der ATQ-Threads sein.<br>Für den Sync-Dienst können dies z.B. die Latenz des Ausführungsprofil, hergestellte TCP-Verbindungen, private Bytes des Insights-Agents oder Statistiken über Exporte in Azure AD sein. |
> | Aktion | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Fügt die Überwachungskonfiguration für einen Dienst hinzu oder aktualisiert diese. |
> | Aktion | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Ruft Überwachungskonfigurationen für einen bestimmten Dienst ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Fügt Überwachungskonfigurationen zu einem Dienst hinzu. |
> | Aktion | Microsoft.ADHybridHealthService/services/premiumcheck/read | Diese API ruft die Liste aller eingebundenen Dienste für einen Premium-Mandanten ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/read | Liest die Dienstinstanz im Mandanten. |
> | Aktion | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Ruft alle URIs für Berichte über riskante IP-Adressen für die letzten 7 Tage ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/reports/details/read | Ruft einen Bericht mit den obersten 50 Benutzern mit dem Fehler „Falsches Kennwort“ der letzten sieben Tage ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Generiert einen Bericht über riskante IP-Adressen und gibt einen URI zurück, der darauf verweist. |
> | Aktion | Microsoft.ADHybridHealthService/services/servicemembers/action | Erstellt eine Dienstinstanz im Dienst. |
> | Aktion | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Liest die Warnungen für einen Server. |
> | Aktion | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Während der Serverregistrierung wird diese API aufgerufen, um die Anmeldeinformationen für das Onboarding neuer Server abzurufen. |
> | Aktion | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Diese API ruft für einen bestimmten Server die Liste der Datentypen, die von den Servern hochgeladen werden, sowie die letzte Uhrzeit für jeden Upload ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/servicemembers/delete | Löscht eine Dienstinstanz aus dem Dienst. |
> | Aktion | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Ruft Informationen zu Exportfehlern bei der Synchronisierung für einen bestimmten Synchronisierungsdienst ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Diese API ruft die Metrikinformationen für einen bestimmten Dienst ab.<br>Für den AD-Verbunddienst kann die API beispielsweise verwendet werden, um Informationen zu Folgendem abzurufen: Sperrungen von Extranet-Konten, Gesamtanzahl nicht erfolgreicher Anforderungen, ausstehende Tokenanforderungen (Proxy), Tokenanforderungen/Sekunde und Ähnliches.<br>Für den AD-Domänendienst können dies z.B. NTLM-Authentifizierungen/Sekunde, erfolgreiche LDAP-Bindungen/Sekunde, die Bindungszeit für LDAPs, aktive LDAP-Threads, Kerberos-Authentifizierungen/Sekunde oder die Gesamtanzahl der ATQ-Threads sein.<br>Für den Sync-Dienst können dies z.B. die Latenz des Ausführungsprofil, hergestellte TCP-Verbindungen, private Bytes des Insights-Agents oder Statistiken über Exporte in Azure AD sein. |
> | Aktion | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Ruft die Liste der Connectors und Ausführungsprofilnamen für den angegebenen Dienst und das angegebene Dienstmitglied ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/servicemembers/read | Liest die Dienstinstanz im Dienst. |
> | Aktion | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Ruft die Dienstkonfiguration für einen bestimmten Mandanten ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Ruft den Status eines Features für die Aufnahme in die Whitelist für einen bestimmten Mandanten ab. |
> | Aktion | Microsoft.ADHybridHealthService/services/write | Erstellt eine Dienstinstanz im Mandanten. |
> | Aktion | Microsoft.ADHybridHealthService/unregister/action | Hebt die Registrierung des Abonnements für den AD Hybrid Health Service-Ressourcenanbieter ab. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Advisor/configurations/read | Konfigurationen abrufen |
> | Aktion | Microsoft.Advisor/configurations/write | Erstellt oder aktualisiert Konfigurationen. |
> | Aktion | Microsoft.Advisor/generateRecommendations/action | Generiert Empfehlungen. |
> | Aktion | Microsoft.Advisor/generateRecommendations/read | Ruft den Status der Empfehlungsgenerierung ab. |
> | Aktion | Microsoft.Advisor/operations/read | Ruft die Vorgänge für den Microsoft Advisor ab. |
> | Aktion | Microsoft.Advisor/recommendations/available/action | Neue Empfehlung im Microsoft Advisor verfügbar |
> | Aktion | Microsoft.Advisor/recommendations/read | Liest Empfehlungen. |
> | Aktion | Microsoft.Advisor/recommendations/suppressions/delete | Löscht Unterdrückungen. |
> | Aktion | Microsoft.Advisor/recommendations/suppressions/read | Ruft Unterdrückungen ab. |
> | Aktion | Microsoft.Advisor/recommendations/suppressions/write | Erstellt/Aktualisiert Unterdrückungen. |
> | Aktion | Microsoft.Advisor/register/action | Registriert das Abonnement für den Microsoft Advisor. |
> | Aktion | Microsoft.Advisor/suppressions/delete | Löscht Unterdrückungen. |
> | Aktion | Microsoft.Advisor/suppressions/read | Ruft Unterdrückungen ab. |
> | Aktion | Microsoft.Advisor/suppressions/write | Erstellt/Aktualisiert Unterdrückungen. |
> | Aktion | Microsoft.Advisor/unregister/action | Hebt die Registrierung für das Abonnement für den Microsoft Advisor auf. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.AlertsManagement/actionRules/delete | Löscht eine Aktionsregel in einem bestimmten Abonnement. |
> | Aktion | Microsoft.AlertsManagement/actionRules/read | Ruft alle Aktionsregeln für die Eingabefilter ab. |
> | Aktion | Microsoft.AlertsManagement/actionRules/write | Erstellt oder aktualisiert eine Aktionsregel in einem bestimmten Abonnement. |
> | Aktion | Microsoft.AlertsManagement/alerts/changestate/action | Ändert den Status der Warnung. |
> | Aktion | Microsoft.AlertsManagement/alerts/diagnostics/read | Ruft alle Diagnosen für die Warnung ab. |
> | Aktion | Microsoft.AlertsManagement/alerts/history/read | Ruft den Verlauf der Warnung ab. |
> | Aktion | Microsoft.AlertsManagement/alerts/read | Ruft alle Benachrichtigungen für die Eingabefilter ab. |
> | Aktion | Microsoft.AlertsManagement/alertsList/read | Ruft alle Benachrichtigungen für die Eingabefilter abonnementübergreifend ab. |
> | Aktion | Microsoft.AlertsManagement/alertsSummary/read | Ruft die Zusammenfassung der Benachrichtigungen ab. |
> | Aktion | Microsoft.AlertsManagement/alertsSummaryList/read | Ruft die Zusammenfassung der Benachrichtigungen abonnementübergreifend ab. |
> | Aktion | Microsoft.AlertsManagement/Operations/read | Liest die angegebenen Vorgänge. |
> | Aktion | Microsoft.AlertsManagement/register/action | Registriert das Abonnement für die Microsoft-Warnungsverwaltung. |
> | Aktion | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Löschen der Smart Detector-Warnungsregel in einem bestimmten Abonnement. |
> | Aktion | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Abrufen aller Smart Detector-Warnungsregeln für die Eingabefilter. |
> | Aktion | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Erstellen oder Aktualisieren der Smart Detector-Warnungsregel in einem bestimmten Abonnement. |
> | Aktion | Microsoft.AlertsManagement/smartGroups/changestate/action | Ändert den Status der intelligenten Gruppe. |
> | Aktion | Microsoft.AlertsManagement/smartGroups/history/read | Ruft den Verlauf der intelligenten Gruppe ab. |
> | Aktion | Microsoft.AlertsManagement/smartGroups/read | Ruft alle intelligenten Gruppen für die Eingabefilter ab. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Prüft, ob der angegebene Analysis-Servername gültig ist oder bereits verwendet wird. |
> | Aktion | Microsoft.AnalysisServices/locations/operationresults/read | Ruft die Informationen des angegebenen Vorgangsergebnisses ab. |
> | Aktion | Microsoft.AnalysisServices/locations/operationstatuses/read | Ruft die Informationen des angegebenen Vorgangsstatus ab. |
> | Aktion | Microsoft.AnalysisServices/operations/read | Ruft die Informationen von Vorgängen ab. |
> | Aktion | Microsoft.AnalysisServices/register/action | Registriert den Analysis Services-Ressourcenanbieter. |
> | Aktion | Microsoft.AnalysisServices/servers/delete | Löscht den Analysis-Server. |
> | Aktion | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Listet den Status des Gateways auf, der dem Server zugeordnet ist. |
> | Aktion | Microsoft.AnalysisServices/servers/read | Ruft die Informationen des angegebenen Analysis-Servers ab. |
> | Aktion | Microsoft.AnalysisServices/servers/resume/action | Setzt den Analysis-Server fort. |
> | Aktion | Microsoft.AnalysisServices/servers/skus/read | Ruft die verfügbaren SKU-Informationen für den Server ab. |
> | Aktion | Microsoft.AnalysisServices/servers/suspend/action | Hält den Analysis-Server an. |
> | Aktion | Microsoft.AnalysisServices/servers/write | Erstellt oder aktualisiert den angegebenen Analysis-Server. |
> | Aktion | Microsoft.AnalysisServices/skus/read | Ruft die Informationen der SKUs ab. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ApiManagement/checkNameAvailability/read | Prüft, ob der angegebene Dienstname verfügbar ist. |
> | Aktion | Microsoft.ApiManagement/operations/read | Liest alle API-Vorgänge, die für die Microsoft.ApiManagement-Ressource verfügbar sind. |
> | Aktion | Microsoft.ApiManagement/register/action | Dient zum Registrieren des Microsoft.ApiManagement-Ressourcenanbieters. |
> | Aktion | Microsoft.ApiManagement/reports/read | Ruft Berichte ab, die nach Zeiträumen, geografischer Region, Entwicklern, Produkten, APIs, Vorgängen, Abonnement und byRequest aggregiert sind. |
> | Aktion | Microsoft.ApiManagement/service/apis/delete | Löscht die angegebene API aus der API Management-Dienstinstanz |
> | Aktion | Microsoft.ApiManagement/service/apis/diagnostics/delete | Löscht die angegebene Diagnose aus einer API |
> | Aktion | Microsoft.ApiManagement/service/apis/diagnostics/read | Listet alle Diagnosen einer API auf oder ruft Details der Diagnose für eine durch ihren Bezeichner angegebene API ab |
> | Aktion | Microsoft.ApiManagement/service/apis/diagnostics/write | Erstellt eine neue Diagnose für eine API oder aktualisiert eine bereits vorhandene oder aktualisiert Details der Diagnose für eine durch ihren Bezeichner angegebene API |
> | Aktion | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Löscht den angegebenen Kommentar aus einem Problem |
> | Aktion | Microsoft.ApiManagement/service/apis/issues/attachments/read | Listet alle Anlagen zu dem der angegebenen API zugeordneten Problem auf oder ruft Details der Problemanlage für eine durch ihren Bezeichner angegebene API ab |
> | Aktion | Microsoft.ApiManagement/service/apis/issues/attachments/write | Erstellt eine neue Anlage für das Problem in einer API oder aktualisiert eine bereits vorhandene |
> | Aktion | Microsoft.ApiManagement/service/apis/issues/comments/delete | Löscht den angegebenen Kommentar aus einem Problem |
> | Aktion | Microsoft.ApiManagement/service/apis/issues/comments/read | Listet alle Kommentare zu dem der angegebenen API zugeordneten Problem auf oder ruft Details des Problemkommentars für eine durch ihren Bezeichner angegebene API ab |
> | Aktion | Microsoft.ApiManagement/service/apis/issues/comments/write | Erstellt einen neuen Kommentar für das Problem in einer API oder aktualisiert einen bereits vorhandenen |
> | Aktion | Microsoft.ApiManagement/service/apis/issues/delete | Löscht das angegebene Problem aus einer API |
> | Aktion | Microsoft.ApiManagement/service/apis/issues/read | Listet die der angegebenen API zugeordneten Probleme auf oder ruft Details des Problems für eine durch ihren Bezeichner angegebene API ab |
> | Aktion | Microsoft.ApiManagement/service/apis/issues/write | Erstellt ein neues Problem für eine API oder aktualisiert ein bereits vorhandenes oder aktualisiert ein vorhandenes Problem für eine API |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/delete | Löscht den angegebenen Vorgang aus der API |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/policies/delete | Löscht die Richtlinienkonfiguration aus dem API-Vorgang |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/policies/read | Ruft die Liste der Richtlinienkonfigurationen auf Ebene des API-Vorgangs ab oder ruft die Richtlinienkonfiguration auf Ebene des API-Vorgangs ab |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/policies/write | Erstellt oder aktualisiert die Richtlinienkonfiguration auf Ebene des API-Vorgangs |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/policy/delete | Löscht die Richtlinienkonfiguration auf Vorgangsebene |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/policy/read | Ruft die Richtlinienkonfiguration auf Vorgangsebene ab |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/policy/write | Erstellt die Richtlinienkonfiguration auf Vorgangsebene |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/read | Listet eine Auflistung der Vorgänge für die angegebene API auf oder ruft Details des durch seinen Bezeichner angegebenen API-Vorgangs ab |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/tags/delete | Trennt das Tag vom Vorgang |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/tags/read | Listet alle dem Vorgang zugeordneten Tags auf oder ruft die dem Vorgang zugeordneten Tags ab |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/tags/write | Weist dem Vorgang ein Tag zu |
> | Aktion | Microsoft.ApiManagement/service/apis/operations/write | Erstellt einen neuen Vorgang in der API oder aktualisiert einen bereits vorhandenen oder aktualisiert Details des Vorgangs in der durch ihren Bezeichner angegebenen API |
> | Aktion | Microsoft.ApiManagement/service/apis/operationsByTags/read | Listet eine Auflistung von Vorgängen auf, denen Tags zugeordnet sind |
> | Aktion | Microsoft.ApiManagement/service/apis/policies/delete | Löscht die Richtlinienkonfiguration aus der API |
> | Aktion | Microsoft.ApiManagement/service/apis/policies/read | Ruft die Richtlinienkonfiguration auf API-Ebene ab oder ruft die Richtlinienkonfiguration auf API-Ebene ab |
> | Aktion | Microsoft.ApiManagement/service/apis/policies/write | Erstellt oder aktualisiert die Richtlinienkonfiguration für die API |
> | Aktion | Microsoft.ApiManagement/service/apis/policy/delete | Löscht die Richtlinienkonfiguration auf API-Ebene |
> | Aktion | Microsoft.ApiManagement/service/apis/policy/read | Ruft die Richtlinienkonfiguration auf API-Ebene ab |
> | Aktion | Microsoft.ApiManagement/service/apis/policy/write | Erstellt eine Richtlinienkonfiguration auf API-Ebene |
> | Aktion | Microsoft.ApiManagement/service/apis/products/read | Listet alle Produkte auf, zu denen die API gehört |
> | Aktion | Microsoft.ApiManagement/service/apis/read | Listet alle APIs der API Management-Dienstinstanz auf oder ruft Details der durch ihren Bezeichner angegebenen API ab |
> | Aktion | Microsoft.ApiManagement/service/apis/releases/delete | Entfernt alle Releases der API oder löscht das angegebene Release auf der API |
> | Aktion | Microsoft.ApiManagement/service/apis/releases/read | Listet alle Releases einer API auf.<br>Ein API-Release wird erstellt, wenn Sie eine API-Revision zur aktuellen machen.<br>Releases werden auch für einen Rollback zu früheren Revisionen verwendet.<br>Die Ergebnisse werden seitenweise ausgegeben und können mit den Parametern $top und $skip eingeschränkt werden.<br>oder gibt Details eines API-Releases zurück |
> | Aktion | Microsoft.ApiManagement/service/apis/releases/write | Erstellt ein neues Release für die API oder aktualisiert Details des Releases in der durch ihren Bezeichner angegebenen API |
> | Aktion | Microsoft.ApiManagement/service/apis/revisions/delete | Entfernt alle Revisionen einer API. |
> | Aktion | Microsoft.ApiManagement/service/apis/revisions/read | Listet alle Revisionen einer API auf |
> | Aktion | Microsoft.ApiManagement/service/apis/schemas/delete | Löscht die Schemakonfiguration aus der API |
> | Aktion | Microsoft.ApiManagement/service/apis/schemas/read | Ruft die Schemakonfiguration auf API-Ebene ab oder ruft die Schemakonfiguration auf API-Ebene ab |
> | Aktion | Microsoft.ApiManagement/service/apis/schemas/write | Erstellt oder aktualisiert die Schemakonfiguration für die API |
> | Aktion | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Löscht die Tagbeschreibung aus der API |
> | Aktion | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Listet alle Tagbeschreibungen im Bereich der API auf. Modell ähnlich wie Swagger: tagDescription wird auf API-Ebene definiert, Tags können jedoch Vorgängen zugewiesen werden – oder ruft die Tagbeschreibung im Bereich der API ab. |
> | Aktion | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Erstellt oder aktualisiert eine Tagbeschreibung im Bereich der API |
> | Aktion | Microsoft.ApiManagement/service/apis/tags/delete | Trennt das Tag von der API |
> | Aktion | Microsoft.ApiManagement/service/apis/tags/read | Listet alle der API zugeordneten Tags auf oder ruft die der API zugeordneten Tags ab |
> | Aktion | Microsoft.ApiManagement/service/apis/tags/write | Weist der API ein Tag zu |
> | Aktion | Microsoft.ApiManagement/service/apis/write | Erstellt oder aktualisiert die angegebene API der API Management-Dienstinstanz oder aktualisiert die angegebene API der API Management-Dienstinstanz |
> | Aktion | Microsoft.ApiManagement/service/apisByTags/read | Listet eine Auflistung von APIs auf, denen Tags zugeordnet sind |
> | Aktion | Microsoft.ApiManagement/service/apiVersionSets/delete | Löscht die angegebene API-Versionsgruppe |
> | Aktion | Microsoft.ApiManagement/service/apiVersionSets/read | Listet eine Auflistung von API-Versionsgruppen in der angegebenen Dienstinstanz auf oder ruft Details der durch ihren Bezeichner angegebenen API-Versionsgruppe ab |
> | Aktion | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Ruft die Liste der Versionsentitäten ab. |
> | Aktion | Microsoft.ApiManagement/service/apiVersionSets/write | Erstellt oder aktualisiert eine API-Versionsgruppe oder aktualisiert Details der durch ihren Bezeichner angegebenen API-Versionsgruppe |
> | Aktion | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Aktualisiert die in Virtual Network ausgeführten Microsoft.ApiManagement-Ressourcen, um aktualisierte Netzwerkeinstellungen zu verwenden. |
> | Aktion | Microsoft.ApiManagement/service/authorizationServers/delete | Löscht die angegebene Autorisierungsserverinstanz |
> | Aktion | Microsoft.ApiManagement/service/authorizationServers/read | Listet eine Auflistung der in einer Dienstinstanz definierten Autorisierungsserver auf oder ruft Details des durch seinen Bezeichner angegebenen Autorisierungsservers ab |
> | Aktion | Microsoft.ApiManagement/service/authorizationServers/write | Erstellt einen neuen Autorisierungsserver oder aktualisiert Details eines bereits vorhandenen Autorisierungsservers oder aktualisiert Details des durch seinen Bezeichner angegebenen Autorisierungsservers |
> | Aktion | Microsoft.ApiManagement/service/backends/delete | Löscht das angegebene Back-End |
> | Aktion | Microsoft.ApiManagement/service/backends/read | Listet eine Auflistung von Back-Ends in der angegebenen Dienstinstanz auf oder ruft Details des durch seinen Bezeichner angegebenen Back-Ends ab |
> | Aktion | Microsoft.ApiManagement/service/backends/reconnect/action | Benachrichtigt den APIM-Proxy, um nach dem angegebenen Timeout eine neue Verbindung mit dem Back-End zu erstellen. Wenn kein Timeout angegeben wurde, wird ein Timeout von 2 Minuten verwendet. |
> | Aktion | Microsoft.ApiManagement/service/backends/write | Erstellt oder aktualisiert ein Back-End oder aktualisiert ein vorhandenes Back-End |
> | Aktion | Microsoft.ApiManagement/service/backup/action | Dient zum Sichern des API Management-Diensts im angegebenen Container in einem vom Benutzer bereitgestellten Speicherkonto. |
> | Aktion | Microsoft.ApiManagement/service/caches/delete | Löscht den angegebenen Cache |
> | Aktion | Microsoft.ApiManagement/service/caches/read | Listet eine Auflistung aller externen Caches in der angegebenen Dienstinstanz auf oder ruft Details des durch seinen Bezeichner angegebenen Caches ab |
> | Aktion | Microsoft.ApiManagement/service/caches/write | Erstellt oder aktualisiert einen externen Cache, der in der API Management-Instanz verwendet werden soll oder aktualisiert Details des durch seinen Bezeichner angegebenen Caches |
> | Aktion | Microsoft.ApiManagement/service/certificates/delete | Löscht das angegebene Zertifikat |
> | Aktion | Microsoft.ApiManagement/service/certificates/read | Listet eine Auflistung aller Zertifikate in der angegebenen Dienstinstanz auf oder ruft Details des durch seinen Bezeichner angegebenen Zertifikats ab |
> | Aktion | Microsoft.ApiManagement/service/certificates/write | Erstellt oder aktualisiert das Zertifikat für die Authentifizierung beim Back-End |
> | Aktion | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Entfernt das angegebene Inhaltselement. |
> | Aktion | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Gibt eine Liste mit Inhaltselementen oder Details zu Inhaltselementen zurück. |
> | Aktion | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Erstellt ein neues Inhaltselement oder aktualisiert das angegebene Inhaltselement. |
> | Aktion | Microsoft.ApiManagement/service/contentTypes/read | Gibt eine Liste mit Inhaltstypen zurück. |
> | Aktion | Microsoft.ApiManagement/service/delete | Dient zum Löschen einer API Management-Dienstinstanz. |
> | Aktion | Microsoft.ApiManagement/service/diagnostics/delete | Löscht die angegebene Diagnose |
> | Aktion | Microsoft.ApiManagement/service/diagnostics/read | Listet alle Diagnosen der API Management-Dienstinstanz auf oder ruft Details der durch ihren Bezeichner angegebenen Diagnose ab |
> | Aktion | Microsoft.ApiManagement/service/diagnostics/write | Erstellt eine neue Diagnose oder aktualisiert eine bereits vorhandene oder aktualisiert Details der durch ihren Bezeichner angegebenen Diagnose |
> | Aktion | Microsoft.ApiManagement/service/getssotoken/action | Ruft ein SSO-Token ab, das verwendet werden kann, um sich als Administrator beim Legacyportal des API Management-Diensts anzumelden. |
> | Aktion | Microsoft.ApiManagement/service/groups/delete | Löscht die angegebene Gruppe aus der API Management-Dienstinstanz |
> | Aktion | Microsoft.ApiManagement/service/groups/read | Listet eine Auflistung der in einer Dienstinstanz definierten Gruppen auf oder ruft Details der durch ihren Bezeichner angegebenen Gruppe ab |
> | Aktion | Microsoft.ApiManagement/service/groups/users/delete | Entfernt vorhandene Benutzer aus vorhandenen Gruppen |
> | Aktion | Microsoft.ApiManagement/service/groups/users/read | Listet eine Auflistung der der Gruppe zugeordneten Benutzerentitäten auf |
> | Aktion | Microsoft.ApiManagement/service/groups/users/write | Dient zum Hinzufügen von vorhandenen Benutzern zu vorhandenen Gruppen. |
> | Aktion | Microsoft.ApiManagement/service/groups/write | Erstellt oder aktualisiert eine Gruppe oder aktualisiert Details der durch ihren Bezeichner angegebenen Gruppe |
> | Aktion | Microsoft.ApiManagement/service/identityProviders/delete | Löscht die angegebene Konfiguration des Identitätsanbieters |
> | Aktion | Microsoft.ApiManagement/service/identityProviders/read | Listet eine Auflistung der in der angegebenen Dienstinstanz konfigurierten Identitätsanbieter auf oder ruft die Konfigurationsdetails des in der angegebenen Dienstinstanz konfigurierten Identitätsanbieters ab |
> | Aktion | Microsoft.ApiManagement/service/identityProviders/write | Erstellt oder aktualisiert die Konfiguration des Identitätsanbieters oder aktualisiert eine vorhandene Konfiguration eines Identitätsanbieters |
> | Aktion | Microsoft.ApiManagement/service/issues/read | Listet eine Auflistung von Problemen in der angegebenen Dienstinstanz auf oder ruft Details zum API Management-Problem ab |
> | Aktion | Microsoft.ApiManagement/service/locations/networkstatus/read | Ruft den Netzwerkzugriffsstatus von Ressourcen ab, von denen der Dienst am jeweiligen Standort abhängt. |
> | Aktion | Microsoft.ApiManagement/service/loggers/delete | Löscht die angegebene Protokollierung |
> | Aktion | Microsoft.ApiManagement/service/loggers/read | Listet eine Auflistung der Protokollierungen in der angegebenen Dienstinstanz auf oder ruft Details der durch ihren Bezeichner angegebenen Protokollierung ab |
> | Aktion | Microsoft.ApiManagement/service/loggers/write | Erstellt oder aktualisiert eine Protokollierung oder aktualisiert eine vorhandene Protokollierung |
> | Aktion | Microsoft.ApiManagement/service/managedeployments/action | Dient zum Ändern der SKU/Einheiten sowie zum Hinzufügen/Entfernen regionaler Bereitstellungen des API Management-Diensts. |
> | Aktion | Microsoft.ApiManagement/service/networkstatus/read | Ruft den Netzwerkzugriffsstatus von Ressourcen ab, von denen der Dienst abhängt. |
> | Aktion | Microsoft.ApiManagement/service/notifications/action | Sendet Benachrichtigungen an einen angegebenen Benutzer. |
> | Aktion | Microsoft.ApiManagement/service/notifications/read | Listet eine Auflistung der in einer Dienstinstanz definierten Eigenschaften auf oder ruft Details der durch ihren Bezeichner angegebenen Benachrichtigung ab |
> | Aktion | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Entfernt die E-Mail-Adresse aus der Liste der Benachrichtigung |
> | Aktion | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Ruft die Liste der Empfänger-E-Mail-Adressen ab, die eine Benachrichtigung abonniert haben |
> | Aktion | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Fügt die E-Mail-Adresse der Liste der Empfänger der Benachrichtigung hinzu |
> | Aktion | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Entfernt den API Management-Benutzer aus der Liste der Benachrichtigung |
> | Aktion | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Ruft die Liste der Benutzer ab, die den Empfang der Benachrichtigung abonniert haben |
> | Aktion | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Fügt den API Management-Benutzer der Liste der Empfänger der Benachrichtigung hinzu |
> | Aktion | Microsoft.ApiManagement/service/notifications/write | Erstellt oder aktualisiert eine API Management-Benachrichtigung für Herausgeber |
> | Aktion | Microsoft.ApiManagement/service/openidConnectProviders/delete | Löscht den angegebenen OpenID Connect-Anbieter aus der API Management-Dienstinstanz |
> | Aktion | Microsoft.ApiManagement/service/openidConnectProviders/read | Listet alle OpenID Connect-Anbieter auf oder ruft den angegebenen OpenID Connect-Anbieter ab |
> | Aktion | Microsoft.ApiManagement/service/openidConnectProviders/write | Erstellt oder aktualisiert den OpenID Connect-Anbieter oder aktualisiert den angegebenen OpenID Connect-Anbieter |
> | Aktion | Microsoft.ApiManagement/service/operationresults/read | Ruft den aktuellen Status eines Vorgangs mit langer Ausführungsdauer ab. |
> | Aktion | Microsoft.ApiManagement/service/policies/delete | Löscht die globale Richtlinienkonfiguration des API Management-Diensts |
> | Aktion | Microsoft.ApiManagement/service/policies/read | Listet alle globalen Richtliniendefinitionen des API Management-Diensts auf oder ruft die globale Richtlinienkonfiguration des API Management-Diensts ab |
> | Aktion | Microsoft.ApiManagement/service/policies/write | Erstellt oder aktualisiert die globale Richtlinienkonfiguration des API Management-Diensts |
> | Aktion | Microsoft.ApiManagement/service/policy/delete | Löscht die Richtlinienkonfiguration auf Mandantenebene |
> | Aktion | Microsoft.ApiManagement/service/policy/read | Ruft die Richtlinienkonfiguration auf Mandantenebene ab |
> | Aktion | Microsoft.ApiManagement/service/policy/write | Erstellt die Richtlinienkonfiguration auf Mandantenebene |
> | Aktion | Microsoft.ApiManagement/service/policySnippets/read | Listet alle Richtlinienausschnitte auf |
> | Aktion | Microsoft.ApiManagement/service/portalsettings/read | Ruft die Registrierungs-, Anmelde- oder Delegierungseinstellungen für das Portal ab |
> | Aktion | Microsoft.ApiManagement/service/portalsettings/write | Aktualisiert die Anmeldeeinstellungen oder erstellt oder aktualisiert die Anmeldeeinstellungen oder aktualisiert die Registrierungs-, Anmelde- oder Delegierungseinstellungen oder erstellt oder aktualisiert die Delegierungseinstellungen |
> | Aktion | Microsoft.ApiManagement/service/products/apis/delete | Löscht die angegebene API aus dem angegebenen Produkt |
> | Aktion | Microsoft.ApiManagement/service/products/apis/read | Listet eine Auflistung von APIs auf, die einem Produkt zugeordnet sind |
> | Aktion | Microsoft.ApiManagement/service/products/apis/write | Fügt dem angegebenen Produkt eine API hinzu |
> | Aktion | Microsoft.ApiManagement/service/products/delete | Löscht das Produkt |
> | Aktion | Microsoft.ApiManagement/service/products/groups/delete | Löscht die Zuordnung zwischen der angegebenen Gruppe und dem Produkt |
> | Aktion | Microsoft.ApiManagement/service/products/groups/read | Listet die Auflistung der dem angegebenen Produkt zugeordneten Entwicklergruppen auf |
> | Aktion | Microsoft.ApiManagement/service/products/groups/write | Fügt eine Zuordnung zwischen der angegebenen Entwicklergruppe und dem angegebenen Produkt hinzu |
> | Aktion | Microsoft.ApiManagement/service/products/policies/delete | Löscht die Richtlinienkonfiguration aus dem Produkt |
> | Aktion | Microsoft.ApiManagement/service/products/policies/read | Ruft die Richtlinienkonfiguration auf Produktebene ab oder ruft die Richtlinienkonfiguration auf Produktebene ab |
> | Aktion | Microsoft.ApiManagement/service/products/policies/write | Erstellt oder aktualisiert die Richtlinienkonfiguration für das Produkt |
> | Aktion | Microsoft.ApiManagement/service/products/policy/delete | Löscht die Richtlinienkonfiguration auf Produktebene |
> | Aktion | Microsoft.ApiManagement/service/products/policy/read | Ruft die Richtlinienkonfiguration auf Produktebene ab |
> | Aktion | Microsoft.ApiManagement/service/products/policy/write | Erstellt eine Richtlinienkonfiguration auf Produktebene |
> | Aktion | Microsoft.ApiManagement/service/products/read | Listet eine Auflistung der Produkte in der angegebenen Dienstinstanz auf oder ruft Details des durch seinen Bezeichner angegebenen Produkts ab |
> | Aktion | Microsoft.ApiManagement/service/products/subscriptions/read | Listet die Auflistung der Abonnements für das angegebene Produkt auf |
> | Aktion | Microsoft.ApiManagement/service/products/tags/delete | Trennt das Tag vom Produkt |
> | Aktion | Microsoft.ApiManagement/service/products/tags/read | Listet alle dem Produkt zugeordneten Tags auf oder ruft die dem Produkt zugeordneten Tags ab |
> | Aktion | Microsoft.ApiManagement/service/products/tags/write | Weist dem Produkt ein Tag zu |
> | Aktion | Microsoft.ApiManagement/service/products/write | Erstellt oder aktualisiert ein Produkt oder aktualisiert Details vorhandener Produkte |
> | Aktion | Microsoft.ApiManagement/service/productsByTags/read | Listet eine Auflistung von Produkten auf, denen Tags zugeordnet sind |
> | Aktion | Microsoft.ApiManagement/service/properties/delete | Löscht die angegebene Eigenschaft aus der API Management-Dienstinstanz |
> | Aktion | Microsoft.ApiManagement/service/properties/read | Listet eine Auflistung der in einer Dienstinstanz definierten Eigenschaften auf oder ruft Details der durch ihren Bezeichner angegebenen Eigenschaft ab |
> | Aktion | Microsoft.ApiManagement/service/properties/write | Erstellt oder aktualisiert eine Eigenschaft oder aktualisiert die angegebene Eigenschaft |
> | Aktion | Microsoft.ApiManagement/service/quotas/periods/read | Ruft den Wert des Kontingentzählers für einen bestimmten Zeitraum ab. |
> | Aktion | Microsoft.ApiManagement/service/quotas/periods/write | Legt den aktuellen Wert für den Kontingentzähler fest. |
> | Aktion | Microsoft.ApiManagement/service/quotas/read | Ruft Kontingentwerte ab. |
> | Aktion | Microsoft.ApiManagement/service/quotas/write | Legt den aktuellen Wert für den Kontingentzähler fest. |
> | Aktion | Microsoft.ApiManagement/service/read | Dient zum Lesen der Metadaten für eine API Management-Dienstinstanz. |
> | Aktion | Microsoft.ApiManagement/service/regions/read | Listet alle Azure-Regionen auf, in denen der Dienst verfügbar ist |
> | Aktion | Microsoft.ApiManagement/service/reports/read | Ruft Berichte ab, die nach Zeiträumen, geografischer Region oder Entwicklern aggregiert sind.<br>Dient alternativ zum Abrufen von nach Produkten aggregierten Berichten.<br>Dient alternativ zum Abrufen von Berichten, die nach APIs, Vorgängen oder Abonnement aggregiert sind.<br>Dient alternativ zum Abrufen von Berichtsdaten für Anforderungen. |
> | Aktion | Microsoft.ApiManagement/service/restore/action | Dient zum Wiederherstellen des API Management-Diensts aus dem angegebenen Container in einem vom Benutzer bereitgestellten Speicherkonto. |
> | Aktion | Microsoft.ApiManagement/service/subscriptions/delete | Löscht das angegebene Abonnement |
> | Aktion | Microsoft.ApiManagement/service/subscriptions/read | Listet alle Abonnements der API Management-Dienstinstanz auf oder ruft die angegebene Abonnemententität ab |
> | Aktion | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Generiert den Primärschlüssel eines vorhandenen Abonnements der API Management-Dienstinstanz neu |
> | Aktion | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Generiert den Sekundärschlüssel eines vorhandenen Abonnements der API Management-Dienstinstanz neu |
> | Aktion | Microsoft.ApiManagement/service/subscriptions/write | Erstellt oder aktualisiert das Abonnement des angegebenen Benutzers für das angegebene Produkt oder aktualisiert Details eines durch seinen Bezeichner angegebenen Abonnements |
> | Aktion | Microsoft.ApiManagement/service/tagResources/read | Listet eine Auflistung von Ressourcen auf, denen Tags zugeordnet sind |
> | Aktion | Microsoft.ApiManagement/service/tags/delete | Löscht das angegebene Tag aus der API Management-Dienstinstanz |
> | Aktion | Microsoft.ApiManagement/service/tags/read | Listet eine Auflistung der in einer Dienstinstanz definierten Tags auf oder ruft Details des durch seinen Bezeichner angegebenen Tags ab |
> | Aktion | Microsoft.ApiManagement/service/tags/write | Erstellt ein Tag oder aktualisiert Details des durch seinen Bezeichner angegebenen Tags |
> | Aktion | Microsoft.ApiManagement/service/templates/delete | Setzt die standardmäßige E-Mail-Vorlage des API Management zurück. |
> | Aktion | Microsoft.ApiManagement/service/templates/read | Ruft alle E-Mail-Vorlagen des API Management oder die entsprechenden Details ab. |
> | Aktion | Microsoft.ApiManagement/service/templates/write | Erstellt oder aktualisiert die E-Mail-Vorlage des API Management. |
> | Aktion | Microsoft.ApiManagement/service/tenant/delete | Dient zum Entfernen der Richtlinienkonfiguration für den Mandanten. |
> | Aktion | Microsoft.ApiManagement/service/tenant/deploy/action | Führt eine Bereitstellungsaufgabe aus, um Änderungen aus der angegebenen Git-Verzweigung auf die Konfiguration in der Datenbank anzuwenden. |
> | Aktion | Microsoft.ApiManagement/service/tenant/operationResults/read | Dient zum Abrufen einer Liste mit Vorgangsergebnissen oder zum Abrufen des Ergebnisses eines bestimmten Vorgangs. |
> | Aktion | Microsoft.ApiManagement/service/tenant/read | Ruft die globale Richtlinienkonfiguration des API Management-Diensts ab oder ruft Zugriffsinformationsdetails für den Mandanten ab |
> | Aktion | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Dient zum erneuten Generieren des primären Zugriffsschlüssels. |
> | Aktion | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Dient zum erneuten Generieren des sekundären Zugriffsschlüssels. |
> | Aktion | Microsoft.ApiManagement/service/tenant/save/action | Erstellt Commit mit Konfigurationsmomentaufnahme in der angegebenen Verzweigung im Repository. |
> | Aktion | Microsoft.ApiManagement/service/tenant/syncState/read | Dient zum Abrufen des Status der letzten Git-Synchronisierung. |
> | Aktion | Microsoft.ApiManagement/service/tenant/validate/action | Überprüft Änderungen aus der angegebenen Git-Verzweigung. |
> | Aktion | Microsoft.ApiManagement/service/tenant/write | Legt die Richtlinienkonfiguration für den Mandanten ab oder aktualisiert die Zugriffsdetails des Mandanten. |
> | Aktion | Microsoft.ApiManagement/service/updatecertificate/action | Dient zum Hochladen eines SSL-Zertifikats für einen API Management-Dienst. |
> | Aktion | Microsoft.ApiManagement/service/updatehostname/action | Dient zum Einrichten, Aktualisieren oder Entfernen benutzerdefinierter Domänennamen für einen API Management-Dienst. |
> | Aktion | Microsoft.ApiManagement/service/users/action | Registriert einen neuen Benutzer. |
> | Aktion | Microsoft.ApiManagement/service/users/confirmations/send/action | Sendet eine Bestätigung. |
> | Aktion | Microsoft.ApiManagement/service/users/delete | Löscht den angegebenen Benutzer |
> | Aktion | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Ruft eine Umleitungs-URL mit einem Authentifizierungstoken zum Anmelden des angegebenen Benutzers beim Entwicklerportal ab |
> | Aktion | Microsoft.ApiManagement/service/users/groups/read | Listet alle Benutzergruppen auf |
> | Aktion | Microsoft.ApiManagement/service/users/identities/read | Listet alle Benutzeridentitäten auf |
> | Aktion | Microsoft.ApiManagement/service/users/keys/read | Abrufen von Benutzern zugeordneten Schlüsseln |
> | Aktion | Microsoft.ApiManagement/service/users/read | Listet eine Auflistung der in der angegebenen Dienstinstanz registrierten Benutzer auf oder ruft Details des durch seinen Bezeichner angegebenen Benutzers ab |
> | Aktion | Microsoft.ApiManagement/service/users/subscriptions/read | Listet die Auflistung der Abonnements für den angegebenen Benutzer auf |
> | Aktion | Microsoft.ApiManagement/service/users/token/action | Ruft das SAS-Autorisierungstoken für den Benutzer ab |
> | Aktion | Microsoft.ApiManagement/service/users/write | Erstellt oder aktualisiert einen Benutzer oder aktualisiert Details des durch seinen Bezeichner angegebenen Benutzers |
> | Aktion | Microsoft.ApiManagement/service/write | Dient zum Erstellen einer neuen Instanz des API Management-Diensts. |
> | Aktion | Microsoft.ApiManagement/unregister/action | Dient zum Aufheben der Registrierung des Microsoft.ApiManagement-Ressourcenanbieters. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Authorization/classicAdministrators/delete | Entfernt den Administrator aus dem Abonnement. |
> | Aktion | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Ruft die Administratorvorgangsstatus des Abonnements ab. |
> | Aktion | Microsoft.Authorization/classicAdministrators/read | Liest die Administratoren für das Abonnement. |
> | Aktion | Microsoft.Authorization/classicAdministrators/write | Dient zum Hinzufügen oder Ändern von Administratoren für ein Abonnement. |
> | Aktion | Microsoft.Authorization/denyAssignments/delete | Hiermit wird eine Ablehnungszuweisung mit dem angegebenen Bereich gelöscht. |
> | Aktion | Microsoft.Authorization/denyAssignments/read | Hiermit werden Informationen zu einer Ablehnungszuweisung abgerufen. |
> | Aktion | Microsoft.Authorization/denyAssignments/write | Hiermit wird eine Ablehnungszuweisung mit dem angegebenen Bereich erstellt. |
> | Aktion | Microsoft.Authorization/elevateAccess/action | Gewährt dem Aufrufer Zugriff vom Typ „Benutzerzugriffsadministrator“ auf der Mandantenebene. |
> | Aktion | Microsoft.Authorization/locks/delete | Dient zum Löschen von Sperren im angegebenen Bereich. |
> | Aktion | Microsoft.Authorization/locks/read | Ruft Sperren im angegebenen Bereich ab. |
> | Aktion | Microsoft.Authorization/locks/write | Dient zum Hinzufügen von Sperren im angegebenen Bereich. |
> | Aktion | Microsoft.Authorization/operations/read | Ruft die Liste mit den Vorgängen ab. |
> | Aktion | Microsoft.Authorization/permissions/read | Listet alle Berechtigungen auf, über die der Aufrufer in einem bestimmten Bereich verfügt. |
> | Aktion | Microsoft.Authorization/policyAssignments/delete | Dient zum Löschen einer Richtlinienzuweisung im angegebenen Bereich. |
> | Aktion | Microsoft.Authorization/policyAssignments/read | Dient zum Abrufen von Informationen zu einer Richtlinienzuweisung. |
> | Aktion | Microsoft.Authorization/policyAssignments/write | Dient zum Erstellen einer Richtlinienzuweisung im angegebenen Bereich. |
> | Aktion | Microsoft.Authorization/policyDefinitions/delete | Dient zum Löschen einer Richtliniendefinition. |
> | Aktion | Microsoft.Authorization/policyDefinitions/read | Dient zum Abrufen von Informationen zu einer Richtliniendefinition. |
> | Aktion | Microsoft.Authorization/policyDefinitions/write | Dient zum Erstellen einer benutzerdefinierten Richtliniendefinition. |
> | Aktion | Microsoft.Authorization/policySetDefinitions/delete | Löscht eine Richtliniengruppendefinition. |
> | Aktion | Microsoft.Authorization/policySetDefinitions/read | Ruft Informationen zu einer Richtliniengruppendefinition ab. |
> | Aktion | Microsoft.Authorization/policySetDefinitions/write | Erstellt eine benutzerdefinierte Richtliniengruppendefinition. |
> | Aktion | Microsoft.Authorization/providerOperations/read | Dient zum Abrufen von Vorgängen für alle Ressourcenanbieter, die in Rollendefinitionen verwendet werden können. |
> | Aktion | Microsoft.Authorization/roleAssignments/delete | Dient zum Löschen einer Rollenzuweisung im angegebenen Bereich. |
> | Aktion | Microsoft.Authorization/roleAssignments/read | Dient zum Abrufen von Informationen zu einer Rollenzuweisung. |
> | Aktion | Microsoft.Authorization/roleAssignments/write | Dient zum Erstellen einer Rollenzuweisung im angegebenen Bereich. |
> | Aktion | Microsoft.Authorization/roleDefinitions/delete | Dient zum Löschen der angegebenen benutzerdefinierten Rollendefinition. |
> | Aktion | Microsoft.Authorization/roleDefinitions/read | Dient zum Abrufen von Informationen zu einer Rollendefinition. |
> | Aktion | Microsoft.Authorization/roleDefinitions/write | Dient zum Erstellen oder Aktualisieren einer benutzerdefinierten Rollendefinition mit angegebenen Berechtigungen und zuweisbaren Bereichen. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Liest die Registrierungsinformationen einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Schreibt eine Anforderung zum erneuten Generieren von Azure Automation DSC-Schlüsseln. |
> | Aktion | Microsoft.Automation/automationAccounts/certificates/delete | Löscht ein Azure Automation-Zertifikatasset. |
> | Aktion | Microsoft.Automation/automationAccounts/certificates/getCount/action | Liest die Anzahl der Zertifikate. |
> | Aktion | Microsoft.Automation/automationAccounts/certificates/read | Ruft ein Azure Automation-Zertifikatasset ab. |
> | Aktion | Microsoft.Automation/automationAccounts/certificates/write | Erstellt oder aktualisiert ein Azure Automation-Zertifikatasset. |
> | Aktion | Microsoft.Automation/automationAccounts/compilationjobs/read | Liest die Kompilierung einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/compilationjobs/read | Liest die Kompilierung einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/compilationjobs/write | Schreibt die Kompilierung einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/compilationjobs/write | Schreibt die Kompilierung einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/configurations/content/read | Liest den Inhalt der Konfigurationsmedien. |
> | Aktion | Microsoft.Automation/automationAccounts/configurations/delete | Löscht den Inhalt einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/configurations/getCount/action | Liest den Inhalt einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/configurations/read | Ruft den Inhalt einer Azure Automation DSC-Instanz ab. |
> | Aktion | Microsoft.Automation/automationAccounts/configurations/write | Schreibt den Inhalt einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/connections/delete | Löscht ein Azure Automation-Verbindungsasset. |
> | Aktion | Microsoft.Automation/automationAccounts/connections/getCount/action | Liest die Anzahl der Verbindungen. |
> | Aktion | Microsoft.Automation/automationAccounts/connections/read | Ruft ein Azure Automation-Verbindungsasset ab. |
> | Aktion | Microsoft.Automation/automationAccounts/connections/write | Erstellt oder aktualisiert ein Azure Automation-Verbindungsasset. |
> | Aktion | Microsoft.Automation/automationAccounts/connectionTypes/delete | Löscht ein Azure Automation-Verbindungstypasset. |
> | Aktion | Microsoft.Automation/automationAccounts/connectionTypes/read | Ruft ein Azure Automation-Verbindungstypasset ab. |
> | Aktion | Microsoft.Automation/automationAccounts/connectionTypes/write | Erstellt ein Azure Automation-Verbindungstypasset. |
> | Aktion | Microsoft.Automation/automationAccounts/credentials/delete | Löscht ein Azure Automation-Anmeldeinformationsasset. |
> | Aktion | Microsoft.Automation/automationAccounts/credentials/getCount/action | Liest die Anzahl der Anmeldeinformationen. |
> | Aktion | Microsoft.Automation/automationAccounts/credentials/read | Ruft ein Azure Automation-Anmeldeinformationsasset ab. |
> | Aktion | Microsoft.Automation/automationAccounts/credentials/write | Erstellt oder aktualisiert ein Azure Automation-Anmeldeinformationsasset. |
> | Aktion | Microsoft.Automation/automationAccounts/delete | Löscht ein Azure Automation-Konto. |
> | Aktion | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Löscht Hybrid Runbook Worker-Ressourcen. |
> | Aktion | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Liest Hybrid Runbook Worker-Ressourcen. |
> | Aktion | Microsoft.Automation/automationAccounts/jobs/output/read | Ruft die Ausgabe eines Auftrags ab. |
> | Aktion | Microsoft.Automation/automationAccounts/jobs/read | Ruft einen Azure Automation-Auftrag ab. |
> | Aktion | Microsoft.Automation/automationAccounts/jobs/resume/action | Setzt einen Azure Automation-Auftrag fort. |
> | Aktion | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Ruft den Inhalt des Azure Automation-Runbooks zum Zeitpunkt der Auftragsausführung ab. |
> | Aktion | Microsoft.Automation/automationAccounts/jobs/stop/action | Beendet einen Azure Automation-Auftrag. |
> | Aktion | Microsoft.Automation/automationAccounts/jobs/streams/read | Ruft einen Azure Automation-Auftragsdatenstrom ab. |
> | Aktion | Microsoft.Automation/automationAccounts/jobs/streams/read | Ruft einen Azure Automation-Auftragsdatenstrom ab. |
> | Aktion | Microsoft.Automation/automationAccounts/jobs/suspend/action | Hält einen Azure Automation-Auftrag an. |
> | Aktion | Microsoft.Automation/automationAccounts/jobs/write | Erstellt einen Azure Automation-Auftrag. |
> | Aktion | Microsoft.Automation/automationAccounts/jobSchedules/delete | Löscht einen Azure Automation-Auftragszeitplan. |
> | Aktion | Microsoft.Automation/automationAccounts/jobSchedules/read | Ruft einen Azure Automation-Auftragszeitplan ab. |
> | Aktion | Microsoft.Automation/automationAccounts/jobSchedules/write | Erstellt einen Azure Automation-Auftragszeitplan. |
> | Aktion | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Ruft den Arbeitsbereich ab, der mit dem Automation-Konto verknüpft ist. |
> | Aktion | Microsoft.Automation/automationAccounts/listKeys/action | Liest die Schlüssel für das Automation-Konto. |
> | Aktion | Microsoft.Automation/automationAccounts/modules/activities/read | Ruft Azure Automation-Aktivitäten ab. |
> | Aktion | Microsoft.Automation/automationAccounts/modules/delete | Löscht ein Powershell-Modul in Azure Automation. |
> | Aktion | Microsoft.Automation/automationAccounts/modules/getCount/action | Ruft die Anzahl der Powershell-Module innerhalb des Automation-Kontos ab. |
> | Aktion | Microsoft.Automation/automationAccounts/modules/read | Ruft ein Powershell-Modul in Azure Automation ab. |
> | Aktion | Microsoft.Automation/automationAccounts/modules/write | Erstellt oder aktualisiert ein Powershell-Modul in Azure Automation. |
> | Aktion | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Löscht die Knotenkonfiguration einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Liest den Inhalt der Knotenkonfiguration einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Liest die Knotenkonfiguration einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Schreibt die Knotenkonfiguration einer Azure Automation DSC-Instanz. |
> | Aktion | Microsoft.Automation/automationAccounts/nodecounts/read | Liest die zusammengefasste Anzahl der Knoten für den angegebenen Typ. |
> | Aktion | Microsoft.Automation/automationAccounts/nodes/delete | Löscht Azure Automation DSC-Knoten. |
> | Aktion | Microsoft.Automation/automationAccounts/nodes/read | Liest Azure Automation DSC-Knoten. |
> | Aktion | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Liest Azure Automation DSC-Berichtsinhalte. |
> | Aktion | Microsoft.Automation/automationAccounts/nodes/reports/read | Liest Azure Automation DSC-Berichte. |
> | Aktion | Microsoft.Automation/automationAccounts/nodes/write | Erstellt oder aktualisiert Azure Automation DSC-Knoten. |
> | Aktion | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Ruft TypeFields-Elemente für Azure Automation ab. |
> | Aktion | Microsoft.Automation/automationAccounts/python2Packages/delete | Löscht ein Azure Automation Python 2-Paket. |
> | Aktion | Microsoft.Automation/automationAccounts/python2Packages/read | Ruft ein Azure Automation Python 2-Paket ab. |
> | Aktion | Microsoft.Automation/automationAccounts/python2Packages/write | Erstellt oder aktualisiert ein Azure Automation Python 2-Paket. |
> | Aktion | Microsoft.Automation/automationAccounts/python3Packages/delete | Löscht ein Azure Automation Python 3-Paket. |
> | Aktion | Microsoft.Automation/automationAccounts/python3Packages/read | Ruft ein Azure Automation Python 3-Paket ab. |
> | Aktion | Microsoft.Automation/automationAccounts/python3Packages/write | Erstellt oder aktualisiert ein Azure Automation Python 3-Paket. |
> | Aktion | Microsoft.Automation/automationAccounts/read | Ruft ein Azure Automation-Konto ab. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/content/read | Ruft den Inhalt eines Azure Automation-Runbooks ab. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/delete | Löscht ein Azure Automation-Runbook. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Erstellt den Inhalt eines Azure Automation-Runbookentwurfs. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Ruft die Vorgangsergebnisse des Azure Automation-Runbookentwurfs ab. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/draft/read | Ruft einen Azure Automation-Runbookentwurf ab. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Ruft einen Testauftrag für einen Azure Automation-Runbookentwurf ab. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Setzt einen Testauftrag für einen Azure Automation-Runbookentwurf fort. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Beendet einen Testauftrag für einen Azure Automation-Runbookentwurf. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Hält einen Testauftrag für einen Azure Automation-Runbookentwurf an. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Erstellt einen Testauftrag für einen Azure Automation-Runbookentwurf. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Macht die Bearbeitung eines Azure Automation-Runbookentwurfs rückgängig. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Ruft die Anzahl der Azure Automation-Runbooks ab. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/publish/action | Veröffentlicht einen Azure Automation-Runbookentwurf. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/read | Ruft ein Azure Automation-Runbook ab. |
> | Aktion | Microsoft.Automation/automationAccounts/runbooks/write | Erstellt oder aktualisiert ein Azure Automation-Runbook. |
> | Aktion | Microsoft.Automation/automationAccounts/schedules/delete | Löscht ein Azure Automation-Zeitplanasset. |
> | Aktion | Microsoft.Automation/automationAccounts/schedules/getCount/action | Ruft die Anzahl der Azure Automation-Zeitpläne ab. |
> | Aktion | Microsoft.Automation/automationAccounts/schedules/read | Ruft ein Azure Automation-Zeitplanasset ab. |
> | Aktion | Microsoft.Automation/automationAccounts/schedules/write | Erstellt oder aktualisiert ein Azure Automation-Zeitplanasset. |
> | Aktion | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Löscht die Konfiguration zur Aktualisierung der Azure Automation-Software. |
> | Aktion | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Ruft die Konfiguration zur Aktualisierung der Azure Automation-Software ab. |
> | Aktion | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Erstellt oder aktualisiert die Konfiguration zur Aktualisierung der Azure Automation-Software. |
> | Aktion | Microsoft.Automation/automationAccounts/statistics/read | Ruft die Statistik für Azure Automation ab. |
> | Aktion | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Ruft einen Bereitstellungscomputer für Azure Automation-Updates ab. |
> | Aktion | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Ruft einen Patchauftrag für die Azure Automation-Updateverwaltung ab. |
> | Aktion | Microsoft.Automation/automationAccounts/usages/read | Ruft die Azure Automation-Nutzung ab. |
> | Aktion | Microsoft.Automation/automationAccounts/variables/delete | Löscht ein Azure Automation-Variablenasset. |
> | Aktion | Microsoft.Automation/automationAccounts/variables/read | Liest ein Azure Automation-Variablenasset. |
> | Aktion | Microsoft.Automation/automationAccounts/variables/write | Erstellt oder aktualisiert ein Azure Automation-Variablenasset. |
> | Aktion | Microsoft.Automation/automationAccounts/watchers/delete | Löscht einen Azure Automation Watcher-Auftrag. |
> | Aktion | Microsoft.Automation/automationAccounts/watchers/read | Ruft einen Azure Automation Watcher-Auftrag ab. |
> | Aktion | Microsoft.Automation/automationAccounts/watchers/start/action | Startet einen Azure Automation Watcher-Auftrag. |
> | Aktion | Microsoft.Automation/automationAccounts/watchers/stop/action | Beendet einen Azure Automation Watcher-Auftrag. |
> | Aktion | Microsoft.Automation/automationAccounts/watchers/streams/read | Ruft einen Azure Automation Watcher-Auftragsdatenstrom ab. |
> | Aktion | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Löscht Aktionen eines Azure Automation Watcher-Auftrags. |
> | Aktion | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Ruft Aktionen eines Azure Automation Watcher-Auftrags ab. |
> | Aktion | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Erstellt Aktionen eines Azure Automation Watcher-Auftrags. |
> | Aktion | Microsoft.Automation/automationAccounts/watchers/write | Erstellt einen Azure Automation Watcher-Auftrag. |
> | Aktion | Microsoft.Automation/automationAccounts/webhooks/action | Generiert einen URI für einen Azure Automation-Webhook. |
> | Aktion | Microsoft.Automation/automationAccounts/webhooks/delete | Löscht einen Azure Automation-Webhook.  |
> | Aktion | Microsoft.Automation/automationAccounts/webhooks/read | Liest einen Azure Automation-Webhook. |
> | Aktion | Microsoft.Automation/automationAccounts/webhooks/write | Erstellt oder aktualisiert einen Azure Automation-Webhook. |
> | Aktion | Microsoft.Automation/automationAccounts/write | Erstellt oder aktualisiert ein Azure Automation-Konto. |
> | Aktion | Microsoft.Automation/operations/read | Ruft verfügbare Vorgänge für Azure Automation-Ressourcen ab. |
> | Aktion | Microsoft.Automation/register/action | Registriert das Abonnement für Azure Automation. |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Löscht eine B2C-Verzeichnisressource. |
> | Aktion | Microsoft.AzureActiveDirectory/b2cDirectories/read | Zeigt eine B2C-Verzeichnisressource an. |
> | Aktion | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C-Verzeichnisressource erstellen oder aktualisieren |
> | Aktion | Microsoft.AzureActiveDirectory/b2ctenants/read | Listet alle B2C-Mandanten auf, in denen der Benutzer Mitglied ist. |
> | Aktion | Microsoft.AzureActiveDirectory/operations/read | Hiermit lesen Sie alle API-Vorgänge, die für den Microsoft.AzureActiveDirectory-Ressourcenanbieter verfügbar sind. |
> | Aktion | Microsoft.AzureActiveDirectory/register/action | Hiermit registrieren Sie das Abonnement für den Microsoft.AzureActiveDirectory-Ressourcenanbieter. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.AzureStack/Operations/read | Ruft die Eigenschaften für einen Vorgang eines Ressourcenanbieters ab. |
> | Aktion | Microsoft.AzureStack/register/action | Registriert ein Abonnement beim Microsoft.AzureStack-Ressourcenanbieter. |
> | Aktion | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Löscht ein Azure Stack-Kundenabonnement. |
> | Aktion | Microsoft.AzureStack/registrations/customerSubscriptions/read | Ruft die Eigenschaften eines Azure Stack-Kundenabonnements ab. |
> | Aktion | Microsoft.AzureStack/registrations/customerSubscriptions/write | Erstellt oder aktualisiert ein Azure Stack-Kundenabonnement. |
> | Aktion | Microsoft.AzureStack/registrations/delete | Löscht eine Azure Stack-Registrierung. |
> | Aktion | Microsoft.AzureStack/registrations/getActivationKey/action | Ruft den neuesten Azure Stack-Aktivierungsschlüssel ab. |
> | Aktion | Microsoft.AzureStack/registrations/products/listDetails/action | Ruft erweiterte Details für ein Azure Stack-Marketplace-Produkt ab. |
> | Aktion | Microsoft.AzureStack/registrations/products/read | Ruft die Eigenschaften eines Azure Stack-Marketplace-Produkts ab. |
> | Aktion | Microsoft.AzureStack/registrations/read | Ruft die Eigenschaften einer Azure Stack-Registrierung ab. |
> | Aktion | Microsoft.AzureStack/registrations/write | Erstellt oder aktualisiert eine Azure Stack-Registrierung. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Batch/batchAccounts/applications/delete | Löscht eine Anwendung. |
> | Aktion | Microsoft.Batch/batchAccounts/applications/read | Listet Anwendungen auf oder ruft die Eigenschaften einer Anwendung ab. |
> | Aktion | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Aktiviert ein Anwendungspaket. |
> | Aktion | Microsoft.Batch/batchAccounts/applications/versions/delete | Löscht ein Anwendungspaket. |
> | Aktion | Microsoft.Batch/batchAccounts/applications/versions/read | Ruft die Eigenschaften eines Anwendungspakets ab. |
> | Aktion | Microsoft.Batch/batchAccounts/applications/versions/write | Erstellt ein neues Anwendungspaket oder aktualisiert ein vorhandenes Anwendungspaket. |
> | Aktion | Microsoft.Batch/batchAccounts/applications/write | Erstellt eine neue Anwendung oder aktualisiert eine vorhandene Anwendung. |
> | Aktion | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Ruft die Ergebnisse eines lange andauernden Zertifikatvorgangs für ein Batch-Konto ab. |
> | Aktion | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Bricht eine fehlerhafte Löschung eines Zertifikats für ein Batch-Konto ab. |
> | Aktion | Microsoft.Batch/batchAccounts/certificates/delete | Löscht ein Zertifikat aus einem Batch-Konto. |
> | Aktion | Microsoft.Batch/batchAccounts/certificates/read | Listet die Zertifikate für ein Batch-Konto auf oder ruft die Eigenschaften eines Zertifikats ab. |
> | Aktion | Microsoft.Batch/batchAccounts/certificates/write | Erstellt ein neues Zertifikat für ein Batch-Konto oder aktualisiert ein vorhandenes Zertifikat. |
> | Aktion | Microsoft.Batch/batchAccounts/delete | Löscht ein Batch-Konto. |
> | Aktion | Microsoft.Batch/batchAccounts/listkeys/action | Listet Zugriffsschlüssel für ein Batch-Konto auf. |
> | Aktion | Microsoft.Batch/batchAccounts/operationResults/read | Ruft die Ergebnisse eines lange andauernden Vorgangs im Batch-Konto ab. |
> | Aktion | Microsoft.Batch/batchAccounts/poolOperationResults/read | Ruft die Ergebnisse eines lange andauernden Poolvorgangs für ein Batch-Konto ab. |
> | Aktion | Microsoft.Batch/batchAccounts/pools/delete | Löscht einen Pool aus einem Batch-Konto. |
> | Aktion | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Deaktiviert die automatische Skalierung für einen Pool des Batch-Kontos. |
> | Aktion | Microsoft.Batch/batchAccounts/pools/read | Listet Pools für ein Batch-Konto auf oder ruft die Eigenschaften eines Pools ab. |
> | Aktion | Microsoft.Batch/batchAccounts/pools/stopResize/action | Beendet einen aktiven Größenänderungsvorgang für einen Pool des Batch-Kontos. |
> | Aktion | Microsoft.Batch/batchAccounts/pools/write | Erstellt einen neuen Pool für ein Batch-Konto oder aktualisiert einen vorhandenen Pool. |
> | Aktion | Microsoft.Batch/batchAccounts/read | Listet Batch-Konten auf oder ruft die Eigenschaften eines Batch-Kontos ab. |
> | Aktion | Microsoft.Batch/batchAccounts/regeneratekeys/action | Generiert Zugriffsschlüssel für ein Batch-Konto neu. |
> | Aktion | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synchronisiert Zugriffsschlüssel für das automatische Speicherkonto, das für ein Batch-Konto konfiguriert ist. |
> | Aktion | Microsoft.Batch/batchAccounts/write | Erstellt ein neues Batch-Konto oder aktualisiert ein vorhandenes Batch-Konto. |
> | Aktion | Microsoft.Batch/locations/accountOperationResults/read | Ruft die Ergebnisse eines lange andauernden Vorgangs im Batch-Konto ab. |
> | Aktion | Microsoft.Batch/locations/checkNameAvailability/action | Prüft, ob der Kontoname gültig ist und noch nicht verwendet wird. |
> | Aktion | Microsoft.Batch/locations/quotas/read | Ruft Batch-Kontingente des angegebenen Abonnements in der angegebenen Azure-Region ab. |
> | Aktion | Microsoft.Batch/operations/read | Listet die verfügbaren Vorgänge für den Microsoft.Batch-Ressourcenanbieter auf. |
> | Aktion | Microsoft.Batch/register/action | Registriert das Abonnement für den Batch-Ressourcenanbieter und ermöglicht die Erstellung von Batch-Konten. |
> | Aktion | Microsoft.Batch/unregister/action | Hebt die Registrierung für das Abonnement für den Batch-Ressourcenanbieter auf und verhindert so die Erstellung von Batch-Konten. |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Billing/billingAccounts/departments/read | Listet alle Abteilungen eines Abrechnungskontobereichs auf. |
> | Aktion | Microsoft.Billing/billingAccounts/enrollmentAccounts/read | Listet alle Registrierungskonten unter einem Abrechnungskontobereich auf. |
> | Aktion | Microsoft.Billing/billingAccounts/read | Listet alle Abrechnungskonten auf, auf die der Benutzer Zugriff hat. |
> | Aktion | Microsoft.Billing/billingPeriods/read | Listet die verfügbaren Abrechnungszeiträume auf. |
> | Aktion | Microsoft.Billing/departments/read | Listet alle Abteilungen auf, auf die der Benutzer Zugriff hat. |
> | Aktion | Microsoft.Billing/invoices/read | Listet verfügbare Rechnungen auf. |
> | Aktion | Microsoft.Billing/register/action | Registriert ein Abonnement bei einem Microsoft.Billing-Ressourcenanbieter. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.BingMaps/mapApis/Delete | Löschvorgang |
> | Aktion | Microsoft.BingMaps/mapApis/listSecrets/action | Dient zum Auflisten der Geheimnisse. |
> | Aktion | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Dient zum Lesen des SSO-Autorisierungstokens für die Ressource. |
> | Aktion | Microsoft.BingMaps/mapApis/Read | Lesevorgang |
> | Aktion | Microsoft.BingMaps/mapApis/regenerateKey/action | Generiert den Schlüssel neu. |
> | Aktion | Microsoft.BingMaps/mapApis/Write | Schreibvorgang |
> | Aktion | Microsoft.BingMaps/Operations/read | Beschreibung des Vorgangs. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Blockchain/blockchainMembers/delete | Löscht ein vorhandenes Blockchainmitglied |
> | Aktion | Microsoft.Blockchain/blockchainMembers/read | Ruft die vorhandenen Blockchainmitglieder ab oder listet sie auf |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Stellt eine Verbindung mit dem Transaktionsknoten eines Blockchainmitglieds her |
> | Aktion | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Löscht einen vorhandenen Transaktionsknoten eines Blockchainmitglieds |
> | Aktion | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Ruft die vorhandenen Transaktionsknoten eines Blockchainmitglieds ab oder listet sie auf |
> | Aktion | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Erstellt oder aktualisiert einen Transaktionsknoten eines Blockchainmitglieds |
> | Aktion | Microsoft.Blockchain/blockchainMembers/write | Erstellt oder aktualisiert ein Blockchainmitglied |
> | Aktion | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Ruft die Vorgangsergebnisse von Blockchainmitgliedern ab |
> | Aktion | Microsoft.Blockchain/locations/checkNameAvailability/action | Prüft, ob der Ressourcenname gültig ist und noch nicht verwendet wird |
> | Aktion | Microsoft.Blockchain/operations/read | Listet alle Vorgänge im Microsoft Blockchain-Ressourcenanbieter auf |
> | Aktion | Microsoft.Blockchain/register/action | Registriert das Abonnement für den Blockchain-Ressourcenanbieter. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Liest alle Blaupausenartefakte. |
> | Aktion | Microsoft.Blueprint/blueprintAssignments/delete | Löscht alle Blaupausenartefakte. |
> | Aktion | Microsoft.Blueprint/blueprintAssignments/read | Liest alle Blaupausenartefakte. |
> | Aktion | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Ruft die Azure Blueprints-Dienstprinzipalobjekt-ID ab. |
> | Aktion | Microsoft.Blueprint/blueprintAssignments/write | Erstellt oder aktualisiert alle Blaupausenartefakte. |
> | Aktion | Microsoft.Blueprint/blueprints/artifacts/delete | Löscht alle Blaupausenartefakte. |
> | Aktion | Microsoft.Blueprint/blueprints/artifacts/read | Liest alle Blaupausenartefakte. |
> | Aktion | Microsoft.Blueprint/blueprints/artifacts/write | Erstellt oder aktualisiert alle Blaupausenartefakte. |
> | Aktion | Microsoft.Blueprint/blueprints/delete | Löscht alle Blaupausen. |
> | Aktion | Microsoft.Blueprint/blueprints/read | Liest alle Blaupausen. |
> | Aktion | Microsoft.Blueprint/blueprints/versions/artifacts/read | Liest alle Blaupausenartefakte. |
> | Aktion | Microsoft.Blueprint/blueprints/versions/delete | Löscht alle Blaupausen. |
> | Aktion | Microsoft.Blueprint/blueprints/versions/read | Liest alle Blaupausen. |
> | Aktion | Microsoft.Blueprint/blueprints/versions/write | Erstellt oder aktualisiert alle Blaupausen. |
> | Aktion | Microsoft.Blueprint/blueprints/write | Erstellt oder aktualisiert alle Blaupausen. |
> | Aktion | Microsoft.Blueprint/register/action | Registriert den Azure-Blaupausenressourcenanbieter. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.BotService/botServices/channels/delete | Löscht einen Botdienst. |
> | Aktion | Microsoft.BotService/botServices/channels/read | Liest einen Botdienst. |
> | Aktion | Microsoft.BotService/botServices/channels/write | Schreibt einen Botdienst. |
> | Aktion | Microsoft.BotService/botServices/connections/delete | Löscht einen Botdienst. |
> | Aktion | Microsoft.BotService/botServices/connections/read | Liest einen Botdienst. |
> | Aktion | Microsoft.BotService/botServices/connections/write | Schreibt einen Botdienst. |
> | Aktion | Microsoft.BotService/botServices/delete | Löscht einen Botdienst. |
> | Aktion | Microsoft.BotService/botServices/read | Liest einen Botdienst. |
> | Aktion | Microsoft.BotService/botServices/write | Schreibt einen Botdienst. |
> | Aktion | Microsoft.BotService/locations/operationresults/read | Liest den Status eines asynchronen Vorgangs. |
> | Aktion | Microsoft.BotService/Operations/read | Liest die Vorgänge für alle Ressourcentypen. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Cache/checknameavailability/action | Prüft, ob ein Name für die Verwendung mit einer neuen Redis Cache-Instanz verfügbar ist. |
> | Aktion | Microsoft.Cache/locations/operationresults/read | Ruft das Ergebnis eines lange andauernden Vorgangs ab, dessen Location-Header zuvor an den Client zurückgegeben wurde. |
> | Aktion | Microsoft.Cache/operations/read | Listet die Vorgänge auf, die der Microsoft.Cache-Anbieter unterstützt. |
> | Aktion | Microsoft.Cache/redis/delete | Dient zum Löschen der gesamten Redis Cache-Instanz. |
> | Aktion | Microsoft.Cache/redis/export/action | Dient dazu, Redis-Daten im angegebenen Format in Speicherblobs mit Präfix zu exportieren. |
> | Aktion | Microsoft.Cache/redis/firewallRules/delete | Dient zum Löschen der IP-Firewallregeln einer Redis Cache-Instanz. |
> | Aktion | Microsoft.Cache/redis/firewallRules/read | Dient zum Abrufen der IP-Firewallregeln einer Redis Cache-Instanz. |
> | Aktion | Microsoft.Cache/redis/firewallRules/write | Dient zum Bearbeiten der IP-Firewallregeln einer Redis Cache-Instanz. |
> | Aktion | Microsoft.Cache/redis/forceReboot/action | Dient zum Erzwingen des Neustarts einer Cache-Instanz (ggf. mit Datenverlust). |
> | Aktion | Microsoft.Cache/redis/import/action | Dient zum Importieren von Daten eines angegebenen Formats aus mehreren Blobs in Redis. |
> | Aktion | Microsoft.Cache/redis/linkedservers/delete | Dient zum Löschen von Verbindungsservern aus einer Redis Cache-Instanz. |
> | Aktion | Microsoft.Cache/redis/linkedservers/read | Dient zum Abrufen von Verbindungsservern, die einer Redis Cache-Instanz zugeordnet sind. |
> | Aktion | Microsoft.Cache/redis/linkedservers/write | Dient zum Hinzufügen von Verbindungsservern zu einer Redis Cache-Instanz. |
> | Aktion | Microsoft.Cache/redis/listKeys/action | Dient zum Anzeigen des Werts von Redis Cache-Zugriffsschlüsseln im Verwaltungsportal. |
> | Aktion | Microsoft.Cache/redis/listUpgradeNotifications/read | Dient zum Auflisten der neuesten Upgradebenachrichtigungen für den Cache-Mandanten. |
> | Aktion | Microsoft.Cache/redis/metricDefinitions/read | Ruft die verfügbaren Metriken für eine Redis Cache-Instanz ab. |
> | Aktion | Microsoft.Cache/redis/patchSchedules/delete | Dient zum Löschen des Patchzeitplans einer Redis Cache-Instanz. |
> | Aktion | Microsoft.Cache/redis/patchSchedules/read | Ruft den Patchzeitplan einer Redis Cache-Instanz ab. |
> | Aktion | Microsoft.Cache/redis/patchSchedules/write | Dient zum Ändern des Patchzeitplans einer Redis Cache-Instanz. |
> | Aktion | Microsoft.Cache/redis/read | Dient zum Anzeigen der Redis Cache-Einstellungen und -Konfiguration im Verwaltungsportal. |
> | Aktion | Microsoft.Cache/redis/regenerateKey/action | Dient zum Ändern des Werts von Redis Cache-Zugriffsschlüsseln im Verwaltungsportal. |
> | Aktion | Microsoft.Cache/redis/start/action | Dient zum Starten einer Cache-Instanz. |
> | Aktion | Microsoft.Cache/redis/stop/action | Dient zum Beenden einer Cache-Instanz. |
> | Aktion | Microsoft.Cache/redis/write | Dient zum Ändern der Redis Cache-Einstellungen und -Konfiguration im Verwaltungsportal. |
> | Aktion | Microsoft.Cache/register/action | Registriert den Ressourcenanbieter „Microsoft.Cache“ bei einem Abonnement. |
> | Aktion | Microsoft.Cache/unregister/action | Hebt die Registrierung des Ressourcenanbieters „Microsoft.Cache“ bei einem Abonnement auf. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Capacity/appliedreservations/read | Liest alle Reservierungen. |
> | Aktion | Microsoft.Capacity/calculateexchange/action | Berechnet den Wechselbetrag und den Preis für den neuen Kauf und gibt Richtlinienfehler zurück |
> | Aktion | Microsoft.Capacity/calculateprice/action | Berechnet Reservierungspreise. |
> | Aktion | Microsoft.Capacity/catalogs/read | Liest den Katalog mit den Reservierungen. |
> | Aktion | Microsoft.Capacity/checkoffers/action | Überprüft Abonnementangebote. |
> | Aktion | Microsoft.Capacity/checkscopes/action | Überprüft Abonnements. |
> | Aktion | Microsoft.Capacity/commercialreservationorders/read | Ruft Reservierungsaufträge ab, die unter Mandanten erstellt wurden. |
> | Aktion | Microsoft.Capacity/exchange/action | Tauscht Reservierungen aus |
> | Aktion | Microsoft.Capacity/operations/read | Liest Vorgänge. |
> | Aktion | Microsoft.Capacity/register/action | Registriert den Kapazitätsressourcenanbieter und ermöglicht die Erstellung von Kapazitätsressourcen. |
> | Aktion | Microsoft.Capacity/reservationorders/action | Aktualisiert eine Reservierung. |
> | Aktion | Microsoft.Capacity/reservationorders/availablescopes/action | Sucht nach verfügbaren Bereichen. |
> | Aktion | Microsoft.Capacity/reservationorders/calculaterefund/action | Berechnet den Erstattungsbetrag und den Preis für den neuen Kauf und gibt Richtlinienfehler zurück. |
> | Aktion | Microsoft.Capacity/reservationorders/delete | Löscht eine Reservierung. |
> | Aktion | Microsoft.Capacity/reservationorders/merge/action | Führt Reservierungen zusammen. |
> | Aktion | Microsoft.Capacity/reservationorders/read | Liest alle Reservierungen. |
> | Aktion | Microsoft.Capacity/reservationorders/reservations/action | Aktualisiert eine Reservierung. |
> | Aktion | Microsoft.Capacity/reservationorders/reservations/delete | Löscht eine Reservierung. |
> | Aktion | Microsoft.Capacity/reservationorders/reservations/read | Liest alle Reservierungen. |
> | Aktion | Microsoft.Capacity/reservationorders/reservations/revisions/read | Liest alle Reservierungen. |
> | Aktion | Microsoft.Capacity/reservationorders/reservations/write | Erstellt eine Reservierung. |
> | Aktion | Microsoft.Capacity/reservationorders/return/action | Gibt Reservierungen zurück. |
> | Aktion | Microsoft.Capacity/reservationorders/split/action | Teilt Reservierungen. |
> | Aktion | Microsoft.Capacity/reservationorders/swap/action | Tauscht Reservierungen aus. |
> | Aktion | Microsoft.Capacity/reservationorders/write | Erstellt eine Reservierung. |
> | Aktion | Microsoft.Capacity/tenants/register/action | Registriert Mandanten. |
> | Aktion | Microsoft.Capacity/unregister/action | Hebt die Registrierung von Mandanten auf. |
> | Aktion | Microsoft.Capacity/validatereservationorder/action | Überprüft Reservierungen. |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Aktion | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Aktion | Microsoft.Cdn/edgenodes/delete |  |
> | Aktion | Microsoft.Cdn/edgenodes/read |  |
> | Aktion | Microsoft.Cdn/edgenodes/write |  |
> | Aktion | Microsoft.Cdn/operationresults/delete |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Aktion | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Aktion | Microsoft.Cdn/operationresults/read |  |
> | Aktion | Microsoft.Cdn/operationresults/write |  |
> | Aktion | Microsoft.Cdn/operations/read |  |
> | Aktion | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Aktion | Microsoft.Cdn/profiles/delete |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/read |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Aktion | Microsoft.Cdn/profiles/endpoints/write |  |
> | Aktion | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Aktion | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Aktion | Microsoft.Cdn/profiles/read |  |
> | Aktion | Microsoft.Cdn/profiles/write |  |
> | Aktion | Microsoft.Cdn/register/action | Hiermit wird das Abonnement für den CDN-Ressourcenanbieter registriert und die Erstellung von CDN-Profilen aktiviert. |
> | Aktion | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Dient zum Löschen eines vorhandenen Zertifikats. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Dient zum Abrufen der Zertifikatliste. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Dient zum Hinzufügen eines neuen Zertifikats oder zum Aktualisieren eines bereits vorhandenen. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/Delete | Dient zum Löschen eines vorhandenen AppServiceCertificate-Elements. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/Read | Dient zum Abrufen der CertificateOrders-Liste. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Dient zum erneuten Ausstellen einer vorhandenen Zertifikatbestellung. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Dient zum Verlängern einer vorhandenen Zertifikatbestellung. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Dient zum erneuten Senden von Zertifikat-E-Mails. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Dient zum erneuten Senden von Anforderungs-E-Mails an eine andere E-Mail-Adresse. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Dient zum Abrufen eines Websitesiegels für ein ausgestelltes App Service-Zertifikat. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Dient zum Abrufen der Liste mit Zertifikataktionen. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Dient zum Abrufen des Zertifikat-E-Mail-Verlaufs. |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Überprüfen des Domänenbesitzes |
> | Aktion | Microsoft.CertificateRegistration/certificateOrders/Write | Dient zum Hinzufügen eines neuen certificateOrder-Elements oder zum Aktualisieren eines bereits vorhandenen. |
> | Aktion | Microsoft.CertificateRegistration/operations/Read | Dient zum Auflisten aller Vorgänge über die App Service-Zertifikatregistrierung. |
> | Aktion | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Dient zum Bereitstellen eines Dienstprinzipals für einen Dienst-App-Prinzipal. |
> | Aktion | Microsoft.CertificateRegistration/register/action | Dient zum Registrieren des Microsoft-Zertifikatressourcenanbieters für das Abonnement. |
> | Aktion | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Dient zum Überprüfen eines Zertifikaterwerbsobjekts, ohne es zu übermitteln. |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ClassicCompute/capabilities/read | Zeigt die Funktionen an. |
> | Aktion | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Prüft die Verfügbarkeit eines angegebenen Domänennamens. |
> | Aktion | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Überprüft die Verfügbarkeit eines angegebenen Domänennamens. |
> | Aktion | Microsoft.ClassicCompute/domainNames/active/write | Legt den aktiven Domänennamen fest. |
> | Aktion | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Dient zum Anzeigen der Verfügbarkeitsgruppe für die Ressource. |
> | Aktion | Microsoft.ClassicCompute/domainNames/capabilities/read | Zeigt die Domänennamenfunktionen an. |
> | Aktion | Microsoft.ClassicCompute/domainNames/delete | Dient zum Entfernen der Domänennamen für Ressourcen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Zeigt die Bereitstellungsslots an. |
> | Aktion | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Dient zum Abrufen der Rolle auf dem Bereitstellungsslot des Domänennamens. |
> | Aktion | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Dient zum Abrufen der Rolleninstanz der Rolle auf dem Bereitstellungsslot des Domänennamens. |
> | Aktion | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Ruft den Zustand des Bereitstellungsslots ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Fügt den Zustand des Bereitstellungsslots hinzu. |
> | Aktion | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Dient zum Abrufen der Upgradedomäne für den Bereitstellungsslot auf dem Domänennamen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Dient zum Aktualisieren der Upgradedomäne für den Bereitstellungsslot auf dem Domänennamen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Erstellt oder aktualisiert die Bereitstellung. |
> | Aktion | Microsoft.ClassicCompute/domainNames/extensions/delete | Dient zum Entfernen der Domänennamenerweiterungen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Liest den Vorgangsstatus für die Domänennamenerweiterungen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/extensions/read | Gibt die Domänennamenerweiterungen zurück. |
> | Aktion | Microsoft.ClassicCompute/domainNames/extensions/write | Dient zum Hinzufügen der Domänennamenerweiterungen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Dient zum Entfernen eines neuen internen Lastenausgleichsmoduls. |
> | Aktion | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Liest den Vorgangsstatus für die internen Lastenausgleichsmodule der Domänennamen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Ruft die internen Lastenausgleichsmodule ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Erstellt ein neues internes Lastenausgleichsmodul. |
> | Aktion | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Liest den Vorgangsstatus für die Endpunktgruppen mit Lastenausgleich der Domänennamen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Ruft Endpunktgruppen mit Lastenausgleich ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Fügt die Endpunktgruppe mit Lastenausgleich hinzu. |
> | Aktion | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Ruft den Vorgangsstatus für den Domänennamen ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Liest den Vorgangsstatus für die Domänennamenerweiterungen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/read | Dient zum Zurückgeben der Domänennamen für Ressourcen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Dient zum Löschen der verwendeten Dienstzertifikate. |
> | Aktion | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Liest den Vorgangsstatus für die Dienstzertifikate der Domänennamen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Gibt die verwendeten Dienstzertifikate zurück. |
> | Aktion | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Dient zum Hinzufügen oder Ändern der verwendeten Dienstzertifikate. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Bricht die Migration eines Bereitstellungsslots ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Führt für die Migration eines Bereitstellungsslots einen Commit durch. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/delete | Löscht einen angegebenen Bereitstellungsslot. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Liest den Vorgangsstatus für die Domänennamenslots. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Bereitet die Migration eines Bereitstellungsslots vor. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/read | Zeigt die Bereitstellungsslots an. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Dient zum Entfernen des Erweiterungsverweises für die Rolle „Bereitstellungsslot“. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Liest den Vorgangsstatus für die Slotrollen-Erweiterungsverweise der Domänennamen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Gibt den Erweiterungsverweis für die Rolle „Bereitstellungsslot“ zurück. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Dient zum Hinzufügen oder Ändern des Erweiterungsverweises für die Rolle „Bereitstellungsslot“. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Ruft die Rollenmetrikdefinition für den Domänennamen ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Ruft die Rollenmetrik für den Domänennamen ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Ruft den Vorgangsstatus für die Rolle „Domänennamenslot“ ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Dient zum Abrufen der Diagnoseeinstellungen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Dient zum Hinzufügen oder Ändern der Diagnoseeinstellungen. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Ruft die Metrikdefinitionen ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/read | Dient zum Abrufen der Rolle für den Bereitstellungsslot. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Lädt eine Remotedesktop-Verbindungsdatei für die Rolleninstanz der Rolle „Domänennamenslot“ herunter. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Ruft den Vorgangsstatus für die Rolleninstanz der Rolle „Domänennamenslot“ ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Dient zum Abrufen der Rolleninstanz. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Hiermit wird die Rolleninstanz neu erstellt. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Führt ein Reimaging der Rolleninstanz aus. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Startet Rolleninstanzen neu. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Ruft die Rollen-SKU für den Bereitstellungsslot ab. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/roles/write | Fügt die Rolle für den Bereitstellungsslot hinzu. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/start/action | Startet einen Bereitstellungsslot. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Ändert den Zustand des Bereitstellungsslots in „Beendet“. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Ändert den Zustand des Bereitstellungsslots in „Gestartet“. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/stop/action | Hält den Bereitstellungsslot an. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Dient zum Aufrüsten der Domäne. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Überprüft die Migration eines Bereitstellungsslots. |
> | Aktion | Microsoft.ClassicCompute/domainNames/slots/write | Erstellt oder aktualisiert die Bereitstellung. |
> | Aktion | Microsoft.ClassicCompute/domainNames/swap/action | Vertauscht Stagingslot und Produktionsslot. |
> | Aktion | Microsoft.ClassicCompute/domainNames/write | Dient zum Hinzufügen oder Ändern der Domänennamen für Ressourcen. |
> | Aktion | Microsoft.ClassicCompute/moveSubscriptionResources/action | Dient zum Verschieben aller klassischen Ressourcen in ein anderes Abonnement. |
> | Aktion | Microsoft.ClassicCompute/operatingSystemFamilies/read | Listet die in Microsoft Azure verfügbaren Gastbetriebssystemfamilien sowie die für jede Familie verfügbaren Betriebssystemversionen auf. |
> | Aktion | Microsoft.ClassicCompute/operatingSystems/read | Listet die Versionen des Gastbetriebssystems auf, die derzeit in Microsoft Azure verfügbar sind. |
> | Aktion | Microsoft.ClassicCompute/operations/read | Ruft die Liste der Vorgänge ab. |
> | Aktion | Microsoft.ClassicCompute/operationStatuses/read | Liest den Vorgangsstatus für die Ressource. |
> | Aktion | Microsoft.ClassicCompute/quotas/read | Dient zum Abrufen des Kontingents für das Abonnement. |
> | Aktion | Microsoft.ClassicCompute/register/action | Dient zum Registrieren für klassische Computeressourcen. |
> | Aktion | Microsoft.ClassicCompute/resourceTypes/skus/read | Ruft die SKU-Liste für unterstützte Ressourcentypen ab. |
> | Aktion | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Dient zum Überprüfen der Verfügbarkeit des Abonnements für einen klassischen Verschiebevorgang. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Löscht die Netzwerksicherheitsgruppe, die dem virtuellen Computer zugeordnet ist. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Liest den Vorgangsstatus für die den virtuellen Computern zugeordneten Netzwerksicherheitsgruppen. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Ruft die Netzwerksicherheitsgruppe ab, die dem virtuellen Computer zugeordnet ist. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Fügt eine Netzwerksicherheitsgruppe hinzu, die dem virtuellen Computer zugeordnet ist. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Ruft die möglichen asynchronen Vorgänge ab. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Fügt einem virtuellen Computer einen Datenträger an. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/capture/action | Erfasst einen virtuellen Computer. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/delete | Entfernt virtuelle Computer. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Trennt einen Datenträger von einem virtuellen Computer. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Ruft VM-Diagnoseeinstellungen ab. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/disks/read | Ruft die Liste mit den Datenträgern ab. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Lädt die RDP-Datei für einen virtuellen Computer herunter. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Liest den Vorgangsstatus für die VM-Erweiterungen. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/extensions/read | Ruft die VM-Erweiterung ab. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/extensions/write | Legt die VM-Erweiterung fest. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Ruft die VM-Metrikdefinition ab. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/metrics/read | Ruft die Metriken ab. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Löscht die Netzwerksicherheitsgruppe, die der Netzwerkschnittstelle zugeordnet ist. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Liest den Vorgangsstatus für die den virtuellen Computern zugeordneten Netzwerksicherheitsgruppen. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Ruft die Netzwerksicherheitsgruppe ab, die der Netzwerkschnittstelle zugeordnet ist. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Fügt eine Netzwerksicherheitsgruppe hinzu, die der Netzwerkschnittstelle zugeordnet ist. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Liest den Vorgangsstatus für die virtuellen Computer. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Hiermit wird eine Wartung für den virtuellen Computer durchgeführt. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Dient zum Abrufen der Diagnoseeinstellungen. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Dient zum Hinzufügen oder Ändern der Diagnoseeinstellungen. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Ruft die Metrikdefinitionen ab. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/read | Ruft eine Liste mit virtuellen Computern ab. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Stellt den virtuellen Computer erneut bereit. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/restart/action | Startet virtuelle Computer neu. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Fährt den virtuellen Computer herunter. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/start/action | Starten Sie den virtuellen Computer. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/stop/action | Beendet den virtuellen Computer. |
> | Aktion | Microsoft.ClassicCompute/virtualMachines/write | Dient zum Hinzufügen oder Ändern virtueller Computer. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Hiermit wird der Vorgangsstatus für eine ExpressRoute-Querverbindung abgerufen. |
> | Aktion | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Hiermit wird das Peering der ExpressRoute-Querverbindung gelöscht. |
> | Aktion | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Hiermit wird der Vorgangsstatus für das Peering einer ExpressRoute-Querverbindung abgerufen. |
> | Aktion | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Hiermit wird das Peering einer ExpressRoute-Querverbindung abgerufen. |
> | Aktion | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Hiermit wird das Peering einer ExpressRoute-Querverbindung hinzugefügt. |
> | Aktion | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Hiermit werden ExpressRoute-Querverbindungen abgerufen. |
> | Aktion | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Hiermit werden ExpressRoute-Querverbindungen hinzugefügt. |
> | Aktion | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Ruft die Liste mit unterstützten Geräten ab. |
> | Aktion | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Löscht die Netzwerksicherheitsgruppe. |
> | Aktion | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Liest den Vorgangsstatus für die Netzwerksicherheitsgruppe. |
> | Aktion | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellungen für Netzwerksicherheitsgruppen ab. |
> | Aktion | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellungen für Netzwerksicherheitsgruppen. Dieser Vorgang wird durch den Insights-Ressourcenanbieter unterstützt. |
> | Aktion | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Ruft die Ereignisse für Netzwerksicherheitsgruppen ab. |
> | Aktion | Microsoft.ClassicNetwork/networkSecurityGroups/read | Ruft die Netzwerksicherheitsgruppe ab. |
> | Aktion | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Löscht die Sicherheitsregel. |
> | Aktion | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Liest den Vorgangsstatus für die Sicherheitsregeln der Netzwerksicherheitsgruppe. |
> | Aktion | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Ruft die Sicherheitsregel ab. |
> | Aktion | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Fügt eine Sicherheitsregel hinzu oder aktualisiert sie. |
> | Aktion | Microsoft.ClassicNetwork/networkSecurityGroups/write | Fügt eine neue Netzwerksicherheitsgruppe hinzu. |
> | Aktion | Microsoft.ClassicNetwork/operations/read | Hiermit werden klassische Netzwerkvorgänge abgerufen. |
> | Aktion | Microsoft.ClassicNetwork/quotas/read | Dient zum Abrufen des Kontingents für das Abonnement. |
> | Aktion | Microsoft.ClassicNetwork/register/action | Dient zum Registrieren für klassische Netzwerkressourcen. |
> | Aktion | Microsoft.ClassicNetwork/reservedIps/delete | Dient zum Löschen einer reservierten IP-Adresse. |
> | Aktion | Microsoft.ClassicNetwork/reservedIps/join/action | Dient zum Einbinden einer reservierten IP-Adresse. |
> | Aktion | Microsoft.ClassicNetwork/reservedIps/link/action | Dient zum Verknüpfen einer reservierten IP-Adresse. |
> | Aktion | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Liest den Vorgangsstatus für die reservierten IP-Adressen. |
> | Aktion | Microsoft.ClassicNetwork/reservedIps/read | Ruft die reservierten IP-Adressen ab. |
> | Aktion | Microsoft.ClassicNetwork/reservedIps/write | Dient zum Hinzufügen einer neuen reservierten IP-Adresse. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Bricht die Migration eines virtuellen Netzwerks ab. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Zeigt die Funktionen an. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Prüft die Verfügbarkeit einer bestimmten IP-Adresse in einem virtuellen Netzwerk. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Führt einen Commit für die Migration eines virtuellen Netzwerks aus. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/delete | Löscht das virtuelle Netzwerk. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Hebt die Sperrung eines Clientzertifikats auf. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Dient zum Lesen der gesperrten Clientzertifikate. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Sperrt ein Clientzertifikat. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Löscht das Clientzertifikat des virtuellen Netzwerkgateways. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Lädt ein Zertifikat nach Fingerabdruck herunter. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Listet das Zertifikatpaket für das virtuelle Netzwerkgateway auf. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Dient zum Suchen nach den Clientstammzertifikaten. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Lädt ein neues Clientstammzertifikat hoch. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Stellt eine Site-to-Site-Gatewayverbindung her. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Trennt eine Site-to-Site-Gatewayverbindung. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Ruft die Verbindungsliste ab. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testet eine Site-to-Site-Gatewayverbindung. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Löscht das virtuelle Netzwerkgateway. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Lädt das Gerätekonfigurationsskript herunter. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Lädt die Gatewaydiagnose herunter. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Ruft den Schlüssel des Verbindungsdiensts ab. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Listet das Paket für das virtuelle Netzwerkgateway auf. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Liest den Vorgangsstatus für die virtuellen Netzwerkgateways. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Ruft das Paket für das virtuelle Netzwerkgateway ab. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Ruft die virtuellen Netzwerkgateways ab. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Startet die Diagnose für das Gateway für virtuelle Netzwerke. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Beendet die Diagnose für das Gateway für virtuelle Netzwerke. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Fügt ein virtuelles Netzwerkgateway hinzu. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/join/action | Führt zum Beitritt zum virtuellen Netzwerk. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Liest den Vorgangsstatus für die virtuellen Netzwerke. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Richtet ein virtuelles Netzwerk als Peer für ein anderes virtuelles Netzwerk ein. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Bereitet die Migration eines virtuellen Netzwerks vor. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/read | Dient zum Abrufen des virtuellen Netzwerks. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Löscht den Proxy für das Peering virtueller Remotenetzwerke. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Ruft den Proxy für das Peering virtueller Remotenetzwerke ab. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Fügt den Proxy für das Peering virtueller Remotenetzwerke hinzu bzw. aktualisiert ihn. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Löscht die Netzwerksicherheitsgruppe, die dem Subnetz zugeordnet ist. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Liest den Vorgangsstatus für die dem Subnetz des virtuellen Netzwerks zugeordnete Netzwerksicherheitsgruppe. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Ruft die Netzwerksicherheitsgruppe ab, die dem Subnetz zugeordnet ist. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Fügt die Netzwerksicherheitsgruppe hinzu, die dem Subnetz zugeordnet ist. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Überprüft die Migration eines virtuellen Netzwerks. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Ruft das Peering virtueller Netzwerke ab. |
> | Aktion | Microsoft.ClassicNetwork/virtualNetworks/write | Dient zum Hinzufügen eines neuen virtuellen Netzwerks. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ClassicStorage/capabilities/read | Zeigt die Funktionen an. |
> | Aktion | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Prüft die Verfügbarkeit eines Speicherkontos. |
> | Aktion | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Hiermit wird die Verfügbarkeit eines Speicherkontos überprüft. |
> | Aktion | Microsoft.ClassicStorage/disks/read | Gibt den Speicherkontodatenträger zurück. |
> | Aktion | Microsoft.ClassicStorage/images/operationstatuses/read | Hiermit wird der Vorgangsstatus für ein Image abgerufen. |
> | Aktion | Microsoft.ClassicStorage/images/read | Gibt das Image zurück. |
> | Aktion | Microsoft.ClassicStorage/operations/read | Hiermit werden klassische Speichervorgänge abgerufen. |
> | Aktion | Microsoft.ClassicStorage/osImages/read | Gibt das Betriebssystemimage zurück. |
> | Aktion | Microsoft.ClassicStorage/osPlatformImages/read | Ruft das Image der Betriebssystemplattform ab. |
> | Aktion | Microsoft.ClassicStorage/publicImages/read | Ruft das öffentliche VM-Image ab. |
> | Aktion | Microsoft.ClassicStorage/quotas/read | Dient zum Abrufen des Kontingents für das Abonnement. |
> | Aktion | Microsoft.ClassicStorage/register/action | Dient zum Registrieren für klassischen Speicher. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Bricht die Migration eines Speicherkontos ab. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Führt den Commit für die Migration eines Speicherkontos aus. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/delete | Löschen Sie das Speicherkonto. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/disks/delete | Löscht den angegebenen Speicherkontodatenträger. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Liest den Vorgangsstatus für die Ressource. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/disks/read | Gibt den Speicherkontodatenträger zurück. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/disks/write | Fügt einen Speicherkontodatenträger hinzu. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/images/delete | Löscht das angegebene Speicherkontoimage. (Veraltet. Verwenden Sie „Microsoft.ClassicStorage/storageAccounts/vmImages“) |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Hiermit wird der Vorgangsstatus für das Speicherkontoimage zurückgegeben. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/images/read | Gibt das Speicherkontoimage zurück. (Veraltet. Verwenden Sie „Microsoft.ClassicStorage/storageAccounts/vmImages“) |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listet die Zugriffsschlüssel für die Speicherkonten auf. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Liest den Vorgangsstatus für die Ressource. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Löscht das angegebene Betriebssystemimage für das Speicherkonto. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/osImages/read | Gibt das Betriebssystemimage für das Speicherkonto zurück. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/osImages/write | Fügt das Betriebssystemimage eines bestimmten Speicherkontos hinzu. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Bereitet die Migration eines Speicherkontos vor. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/read | Dient zum Zurückgeben des Speicherkontos mit dem angegebenen Konto. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Generiert die vorhandenen Zugriffsschlüssel für das Speicherkonto neu. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Dient zum Abrufen der Diagnoseeinstellungen. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Dient zum Hinzufügen oder Ändern der Diagnoseeinstellungen. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Ruft die Metrikdefinitionen ab. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Ruft die Metriken ab. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/services/read | Dient zum Abrufen der verfügbaren Dienste. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Überprüft die Migration eines Speicherkontos. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Löscht ein bestimmtes VM-Image. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Hiermit wird der Vorgangsstatus für ein angegebenes VM-Image abgerufen. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Gibt das VM-Image zurück. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Fügt ein bestimmtes VM-Image hinzu. |
> | Aktion | Microsoft.ClassicStorage/storageAccounts/write | Fügt ein neues Speicherkonto hinzu. |
> | Aktion | Microsoft.ClassicStorage/vmImages/read | Listet die VM-Images auf. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.CognitiveServices/accounts/delete | Löscht API-Konten. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Hiermit werden menschliche Gesichter in einem Bild erkannt und Rechtecke für die Gesichter sowie optional faceIds, Sehenswürdigkeiten und Attribute zurückgegeben. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Hiermit löschen Sie eine angegebene Liste mit Gesichtern. Die zugehörigen Bilder der Gesichter in der Gesichterliste werden ebenfalls gelöscht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Hiermit löschen Sie ein Gesicht aus einer Liste mit Gesichtern anhand der angegebenen faceListId und persisitedFaceId. Auch das zugehörige Bild des Gesichts wird gelöscht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Hiermit fügen Sie ein Gesicht einer angegebenen Liste mit Gesichtern hinzu. Bis zu 1.000 Gesichter können hinzugefügt werden. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Hiermit rufen Sie die faceListId der Gesichterliste, den Namen, userData sowie Gesichter in der Gesichterliste ab. Hiermit listen Sie faceListId, Namen und userData der Gesichterliste auf. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Hiermit erstellen Sie eine leere Gesichterliste mit benutzerdefinierten Angaben für faceListId, Name und optional userData. Bis zu 64 Gesichterlisten sind zulässig. Hiermit aktualisieren Sie die Informationen einer Gesichterliste, einschließlich Name und UserData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Hiermit können Sie anhand der faceId des abgefragten Gesichts ähnliche Gesichter aus einem faceId-Array, einer Gesichterliste oder einer umfangreichen Gesichterliste suchen. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Hiermit unterteilen Sie die Gesichter der Kandidaten basierend auf der Gesichterähnlichkeit in Gruppen. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | 1:n-Identifikationsvorgang zum Ermitteln der besten Ergebnisse für das Gesicht der abgefragten Person aus einer Personengruppe oder umfangreichen Personengruppe. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Hiermit löschen Sie eine angegebene umfangreiche Gesichterliste. Die zugehörigen Bilder der Gesichter in der umfangreichen Gesichterliste werden ebenfalls gelöscht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Hiermit löschen Sie ein Gesicht aus einer umfangreichen Liste mit Gesichtern anhand der angegebenen largeFaceListId und persisitedFaceId. Auch das zugehörige Bild des Gesichts wird gelöscht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Hiermit rufen Sie das in einer umfangreichen Gesichterliste gespeicherte Gesicht über largeFaceListId und persistedFaceId ab. Hiermit nehmen Sie die persistedFaceId und userData in einer angegebenen umfangreichen Gesichterliste auf. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Hiermit fügen Sie ein Gesicht einer angegebenen umfangreichen Liste mit Gesichtern hinzu. Bis zu 1.000.000 Gesichter können hinzugefügt werden. Hiermit aktualisieren Sie das userData-Feld des angegebenen Gesichts in einer umfangreichen Gesichterliste anhand seiner persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Hiermit rufen Sie largeFaceListId, Name und userData einer umfangreichen Gesichterliste ab. Hiermit führen Sie die Informationen largeFaceListId, Name und userData einer umfangreichen Gesichterliste auf. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Hiermit senden Sie einen Trainingstask für eine umfangreiche Gesichterliste. Das Training ist ein wichtiger Schritt, den nur eine trainierte umfangreiche Gesichterliste verwenden kann. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Hiermit prüfen Sie den Trainingsstatus für die umfangreiche Gesichterliste, um zu ermitteln, ob der Vorgang abgeschlossen ist oder noch durchgeführt wird. Das LargeFaceList-Training ist ein asynchroner Vorgang. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Hiermit erstellen Sie eine leere umfangreiche Gesichterliste mit benutzerdefinierten Angaben für largeFaceListId, Name und optional userData. Hiermit aktualisieren Sie die Informationen einer umfangreichen Gesichterliste, einschließlich Name und UserData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Hiermit löschen Sie eine vorhandene umfangreiche Personengruppe mit angegebener personGroupId. In dieser umfangreichen Personengruppe gespeicherte Daten werden gelöscht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Hiermit erstellen Sie eine neue Person in einer angegebenen umfangreichen Personengruppe. Um dieser Person ein Gesicht hinzuzufügen, rufen Sie Folgendes auf: |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Hiermit löschen Sie eine vorhandene Person aus einer umfangreichen Personengruppe. Alle im Personeneintrag gespeicherten Personendaten und Gesichtsbilder werden gelöscht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Hiermit löschen Sie ein Gesicht von einer Person in einer umfangreichen Personengruppe. Die zu diesem Gesichtseintrag gehörigen Gesichtsdaten und das Bild werden ebenfalls gelöscht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Hiermit rufen Sie Informationen zum Gesicht einer Person ab. Das gespeicherte Gesicht der Person wird durch die largePersonGroupId, personId und persistedFaceId angegeben. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Hiermit fügen Sie einer Person zur Gesichtsidentifikation oder -verifizierung ein Gesichtsbild in einer umfangreichen Personengruppe hinzu. So verarbeiten Sie das Bild von Hiermit aktualisieren Sie das userData-Feld des gespeicherten Gesichts einer Person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Hiermit rufen Sie den Namen und die userData-Angabe einer Person sowie die gespeicherten faceIds ab, die dem Gesichtsbild der registrierten Person entsprechen. Hiermit listen Sie die Informationen aller Personen in der angegebenen umfangreichen Personengruppe auf, einschließlich personId, Name, userData und persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Hiermit aktualisieren Sie den Namen oder die userData-Angabe einer Person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Hiermit rufen Sie die Informationen einer umfangreichen Personengruppe ab, einschließlich Name und userData. Diese API gibt Informationen zu einer umfangreichen Personengruppe zurück. Listen Sie die largePesonGroupId, den Namen und die userData-Angabe aller vorhandenen umfangreichen Personengruppen auf. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Hiermit übermitteln Sie einen Trainingstask für eine umfangreiche Personengruppe. Das Training ist ein wichtiger Schritt, den nur eine trainierte umfangreiche Personengruppe verwenden kann. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Hiermit prüfen Sie den Trainingsstatus für die umfangreiche Personengruppe, um zu ermitteln, ob der Vorgang abgeschlossen ist oder noch durchgeführt wird. Das LargePersonGroup-Training ist ein asynchroner Vorgang. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Hiermit erstellen Sie eine neue umfangreiche Personengruppe mit benutzerdefinierten Angaben zu largePersonGroupId, Name und optional userData. Hiermit aktualisieren Sie den Namen und die userData-Angabe einer vorhandenen umfangreichen Personengruppe. Die Eigenschaften bleiben unverändert, wenn sie nicht im Anforderungstext enthalten sind. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Hiermit löschen Sie eine vorhandene Personengruppe mit angegebener personGroupId. In dieser Personengruppe gespeicherte Daten werden gelöscht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Hiermit erstellen Sie eine neue Person in einer angegebenen Personengruppe. Um dieser Person ein Gesicht hinzuzufügen, rufen Sie Folgendes auf: |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Hiermit löschen Sie eine vorhandene Person aus einer Personengruppe. Alle im Personeneintrag gespeicherten Personendaten und Gesichtsbilder werden gelöscht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Hiermit löschen Sie ein Gesicht von einer Person in einer Personengruppe. Die zu diesem Gesichtseintrag gehörigen Gesichtsdaten und das Bild werden ebenfalls gelöscht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Hiermit rufen Sie Informationen zum Gesicht einer Person ab. Das gespeicherte Gesicht der Person wird durch die personGroupId, personId und persistedFaceId angegeben. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Hiermit fügen Sie einer Person zur Gesichtsidentifikation oder -verifizierung ein Gesichtsbild in einer Personengruppe hinzu. So verarbeiten Sie das Bild von mehreren Hiermit aktualisieren Sie das userData-Feld des gespeicherten Gesichts einer Person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Hiermit rufen Sie den Namen und die userData-Angabe einer Person sowie die gespeicherten faceIds ab, die dem Gesichtsbild der registrierten Person entsprechen. Hiermit listen Sie die Informationen aller Personen in der angegebenen Personengruppe auf, einschließlich personId, Name, userData und persistedFaceIds registrierter Personen. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Hiermit aktualisieren Sie den Namen oder die userData-Angabe einer Person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Hiermit rufen Sie den Gruppennamen und die userData-Angabe der Person ab. Zum Abrufen von Informationen unter dieser personGroup verwenden Sie Hiermit listen Sie die Angaben zu personGroupId, Name und userData auf. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Hiermit übermitteln Sie einen Trainingstask für eine Personengruppe. Das Training ist ein wichtiger Schritt, den nur eine trainierte Personengruppe verwenden kann. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | Hiermit prüfen Sie den Trainingsstatus für die Personengruppe, um zu ermitteln, ob der Vorgang abgeschlossen ist oder noch durchgeführt wird. Das PersonGroup-Training ist ein asynchroner Vorgang, ausgelöst durch |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Hiermit erstellen Sie eine neue Personengruppe anhand der Angaben von personGroupId, Name und vom Benutzer bereitgestellten userData. Hiermit aktualisieren Sie den Namen und die userData-Angabe einer vorhandenen Personengruppe. Die Eigenschaften bleiben unverändert, wenn sie nicht im Anforderungstext enthalten sind. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Hiermit überprüfen Sie, ob zwei Gesichter zu derselben Person gehören oder ob je ein Gesicht zu einer Person gehört. |
> | Aktion | Microsoft.CognitiveServices/accounts/listKeys/action | Dient zum Auflisten von Schlüsseln. |
> | Aktion | Microsoft.CognitiveServices/accounts/read | Liest API-Konten. |
> | Aktion | Microsoft.CognitiveServices/accounts/regenerateKey/action | Dient zum erneuten Generieren eines Schlüssels. |
> | Aktion | Microsoft.CognitiveServices/accounts/skus/read | Liest verfügbare SKUs für eine vorhandene Ressource. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | Die API gibt eine Liste bekannter Entitäten und allgemeiner benannter Entitäten in einem bestimmten Dokument zurück (\"Person\", \"Location\", \"Organization\" usw.). |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | Die API gibt eine Liste von Zeichenfolgen zurück, die die wichtigsten Themen im Eingabetext angeben. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | Die API gibt die erkannte Sprache und eine numerische Bewertung zwischen 0 und 1 zurück. Werte nahe 1 stehen für 100 %-ige Sicherheit, dass die identifizierte Sprache stimmt. Insgesamt werden 120 Sprachen unterstützt. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | Die API gibt eine numerische Bewertung zwischen 0 und 1 zurück.<br>Werte nahe 1 zeigen eine positive Absicht an, Werte nahe 0 zeigen eine negative Absicht an.<br>Eine Bewertung von 0,5 gibt das Fehlen der Stimmung an (z. B.<br>bei einer faktischen Aussage). |
> | Aktion | Microsoft.CognitiveServices/accounts/usages/read | Dient zum Abrufen der Kontingentnutzung für eine vorhandene Ressource. |
> | Aktion | Microsoft.CognitiveServices/accounts/write | Schreibt API-Konten. |
> | Aktion | Microsoft.CognitiveServices/checkDomainAvailability/action | Liest verfügbare SKUs für ein Abonnement. |
> | Aktion | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Liest verfügbare SKUs für ein Abonnement. |
> | Aktion | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Benachrichtigung von Microsoft.Network über das Löschen virtueller Netzwerke oder Subnetze |
> | Aktion | Microsoft.CognitiveServices/Operations/read | Listet alle verfügbaren Vorgänge auf. |
> | Aktion | Microsoft.CognitiveServices/register/action | Registriert ein Abonnement für Cognitive Services. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Commerce/RateCard/read | Gibt Angebotsdaten, Metadaten für Ressourcen/Verbrauchseinheiten und Preise für das angegebene Abonnement zurück. |
> | Aktion | Microsoft.Commerce/UsageAggregates/read | Ruft die Nutzung von Microsoft Azure für ein Abonnement ab. Das Ergebnis enthält aggregierte Nutzungsdaten sowie abonnement- und ressourcenbezogene Informationen für einen bestimmten Zeitbereich. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Compute/availabilitySets/delete | Löscht die Verfügbarkeitsgruppe. |
> | Aktion | Microsoft.Compute/availabilitySets/read | Dient zum Abrufen der Eigenschaften einer Verfügbarkeitsgruppe. |
> | Aktion | Microsoft.Compute/availabilitySets/vmSizes/read | Dient zum Auflisten der Größen, die zum Erstellen oder Aktualisieren eines virtuellen Computers in der Verfügbarkeitsgruppe zur Verfügung stehen. |
> | Aktion | Microsoft.Compute/availabilitySets/write | Erstellt eine neue Verfügbarkeitsgruppe oder aktualisiert eine bereits vorhandene. |
> | Aktion | Microsoft.Compute/disks/beginGetAccess/action | Dient zum Abrufen des SAS-URIs des Datenträgers für den Blobzugriff. |
> | Aktion | Microsoft.Compute/disks/delete | Löscht den Datenträger. |
> | Aktion | Microsoft.Compute/disks/endGetAccess/action | Dient zum Sperren des SAS-URIs des Datenträgers. |
> | Aktion | Microsoft.Compute/disks/read | Dient zum Abrufen der Eigenschaften eines Datenträgers. |
> | Aktion | Microsoft.Compute/disks/write | Erstellt einen neuen Datenträger oder aktualisiert einen bereits vorhandenen. |
> | Aktion | Microsoft.Compute/galleries/delete | Hiermit wird der Katalog gelöscht. |
> | Aktion | Microsoft.Compute/galleries/images/delete | Hiermit wird das Katalogimage gelöscht. |
> | Aktion | Microsoft.Compute/galleries/images/read | Hiermit werden die Eigenschaften des Katalogimages abgerufen. |
> | Aktion | Microsoft.Compute/galleries/images/versions/delete | Hiermit wird die Katalogimageversion gelöscht. |
> | Aktion | Microsoft.Compute/galleries/images/versions/read | Hiermit werden die Eigenschaften der Katalogimageversion abgerufen. |
> | Aktion | Microsoft.Compute/galleries/images/versions/write | Hiermit wird eine neue Katalogimageversion erstellt oder eine vorhandene Katalogimageversion aktualisiert. |
> | Aktion | Microsoft.Compute/galleries/images/write | Hiermit wird ein neues Katalogimage erstellt oder ein vorhandenes Katalogimage aktualisiert. |
> | Aktion | Microsoft.Compute/galleries/read | Hiermit werden die Eigenschaften des Katalogs abgerufen. |
> | Aktion | Microsoft.Compute/galleries/write | Hiermit wird ein neuer Katalog erstellt oder ein vorhandener Katalog aktualisiert. |
> | Aktion | Microsoft.Compute/hostGroups/delete | Löscht die Hostgruppe |
> | Aktion | Microsoft.Compute/hostGroups/read | Ruft die Eigenschaften einer Hostgruppe ab |
> | Aktion | Microsoft.Compute/hostGroups/write | Erstellt eine neue Hostgruppe oder aktualisiert eine vorhandene Hostgruppe |
> | Aktion | Microsoft.Compute/hosts/delete | Löscht den Host |
> | Aktion | Microsoft.Compute/hosts/read | Ruft die Eigenschaften eines Hosts ab |
> | Aktion | Microsoft.Compute/hosts/write | Erstellt einen neuen Host oder aktualisiert einen bereits vorhandenen Host |
> | Aktion | Microsoft.Compute/images/delete | Löscht das Image. |
> | Aktion | Microsoft.Compute/images/read | Dient zum Abrufen der Eigenschaften des Images. |
> | Aktion | Microsoft.Compute/images/write | Erstellt ein neues Image oder aktualisiert ein bereits vorhandenes. |
> | Aktion | Microsoft.Compute/locations/capsOperations/read | Ruft den Status eines asynchronen Vorgangs zum Abrufen von Obergrenzen ab. |
> | Aktion | Microsoft.Compute/locations/diskOperations/read | Ruft den Status eines asynchronen Vorgangs zum Abrufen von Datenträgern ab. |
> | Aktion | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Dient zum Erstellen von Protokollen, die die gesamten Anforderungen nach Zeitintervall anzeigen, um bei der Drosselungsdiagnose zu helfen. |
> | Aktion | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Dient zum Erstellen von Protokollen, die Aggregate von gedrosselten Anforderungen, nach Ressourcenname, OperationName oder der angewendeten Drosselungsrichtlinie gruppiert, anzeigen. |
> | Aktion | Microsoft.Compute/locations/operations/read | Ruft den Status eines asynchronen Vorgangs ab. |
> | Aktion | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Ruft die Eigenschaften eines Plattformimageangebots ab. |
> | Aktion | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Ruft die Eigenschaften einer Plattformimage-SKU ab. |
> | Aktion | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Ruft die Eigenschaften einer Plattformimageversion ab. |
> | Aktion | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Dient zum Abrufen der Eigenschaften eines VMExtension-Typs. |
> | Aktion | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Dient zum Abrufen der Eigenschaften einer VMExtension-Version. |
> | Aktion | Microsoft.Compute/locations/publishers/read | Dient zum Abrufen der Eigenschaften eines Herausgebers. |
> | Aktion | Microsoft.Compute/locations/runCommands/read | Hiermit werden die am Standort verfügbaren Ausführungsbefehle aufgelistet. |
> | Aktion | Microsoft.Compute/locations/usages/read | Ruft Dienstlimits und aktuelle Nutzungsmengen für die Computeressourcen des Abonnements an einem Standort ab. |
> | Aktion | Microsoft.Compute/locations/vmSizes/read | Listet die verfügbaren VM-Größen für einen Standort auf. |
> | Aktion | Microsoft.Compute/operations/read | Listet verfügbare Vorgänge für den Microsoft.Compute-Ressourcenanbieter auf. |
> | Aktion | Microsoft.Compute/proximityPlacementGroups/delete | Löscht die Näherungsplatzierungsgruppe |
> | Aktion | Microsoft.Compute/proximityPlacementGroups/read | Ruft die Eigenschaften einer Näherungsplatzierungsgruppe ab |
> | Aktion | Microsoft.Compute/proximityPlacementGroups/write | Erstellt eine neue Näherungsplatzierungsgruppe oder aktualisiert eine bereits vorhandene |
> | Aktion | Microsoft.Compute/register/action | Registriert ein Abonnement beim Microsoft.Compute-Ressourcenanbieter. |
> | Aktion | Microsoft.Compute/restorePointCollections/delete | Löscht die Wiederherstellungspunktsammlung und die darin enthaltenen Wiederherstellungspunkte. |
> | Aktion | Microsoft.Compute/restorePointCollections/read | Dient zum Abrufen der Eigenschaften einer Wiederherstellungspunktsammlung. |
> | Aktion | Microsoft.Compute/restorePointCollections/restorePoints/delete | Löscht den Wiederherstellungspunkt. |
> | Aktion | Microsoft.Compute/restorePointCollections/restorePoints/read | Dient zum Abrufen der Eigenschaften eines Wiederherstellungspunkts. |
> | Aktion | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Dient zum Abrufen der Eigenschaften eines Wiederherstellungspunkts sowie der Blob-SAS-URIs. |
> | Aktion | Microsoft.Compute/restorePointCollections/restorePoints/write | Erstellt einen neuen Wiederherstellungspunkt. |
> | Aktion | Microsoft.Compute/restorePointCollections/write | Erstellt eine neue Wiederherstellungspunktsammlung oder aktualisiert eine bereits vorhandene. |
> | Aktion | Microsoft.Compute/sharedVMImages/delete | Löscht SharedVMImage. |
> | Aktion | Microsoft.Compute/sharedVMImages/read | Dient zum Abrufen der Eigenschaften von SharedVMImage. |
> | Aktion | Microsoft.Compute/sharedVMImages/versions/delete | Dient zum Löschen von SharedVMImageVersion. |
> | Aktion | Microsoft.Compute/sharedVMImages/versions/read | Ruft die Eigenschaften von SharedVMImageVersion ab. |
> | Aktion | Microsoft.Compute/sharedVMImages/versions/replicate/action | Repliziert SharedVMImageVersion in Zielregionen. |
> | Aktion | Microsoft.Compute/sharedVMImages/versions/write | Erstellt eine neue SharedVMImageVersion oder aktualisiert eine vorhandene. |
> | Aktion | Microsoft.Compute/sharedVMImages/write | Erstellt ein neues SharedVMImage oder aktualisiert ein vorhandenes. |
> | Aktion | Microsoft.Compute/skus/read | Ruft die Liste der verfügbaren Microsoft.Compute-SKUs für Ihr Abonnement ab. |
> | Aktion | Microsoft.Compute/snapshots/beginGetAccess/action | Dient zum Abrufen des SAS-URI der Momentaufnahme für den Blobzugriff. |
> | Aktion | Microsoft.Compute/snapshots/delete | Dient zum Löschen einer Momentaufnahme. |
> | Aktion | Microsoft.Compute/snapshots/endGetAccess/action | Dient zum Sperren des SAS-URI der Momentaufnahme. |
> | Aktion | Microsoft.Compute/snapshots/read | Dient zum Abrufen der Eigenschaften einer Momentaufnahme. |
> | Aktion | Microsoft.Compute/snapshots/write | Dient zum Erstellen einer neuen Momentaufnahme oder zum Aktualisieren einer bereits vorhandenen. |
> | Aktion | Microsoft.Compute/unregister/action | Hebt die Registrierung eines Abonnements bei einem Microsoft.Compute-Ressourcenanbieter auf. |
> | Aktion | Microsoft.Compute/virtualMachines/capture/action | Erfasst den virtuellen Computer, indem die virtuellen Datenträger kopiert werden und eine Vorlage generiert wird, die dann zum Erstellen ähnlicher virtueller Computer verwendet werden kann. |
> | Aktion | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Konvertiert blobbasierte Datenträger des virtuellen Computers in verwaltete Datenträger. |
> | Aktion | Microsoft.Compute/virtualMachines/deallocate/action | Schaltet den virtuellen Computer aus und gibt die Computeressourcen frei. |
> | Aktion | Microsoft.Compute/virtualMachines/delete | Löscht den virtuellen Computer. |
> | Aktion | Microsoft.Compute/virtualMachines/extensions/delete | Löscht die VM-Erweiterung. |
> | Aktion | Microsoft.Compute/virtualMachines/extensions/read | Dient zum Abrufen der Eigenschaften einer VM-Erweiterung. |
> | Aktion | Microsoft.Compute/virtualMachines/extensions/write | Erstellt eine neue VM-Erweiterung oder aktualisiert eine bereits vorhandene. |
> | Aktion | Microsoft.Compute/virtualMachines/generalize/action | Legt den Zustand des virtuellen Computers auf „Generalisiert“ fest und bereitet den virtuellen Computer auf die Erfassung vor. |
> | Aktion | Microsoft.Compute/virtualMachines/instanceView/read | Ruft den detaillierten Laufzeitstatus des virtuellen Computers und seiner Ressourcen ab. |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Hiermit melden Sie sich bei einem virtuellen Computer als normaler Benutzer an. |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Hiermit melden Sie sich bei einem virtuellen Computer mit Windows-Administrator- oder Linux-Root-Benutzerrechten an. |
> | Aktion | Microsoft.Compute/virtualMachines/performMaintenance/action | Führt den Wartungsvorgang für die VM durch. |
> | Aktion | Microsoft.Compute/virtualMachines/powerOff/action | Schaltet den virtuellen Computer aus. Der virtuelle Computer wird weiterhin in Rechnung gestellt. |
> | Aktion | Microsoft.Compute/virtualMachines/read | Dient zum Abrufen der Eigenschaften eines virtuellen Computers. |
> | Aktion | Microsoft.Compute/virtualMachines/redeploy/action | Stellt den virtuellen Computer erneut bereit. |
> | Aktion | Microsoft.Compute/virtualMachines/reimage/action | Hiermit wird ein Reimaging für einen virtuellen Computer durchgeführt, der einen differenzierenden Datenträger verwendet. |
> | Aktion | Microsoft.Compute/virtualMachines/restart/action | Startet den virtuellen Computer neu. |
> | Aktion | Microsoft.Compute/virtualMachines/runCommand/action | Hiermit wird ein vordefiniertes Skript für den virtuellen Computer ausgeführt. |
> | Aktion | Microsoft.Compute/virtualMachines/start/action | Startet den virtuellen Computer. |
> | Aktion | Microsoft.Compute/virtualMachines/vmSizes/read | Listet die verfügbaren Größen auf, auf die der virtuelle Computer aktualisiert werden kann. |
> | Aktion | Microsoft.Compute/virtualMachines/write | Erstellt einen neuen virtuellen Computer oder aktualisiert einen vorhandenen virtuellen Computer. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Schaltet die Computeressourcen für die Instanzen der VM-Skalierungsgruppe aus und gibt sie frei.  |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/delete | Löscht die VM-Skalierungsgruppe. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/delete/action | Löscht die Instanzen der VM-Skalierungsgruppe. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Hiermit wird die VM-Skalierungsgruppenerweiterung gelöscht. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Hiermit werden die Eigenschaften einer VM-Skalierungsgruppenerweiterung abgerufen. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Hiermit wird eine neue VM-Skalierungsgruppenerweiterung erstellt oder eine vorhandene aktualisiert. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Hiermit werden Plattformupdatedomänen einer Service Fabric-VM-Skalierungsgruppe manuell durchgeführt, um eine ausstehende Aktualisierung fertigzustellen. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Ruft die Instanzansicht der VM-Skalierungsgruppe ab. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Aktualisiert Instanzen manuell auf das neueste Modell der VM-Skalierungsgruppe. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Ruft die Eigenschaften aller Netzwerkschnittstellen einer VM-Skalierungsgruppe ab. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Hiermit wird ein paralleles Upgrade gestartet, um alle VM-Skalierungsgruppen auf die neueste verfügbare Plattformimage-Betriebssystemversion zu aktualisieren. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Ruft den Verlauf von Betriebssystemupgrades für eine VM-Skalierungsgruppe ab. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Führt ungeplante Wartungsaufgaben für die Instanzen der VM-Skalierungsgruppe durch. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Schaltet die Instanzen der VM-Skalierungsgruppe aus. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Ruft die Eigenschaften aller öffentlichen IP-Adressen einer VM-Skalierungsgruppe ab. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/read | Dient zum Abrufen der Eigenschaften einer VM-Skalierungsgruppe. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Stellt erneut die Instanzen der VM-Skalierungsgruppe bereit. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Führt ein Reimaging für die Instanzen der VM-Skalierungsgruppe durch. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Hiermit wird ein Reimaging für alle Datenträger (Betriebssystem- und andere Datenträger) für die Instanzen einer VM-Skalierungsgruppe durchgeführt.  |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/restart/action | Startet die Instanzen der VM-Skalierungsgruppe neu. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Bricht das parallele Upgrade einer VM-Skalierungsgruppe ab. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Hiermit wird der aktuelle Status des parallelen Upgrades für eine VM-Skalierungsgruppe abgerufen. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/scale/action | Hiermit wird überprüft, ob eine vorhandene VM-Skalierungsgruppe horizontal auf die angegebene Anzahl von Instanzen hoch- oder herunterskaliert werden kann. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/skus/read | Listet die gültigen SKUs für eine vorhandene VM-Skalierungsgruppe auf. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/start/action | Startet die Instanzen der VM-Skalierungsgruppe. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Schaltet die Computeressourcen für einen virtuellen Computer in einer VM-Skalierungsgruppe aus und gibt sie frei. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Dient zum Löschen eines bestimmten virtuellen Computers in einer VM-Skalierungsgruppe. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Ruft die Instanzansicht eines virtuellen Computers in einer VM-Skalierungsgruppe ab. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Ruft die Eigenschaften einer öffentlichen IP-Adresse ab, die mithilfe einer VM-Skalierungsgruppe erstellt wurde. Eine VM-Skalierungsgruppe kann höchstens eine öffentliche IP pro IP-Konfiguration (private IP) erstellen. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Ruft die Eigenschaften einer oder aller IP-Konfigurationen einer Netzwerkschnittstelle ab, die mit der VM-Skalierungsgruppe erstellt wurde. IP-Konfigurationen stellen private IP-Adressen dar. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Ruft die Eigenschaften einer oder aller Netzwerkschnittstelle eines virtuellen Computers ab, der mithilfe der VM-Skalierungsgruppe erstellt wurde. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Führt ungeplante Wartungsaufgaben für eine VM-Instanz in einer VM-Skalierungsgruppe durch. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Schaltet eine Instanz eines virtuellen Computers in einer VM-Skalierungsgruppe aus. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Ruft die Eigenschaften eines virtuellen Computers in einer VM-Skalierungsgruppe ab. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Hiermit wird eine erneute Bereitstellung für eine VM-Instanz in einer VM-Skalierungsgruppe durchgeführt. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Hiermit wird ein Reimaging für eine VM-Instanz in einer VM-Skalierungsgruppe durchgeführt. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Hiermit wird ein Reimaging für alle Datenträger (Betriebssystem- und andere Datenträger) für eine VM-Instanz in einer VM-Skalierungsgruppe durchgeführt. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Startet eine Instanz eines virtuellen Computers in einer VM-Skalierungsgruppe neu. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Hiermit wird ein vordefiniertes Skript für eine VM-Instanz in einer VM-Skalierungsgruppe ausgeführt. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Startet eine Instanz eines virtuellen Computers in einer VM-Skalierungsgruppe. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Aktualisiert die Eigenschaften eines virtuellen Computers in einer VM-Skalierungsgruppe. |
> | Aktion | Microsoft.Compute/virtualMachineScaleSets/write | Erstellt eine neue VM-Skalierungsgruppe oder aktualisiert eine bereits vorhandene. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Consumption/balances/read | Listet die Auslastungszusammenfassung für einen Abrechnungszeitraum für eine Verwaltungsgruppe auf. |
> | Aktion | Microsoft.Consumption/budgets/delete | Löscht die Budgets anhand eines Abonnements oder einer Verwaltungsgruppe. |
> | Aktion | Microsoft.Consumption/budgets/read | Liste die Budgets nach einem Abonnement oder einer Verwaltungsgruppe auf. |
> | Aktion | Microsoft.Consumption/budgets/write | Erstellt und aktualisiert die Budgets anhand eines Abonnements oder einer Verwaltungsgruppe. |
> | Aktion | Microsoft.Consumption/charges/read | Listet Gebühren auf. |
> | Aktion | Microsoft.Consumption/credits/read | Listet Gutschriften auf. |
> | Aktion | Microsoft.Consumption/events/read | Listet Ereignisse auf. |
> | Aktion | Microsoft.Consumption/forecasts/read | Listet Vorhersagen auf. |
> | Aktion | Microsoft.Consumption/lots/read | Listet Chargen auf. |
> | Aktion | Microsoft.Consumption/marketplaces/read | Listet die Nutzungsdetails von Marketplace-Ressourcen für einen Bereich für EA- und WebDirect-Abonnements auf. |
> | Aktion | Microsoft.Consumption/operationresults/read | Listet Vorgangsergebnisse auf. |
> | Aktion | Microsoft.Consumption/operations/read | Listet alle vom Microsoft.Consumption-Ressourcenanbieter unterstützte Vorgänge auf. |
> | Aktion | Microsoft.Consumption/operationstatus/read | Listet den Vorgangsstatus auf. |
> | Aktion | Microsoft.Consumption/pricesheets/read | Listet die Daten zu Preisblättern nach einem Abonnement oder einer Verwaltungsgruppe auf. |
> | Aktion | Microsoft.Consumption/register/action | Führt die Registrierung für den Verbrauchsressourcenanbieter durch. |
> | Aktion | Microsoft.Consumption/reservationDetails/read | Listet die Auslastungsdetails für reservierte Instanzen nach Reservierungsauftrag oder Verwaltungsgruppe auf. Die Detaildaten gelten pro Instanz und Tag. |
> | Aktion | Microsoft.Consumption/reservationRecommendations/read | Führt einzelne oder freigegebene Empfehlungen für reservierte Instanzen für ein Abonnement ab. |
> | Aktion | Microsoft.Consumption/reservationSummaries/read | Zeigt die Auslastungszusammenfassung für reservierte Instanzen nach Reservierungsauftrag oder Verwaltungsgruppe an. Die Zusammenfassungsdaten gelten entweder pro Monat oder Tag. |
> | Aktion | Microsoft.Consumption/reservationTransactions/read | Listet den Transaktionsverlauf für reservierte Instanzen nach Verwaltungsgruppe auf. |
> | Aktion | Microsoft.Consumption/tags/read | Listet Tags für EA und Abonnements auf. |
> | Aktion | Microsoft.Consumption/tenants/read | Hiermit listen Sie Mandanten auf. |
> | Aktion | Microsoft.Consumption/tenants/register/action | Hiermit registrieren Sie eine Aktion für den Umfang von Microsoft.Consumption durch einen Mandanten. |
> | Aktion | Microsoft.Consumption/terms/read | Liste die Bedingungen für ein Abonnement oder eine Verwaltungsgruppe auf. |
> | Aktion | Microsoft.Consumption/usageDetails/read | Listet die Nutzungsdetails für einen Bereich für EA- und WebDirect-Abonnements auf. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Hiermit erfolgt eine Ausführung in einem bestimmten Container. |
> | Aktion | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Hiermit rufen Sie Protokolle für einen bestimmten Container ab. |
> | Aktion | Microsoft.ContainerInstance/containerGroups/delete | Hiermit löschen Sie eine bestimmte Containergruppe. |
> | Aktion | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Containergruppe ab. |
> | Aktion | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Containergruppe. |
> | Aktion | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für die Containergruppe ab. |
> | Aktion | Microsoft.ContainerInstance/containerGroups/read | Ruft alle Containergruppen ab. |
> | Aktion | Microsoft.ContainerInstance/containerGroups/restart/action | Startet eine bestimmte Containergruppe neu. |
> | Aktion | Microsoft.ContainerInstance/containerGroups/start/action | Startet eine bestimmte Containergruppe. |
> | Aktion | Microsoft.ContainerInstance/containerGroups/stop/action | Beendet eine bestimmte Containergruppe. Die Zuordnung von Computeressourcen wird aufgehoben, und die Berechnung von Gebühren wird beendet. |
> | Aktion | Microsoft.ContainerInstance/containerGroups/write | Hiermit erstellen oder aktualisieren Sie eine bestimmte Containergruppe. |
> | Aktion | Microsoft.ContainerInstance/locations/cachedImages/read | Ruft die zwischengespeicherten Images für das Abonnement in einer Region ab. |
> | Aktion | Microsoft.ContainerInstance/locations/capabilities/read | Ruft die Funktionen für eine Region ab. |
> | Aktion | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Benachrichtigt „Microsoft.ContainerInstance“ darüber, dass das virtuelle Netzwerk oder Subnetz gelöscht wird. |
> | Aktion | Microsoft.ContainerInstance/locations/usages/read | Ruft die Nutzung für eine bestimmte Region ab. |
> | Aktion | Microsoft.ContainerInstance/register/action | Registriert das Abonnement für den Ressourcenanbieter der Containerinstanz und aktiviert die Erstellung von Containergruppen. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ContainerRegistry/checkNameAvailability/read | Überprüft, ob der Name der Containerregistrierung für die Verwendung verfügbar ist. |
> | Aktion | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Benachrichtigt Microsoft.ContainerRegistry darüber, dass das virtuelle Netzwerk oder Subnetz gelöscht wird. |
> | Aktion | Microsoft.ContainerRegistry/locations/operationResults/read | Ruft das Ergebnis eines asynchronen Vorgangs ab. |
> | Aktion | Microsoft.ContainerRegistry/operations/read | Listet alle verfügbaren REST-API-Vorgänge von Azure Container Registry auf. |
> | Aktion | Microsoft.ContainerRegistry/register/action | Registriert das Abonnement für den Containerregistrierungs-Ressourcenanbieter und ermöglicht die Erstellung von Containerregistrierungen. |
> | Aktion | Microsoft.ContainerRegistry/registries/artifacts/delete | Löschen von Artefakten aus einer Containerregistrierung. |
> | Aktion | Microsoft.ContainerRegistry/registries/builds/cancel/action | Bricht einen vorhandenen Build ab. |
> | Aktion | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Ruft einen Link zum Herunterladen der Buildprotokolle ab. |
> | Aktion | Microsoft.ContainerRegistry/registries/builds/read | Ruft die Eigenschaften des angegebenen Builds ab oder listet alle Builds für die angegebene Containerregistrierung auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/builds/write | Aktualisiert einen Build für eine Containerregistrierung mit den angegebenen Parametern. |
> | Aktion | Microsoft.ContainerRegistry/registries/buildTasks/delete | Löscht eine Buildaufgabe aus einer Containerregistrierung. |
> | Aktion | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Listet die Eigenschaften der Quellcodeverwaltung für eine Buildaufgabe auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/buildTasks/read | Ruft die Eigenschaften der angegebenen Buildaufgabe ab oder listet alle Buildaufgaben für die angegebene Containerregistrierung auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Löscht einen Buildschritt aus einer Buildaufgabe. |
> | Aktion | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Listet die Buildargumente für einen Buildschritt einschließlich der Geheimnisargumente auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Ruft die Eigenschaften des angegebenen Buildschritts ab oder listet alle Buildschritte für die angegebene Buildaufgabe auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Erstellt oder aktualisiert einen Buildschritt für eine Buildaufgabe mit den angegebenen Parametern. |
> | Aktion | Microsoft.ContainerRegistry/registries/buildTasks/write | Erstellt oder aktualisiert eine Buildaufgabe für eine Containerregistrierung mit den angegebenen Parametern. |
> | Aktion | Microsoft.ContainerRegistry/registries/delete | Löscht eine Containerregistrierung. |
> | Aktion | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Löscht einen Event Grid-Filter aus einer Containerregistrierung. |
> | Aktion | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Ruft die Eigenschaften des angegebenen Event Grid-Filters ab oder listet alle Event Grid-Filter für die angegebene Containerregistrierung auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Erstellt oder aktualisiert einen Event Grid-Filter für eine Containerregistrierung mit den angegebenen Parametern. |
> | Aktion | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Ruft den Uploadspeicherort ab, damit der Benutzer die Quelle hochladen kann. |
> | Aktion | Microsoft.ContainerRegistry/registries/importImage/action | Importiert ein Image in die Containerregistrierung mit den angegebenen Parametern. |
> | Aktion | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Ruft den Quellupload-URL-Speicherort für eine Containerregistrierung ab. |
> | Aktion | Microsoft.ContainerRegistry/registries/listCredentials/action | Listet die Anmeldeinformationen für die angegebene Containerregistrierung auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/listPolicies/read | Listet die Richtlinien für die angegebene Containerregistrierung auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/listUsages/read | Listet den Kontingentbedarf für die angegebene Containerregistrierung auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/metadata/read | Ruft die Metadaten eines angegebenen Repositorys für eine Containerregistrierung ab |
> | Aktion | Microsoft.ContainerRegistry/registries/metadata/write | Aktualisiert die Metadaten eines Repositorys für eine Containerregistrierung |
> | Aktion | Microsoft.ContainerRegistry/registries/operationStatuses/read | Ruft den Status eines asynchronen Registrierungsvorgangs ab. |
> | Aktion | Microsoft.ContainerRegistry/registries/pull/read | Pullen oder Abrufen von Images aus einer Containerregistrierung |
> | Aktion | Microsoft.ContainerRegistry/registries/push/write | Pushen oder Schreiben von Images in eine Containerregistrierung |
> | Aktion | Microsoft.ContainerRegistry/registries/quarantineRead/read | Pullen oder Abrufen von Images in Quarantäne aus einer Containerregistrierung |
> | Aktion | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Schreiben/Ändern des Quarantänezustands von unter Quarantäne gestellten Images |
> | Aktion | Microsoft.ContainerRegistry/registries/queueBuild/action | Erstellt einen neuen Build basierend auf den Anforderungsparametern und fügt ihn der Buildwarteschlange hinzu. |
> | Aktion | Microsoft.ContainerRegistry/registries/read | Ruft die Eigenschaften der angegebenen Containerregistrierung ab oder listet alle Containerregistrierungen unter der angegebenen Ressourcengruppe oder dem angegebenen Abonnement auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Generiert die Anmeldeinformationen für die angegebene Containerregistrierung neu. |
> | Aktion | Microsoft.ContainerRegistry/registries/replications/delete | Löscht eine Replikation aus einer Containerregistrierung. |
> | Aktion | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Ruft den Status eines asynchronen Replikationsvorgangs ab. |
> | Aktion | Microsoft.ContainerRegistry/registries/replications/read | Ruft die Eigenschaften der angegebenen Replikation ab oder listet alle Replikationen für die angegebene Containerregistrierung auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/replications/write | Erstellt oder aktualisiert eine Replikation für eine Containerregistrierung mit den angegebenen Parametern. |
> | Aktion | Microsoft.ContainerRegistry/registries/runs/cancel/action | Bricht eine vorhandene Ausführung ab. |
> | Aktion | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Ruft die Protokoll-SAS-URL für eine Ausführung ab. |
> | Aktion | Microsoft.ContainerRegistry/registries/runs/read | Ruft die Eigenschaften einer Ausführung für eine Containerregistrierung oder Listenausführungen ab. |
> | Aktion | Microsoft.ContainerRegistry/registries/runs/write | Aktualisiert eine Ausführung. |
> | Aktion | Microsoft.ContainerRegistry/registries/scheduleRun/action | Plant eine Ausführung für eine Containerregistrierung. |
> | Aktion | Microsoft.ContainerRegistry/registries/sign/write | Pushen/Pullen von Inhaltsvertrauen-Metadaten für eine Containerregistrierung |
> | Aktion | Microsoft.ContainerRegistry/registries/tasks/delete | Löscht einen Task für eine Containerregistrierung. |
> | Aktion | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Listet die Details eines Tasks für eine Containerregistrierung auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/tasks/read | Ruft einen Task für eine Containerregistrierung ab oder listet alle Tasks auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/tasks/write | Erstellt oder aktualisiert einen Task für eine Containerregistrierung. |
> | Aktion | Microsoft.ContainerRegistry/registries/updatePolicies/write | Aktualisiert die Richtlinien für die angegebene Containerregistrierung. |
> | Aktion | Microsoft.ContainerRegistry/registries/webhooks/delete | Löscht einen Webhook aus einer Containerregistrierung. |
> | Aktion | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Ruft die Konfiguration des Dienst-URI und benutzerdefinierte Header für den Webhook ab. |
> | Aktion | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Listet die aktuellen Ereignisse für den angegebenen Webhook auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Ruft den Status eines asynchronen Webhookvorgangs ab. |
> | Aktion | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Löst ein Pingereignis aus, das an den Webhook gesendet werden muss. |
> | Aktion | Microsoft.ContainerRegistry/registries/webhooks/read | Ruft die Eigenschaften des angegebenen Webhooks ab oder listet alle Webhooks für die angegebene Containerregistrierung auf. |
> | Aktion | Microsoft.ContainerRegistry/registries/webhooks/write | Erstellt oder aktualisiert einen Webhook für eine Containerregistrierung mit den angegebenen Parametern. |
> | Aktion | Microsoft.ContainerRegistry/registries/write | Erstellt oder aktualisiert eine Containerregistrierung mit den angegebenen Parametern. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ContainerService/containerServices/delete | Erstellt einen Containerdienst. |
> | Aktion | Microsoft.ContainerService/containerServices/read | Ruft einen Containerdienst ab. |
> | Aktion | Microsoft.ContainerService/containerServices/write | Erstellt einen neuen Containerdienst oder aktualisiert einen vorhandenen. |
> | Aktion | Microsoft.ContainerService/locations/operationresults/read | Ruft den Status eines Ergebnisses eines asynchronen Vorgangs ab. |
> | Aktion | Microsoft.ContainerService/locations/operations/read | Ruft den Status eines asynchronen Vorgangs ab. |
> | Aktion | Microsoft.ContainerService/locations/orchestrators/read | Listet die unterstützten Orchestratoren auf. |
> | Aktion | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Ruft ein Zugriffsprofil für verwaltete Cluster anhand des Rollennamens mithilfe der Liste der Anmeldeinformationen ab. |
> | Aktion | Microsoft.ContainerService/managedClusters/accessProfiles/read | Ruft ein Zugriffsprofil für verwaltete Cluster anhand des Rollennamens ab. |
> | Aktion | Microsoft.ContainerService/managedClusters/agentPools/delete | Löscht einen Agentpool |
> | Aktion | Microsoft.ContainerService/managedClusters/agentPools/read | Ruft einen Agentpool ab |
> | Aktion | Microsoft.ContainerService/managedClusters/agentPools/write | Erstellt einen neuen Agentpool oder aktualisiert einen vorhandenen |
> | Aktion | Microsoft.ContainerService/managedClusters/delete | Löscht einen verwalteten Cluster. |
> | Aktion | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listet die clusterAdmin-Anmeldeinformationen eines verwalteten Clusters auf. |
> | Aktion | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listet die clusterUser-Anmeldeinformationen eines verwalteten Clusters auf. |
> | Aktion | Microsoft.ContainerService/managedClusters/read | Ruft einen verwalteten Cluster ab. |
> | Aktion | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Setzt das AAD-Profil eines verwalteten Clusters zurück. |
> | Aktion | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Setzt das Dienstprinzipalprofil eines verwalteten Clusters zurück. |
> | Aktion | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Ruft das Upgradeprofil des Clusters ab. |
> | Aktion | Microsoft.ContainerService/managedClusters/write | Erstellt einen neuen verwalteten Cluster oder aktualisiert einen vorhandenen. |
> | Aktion | Microsoft.ContainerService/openShiftClusters/delete | Löscht einen geöffneten Shiftcluster. |
> | Aktion | Microsoft.ContainerService/openShiftClusters/read | Ruft einen geöffneten Shiftcluster ab. |
> | Aktion | Microsoft.ContainerService/openShiftClusters/write | Erstellt einen neuen geöffneten Shiftcluster oder aktualisiert einen vorhandenen. |
> | Aktion | Microsoft.ContainerService/openShiftManagedClusters/delete | Löscht einen geöffneten verwalteten Shiftcluster. |
> | Aktion | Microsoft.ContainerService/openShiftManagedClusters/read | Ruft einen geöffneten verwalteten Shiftcluster ab. |
> | Aktion | Microsoft.ContainerService/openShiftManagedClusters/write | Erstellt einen neuen verwalteten geöffneten Shiftcluster oder aktualisiert einen vorhandenen. |
> | Aktion | Microsoft.ContainerService/operations/read | Listet verfügbare Vorgänge für den Microsoft.ContainerService-Ressourcenanbieter auf. |
> | Aktion | Microsoft.ContainerService/register/action | Registriert ein Abonnement beim Microsoft.ContainerService-Ressourcenanbieter. |
> | Aktion | Microsoft.ContainerService/unregister/action | Hebt die Registrierung eines Abonnements beim Microsoft.ContainerService-Ressourcenanbieter auf. |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ContentModerator/applications/delete | Löschvorgang |
> | Aktion | Microsoft.ContentModerator/applications/listSecrets/action | Dient zum Auflisten von Geheimnissen. |
> | Aktion | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Dient zum Lesen von Token für einmaliges Anmelden. |
> | Aktion | Microsoft.ContentModerator/applications/read | Lesevorgang |
> | Aktion | Microsoft.ContentModerator/applications/write | Schreibvorgang |
> | Aktion | Microsoft.ContentModerator/applications/write | Schreibvorgang |
> | Aktion | Microsoft.ContentModerator/listCommunicationPreference/action | Dient zum Auflisten der Kommunikationseinstellungen. |
> | Aktion | Microsoft.ContentModerator/operations/read | Dient zum Lesen von Vorgängen. |
> | Aktion | Microsoft.ContentModerator/updateCommunicationPreference/action | Dient zum Aktualisieren der Kommunikationseinstellungen. |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.CostManagement/cloudConnectors/delete | Löscht den angegebenen Cloudconnector |
> | Aktion | Microsoft.CostManagement/cloudConnectors/read | Listet die Cloudconnectors für den authentifizierten Benutzer auf |
> | Aktion | Microsoft.CostManagement/cloudConnectors/write | Erstellt oder aktualisiert den angegebenen Cloudconnector |
> | Aktion | Microsoft.CostManagement/dimensions/read | Dient zum Auflisten aller unterstützten Dimensionen nach Bereich. |
> | Aktion | Microsoft.CostManagement/exports/action | Führt den angegebenen Export aus. |
> | Aktion | Microsoft.CostManagement/exports/delete | Löscht den angegebenen Export. |
> | Aktion | Microsoft.CostManagement/exports/read | Listet die Exporte nach Bereich auf. |
> | Aktion | Microsoft.CostManagement/exports/run/action | Führt Exporte aus |
> | Aktion | Microsoft.CostManagement/exports/write | Erstellt oder aktualisiert den angegebenen Export. |
> | Aktion | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Listet die externalSubscriptions in einem externalBillingAccount für den authentifizierten Benutzer auf |
> | Aktion | Microsoft.CostManagement/externalBillingAccounts/read | Listet die externalBillingAccounts für den authentifizierten Benutzer auf |
> | Aktion | Microsoft.CostManagement/externalSubscriptions/read | Listet die externalSubscriptions für den authentifizierten Benutzer auf |
> | Aktion | Microsoft.CostManagement/externalSubscriptions/write | Aktualisiert die zugeordnete Verwaltungsgruppe der externalSubscription |
> | Aktion | Microsoft.CostManagement/query/action | Dient zum Abfragen von Nutzungsdaten nach Bereich. |
> | Aktion | Microsoft.CostManagement/query/read | Dient zum Abfragen von Nutzungsdaten nach Bereich. |
> | Aktion | Microsoft.CostManagement/register/action | Registriert eine Aktion im Bereich von Microsoft.CostManagement mit einem Abonnement |
> | Aktion | Microsoft.CostManagement/reports/action | Dient zum Planen von Berichten zu Nutzungsdaten nach Bereich. |
> | Aktion | Microsoft.CostManagement/reports/read | Dient zum Planen von Berichten zu Nutzungsdaten nach Bereich. |
> | Aktion | Microsoft.CostManagement/tenants/register/action | Registriert eine Aktion im Bereich von Microsoft.CostManagement mit einem Mandanten |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.CustomerInsights/hubs/adobemetadata/action | Dient zum Erstellen oder Aktualisieren von Adobe-Metadaten in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/adobemetadata/read | Dient zum Lesen von Adobe-Metadaten in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Dient zum Löschen einer beliebigen SAS-Richtlinie für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Dient zum Lesen einer beliebigen SAS-Richtlinie für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Dient zum erneuten Generieren des primären Schlüssels einer SAS-Richtlinie für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Dient zum erneuten Generieren des sekundären Schlüssels einer SAS-Richtlinie für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Dient zum Erstellen oder Aktualisieren einer beliebigen SAS-Richtlinie für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/activate/action | Dient zum Aktivieren eines beliebigen Azure Customer Insights-Connectors. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/activate/action | Dient zum Aktivieren eines beliebigen Azure Customer Insights-Connectors. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/delete | Dient zum Löschen eines beliebigen Azure Customer Insights-Connectors. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Dient zum Abrufen eines beliebigen Laufzeitstatus des Azure Customer Insights-Connectors. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Dient zum Aktivieren einer beliebigen Azure Customer Insights-Connectorzuordnung. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Dient zum Löschen einer beliebigen Azure Customer Insights-Connectorzuordnung. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Dient zum Lesen eines beliebigen Vorgangsergebnisses einer Azure Customer Insights-Connectorzuordnung. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Dient zum Lesen einer beliebigen Azure Customer Insights-Connectorzuordnung. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Dient zum Erstellen oder Aktualisieren einer beliebigen Azure Customer Insights-Connectorzuordnung. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/operations/read | Dient zum Lesen eines beliebigen Vorgangsergebnisses eines Azure Customer Insights-Connectors. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/read | Dient zum Lesen eines beliebigen Azure Customer Insights-Connectors. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Dient zum Erstellen oder Aktualisieren von Informationen zur Authentifizierung eines beliebigen Azure Customer Insights-Connectors. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/update/action | Dient zum Aktualisieren eines beliebigen Azure Customer Insights-Connectors. |
> | Aktion | Microsoft.CustomerInsights/hubs/connectors/write | Dient zum Erstellen oder Aktualisieren eines beliebigen Azure Customer Insights-Connectors. |
> | Aktion | Microsoft.CustomerInsights/hubs/crmmetadata/action | Dient zum Erstellen oder Aktualisieren von CRM-Metadaten in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/crmmetadata/read | Dient zum Lesen von CRM-Metadaten in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/delete | Dient zum Löschen eines beliebigen Azure Customer Insights-Hubs. |
> | Aktion | Microsoft.CustomerInsights/hubs/gdpr/delete | Dient zum Löschen einer beliebigen DSGVO für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/gdpr/read | Dient zum Lesen einer beliebigen Azure Customer Insights-DSGVO. |
> | Aktion | Microsoft.CustomerInsights/hubs/gdpr/write | Dient zum Erstellen oder Aktualisieren einer beliebigen Azure Customer Insights-DSGVO. |
> | Aktion | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Dient zum Abrufen des Abrechnungsguthaben im Azure Customer Insights-Hub. |
> | Aktion | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Dient zum Abrufen des Abrechnungsverlaufs im Azure Customer Insights-Hub. |
> | Aktion | Microsoft.CustomerInsights/hubs/images/delete | Dient zum Löschen eines beliebigen Azure Customer Insights-Image. |
> | Aktion | Microsoft.CustomerInsights/hubs/images/read | Dient zum Lesen eines beliebigen Azure Customer Insights-Image. |
> | Aktion | Microsoft.CustomerInsights/hubs/images/write | Dient zum Erstellen oder Aktualisieren eines beliebigen Azure Customer Insights-Image. |
> | Aktion | Microsoft.CustomerInsights/hubs/interactions/delete | Dient zum Löschen beliebiger Azure Customer Insights-Interaktionen. |
> | Aktion | Microsoft.CustomerInsights/hubs/interactions/operations/read | Dient zum Lesen eines beliebigen Vorgangsergebnisses von Azure Customer Insights-Interaktionen. |
> | Aktion | Microsoft.CustomerInsights/hubs/interactions/read | Dient zum Lesen einer beliebigen Azure Customer Insights-Interaktion. |
> | Aktion | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Empfiehlt RelationShip-Links für beliebige Azure Insights Customer Insights-Interaktionen. |
> | Aktion | Microsoft.CustomerInsights/hubs/interactions/write | Dient zum Erstellen oder Aktualisieren einer beliebigen Azure Customer Insights-Interaktion. |
> | Aktion | Microsoft.CustomerInsights/hubs/kpi/delete | Dient zum Löschen eines beliebigen Key Performance Indicators für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/kpi/operations/read | Dient zum Lesen eines Vorgangsergebnisses für einen beliebigen Key Performance Indicator für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/kpi/read | Dient zum Lesen eines beliebigen Key Performance Indicators für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Dient zum erneuten Verarbeiten beliebiger Key Performance Indicators für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/kpi/write | Dient zum Erstellen oder Aktualisieren eines beliebigen Key Performance Indicators für Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/links/delete | Dient zum Löschen beliebiger Azure Customer Insights-Verknüpfungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/links/operations/read | Dient zum Lesen eines beliebigen Vorgangsergebnisses für Azure Customer Insights-Verknüpfungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/links/read | Dient zum Lesen beliebiger Azure Customer Insights-Verknüpfungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/links/write | Dient zum Erstellen oder Aktualisieren beliebiger Azure Customer Insights-Verknüpfungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/msemetadata/action | Dient zum Erstellen oder Aktualisieren von MSE-Metadaten in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/msemetadata/read | Dient zum Lesen von MSE-Metadaten in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/operationresults/read | Dient zum Abrufen von Abrechnungsergebnissen im Azure Customer Insights-Hub. |
> | Aktion | Microsoft.CustomerInsights/hubs/predictions/delete | Dient zum Löschen beliebiger Azure Customer Insights-Vorhersagen. |
> | Aktion | Microsoft.CustomerInsights/hubs/predictions/operations/read | Dient zum Lesen eines beliebigen Vorgangsergebnisses für Azure Customer Insights-Vorhersagen. |
> | Aktion | Microsoft.CustomerInsights/hubs/predictions/read | Dient zum Lesen beliebiger Azure Customer Insights-Vorhersagen. |
> | Aktion | Microsoft.CustomerInsights/hubs/predictions/write | Dient zum Erstellen oder Aktualisieren beliebiger Azure Customer Insights-Vorhersagen. |
> | Aktion | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Dient zum Löschen aller Azure Customer Insights-Richtlinien für den Vorhersageabgleich. |
> | Aktion | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Dient zum Lesen des Vorgangsergebnisses aller Azure Customer Insights-Richtlinien für den Vorhersageabgleich. |
> | Aktion | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Dient zum Lesen aller Azure Customer Insights-Richtlinien für den Vorhersageabgleich. |
> | Aktion | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Dient zum Erstellen oder Aktualisieren aller Azure Customer Insights-Richtlinien für den Vorhersageabgleich. |
> | Aktion | Microsoft.CustomerInsights/hubs/profiles/delete | Dient zum Löschen eines beliebigen Azure Customer Insights-Profils. |
> | Aktion | Microsoft.CustomerInsights/hubs/profiles/operations/read | Dient zum Lesen eines beliebigen Vorgangsergebnisses für das Azure Customer Insights-Profil. |
> | Aktion | Microsoft.CustomerInsights/hubs/profiles/read | Dient zum Lesen eines beliebigen Azure Customer Insights-Profils. |
> | Aktion | Microsoft.CustomerInsights/hubs/profiles/write | Dient zum Schreiben eines beliebigen Azure Customer Insights-Profils. |
> | Aktion | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Ruft die verfügbaren Protokolle für die Ressource ab. |
> | Aktion | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für die Ressource ab. |
> | Aktion | Microsoft.CustomerInsights/hubs/read | Dient zum Lesen eines beliebigen Azure Customer Insights-Hubs. |
> | Aktion | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Dient zum Löschen beliebiger Azure Customer Insights-Beziehungsverknüpfungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Dient zum Lesen eines beliebigen Vorgangsergebnisses für Azure Customer Insights-Beziehungsverknüpfungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Dient zum Lesen beliebiger Azure Customer Insights-Beziehungsverknüpfungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Dient zum Erstellen oder Aktualisieren beliebiger Azure Customer Insights-Beziehungsverknüpfungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/relationships/delete | Dient zum Löschen beliebiger Azure Customer Insights-Beziehungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/relationships/operations/read | Dient zum Lesen eines beliebigen Vorgangsergebnisses für Azure Customer Insights-Beziehungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/relationships/read | Dient zum Lesen beliebiger Azure Customer Insights-Beziehungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/relationships/write | Dient zum Erstellen oder Aktualisieren beliebiger Azure Customer Insights-Beziehungen. |
> | Aktion | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Dient zum Löschen einer beliebigen Azure Customer Insights-RBAC-Zuweisung. |
> | Aktion | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Dient zum Lesen eines beliebigen Vorgangsergebnisses für eine Azure Customer Insights-RBAC-Zuweisung. |
> | Aktion | Microsoft.CustomerInsights/hubs/roleAssignments/read | Dient zum Lesen einer beliebigen Azure Customer Insights-RBAC-Zuweisung. |
> | Aktion | Microsoft.CustomerInsights/hubs/roleAssignments/write | Dient zum Erstellen oder Aktualisieren einer beliebigen Azure Customer Insights-RBAC-Zuweisung. |
> | Aktion | Microsoft.CustomerInsights/hubs/roles/read | Dient zum Lesen beliebiger Azure Customer Insights-RBAC-Rollen. |
> | Aktion | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Dient zum Erstellen oder Aktualisieren von Salesforce-Metadaten in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Dient zum Lesen von Salesforce-Metadaten in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/segments/delete | Dient zum Löschen beliebiger Azure Customer Insights-Segmente. |
> | Aktion | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Dient zur Verwaltung beliebiger Azure Customer Insights-Segmente. |
> | Aktion | Microsoft.CustomerInsights/hubs/segments/read | Dient zum Lesen beliebiger Azure Customer Insights-Segmente. |
> | Aktion | Microsoft.CustomerInsights/hubs/segments/static/action | Dient zur Verwaltung beliebiger statischer Azure Customer Insights-Segmente. |
> | Aktion | Microsoft.CustomerInsights/hubs/segments/write | Dient zum Erstellen oder Aktualisieren beliebiger Azure Customer Insights-Segmente. |
> | Aktion | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Dient zum Löschen beliebiger SqlConnectionStrings-Elemente in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Dient zum Lesen beliebiger SqlConnectionStrings-Elemente in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Dient zum Erstellen oder Aktualisieren beliebiger SqlConnectionStrings-Elemente in Azure Customer Insights. |
> | Aktion | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Generiert ein Empfehlungstypschema von Beispieldaten. |
> | Aktion | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Verwaltet alle Einstellungen für Azure Customer Insights-Hubs. |
> | Aktion | Microsoft.CustomerInsights/hubs/views/delete | Dient zum Löschen einer beliebigen Azure Customer Insights-App-Ansicht. |
> | Aktion | Microsoft.CustomerInsights/hubs/views/read | Dient zum Lesen einer beliebigen Azure Customer Insights-App-Ansicht. |
> | Aktion | Microsoft.CustomerInsights/hubs/views/write | Dient zum Erstellen oder Aktualisieren einer beliebigen Azure Customer Insights-App-Ansicht. |
> | Aktion | Microsoft.CustomerInsights/hubs/widgettypes/read | Liest Widgettypen der Azure Customer Insights-App. |
> | Aktion | Microsoft.CustomerInsights/hubs/write | Dient zum Erstellen oder Aktualisieren eines beliebigen Azure Customer Insights-Hubs. |
> | Aktion | Microsoft.CustomerInsights/operations/read | Liest Metadaten der Azure Customer Insights-API. |
> | Aktion | Microsoft.CustomerInsights/register/action | Registriert das Abonnement für den Customer Insights-Ressourcenanbieter und ermöglicht die Erstellung von Customer Insights-Ressourcen. |
> | Aktion | Microsoft.CustomerInsights/unregister/action | Hebt die Registrierung des Abonnements für den Customer Insights-Ressourcenanbieter auf. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Hiermit kann eine Abholung für Rücklieferungen gebucht werden. |
> | Aktion | Microsoft.DataBox/jobs/cancel/action | Hiermit wird ein laufender Auftrag storniert. |
> | Aktion | Microsoft.DataBox/jobs/delete | Hiermit werden die Aufträge gelöscht. |
> | Aktion | Microsoft.DataBox/jobs/listCredentials/action | Hiermit werden die unverschlüsselten Anmeldeinformationen für den Auftrag aufgelistet. |
> | Aktion | Microsoft.DataBox/jobs/read | Hiermit werden die Aufträge aufgelistet oder abgerufen. |
> | Aktion | Microsoft.DataBox/jobs/write | Dient zum Erstellen oder Aktualisieren der Aufträge. |
> | Aktion | Microsoft.DataBox/locations/availableSkus/action | Mit dieser Methode wird die Liste der verfügbaren SKUs zurückgegeben. |
> | Aktion | Microsoft.DataBox/locations/operationResults/read | Listet die Vorgangsergebnisse auf bzw. ruft sie ab. |
> | Aktion | Microsoft.DataBox/locations/validateAddress/action | Hiermit wird die Lieferadresse überprüft, und es werden – sofern vorhanden – alternative Adressen bereitgestellt. |
> | Aktion | Microsoft.DataBox/register/action | Registriert den Anbieter „Microsoft.Databox“. |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Listet die Warnungen auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Listet die Warnungen auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Löscht die Bandbreitenzeitpläne. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Listet die Vorgangsergebnisse auf oder ruft sie ab |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Listet die Bandbreitenzeitpläne auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Listet die Bandbreitenzeitpläne auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Hiermit werden die Bandbreitenzeitpläne erstellt oder aktualisiert. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Löscht die Data Box Edge-Geräte. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Dient zum Herunterladen von Updates auf Geräte. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/extendedInformation/action | Ruft die erweiterten Informationen der Ressource ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Ruft die erweiterten Informationen der Ressource ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Dient zum Installieren von Updates auf einem Gerät. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Listet die Aufträge auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Listet die Netzwerkeinstellungen des Geräts auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Listet die Vorgangsergebnisse auf oder ruft sie ab |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Dient zum Auflisten oder Abrufen des Vorgangsstatus. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Hiermit werden die Aufträge gelöscht. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Listet die Vorgangsergebnisse auf oder ruft sie ab |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Listet die Aufträge auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Listet die Aufträge auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Erstellt oder aktualisiert die Aufträge. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Listet die Data Box Edge-Geräte auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Listet die Data Box Edge-Geräte auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Listet die Data Box Edge-Geräte auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Löscht die Rollen. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Listet die Vorgangsergebnisse auf oder ruft sie ab |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Listet die Rollen auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Listet die Rollen auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Erstellt oder aktualisiert die Rollen. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Dient zum Suchen nach Updates. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Listet die Vorgangsergebnisse auf oder ruft sie ab |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Dient zum Aktualisieren der Sicherheitseinstellungen. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Löscht die Freigaben. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Listet die Vorgangsergebnisse auf oder ruft sie ab |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Listet die Freigaben auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Listet die Freigaben auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Aktualisiert Freigabemetadaten mit Daten aus der Cloud. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Erstellt oder aktualisiert die Freigaben. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Löscht die Speicherkonto-Anmeldedaten. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Listet die Vorgangsergebnisse auf oder ruft sie ab |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Listet die Speicherkonto-Anmeldedaten auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Listet die Speicherkonto-Anmeldedaten auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Erstellt oder aktualisiert die Speicherkonto-Anmeldedaten. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Löscht die Trigger. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Listet die Vorgangsergebnisse auf oder ruft sie ab |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Listet die Trigger auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Listet die Trigger auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Erstellt oder aktualisiert die Trigger. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Listet die Updatezusammenfassung auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Dient zum Hochladen des Zertifikats für die Geräteregistrierung. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Löscht die Benutzer der Freigabe. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Listet die Vorgangsergebnisse auf oder ruft sie ab |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Listet die Benutzer der Freigabe auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Listet die Benutzer der Freigabe auf oder ruft sie ab. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Erstellt oder aktualisiert die Benutzer der Freigabe. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Erstellt oder aktualisiert die Data Box Edge-Geräte. |
> | Aktion | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Erstellt oder aktualisiert die Data Box Edge-Geräte. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Databricks/locations/getNetworkPolicies/action | Ruft die Netzwerkzielrichtlinien für ein Subnetz basierend auf dem Standort der NRP ab |
> | Aktion | Microsoft.Databricks/register/action | Führt die Registrierung bei Databricks aus. |
> | Aktion | Microsoft.Databricks/workspaces/delete | Entfernt einen Databricks-Arbeitsbereich. |
> | Aktion | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Legt die verfügbaren Diagnoseeinstellungen für den Databricks-Arbeitsbereich fest. |
> | Aktion | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Dient zum Hinzufügen oder Ändern der Diagnoseeinstellungen. |
> | Aktion | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Ruft die verfügbaren Protokolldefinitionen für den Databricks-Arbeitsbereich ab. |
> | Aktion | Microsoft.Databricks/workspaces/read | Ruft eine Liste der Databricks-Arbeitsbereiche ab. |
> | Aktion | Microsoft.Databricks/workspaces/write | Erstellt einen Databricks-Arbeitsbereich. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DataCatalog/datacatalogs/delete | Löscht die DataCatalog-Ressource für den Data Catalog-Ressourcenanbieter |
> | Aktion | Microsoft.DataCatalog/datacatalogs/read | Liest die DataCatalog-Ressource für den Data Catalog-Ressourcenanbieter |
> | Aktion | Microsoft.DataCatalog/datacatalogs/write | Schreibt die DataCatalog-Ressource für den Data Catalog-Ressourcenanbieter |
> | Aktion | Microsoft.DataCatalog/operations/read | Liest alle verfügbaren Vorgänge im Data Catalog-Ressourcenanbieter |
> | Aktion | Microsoft.DataCatalog/register/action | Registriert das Abonnement für den Data Catalog-Ressourcenanbieter |
> | Aktion | Microsoft.DataCatalog/unregister/action | Hebt die Registrierung des Abonnements für den Data Catalog-Ressourcenanbieter auf |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Überprüft, ob der Data Factory-Name für die Nutzung verfügbar ist. |
> | Aktion | Microsoft.DataFactory/datafactories/activitywindows/read | Liest die Aktivitätsfenster in der Data Factory mit den angegebenen Parametern. |
> | Aktion | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Liest die Aktivitätsfenster für die Pipelineaktivität mit den angegebenen Parametern. |
> | Aktion | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Liest die Aktivitätsfenster für die Pipeline mit den angegebenen Parametern. |
> | Aktion | Microsoft.DataFactory/datafactories/datapipelines/delete | Löscht alle Pipelines. |
> | Aktion | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Hält alle Pipelines an. |
> | Aktion | Microsoft.DataFactory/datafactories/datapipelines/read | Liest alle Pipelines. |
> | Aktion | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Setzt alle Pipelines fort. |
> | Aktion | Microsoft.DataFactory/datafactories/datapipelines/update/action | Aktualisiert alle Pipelines. |
> | Aktion | Microsoft.DataFactory/datafactories/datapipelines/write | Erstellt oder aktualisiert alle Pipelines. |
> | Aktion | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Liest die Aktivitätsfenster für das Dataset mit den angegebenen Parametern. |
> | Aktion | Microsoft.DataFactory/datafactories/datasets/delete | Löscht alle Datasets. |
> | Aktion | Microsoft.DataFactory/datafactories/datasets/read | Liest alle Datasets. |
> | Aktion | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Liest die Ausführung des Datenslices für ein angegebenes Dataset mit der angegebenen Startzeit. |
> | Aktion | Microsoft.DataFactory/datafactories/datasets/slices/read | Ruft die Datenslices im angegebenen Zeitraum ab. |
> | Aktion | Microsoft.DataFactory/datafactories/datasets/slices/write | Aktualisiert den Status des Datenslices. |
> | Aktion | Microsoft.DataFactory/datafactories/datasets/write | Erstellt oder aktualisiert alle Datasets. |
> | Aktion | Microsoft.DataFactory/datafactories/delete | Löscht die Data Factory. |
> | Aktion | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Liest die Verbindungsinformationen für alle Gateways. |
> | Aktion | Microsoft.DataFactory/datafactories/gateways/delete | Löscht alle Gateways. |
> | Aktion | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Führt die Authentifizierungsschlüssel für alle Gateways auf. |
> | Aktion | Microsoft.DataFactory/datafactories/gateways/read | Liest alle Gateways. |
> | Aktion | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Generiert die Authentifizierungsschlüssel für alle Gateways neu. |
> | Aktion | Microsoft.DataFactory/datafactories/gateways/write | Erstellt oder aktualisiert alle Gateways. |
> | Aktion | Microsoft.DataFactory/datafactories/linkedServices/delete | Löscht alle verknüpften Dienste. |
> | Aktion | Microsoft.DataFactory/datafactories/linkedServices/read | Liest alle verknüpften Dienste. |
> | Aktion | Microsoft.DataFactory/datafactories/linkedServices/write | Erstellt oder aktualisiert alle verknüpften Dienste. |
> | Aktion | Microsoft.DataFactory/datafactories/read | Liest die Data Factory. |
> | Aktion | Microsoft.DataFactory/datafactories/runs/loginfo/read | Liest den SAS-URI für einen Blobcontainer, der die Protokolle enthält. |
> | Aktion | Microsoft.DataFactory/datafactories/tables/delete | Löscht alle Datasets. |
> | Aktion | Microsoft.DataFactory/datafactories/tables/read | Liest alle Datasets. |
> | Aktion | Microsoft.DataFactory/datafactories/tables/write | Erstellt oder aktualisiert alle Datasets. |
> | Aktion | Microsoft.DataFactory/datafactories/write | Erstellt oder aktualisiert die Data Factory. |
> | Aktion | Microsoft.DataFactory/factories/cancelpipelinerun/action | Bricht die Ausführung der Pipeline ab, die von der Ausführungs-ID angegeben wurde. |
> | Aktion | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Erstellt eine Debugsitzung für einen Datenfluss. |
> | Aktion | Microsoft.DataFactory/factories/dataflows/delete | Löscht den Dataflow. |
> | Aktion | Microsoft.DataFactory/factories/dataflows/read | Liest den Dataflow. |
> | Aktion | Microsoft.DataFactory/factories/dataflows/write | Dient zum Erstellen oder Aktualisieren des Dataflows. |
> | Aktion | Microsoft.DataFactory/factories/datasets/delete | Löscht alle Datasets. |
> | Aktion | Microsoft.DataFactory/factories/datasets/read | Liest alle Datasets. |
> | Aktion | Microsoft.DataFactory/factories/datasets/write | Erstellt oder aktualisiert alle Datasets. |
> | Aktion | Microsoft.DataFactory/factories/delete | Löscht Data Factorys. |
> | Aktion | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Löscht eine Debugsitzung für einen Datenfluss. |
> | Aktion | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Ruft den Zugriff auf den ADF-DataPlane-Dienst ab. |
> | Aktion | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Liest den Zugriff auf den ADF-DataPlane-Dienst. |
> | Aktion | Microsoft.DataFactory/factories/getFeatureValue/action | Ruft für den jeweiligen Standort den Wert des Features für die Anzeigesteuerung ab. |
> | Aktion | Microsoft.DataFactory/factories/getFeatureValue/read | Liest für den jeweiligen Standort den Wert des Features für die Anzeigesteuerung. |
> | Aktion | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Ruft ein GitHub-Zugriffstoken ab. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/delete | Löscht alle Integration Runtimes. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Liest Verbindungsinformationen für die Integration Runtime. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Ruft die SSIS Integration Runtime-Metadaten für die angegebene Integration Runtime ab. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Liest den Status der Integration Runtime. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Erstellt den verknüpften Integration Runtime-Verweis aus der freigegebenen Integration Runtime, die angegeben wurde. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Führt die Authentifizierungsschlüssel für alle Integration Runtimes auf. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Ruft die Überwachungsdaten für alle Integration Runtimes ab. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Löscht den Knoten für die angegebene Integration Runtime. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Gibt die IP-Adresse für den angegebenen Knoten der Integration Runtime zurück. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Liest den Knoten für die angegebene Integration Runtime. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Aktualisiert einen selbstgehosteten Integration Runtime-Knoten. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/read | Liest alle Integration Runtimes. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Aktualisiert die SSIS Integration Runtime-Metadaten für die angegebene Integration Runtime. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Generiert die Authentifizierungsschlüssel für die angegebene Integration Runtime neu. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Entfernt verknüpfte Integration Runtime-Verweise aus der angegebenen Integration Runtime. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/start/action | Startet alle Integration Runtimes. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Beendet alle Integration Runtimes. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synchronisiert die Anmeldeinformationen für die angegebene Integration Runtime. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Führt ein Upgrade für die angegebene Integration Runtime durch. |
> | Aktion | Microsoft.DataFactory/factories/integrationruntimes/write | Erstellt oder aktualisiert alle Integration Runtimes. |
> | Aktion | Microsoft.DataFactory/factories/linkedServices/delete | Löscht verknüpfte Dienste. |
> | Aktion | Microsoft.DataFactory/factories/linkedServices/read | Liest verknüpfte Dienste. |
> | Aktion | Microsoft.DataFactory/factories/linkedServices/write | Erstellt oder aktualisiert verknüpfte Dienste. |
> | Aktion | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Liest die Ausführung von Aktivitäten für die angegebene Ausführungs-ID der Pipeline. |
> | Aktion | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Bricht die Ausführung der Pipeline ab, die von der Ausführungs-ID angegeben wurde. |
> | Aktion | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Fragt die Aktivitätsausführungen für die angegebene Ausführungs-ID der Pipeline ab. |
> | Aktion | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Liest das Ergebnis der Ausführung von Aktivitäten für die angegebene Ausführungs-ID der Pipeline. |
> | Aktion | Microsoft.DataFactory/factories/pipelineruns/read | Liest die Pipelineausführungen. |
> | Aktion | Microsoft.DataFactory/factories/pipelines/createrun/action | Erstellt eine Ausführung für die Pipeline. |
> | Aktion | Microsoft.DataFactory/factories/pipelines/delete | Löscht Pipelines. |
> | Aktion | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Ruft den Fortschritt von Aktivitätsausführungen ab. |
> | Aktion | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Liest die Pipelineausführung. |
> | Aktion | Microsoft.DataFactory/factories/pipelines/read | Liest Pipelines. |
> | Aktion | Microsoft.DataFactory/factories/pipelines/write | Dient zum Erstellen oder Aktualisieren von Pipelines. |
> | Aktion | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Fragt die Debugpipelineausführungen ab. |
> | Aktion | Microsoft.DataFactory/factories/querypipelineruns/action | Fragt die Pipelineausführungen ab. |
> | Aktion | Microsoft.DataFactory/factories/querypipelineruns/read | Liest das Ergebnis von Ausführungen der Abfragepipeline. |
> | Aktion | Microsoft.DataFactory/factories/querytriggerruns/action | Fragt die Triggerausführungen ab. |
> | Aktion | Microsoft.DataFactory/factories/querytriggerruns/read | Liest das Ergebnis von Triggerausführungen. |
> | Aktion | Microsoft.DataFactory/factories/read | Liest Data Factorys. |
> | Aktion | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Startet eine Debugsitzung für einen Datenfluss. |
> | Aktion | Microsoft.DataFactory/factories/triggerruns/read | Liest die Triggerausführungen. |
> | Aktion | Microsoft.DataFactory/factories/triggers/delete | Löscht alle Trigger. |
> | Aktion | Microsoft.DataFactory/factories/triggers/read | Liest alle Trigger. |
> | Aktion | Microsoft.DataFactory/factories/triggers/start/action | Startet alle Trigger. |
> | Aktion | Microsoft.DataFactory/factories/triggers/stop/action | Beendet alle Trigger. |
> | Aktion | Microsoft.DataFactory/factories/triggers/triggerruns/read | Liest die Triggerausführungen. |
> | Aktion | Microsoft.DataFactory/factories/triggers/write | Erstellt oder aktualisiert alle Trigger. |
> | Aktion | Microsoft.DataFactory/factories/write | Dient zum Erstellen oder Aktualisieren von Data Factorys. |
> | Aktion | Microsoft.DataFactory/locations/configureFactoryRepo/action | Konfiguriert das Repository für die Factory. |
> | Aktion | Microsoft.DataFactory/locations/getFeatureValue/action | Ruft für den jeweiligen Standort den Wert des Features für die Anzeigesteuerung ab. |
> | Aktion | Microsoft.DataFactory/locations/getFeatureValue/read | Liest für den jeweiligen Standort den Wert des Features für die Anzeigesteuerung. |
> | Aktion | Microsoft.DataFactory/operations/read | Liest alle Vorgänge im Microsoft Data Factory-Anbieter. |
> | Aktion | Microsoft.DataFactory/register/action | Registriert das Abonnement für den Data Factory-Ressourcenanbieter. |
> | Aktion | Microsoft.DataFactory/unregister/action | Hebt die Registrierung des Abonnements für den Data Factory-Ressourcenanbieter auf. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Löscht eine Computerichtlinie. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Ruft Informationen zu einer Computerichtlinie ab. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Erstellt oder aktualisiert eine Computerichtlinie. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Hebt die Verknüpfung eines DataLakeStore-Kontos mit einem DataLakeAnalytics-Konto auf. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Ruft Informationen zu einem verknüpften DataLakeStore-Konto eines DataLakeAnalytics-Kontos ab. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Erstellt oder aktualisiert ein verknüpftes DataLakeStore-Konto eines DataLakeAnalytics-Kontos. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/delete | Löscht ein DataLakeAnalytics-Konto. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Dient zum Löschen einer Firewallregel. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Dient zum Abrufen von Informationen zu einer Firewallregel. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Dient zum Erstellen oder Aktualisieren einer Firewallregel. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Ruft das Ergebnis eines Vorgangs des DataLakeAnalytics-Kontos ab. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/read | Ruft Informationen zu einem vorhandenen DataLakeAnalytics-Konto ab. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Listet SAS-Tokens für Speichercontainer eines verknüpften Speicherkontos eines DataLakeAnalytics-Kontos auf. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Ruft Container eines verknüpften Speicherkontos eines DataLakeAnalytics-Kontos ab. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Hebt die Verknüpfung eines Speicherkontos mit einem DataLakeAnalytics-Konto auf. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Ruft Informationen zu einem verknüpften Speicherkonto eines DataLakeAnalytics-Kontos ab. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Erstellt oder aktualisiert ein verknüpftes Speicherkonto eines DataLakeAnalytics-Kontos. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Erteilt Berechtigungen zum Abbrechen von Aufträgen, die von anderen Benutzern übermittelt wurden. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Überträgt SystemMaxAnalyticsUnits zwischen DataLakeAnalytics-Konten. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Dient zum Löschen einer VNET-Regel. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Hiermit rufen Sie Informationen zu einer VNET-Regel ab. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Dient zum Erstellen oder Aktualisieren einer VNET-Regel. |
> | Aktion | Microsoft.DataLakeAnalytics/accounts/write | Erstellt oder aktualisiert ein DataLakeAnalytics-Konto. |
> | Aktion | Microsoft.DataLakeAnalytics/locations/capability/read | Ruft Funktionsinformationen eines Abonnements bezüglich der Verwendung von DataLakeAnalytics ab. |
> | Aktion | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Überprüft die Verfügbarkeit eines DataLakeAnalytics-Kontonamens. |
> | Aktion | Microsoft.DataLakeAnalytics/locations/operationResults/read | Ruft das Ergebnis eines Vorgangs des DataLakeAnalytics-Kontos ab. |
> | Aktion | Microsoft.DataLakeAnalytics/locations/usages/read | Ruft die Informationen zum Kontingentbedarf eines Abonnements bezüglich der Verwendung von DataLakeAnalytics ab. |
> | Aktion | Microsoft.DataLakeAnalytics/operations/read | Ruft verfügbare Vorgänge von DataLakeAnalytics ab. |
> | Aktion | Microsoft.DataLakeAnalytics/register/action | Registriert ein Abonnement für DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DataLakeStore/accounts/delete | Löscht ein DataLakeStore-Konto. |
> | Aktion | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Aktiviert KeyVault für ein DataLakeStore-Konto. |
> | Aktion | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Hiermit löschen Sie einen EventGrid-Filter. |
> | Aktion | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Hiermit rufen Sie einen EventGrid-Filter ab. |
> | Aktion | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Hiermit erstellen oder aktualisieren Sie einen Event Grid-Filter. |
> | Aktion | Microsoft.DataLakeStore/accounts/firewallRules/delete | Dient zum Löschen einer Firewallregel. |
> | Aktion | Microsoft.DataLakeStore/accounts/firewallRules/read | Dient zum Abrufen von Informationen zu einer Firewallregel. |
> | Aktion | Microsoft.DataLakeStore/accounts/firewallRules/write | Dient zum Erstellen oder Aktualisieren einer Firewallregel. |
> | Aktion | Microsoft.DataLakeStore/accounts/operationResults/read | Ruft das Ergebnis eines Vorgangs des DataLakeStore-Kontos ab. |
> | Aktion | Microsoft.DataLakeStore/accounts/read | Ruft Informationen zu einem vorhandenen DataLakeStore-Konto ab. |
> | Aktion | Microsoft.DataLakeStore/accounts/Superuser/action | Erteilt Superuser-Berechtigungen für Data Lake Store, sofern diese über „Microsoft.Authorization/roleAssignments/write“ erteilt werden. |
> | Aktion | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Dient zum Löschen eines vertrauenswürdigen Identitätsanbieters. |
> | Aktion | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Dient zum Abrufen von Informationen zu einem vertrauenswürdigen Identitätsanbieter. |
> | Aktion | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Dient zum Erstellen oder Aktualisieren eines vertrauenswürdigen Identitätsanbieters. |
> | Aktion | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Dient zum Löschen einer VNET-Regel. |
> | Aktion | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Hiermit rufen Sie Informationen zu einer VNET-Regel ab. |
> | Aktion | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Dient zum Erstellen oder Aktualisieren einer VNET-Regel. |
> | Aktion | Microsoft.DataLakeStore/accounts/write | Erstellt oder aktualisiert ein DataLakeStore-Konto. |
> | Aktion | Microsoft.DataLakeStore/locations/capability/read | Ruft Funktionsinformationen eines Abonnements bezüglich der Verwendung von DataLakeStore ab. |
> | Aktion | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Überprüft die Verfügbarkeit eines DataLakeStore-Kontonamens. |
> | Aktion | Microsoft.DataLakeStore/locations/operationResults/read | Ruft das Ergebnis eines Vorgangs des DataLakeStore-Kontos ab. |
> | Aktion | Microsoft.DataLakeStore/locations/usages/read | Ruft die Informationen zum Kontingentbedarf eines Abonnements bezüglich der Verwendung von DataLakeStore ab. |
> | Aktion | Microsoft.DataLakeStore/operations/read | Ruft verfügbare Vorgänge von DataLakeStore ab. |
> | Aktion | Microsoft.DataLakeStore/register/action | Registriert ein Abonnement für DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DataMigration/locations/operationResults/read | Hiermit rufen Sie den Status eines Vorgangs mit langer Ausführungsdauer in Zusammenhang mit einer Antwort "202: Akzeptiert" ab. |
> | Aktion | Microsoft.DataMigration/locations/operationStatuses/read | Hiermit rufen Sie den Status eines Vorgangs mit langer Ausführungsdauer in Zusammenhang mit einer Antwort "202: Akzeptiert" ab. |
> | Aktion | Microsoft.DataMigration/register/action | Registriert das Abonnement beim Azure Database Migration Service-Anbieter registriert. |
> | Aktion | Microsoft.DataMigration/services/addWorker/action | Fügt den verfügbaren Workern des Diensts einen DMS-Worker hinzu. |
> | Aktion | Microsoft.DataMigration/services/checkStatus/action | Hiermit überprüfen Sie, ob der Dienst bereitgestellt wurde und ausgeführt wird. |
> | Aktion | Microsoft.DataMigration/services/configureWorker/action | Konfiguriert einen DMS-Worker für die verfügbaren Worker des Diensts. |
> | Aktion | Microsoft.DataMigration/services/delete | Löscht eine Ressource und alle zugehörigen untergeordneten Elemente. |
> | Aktion | Microsoft.DataMigration/services/projects/accessArtifacts/action | Generiert eine URL, die für GET- oder PUT-Vorgänge für Projektartefakte verwendet werden kann. |
> | Aktion | Microsoft.DataMigration/services/projects/delete | Löscht eine Ressource und alle zugehörigen untergeordneten Elemente. |
> | Aktion | Microsoft.DataMigration/services/projects/files/delete | Löscht eine Ressource und alle zugehörigen untergeordneten Elemente. |
> | Aktion | Microsoft.DataMigration/services/projects/files/read | Hiermit lesen Sie Informationen zu Ressourcen. |
> | Aktion | Microsoft.DataMigration/services/projects/files/read/action | Hiermit wird eine URL abgerufen, die zum Lesen des Dateiinhalts verwendet werden kann. |
> | Aktion | Microsoft.DataMigration/services/projects/files/readWrite/action | Hiermit wird eine URL abgerufen, die zum Lesen oder Schreiben des Dateiinhalts verwendet werden kann. |
> | Aktion | Microsoft.DataMigration/services/projects/files/write | Hiermit erstellen oder aktualisieren Sie Ressourcen und ihre Eigenschaften. |
> | Aktion | Microsoft.DataMigration/services/projects/read | Hiermit lesen Sie Informationen zu Ressourcen. |
> | Aktion | Microsoft.DataMigration/services/projects/tasks/cancel/action | Hiermit brechen Sie die Aufgabe ab, wenn sie zurzeit ausgeführt wird. |
> | Aktion | Microsoft.DataMigration/services/projects/tasks/delete | Löscht eine Ressource und alle zugehörigen untergeordneten Elemente. |
> | Aktion | Microsoft.DataMigration/services/projects/tasks/read | Hiermit lesen Sie Informationen zu Ressourcen. |
> | Aktion | Microsoft.DataMigration/services/projects/tasks/write | Hiermit führen Sie Azure Database Migration Service-Aufgaben aus. |
> | Aktion | Microsoft.DataMigration/services/projects/write | Hiermit führen Sie Azure Database Migration Service-Aufgaben aus. |
> | Aktion | Microsoft.DataMigration/services/read | Hiermit lesen Sie Informationen zu Ressourcen. |
> | Aktion | Microsoft.DataMigration/services/removeWorker/action | Entfernt einen DMS-Worker aus den verfügbaren Workern des Diensts. |
> | Aktion | Microsoft.DataMigration/services/serviceTasks/cancel/action | Hiermit brechen Sie die Aufgabe ab, wenn sie zurzeit ausgeführt wird. |
> | Aktion | Microsoft.DataMigration/services/serviceTasks/delete | Löscht eine Ressource und alle zugehörigen untergeordneten Elemente. |
> | Aktion | Microsoft.DataMigration/services/serviceTasks/read | Hiermit lesen Sie Informationen zu Ressourcen. |
> | Aktion | Microsoft.DataMigration/services/serviceTasks/write | Hiermit führen Sie Azure Database Migration Service-Aufgaben aus. |
> | Aktion | Microsoft.DataMigration/services/slots/delete | Löscht eine Ressource und alle zugehörigen untergeordneten Elemente. |
> | Aktion | Microsoft.DataMigration/services/slots/read | Hiermit lesen Sie Informationen zu Ressourcen. |
> | Aktion | Microsoft.DataMigration/services/slots/write | Hiermit erstellen oder aktualisieren Sie Ressourcen und ihre Eigenschaften. |
> | Aktion | Microsoft.DataMigration/services/start/action | Hiermit starten Sie den DMS-Dienst, um das erneute Verarbeiten von Migrationen zuzulassen. |
> | Aktion | Microsoft.DataMigration/services/stop/action | Hiermit beenden Sie den DMS-Dienst, um seine Kosten zu minimieren. |
> | Aktion | Microsoft.DataMigration/services/updateAgentConfig/action | Aktualisiert die Konfiguration des DMS-Agents mit den angegebenen Werten |
> | Aktion | Microsoft.DataMigration/services/write | Hiermit erstellen oder aktualisieren Sie Ressourcen und ihre Eigenschaften. |
> | Aktion | Microsoft.DataMigration/skus/read | Hiermit rufen Sie eine Liste der von den DMS-Ressourcen unterstützten SKUs ab. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Gibt die Ergebnisse von MariaDB-Servervorgängen zurück |
> | Aktion | Microsoft.DBforMariaDB/locations/operationResults/read | Gibt die ResourceGroup basierend auf den Ergebnissen des MariaDB-Servervorgangs zurück |
> | Aktion | Microsoft.DBforMariaDB/locations/operationResults/read | Gibt die Ergebnisse von MariaDB-Servervorgängen zurück |
> | Aktion | Microsoft.DBforMariaDB/locations/performanceTiers/read | Gibt die Liste der verfügbaren Leistungsstufen zurück. |
> | Aktion | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Gibt die Liste der Vorgangsergebnisse der Bedrohungserkennung auf dem Server zurück |
> | Aktion | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Gibt die Liste der Vorgangsergebnisse der Bedrohungserkennung auf dem Server zurück |
> | Aktion | Microsoft.DBforMariaDB/operations/read | Gibt die Liste der MariaDB-Vorgänge zurück |
> | Aktion | Microsoft.DBforMariaDB/performanceTiers/read | Gibt die Liste der verfügbaren Leistungsstufen zurück. |
> | Aktion | Microsoft.DBforMariaDB/register/action | Registriert einen MariaDB-Ressourcenanbieter |
> | Aktion | Microsoft.DBforMariaDB/servers/configurations/read | Gibt die Liste mit den Konfigurationen für einen Server zurück oder ruft die Eigenschaften für die angegebene Konfiguration ab. |
> | Aktion | Microsoft.DBforMariaDB/servers/configurations/write | Aktualisiert den Wert für die angegebene Konfiguration. |
> | Aktion | Microsoft.DBforMariaDB/servers/databases/delete | Löscht eine vorhandene MariaDB-Datenbank |
> | Aktion | Microsoft.DBforMariaDB/servers/databases/read | Gibt die Liste der MariaDB-Datenbanken zurück oder ruft die Eigenschaften für die angegebene Datenbank ab |
> | Aktion | Microsoft.DBforMariaDB/servers/databases/write | Erstellt eine MariaDB-Datenbank mit den angegebenen Parametern oder aktualisiert die Eigenschaften für die angegebene Datenbank |
> | Aktion | Microsoft.DBforMariaDB/servers/delete | Löscht einen vorhandenen Server. |
> | Aktion | Microsoft.DBforMariaDB/servers/firewallRules/delete | Löscht eine vorhandene Firewallregel. |
> | Aktion | Microsoft.DBforMariaDB/servers/firewallRules/read | Gibt die Liste der Firewallregeln für einen Server zurück oder ruft die Eigenschaften für die angegebene Firewallregel ab. |
> | Aktion | Microsoft.DBforMariaDB/servers/firewallRules/write | Erstellt eine Firewallregel mit den angegebenen Parametern oder aktualisiert eine vorhandene Regel. |
> | Aktion | Microsoft.DBforMariaDB/servers/logFiles/read | Gibt die Liste der MariaDB-Protokolldateien zurück |
> | Aktion | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Ruft die verfügbaren Protokolle für MariaDB-Server ab. |
> | Aktion | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Zurückgeben verfügbarer Metriktypen für Datenbanken. |
> | Aktion | Microsoft.DBforMariaDB/servers/queryTexts/action | Gibt die Texte für eine Liste von Abfragen zurück. |
> | Aktion | Microsoft.DBforMariaDB/servers/read | Gibt die Liste der Server zurück oder ruft die Eigenschaften für den angegebenen Server ab. |
> | Aktion | Microsoft.DBforMariaDB/servers/recoverableServers/read | Gibt die wiederherstellbaren MariaDB Server-Informationen zurück. |
> | Aktion | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Dient zum Abrufen von Details zur Richtlinie für die Serverbedrohungserkennung, die für einen bestimmten Server konfiguriert ist. |
> | Aktion | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Dient zum Ändern der Richtlinie für die Serverbedrohungserkennung für einen bestimmten Server. |
> | Aktion | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Gibt die Liste mit der Abfragestatistik für die obersten Abfragen zurück. |
> | Aktion | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Aktualisiert Konfigurationen für den angegebenen Server. |
> | Aktion | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Löscht eine vorhandene VNET-Regel. |
> | Aktion | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Gibt die Liste der VNET-Regeln zurück oder ruft die Eigenschaften für die angegebene VNET-Regel ab. |
> | Aktion | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Erstellt eine VNET-Regel mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für die angegebene VNET-Regel. |
> | Aktion | Microsoft.DBforMariaDB/servers/waitStatistics/read | Gibt Wartestatistiken für eine Instanz zurück. |
> | Aktion | Microsoft.DBforMariaDB/servers/write | Erstellt einen Server mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für den angegebenen Server. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Gibt die Ergebnisse von MySQL-Servervorgängen zurück |
> | Aktion | Microsoft.DBforMySQL/locations/operationResults/read | Gibt die ResourceGroup basierend auf den Ergebnissen des MySQL-Servervorgangs zurück |
> | Aktion | Microsoft.DBforMySQL/locations/operationResults/read | Gibt die Ergebnisse von MySQL-Servervorgängen zurück |
> | Aktion | Microsoft.DBforMySQL/locations/performanceTiers/read | Gibt die Liste der verfügbaren Leistungsstufen zurück. |
> | Aktion | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Gibt die Liste der Vorgangsergebnisse der Bedrohungserkennung auf dem Server zurück |
> | Aktion | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Gibt die Liste der Vorgangsergebnisse der Bedrohungserkennung auf dem Server zurück |
> | Aktion | Microsoft.DBforMySQL/operations/read | Gibt die Liste der MySQL-Vorgänge zurück |
> | Aktion | Microsoft.DBforMySQL/performanceTiers/read | Gibt die Liste der verfügbaren Leistungsstufen zurück. |
> | Aktion | Microsoft.DBforMySQL/register/action | Registriert den MySQL-Ressourcenanbieter |
> | Aktion | Microsoft.DBforMySQL/servers/configurations/read | Gibt die Liste mit den Konfigurationen für einen Server zurück oder ruft die Eigenschaften für die angegebene Konfiguration ab. |
> | Aktion | Microsoft.DBforMySQL/servers/configurations/write | Aktualisiert den Wert für die angegebene Konfiguration. |
> | Aktion | Microsoft.DBforMySQL/servers/databases/delete | Löscht eine vorhandene MySQL-Datenbank |
> | Aktion | Microsoft.DBforMySQL/servers/databases/read | Gibt die Liste der MySQL-Datenbanken zurück oder ruft die Eigenschaften für die angegebene Datenbank ab |
> | Aktion | Microsoft.DBforMySQL/servers/databases/write | Erstellt eine MySQL-Datenbank mit den angegebenen Parametern oder aktualisiert die Eigenschaften für die angegebene Datenbank |
> | Aktion | Microsoft.DBforMySQL/servers/delete | Löscht einen vorhandenen Server. |
> | Aktion | Microsoft.DBforMySQL/servers/firewallRules/delete | Löscht eine vorhandene Firewallregel. |
> | Aktion | Microsoft.DBforMySQL/servers/firewallRules/read | Gibt die Liste der Firewallregeln für einen Server zurück oder ruft die Eigenschaften für die angegebene Firewallregel ab. |
> | Aktion | Microsoft.DBforMySQL/servers/firewallRules/write | Erstellt eine Firewallregel mit den angegebenen Parametern oder aktualisiert eine vorhandene Regel. |
> | Aktion | Microsoft.DBforMySQL/servers/logFiles/read | Gibt die Liste der PostgreSQL-Protokolldateien zurück |
> | Aktion | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Ruft die verfügbaren Protokolle für MySQL-Server ab. |
> | Aktion | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Zurückgeben verfügbarer Metriktypen für Datenbanken. |
> | Aktion | Microsoft.DBforMySQL/servers/queryTexts/action | Gibt die Texte für eine Liste von Abfragen zurück. |
> | Aktion | Microsoft.DBforMySQL/servers/read | Gibt die Liste der Server zurück oder ruft die Eigenschaften für den angegebenen Server ab. |
> | Aktion | Microsoft.DBforMySQL/servers/recoverableServers/read | Gibt die wiederherstellbaren MySQL Server-Informationen zurück. |
> | Aktion | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Dient zum Abrufen von Details zur Richtlinie für die Serverbedrohungserkennung, die für einen bestimmten Server konfiguriert ist. |
> | Aktion | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Dient zum Ändern der Richtlinie für die Serverbedrohungserkennung für einen bestimmten Server. |
> | Aktion | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Gibt die Liste mit der Abfragestatistik für die obersten Abfragen zurück. |
> | Aktion | Microsoft.DBforMySQL/servers/updateConfigurations/action | Aktualisiert Konfigurationen für den angegebenen Server. |
> | Aktion | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Löscht eine vorhandene VNET-Regel. |
> | Aktion | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Gibt die Liste der VNET-Regeln zurück oder ruft die Eigenschaften für die angegebene VNET-Regel ab. |
> | Aktion | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Erstellt eine VNET-Regel mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für die angegebene VNET-Regel. |
> | Aktion | Microsoft.DBforMySQL/servers/waitStatistics/read | Gibt Wartestatistiken für eine Instanz zurück. |
> | Aktion | Microsoft.DBforMySQL/servers/write | Erstellt einen Server mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für den angegebenen Server. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Gibt die Ergebnisse von PostgreSQL-Servervorgängen zurück |
> | Aktion | Microsoft.DBforPostgreSQL/locations/operationResults/read | Gibt die ResourceGroup basierend auf den Ergebnissen des PostgreSQL-Servervorgangs zurück |
> | Aktion | Microsoft.DBforPostgreSQL/locations/operationResults/read | Gibt die Ergebnisse von PostgreSQL-Servervorgängen zurück |
> | Aktion | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Gibt die Liste der verfügbaren Leistungsstufen zurück. |
> | Aktion | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Gibt die Liste der Vorgangsergebnisse der Bedrohungserkennung auf dem Server zurück |
> | Aktion | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Gibt die Liste der Vorgangsergebnisse der Bedrohungserkennung auf dem Server zurück |
> | Aktion | Microsoft.DBforPostgreSQL/operations/read | Gibt die Liste der PostgreSQL-Vorgänge zurück |
> | Aktion | Microsoft.DBforPostgreSQL/performanceTiers/read | Gibt die Liste der verfügbaren Leistungsstufen zurück. |
> | Aktion | Microsoft.DBforPostgreSQL/register/action | Registriert den PostgreSQL-Ressourcenanbieter |
> | Aktion | Microsoft.DBforPostgreSQL/servers/advisors/read | Gibt die Liste mit den Ratgebern zurück. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Gibt die Liste der empfohlenen Aktionen zurück. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Dient dazu, Empfehlungen abzugeben. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/configurations/read | Gibt die Liste mit den Konfigurationen für einen Server zurück oder ruft die Eigenschaften für die angegebene Konfiguration ab. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/configurations/write | Aktualisiert den Wert für die angegebene Konfiguration. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/databases/delete | Löscht eine vorhandene PostgreSQL-Datenbank |
> | Aktion | Microsoft.DBforPostgreSQL/servers/databases/read | Gibt die Liste der PostgreSQL-Datenbanken zurück oder ruft die Eigenschaften für die angegebene Datenbank ab |
> | Aktion | Microsoft.DBforPostgreSQL/servers/databases/write | Erstellt eine PostgreSQL-Datenbank mit den angegebenen Parametern oder aktualisiert die Eigenschaften für die angegebene Datenbank |
> | Aktion | Microsoft.DBforPostgreSQL/servers/delete | Löscht einen vorhandenen Server. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Löscht eine vorhandene Firewallregel. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Gibt die Liste der Firewallregeln für einen Server zurück oder ruft die Eigenschaften für die angegebene Firewallregel ab. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Erstellt eine Firewallregel mit den angegebenen Parametern oder aktualisiert eine vorhandene Regel. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/logFiles/read | Gibt die Liste der PostgreSQL-Protokolldateien zurück |
> | Aktion | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Ruft die verfügbaren Protokolle für Postgres-Server ab. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Zurückgeben verfügbarer Metriktypen für Datenbanken. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Zurückgeben des Texts einer Abfrage |
> | Aktion | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Gibt die Texte für eine Liste von Abfragen zurück. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/read | Gibt die Liste der Server zurück oder ruft die Eigenschaften für den angegebenen Server ab. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Gibt die wiederherstellbaren PostgreSQL Server-Informationen zurück. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Dient zum Abrufen von Details zur Richtlinie für die Serverbedrohungserkennung, die für einen bestimmten Server konfiguriert ist. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Dient zum Ändern der Richtlinie für die Serverbedrohungserkennung für einen bestimmten Server. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Gibt die Liste mit der Abfragestatistik für die obersten Abfragen zurück. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Aktualisiert Konfigurationen für den angegebenen Server. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Löscht eine vorhandene VNET-Regel. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Gibt die Liste der VNET-Regeln zurück oder ruft die Eigenschaften für die angegebene VNET-Regel ab. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Erstellt eine VNET-Regel mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für die angegebene VNET-Regel. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Gibt Wartestatistiken für eine Instanz zurück. |
> | Aktion | Microsoft.DBforPostgreSQL/servers/write | Erstellt einen Server mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für den angegebenen Server. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Gibt die Liste mit den Konfigurationen für einen Server zurück oder ruft die Eigenschaften für die angegebene Konfiguration ab. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Aktualisiert den Wert für die angegebene Konfiguration. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/delete | Löscht einen vorhandenen Server. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Löscht eine vorhandene Firewallregel. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Gibt die Liste der Firewallregeln für einen Server zurück oder ruft die Eigenschaften für die angegebene Firewallregel ab. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Erstellt eine Firewallregel mit den angegebenen Parametern oder aktualisiert eine vorhandene Regel. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Ruft die verfügbaren Protokolle für Postgres-Server ab. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Zurückgeben verfügbarer Metriktypen für Datenbanken. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/read | Gibt die Liste der Server zurück oder ruft die Eigenschaften für den angegebenen Server ab. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Aktualisiert Konfigurationen für den angegebenen Server. |
> | Aktion | Microsoft.DBforPostgreSQL/serversv2/write | Erstellt einen Server mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für den angegebenen Server. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Devices/checkNameAvailability/Action | Dient zum Prüfen, ob ein IotHub-Name verfügbar ist. |
> | Aktion | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Überprüft, ob der Name des Provisioning-Diensts verfügbar ist. |
> | Aktion | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Löscht Zertifikate. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Generiert einen Prüfcode. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Ruft das Zertifikat ab. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Überprüft die Zertifikatsressource. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Erstellt oder aktualisiert das Zertifikat. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Löscht die IotHub-Mandantenressource. |
> | Aktion | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Dient zum Löschen von EventHub-Consumergruppen. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Dient zum Abrufen von EventHub-Consumergruppen. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Dient zum Erstellen von EventHub-Consumergruppen. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Dient zum Exportieren von Geräten. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Ruft die IotHub-Mandantenstatistikressource ab. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Dient zum Importieren von Geräten. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Ruft den IotHub-Mandantenschlüssel ab. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Dient zum Abrufen von Auftragsdetails, die für eine bestimmte IotHub-Instanz übermittelt wurden. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Ruft die IotHub-Mandantenschlüssel ab. |
> | Aktion | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Ruft die verfügbaren Protokolldefinitionen für den IotHub-Dienst ab. |
> | Aktion | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Ruft die verfügbaren Metriken für den IotHub-Dienst ab. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Dient zum Abrufen von Kontingentmetriken. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/Read | Ruft die IotHub-Mandantenressource ab. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Dient zum Testen einer Nachricht mit allen vorhandenen Routen. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Dient zum Testen einer Nachricht mit einer angegebenen Testroute. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Ruft die Integrität aller Routingendpunkte für eine IotHub-Instanz ab. |
> | Aktion | Microsoft.Devices/elasticPools/iotHubTenants/Write | Erstellt oder aktualisiert die IotHub-Mandantenressource. |
> | Aktion | Microsoft.Devices/ElasticPools/metricDefinitions/read | Ruft die verfügbaren Metriken für den IotHub-Dienst ab. |
> | Aktion | Microsoft.Devices/iotHubs/certificates/Delete | Löscht Zertifikate. |
> | Aktion | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generiert einen Prüfcode. |
> | Aktion | Microsoft.Devices/iotHubs/certificates/Read | Ruft das Zertifikat ab. |
> | Aktion | Microsoft.Devices/iotHubs/certificates/verify/Action | Überprüft die Zertifikatsressource. |
> | Aktion | Microsoft.Devices/iotHubs/certificates/Write | Erstellt oder aktualisiert das Zertifikat. |
> | Aktion | Microsoft.Devices/iotHubs/Delete | Dient zum Löschen von IotHub-Ressourcen. |
> | Aktion | Microsoft.Devices/IotHubs/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.Devices/IotHubs/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Löscht den Event Grid-Filter. |
> | Aktion | Microsoft.Devices/iotHubs/eventGridFilters/Read | Ruft den Event Grid-Filter ab. |
> | Aktion | Microsoft.Devices/iotHubs/eventGridFilters/Write | Erstellt einen neuen Event Grid-Filter oder aktualisiert einen vorhandenen Event Grid-Filter. |
> | Aktion | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Dient zum Löschen von EventHub-Consumergruppen. |
> | Aktion | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Dient zum Abrufen von EventHub-Consumergruppen. |
> | Aktion | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Dient zum Erstellen von EventHub-Consumergruppen. |
> | Aktion | Microsoft.Devices/iotHubs/exportDevices/Action | Dient zum Exportieren von Geräten. |
> | Aktion | Microsoft.Devices/iotHubs/importDevices/Action | Dient zum Importieren von Geräten. |
> | Aktion | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Dient zum Abrufen des IotHub-Schlüssels für den angegebenen Namen. |
> | Aktion | Microsoft.Devices/iotHubs/iotHubStats/Read | Dient zum Abrufen der IotHub-Statistik. |
> | Aktion | Microsoft.Devices/iotHubs/jobs/Read | Dient zum Abrufen von Auftragsdetails, die für eine bestimmte IotHub-Instanz übermittelt wurden. |
> | Aktion | Microsoft.Devices/iotHubs/listkeys/Action | Dient zum Abrufen aller IotHub-Schlüssel. |
> | Aktion | Microsoft.Devices/IotHubs/logDefinitions/read | Ruft die verfügbaren Protokolldefinitionen für den IotHub-Dienst ab. |
> | Aktion | Microsoft.Devices/IotHubs/metricDefinitions/read | Ruft die verfügbaren Metriken für den IotHub-Dienst ab. |
> | Aktion | Microsoft.Devices/iotHubs/operationresults/Read | Hiermit rufen Sie das Vorgangsergebnis (veraltete API) ab. |
> | Aktion | Microsoft.Devices/iotHubs/quotaMetrics/Read | Dient zum Abrufen von Kontingentmetriken. |
> | Aktion | Microsoft.Devices/iotHubs/Read | Ruft die IotHub-Ressourcen ab. |
> | Aktion | Microsoft.Devices/iotHubs/routing/$testall/Action | Dient zum Testen einer Nachricht mit allen vorhandenen Routen. |
> | Aktion | Microsoft.Devices/iotHubs/routing/$testnew/Action | Dient zum Testen einer Nachricht mit einer angegebenen Testroute. |
> | Aktion | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Ruft die Integrität aller Routingendpunkte für eine IotHub-Instanz ab. |
> | Aktion | Microsoft.Devices/iotHubs/skus/Read | Dient zum Abrufen gültiger IotHub-SKUs. |
> | Aktion | Microsoft.Devices/iotHubs/Write | Dient zum Erstellen oder Aktualisieren von IotHub-Ressourcen. |
> | Aktion | Microsoft.Devices/locations/operationresults/Read | Ruft das standortbasierte Vorgangsergebnis ab. |
> | Aktion | Microsoft.Devices/operationresults/Read | Hiermit rufen Sie das Vorgangsergebnis ab. |
> | Aktion | Microsoft.Devices/operations/Read | Dient zum Abrufen aller ResourceProvider-Vorgänge. |
> | Aktion | Microsoft.Devices/provisioningServices/certificates/Delete | Löscht Zertifikate. |
> | Aktion | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generiert einen Prüfcode. |
> | Aktion | Microsoft.Devices/provisioningServices/certificates/Read | Ruft das Zertifikat ab. |
> | Aktion | Microsoft.Devices/provisioningServices/certificates/verify/Action | Überprüft die Zertifikatsressource. |
> | Aktion | Microsoft.Devices/provisioningServices/certificates/Write | Erstellt oder aktualisiert das Zertifikat. |
> | Aktion | Microsoft.Devices/provisioningServices/Delete | Löscht die IotDps-Ressource. |
> | Aktion | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Ruft IotDps-Schlüssel für Schlüsselnamen ab. |
> | Aktion | Microsoft.Devices/provisioningServices/listkeys/Action | Ruft alle IotDps-Schlüssel ab. |
> | Aktion | Microsoft.Devices/provisioningServices/logDefinitions/read | Ruft die verfügbaren Protokolldefinitionen für den Bereitstellungsdienst ab. |
> | Aktion | Microsoft.Devices/provisioningServices/metricDefinitions/read | Ruft die verfügbaren Metriken für den Bereitstellungsdienst ab. |
> | Aktion | Microsoft.Devices/provisioningServices/operationresults/Read | Hiermit rufen Sie das DPS-Vorgangsergebnis ab. |
> | Aktion | Microsoft.Devices/provisioningServices/Read | Ruft die IotDps-Ressource ab. |
> | Aktion | Microsoft.Devices/provisioningServices/skus/Read | Ruft gültige IotDps-SKUs ab. |
> | Aktion | Microsoft.Devices/provisioningServices/Write | Erstellt die IotDps-Ressource. |
> | Aktion | Microsoft.Devices/register/action | Dient zum Registrieren des Abonnements für den IotHub-Ressourcenanbieter und ermöglicht die Erstellung von IotHub-Ressourcen. |
> | Aktion | Microsoft.Devices/register/action | Dient zum Registrieren des Abonnements für den IotHub-Ressourcenanbieter und ermöglicht die Erstellung von IotHub-Ressourcen. |
> | Aktion | Microsoft.Devices/usages/Read | Dient zum Abrufen von Details zur Abonnementnutzung für diesen Anbieter. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DevSpaces/controllers/delete | Löscht den Azure Dev Spaces-Controller und Dataplane-Dienste. |
> | Aktion | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Listet Verbindungsdetails für die Infrastruktur des Azure Dev Spaces-Controllers auf. |
> | Aktion | Microsoft.DevSpaces/controllers/read | Liest Azure Dev Spaces-Controllereigenschaften. |
> | Aktion | Microsoft.DevSpaces/controllers/write | Erstellt oder aktualisiert Azure Dev Spaces-Controllereigenschaften. |
> | Aktion | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Überprüft die bestehende Controllerzuordnung für einen Containerhost |
> | Aktion | Microsoft.DevSpaces/locations/operationresults/read | Liest den Status eines asynchronen Vorgangs |
> | Aktion | Microsoft.DevSpaces/register/action | Registriert den Microsoft Dev Spaces-Ressourcenanbieter bei einem Abonnement. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DevTestLab/labCenters/delete | Löscht die Lab-Center. |
> | Aktion | Microsoft.DevTestLab/labCenters/read | Liest die Lab-Center. |
> | Aktion | Microsoft.DevTestLab/labCenters/write | Dient zum Hinzufügen oder Ändern von Lab-Centern. |
> | Aktion | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Dient zum Lesen von Azure Resource Manager-Vorlagen. |
> | Aktion | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Generiert eine ARM-Vorlage für das angegebene Artefakt, lädt die erforderlichen Dateien in ein Speicherkonto hoch und überprüft das generierte Artefakt. |
> | Aktion | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Dient zum Lesen von Artefakten. |
> | Aktion | Microsoft.DevTestLab/labs/artifactSources/delete | Dient zum Löschen von Artefaktquellen. |
> | Aktion | Microsoft.DevTestLab/labs/artifactSources/read | Dient zum Lesen von Artefaktquellen. |
> | Aktion | Microsoft.DevTestLab/labs/artifactSources/write | Dient zum Hinzufügen oder Ändern von Artefaktquellen. |
> | Aktion | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Dient zum Anfordern eines beliebigen anforderbaren virtuellen Computers im Lab. |
> | Aktion | Microsoft.DevTestLab/labs/costs/read | Dient zum Lesen von Kosten. |
> | Aktion | Microsoft.DevTestLab/labs/costs/write | Dient zum Hinzufügen oder Ändern von Kosten. |
> | Aktion | Microsoft.DevTestLab/labs/CreateEnvironment/action | Dient zum Erstellen von virtuellen Computern in einem Lab. |
> | Aktion | Microsoft.DevTestLab/labs/customImages/delete | Dient zum Löschen benutzerdefinierter Images. |
> | Aktion | Microsoft.DevTestLab/labs/customImages/read | Dient zum Lesen benutzerdefinierter Images. |
> | Aktion | Microsoft.DevTestLab/labs/customImages/write | Dient zum Hinzufügen oder Ändern benutzerdefinierter Images. |
> | Aktion | Microsoft.DevTestLab/labs/delete | Dient zum Löschen von Labs. |
> | Aktion | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Stellt sicher, dass der aktuelle Benutzer über ein gültiges Profil im Lab verfügt |
> | Aktion | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exportiert die Labressourcenverwendung in ein Speicherkonto. |
> | Aktion | Microsoft.DevTestLab/labs/formulas/delete | Dient zum Löschen von Formeln. |
> | Aktion | Microsoft.DevTestLab/labs/formulas/read | Dient zum Lesen von Formeln. |
> | Aktion | Microsoft.DevTestLab/labs/formulas/write | Dient zum Hinzufügen oder Ändern von Formeln. |
> | Aktion | Microsoft.DevTestLab/labs/galleryImages/read | Dient zum Lesen von Katalogimages. |
> | Aktion | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Dient zum Generieren eines URI für das Hochladen benutzerdefinierter Datenträgerimages in ein Lab. |
> | Aktion | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importiert einen virtuellen Computer in ein anderes Lab. |
> | Aktion | Microsoft.DevTestLab/labs/ListVhds/action | Dient zum Auflisten von Datenträgerimages, die für die Erstellung benutzerdefinierter Images verfügbar sind. |
> | Aktion | Microsoft.DevTestLab/labs/notificationChannels/delete | Dient zum Löschen von Benachrichtigungskanälen. |
> | Aktion | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Dient zum Senden einer Benachrichtigung an einen angegebenen Kanal. |
> | Aktion | Microsoft.DevTestLab/labs/notificationChannels/read | Dient zum Lesen von Benachrichtigungskanälen. |
> | Aktion | Microsoft.DevTestLab/labs/notificationChannels/write | Dient zum Hinzufügen oder Ändern von Benachrichtigungskanälen. |
> | Aktion | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Wertet Labrichtlinien aus. |
> | Aktion | Microsoft.DevTestLab/labs/policySets/policies/delete | Dient zum Löschen von Richtlinien. |
> | Aktion | Microsoft.DevTestLab/labs/policySets/policies/read | Dient zum Lesen von Richtlinien. |
> | Aktion | Microsoft.DevTestLab/labs/policySets/policies/write | Dient zum Hinzufügen oder Ändern von Richtlinien. |
> | Aktion | Microsoft.DevTestLab/labs/policySets/read | Liest Richtliniensätze. |
> | Aktion | Microsoft.DevTestLab/labs/read | Dient zum Lesen von Labs. |
> | Aktion | Microsoft.DevTestLab/labs/schedules/delete | Dient zum Löschen von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/labs/schedules/Execute/action | Dient zum Ausführen eines Zeitplans. |
> | Aktion | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Listet alle anwendbaren Zeitpläne auf. |
> | Aktion | Microsoft.DevTestLab/labs/schedules/read | Dient zum Lesen von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/labs/schedules/write | Dient zum Hinzufügen oder Ändern von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/labs/serviceRunners/delete | Dient zum Löschen von Dienstausführern. |
> | Aktion | Microsoft.DevTestLab/labs/serviceRunners/read | Dient zum Lesen von Dienstausführern. |
> | Aktion | Microsoft.DevTestLab/labs/serviceRunners/write | Dient zum Hinzufügen oder Ändern von Dienstausführern. |
> | Aktion | Microsoft.DevTestLab/labs/sharedGalleries/delete | Löscht freigegebene Kataloge |
> | Aktion | Microsoft.DevTestLab/labs/sharedGalleries/read | Liest freigegebene Kataloge |
> | Aktion | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Löscht freigegebene Bilder |
> | Aktion | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Liest freigegebene Bilder |
> | Aktion | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Fügt freigegebene Bilder hinzu oder ändert sie |
> | Aktion | Microsoft.DevTestLab/labs/sharedGalleries/write | Fügt freigegebene Kataloge hinzu oder ändert sie |
> | Aktion | Microsoft.DevTestLab/labs/users/delete | Dient zum Löschen von Benutzerprofilen. |
> | Aktion | Microsoft.DevTestLab/labs/users/disks/Attach/action | Dient zum Anfügen des Datenträgers an den virtuellen Computer sowie zum Erstellen der Datenträgerlease für den virtuellen Computer. |
> | Aktion | Microsoft.DevTestLab/labs/users/disks/delete | Dient zum Löschen von Datenträgern. |
> | Aktion | Microsoft.DevTestLab/labs/users/disks/Detach/action | Dient zum Trennen des an den virtuellen Computer angefügten Datenträgers sowie zum Beenden der Datenträgerlease für den virtuellen Computer. |
> | Aktion | Microsoft.DevTestLab/labs/users/disks/read | Dient zum Lesen von Datenträgern. |
> | Aktion | Microsoft.DevTestLab/labs/users/disks/write | Dient zum Hinzufügen oder Ändern von Datenträgern. |
> | Aktion | Microsoft.DevTestLab/labs/users/environments/delete | Dient zum Löschen von Umgebungen. |
> | Aktion | Microsoft.DevTestLab/labs/users/environments/read | Dient zum Lesen von Umgebungen. |
> | Aktion | Microsoft.DevTestLab/labs/users/environments/write | Dient zum Hinzufügen oder Ändern von Umgebungen. |
> | Aktion | Microsoft.DevTestLab/labs/users/read | Dient zum Lesen von Benutzerprofilen. |
> | Aktion | Microsoft.DevTestLab/labs/users/secrets/delete | Dient zum Löschen von Geheimnissen. |
> | Aktion | Microsoft.DevTestLab/labs/users/secrets/read | Dient zum Lesen von Geheimnissen. |
> | Aktion | Microsoft.DevTestLab/labs/users/secrets/write | Dient zum Hinzufügen oder Ändern von Geheimnissen. |
> | Aktion | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Löscht Service Fabrics. |
> | Aktion | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Listet die anwendbaren Zeitpläne zum Starten/Beenden auf, sofern vorhanden. |
> | Aktion | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Liest Service Fabrics. |
> | Aktion | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Dient zum Löschen von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Dient zum Ausführen eines Zeitplans. |
> | Aktion | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Dient zum Lesen von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Dient zum Hinzufügen oder Ändern von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Startet ein Service Fabric. |
> | Aktion | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Beendet ein Service Fabric. |
> | Aktion | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Dient zum Hinzufügen oder Ändern von Service Fabrics. |
> | Aktion | Microsoft.DevTestLab/labs/users/write | Dient zum Hinzufügen oder Ändern von Benutzerprofilen. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Dient zum Anfügen eines neuen oder vorhandenen Datenträgers an virtuelle Computer. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Dient zum Anwenden von Artefakten auf virtuelle Computer. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Dient dazu, einen vorhandenen virtuellen Computer in Besitz zu nehmen. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/delete | Dient zum Löschen virtueller Computer. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Dient zum Trennen des angegebenen Datenträgers vom virtuellen Computer. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Ruft eine Zeichenfolge ab, die den Inhalt der RDP-Datei für den virtuellen Computer darstellt. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Listet die anwendbaren Zeitpläne zum Starten/Beenden auf, sofern vorhanden. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/read | Dient zum Lesen virtueller Computer. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Stellt einen virtuellen Computer erneut bereit. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Ändert die Größe eines virtuellen Computers. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Startet einen virtuellen Computer. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Dient zum Löschen von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Dient zum Ausführen eines Zeitplans. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Dient zum Lesen von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Dient zum Hinzufügen oder Ändern von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Dient zum Starten eines virtuellen Computers. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Dient zum Beenden eines virtuellen Computers. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Überträgt den Besitz aller an den virtuellen Computer angefügten Datenträger auf den aktuellen Benutzer. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Hiermit geben Sie den Besitz eines vorhandenen virtuellen Computers frei. |
> | Aktion | Microsoft.DevTestLab/labs/virtualMachines/write | Dient zum Hinzufügen oder Ändern virtueller Computer. |
> | Aktion | Microsoft.DevTestLab/labs/virtualNetworks/delete | Dient zum Löschen virtueller Netzwerke. |
> | Aktion | Microsoft.DevTestLab/labs/virtualNetworks/read | Dient zum Lesen virtueller Netzwerke. |
> | Aktion | Microsoft.DevTestLab/labs/virtualNetworks/write | Dient zum Hinzufügen oder Ändern virtueller Netzwerke. |
> | Aktion | Microsoft.DevTestLab/labs/vmPools/delete | Löscht Pools für virtuelle Computer. |
> | Aktion | Microsoft.DevTestLab/labs/vmPools/read | Liest Pools für virtuelle Computer. |
> | Aktion | Microsoft.DevTestLab/labs/vmPools/write | Fügt Pools für virtuelle Computer hinzu oder ändert diese. |
> | Aktion | Microsoft.DevTestLab/labs/write | Dient zum Hinzufügen oder Ändern von Labs. |
> | Aktion | Microsoft.DevTestLab/locations/operations/read | Dient zum Lesen von Vorgängen. |
> | Aktion | Microsoft.DevTestLab/register/action | Registriert das Abonnement. |
> | Aktion | Microsoft.DevTestLab/schedules/delete | Dient zum Löschen von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/schedules/Execute/action | Dient zum Ausführen eines Zeitplans. |
> | Aktion | Microsoft.DevTestLab/schedules/read | Dient zum Lesen von Zeitplänen. |
> | Aktion | Microsoft.DevTestLab/schedules/Retarget/action | Aktualisiert die Zielressourcen-ID eines Zeitplans. |
> | Aktion | Microsoft.DevTestLab/schedules/write | Dient zum Hinzufügen oder Ändern von Zeitplänen. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DocumentDB/databaseAccountNames/read | Prüft die Verfügbarkeit des Namens. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Löscht eine Auflistung. Gilt nur für den API-Typ „mongodb“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Liest eine Auflistung oder listet alle Auflistungen auf. Gilt nur für den API-Typ „mongodb“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/throughput/read | Liest den Durchsatz einer Auflistung. Gilt nur für den API-Typ „mongodb“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/throughput/write | Aktualisiert den Durchsatz einer Auflistung. Gilt nur für den API-Typ „mongodb“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Erstellt oder aktualisiert eine Auflistung. Gilt nur für den API-Typ „mongodb“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Löschen eines Containers. Gilt nur für den API-Typ „sql“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Liest einen Container oder listet alle Container auf. Gilt nur für den API-Typ „sql“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/throughput/read | Liest den Containerdurchsatz. Gilt nur für den API-Typ „sql“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/throughput/write | Aktualisiert einen Containerdurchsatz. Gilt nur für den API-Typ „sql“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Erstellt oder aktualisiert einen Container. Gilt nur für den API-Typ „sql“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Löscht eine Datenbank. Gilt nur für die API-Typen „sql“, „mongodb“ und „gremlin“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Löscht einen Graphen. Gilt nur für den API-Typ „gremlin“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Liest einen Graphen oder listet alle Graphen auf. Gilt nur für den API-Typ „gremlin“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/throughput/read | Liest den Durchsatz eines Graphen. Gilt nur für den API-Typ „gremlin“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/throughput/write | Aktualisiert den Durchsatz eines Graphen. Gilt nur für den API-Typ „gremlin“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Erstellt oder aktualisiert einen Graphen. Gilt nur für den API-Typ „gremlin“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Liest eine Datenbank oder listet alle Datenbanken auf. Gilt nur für die API-Typen „sql“, „mongodb“ und „gremlin“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/throughput/read | Liest den Durchsatz einer Datenbank. Gilt nur für die API-Typen „sql“, „mongodb“ und „gremlin“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/throughput/write | Aktualisiert den Durchsatz einer Datenbank. Gilt nur für die API-Typen „sql“, „mongodb“ und „gremlin“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Erstellen einer Datenbank Gilt nur für die API-Typen „sql“, „mongodb“ und „gremlin“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Löscht einen Keyspace. Gilt nur für den API-Typ „cassandra“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Liest einen Keyspace oder listet alle Keyspaces auf. Gilt nur für den API-Typ „cassandra“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/throughput/read | Liest den Durchsatz eines Keyspace. Gilt nur für den API-Typ „cassandra“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/throughput/write | Aktualisiert den Durchsatz eines Keyspace. Gilt nur für den API-Typ „cassandra“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Löscht eine Tabelle. Gilt nur für den API-Typ „cassandra“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Liest eine Tabelle oder listet alle Tabellen auf. Gilt nur für den API-Typ „cassandra“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/throughput/read | Liest den Durchsatz einer Tabelle. Gilt nur für den API-Typ „cassandra“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/throughput/write | Aktualisiert den Durchsatz einer Tabelle. Gilt nur für den API-Typ „cassandra“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Erstellt oder aktualisiert eine Tabelle. Gilt nur für den API-Typ „cassandra“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Erstellt einen Keyspace. Gilt nur für den API-Typ „cassandra“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Löscht eine Tabelle. Gilt nur für den API-Typ „table“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Liest eine Tabelle oder listet alle Tabellen auf. Gilt nur für den API-Typ „table“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/throughput/read | Liest den Durchsatz einer Tabelle. Gilt nur für den API-Typ „table“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/throughput/write | Aktualisiert den Durchsatz einer Tabelle. Gilt nur für den API-Typ „table“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Erstellt oder aktualisiert eine Tabelle. Gilt nur für den API-Typ „table“. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/backup/action | Sendet eine Anforderung zum Konfigurieren der Sicherung. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Dient zum Ändern der Ressourcengruppe eines Datenbankkontos. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Liest die Sammlungsmetrikdefinitionen. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Liest die Sammlungsmetriken. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Liest Metriken auf Partitionsschlüsselebene eines Datenbankkontos. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Liest Metriken auf Partitionsebene eines Datenbankkontos. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Liest die Partitionen eines Datenbankkontos in einer Collection. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Liest Verwendungen auf Partitionsebene eines Datenbankkontos. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Liest Informationen zur Sammlungsverwendung. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Liest die Datenbankmetrikdefinitionen. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Liest die Datenbankmetriken. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Liest Informationen zur Datenbankverwendung. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/delete | Löscht die Datenbankkonten. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Dient zum Ändern der Failoverprioritäten von Regionen eines Datenbankkontos. Wird für manuelle Failovervorgänge verwendet. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Dient zum Abrufen der Verbindungszeichenfolgen für ein Datenbankkonto. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Dient zum Auflisten von Schlüsseln eines Datenbankkontos. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Liest die Metrikdefinitionen für Datenbankkonten. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/metrics/read | Liest die Datenbankkontometriken. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Hiermit setzen Sie einen Bereich eines Datenbankkontos offline.  |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Hiermit setzen Sie einen Bereich eines Datenbankkontos online. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Liest den Status eines asynchronen Vorgangs. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Liest Latenzmetriken. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/percentile/read | Liest Perzentile von Replikationslatenzen. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Liest die Latenzmetriken für eine bestimmte Quell- und Zielregion. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Liest die Latenzmetriken für eine bestimmte Zielregion. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Löscht einen Verbindungsproxy eines privaten Endpunkts eines Datenbankkontos |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | Liest den Status eines asynchronen Vorgangs von privateEndpointConnectionProxies |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Liest einen Verbindungsproxy eines privaten Endpunkts eines Datenbankkontos |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Überprüft einen Verbindungsproxy eines privaten Endpunkts eines Datenbankkontos |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Erstellt oder aktualisiert einen Verbindungsproxy eines privaten Endpunkts eines Datenbankkontos |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/read | Liest ein Datenbankkonto. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Liest die schreibgeschützten Schlüssel für Datenbankkonten. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Liest die schreibgeschützten Schlüssel für Datenbankkonten. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Dient zum Rotieren von Schlüsseln eines Datenbankkontos. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Liest die Metriken von regionalen Collections. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Liest regionale Metriken auf Partitionsschlüsselebene eines Datenbankkontos. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Liest regionale Metriken auf Partitionsebene eines Datenbankkontos. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Liest die Partitionen eines regionalen Datenbankkontos in einer Collection. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Liest die Region und die Datenbankkontometriken. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/restore/action | Sendet eine Wiederherstellungsanforderung. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/usages/read | Liest Informationen zur Datenbankkontoverwendung. |
> | Aktion | Microsoft.DocumentDB/databaseAccounts/write | Dient zum Aktualisieren von Datenbankkonten. |
> | Aktion | Microsoft.DocumentDB/locations/asyncoperations/read | Liest den Status asynchroner Vorgänge |
> | Aktion | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Benachrichtigt Microsoft.DocumentDB darüber, dass das virtuelle Netzwerk oder Subnetz gelöscht wird. |
> | Aktion | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Liest den Status des asynchronen Vorgangs „deleteVirtualNetworkOrSubnets“. |
> | Aktion | Microsoft.DocumentDB/operationResults/read | Liest den Status eines asynchronen Vorgangs. |
> | Aktion | Microsoft.DocumentDB/operations/read | Liest verfügbare Vorgänge für Microsoft DocumentDB  |
> | Aktion | Microsoft.DocumentDB/register/action |  Dient zum Registrieren des Microsoft DocumentDB-Ressourcenanbieters für das Abonnement. |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.DomainRegistration/checkDomainAvailability/Action | Dient zum Prüfen, ob eine Domäne erworben werden kann. |
> | Aktion | Microsoft.DomainRegistration/domains/Delete | Dient zum Löschen einer vorhandenen Domäne. |
> | Aktion | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Löscht die Besitz-ID. |
> | Aktion | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Listet die Besitz-IDs auf. |
> | Aktion | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Ruft die Besitz-ID ab. |
> | Aktion | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Erstellt oder aktualisiert eine ID. |
> | Aktion | Microsoft.DomainRegistration/domains/operationresults/Read | Dient zum Abrufen eines Domänenvorgangs. |
> | Aktion | Microsoft.DomainRegistration/domains/Read | Dient zum Abrufen der Domänenliste. |
> | Aktion | Microsoft.DomainRegistration/domains/Read | Ruft die Domäne ab. |
> | Aktion | Microsoft.DomainRegistration/domains/renew/Action | Dient zum Verlängern einer vorhandenen Domäne. |
> | Aktion | Microsoft.DomainRegistration/domains/Write | Dient zum Hinzufügen einer neuen Domäne oder zum Aktualisieren einer bereits vorhandenen. |
> | Aktion | Microsoft.DomainRegistration/generateSsoRequest/Action | Dient zum Generieren einer Anforderung für die Anmeldung beim Domänensteuerungszentrum. |
> | Aktion | Microsoft.DomainRegistration/listDomainRecommendations/Action | Dient zum Abrufen der Liste mit Domänenempfehlungen auf der Grundlage von Schlüsselwörtern. |
> | Aktion | Microsoft.DomainRegistration/operations/Read | Dient zum Auflisten aller Vorgänge über die App Service-Domänenregistrierung. |
> | Aktion | Microsoft.DomainRegistration/register/action | Dient zum Registrieren des Microsoft-Domänenressourcenanbieters für das Abonnement. |
> | Aktion | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Listet die Vereinbarungsaktion auf. |
> | Aktion | Microsoft.DomainRegistration/topLevelDomains/Read | Ruft die Domänen der obersten Domäne ab. |
> | Aktion | Microsoft.DomainRegistration/topLevelDomains/Read | Ruft die Domäne der obersten Domäne ab. |
> | Aktion | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Dient zum Überprüfen eines Domänenerwerbsobjekts, ohne es zu übermitteln. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.EventGrid/domains/delete | Löscht eine Domäne. |
> | Aktion | Microsoft.EventGrid/domains/listKeys/action | Listet die Schlüssel für eine Domäne auf. |
> | Aktion | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für Domänen ab. |
> | Aktion | Microsoft.EventGrid/domains/read | Liest eine Domäne. |
> | Aktion | Microsoft.EventGrid/domains/regenerateKey/action | Generiert einen Schlüssel für eine Domäne neu. |
> | Aktion | Microsoft.EventGrid/domains/topics/read | Liest ein Domänenthema. |
> | Aktion | Microsoft.EventGrid/domains/write | Erstellt oder aktualisiert eine Domäne. |
> | Aktion | Microsoft.EventGrid/eventSubscriptions/delete | Löscht eventSubscription. |
> | Aktion | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Ruft die vollständige URL für das Ereignisabonnement ab. |
> | Aktion | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für Ereignisabonnements ab. |
> | Aktion | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für Ereignisabonnements. |
> | Aktion | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für eventSubscriptions ab. |
> | Aktion | Microsoft.EventGrid/eventSubscriptions/read | Liest eventSubscription. |
> | Aktion | Microsoft.EventGrid/eventSubscriptions/write | Erstellt oder aktualisiert eventSubscription. |
> | Aktion | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Themen ab. |
> | Aktion | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für Themen. |
> | Aktion | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für Themen ab. |
> | Aktion | Microsoft.EventGrid/extensionTopics/read | Liest ein extensionTopic. |
> | Aktion | Microsoft.EventGrid/locations/eventSubscriptions/read | Listet regionale Ereignisabonnements auf. |
> | Aktion | Microsoft.EventGrid/locations/operationResults/read | Liest das Ergebnis eines regionalen Vorgangs. |
> | Aktion | Microsoft.EventGrid/locations/operationsStatus/read | Liest den Status eines regionalen Vorgangs. |
> | Aktion | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Listet regionale Ereignisabonnements nach Thematyp auf. |
> | Aktion | Microsoft.EventGrid/operationResults/read | Liest das Ergebnis eines Vorgangs. |
> | Aktion | Microsoft.EventGrid/operations/read | Listet EventGrid-Vorgänge auf. |
> | Aktion | Microsoft.EventGrid/operationsStatus/read | Liest den Status eines Vorgangs. |
> | Aktion | Microsoft.EventGrid/register/action | Registriert das Abonnement für den EventGrid-Ressourcenanbieter. |
> | Aktion | Microsoft.EventGrid/topics/delete | Löschen eines Themas |
> | Aktion | Microsoft.EventGrid/topics/listKeys/action | Listet die Schlüssel für ein Thema auf. |
> | Aktion | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Themen ab. |
> | Aktion | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für Themen. |
> | Aktion | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für Themen ab. |
> | Aktion | Microsoft.EventGrid/topics/read | Liest ein Thema. |
> | Aktion | Microsoft.EventGrid/topics/regenerateKey/action | Generiert einen Schlüssel für ein Thema neu. |
> | Aktion | Microsoft.EventGrid/topics/write | Erstellt oder aktualisiert ein Thema. |
> | Aktion | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Listet globale Ereignisabonnements nach Thematyp auf. |
> | Aktion | Microsoft.EventGrid/topictypes/eventtypes/read | Liest die von einem Thementyp unterstützten Ereignistypen. |
> | Aktion | Microsoft.EventGrid/topictypes/read | Liest einen Thementyp. |
> | Aktion | Microsoft.EventGrid/unregister/action | Hebt die Registrierung des Abonnements für den EventGrid-Ressourcenanbieter auf. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.EventHub/checkNameAvailability/action | Prüft die Verfügbarkeit des Namespaces unter dem angegebenen Abonnement. |
> | Aktion | Microsoft.EventHub/checkNamespaceAvailability/action | Prüft die Verfügbarkeit des Namespaces unter dem angegebenen Abonnement. Diese API ist veraltet. Verwenden Sie stattdessen CheckNameAvailability. |
> | Aktion | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Ruft die Liste der Ressourcenbeschreibungen für Clustermetriken ab. |
> | Aktion | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Löscht die VNET-Regeln im EventHub-Ressourcenanbieter für das angegebene VNET. |
> | Aktion | Microsoft.EventHub/namespaces/authorizationRules/action | Dient zum Aktualisieren von Namespace-Autorisierungsregeln. Diese API ist veraltet. Verwenden Sie stattdessen einen PUT-Aufruf, um die Namespace-Autorisierungsregel zu aktualisieren. Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. |
> | Aktion | Microsoft.EventHub/namespaces/authorizationRules/delete | Dient zum Löschen von Namespace-Autorisierungsregeln. Die standardmäßige Namespace-Autorisierungsregel kann nicht gelöscht werden.  |
> | Aktion | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Dient zum Abrufen der Verbindungszeichenfolge für den Namespace. |
> | Aktion | Microsoft.EventHub/namespaces/authorizationRules/read | Dient zum Abrufen der Liste mit Beschreibungen für Namespace-Autorisierungsregeln. |
> | Aktion | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Dient zum erneuten Generieren des primären oder sekundären Schlüssels für die Ressource. |
> | Aktion | Microsoft.EventHub/namespaces/authorizationRules/write | Dient zum Erstellen von Autorisierungsregeln auf Namespaceebene und zum Aktualisieren der dazugehörigen Eigenschaften. Aktualisiert werden können die Zugriffsrechte für Autorisierungsregeln sowie der primäre und der sekundäre Schlüssel. |
> | Aktion | Microsoft.EventHub/namespaces/Delete | Dient zum Löschen von Namespaceressourcen. |
> | Aktion | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Ruft die Schlüssel der Autorisierungsregeln für den primären Namespace für die Notfallwiederherstellung. |
> | Aktion | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Ruft die Autorisierungsregeln des primären Namespace für die Notfallwiederherstellung ab. |
> | Aktion | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Hiermit wird die Notfallwiederherstellung deaktiviert und die Replikation von Änderungen vom primären Namespace zu sekundären Namespaces beendet. |
> | Aktion | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Prüft die Verfügbarkeit des Namespacealias unter dem angegebenen Abonnement. |
> | Aktion | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Hiermit wird die dem Namespace zugeordnete Notfallwiederherstellungskonfiguration gelöscht. Dieser Vorgang kann nur über den primären Namespace aufgerufen werden. |
> | Aktion | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Hiermit wird ein Failover bei der georedundanten Notfallwiederherstellung ausgelöst und der Namespacealias neu konfiguriert, um auf den sekundären Namespace zu zeigen. |
> | Aktion | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Hiermit wird die dem Namespace zugeordnete Notfallwiederherstellungskonfiguration abgerufen. |
> | Aktion | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Hiermit wird die dem Namespace zugeordnete Notfallwiederherstellungskonfiguration erstellt oder aktualisiert. |
> | Aktion | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Vorgang zum Aktualisieren von EventHub. Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. Autorisierungsregeln. Verwenden Sie einen PUT-Aufruf, um die Autorisierungsregel zu aktualisieren. |
> | Aktion | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Vorgang zum Löschen von EventHub-Autorisierungsregeln. |
> | Aktion | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Dient zum Abrufen der EventHub-Verbindungszeichenfolge. |
> | Aktion | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Dient zum Abrufen der Liste mit EventHub-Autorisierungsregeln. |
> | Aktion | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Dient zum erneuten Generieren des primären oder sekundären Schlüssels für die Ressource. |
> | Aktion | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Dient zum Erstellen von EventHub-Autorisierungsregeln und zum Aktualisieren der dazugehörigen Eigenschaften. Die Zugriffsrechte für Autorisierungsregeln können aktualisiert werden. |
> | Aktion | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Vorgang zum Löschen von ConsumerGroup-Ressourcen. |
> | Aktion | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Dient zum Abrufen einer Liste mit ConsumerGroup-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Dient zum Erstellen oder Aktualisieren von ConsumerGroup-Eigenschaften. |
> | Aktion | Microsoft.EventHub/namespaces/eventhubs/Delete | Vorgang zum Löschen von EventHub-Ressourcen. |
> | Aktion | Microsoft.EventHub/namespaces/eventhubs/read | Dient zum Abrufen einer Liste mit EventHub-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.EventHub/namespaces/eventhubs/write | Dient zum Erstellen oder Aktualisieren von EventHub-Eigenschaften. |
> | Aktion | Microsoft.EventHub/namespaces/ipFilterRules/delete | Löscht eine IP-Filterressource. |
> | Aktion | Microsoft.EventHub/namespaces/ipFilterRules/read | Ruft eine IP-Filterressource ab. |
> | Aktion | Microsoft.EventHub/namespaces/ipFilterRules/write | Erstellt eine IP-Filterressource. |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Empfangen von Nachrichten |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Senden von Nachrichten |
> | Aktion | Microsoft.EventHub/namespaces/messagingPlan/read | Hiermit wird der Messagingplan für einen Namespace abgerufen.<br>Diese API ist veraltet.<br>Über die MessagingPlan-Ressource verfügbar gemachte Eigenschaften werden in höheren API-Versionen in die (übergeordnete) Namespaceressource verschoben.<br>Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. |
> | Aktion | Microsoft.EventHub/namespaces/messagingPlan/write | Hiermit wird der Messagingplan für einen Namespace aktualisiert.<br>Diese API ist veraltet.<br>Über die MessagingPlan-Ressource verfügbar gemachte Eigenschaften werden in höheren API-Versionen in die (übergeordnete) Namespaceressource verschoben.<br>Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. |
> | Aktion | Microsoft.EventHub/namespaces/networkrulesets/delete | Löscht eine VNET-Regelressource. |
> | Aktion | Microsoft.EventHub/namespaces/networkrulesets/read | Ruft die NetworkRuleSet-Ressource ab |
> | Aktion | Microsoft.EventHub/namespaces/networkrulesets/write | Erstellt eine VNET-Regelressource. |
> | Aktion | Microsoft.EventHub/namespaces/operationresults/read | Hiermit wird der Status des Namespacevorgangs abgerufen. |
> | Aktion | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Dient zum Abrufen der Liste mit Ressourcenbeschreibungen für Namespacediagnoseeinstellungen. |
> | Aktion | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Dient zum Abrufen der Liste mit Ressourcenbeschreibungen für Namespacediagnoseeinstellungen. |
> | Aktion | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Dient zum Abrufen der Liste mit Ressourcenbeschreibungen für Namespaceprotokolle. |
> | Aktion | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Abrufen der Liste mit Ressourcenbeschreibungen für Namespacemetriken. |
> | Aktion | Microsoft.EventHub/namespaces/read | Dient zum Abrufen der Liste mit Beschreibungen für Namespaceressourcen. |
> | Aktion | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Entfernt den ACS-Namespace. |
> | Aktion | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Löscht eine VNET-Regelressource. |
> | Aktion | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Ruft eine VNET-Regelressource ab. |
> | Aktion | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Erstellt eine VNET-Regelressource. |
> | Aktion | Microsoft.EventHub/namespaces/write | Dient zum Erstellen einer Namespaceressource und zum Aktualisieren ihrer Eigenschaften. Die Eigenschaften für Markierungen und Kapazität des Namespace können aktualisiert werden. |
> | Aktion | Microsoft.EventHub/operations/read | Dient zum Abrufen von Vorgängen. |
> | Aktion | Microsoft.EventHub/register/action | Registriert das Abonnement für den EventHub-Ressourcenanbieter und ermöglicht die Erstellung von EventHub-Ressourcen. |
> | Aktion | Microsoft.EventHub/sku/read | Dient zum Abrufen einer Liste mit SKU-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.EventHub/sku/regions/read | Dient zum Abrufen einer Liste mit SkuRegions-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.EventHub/unregister/action | Registriert den EventHub-Ressourcenanbieter. |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Features/features/read | Ruft die Features eines Abonnements ab. |
> | Aktion | Microsoft.Features/operations/read | Ruft die Liste der Vorgänge ab. |
> | Aktion | Microsoft.Features/providers/features/read | Ruft das Feature eines Abonnements in einem angegebenen Ressourcenanbieter ab. |
> | Aktion | Microsoft.Features/providers/features/register/action | Registriert das Feature für ein Abonnement in einem angegebenen Ressourcenanbieter. |
> | Aktion | Microsoft.Features/providers/features/unregister/action | Hiermit wird die Registrierung des Features für ein Abonnement in einem vorgegebenen Ressourcenanbieter aufgehoben. |
> | Aktion | Microsoft.Features/register/action | Registriert das Feature eines Abonnements. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Abrufen der Zuweisung der Gastkonfiguration |
> | Aktion | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Rufen den Gastkonfiguration-Zuweisungsbericht ab. |
> | Aktion | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Erstellen der Zuweisung einer neuen Gastkonfiguration |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.HDInsight/clusters/applications/delete | Dient zum Löschen der Anwendung für HDInsight-Cluster. |
> | Aktion | Microsoft.HDInsight/clusters/applications/read | Dient zum Abrufen der Anwendung für HDInsight-Cluster. |
> | Aktion | Microsoft.HDInsight/clusters/applications/write | Dient zum Erstellen oder Aktualisieren der Anwendung für HDInsight-Cluster. |
> | Aktion | Microsoft.HDInsight/clusters/changerdpsetting/action | Dient zum Ändern der RDP-Einstellung für HDInsight-Cluster. |
> | Aktion | Microsoft.HDInsight/clusters/configurations/action | Dient zum Aktualisieren der Konfiguration von HDInsight-Clustern. |
> | Aktion | Microsoft.HDInsight/clusters/configurations/action | Dient zum Abrufen der Konfigurationen von HDInsight-Clustern. |
> | Aktion | Microsoft.HDInsight/clusters/configurations/read | Dient zum Abrufen der Konfigurationen von HDInsight-Clustern. |
> | Aktion | Microsoft.HDInsight/clusters/delete | Dient zum Löschen eines HDInsight-Clusters. |
> | Aktion | Microsoft.HDInsight/clusters/getGatewaySettings/action | Ruft Gatewayeinstellungen für HDInsight-Cluster ab. |
> | Aktion | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für den HDInsight-Cluster der Ressource ab. |
> | Aktion | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für den HDInsight-Cluster der Ressource. |
> | Aktion | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für den HDInsight-Cluster ab. |
> | Aktion | Microsoft.HDInsight/clusters/read | Dient zum Abrufen von Details zu HDInsight-Clustern. |
> | Aktion | Microsoft.HDInsight/clusters/roles/resize/action | Dient zum Anpassen der Größe eines HDInsight-Clusters. |
> | Aktion | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aktualisiert Gatewayeinstellungen für HDInsight-Cluster. |
> | Aktion | Microsoft.HDInsight/clusters/write | Dient zum Erstellen oder Aktualisieren von HDInsight-Clustern. |
> | Aktion | Microsoft.HDInsight/locations/capabilities/read | Dient zum Abrufen von Abonnementfunktionen. |
> | Aktion | Microsoft.HDInsight/locations/checkNameAvailability/read | Dient zum Prüfen der Verfügbarkeit des Namens. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ImportExport/jobs/delete | Löscht einen vorhandenen Auftrag. |
> | Aktion | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Ruft die BitLocker-Schlüssel für den angegebenen Auftrag ab. |
> | Aktion | Microsoft.ImportExport/jobs/read | Ruft die Eigenschaften für den angegebenen Auftrag ab oder gibt die Auftragsliste zurück. |
> | Aktion | Microsoft.ImportExport/jobs/write | Erstellt einen Auftrag mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Tags für den angegebenen Auftrag. |
> | Aktion | Microsoft.ImportExport/locations/read | Ruft die Eigenschaften für den angegebenen Standort ab oder gibt die Standortliste zurück. |
> | Aktion | Microsoft.ImportExport/register/action | Registriert das Abonnement für den Import/Export-Ressourcenanbieter und ermöglicht die Erstellung von Import/Exportaufträgen. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Insights/ActionGroups/Delete | Löscht eine Aktionsgruppe. |
> | Aktion | Microsoft.Insights/ActionGroups/Read | Liest eine Aktionsgruppe. |
> | Aktion | Microsoft.Insights/ActionGroups/Write | Erstellt oder aktualisiert eine Aktionsgruppe. |
> | Aktion | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktivitätsprotokollwarnung aktiviert |
> | Aktion | Microsoft.Insights/ActivityLogAlerts/Delete | Löscht eine Aktivitätsprotokollwarnung. |
> | Aktion | Microsoft.Insights/ActivityLogAlerts/Read | Liest eine Aktivitätsprotokollwarnung. |
> | Aktion | Microsoft.Insights/ActivityLogAlerts/Write | Erstellt oder aktualisiert eine Aktivitätsprotokollwarnung. |
> | Aktion | Microsoft.Insights/AlertRules/Activated/Action | Klassische Metrikwarnung aktiviert |
> | Aktion | Microsoft.Insights/AlertRules/Delete | Löscht eine klassische Metrikwarnung. |
> | Aktion | Microsoft.Insights/AlertRules/Incidents/Read | Liest eine klassische Metrikwarnung zu einem Vorfall. |
> | Aktion | Microsoft.Insights/AlertRules/Read | Liest eine klassische Metrikwarnung. |
> | Aktion | Microsoft.Insights/AlertRules/Resolved/Action | Klassische Metrikwarnung gelöst |
> | Aktion | Microsoft.Insights/AlertRules/Throttled/Action | Klassische Metrikwarnungsregel gedrosselt |
> | Aktion | Microsoft.Insights/AlertRules/Write | Erstellt oder aktualisiert eine klassische Metrikwarnung. |
> | Aktion | Microsoft.Insights/AutoscaleSettings/Delete | Löscht eine Einstellung zur automatischen Skalierung. |
> | Aktion | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Liest eine Diagnoseeinstellung für eine Ressource. |
> | Aktion | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Erstellt oder aktualisiert eine Diagnoseeinstellung für eine Ressource. |
> | Aktion | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Dient zum Lesen von Protokolldefinitionen. |
> | Aktion | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Dient zum Lesen von Metrikdefinitionen. |
> | Aktion | Microsoft.Insights/AutoscaleSettings/Read | Liest eine Einstellung zur automatischen Skalierung. |
> | Aktion | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Automatisches zentrales Herunterskalieren ausgelöst |
> | Aktion | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Automatisches zentrales Herunterskalieren abgeschlossen |
> | Aktion | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Automatisches zentrales Hochskalieren ausgelöst |
> | Aktion | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Automatisches zentrales Hochskalieren abgeschlossen |
> | Aktion | Microsoft.Insights/AutoscaleSettings/Write | Erstellt oder aktualisiert eine Einstellung zur automatischen Skalierung. |
> | Aktion | Microsoft.Insights/Baseline/Read | Metrikbaseline lesen (Vorschau) |
> | Aktion | Microsoft.Insights/CalculateBaseline/Read | Baseline für Metrikwerte berechnen (Vorschau) |
> | Aktion | Microsoft.Insights/Components/AnalyticsItems/Delete | Hiermit wird ein Application Insights-Analyseelement gelöscht. |
> | Aktion | Microsoft.Insights/Components/AnalyticsItems/Read | Hiermit wird ein Application Insights-Analyseelement gelesen. |
> | Aktion | Microsoft.Insights/Components/AnalyticsItems/Write | Hiermit wird ein Application Insights-Analyseelement geschrieben. |
> | Aktion | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights-Analysetabellenaktion |
> | Aktion | Microsoft.Insights/Components/AnalyticsTables/Delete | Hiermit wird ein Application Insights-Analysetabellenschema gelöscht. |
> | Aktion | Microsoft.Insights/Components/AnalyticsTables/Read | Hiermit wird ein Application Insights-Analysetabellenschema gelesen. |
> | Aktion | Microsoft.Insights/Components/AnalyticsTables/Write | Hiermit wird ein Application Insights-Analysetabellenschema geschrieben. |
> | Aktion | Microsoft.Insights/Components/Annotations/Delete | Hiermit wird eine Application Insights-Anmerkung gelöscht. |
> | Aktion | Microsoft.Insights/Components/Annotations/Read | Hiermit wird eine Application Insights-Anmerkung gelesen. |
> | Aktion | Microsoft.Insights/Components/Annotations/Write | Hiermit wird eine Application Insights-Anmerkung geschrieben. |
> | Aktion | Microsoft.Insights/Components/Api/Read | Hiermit wird die Daten-API für Application Insights-Komponenten gelesen. |
> | Aktion | Microsoft.Insights/Components/ApiKeys/Action | Hiermit wird ein Application Insights-API-Schlüssel generiert. |
> | Aktion | Microsoft.Insights/Components/ApiKeys/Delete | Hiermit wird ein Application Insights-API-Schlüssel gelöscht. |
> | Aktion | Microsoft.Insights/Components/ApiKeys/Read | Hiermit wird ein Application Insights-API-Schlüssel gelesen. |
> | Aktion | Microsoft.Insights/Components/BillingPlanForComponent/Read | Hiermit wird ein Abrechnungsplan für eine Application Insights-Komponente gelesen. |
> | Aktion | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Hiermit werden die aktuellen Abrechnungsfeatures für eine Application Insights-Komponente gelesen. |
> | Aktion | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Hiermit werden die aktuellen Abrechnungsfeatures für eine Application Insights-Komponente geschrieben. |
> | Aktion | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Hiermit wird die standardmäßige ALM-Integrationskonfiguration von Application Insights gelesen. |
> | Aktion | Microsoft.Insights/Components/Delete | Hiermit wird die Konfiguration der Application Insights-Komponente gelöscht. |
> | Aktion | Microsoft.Insights/Components/Events/Read | Hiermit rufen Sie mithilfe des OData-Abfrageformats Protokolle aus Application Insights ab. |
> | Aktion | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights-Exporteinstellungsaktion |
> | Aktion | Microsoft.Insights/Components/ExportConfiguration/Delete | Hiermit werden die Application Insights-Exporteinstellungen gelöscht. |
> | Aktion | Microsoft.Insights/Components/ExportConfiguration/Read | Hiermit werden die Application Insights-Exporteinstellungen gelesen. |
> | Aktion | Microsoft.Insights/Components/ExportConfiguration/Write | Hiermit werden die Application Insights-Exporteinstellungen geschrieben. |
> | Aktion | Microsoft.Insights/Components/ExtendQueries/Read | Hiermit werden die erweiterten Abfrageergebnisse der Application Insights-Komponenten gelesen. |
> | Aktion | Microsoft.Insights/Components/Favorites/Delete | Hiermit wird ein Application Insights-Favorit gelöscht. |
> | Aktion | Microsoft.Insights/Components/Favorites/Read | Hiermit wird ein Application Insights-Favorit gelesen. |
> | Aktion | Microsoft.Insights/Components/Favorites/Write | Hiermit wird ein Application Insights-Favorit geschrieben. |
> | Aktion | Microsoft.Insights/Components/FeatureCapabilities/Read | Hiermit werden die Features der Application Insights-Komponente gelesen. |
> | Aktion | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Hiermit werden die verfügbaren Abrechnungsfeatures für eine Application Insights-Komponente gelesen. |
> | Aktion | Microsoft.Insights/Components/GetToken/Read | Hiermit wird ein Token einer Application Insights-Komponente gelesen. |
> | Aktion | Microsoft.Insights/Components/MetricDefinitions/Read | Hiermit werden die Metrikdefinitionen für Application Insights-Komponenten gelesen. |
> | Aktion | Microsoft.Insights/Components/Metrics/Read | Hiermit werden die Metriken für Application Insights-Komponenten gelesen. |
> | Aktion | Microsoft.Insights/Components/Move/Action | Hiermit wird eine Application Insights-Komponente auf eine andere Ressourcengruppe oder ein anderes Abonnement migriert. |
> | Aktion | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Hiermit wird ein persönliches Application Insights-Analyseelement gelöscht. |
> | Aktion | Microsoft.Insights/Components/MyAnalyticsItems/Read | Hiermit wird ein persönliches Application Insights-Analyseelement gelesen. |
> | Aktion | Microsoft.Insights/Components/MyAnalyticsItems/Write | Hiermit wird ein persönliches Application Insights-Analyseelement geschrieben. |
> | Aktion | Microsoft.Insights/Components/MyFavorites/Read | Hiermit wird ein persönlicher Application Insights-Favorit gelesen. |
> | Aktion | Microsoft.Insights/Components/Operations/Read | Hiermit rufen Sie den Status von Vorgängen mit langer Ausführungszeit in Application Insights ab. |
> | Aktion | Microsoft.Insights/Components/PricingPlans/Read | Hiermit wird ein Tarif für eine Application Insights-Komponente gelesen. |
> | Aktion | Microsoft.Insights/Components/PricingPlans/Write | Hiermit wird ein Tarif für eine Application Insights-Komponente geschrieben. |
> | Aktion | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Hiermit wird die Konfiguration von Proactive Detection in Application Insights gelesen. |
> | Aktion | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Hiermit wird die Konfiguration von Proactive Detection in Application Insights geschrieben. |
> | Aktion | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Dient zum Lesen von Metrikdefinitionen. |
> | Aktion | Microsoft.Insights/Components/Purge/Action | Daten werden aus Application Insights gelöscht |
> | Aktion | Microsoft.Insights/Components/Query/Read | Hiermit führen Sie Abfragen für Application Insights-Protokolle aus. |
> | Aktion | Microsoft.Insights/Components/QuotaStatus/Read | Hiermit wird der Kontingentstatus der Application Insights-Komponente gelesen. |
> | Aktion | Microsoft.Insights/Components/Read | Hiermit wird die Konfiguration der Application Insights-Komponente gelesen. |
> | Aktion | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Hiermit werden die Webteststandorte von Application Insights gelesen. |
> | Aktion | Microsoft.Insights/Components/Webtests/Read | Hiermit wird eine Webtestkonfiguration gelesen. |
> | Aktion | Microsoft.Insights/Components/WorkItemConfigs/Delete | Hiermit wird die standardmäßige ALM-Integrationskonfiguration von Application Insights gelöscht. |
> | Aktion | Microsoft.Insights/Components/WorkItemConfigs/Read | Hiermit wird die ALM-Integrationskonfiguration von Application Insights gelesen. |
> | Aktion | Microsoft.Insights/Components/WorkItemConfigs/Write | Hiermit wird die ALM-Integrationskonfiguration von Application Insights geschrieben. |
> | Aktion | Microsoft.Insights/Components/Write | Hiermit wird in eine Application Insights-Komponentenkonfiguration geschrieben. |
> | Aktion | Microsoft.Insights/DiagnosticSettings/Delete | Löscht eine Diagnoseeinstellung für eine Ressource. |
> | Aktion | Microsoft.Insights/DiagnosticSettings/Read | Liest eine Diagnoseeinstellung für eine Ressource. |
> | Aktion | Microsoft.Insights/DiagnosticSettings/Write | Erstellt oder aktualisiert eine Diagnoseeinstellung für eine Ressource. |
> | Aktion | Microsoft.Insights/EventCategories/Read | Liest im Aktivitätsprotokoll verfügbare Ereigniskategorien. |
> | Aktion | Microsoft.Insights/eventtypes/digestevents/Read | Dient zum Lesen des Verwaltungsereignistyp-Digests. |
> | Aktion | Microsoft.Insights/eventtypes/values/Read | Liest Aktivitätsprotokollereignisse. |
> | Aktion | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Löscht eine Einstellung für die Netzwerkflussdiagnose. |
> | Aktion | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Liest eine Einstellung für die Netzwerkflussdiagnose. |
> | Aktion | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Erstellt oder aktualisiert eine Einstellung für die Netzwerkflussdiagnose. |
> | Aktion | Microsoft.Insights/ListMigrationDate/Action | Hiermit wird das Migrationsdatum für das Abonnement zurückgegeben. |
> | Aktion | Microsoft.Insights/ListMigrationDate/Read | Hiermit wird das Migrationsdatum für das Abonnement zurückgegeben. |
> | Aktion | Microsoft.Insights/LogDefinitions/Read | Dient zum Lesen von Protokolldefinitionen. |
> | Aktion | Microsoft.Insights/LogProfiles/Delete | Löscht ein Aktivitätsprotokollprofil. |
> | Aktion | Microsoft.Insights/LogProfiles/Read | Liest ein Aktivitätsprotokollprofil. |
> | Aktion | Microsoft.Insights/LogProfiles/Write | Erstellt oder aktualisiert ein Aktivitätsprotokollprofil. |
> | Aktion | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der ADAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ADReplicationResult/Read | Hiermit lesen Sie Daten aus der ADReplicationResult-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der ADSecurityAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/Alert/Read | Hiermit lesen Sie Daten aus der Alert-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/AlertHistory/Read | Hiermit lesen Sie Daten aus der AlertHistory-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ApplicationInsights/Read | Hiermit lesen Sie Daten aus der ApplicationInsights-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/AzureActivity/Read | Hiermit lesen Sie Daten aus der AzureActivity-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/AzureMetrics/Read | Hiermit lesen Sie Daten aus der AzureMetrics-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/BoundPort/Read | Hiermit lesen Sie Daten aus der BoundPort-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/CommonSecurityLog/Read | Hiermit lesen Sie Daten aus der CommonSecurityLog-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ComputerGroup/Read | Hiermit lesen Sie Daten aus der ComputerGroup-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ConfigurationChange/Read | Hiermit lesen Sie Daten aus der ConfigurationChange-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ConfigurationData/Read | Hiermit lesen Sie Daten aus der ConfigurationData-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ContainerImageInventory/Read | Hiermit lesen Sie Daten aus der ContainerImageInventory-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ContainerInventory/Read | Hiermit lesen Sie Daten aus der ContainerInventory-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ContainerLog/Read | Hiermit lesen Sie Daten aus der ContainerLog-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ContainerServiceLog/Read | Hiermit lesen Sie Daten aus der ContainerServiceLog-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceAppCrash/Read | Hiermit lesen Sie Daten aus der DeviceAppCrash-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Hiermit lesen Sie Daten aus der DeviceAppLaunch-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceCalendar/Read | Hiermit lesen Sie Daten aus der DeviceCalendar-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceCleanup/Read | Hiermit lesen Sie Daten aus der DeviceCleanup-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceConnectSession/Read | Hiermit lesen Sie Daten aus der DeviceConnectSession-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceEtw/Read | Hiermit lesen Sie Daten aus der DeviceEtw-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Hiermit lesen Sie Daten aus der DeviceHardwareHealth-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceHealth/Read | Hiermit lesen Sie Daten aus der DeviceHealth-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Hiermit lesen Sie Daten aus der DeviceHeartbeat-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Hiermit lesen Sie Daten aus der DeviceSkypeHeartbeat-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Hiermit lesen Sie Daten aus der DeviceSkypeSignIn-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DeviceSleepState/Read | Hiermit lesen Sie Daten aus der DeviceSleepState-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DHAppFailure/Read | Hiermit lesen Sie Daten aus der DHAppFailure-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DHAppReliability/Read | Hiermit lesen Sie Daten aus der DHAppReliability-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DHDriverReliability/Read | Hiermit lesen Sie Daten aus der DHDriverReliability-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DHLogonFailures/Read | Hiermit lesen Sie Daten aus der DHLogonFailures-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DHLogonMetrics/Read | Hiermit lesen Sie Daten aus der DHLogonMetrics-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DHOSCrashData/Read | Hiermit lesen Sie Daten aus der DHOSCrashData-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DHOSReliability/Read | Hiermit lesen Sie Daten aus der DHOSReliability-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DHWipAppLearning/Read | Hiermit lesen Sie Daten aus der DHWipAppLearning-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DnsEvents/Read | Hiermit lesen Sie Daten aus der DnsEvents-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/DnsInventory/Read | Hiermit lesen Sie Daten aus der DnsInventory-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ETWEvent/Read | Hiermit lesen Sie Daten aus der ETWEvent-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/Event/Read | Hiermit lesen Sie Daten aus der Event-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der ExchangeAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der ExchangeOnlineAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/Heartbeat/Read | Hiermit lesen Sie Daten aus der Heartbeat-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der IISAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/InboundConnection/Read | Hiermit lesen Sie Daten aus der InboundConnection-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/KubeNodeInventory/Read | Hiermit lesen Sie Daten aus der KubeNodeInventory-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/KubePodInventory/Read | Hiermit lesen Sie Daten aus der KubePodInventory-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/LinuxAuditLog/Read | Hiermit lesen Sie Daten aus der LinuxAuditLog-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAApplication/Read | Hiermit lesen Sie Daten aus der MAApplication-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAApplicationHealth/Read | Hiermit lesen Sie Daten aus der MAApplicationHealth-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Liest Daten aus der Tabelle „MAApplicationHealthAlternativeVersions“. |
> | Aktion | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Liest Daten aus der Tabelle „MAApplicationHealthIssues“. |
> | Aktion | Microsoft.Insights/Logs/MAApplicationInstance/Read | Hiermit lesen Sie Daten aus der MAApplicationInstance-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Liest Daten aus der Tabelle „MAApplicationInstanceReadiness“. |
> | Aktion | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Hiermit lesen Sie Daten aus der MAApplicationReadiness-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MADeploymentPlan/Read | Hiermit lesen Sie Daten aus der MADeploymentPlan-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MADevice/Read | Hiermit lesen Sie Daten aus der MADevice-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Liest Daten aus der Tabelle „MADevicePnPHealth“. |
> | Aktion | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Liest Daten aus der Tabelle „MADevicePnPHealthAlternativeVersions“. |
> | Aktion | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Liest Daten aus der Tabelle „MADevicePnPHealthIssues“. |
> | Aktion | Microsoft.Insights/Logs/MADeviceReadiness/Read | Hiermit lesen Sie Daten aus der MADeviceReadiness-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Liest Daten aus der Tabelle „MADriverInstanceReadiness“. |
> | Aktion | Microsoft.Insights/Logs/MADriverReadiness/Read | Hiermit lesen Sie Daten aus der MADriverReadiness-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeAddin/Read | Hiermit lesen Sie Daten aus der MAOfficeAddin-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Hiermit lesen Sie Daten aus der MAOfficeAddinHealth-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Liest Daten aus der Tabelle „MAOfficeAddinHealthIssues“. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Hiermit lesen Sie Daten aus der MAOfficeAddinInstance-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Liest Daten aus der Tabelle „MAOfficeAddinInstanceReadiness“. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Hiermit lesen Sie Daten aus der MAOfficeAddinReadiness-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeApp/Read | Hiermit lesen Sie Daten aus der MAOfficeApp-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Hiermit lesen Sie Daten aus der MAOfficeAppHealth-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Hiermit lesen Sie Daten aus der MAOfficeAppInstance-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Hiermit lesen Sie Daten aus der MAOfficeAppReadiness-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Hiermit lesen Sie Daten aus der MAOfficeBuildInfo-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Hiermit lesen Sie Daten aus der MAOfficeCurrencyAssessment-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Hiermit lesen Sie Daten aus der MAOfficeCurrencyAssessmentDailyCounts-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Hiermit lesen Sie Daten aus der MAOfficeDeploymentStatus-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Liest Daten aus der Tabelle „MAOfficeMacroHealth“. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Liest Daten aus der Tabelle „MAOfficeMacroHealthIssues“. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Liest Daten aus der Tabelle „MAOfficeMacroIssueInstanceReadiness“. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Hiermit lesen Sie Daten aus der MAOfficeMacroIssueReadiness-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Hiermit lesen Sie Daten aus der MAOfficeMacroSummary-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeSuite/Read | Hiermit lesen Sie Daten aus der MAOfficeSuite-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Hiermit lesen Sie Daten aus der MAOfficeSuiteInstance-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Hiermit lesen Sie Daten aus der MAProposedPilotDevices-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Hiermit lesen Sie Daten aus der MAWindowsBuildInfo-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Hiermit lesen Sie Daten aus der MAWindowsCurrencyAssessment-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Hiermit lesen Sie Daten aus der MAWindowsCurrencyAssessmentDailyCounts-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Hiermit lesen Sie Daten aus der MAWindowsDeploymentStatus-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Liest Daten aus der Tabelle „MAWindowsSysReqInstanceReadiness“. |
> | Aktion | Microsoft.Insights/Logs/NetworkMonitoring/Read | Hiermit lesen Sie Daten aus der NetworkMonitoring-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/OfficeActivity/Read | Hiermit lesen Sie Daten aus der OfficeActivity-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/Operation/Read | Hiermit lesen Sie Daten aus der Operation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/OutboundConnection/Read | Hiermit lesen Sie Daten aus der OutboundConnection-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/Perf/Read | Hiermit lesen Sie Daten aus der Perf-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ProtectionStatus/Read | Hiermit lesen Sie Daten aus der ProtectionStatus-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/Read | Hiermit lesen Sie Daten aus allen Ihren Protokollen. |
> | Aktion | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Hiermit lesen Sie Daten aus der ReservedAzureCommonFields-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ReservedCommonFields/Read | Hiermit lesen Sie Daten aus der ReservedCommonFields-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der SCCMAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der SCOMAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SecurityAlert/Read | Hiermit lesen Sie Daten aus der SecurityAlert-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SecurityBaseline/Read | Hiermit lesen Sie Daten aus der SecurityBaseline-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Hiermit lesen Sie Daten aus der SecurityBaselineSummary-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SecurityDetection/Read | Hiermit lesen Sie Daten aus der SecurityDetection-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SecurityEvent/Read | Hiermit lesen Sie Daten aus der SecurityEvent-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Hiermit lesen Sie Daten aus der ServiceFabricOperationalEvent-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Hiermit lesen Sie Daten aus der ServiceFabricReliableActorEvent-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Hiermit lesen Sie Daten aus der ServiceFabricReliableServiceEvent-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der SfBAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der SfBOnlineAssessmentRecommendatio-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der SharePointOnlineAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der SPAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der SQLAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Hiermit lesen Sie Daten aus der SQLQueryPerformance-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/Syslog/Read | Hiermit lesen Sie Daten aus der Syslog-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/SysmonEvent/Read | Hiermit lesen Sie Daten aus der SysmonEvent-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UAApp/Read | Hiermit lesen Sie Daten aus der UAApp-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UAComputer/Read | Hiermit lesen Sie Daten aus der UAComputer-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UAComputerRank/Read | Hiermit lesen Sie Daten aus der UAComputerRank-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UADriver/Read | Hiermit lesen Sie Daten aus der UADriver-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Hiermit lesen Sie Daten aus der UADriverProblemCodes-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UAFeedback/Read | Hiermit lesen Sie Daten aus der UAFeedback-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Hiermit lesen Sie Daten aus der UAHardwareSecurity-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Hiermit lesen Sie Daten aus der UAIESiteDiscovery-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Hiermit lesen Sie Daten aus der UAOfficeAddIn-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Hiermit lesen Sie Daten aus der UAProposedActionPlan-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UASysReqIssue/Read | Hiermit lesen Sie Daten aus der UASysReqIssue-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Hiermit lesen Sie Daten aus der UAUpgradedComputer-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/Update/Read | Hiermit lesen Sie Daten aus der Update-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UpdateRunProgress/Read | Hiermit lesen Sie Daten aus der UpdateRunProgress-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/UpdateSummary/Read | Hiermit lesen Sie Daten aus der UpdateSummary-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/Usage/Read | Hiermit lesen Sie Daten aus der Usage-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/W3CIISLog/Read | Hiermit lesen Sie Daten aus der W3CIISLog-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Hiermit lesen Sie Daten aus der WaaSDeploymentStatus-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Hiermit lesen Sie Daten aus der WaaSInsiderStatus-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Hiermit lesen Sie Daten aus der WaaSUpdateStatus-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WDAVStatus/Read | Hiermit lesen Sie Daten aus der WDAVStatus-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WDAVThreat/Read | Hiermit lesen Sie Daten aus der WDAVThreat-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der WindowsClientAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WindowsFirewall/Read | Hiermit lesen Sie Daten aus der WindowsFirewall-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Hiermit lesen Sie Daten aus der WindowsServerAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WireData/Read | Hiermit lesen Sie Daten aus der WireData-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Hiermit lesen Sie Daten aus der WUDOAggregatedStatus-Tabelle. |
> | Aktion | Microsoft.Insights/Logs/WUDOStatus/Read | Hiermit lesen Sie Daten aus der WUDOStatus-Tabelle. |
> | Aktion | Microsoft.Insights/MetricAlerts/Delete | Löscht ein Metrikwarnung. |
> | Aktion | Microsoft.Insights/MetricAlerts/Read | Liest eine Metrikwarnung. |
> | Aktion | Microsoft.Insights/MetricAlerts/Status/Read | Liest den Metrikwarnungsstatus. |
> | Aktion | Microsoft.Insights/MetricAlerts/Write | Erstellt oder aktualisiert eine Metrikwarnung. |
> | Aktion | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Dient zum Lesen von Metrikdefinitionen. |
> | Aktion | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Dient zum Lesen von Metrikdefinitionen. |
> | Aktion | Microsoft.Insights/MetricDefinitions/Read | Dient zum Lesen von Metrikdefinitionen. |
> | Aktion | Microsoft.Insights/Metricnamespaces/Read | Liest die Metriknamespaces |
> | Aktion | Microsoft.Insights/Metrics/Action | Metrikaktion |
> | Aktion | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Dient zum Lesen von Metriken. |
> | Aktion | Microsoft.Insights/Metrics/providers/Metrics/Read | Dient zum Lesen von Metriken. |
> | Aktion | Microsoft.Insights/Metrics/Read | Dient zum Lesen von Metriken. |
> | DataAction | Microsoft.Insights/Metrics/Write | Hiermit werden Metriken geschrieben. |
> | Aktion | Microsoft.Insights/MigrateToNewpricingModel/Action | Hiermit wird ein Abonnement auf das neue Preismodell migriert. |
> | Aktion | Microsoft.Insights/Operations/Read | Dient zum Lesen von Vorgängen. |
> | Aktion | Microsoft.Insights/Register/Action | Dient zum Registrieren des Microsoft Insights-Anbieters. |
> | Aktion | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Hiermit wird ein Rollback für das Abonnement auf das Legacypreismodell durchgeführt. |
> | Aktion | Microsoft.Insights/ScheduledQueryRules/Delete | Löscht eine geplante Abfrageregel. |
> | Aktion | Microsoft.Insights/ScheduledQueryRules/Read | Liest eine geplante Abfrageregel. |
> | Aktion | Microsoft.Insights/ScheduledQueryRules/Write | Schreibt eine geplante Abfrageregel. |
> | Aktion | Microsoft.Insights/Tenants/Register/Action | Hiermit wird der Microsoft Insights-Anbieter initialisiert. |
> | Aktion | Microsoft.Insights/Unregister/Action | Dient zum Registrieren des Microsoft Insights-Anbieters. |
> | Aktion | Microsoft.Insights/Webtests/Delete | Hiermit wird eine Webtestkonfiguration gelöscht. |
> | Aktion | Microsoft.Insights/Webtests/GetToken/Read | Hiermit wird ein Webtesttoken gelesen. |
> | Aktion | Microsoft.Insights/Webtests/MetricDefinitions/Read | Hiermit werden Webtestmetrikdefinitionen gelesen. |
> | Aktion | Microsoft.Insights/Webtests/Metrics/Read | Hiermit werden Webtestmetriken gelesen. |
> | Aktion | Microsoft.Insights/Webtests/Read | Hiermit wird eine Webtestkonfiguration gelesen. |
> | Aktion | Microsoft.Insights/Webtests/Write | Hiermit wird in eine Webtestkonfiguration geschrieben. |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Intune/diagnosticsettings/delete | Löscht eine Diagnoseeinstellung. |
> | Aktion | Microsoft.Intune/diagnosticsettings/read | Liest eine Diagnoseeinstellung. |
> | Aktion | Microsoft.Intune/diagnosticsettings/write | Schreibt eine Diagnoseeinstellung. |
> | Aktion | Microsoft.Intune/diagnosticsettingscategories/read | Liest die Kategorien einer Diagnoseeinstellung. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.IoTCentral/appTemplates/action | Ruft alle verfügbaren Anwendungsvorlagen in Azure IoT Central ab |
> | Aktion | Microsoft.IoTCentral/checkNameAvailability/action | Überprüft, ob ein IoT Central-Anwendungsname verfügbar ist. |
> | Aktion | Microsoft.IoTCentral/checkSubdomainAvailability/action | Überprüft, ob eine IoT Central-Anwendungsunterdomäne verfügbar ist. |
> | Aktion | Microsoft.IoTCentral/IoTApps/delete | Löscht eine IoT Central-Anwendung. |
> | Aktion | Microsoft.IoTCentral/IoTApps/read | Ruft eine einzelne IoT Central-Anwendung ab. |
> | Aktion | Microsoft.IoTCentral/IoTApps/write | Erstellt oder aktualisiert eine IoT Central-Anwendung. |
> | Aktion | Microsoft.IoTCentral/operations/read | Ruft alle verfügbaren Vorgänge für IoT Central-Anwendungen ab. |
> | Aktion | Microsoft.IoTCentral/register/action | Registriert das Abonnement für den Azure IoT Central-Ressourcenanbieter. |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.IoTSpaces/Graph/delete | Löscht die Microsoft.IoTSpaces Graph-Ressource. |
> | Aktion | Microsoft.IoTSpaces/Graph/read | Ruft die Microsoft.IoTSpaces Graph-Ressource(n) ab. |
> | Aktion | Microsoft.IoTSpaces/Graph/write | Erstellt die Microsoft.IoTSpaces Graph-Ressource. |
> | Aktion | Microsoft.IoTSpaces/register/action | Dient zum Registrieren des Abonnements für den Microsoft.IoTSpaces Graph-Ressourcenanbieter, um die Erstellung von Ressourcen zu ermöglichen. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.KeyVault/checkNameAvailability/read | Prüft, ob ein Schlüsseltresorname gültig ist und noch nicht verwendet wird. |
> | Aktion | Microsoft.KeyVault/deletedVaults/read | Dient zum Anzeigen der Eigenschaften vorläufig gelöschter Schlüsseltresore. |
> | Aktion | Microsoft.KeyVault/hsmPools/delete | Hiermit wird ein HSM-Pool gelöscht. |
> | Aktion | Microsoft.KeyVault/hsmPools/joinVault/action | Hiermit wird ein Schlüsseltresor zu einem HSM-Pool hinzugefügt. |
> | Aktion | Microsoft.KeyVault/hsmPools/read | Hiermit werden die Eigenschaften eines HSM-Pools angezeigt. |
> | Aktion | Microsoft.KeyVault/hsmPools/write | Hiermit wird durch das Aktualisieren der Eigenschaften eines vorhandenen HSM-Pools ein neuer HSM-Pool erstellt. |
> | Aktion | Microsoft.KeyVault/locations/deletedVaults/purge/action | Dient zum endgültigen Löschen eines vorläufig gelöschten Schlüsseltresors. |
> | Aktion | Microsoft.KeyVault/locations/deletedVaults/read | Dient zum Anzeigen der Eigenschaften eines vorläufig gelöschten Schlüsseltresors. |
> | Aktion | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Hiermit wird Microsoft.KeyVault darüber benachrichtigt, dass ein virtuelles Netzwerk oder Subnetz gelöscht wird. |
> | Aktion | Microsoft.KeyVault/locations/operationResults/read | Dient zum Überprüfen des Ergebnisses eines Vorgangs mit langer Ausführungszeit. |
> | Aktion | Microsoft.KeyVault/operations/read | Listet die verfügbaren Vorgänge für den Microsoft.KeyVault-Ressourcenanbieter auf. |
> | Aktion | Microsoft.KeyVault/register/action | Registriert ein Abonnement. |
> | Aktion | Microsoft.KeyVault/unregister/action | Hiermit wird die Registrierung für ein Abonnement aufgehoben. |
> | Aktion | Microsoft.KeyVault/vaults/accessPolicies/write | Dient zum Aktualisieren einer vorhandenen Zugriffsrichtlinie durch Zusammenführen oder Ersetzen oder zum Hinzufügen einer neuen Zugriffsrichtlinie zu einem Tresor. |
> | Aktion | Microsoft.KeyVault/vaults/delete | Dient zum Löschen eines Schlüsseltresors. |
> | Aktion | Microsoft.KeyVault/vaults/deploy/action | Ermöglicht den Zugriff auf Geheimnisse in einem Schlüsseltresor beim Bereitstellen von Azure-Ressourcen. |
> | Aktion | Microsoft.KeyVault/vaults/eventGridFilters/delete | Hiermit wird „Microsoft.KeyVault“ darüber benachrichtigt, dass ein EventGrid-Abonnement für Key Vault gelöscht wird. |
> | Aktion | Microsoft.KeyVault/vaults/eventGridFilters/read | Hiermit wird „Microsoft.KeyVault“ darüber benachrichtigt, dass ein EventGrid-Abonnement für Key Vault angezeigt wird. |
> | Aktion | Microsoft.KeyVault/vaults/eventGridFilters/write | Hiermit wird „Microsoft.KeyVault“ darüber benachrichtigt, dass ein EventGrid-Abonnement für Key Vault erstellt wird. |
> | Aktion | Microsoft.KeyVault/vaults/read | Dient zum Anzeigen der Eigenschaften eines Schlüsseltresors. |
> | Aktion | Microsoft.KeyVault/vaults/secrets/read | Dient zum Anzeigen der Eigenschaften (aber nicht des Werts) eines Geheimnisses. |
> | Aktion | Microsoft.KeyVault/vaults/secrets/write | Dient zum Erstellen eines neuen Geheimnisses oder zum Aktualisieren des Werts eines vorhandenen Geheimnisses. |
> | Aktion | Microsoft.KeyVault/vaults/write | Dient zum Erstellen eines neuen Schlüsseltresors oder zum Aktualisieren der Eigenschaften einer vorhandenen Schlüsseltresors. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Kusto/Clusters/Activate/action | Startet den Cluster. |
> | Aktion | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Überprüft die Verfügbarkeit des Clusternamens. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Fügt Datenbankprinzipale hinzu. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Prüft die Verfügbarkeit von Namen für einen bestimmten Typ. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Löscht eine Datenverbindungsressource. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Liest eine Datenverbindungsressource. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Schreibt eine Datenverbindungsressource. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Überprüft eine Datenbankdatenverbindung. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/delete | Löscht eine Datenbankressource. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Löscht eine Event Hub-Verbindungsressource. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Liest eine Event Hub-Verbindungsressource. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Schreibt eine Event Hub-Verbindungsressource. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Überprüft eine Datenbank-Event Hub-Verbindung. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Listet Datenbankprinzipale auf. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/read | Liest eine Datenbankressource. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Entfernt Datenbankprinzipale. |
> | Aktion | Microsoft.Kusto/Clusters/Databases/write | Schreibt eine Datenbankressource. |
> | Aktion | Microsoft.Kusto/Clusters/Deactivate/action | Beendet den Cluster. |
> | Aktion | Microsoft.Kusto/Clusters/delete | Löscht eine Clusterressource. |
> | Aktion | Microsoft.Kusto/Clusters/read | Liest eine Clusterressource. |
> | Aktion | Microsoft.Kusto/Clusters/SKUs/read | Liest eine Cluster-SKU-Ressource. |
> | Aktion | Microsoft.Kusto/Clusters/Start/action | Startet den Cluster. |
> | Aktion | Microsoft.Kusto/Clusters/Stop/action | Beendet den Cluster. |
> | Aktion | Microsoft.Kusto/Clusters/write | Schreibt eine Clusterressource. |
> | Aktion | Microsoft.Kusto/Locations/CheckNameAvailability/action | Überprüft die Verfügbarkeit des Ressourcennamens. |
> | Aktion | Microsoft.Kusto/locations/operationresults/read | Liest betriebliche Ressourcen. |
> | Aktion | Microsoft.Kusto/Operations/read | Liest betriebliche Ressourcen. |
> | Aktion | Microsoft.Kusto/Register/action | Registriert das Abonnement beim Kusto-Ressourcenanbieter. |
> | Aktion | Microsoft.Kusto/SKUs/read | Liest eine SKU-Ressource. |
> | Aktion | Microsoft.Kusto/Unregister/action | Hebt die Registrierung des Abonnements beim Kusto-Ressourcenanbieter auf. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.LabServices/labAccounts/CreateLab/action | Hiermit wird ein Lab in einem Lab-Konto erstellt. |
> | Aktion | Microsoft.LabServices/labAccounts/delete | Hiermit werden Lab-Konten gelöscht. |
> | Aktion | Microsoft.LabServices/labAccounts/galleryImages/delete | Löscht Katalogimages. |
> | Aktion | Microsoft.LabServices/labAccounts/galleryImages/read | Dient zum Lesen von Katalogimages. |
> | Aktion | Microsoft.LabServices/labAccounts/galleryImages/write | Fügt Katalogimages hinzu oder ändert diese. |
> | Aktion | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Hiermit werden Informationen zur regionalen Verfügbarkeit jeder Größenkategorie abgerufen, die in einem Labkonto konfiguriert sind. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Benutzer zu einem Lab hinzufügen |
> | Aktion | Microsoft.LabServices/labAccounts/labs/delete | Dient zum Löschen von Labs. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Beansprucht eine zufällige Umgebung für einen Benutzer in den Umgebungseinstellungen. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Hiermit wird eine Umgebungseinstellung gelöscht. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Beansprucht die Umgebung und weist diese dem Benutzer zu. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Dient zum Löschen von Umgebungen. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Dient zum Lesen von Umgebungen. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Setzt das Kennwort des Benutzers für eine Umgebung zurück. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Startet eine Umgebung, indem alle Ressourcen innerhalb der Umgebung gestartet werden. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Beendet eine Umgebung, indem alle Ressourcen innerhalb der Umgebung beendet werden. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Dient zum Hinzufügen oder Ändern von Umgebungen. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Stellt die erforderlichen Ressourcen für eine Umgebungseinstellung anhand des aktuellen Status der Lab- bzw. Umgebungseinstellung bereit oder hebt deren Bereitstellung auf. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Hiermit wird eine Umgebungseinstellung gelesen. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Hiermit wird das aktuelle Vorlagenimage im freigegebenen Katalog im Labkonto gespeichert. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Dient zum Löschen von Zeitplänen. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Dient zum Lesen von Zeitplänen. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Dient zum Hinzufügen oder Ändern von Zeitplänen. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Startet eine Vorlage, indem alle Ressourcen innerhalb der Vorlage gestartet werden. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Beendet eine Vorlage, indem alle in der Vorlage enthaltenen Ressourcen beendet werden. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Dient zum Hinzufügen oder Ändern einer Umgebungseinstellung. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/read | Dient zum Lesen von Labs. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/Register/action | Führt eine Registrierung im verwalteten Lab durch. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Sendet eine E-Mail mit dem Registrierungslink an das Lab. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/users/delete | Hiermit werden Benutzer gelöscht. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/users/read | Hiermit werden Benutzer gelesen. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/users/write | Dient zum Hinzufügen oder Ändern von Benutzern. |
> | Aktion | Microsoft.LabServices/labAccounts/labs/write | Dient zum Hinzufügen oder Ändern von Labs. |
> | Aktion | Microsoft.LabServices/labAccounts/read | Hiermit werden Lab-Konten gelesen. |
> | Aktion | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Löscht sharedgalleries. |
> | Aktion | Microsoft.LabServices/labAccounts/sharedGalleries/read | Liest sharedgalleries. |
> | Aktion | Microsoft.LabServices/labAccounts/sharedGalleries/write | Dient zum Hinzufügen oder Ändern von sharedgalleries. |
> | Aktion | Microsoft.LabServices/labAccounts/sharedImages/delete | Löscht sharedimages. |
> | Aktion | Microsoft.LabServices/labAccounts/sharedImages/read | Liest sharedimages. |
> | Aktion | Microsoft.LabServices/labAccounts/sharedImages/write | Dient zum Hinzufügen oder Ändern von sharedimages. |
> | Aktion | Microsoft.LabServices/labAccounts/write | Dient zum Hinzufügen oder Ändern von Lab-Konten. |
> | Aktion | Microsoft.LabServices/locations/operations/read | Dient zum Lesen von Vorgängen. |
> | Aktion | Microsoft.LabServices/register/action | Registriert das Abonnement. |
> | Aktion | Microsoft.LabServices/users/GetEnvironment/action | Ruft Details zu virtuellen Computern ab. |
> | Aktion | Microsoft.LabServices/users/GetOperationBatchStatus/action | Hiermit wird der Batchvorgangsstatus abgerufen. |
> | Aktion | Microsoft.LabServices/users/GetOperationStatus/action | Ruft den Status eines Vorgangs mit langer Ausführungsdauer ab. |
> | Aktion | Microsoft.LabServices/users/GetPersonalPreferences/action | Ruft persönliche Einstellungen für einen Benutzer ab. |
> | Aktion | Microsoft.LabServices/users/ListAllEnvironments/action | Listet alle Umgebungen für den Benutzer auf. |
> | Aktion | Microsoft.LabServices/users/ListEnvironments/action | Führt die Umgebungen für den Benutzer auf. |
> | Aktion | Microsoft.LabServices/users/ListLabs/action | Führt die Labs für den Benutzer auf. |
> | Aktion | Microsoft.LabServices/users/Register/action | Registriert einen Benutzer für ein verwaltetes Lab. |
> | Aktion | Microsoft.LabServices/users/ResetPassword/action | Setzt das Kennwort des Benutzers für eine Umgebung zurück. |
> | Aktion | Microsoft.LabServices/users/StartEnvironment/action | Startet eine Umgebung, indem alle Ressourcen innerhalb der Umgebung gestartet werden. |
> | Aktion | Microsoft.LabServices/users/StopEnvironment/action | Beendet eine Umgebung, indem alle Ressourcen innerhalb der Umgebung beendet werden. |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | BESCHREIBUNG |
> | --- | --- | --- |
> | DataAction | Microsoft.LogAnalytics/logs/ADAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der ADAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ADReplicationResult/read | Hiermit lesen Sie Daten aus der ADReplicationResult-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ADSecurityAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der ADSecurityAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/Alert/read | Hiermit lesen Sie Daten aus der Alert-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/AlertHistory/read | Hiermit lesen Sie Daten aus der AlertHistory-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/AppCenterError/read | Liest Daten aus der AppCenterError-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ApplicationInsights/read | Hiermit lesen Sie Daten aus der ApplicationInsights-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/AuditLogs/read | Dient zum Lesen von Daten aus der AuditLogs-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/AzureActivity/read | Hiermit lesen Sie Daten aus der AzureActivity-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/AzureMetrics/read | Hiermit lesen Sie Daten aus der AzureMetrics-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/BoundPort/read | Hiermit lesen Sie Daten aus der BoundPort-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/CommonSecurityLog/read | Hiermit lesen Sie Daten aus der CommonSecurityLog-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ComputerGroup/read | Hiermit lesen Sie Daten aus der ComputerGroup-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationChange/read | Hiermit lesen Sie Daten aus der ConfigurationChange-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationData/read | Hiermit lesen Sie Daten aus der ConfigurationData-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerImageInventory/read | Hiermit lesen Sie Daten aus der ContainerImageInventory-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerInventory/read | Hiermit lesen Sie Daten aus der ContainerInventory-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerLog/read | Hiermit lesen Sie Daten aus der ContainerLog-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerServiceLog/read | Hiermit lesen Sie Daten aus der ContainerServiceLog-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppCrash/read | Hiermit lesen Sie Daten aus der DeviceAppCrash-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppLaunch/read | Hiermit lesen Sie Daten aus der DeviceAppLaunch-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCalendar/read | Hiermit lesen Sie Daten aus der DeviceCalendar-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCleanup/read | Hiermit lesen Sie Daten aus der DeviceCleanup-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceConnectSession/read | Hiermit lesen Sie Daten aus der DeviceConnectSession-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceEtw/read | Hiermit lesen Sie Daten aus der DeviceEtw-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHardwareHealth/read | Hiermit lesen Sie Daten aus der DeviceHardwareHealth-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHealth/read | Hiermit lesen Sie Daten aus der DeviceHealth-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHeartbeat/read | Hiermit lesen Sie Daten aus der DeviceHeartbeat-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeHeartbeat/read | Hiermit lesen Sie Daten aus der DeviceSkypeHeartbeat-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeSignIn/read | Hiermit lesen Sie Daten aus der DeviceSkypeSignIn-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSleepState/read | Hiermit lesen Sie Daten aus der DeviceSleepState-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppFailure/read | Hiermit lesen Sie Daten aus der DHAppFailure-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppReliability/read | Hiermit lesen Sie Daten aus der DHAppReliability-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DHDriverReliability/read | Hiermit lesen Sie Daten aus der DHDriverReliability-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonFailures/read | Hiermit lesen Sie Daten aus der DHLogonFailures-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonMetrics/read | Hiermit lesen Sie Daten aus der DHLogonMetrics-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSCrashData/read | Hiermit lesen Sie Daten aus der DHOSCrashData-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSReliability/read | Hiermit lesen Sie Daten aus der DHOSReliability-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DHWipAppLearning/read | Hiermit lesen Sie Daten aus der DHWipAppLearning-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DnsEvents/read | Hiermit lesen Sie Daten aus der DnsEvents-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/DnsInventory/read | Hiermit lesen Sie Daten aus der DnsInventory-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ETWEvent/read | Hiermit lesen Sie Daten aus der ETWEvent-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/Event/read | Hiermit lesen Sie Daten aus der Event-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der ExchangeAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeOnlineAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der ExchangeOnlineAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/Heartbeat/read | Hiermit lesen Sie Daten aus der Heartbeat-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/IISAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der IISAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/InboundConnection/read | Hiermit lesen Sie Daten aus der InboundConnection-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/KubeEvents/read | Liest Daten aus der Tabelle „KubeEvents“. |
> | DataAction | Microsoft.LogAnalytics/logs/KubeNodeInventory/read | Hiermit lesen Sie Daten aus der KubeNodeInventory-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/KubePodInventory/read | Hiermit lesen Sie Daten aus der KubePodInventory-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/KubeServices/read | Liest Daten aus der Tabelle „KubeServices“. |
> | DataAction | Microsoft.LogAnalytics/logs/LinuxAuditLog/read | Hiermit lesen Sie Daten aus der LinuxAuditLog-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplication/read | Hiermit lesen Sie Daten aus der MAApplication-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealth/read | Hiermit lesen Sie Daten aus der MAApplicationHealth-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthAlternativeVersions/read | Liest Daten aus der Tabelle „MAApplicationHealthAlternativeVersions“. |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthIssues/read | Liest Daten aus der Tabelle „MAApplicationHealthIssues“. |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstance/read | Hiermit lesen Sie Daten aus der MAApplicationInstance-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstanceReadiness/read | Liest Daten aus der Tabelle „MAApplicationInstanceReadiness“. |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationReadiness/read | Hiermit lesen Sie Daten aus der MAApplicationReadiness-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MADeploymentPlan/read | Hiermit lesen Sie Daten aus der MADeploymentPlan-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MADevice/read | Hiermit lesen Sie Daten aus der MADevice-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceNotEnrolled/read | Liest Daten aus der MADeviceNotEnrolled-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealth/read | Liest Daten aus der Tabelle „MADevicePnPHealth“. |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthAlternativeVersions/read | Liest Daten aus der Tabelle „MADevicePnPHealthAlternativeVersions“. |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthIssues/read | Liest Daten aus der Tabelle „MADevicePnPHealthIssues“. |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceReadiness/read | Hiermit lesen Sie Daten aus der MADeviceReadiness-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverInstanceReadiness/read | Liest Daten aus der Tabelle „MADriverInstanceReadiness“. |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverReadiness/read | Hiermit lesen Sie Daten aus der MADriverReadiness-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddin/read | Hiermit lesen Sie Daten aus der MAOfficeAddin-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealth/read | Hiermit lesen Sie Daten aus der MAOfficeAddinHealth-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealthIssues/read | Liest Daten aus der Tabelle „MAOfficeAddinHealthIssues“. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstance/read | Hiermit lesen Sie Daten aus der MAOfficeAddinInstance-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstanceReadiness/read | Liest Daten aus der Tabelle „MAOfficeAddinInstanceReadiness“. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinReadiness/read | Hiermit lesen Sie Daten aus der MAOfficeAddinReadiness-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeApp/read | Hiermit lesen Sie Daten aus der MAOfficeApp-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppHealth/read | Hiermit lesen Sie Daten aus der MAOfficeAppHealth-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppInstance/read | Hiermit lesen Sie Daten aus der MAOfficeAppInstance-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppReadiness/read | Hiermit lesen Sie Daten aus der MAOfficeAppReadiness-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeBuildInfo/read | Hiermit lesen Sie Daten aus der MAOfficeBuildInfo-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessment/read | Hiermit lesen Sie Daten aus der MAOfficeCurrencyAssessment-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessmentDailyCounts/read | Hiermit lesen Sie Daten aus der MAOfficeCurrencyAssessmentDailyCounts-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeDeploymentStatus/read | Hiermit lesen Sie Daten aus der MAOfficeDeploymentStatus-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealth/read | Liest Daten aus der Tabelle „MAOfficeMacroHealth“. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealthIssues/read | Liest Daten aus der Tabelle „MAOfficeMacroHealthIssues“. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueInstanceReadiness/read | Liest Daten aus der Tabelle „MAOfficeMacroIssueInstanceReadiness“. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueReadiness/read | Hiermit lesen Sie Daten aus der MAOfficeMacroIssueReadiness-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroSummary/read | Hiermit lesen Sie Daten aus der MAOfficeMacroSummary-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuite/read | Hiermit lesen Sie Daten aus der MAOfficeSuite-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuiteInstance/read | Hiermit lesen Sie Daten aus der MAOfficeSuiteInstance-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAProposedPilotDevices/read | Hiermit lesen Sie Daten aus der MAProposedPilotDevices-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsBuildInfo/read | Hiermit lesen Sie Daten aus der MAWindowsBuildInfo-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessment/read | Hiermit lesen Sie Daten aus der MAWindowsCurrencyAssessment-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessmentDailyCounts/read | Hiermit lesen Sie Daten aus der MAWindowsCurrencyAssessmentDailyCounts-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsDeploymentStatus/read | Hiermit lesen Sie Daten aus der MAWindowsDeploymentStatus-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsSysReqInstanceReadiness/read | Liest Daten aus der Tabelle „MAWindowsSysReqInstanceReadiness“. |
> | DataAction | Microsoft.LogAnalytics/logs/NetworkMonitoring/read | Hiermit lesen Sie Daten aus der NetworkMonitoring-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/OfficeActivity/read | Hiermit lesen Sie Daten aus der OfficeActivity-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/Operation/read | Hiermit lesen Sie Daten aus der Operation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/OutboundConnection/read | Hiermit lesen Sie Daten aus der OutboundConnection-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/Perf/read | Hiermit lesen Sie Daten aus der Perf-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ProtectionStatus/read | Hiermit lesen Sie Daten aus der ProtectionStatus-Tabelle. |
> | Aktion | Microsoft.LogAnalytics/logs/read | Hiermit lesen Sie Daten aus allen Ihren Protokollen. |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedAzureCommonFields/read | Hiermit lesen Sie Daten aus der ReservedAzureCommonFields-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedCommonFields/read | Hiermit lesen Sie Daten aus der ReservedCommonFields-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SCCMAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SCCMAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SCOMAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SCOMAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityAlert/read | Hiermit lesen Sie Daten aus der SecurityAlert-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaseline/read | Hiermit lesen Sie Daten aus der SecurityBaseline-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaselineSummary/read | Hiermit lesen Sie Daten aus der SecurityBaselineSummary-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityDetection/read | Hiermit lesen Sie Daten aus der SecurityDetection-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityEvent/read | Hiermit lesen Sie Daten aus der SecurityEvent-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricOperationalEvent/read | Hiermit lesen Sie Daten aus der ServiceFabricOperationalEvent-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableActorEvent/read | Hiermit lesen Sie Daten aus der ServiceFabricReliableActorEvent-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableServiceEvent/read | Hiermit lesen Sie Daten aus der ServiceFabricReliableServiceEvent-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SfBAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SfBAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SfBOnlineAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SfBOnlineAssessmentRecommendatio-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SharePointOnlineAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SharePointOnlineAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SigninLogs/read | Dient zum Lesen von Daten aus der SigninLogs-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SPAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SPAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SQLAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SQLAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SQLQueryPerformance/read | Hiermit lesen Sie Daten aus der SQLQueryPerformance-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/Syslog/read | Hiermit lesen Sie Daten aus der Syslog-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/SysmonEvent/read | Hiermit lesen Sie Daten aus der SysmonEvent-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/Tables.Custom/read | Hiermit lesen Sie Daten aus einem beliebigen benutzerdefinierten Protokoll. |
> | DataAction | Microsoft.LogAnalytics/logs/UAApp/read | Hiermit lesen Sie Daten aus der UAApp-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputer/read | Hiermit lesen Sie Daten aus der UAComputer-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputerRank/read | Hiermit lesen Sie Daten aus der UAComputerRank-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UADriver/read | Hiermit lesen Sie Daten aus der UADriver-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UADriverProblemCodes/read | Hiermit lesen Sie Daten aus der UADriverProblemCodes-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UAFeedback/read | Hiermit lesen Sie Daten aus der UAFeedback-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UAHardwareSecurity/read | Hiermit lesen Sie Daten aus der UAHardwareSecurity-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UAIESiteDiscovery/read | Hiermit lesen Sie Daten aus der UAIESiteDiscovery-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UAOfficeAddIn/read | Hiermit lesen Sie Daten aus der UAOfficeAddIn-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UAProposedActionPlan/read | Hiermit lesen Sie Daten aus der UAProposedActionPlan-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UASysReqIssue/read | Hiermit lesen Sie Daten aus der UASysReqIssue-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UAUpgradedComputer/read | Hiermit lesen Sie Daten aus der UAUpgradedComputer-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/Update/read | Hiermit lesen Sie Daten aus der Update-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateRunProgress/read | Hiermit lesen Sie Daten aus der UpdateRunProgress-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateSummary/read | Hiermit lesen Sie Daten aus der UpdateSummary-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/Usage/read | Hiermit lesen Sie Daten aus der Usage-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/VMBoundPort/read | Liest Daten aus der Tabelle „VMBoundPort“. |
> | DataAction | Microsoft.LogAnalytics/logs/VMConnection/read | Liest Daten aus der Tabelle „VMConnection“. |
> | DataAction | Microsoft.LogAnalytics/logs/W3CIISLog/read | Hiermit lesen Sie Daten aus der W3CIISLog-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSDeploymentStatus/read | Hiermit lesen Sie Daten aus der WaaSDeploymentStatus-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSInsiderStatus/read | Hiermit lesen Sie Daten aus der WaaSInsiderStatus-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSUpdateStatus/read | Hiermit lesen Sie Daten aus der WaaSUpdateStatus-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVStatus/read | Hiermit lesen Sie Daten aus der WDAVStatus-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVThreat/read | Hiermit lesen Sie Daten aus der WDAVThreat-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsClientAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der WindowsClientAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsEvent/read | Dient zum Lesen von Daten aus der WindowsEvent-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsFirewall/read | Hiermit lesen Sie Daten aus der WindowsFirewall-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsServerAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der WindowsServerAssessmentRecommendation-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WireData/read | Hiermit lesen Sie Daten aus der WireData-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WorkloadMonitoringPerf/read | Lesen von Daten aus der Tabelle „WorkloadMonitoringPerf“ |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOAggregatedStatus/read | Hiermit lesen Sie Daten aus der WUDOAggregatedStatus-Tabelle. |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOStatus/read | Hiermit lesen Sie Daten aus der WUDOStatus-Tabelle. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Logic/integrationAccounts/agreements/delete | Löscht die Vereinbarung im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Ruft die Rückruf-URL für den Vereinbarungsinhalt im Integrationskonto ab. |
> | Aktion | Microsoft.Logic/integrationAccounts/agreements/read | Liest die Vereinbarung im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/agreements/write | Erstellt oder aktualisiert die Vereinbarung im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/assemblies/delete | Löscht die Assembly im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Ruft die Rückruf-URL für den Inhalt der Assembly im Integrationskonto ab. |
> | Aktion | Microsoft.Logic/integrationAccounts/assemblies/read | Liest die Assembly im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/assemblies/write | Erstellt oder aktualisiert die Assembly im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Löscht die Batchkonfiguration im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Liest die Batchkonfiguration im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Erstellt oder aktualisiert die Batchkonfiguration im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/certificates/delete | Löscht das Zertifikat im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/certificates/read | Liest das Zertifikat im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/certificates/write | Erstellt oder aktualisiert das Zertifikat im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/delete | Löscht das Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/join/action | Verknüpft das Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Ruft die Rückruf-URL für das Integrationskonto ab. |
> | Aktion | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Ruft die Schlüssel im Schlüsseltresor ab. |
> | Aktion | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Protokolliert die Überwachungsereignisse im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/maps/delete | Löscht die Zuordnung im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Ruft die Rückruf-URL für den Inhalt der Zuordnung im Integrationskonto ab. |
> | Aktion | Microsoft.Logic/integrationAccounts/maps/read | Liest die Zuordnung im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/maps/write | Erstellt oder aktualisiert die Zuordnung im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/partners/delete | Löscht den Partner im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Ruft die Rückruf-URL für den Partnerinhalt im Integrationskonto ab. |
> | Aktion | Microsoft.Logic/integrationAccounts/partners/read | Liest den Partner im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/partners/write | Erstellt oder aktualisiert den Partner im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Liest die Protokolldefinitionen des Integrationskontos. |
> | Aktion | Microsoft.Logic/integrationAccounts/read | Liest das Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Generiert die Zugriffsschlüsselgeheimnisse neu. |
> | Aktion | Microsoft.Logic/integrationAccounts/schemas/delete | Löscht das Schema im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Ruft die Rückruf-URL für den Schemainhalt im Integrationskonto ab. |
> | Aktion | Microsoft.Logic/integrationAccounts/schemas/read | Liest das Schema im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/schemas/write | Erstellt oder aktualisiert das Schema im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/sessions/delete | Löscht die Sitzung im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/sessions/read | Liest die Batchkonfiguration im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/sessions/write | Erstellt oder aktualisiert die Sitzung im Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationAccounts/write | Erstellt oder aktualisiert das Integrationskonto. |
> | Aktion | Microsoft.Logic/integrationServiceEnvironments/delete | Hiermit wird die Integrationsdienstumgebung gelöscht. |
> | Aktion | Microsoft.Logic/integrationServiceEnvironments/join/action | Hiermit erfolgt ein Beitritt zur Integrationsdienstumgebung. |
> | Aktion | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Liest den Vorgang der in der Integrationsdienstumgebung verwalteten API. |
> | Aktion | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Liest die in der Integrationsdienstumgebung verwalteten API. |
> | Aktion | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Liest die Vorgangsstatusangaben zur Integrationsdienstumgebung. |
> | Aktion | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Liest die Metrikdefinitionen in der Integrationsdienstumgebung. |
> | Aktion | Microsoft.Logic/integrationServiceEnvironments/read | Hiermit wird die Integrationsdienstumgebung gelesen. |
> | Aktion | Microsoft.Logic/integrationServiceEnvironments/write | Hiermit wird die Integrationsdienstumgebung erstellt oder aktualisiert. |
> | Aktion | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Ruft den Workflow der empfohlenen Vorgangsgruppen ab. |
> | Aktion | Microsoft.Logic/locations/workflows/validate/action | Überprüft den Workflow. |
> | Aktion | Microsoft.Logic/operations/read | Ruft den Vorgang ab. |
> | Aktion | Microsoft.Logic/register/action | Registriert den Microsoft.Logic-Ressourcenanbieter für ein angegebenes Abonnement. |
> | Aktion | Microsoft.Logic/workflows/accessKeys/delete | Löscht den Zugriffsschlüssel. |
> | Aktion | Microsoft.Logic/workflows/accessKeys/list/action | Listet die Zugriffsschlüsselgeheimnisse auf. |
> | Aktion | Microsoft.Logic/workflows/accessKeys/read | Liest den Zugriffsschlüssel. |
> | Aktion | Microsoft.Logic/workflows/accessKeys/regenerate/action | Generiert die Zugriffsschlüsselgeheimnisse neu. |
> | Aktion | Microsoft.Logic/workflows/accessKeys/write | Erstellt oder aktualisiert den Zugriffsschlüssel. |
> | Aktion | Microsoft.Logic/workflows/delete | Löscht den Workflow. |
> | Aktion | Microsoft.Logic/workflows/disable/action | Deaktiviert den Workflow. |
> | Aktion | Microsoft.Logic/workflows/enable/action | Aktiviert den Workflow. |
> | Aktion | Microsoft.Logic/workflows/listCallbackUrl/action | Ruft die Rückruf-URL für Workflows ab. |
> | Aktion | Microsoft.Logic/workflows/listSwagger/action | Ruft die Swagger-Definitionen für den Workflow ab. |
> | Aktion | Microsoft.Logic/workflows/move/action | Verschiebt den Workflow von der vorhandenen Abonnement-ID, der Ressourcengruppe und/oder dem Namen in eine andere Abonnement-ID, eine andere Ressourcengruppe und/oder einen anderen Namen. |
> | Aktion | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Liest die Diagnoseeinstellungen des Workflows. |
> | Aktion | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung des Workflows. |
> | Aktion | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Liest die Protokolldefinitionen des Workflows. |
> | Aktion | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Liest die Metrikdefinitionen des Workflows. |
> | Aktion | Microsoft.Logic/workflows/read | Liest den Workflow. |
> | Aktion | Microsoft.Logic/workflows/regenerateAccessKey/action | Generiert die Zugriffsschlüsselgeheimnisse neu. |
> | Aktion | Microsoft.Logic/workflows/run/action | Startet eine Ausführung des Workflows. |
> | Aktion | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Ruft die Ausdrucksablaufverfolgungen für Workflowausführungsaktionen ab. |
> | Aktion | Microsoft.Logic/workflows/runs/actions/read | Liest die Workflowausführungsaktion. |
> | Aktion | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Ruft die Ausdrucksablaufverfolgungen für Wiederholungen von Workflowausführungsaktionen ab. |
> | Aktion | Microsoft.Logic/workflows/runs/actions/repetitions/read | Liest die Wiederholung von Workflowausführungsaktionen. |
> | Aktion | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | List den Anforderungsverlauf für wiederholte Workflowausführungsaktionen. |
> | Aktion | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Liest den Anforderungsverlauf für Workflowausführungsaktionen. |
> | Aktion | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Liest die Bereichswiederholung von Workflowausführungsaktionen. |
> | Aktion | Microsoft.Logic/workflows/runs/cancel/action | Bricht die Ausführung eines Workflows ab. |
> | Aktion | Microsoft.Logic/workflows/runs/delete | Löscht eine Ausführung eines Workflows. |
> | Aktion | Microsoft.Logic/workflows/runs/operations/read | Liest den Status von Workflowausführungsaktionen. |
> | Aktion | Microsoft.Logic/workflows/runs/read | Liest die Workflowausführung. |
> | Aktion | Microsoft.Logic/workflows/suspend/action | Hält den Workflow an. |
> | Aktion | Microsoft.Logic/workflows/triggers/histories/read | Liest die Triggerverläufe. |
> | Aktion | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Übermittelt den Workflowtrigger erneut. |
> | Aktion | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Ruft die Rückruf-URL für Trigger ab. |
> | Aktion | Microsoft.Logic/workflows/triggers/read | Liest den Trigger. |
> | Aktion | Microsoft.Logic/workflows/triggers/reset/action | Setzt den Trigger zurück. |
> | Aktion | Microsoft.Logic/workflows/triggers/run/action | Führt den Trigger aus. |
> | Aktion | Microsoft.Logic/workflows/triggers/setState/action | Legt den Triggerzustand fest. |
> | Aktion | Microsoft.Logic/workflows/validate/action | Überprüft den Workflow. |
> | Aktion | Microsoft.Logic/workflows/versions/read | Liest die Workflowversion. |
> | Aktion | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Ruft die Rückruf-URL für Trigger ab. |
> | Aktion | Microsoft.Logic/workflows/write | Erstellt oder aktualisiert den Workflow. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Dient zum Verschieben beliebiger Machine Learning-Vertragsplanzuordnungen. |
> | Aktion | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Dient zum Lesen beliebiger Machine Learning-Vertragsplanzuordnungen. |
> | Aktion | Microsoft.MachineLearning/commitmentPlans/delete | Dient zum Löschen beliebiger Machine Learning-Vertragspläne. |
> | Aktion | Microsoft.MachineLearning/commitmentPlans/join/action | Dient zum Beitreten zu beliebigen Machine Learning-Vertragsplänen. |
> | Aktion | Microsoft.MachineLearning/commitmentPlans/read | Dient zum Lesen beliebiger Machine Learning-Vertragspläne. |
> | Aktion | Microsoft.MachineLearning/commitmentPlans/write | Dient zum Erstellen oder Aktualisieren beliebiger Machine Learning-Vertragspläne. |
> | Aktion | Microsoft.MachineLearning/locations/operationresults/read | Ruft das Ergebnis eines Machine Learning-Vorgangs ab. |
> | Aktion | Microsoft.MachineLearning/locations/operationsstatus/read | Ruft den Status eines laufenden Machine Learning-Vorgangs ab. |
> | Aktion | Microsoft.MachineLearning/operations/read | Ruft Machine Learning-Vorgänge ab. |
> | Aktion | Microsoft.MachineLearning/register/action | Registriert das Abonnement für den Machine Learning-Webdienst-Ressourcenanbieter und ermöglicht die Erstellung von Webdiensten. |
> | Aktion | Microsoft.MachineLearning/skus/read | Dient zum Abrufen von SKUs für Machine Learning-Verpflichtungspläne. |
> | Aktion | Microsoft.MachineLearning/webServices/action | Dient zum Erstellen regionaler Webdiensteigenschaften für unterstützte Regionen. |
> | Aktion | Microsoft.MachineLearning/webServices/delete | Dient zum Löschen beliebiger Machine Learning-Webdienste. |
> | Aktion | Microsoft.MachineLearning/webServices/listkeys/read | Ruft Schlüssel für einen Machine Learning-Webdienst ab. |
> | Aktion | Microsoft.MachineLearning/webServices/read | Dient zum Lesen beliebiger Machine Learning-Webdienste. |
> | Aktion | Microsoft.MachineLearning/webServices/write | Dient zum Erstellen oder Aktualisieren beliebiger Machine Learning-Webdienste. |
> | Aktion | Microsoft.MachineLearning/Workspaces/delete | Dient zum Löschen beliebiger Machine Learning-Arbeitsbereiche. |
> | Aktion | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Dient zum Auflisten von Schlüsseln für einen Machine Learning-Arbeitsbereich. |
> | Aktion | Microsoft.MachineLearning/Workspaces/read | Dient zum Lesen beliebiger Machine Learning-Arbeitsbereiche. |
> | Aktion | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Dient zum erneuten Synchronisieren von Schlüsseln eines für einen Machine Learning-Arbeitsbereich konfigurierten Speicherkontos. |
> | Aktion | Microsoft.MachineLearning/Workspaces/write | Dient zum Erstellen oder Aktualisieren beliebiger Machine Learning-Arbeitsbereiche. |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Hiermit wird überprüft, ob Updates für Systemdienste für den Operationalisierungscluster verfügbar sind. |
> | Aktion | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Hiermit werden alle Hostingkonten gelöscht. |
> | Aktion | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Hiermit werden die dem Operationalisierungscluster zugeordneten Schlüssel aufgelistet. |
> | Aktion | Microsoft.MachineLearningCompute/operationalizationClusters/read | Hiermit werden alle Hostingkonten gelesen. |
> | Aktion | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Hiermit werden die Systemdienste in einem Operationalisierungscluster aktualisiert. |
> | Aktion | Microsoft.MachineLearningCompute/operationalizationClusters/write | Hiermit werden alle Hostingkonten erstellt oder aktualisiert. |
> | Aktion | Microsoft.MachineLearningCompute/register/action | Registriert die Abonnement-ID beim Ressourcenanbieter und ermöglicht die Erstellung von Machine Learning-Computeressourcen. |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.MachineLearningModelManagement/accounts/delete | Hiermit werden alle Hostingkonten gelöscht. |
> | Aktion | Microsoft.MachineLearningModelManagement/accounts/read | Hiermit werden alle Hostingkonten gelesen. |
> | Aktion | Microsoft.MachineLearningModelManagement/accounts/write | Hiermit werden alle Hostingkonten erstellt oder aktualisiert. |
> | Aktion | Microsoft.MachineLearningModelManagement/register/action | Registriert die Abonnement-ID beim Ressourcenanbieter und ermöglicht die Erstellung eines Hostingkontos. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Ruft den Status eines bestimmten Computevorgangs ab. |
> | Aktion | Microsoft.MachineLearningServices/locations/usages/read | Nutzungsbericht für AML-Computeressourcen in einem Abonnement. |
> | Aktion | Microsoft.MachineLearningServices/locations/vmsizes/read | Ruft unterstützte VM-Größen ab. |
> | Aktion | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Ruft den Status eines bestimmten Arbeitsbereichsvorgangs ab. |
> | Aktion | Microsoft.MachineLearningServices/register/action | Registriert das Abonnement für den Machine Learning Services-Ressourcenanbieter. |
> | Aktion | Microsoft.MachineLearningServices/workspaces/computes/delete | Löscht die Computeressourcen in Machine Learning Services-Arbeitsbereichen. |
> | Aktion | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Führt geheime Schlüssel für die Computeressourcen in Machine Learning Services-Arbeitsbereichen auf. |
> | Aktion | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Listet Knoten für die Computeressource im Machine Learning Services-Arbeitsbereich auf. |
> | Aktion | Microsoft.MachineLearningServices/workspaces/computes/read | Ruft die Computeressourcen in Machine Learning Services-Arbeitsbereichen ab. |
> | Aktion | Microsoft.MachineLearningServices/workspaces/computes/write | Erstellt oder aktualisiert die Computeressourcen in Machine Learning Services-Arbeitsbereichen. |
> | Aktion | Microsoft.MachineLearningServices/workspaces/delete | Löscht die Machine Learning Services-Arbeitsbereiche. |
> | Aktion | Microsoft.MachineLearningServices/workspaces/listKeys/action | Führt die geheimen Schlüssel für einen Machine Learning Services-Arbeitsbereich auf. |
> | Aktion | Microsoft.MachineLearningServices/workspaces/read | Ruft die Machine Learning Services-Arbeitsbereiche ab. |
> | Aktion | Microsoft.MachineLearningServices/workspaces/write | Erstellt oder aktualisiert die Machine Learning Services-Arbeitsbereiche. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ManagedIdentity/register/action | Registriert das Abonnement für den Ressourcenanbieter der verwalteten Identität. |
> | Aktion | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC-Aktion für das Zuweisen einer vorhandenen Identität, die einem Benutzer zugewiesen ist, zu einer Ressource |
> | Aktion | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Löscht eine vorhandene zugewiesene Benutzeridentität. |
> | Aktion | Microsoft.ManagedIdentity/userAssignedIdentities/read | Ruft eine vorhandene zugewiesene Benutzeridentität ab. |
> | Aktion | Microsoft.ManagedIdentity/userAssignedIdentities/write | Erstellt eine neue zugewiesene Benutzeridentität oder aktualisiert die Markierungen, die einer vorhandenen zugewiesenen Benutzeridentität zugeordnet sind. |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ManagedLab/labAccounts/CreateLab/action | Hiermit wird ein Lab in einem Lab-Konto erstellt. |
> | Aktion | Microsoft.ManagedLab/labAccounts/delete | Hiermit werden Lab-Konten gelöscht. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/delete | Dient zum Löschen von Labs. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Hiermit wird eine Umgebungseinstellung gelöscht. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Dient zum Löschen von Umgebungen. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Dient zum Lesen von Umgebungen. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Dient zum Hinzufügen oder Ändern von Umgebungen. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Hiermit wird eine Umgebungseinstellung gelesen. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Dient zum Hinzufügen oder Ändern einer Umgebungseinstellung. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Löscht virtuelle Lab-Computer. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Liest virtuelle Lab-Computer. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Dient zum Hinzufügen oder Ändern virtueller Lab-Computer. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/read | Dient zum Lesen von Labs. |
> | Aktion | Microsoft.ManagedLab/labAccounts/labs/write | Dient zum Hinzufügen oder Ändern von Labs. |
> | Aktion | Microsoft.ManagedLab/labAccounts/read | Hiermit werden Lab-Konten gelesen. |
> | Aktion | Microsoft.ManagedLab/labAccounts/write | Dient zum Hinzufügen oder Ändern von Lab-Konten. |
> | Aktion | Microsoft.ManagedLab/locations/operations/read | Dient zum Lesen von Vorgängen. |
> | Aktion | Microsoft.ManagedLab/register/action | Registriert das Abonnement. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Management/checkNameAvailability/action | Überprüft, ob der Name der angegebenen Verwaltungsgruppe gültig und eindeutig ist. |
> | Aktion | Microsoft.Management/getEntities/action | Listet alle Entitäten (Verwaltungsgruppen, Abonnements usw.) für den authentifizierten Benutzer auf. |
> | Aktion | Microsoft.Management/managementGroups/delete | Löscht eine Verwaltungsgruppe. |
> | Aktion | Microsoft.Management/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | Aktion | Microsoft.Management/managementGroups/subscriptions/delete | Hebt die Zuordnung des Abonnements mit der Verwaltungsgruppe auf. |
> | Aktion | Microsoft.Management/managementGroups/subscriptions/write | Ordnet ein vorhandenes Abonnement der Verwaltungsgruppe zu. |
> | Aktion | Microsoft.Management/managementGroups/write | Erstellt oder aktualisiert eine Verwaltungsgruppe. |
> | Aktion | Microsoft.Management/register/action | Registriert das angegebene Abonnement mit Microsoft.Management. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | BESCHREIBUNG |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Gewährt Datenlesezugriff auf ein Maps-Konto. |
> | Aktion | Microsoft.Maps/accounts/delete | Löscht ein Maps-Konto. |
> | Aktion | Microsoft.Maps/accounts/eventGridFilters/delete | Löscht einen Event Grid-Filter. |
> | Aktion | Microsoft.Maps/accounts/eventGridFilters/read | Ruft einen Event Grid-Filter ab. |
> | Aktion | Microsoft.Maps/accounts/eventGridFilters/write | Erstellt oder aktualisiert einen Event Grid-Filter. |
> | Aktion | Microsoft.Maps/accounts/listKeys/action | Führt Schlüssel für ein Maps-Konto auf. |
> | Aktion | Microsoft.Maps/accounts/read | Ruft ein Maps-Konto ab. |
> | Aktion | Microsoft.Maps/accounts/regenerateKey/action | Generiert einen neuen primären oder sekundären Schlüssel für ein Maps-Konto. |
> | Aktion | Microsoft.Maps/accounts/write | Erstellt oder aktualisiert ein Maps-Konto. |
> | Aktion | Microsoft.Maps/operations/read | Liest die Anbietervorgänge. |
> | Aktion | Microsoft.Maps/register/action | Hiermit wird der Anbieter registriert. |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Gibt eine Vereinbarung zurück. |
> | Aktion | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Akzeptiert eine unterzeichnete Vereinbarung. |
> | Aktion | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importiert ein Image in den Access Control-Datensatz des Endbenutzers. |
> | Aktion | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Gibt eine Konfiguration zurück. |
> | Aktion | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Speichert eine Konfiguration. |
> | Aktion | Microsoft.Marketplace/register/action | Registriert den Microsoft.Marketplace-Ressourcenanbieter im Abonnement. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.MarketplaceApps/ClassicDevServices/delete | Führt einen DELETE-Vorgang für eine Ressource eines klassischen Entwicklerdiensts durch. |
> | Aktion | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Ruft die Ressourcenverwaltungsschlüssel eines klassischen Entwicklerdiensts ab. |
> | Aktion | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Ruft die URL für einmaliges Anmelden für einen klassischen Entwicklerdienst ab. |
> | Aktion | Microsoft.MarketplaceApps/ClassicDevServices/read | Führt einen GET-Vorgang für einen klassischen Entwicklerdienst durch. |
> | Aktion | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Generiert Ressourcenverwaltungsschlüssel eines klassischen Entwicklerdiensts. |
> | Aktion | Microsoft.MarketplaceApps/Operations/read | Liest die Vorgänge für alle Ressourcentypen. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Dient zum Stornieren einer Vereinbarung für ein angegebenes Marketplace-Element. |
> | Aktion | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Dient zum Zurückgeben einer Vereinbarung für ein angegebenes Marketplace-Element. |
> | Aktion | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Dient zum Unterzeichnen einer Vereinbarung für ein angegebenes Marketplace-Element. |
> | Aktion | Microsoft.MarketplaceOrdering/agreements/read | Gibt alle Vereinbarungen eines bestimmten Abonnements zurück. |
> | Aktion | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Dient zum Abrufen einer Vereinbarung für ein angegebenes Marketplace-VM-Element. |
> | Aktion | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Dient zum Registrieren oder Stornieren einer Vereinbarung für ein angegebenes Marketplace-VM-Element. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Media/checknameavailability/action | Überprüft, ob ein Media Services-Kontoname verfügbar ist. |
> | Aktion | Microsoft.Media/locations/checkNameAvailability/action | Überprüft, ob ein Media Services-Kontoname verfügbar ist. |
> | Aktion | Microsoft.Media/mediaservices/accountfilters/delete | Löscht Kontofilter. |
> | Aktion | Microsoft.Media/mediaservices/accountfilters/read | Liest Kontofilter. |
> | Aktion | Microsoft.Media/mediaservices/accountfilters/write | Dient zum Erstellen oder Aktualisieren von Kontofiltern. |
> | Aktion | Microsoft.Media/mediaservices/assets/assetfilters/delete | Löscht Medienobjektfilter. |
> | Aktion | Microsoft.Media/mediaservices/assets/assetfilters/read | Liest Medienobjektfilter. |
> | Aktion | Microsoft.Media/mediaservices/assets/assetfilters/write | Dient zum Erstellen oder Aktualisieren von Medienobjektfiltern. |
> | Aktion | Microsoft.Media/mediaservices/assets/delete | Löscht alle Objekte. |
> | Aktion | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Ruft Verschlüsselungsschlüssel für ein Objekt ab. |
> | Aktion | Microsoft.Media/mediaservices/assets/listContainerSas/action | Führt die Container-SAS-URLs für ein Objekt auf. |
> | Aktion | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Listet Streaminglocators für ein Medienobjekt auf. |
> | Aktion | Microsoft.Media/mediaservices/assets/read | Liest alle Objekte. |
> | Aktion | Microsoft.Media/mediaservices/assets/write | Erstellt oder aktualisiert alle Objekte. |
> | Aktion | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Löscht alle Richtlinien für Inhaltsschlüssel. |
> | Aktion | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Ruft Richtlinieneigenschaften mit geheimen Schlüsseln ab. |
> | Aktion | Microsoft.Media/mediaservices/contentKeyPolicies/read | Liest alle Richtlinien für Inhaltsschlüssel. |
> | Aktion | Microsoft.Media/mediaservices/contentKeyPolicies/write | Erstellt oder aktualisiert alle Richtlinien für Inhaltsschlüssel. |
> | Aktion | Microsoft.Media/mediaservices/delete | Löscht ein Media Services-Konto. |
> | Aktion | Microsoft.Media/mediaservices/eventGridFilters/delete | Löscht alle Event Grid-Filter. |
> | Aktion | Microsoft.Media/mediaservices/eventGridFilters/read | Liest alle Event Grid-Filter. |
> | Aktion | Microsoft.Media/mediaservices/eventGridFilters/write | Erstellt oder aktualisiert alle Event Grid-Filter. |
> | Aktion | Microsoft.Media/mediaservices/liveEventOperations/read | Liest alle Vorgänge für Liveereignisse. |
> | Aktion | Microsoft.Media/mediaservices/liveEvents/delete | Löscht alle Liveereignisse. |
> | Aktion | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Löscht alle Liveausgaben. |
> | Aktion | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Liest alle Liveausgaben. |
> | Aktion | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Erstellt oder aktualisiert alle Liveausgaben. |
> | Aktion | Microsoft.Media/mediaservices/liveEvents/read | Liest alle Liveereignisse. |
> | Aktion | Microsoft.Media/mediaservices/liveEvents/reset/action | Setzt alle Vorgänge für Liveereignisse zurück. |
> | Aktion | Microsoft.Media/mediaservices/liveEvents/start/action | Startet alle Vorgänge für Liveereignisse. |
> | Aktion | Microsoft.Media/mediaservices/liveEvents/stop/action | Beendet alle Vorgänge für Liveereignisse. |
> | Aktion | Microsoft.Media/mediaservices/liveEvents/write | Erstellt oder aktualisiert alle Liveereignisse. |
> | Aktion | Microsoft.Media/mediaservices/liveOutputOperations/read | Liest alle Vorgänge für Liveausgaben. |
> | Aktion | Microsoft.Media/mediaservices/read | Liest ein Media Services-Konto. |
> | Aktion | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Liest alle Vorgänge des Streamingendpunkts. |
> | Aktion | Microsoft.Media/mediaservices/streamingEndpoints/delete | Löscht alle Streamingendpunkte. |
> | Aktion | Microsoft.Media/mediaservices/streamingEndpoints/read | Liest alle Streamingendpunkte. |
> | Aktion | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Skaliert alle Vorgänge des Streamingendpunkts. |
> | Aktion | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Startet alle Vorgänge des Streamingendpunkts. |
> | Aktion | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Beendet alle Vorgänge des Streamingendpunkts. |
> | Aktion | Microsoft.Media/mediaservices/streamingEndpoints/write | Erstellt oder aktualisiert alle Streamingendpunkte. |
> | Aktion | Microsoft.Media/mediaservices/streamingLocators/delete | Löscht alle Streaminglocators. |
> | Aktion | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Führt Inhaltsschlüssel auf. |
> | Aktion | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Führt Pfade auf. |
> | Aktion | Microsoft.Media/mediaservices/streamingLocators/read | Liest alle Streaminglocators. |
> | Aktion | Microsoft.Media/mediaservices/streamingLocators/write | Erstellt oder aktualisiert alle Streaminglocators. |
> | Aktion | Microsoft.Media/mediaservices/streamingPolicies/delete | Löscht alle Streamingrichtlinien. |
> | Aktion | Microsoft.Media/mediaservices/streamingPolicies/read | Liest alle Streamingrichtlinien. |
> | Aktion | Microsoft.Media/mediaservices/streamingPolicies/write | Erstellt oder aktualisiert alle Streamingrichtlinien. |
> | Aktion | Microsoft.Media/mediaservices/syncStorageKeys/action | Synchronisiert die Speicherschlüssel für ein angefügtes Azure Storage-Konto. |
> | Aktion | Microsoft.Media/mediaservices/transforms/delete | Löscht alle Transformationen. |
> | Aktion | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Dient zum Abbrechen von Aufträgen. |
> | Aktion | Microsoft.Media/mediaservices/transforms/jobs/delete | Löscht alle Aufträge. |
> | Aktion | Microsoft.Media/mediaservices/transforms/jobs/read | Liest alle Aufträge. |
> | Aktion | Microsoft.Media/mediaservices/transforms/jobs/write | Erstellt oder aktualisiert alle Aufträge. |
> | Aktion | Microsoft.Media/mediaservices/transforms/read | Liest alle Transformationen. |
> | Aktion | Microsoft.Media/mediaservices/transforms/write | Erstellt oder aktualisiert alle Transformationen. |
> | Aktion | Microsoft.Media/mediaservices/write | Erstellt oder aktualisiert ein Media Services-Konto. |
> | Aktion | Microsoft.Media/operations/read | Dient zum Abrufen verfügbarer Vorgänge. |
> | Aktion | Microsoft.Media/register/action | Registriert das Abonnement für den Media Services-Ressourcenanbieter und ermöglicht die Erstellung von Media Services-Konten. |
> | Aktion | Microsoft.Media/unregister/action | Hebt die Registrierung des Abonnements für den Media Services-Ressourcenanbieter auf. |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Migrate/locations/assessmentOptions/read | Ruft die Bewertungsoptionen ab, die am angegebenen Standort verfügbar sind. |
> | Aktion | Microsoft.Migrate/locations/checknameavailability/action | Überprüft die Verfügbarkeit des Ressourcennamens für das angegebene Abonnement am angegebenen Standort. |
> | Aktion | Microsoft.Migrate/migrateprojects/read | Ruft die Eigenschaften eines Migrationsprojekts ab. |
> | Aktion | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Ruft die Lösungskonfiguration für ein Migrationsprojekt ab. |
> | Aktion | Microsoft.Migrate/migrateprojects/solutions/read | Ruft die Eigenschaften einer Lösung für Migrationsprojekte ab. |
> | Aktion | Microsoft.Migrate/Operations/read | Listet die verfügbaren Vorgänge für den Microsoft.Migrate-Ressourcenanbieter auf. |
> | Aktion | Microsoft.Migrate/projects/assessments/read | Listet die Bewertungen innerhalb eines Projekts auf. |
> | Aktion | Microsoft.Migrate/projects/delete | Löscht das Projekt. |
> | Aktion | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Ruft die Eigenschaften eines bewerteten Computers ab. |
> | Aktion | Microsoft.Migrate/projects/groups/assessments/delete | Löscht eine Bewertung. |
> | Aktion | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Lädt die URL für einen Bewertungsbericht herunter. |
> | Aktion | Microsoft.Migrate/projects/groups/assessments/read | Ruft die Eigenschaften einer Bewertung ab. |
> | Aktion | Microsoft.Migrate/projects/groups/assessments/write | Erstellt eine neue Bewertung oder aktualisiert eine vorhandene Bewertung. |
> | Aktion | Microsoft.Migrate/projects/groups/delete | Löscht eine Gruppe. |
> | Aktion | Microsoft.Migrate/projects/groups/read | Ruft die Eigenschaften einer Gruppe ab. |
> | Aktion | Microsoft.Migrate/projects/groups/write | Erstellt eine neue Gruppe oder aktualisiert eine vorhandene Gruppe. |
> | Aktion | Microsoft.Migrate/projects/keys/action | Ruft gemeinsam verwendete Schlüssel für das Projekt ab. |
> | Aktion | Microsoft.Migrate/projects/machines/read | Ruft die Eigenschaften eines Computers ab. |
> | Aktion | Microsoft.Migrate/projects/read | Ruft die Eigenschaften eines Projekts ab. |
> | Aktion | Microsoft.Migrate/projects/write | Erstellt ein neues Projekt oder aktualisiert ein vorhandenes Projekt. |
> | Aktion | Microsoft.Migrate/register/action | Registriert ein Abonnement bei einem Microsoft.Migrate-Ressourcenanbieter. |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.MixedReality/register/action | Registriert ein Abonnement für den Mixed Reality-Ressourcenanbieter. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Hiermit werden Raumanker erstellt. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Hiermit werden Raumanker gelöscht. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Hiermit werden Raumanker in räumlicher Nähe ermittelt. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Hiermit werden die Eigenschaften von Raumankern abgerufen. |
> | Aktion | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für Microsoft.MixedReality/SpatialAnchorsAccounts ab. |
> | Aktion | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für Microsoft.MixedReality/SpatialAnchorsAccounts. |
> | Aktion | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für Microsoft.MixedReality/SpatialAnchorsAccounts ab. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Hiermit finden Sie Raumanker. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Hiermit übermitteln Sie Diagnosedaten, um die Qualität des Azure Spatial Anchors-Diensts zu verbessern. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Hiermit aktualisieren Sie die Eigenschaften von Raumankern. |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.NetApp/locations/operationresults/read | Liest eine Vorgangsergebnisressource. |
> | Aktion | Microsoft.NetApp/locations/read | Liest eine Verfügbarkeitsüberprüfungsressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Löscht eine Poolressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/capacityPools/read | Liest eine Poolressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Löscht eine Volumeressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Liest eine Einbindungszielressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Liest eine Volumeressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Löscht eine Momentaufnahmeressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Liest eine Momentaufnahmeressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Schreibt eine Momentaufnahmeressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Schreibt eine Volumeressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/capacityPools/write | Schreibt eine Poolressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/delete | Löscht eine Kontoressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/read | Liest eine Kontoressource. |
> | Aktion | Microsoft.NetApp/netAppAccounts/write | Schreibt eine Kontoressource. |
> | Aktion | Microsoft.NetApp/Operations/read | Liest eine Vorgangsressource. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Ruft für Application Gateway verfügbare Anforderungsheader ab. |
> | Aktion | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Ruft für Application Gateway verfügbare Antwortheader ab. |
> | Aktion | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Ruft für Application Gateway verfügbare Servervariablen ab. |
> | Aktion | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Vordefinierte SSL-Richtlinie für Application Gateway |
> | Aktion | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Verfügbare SSL-Optionen für Application Gateway |
> | Aktion | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Ruft verfügbare WAF-Regelsätze für Application Gateway ab. |
> | Aktion | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Verknüpft einen Back-End-Adresspool für ein Application Gateway. Nicht warnbar. |
> | Aktion | Microsoft.Network/applicationGateways/backendhealth/action | Ruft die Back-End-Integrität eines Anwendungsgateways ab. |
> | Aktion | Microsoft.Network/applicationGateways/delete | Löscht ein Anwendungsgateway. |
> | Aktion | Microsoft.Network/applicationGateways/read | Ruft ein Anwendungsgateway ab. |
> | Aktion | Microsoft.Network/applicationGateways/start/action | Startet ein Anwendungsgateway. |
> | Aktion | Microsoft.Network/applicationGateways/stop/action | Beendet ein Anwendungsgateway. |
> | Aktion | Microsoft.Network/applicationGateways/write | Erstellt ein Anwendungsgateway oder aktualisiert ein vorhandenes Anwendungsgateway. |
> | Aktion | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Löscht eine WAF-Richtlinie für Application Gateways. |
> | Aktion | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Ruft eine WAF-Richtlinie für Application Gateways ab. |
> | Aktion | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Erstellt oder aktualisiert eine WAF-Richtlinie für Application Gateways. |
> | Aktion | Microsoft.Network/applicationSecurityGroups/delete | Löscht eine Anwendungssicherheitsgruppe. |
> | Aktion | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Dient zum Verknüpfen der IP-Konfiguration mit Anwendungssicherheitsgruppen. Nicht warnbar. |
> | Aktion | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Dient zum Verknüpfen der Sicherheitsregel mit Anwendungssicherheitsgruppen. Nicht warnbar. |
> | Aktion | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Listet IP-Konfigurationen in der ApplicationSecurityGroup auf. |
> | Aktion | Microsoft.Network/applicationSecurityGroups/read | Ruft eine Anwendungssicherheitsgruppen-ID ab. |
> | Aktion | Microsoft.Network/applicationSecurityGroups/write | Erstellt eine Anwendungssicherheitsgruppe oder aktualisiert eine vorhandene Anwendungssicherheitsgruppe. |
> | Aktion | Microsoft.Network/azureFirewallFqdnTags/read | Ruft die Azure Firewall-FQDN-Tags ab. |
> | Aktion | Microsoft.Network/azurefirewalls/delete | Hiermit löschen Sie Azure Firewall. |
> | Aktion | Microsoft.Network/azurefirewalls/read | Hiermit rufen Sie Azure Firewall ab. |
> | Aktion | Microsoft.Network/azurefirewalls/write | Erstellt oder aktualisiert eine Azure Firewall. |
> | Aktion | Microsoft.Network/bastionHosts/delete | Löscht einen Bastionhost. |
> | Aktion | Microsoft.Network/bastionHosts/read | Ruft einen Bastionhost ab. |
> | Aktion | Microsoft.Network/bastionHosts/write | Erstellt oder aktualisiert einen Bastionhost. |
> | Aktion | Microsoft.Network/bgpServiceCommunities/read | Dient zum Abrufen von BGP Service Communities. |
> | Aktion | Microsoft.Network/checkFrontDoorNameAvailability/action | Überprüft, ob ein Front Door-Name verfügbar ist. |
> | Aktion | Microsoft.Network/checkTrafficManagerNameAvailability/action | Prüft die Verfügbarkeit eines relativen Traffic Manager-DNS-Namens. |
> | Aktion | Microsoft.Network/connections/delete | Löscht VirtualNetworkGatewayConnection-Elemente. |
> | Aktion | Microsoft.Network/connections/read | Ruft VirtualNetworkGatewayConnection-Elemente ab. |
> | Aktion | Microsoft.Network/connections/revoke/action | Legt den ExpressRoute-Verbindungsstatus auf „Widerrufen“ fest. |
> | Aktion | Microsoft.Network/connections/sharedkey/action | Ruft SharedKey-Elemente für VirtualNetworkGatewayConnection ab. |
> | Aktion | Microsoft.Network/connections/sharedKey/read | Ruft SharedKey-Elemente für „VirtualNetworkGatewayConnection“ ab. |
> | Aktion | Microsoft.Network/connections/sharedKey/write | Erstellt ein SharedKey-Element für „VirtualNetworkGatewayConnection“ oder aktualisiert ein vorhandenes SharedKey-Element für „VirtualNetworkGatewayConnection“. |
> | Aktion | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Ruft die VPN-Gerätekonfiguration von VirtualNetworkGatewayConnection ab. |
> | Aktion | Microsoft.Network/connections/write | Erstellt ein VirtualNetworkGatewayConnection-Element oder aktualisiert ein vorhandenes VirtualNetworkGatewayConnection-Element. |
> | Aktion | Microsoft.Network/ddosCustomPolicies/delete | Löscht eine DDoS-angepasste Richtlinie |
> | Aktion | Microsoft.Network/ddosCustomPolicies/read | Ruft einen DDoS-angepasste Richtliniendefinition ab. |
> | Aktion | Microsoft.Network/ddosCustomPolicies/write | Erstellt eine DDoS-angepasste Richtlinie oder aktualisiert eine vorhandene DDoS-angepasste Richtlinie. |
> | Aktion | Microsoft.Network/ddosProtectionPlans/delete | Löscht einen DDoS Protection-Plan. |
> | Aktion | Microsoft.Network/ddosProtectionPlans/join/action | Dient zum Verknüpfen eines DDoS Protection-Plans. Nicht warnbar. |
> | Aktion | Microsoft.Network/ddosProtectionPlans/read | Dient zum Aufrufen eines DDoS Protection-Plans. |
> | Aktion | Microsoft.Network/ddosProtectionPlans/write | Erstellt oder aktualisiert einen DDoS Protection-Plan.  |
> | Aktion | Microsoft.Network/dnsoperationresults/read | Ruft Ergebnisse eines DNS-Vorgangs ab. |
> | Aktion | Microsoft.Network/dnsoperationstatuses/read | Ruft den Status eines DNS-Vorgangs ab.  |
> | Aktion | Microsoft.Network/dnszones/A/delete | Dient zum Entfernen des Ressourceneintragssatzes mit einem bestimmten Namen und dem Typ „A“ aus einer DNS-Zone. |
> | Aktion | Microsoft.Network/dnszones/A/read | Dient zum Abrufen des Ressourceneintragssatzes vom Typ „A“ im JSON-Format. Der Ressourceneintragssatz enthält eine Liste mit Einträgen sowie Gültigkeitsdauer, Tags und ETag. |
> | Aktion | Microsoft.Network/dnszones/A/write | Dient zum Erstellen oder Aktualisieren eines Ressourceneintragssatzes vom Typ „A“ innerhalb einer DNS-Zone. Die aktuellen Ressourceneinträge des Ressourceneintragssatzes werden durch die angegebenen Einträge ersetzt. |
> | Aktion | Microsoft.Network/dnszones/AAAA/delete | Dient zum Entfernen des Ressourceneintragssatzes mit einem bestimmten Namen und dem Typ „AAAA“ aus einer DNS-Zone. |
> | Aktion | Microsoft.Network/dnszones/AAAA/read | Dient zum Abrufen des Ressourceneintragssatzes vom Typ „AAAA“ im JSON-Format. Der Ressourceneintragssatz enthält eine Liste mit Einträgen sowie Gültigkeitsdauer, Tags und ETag. |
> | Aktion | Microsoft.Network/dnszones/AAAA/write | Dient zum Erstellen oder Aktualisieren eines Ressourceneintragssatzes vom Typ „AAAA“ innerhalb einer DNS-Zone. Die aktuellen Ressourceneinträge des Ressourceneintragssatzes werden durch die angegebenen Einträge ersetzt. |
> | Aktion | Microsoft.Network/dnszones/all/read | Ruft DNS-Ressourceneintragssätze typenübergreifend ab. |
> | Aktion | Microsoft.Network/dnszones/CAA/delete | Dient zum Entfernen der Datensatzgruppe mit einem bestimmten Namen und dem Typ „CAA“ aus einer DNS-Zone. |
> | Aktion | Microsoft.Network/dnszones/CAA/read | Dient zum Abrufen der Datensatzgruppe vom Typ „CAA“ im JSON-Format. Der Ressourceneintragssatz enthält Gültigkeitsdauer, Tags und ETag. |
> | Aktion | Microsoft.Network/dnszones/CAA/write | Dient zum Erstellen oder Aktualisieren einer Datensatzgruppe vom Typ „CAA“ innerhalb einer DNS-Zone. Die aktuellen Ressourceneinträge des Ressourceneintragssatzes werden durch die angegebenen Einträge ersetzt. |
> | Aktion | Microsoft.Network/dnszones/CNAME/delete | Dient zum Entfernen des Ressourceneintragssatzes mit einem bestimmten Namen und dem Typ „CNAME“ aus einer DNS-Zone. |
> | Aktion | Microsoft.Network/dnszones/CNAME/read | Dient zum Abrufen des Ressourceneintragssatzes vom Typ „CNAME“ im JSON-Format. Der Ressourceneintragssatz enthält Gültigkeitsdauer, Tags und ETag. |
> | Aktion | Microsoft.Network/dnszones/CNAME/write | Dient zum Erstellen oder Aktualisieren eines Ressourceneintragssatzes vom Typ „CNAME“ innerhalb einer DNS-Zone. Die aktuellen Ressourceneinträge des Ressourceneintragssatzes werden durch die angegebenen Einträge ersetzt. |
> | Aktion | Microsoft.Network/dnszones/delete | Dient zum Löschen der DNS-Zone im JSON-Format. Zu den Zoneneigenschaften zählen „tags“, „etag“, „numberOfRecordSets“ und „maxNumberOfRecordSets“. |
> | Aktion | Microsoft.Network/dnszones/MX/delete | Dient zum Entfernen des Ressourceneintragssatzes mit einem bestimmten Namen und dem Typ „MX“ aus einer DNS-Zone. |
> | Aktion | Microsoft.Network/dnszones/MX/read | Dient zum Abrufen des Ressourceneintragssatzes vom Typ „MX“ im JSON-Format. Der Ressourceneintragssatz enthält eine Liste mit Einträgen sowie Gültigkeitsdauer, Tags und ETag. |
> | Aktion | Microsoft.Network/dnszones/MX/write | Dient zum Erstellen oder Aktualisieren eines Ressourceneintragssatzes vom Typ „MX“ innerhalb einer DNS-Zone. Die aktuellen Ressourceneinträge des Ressourceneintragssatzes werden durch die angegebenen Einträge ersetzt. |
> | Aktion | Microsoft.Network/dnszones/NS/delete | Löscht den DNS-Ressourceneintragssatz vom Typ „NS“. |
> | Aktion | Microsoft.Network/dnszones/NS/read | Ruft DNS-Ressourceneintragssätze vom Typ „NS“ ab. |
> | Aktion | Microsoft.Network/dnszones/NS/write | Erstellt oder aktualisiert DNS-Ressourceneintragssätze vom Typ „NS“. |
> | Aktion | Microsoft.Network/dnszones/PTR/delete | Dient zum Entfernen des Ressourceneintragssatzes mit einem bestimmten Namen und dem Typ „PTR“ aus einer DNS-Zone. |
> | Aktion | Microsoft.Network/dnszones/PTR/read | Dient zum Abrufen des Ressourceneintragssatzes vom Typ „PTR“ im JSON-Format. Der Ressourceneintragssatz enthält eine Liste mit Einträgen sowie Gültigkeitsdauer, Tags und ETag. |
> | Aktion | Microsoft.Network/dnszones/PTR/write | Dient zum Erstellen oder Aktualisieren eines Ressourceneintragssatzes vom Typ „PTR“ innerhalb einer DNS-Zone. Die aktuellen Ressourceneinträge des Ressourceneintragssatzes werden durch die angegebenen Einträge ersetzt. |
> | Aktion | Microsoft.Network/dnszones/read | Dient zum Abrufen der DNS-Zone im JSON-Format. Zu den Zoneneigenschaften zählen „tags“, „etag“, „numberOfRecordSets“ und „maxNumberOfRecordSets“. Die in der Zone enthaltenen Ressourceneintragssätze werden durch diesen Befehl nicht abgerufen. |
> | Aktion | Microsoft.Network/dnszones/recordsets/read | Ruft DNS-Ressourceneintragssätze typenübergreifend ab. |
> | Aktion | Microsoft.Network/dnszones/SOA/read | Ruft DNS-Ressourceneintragssätze vom Typ „SOA“ ab. |
> | Aktion | Microsoft.Network/dnszones/SOA/write | Erstellt oder aktualisiert DNS-Ressourceneintragssätze vom Typ „SOA“. |
> | Aktion | Microsoft.Network/dnszones/SRV/delete | Dient zum Entfernen des Ressourceneintragssatzes mit einem bestimmten Namen und dem Typ „SRV“ aus einer DNS-Zone. |
> | Aktion | Microsoft.Network/dnszones/SRV/read | Dient zum Abrufen des Ressourceneintragssatzes vom Typ „SRV“ im JSON-Format. Der Ressourceneintragssatz enthält eine Liste mit Einträgen sowie Gültigkeitsdauer, Tags und ETag. |
> | Aktion | Microsoft.Network/dnszones/SRV/write | Dient zum Erstellen oder Aktualisieren von Ressourceneintragssätzen vom Typ „SRV“. |
> | Aktion | Microsoft.Network/dnszones/TXT/delete | Dient zum Entfernen des Ressourceneintragssatzes mit einem bestimmten Namen und dem Typ „TXT“ aus einer DNS-Zone. |
> | Aktion | Microsoft.Network/dnszones/TXT/read | Dient zum Abrufen des Ressourceneintragssatzes vom Typ „TXT“ im JSON-Format. Der Ressourceneintragssatz enthält eine Liste mit Einträgen sowie Gültigkeitsdauer, Tags und ETag. |
> | Aktion | Microsoft.Network/dnszones/TXT/write | Dient zum Erstellen oder Aktualisieren eines Ressourceneintragssatzes vom Typ „TXT“ innerhalb einer DNS-Zone. Die aktuellen Ressourceneinträge des Ressourceneintragssatzes werden durch die angegebenen Einträge ersetzt. |
> | Aktion | Microsoft.Network/dnszones/write | Dient zum Erstellen oder Aktualisieren einer DNS-Zone innerhalb einer Ressourcengruppe.  Wird zum Aktualisieren der Tags für eine DNS-Zonenressource verwendet. Mit diesem Befehl können keine Ressourceneintragssätze innerhalb der Zone erstellt oder aktualisiert werden. |
> | Aktion | Microsoft.Network/expressRouteCircuits/authorizations/delete | Löscht eine ExpressRouteCircuit-Autorisierung. |
> | Aktion | Microsoft.Network/expressRouteCircuits/authorizations/read | Ruft eine ExpressRouteCircuit-Autorisierung ab. |
> | Aktion | Microsoft.Network/expressRouteCircuits/authorizations/write | Erstellt eine ExpressRouteCircuit-Autorisierung oder aktualisiert eine vorhandene ExpressRouteCircuit-Autorisierung. |
> | Aktion | Microsoft.Network/expressRouteCircuits/delete | Löscht ein ExpressRouteCircuit-Element. |
> | Aktion | Microsoft.Network/expressRouteCircuits/join/action | Verknüpft eine ExpressRoute-Verbindung. Nicht warnbar. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Ruft ein ArpTable-Element für ein ExpressRouteCircuit-Peering ab. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Löscht eine ExpressRouteCircuit-Verbindung. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Ruft eine ExpressRouteCircuit-Verbindung ab. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Erstellt eine ExpressRouteCircuit-Verbindungsressource oder aktualisiert eine vorhandene ExpressRouteCircuit-Verbindungsressource. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/delete | Löscht ein ExpressRouteCircuit-Peering. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Ruft eine Peerverbindung für eine ExpressRoute-Leitung ab. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/read | Ruft ein ExpressRouteCircuit-Peering ab. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Ruft ein RouteTable-Element für ein ExpressRouteCircuit-Peering ab. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Ruft eine RouteTable-Zusammenfassung für ein ExpressRouteCircuit-Peering ab. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Ruft eine ExpressRouteCircuit-Peeringstatistik ab. |
> | Aktion | Microsoft.Network/expressRouteCircuits/peerings/write | Erstellt ein ExpressRouteCircuit-Peering oder aktualisiert ein vorhandenes ExpressRouteCircuit-Peering. |
> | Aktion | Microsoft.Network/expressRouteCircuits/read | Dient zum Abrufen eines ExpressRouteCircuit-Elements. |
> | Aktion | Microsoft.Network/expressRouteCircuits/stats/read | Ruft eine ExpressRouteCircuit-Statistik ab. |
> | Aktion | Microsoft.Network/expressRouteCircuits/write | Erstellt ein ExpressRouteCircuit-Element oder aktualisiert ein vorhandenes ExpressRouteCircuit-Element. |
> | Aktion | Microsoft.Network/expressRouteCrossConnections/join/action | Verknüpft eine ExpressRoute-Querverbindung. Nicht warnbar. |
> | Aktion | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Ruft eine ARP-Tabelle zum Peering einer ExpressRoute-Querverbindung ab. |
> | Aktion | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Löscht ein Peering einer ExpressRoute-Querverbindung. |
> | Aktion | Microsoft.Network/expressRouteCrossConnections/peerings/read | Ruft ein Peering einer ExpressRoute-Querverbindung ab. |
> | Aktion | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Ruft eine Routentabelle zum Peering einer ExpressRoute-Querverbindung ab. |
> | Aktion | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Ruft eine Routentabellenzusammenfassung zum Peering einer ExpressRoute-Querverbindung ab. |
> | Aktion | Microsoft.Network/expressRouteCrossConnections/peerings/write | Erstellt ein Peering für eine ExpressRoute-Querverbindung oder aktualisiert ein Peering für eine vorhandene ExpressRoute-Querverbindung. |
> | Aktion | Microsoft.Network/expressRouteCrossConnections/read | Ruft eine ExpressRoute-Querverbindung ab. |
> | Aktion | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Löscht eine ExpressRoute-Verbindung. |
> | Aktion | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Ruft eine ExpressRoute-Verbindung ab. |
> | Aktion | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Erstellt eine ExpressRoute-Verbindung oder aktualisiert eine vorhandene ExpressRoute-Verbindung. |
> | Aktion | Microsoft.Network/expressRouteGateways/join/action | Verknüpft ein ExpressRoute-Gateway. Nicht warnbar. |
> | Aktion | Microsoft.Network/expressRouteGateways/read | Ruft ein ExpressRoute-Gateway ab. |
> | Aktion | Microsoft.Network/expressRoutePorts/delete | Löscht ExpressRoutePorts. |
> | Aktion | Microsoft.Network/expressRoutePorts/join/action | Verknüpft die Express Route-Ports. Nicht warnbar. |
> | Aktion | Microsoft.Network/expressRoutePorts/links/read | Hiermit rufen Sie ExpressRouteLink ab. |
> | Aktion | Microsoft.Network/expressRoutePorts/read | Ruft ExpressRoutePorts ab. |
> | Aktion | Microsoft.Network/expressRoutePorts/write | Erstellt oder aktualisiert ExpressRoutePorts. |
> | Aktion | Microsoft.Network/expressRoutePortsLocations/read | Hiermit rufen Sie Standorte von ExpressRoute-Ports ab. |
> | Aktion | Microsoft.Network/expressRouteServiceProviders/read | Ruft ExpressRoute-Dienstanbieter ab. |
> | Aktion | Microsoft.Network/frontDoors/backendPools/delete | Löscht einen Back-End-Pool. |
> | Aktion | Microsoft.Network/frontDoors/backendPools/read | Ruft einen Back-End-Pool ab. |
> | Aktion | Microsoft.Network/frontDoors/backendPools/read | Erstellt oder aktualisiert einen Back-End-Pool. |
> | Aktion | Microsoft.Network/frontDoors/delete | Löscht eine „Front Door“. |
> | Aktion | Microsoft.Network/frontDoors/frontendEndpoints/delete | Löscht einen Front-End-Endpunkt. |
> | Aktion | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Deaktiviert HTTPS für einen Front-End-Endpunkt. |
> | Aktion | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Aktiviert HTTPS für einen Front-End-Endpunkt. |
> | Aktion | Microsoft.Network/frontDoors/frontendEndpoints/read | Ruft einen Front-End-Endpunkt ab. |
> | Aktion | Microsoft.Network/frontDoors/frontendEndpoints/write | Erstellt oder aktualisiert einen Front-End-Endpunkt. |
> | Aktion | Microsoft.Network/frontDoors/healthProbeSettings/delete | Löscht Einstellungen für die Integritätsüberprüfung. |
> | Aktion | Microsoft.Network/frontDoors/healthProbeSettings/read | Ruft Einstellungen für die Integritätsüberprüfung ab. |
> | Aktion | Microsoft.Network/frontDoors/healthProbeSettings/write | Erstellt oder aktualisiert Einstellungen für die Integritätsüberprüfung. |
> | Aktion | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Erstellt oder aktualisiert Einstellungen für den Lastenausgleich. |
> | Aktion | Microsoft.Network/frontDoors/loadBalancingSettings/read | Ruft Lastenausgleichseinstellungen ab. |
> | Aktion | Microsoft.Network/frontDoors/loadBalancingSettings/write | Erstellt oder aktualisiert Einstellungen für den Lastenausgleich. |
> | Aktion | Microsoft.Network/frontDoors/purge/action | Bereinigt von zwischengespeicherten Inhalt aus einer „Front Door“. |
> | Aktion | Microsoft.Network/frontDoors/read | Ruft eine „Front Door“ ab. |
> | Aktion | Microsoft.Network/frontDoors/routingRules/delete | Löscht eine Routingregel. |
> | Aktion | Microsoft.Network/frontDoors/routingRules/read | Ruft eine Routingregel ab. |
> | Aktion | Microsoft.Network/frontDoors/routingRules/write | Erstellt oder aktualisiert eine Routingregel. |
> | Aktion | Microsoft.Network/frontDoors/validateCustomDomain/action | Überprüft einen Front-End-Endpunkt für eine Front Door. |
> | Aktion | Microsoft.Network/frontDoors/write | Erstellt oder aktualisiert eine Front Door. |
> | Aktion | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Löscht eine Web Application Firewall-Richtlinie. |
> | Aktion | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Ruft eine Web Application Firewall-Richtlinie ab. |
> | Aktion | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Erstellt oder aktualisiert eine Web Application Firewall-Richtlinie. |
> | Aktion | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Verknüpft einen Back-End-Adresspool für den Lastenausgleich. Nicht warnbar. |
> | Aktion | Microsoft.Network/loadBalancers/backendAddressPools/read | Ruft eine Back-End-Adresspooldefinition für den Lastenausgleich ab. |
> | Aktion | Microsoft.Network/loadBalancers/delete | Löscht einen Lastenausgleich. |
> | Aktion | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Verknüpft eine Front-End-IP-Konfiguration für den Lastenausgleich. Nicht warnbar. |
> | Aktion | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Ruft eine Front-End-IP-Konfigurationsdefinition für den Lastenausgleich ab. |
> | Aktion | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Verknüpft einen NAT-Pool für eingehenden Datenverkehr für den Lastenausgleich. Nicht warnbar. |
> | Aktion | Microsoft.Network/loadBalancers/inboundNatPools/read | Ruft eine eingehende NAT-Pooldefinition für den Lastenausgleich ab. |
> | Aktion | Microsoft.Network/loadBalancers/inboundNatRules/delete | Löscht eine eingehende NAT-Regel für den Lastenausgleich. |
> | Aktion | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Verknüpft eine NAT-Regel für eingehenden Datenverkehr für den Lastenausgleich. Nicht warnbar. |
> | Aktion | Microsoft.Network/loadBalancers/inboundNatRules/read | Ruft eine eingehende NAT-Regeldefinition für den Lastenausgleich ab. |
> | Aktion | Microsoft.Network/loadBalancers/inboundNatRules/write | Erstellt eine eingehende NAT-Regel für den Lastenausgleich oder aktualisiert eine vorhandene eingehende NAT-Regel für den Lastenausgleich. |
> | Aktion | Microsoft.Network/loadBalancers/loadBalancingRules/read | Ruft eine Lastenausgleichsregel-Definition für den Lastenausgleich ab. |
> | Aktion | Microsoft.Network/loadBalancers/networkInterfaces/read | Ruft Verweise auf alle Netzwerkschnittstellen unter einem Lastenausgleich ab. |
> | Aktion | Microsoft.Network/loadBalancers/outboundRules/read | Ruft eine ausgehende Regeldefinition für den Lastenausgleich ab. |
> | Aktion | Microsoft.Network/loadBalancers/probes/join/action | Ermöglicht die Verwendung von Prüfpunkten eines Lastenausgleichs. Beispielsweise kann mit dieser Berechtigung die healthProbe-Eigenschaft einer VM-Skalierungsgruppe auf den Prüfpunkt verweisen. Nicht warnbar. |
> | Aktion | Microsoft.Network/loadBalancers/probes/read | Ruft einen Lastenausgleichstest ab. |
> | Aktion | Microsoft.Network/loadBalancers/read | Ruft eine Lastenausgleichsdefinition ab. |
> | Aktion | Microsoft.Network/loadBalancers/virtualMachines/read | Ruft Verweise auf alle virtuellen Computer unter einem Lastenausgleich ab. |
> | Aktion | Microsoft.Network/loadBalancers/write | Erstellt einen Lastenausgleich oder aktualisiert einen vorhandenen Lastenausgleich. |
> | Aktion | Microsoft.Network/localnetworkgateways/delete | Löscht lokale Netzwerkgateways. |
> | Aktion | Microsoft.Network/localnetworkgateways/read | Ruft lokale Netzwerkgateways ab. |
> | Aktion | Microsoft.Network/localnetworkgateways/write | Erstellt ein lokales Netzwerkgateway oder aktualisiert ein vorhandenes lokales Netzwerkgateway. |
> | Aktion | Microsoft.Network/locations/availableDelegations/read | Ruft verfügbare Delegierungen ab. |
> | Aktion | Microsoft.Network/locations/availablePrivateEndpointResources/read | Ruft die verfügbaren privaten Endpunktressourcen ab. |
> | Aktion | Microsoft.Network/locations/bareMetalTenants/action | Führt die Zuordnung oder Überprüfung eines Bare-Metal-Mandanten durch. |
> | Aktion | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Überprüft die Unterstützung für beschleunigte Netzwerke. |
> | Aktion | Microsoft.Network/locations/checkDnsNameAvailability/read | Prüft, ob eine DNS-Bezeichnung am angegebenen Standort verfügbar ist. |
> | Aktion | Microsoft.Network/locations/operationResults/read | Ruft das Ergebnis eines asynchronen POST- oder DELETE-Vorgangs ab. |
> | Aktion | Microsoft.Network/locations/operations/read | Ruft die Vorgangsressource ab, die den Status eines asynchronen Vorgangs darstellt. |
> | Aktion | Microsoft.Network/locations/serviceTags/read | Ruft Diensttags ab |
> | Aktion | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Ruft die unterstützten Größen für virtuelle Computer ab. |
> | Aktion | Microsoft.Network/locations/usages/read | Ruft die Ressourcenverwendungsmetriken ab. |
> | Aktion | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Ruft eine Liste der verfügbaren Dienstendpunkte für virtuelle Netzwerke ab. |
> | Aktion | Microsoft.Network/networkIntentPolicies/delete | Löscht eine Netzwerkzielrichtlinie. |
> | Aktion | Microsoft.Network/networkIntentPolicies/read | Ruft die Beschreibung einer Netzwerkzielrichtlinie ab. |
> | Aktion | Microsoft.Network/networkIntentPolicies/write | Erstellt eine Netzwerkzielrichtlinie oder aktualisiert eine vorhandene Netzwerkzielrichtlinie. |
> | Aktion | Microsoft.Network/networkInterfaces/delete | Löscht eine Netzwerkschnittstelle. |
> | Aktion | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Dient zum Abrufen der Netzwerksicherheitsgruppen, die für die Netzwerkschnittstelle des virtuellen Computers konfiguriert sind. |
> | Aktion | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Dient zum Abrufen der Routingtabelle, die für die Netzwerkschnittstelle des virtuellen Computers konfiguriert ist. |
> | Aktion | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Verknüpft eine Netzwerkschnittstellen-IP-Konfiguration. Nicht warnbar. |
> | Aktion | Microsoft.Network/networkInterfaces/ipconfigurations/read | Ruft eine IP-Konfigurationsdefinition für Netzwerkschnittstellen ab.  |
> | Aktion | Microsoft.Network/networkInterfaces/join/action | Verknüpft einen virtuellen Computer mit einer Netzwerkschnittstelle. Nicht warnbar. |
> | Aktion | Microsoft.Network/networkInterfaces/loadBalancers/read | Ruft alle Lastenausgleichsmodule ab, denen die Netzwerkschnittstelle angehört. |
> | Aktion | Microsoft.Network/networkInterfaces/read | Ruft eine Netzwerkschnittstellendefinition ab.  |
> | Aktion | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Löscht eine Netzwerkschnittstellen-TAP-Konfiguration. |
> | Aktion | Microsoft.Network/networkInterfaces/tapConfigurations/read | Ruft eine Netzwerkschnittstellen-TAP-Konfiguration ab. |
> | Aktion | Microsoft.Network/networkInterfaces/tapConfigurations/write | Erstellt eine Netzwerkschnittstellen-TAP-Konfiguration oder aktualisiert eine vorhandene Netzwerkschnittstellen-TAP-Konfiguration. |
> | Aktion | Microsoft.Network/networkInterfaces/write | Erstellt eine Netzwerkschnittstelle oder aktualisiert eine vorhandene Netzwerkschnittstelle.  |
> | Aktion | Microsoft.Network/networkProfiles/delete | Löscht ein Netzwerkprofil. |
> | Aktion | Microsoft.Network/networkProfiles/read | Liest ein Netzwerkprofil. |
> | Aktion | Microsoft.Network/networkProfiles/removeContainers/action | Entfernt Container. |
> | Aktion | Microsoft.Network/networkProfiles/setContainers/action | Legt Container fest. |
> | Aktion | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Legt Netzwerkschnittstellen von Containern fest. |
> | Aktion | Microsoft.Network/networkProfiles/write | Erstellt oder aktualisiert ein Netzwerkprofil. |
> | Aktion | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Ruft eine Standardsicherheitsregel-Definition ab. |
> | Aktion | Microsoft.Network/networkSecurityGroups/delete | Löscht eine Netzwerksicherheitsgruppe. |
> | Aktion | Microsoft.Network/networkSecurityGroups/join/action | Verknüpft eine Netzwerksicherheitsgruppe. Nicht warnbar. |
> | Aktion | Microsoft.Network/networkSecurityGroups/read | Ruft eine Netzwerksicherheitsgruppen-Definition ab. |
> | Aktion | Microsoft.Network/networkSecurityGroups/securityRules/delete | Löscht eine Sicherheitsregel. |
> | Aktion | Microsoft.Network/networkSecurityGroups/securityRules/read | Ruft eine Sicherheitsregeldefinition ab. |
> | Aktion | Microsoft.Network/networkSecurityGroups/securityRules/write | Erstellt eine Sicherheitsregel oder aktualisiert eine vorhandene Sicherheitsregel. |
> | Aktion | Microsoft.Network/networkSecurityGroups/write | Erstellt eine Netzwerksicherheitsgruppe oder aktualisiert eine vorhandene Netzwerksicherheitsgruppe. |
> | Aktion | Microsoft.Network/networkWatchers/availableProvidersList/action | Gibt alle verfügbaren Internetdienstanbieter für eine bestimmte Azure-Region zurück. |
> | Aktion | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Gibt das relative Latenzergebnis für Internetdienstanbieter zwischen einem bestimmten Standort und Azure-Regionen zurück. |
> | Aktion | Microsoft.Network/networkWatchers/configureFlowLog/action | Konfiguriert die Datenflussprotokollierung für eine Zielressource. |
> | Aktion | Microsoft.Network/networkWatchers/connectionMonitors/delete | Löscht einen Verbindungsmonitor. |
> | Aktion | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Fragt die Überwachungskonnektivität zwischen angegebenen Endpunkten ab. |
> | Aktion | Microsoft.Network/networkWatchers/connectionMonitors/read | Ruft Details zum Verbindungsmonitor ab. |
> | Aktion | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Startet die Überwachungskonnektivität zwischen angegebenen Endpunkten. |
> | Aktion | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Startet oder beendet die Überwachungskonnektivität zwischen angegebenen Endpunkten. |
> | Aktion | Microsoft.Network/networkWatchers/connectionMonitors/write | Erstellt einen Verbindungsmonitor. |
> | Aktion | Microsoft.Network/networkWatchers/connectivityCheck/action | Überprüft die Möglichkeit, eine direkte TCP-Verbindung zwischen einem virtuellen Computer und einem angegebenen Endpunkt herzustellen, einschließlich mit anderen VMs oder einem freien Remoteserver. |
> | Aktion | Microsoft.Network/networkWatchers/delete | Löscht eine Network Watcher-Instanz. |
> | Aktion | Microsoft.Network/networkWatchers/ipFlowVerify/action | Gibt zurück, ob das Paket an oder von einem bestimmten Ziel zugelassen oder abgelehnt wird. |
> | Aktion | Microsoft.Network/networkWatchers/lenses/delete | Löscht einen Bereich. |
> | Aktion | Microsoft.Network/networkWatchers/lenses/query/action | Fragt die Überwachung des Netzwerkdatenverkehrs für einen angegebenen Endpunkt ab. |
> | Aktion | Microsoft.Network/networkWatchers/lenses/read | Ruft Bereichsdetails ab. |
> | Aktion | Microsoft.Network/networkWatchers/lenses/start/action | Startet die Überwachung des Netzwerkdatenverkehrs für einen angegebenen Endpunkt. |
> | Aktion | Microsoft.Network/networkWatchers/lenses/stop/action | Beendet die Überwachung des Netzwerkdatenverkehrs für einen angegebenen Endpunkt oder hält diese an. |
> | Aktion | Microsoft.Network/networkWatchers/lenses/write | Erstellt einen Bereich. |
> | Aktion | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Führt eine Diagnose der Netzwerkkonfiguration durch. |
> | Aktion | Microsoft.Network/networkWatchers/nextHop/action | Gibt für ein angegebenes Ziel und eine Ziel-IP-Adresse die Art des nächsten Hops und die IP-Adresse des nächsten Hops zurück. |
> | Aktion | Microsoft.Network/networkWatchers/packetCaptures/delete | Löscht eine Paketerfassung. |
> | Aktion | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Ruft Informationen zu Eigenschaften und zum Status einer Paketerfassungsressource ab. |
> | Aktion | Microsoft.Network/networkWatchers/packetCaptures/read | Dient zum Abrufen der Paketerfassungsdefinition. |
> | Aktion | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Dient zum Beenden der laufenden Paketerfassungssitzung. |
> | Aktion | Microsoft.Network/networkWatchers/packetCaptures/write | Erstellt eine Paketerfassung. |
> | Aktion | Microsoft.Network/networkWatchers/pingMeshes/delete | Löscht ein PingMesh-Element. |
> | Aktion | Microsoft.Network/networkWatchers/pingMeshes/read | Ruft PingMesh-Details ab. |
> | Aktion | Microsoft.Network/networkWatchers/pingMeshes/start/action | Startet PingMesh zwischen angegebenen VMs. |
> | Aktion | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Beendet PingMesh zwischen angegebenen VMs. |
> | Aktion | Microsoft.Network/networkWatchers/pingMeshes/write | Erstellt ein PingMesh-Element. |
> | Aktion | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Führt eine Batchabfrage der Überwachungskonnektivität zwischen angegebenen Endpunkten ab. |
> | Aktion | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Ruft den Status der Datenflussprotokollierung für eine Ressource ab. |
> | Aktion | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Ruft das Ergebnis des zuvor oder momentan ausgeführten Problembehandlungsvorgangs ab. |
> | Aktion | Microsoft.Network/networkWatchers/read | Dient zum Abrufen der Network Watcher-Definition. |
> | Aktion | Microsoft.Network/networkWatchers/securityGroupView/action | Dient zum Anzeigen der konfigurierten und effektiven Netzwerksicherheitsgruppen-Regeln, die auf einen virtuellen Computer angewendet wurden. |
> | Aktion | Microsoft.Network/networkWatchers/topology/action | Ruft eine Netzwerkebenenansicht mit Ressourcen und deren Beziehungen in einer Ressourcengruppe ab. |
> | Aktion | Microsoft.Network/networkWatchers/troubleshoot/action | Startet die Problembehandlung für eine Netzwerkressource in Azure. |
> | Aktion | Microsoft.Network/networkWatchers/write | Erstellt eine Network Watcher-Instanz oder aktualisiert eine vorhandene Network Watcher-Instanz. |
> | Aktion | Microsoft.Network/operations/read | Dient zum Abrufen verfügbarer Vorgänge. |
> | Aktion | Microsoft.Network/p2sVpnGateways/delete | Löscht ein P2SVpnGateway. |
> | Aktion | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generiert ein VPN-Profil für P2SVpnGateway. |
> | Aktion | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Ruft die P2S-VPN-Verbindungsintegrität für P2SVpnGateway ab. |
> | Aktion | Microsoft.Network/p2sVpnGateways/read | Ruft ein P2SVpnGateway ab. |
> | Aktion | Microsoft.Network/p2sVpnGateways/write | Legt ein P2SVpnGateway fest. |
> | Aktion | Microsoft.Network/privateEndpoints/delete | Löscht eine private Endpunktressource. |
> | Aktion | Microsoft.Network/privateEndpoints/read | Ruft eine private Endpunktressource ab. |
> | Aktion | Microsoft.Network/privateEndpoints/write | Erstellt einen neuen privaten Endpunkt oder aktualisiert einen vorhandenen privaten Endpunkt. |
> | Aktion | Microsoft.Network/privateLinkServices/delete | Löscht eine private Verknüpfungsdienst-Ressource. |
> | Aktion | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Löscht eine private Endpunktverbindung. |
> | Aktion | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Ruft die Definition einer privaten Endpunktverbindung ab. |
> | Aktion | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Erstellt eine neue private Endpunktverbindung oder aktualisiert eine vorhandene private Endpunktverbindung. |
> | Aktion | Microsoft.Network/privateLinkServices/read | Ruft eine private Verknüpfungsdienst-Ressource ab. |
> | Aktion | Microsoft.Network/privateLinkServices/write | Erstellt einen neuen privaten Verknüpfungsdienst oder aktualisiert einen vorhandenen privaten Verknüpfungsdienst. |
> | Aktion | Microsoft.Network/publicIPAddresses/delete | Löscht eine öffentliche IP-Adresse. |
> | Aktion | Microsoft.Network/publicIPAddresses/join/action | Verknüpft eine öffentliche IP-Adresse. Nicht warnbar. |
> | Aktion | Microsoft.Network/publicIPAddresses/read | Ruft eine Definition für eine öffentliche IP-Adresse ab. |
> | Aktion | Microsoft.Network/publicIPAddresses/write | Erstellt eine öffentliche IP-Adresse oder aktualisiert eine vorhandene öffentliche IP-Adresse.  |
> | Aktion | Microsoft.Network/publicIPPrefixes/delete | Löscht ein Präfix einer öffentlichen IP-Adresse. |
> | Aktion | Microsoft.Network/publicIPPrefixes/join/action | Verknüpft ein PublicIPPrefix. Nicht warnbar. |
> | Aktion | Microsoft.Network/publicIPPrefixes/read | Ruft eine Definition eines Präfix einer öffentlichen IP-Adresse ab. |
> | Aktion | Microsoft.Network/publicIPPrefixes/write | Erstellt ein Präfix einer öffentlichen IP-Adresse oder aktualisiert ein Präfix einer vorhandenen öffentlichen IP-Adresse. |
> | Aktion | Microsoft.Network/register/action | Registriert das Abonnement. |
> | Aktion | Microsoft.Network/routeFilters/delete | Löscht eine Routenfilterdefinition. |
> | Aktion | Microsoft.Network/routeFilters/join/action | Verknüpft einen Routenfilter. Nicht warnbar. |
> | Aktion | Microsoft.Network/routeFilters/read | Ruft eine Routenfilterdefinition ab. |
> | Aktion | Microsoft.Network/routeFilters/routeFilterRules/delete | Löscht eine Routenfilterregel-Definition. |
> | Aktion | Microsoft.Network/routeFilters/routeFilterRules/read | Ruft eine Routenfilterregel-Definition ab. |
> | Aktion | Microsoft.Network/routeFilters/routeFilterRules/write | Erstellt eine Routenfilterregel oder aktualisiert eine vorhandene Routenfilterregel. |
> | Aktion | Microsoft.Network/routeFilters/write | Erstellt einen Routenfilter oder aktualisiert einen vorhandenen Routenfilter. |
> | Aktion | Microsoft.Network/routeTables/delete | Löscht eine Routingtabellendefinition. |
> | Aktion | Microsoft.Network/routeTables/join/action | Verknüpft eine Routingtabelle. Nicht warnbar. |
> | Aktion | Microsoft.Network/routeTables/read | Ruft eine Routingtabellendefinition ab. |
> | Aktion | Microsoft.Network/routeTables/routes/delete | Löscht eine Routendefinition. |
> | Aktion | Microsoft.Network/routeTables/routes/read | Ruft eine Routendefinition ab. |
> | Aktion | Microsoft.Network/routeTables/routes/write | Erstellt eine Route oder aktualisiert eine vorhandene Route. |
> | Aktion | Microsoft.Network/routeTables/write | Erstellt eine Routingtabelle oder aktualisiert eine vorhandene Routingtabelle. |
> | Aktion | Microsoft.Network/securegateways/delete | Löscht ein sicheres Gateway. |
> | Aktion | Microsoft.Network/securegateways/read | Ruft ein sicheres Gateway ab. |
> | Aktion | Microsoft.Network/securegateways/write | Erstellt oder aktualisiert ein sicheres Gateway. |
> | Aktion | Microsoft.Network/serviceEndpointPolicies/delete | Löscht eine Dienstendpunktrichtlinie. |
> | Aktion | Microsoft.Network/serviceEndpointPolicies/join/action | Verknüpft eine Dienstendpunktrichtlinie. Nicht warnbar. |
> | Aktion | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Verknüpft ein Subnetz mit Dienstendpunktrichtlinien. Nicht warnbar. |
> | Aktion | Microsoft.Network/serviceEndpointPolicies/read | Ruft die Beschreibung einer Dienstendpunktrichtlinie ab. |
> | Aktion | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Löscht die Definition einer Dienstendpunktrichtlinie. |
> | Aktion | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Ruft die Beschreibung für die Definition einer Dienstendpunktrichtlinie ab. |
> | Aktion | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Erstellt eine Definition für eine Dienstendpunktrichtlinie oder aktualisiert eine vorhandene Definition für eine Dienstendpunktrichtlinie. |
> | Aktion | Microsoft.Network/serviceEndpointPolicies/write | Erstellt eine Dienstendpunktrichtlinie oder aktualisiert eine vorhandene Dienstendpunktrichtlinie. |
> | Aktion | Microsoft.Network/trafficManagerGeographicHierarchies/read | Ruft die geografische Traffic Manager-Hierarchie mit Regionen ab, die mit der geografischen Routingmethode für Datenverkehr verwendet werden können. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Löscht einen Azure-Endpunkt aus einem vorhandenen Traffic Manager-Profil an. Der Traffic Manager beendet die Weiterleitung des Datenverkehrs an den gelöschten Azure-Endpunkt. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Ruft einen Azure-Endpunkt ab, der einem Traffic Manager-Profil zugehörig ist, einschließlich aller Eigenschaften dieses Azure-Endpunkts. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Fügt einen neuen Azure-Endpunkt in einem vorhandenen Traffic Manager-Profil hinzu oder aktualisiert die Eigenschaften eines vorhandenen Azure-Endpunkts in diesem Traffic Manager-Profil. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/delete | Dient zum Löschen des Traffic Manager-Profils. Alle dem Traffic Manager-Profil zugeordneten Einstellungen gehen verloren, und das Profil kann nicht mehr zum Routen von Datenverkehr verwendet werden. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Löscht einen externen Endpunkt aus einem vorhandenen Traffic Manager-Profil. Der Traffic Manager beendet die Weiterleitung des Datenverkehrs an den gelöschten externen Endpunkt. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Ruft einen externen Endpunkt ab, der einem Traffic Manager-Profil zugehörig ist, einschließlich aller Eigenschaften dieses externen Endpunkts. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Fügt einen neuen externen Endpunkt in einem vorhandenen Traffic Manager-Profil hinzu oder aktualisiert die Eigenschaften eines vorhandenen externen Endpunkts in diesem Traffic Manager-Profil. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Ruft die Traffic Manager-Heatmap für das angegebene Traffic Manager-Profil ab, das die Anzahl der Abfragen und Latenzdaten nach Standort und Quell-IP enthält. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Löscht einen geschachtelten Endpunkt aus einem vorhandenen Traffic Manager-Profil. Der Traffic Manager beendet die Weiterleitung des Datenverkehrs an den gelöschten geschachtelten Endpunkt. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Ruft einen geschachtelten Endpunkt ab, der einem Traffic Manager-Profil zugehörig ist, einschließlich aller Eigenschaften dieses geschachtelten Endpunkts. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Fügt einen neuen geschachtelten Endpunkt in einem vorhandenen Traffic Manager-Profil hinzu oder aktualisiert die Eigenschaften eines vorhandenen geschachtelten Endpunkts in diesem Traffic Manager-Profil. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/read | Dient zum Abrufen der Traffic Manager-Profilkonfiguration. Dazu zählen DNS-Einstellungen, Routingeinstellungen für Datenverkehr, Endpunktüberwachungseinstellungen und die Liste mit den Endpunkten, die durch dieses Traffic Manager-Profil geroutet werden. |
> | Aktion | Microsoft.Network/trafficManagerProfiles/write | Dient zum Erstellen eines Traffic Manager-Profils oder zum Ändern der Konfiguration eines vorhandenen Traffic Manager-Profils.<br>Dies schließt das Aktivieren oder Deaktivieren eines Profils sowie das Ändern von DNS-Einstellungen, Routingeinstellungen für Datenverkehr oder Endpunktüberwachungseinstellungen ein.<br>Durch das Traffic Manager-Profil geroutete Endpunkte können hinzugefügt, entfernt, aktiviert oder deaktiviert werden. |
> | Aktion | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Löscht die Schlüssel auf Abonnementebene, die für die Collection für Echtzeitbenutzermetriken verwendet wird. |
> | Aktion | Microsoft.Network/trafficManagerUserMetricsKeys/read | Ruft die Schlüssel auf Abonnementebene für die Collection mit Echtzeitbenutzermetriken ab. |
> | Aktion | Microsoft.Network/trafficManagerUserMetricsKeys/write | Erstellt einen neuen Schlüssel auf Abonnementebene, der für die Collection mit Echtzeitbenutzermetriken verwendet werden soll. |
> | Aktion | Microsoft.Network/unregister/action | Hebt die Registrierung des Abonnements auf. |
> | Aktion | Microsoft.Network/virtualHubs/delete | Löscht einen virtuellen Hub. |
> | Aktion | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Löscht HubVirtualNetworkConnection. |
> | Aktion | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Ruft HubVirtualNetworkConnection ab. |
> | Aktion | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Erstellt oder aktualisiert HubVirtualNetworkConnection. |
> | Aktion | Microsoft.Network/virtualHubs/read | Ruft einen virtuellen Hub ab. |
> | Aktion | Microsoft.Network/virtualHubs/write | Erstellt oder aktualisiert einen virtuellen Hub. |
> | Aktion | microsoft.network/virtualnetworkgateways/connections/read | Ruft VirtualNetworkGatewayConnection ab. |
> | Aktion | Microsoft.Network/virtualNetworkGateways/delete | Löscht virtualNetworkGateway. |
> | Aktion | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Generiert ein VpnClient-Paket für virtualNetworkGateway. |
> | Aktion | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Generiert ein VpnProfile-Paket für VirtualNetworkGateway. |
> | Aktion | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Ruft angekündigte virtualNetworkGateway-Routen ab. |
> | Aktion | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Ruft den Status des BGP-Peerings für virtualNetworkGateway ab. |
> | Aktion | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Ruft die erlernten virtualNetworkGateway-Routen ab. |
> | Aktion | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Ruft für VirtualNetworkGateway die Integrität der VPN-Clientverbindung ab. |
> | Aktion | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Ruft die Vpnclient-IPsec-Parameter für den VirtualNetworkGateway P2S-Client ab. |
> | Aktion | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Ruft die URL eines vorab generierten VPN-Clientprofilpakets ab. |
> | Aktion | Microsoft.Network/virtualNetworkGateways/read | Ruft VirtualNetworkGateway ab. |
> | Aktion | microsoft.network/virtualnetworkgateways/reset/action | Setzt virtualNetworkGateway zurück. |
> | Aktion | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Setzt den gemeinsam verwendeten Vpnclient-Schlüssel für den VirtualNetworkGateway P2S-Client zurück. |
> | Aktion | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Legt die Vpnclient-IPsec-Parameter für den VirtualNetworkGateway P2S-Client fest. |
> | Aktion | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Listet die unterstützten VPN-Geräte auf. |
> | Aktion | Microsoft.Network/virtualNetworkGateways/write | Erstellt oder aktualisiert VirtualNetworkGateway. |
> | Aktion | Microsoft.Network/virtualNetworks/BastionHosts/action | Ruft Bastionhostverweise in einem virtuellen Netzwerk ab. |
> | Aktion | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Ruft Bastionhostverweise in einem virtuellen Netzwerk ab. |
> | Aktion | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Prüft, ob eine IP-Adresse im angegebenen virtuellen Netzwerk verfügbar ist. |
> | Aktion | Microsoft.Network/virtualNetworks/delete | Löscht ein virtuelles Netzwerk. |
> | Aktion | Microsoft.Network/virtualNetworks/peer/action | Richtet ein virtuelles Netzwerk als Peer für ein anderes virtuelles Netzwerk ein. |
> | Aktion | Microsoft.Network/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | Aktion | Microsoft.Network/virtualNetworks/subnets/delete | Löscht ein Subnetz für virtuelle Netzwerke. |
> | Aktion | Microsoft.Network/virtualNetworks/subnets/join/action | Verknüpft ein virtuelles Netzwerk. Nicht warnbar. |
> | Aktion | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Verknüpft Ressourcen wie etwa ein Speicherkonto oder eine SQL-Datenbank mit einem Subnetz. Nicht warnbar. |
> | Aktion | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Bereitet ein Subnetz vor, indem erforderliche Netzwerkrichtlinien angewendet werden. |
> | Aktion | Microsoft.Network/virtualNetworks/subnets/read | Ruft eine Subnetzdefinition für virtuelle Netzwerke ab. |
> | Aktion | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Ruft Verweise auf alle virtuellen Computer im Subnetz eines virtuellen Netzwerks ab. |
> | Aktion | Microsoft.Network/virtualNetworks/subnets/write | Erstellt ein Subnetz für virtuelle Netzwerke oder aktualisiert ein vorhandenes Subnetz für virtuelle Netzwerke. |
> | Aktion | Microsoft.Network/virtualNetworks/usages/read | Ruft die IP-Adressenbelegungen für jedes Subnetz des virtuellen Netzwerks ab. |
> | Aktion | Microsoft.Network/virtualNetworks/virtualMachines/read | Ruft Verweise auf alle virtuellen Computer in einem virtuellen Netzwerk ab. |
> | Aktion | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Löscht ein Peering virtueller Netzwerke. |
> | Aktion | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Ruft eine Definition für das Peering virtueller Netzwerke ab. |
> | Aktion | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Erstellt ein Peering virtueller Netzwerke oder aktualisiert ein vorhandenes Peering virtueller Netzwerke. |
> | Aktion | Microsoft.Network/virtualNetworks/write | Erstellt ein virtuelles Netzwerk oder aktualisiert ein vorhandenes virtuelles Netzwerk. |
> | Aktion | Microsoft.Network/virtualNetworkTaps/delete | Löscht einen TAP eines virtuellen Netzwerks. |
> | Aktion | Microsoft.Network/virtualNetworkTaps/join/action | Verknüpft einen TAP für virtuelle Netzwerke. Nicht warnbar. |
> | Aktion | Microsoft.Network/virtualNetworkTaps/read | Ruft einen TAP eines virtuellen Netzwerks ab. |
> | Aktion | Microsoft.Network/virtualNetworkTaps/write | Erstellt oder aktualisiert einen TAP eines virtuellen Netzwerks. |
> | Aktion | Microsoft.Network/virtualWans/delete | Löscht ein virtuelles WAN. |
> | Aktion | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Löscht P2SVpnServerConfiguration für ein virtuelles WAN. |
> | Aktion | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Ruft P2SVpnServerConfiguration für ein virtuelles WAN ab. |
> | Aktion | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Erstellt oder aktualisiert P2SVpnServerConfiguration für ein virtuelles WAN. |
> | Aktion | Microsoft.Network/virtualWans/read | Ruft ein virtuelles WAN ab. |
> | Aktion | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Ruft unterstützte VirtualWan-Sicherheitsanbieter ab. |
> | Aktion | Microsoft.Network/virtualWans/virtualHubs/read | Ruft alle virtuellen Hubs ab, die auf ein virtuelles WAN verweisen. |
> | Aktion | Microsoft.Network/virtualwans/vpnconfiguration/action | Ruft eine VPN-Konfiguration ab. |
> | Aktion | Microsoft.Network/virtualWans/vpnSites/read | Ruft alle VPN-Standorte ab, die auf ein virtuelles WAN verweisen. |
> | Aktion | Microsoft.Network/virtualWans/write | Erstellt oder aktualisiert ein virtuelles WAN. |
> | Aktion | Microsoft.Network/vpnGateways/delete | Löscht VpnGateway. |
> | Aktion | microsoft.network/vpngateways/listvpnconnectionshealth/action | Ruft die Verbindungsintegrität für alle oder eine Teilmenge der Verbindungen auf einem VpnGateway ab. |
> | Aktion | Microsoft.Network/vpnGateways/read | Ruft VpnGateway ab. |
> | Aktion | microsoft.network/vpngateways/reset/action | Setzt VpnGateway zurück. |
> | Aktion | microsoft.network/vpnGateways/vpnConnections/delete | Löscht VpnConnection. |
> | Aktion | microsoft.network/vpnGateways/vpnConnections/read | Ruft VpnConnection ab. |
> | Aktion | microsoft.network/vpnGateways/vpnConnections/write | Legt VpnConnection fest. |
> | Aktion | Microsoft.Network/vpnGateways/write | Legt VpnGateway fest. |
> | Aktion | Microsoft.Network/vpnsites/delete | Löscht eine Ressource für einen VPN-Standort. |
> | Aktion | Microsoft.Network/vpnsites/read | Ruft eine Ressource für einen VPN-Standort ab. |
> | Aktion | Microsoft.Network/vpnsites/write | Erstellt oder aktualisiert eine Ressource für einen VPN-Standort. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Prüft, ob ein bestimmter Name einer Namespaceressource innerhalb des NotificationHub-Diensts verfügbar ist. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Dient zum Abrufen der Liste mit Beschreibungen für Namespace-Autorisierungsregeln. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Dient zum Löschen von Namespace-Autorisierungsregeln. Die standardmäßige Namespace-Autorisierungsregel kann nicht gelöscht werden.  |
> | Aktion | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Dient zum Abrufen der Verbindungszeichenfolge für den Namespace. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Dient zum Abrufen der Liste mit Beschreibungen für Namespace-Autorisierungsregeln. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Namespace-Autorisierungsregel zum erneuten Generieren des primären/sekundären Schlüssels. Geben Sie den Schlüssel an, der neu generiert werden soll. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Dient zum Erstellen von Autorisierungsregeln auf Namespaceebene und zum Aktualisieren der dazugehörigen Eigenschaften. Aktualisiert werden können die Zugriffsrechte für Autorisierungsregeln sowie der primäre und der sekundäre Schlüssel. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Prüft, ob ein bestimmter NotificationHub-Name innerhalb eines Namespaces verfügbar ist. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/Delete | Dient zum Löschen von Namespaceressourcen. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Dient zum Abrufen der Liste mit Notification Hub-Autorisierungsregeln. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Dient zum Löschen von Notification Hub-Autorisierungsregeln. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Dient zum Abrufen der Notification Hub-Verbindungszeichenfolge. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Dient zum Abrufen der Liste mit Notification Hub-Autorisierungsregeln. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Notification Hub-Autorisierungsregel zum erneuten Generieren des primären/sekundären Schlüssels. Geben Sie den Schlüssel an, der neu generiert werden soll. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Dient zum Erstellen von Notification Hub-Autorisierungsregeln und zum Aktualisieren der dazugehörigen Eigenschaften. Aktualisiert werden können die Zugriffsrechte für Autorisierungsregeln sowie der primäre und der sekundäre Schlüssel. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Dient zum Senden einer Test-Pushbenachrichtigung. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Dient zum Löschen von Notification Hub-Ressourcen. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Dient zum Abrufen der Liste mit Ressourcenbeschreibungen für Namespacemetriken. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Dient zum Abrufen aller Notification Hub-PNS-Anmeldeinformationen. Hierzu zählen Anmeldeinformationen für WNS, MPNS, APNS, GCM und Baidu. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Dient zum Abrufen einer Liste mit Notification Hub-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Dient zum Erstellen eines Notification Hubs und zum Aktualisieren seiner Eigenschaften. Zu diesen Einstellungen zählen in erster Linie PNS-Anmeldeinformationen, Autorisierungsregeln und Gültigkeitsdauer. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/read | Dient zum Abrufen der Liste mit Beschreibungen für Namespaceressourcen. |
> | Aktion | Microsoft.NotificationHubs/Namespaces/write | Dient zum Erstellen einer Namespaceressource und zum Aktualisieren ihrer Eigenschaften. Die Eigenschaften für Markierungen und Kapazität des Namespace können aktualisiert werden. |
> | Aktion | Microsoft.NotificationHubs/operationResults/read | Gibt Vorgangsergebnisse für den Notification Hubs-Anbieter zurück. |
> | Aktion | Microsoft.NotificationHubs/operations/read | Gibt eine Liste unterstützter Vorgänge für den Notification Hubs-Anbieter zurück. |
> | Aktion | Microsoft.NotificationHubs/register/action | Registriert das Abonnement für den NotificationHubs-Ressourcenanbieter und ermöglicht die Erstellung von Namespaces und Notification Hubs. |
> | Aktion | Microsoft.NotificationHubs/unregister/action | Hebt die Registrierung des Abonnements für den NotificationHubs-Ressourcenanbieter auf und ermöglicht die Erstellung von Namespaces und Notification Hubs. |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.OffAzure/HyperVSites/clusters/read | Ruft die Eigenschaften eines Hyper-V-Clusters ab. |
> | Aktion | Microsoft.OffAzure/HyperVSites/clusters/write | Erstellt oder aktualisiert den Hyper-V-Cluster. |
> | Aktion | Microsoft.OffAzure/HyperVSites/delete | Löscht die Hyper-V-Site. |
> | Aktion | Microsoft.OffAzure/HyperVSites/hosts/read | Ruft die Eigenschaften eines Hyper-V-Hosts ab. |
> | Aktion | Microsoft.OffAzure/HyperVSites/hosts/write | Erstellt oder aktualisiert den Hyper-V-Host. |
> | Aktion | Microsoft.OffAzure/HyperVSites/jobs/read | Ruft die Eigenschaften eines Hyper-V-Auftrags ab. |
> | Aktion | Microsoft.OffAzure/HyperVSites/machines/read | Ruft die Eigenschaften eines Hyper-V-Computers ab. |
> | Aktion | Microsoft.OffAzure/HyperVSites/machines/start/action | Startet Hyper-V-Computer. |
> | Aktion | Microsoft.OffAzure/HyperVSites/machines/stop/action | Beendet die Hyper-V-Computer. |
> | Aktion | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Ruft die Eigenschaften eines Hyper-V-Vorgangsstatus ab. |
> | Aktion | Microsoft.OffAzure/HyperVSites/read | Ruft die Eigenschaften einer Hyper-V-Site ab. |
> | Aktion | Microsoft.OffAzure/HyperVSites/refresh/action | Aktualisiert die Objekte in einer Hyper-V-Site. |
> | Aktion | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Ruft die Eigenschaften eines ausführenden Hyper-V-Kontos ab. |
> | Aktion | Microsoft.OffAzure/HyperVSites/usage/read | Ruft die Verwendungen einer Hyper-V-Site ab. |
> | Aktion | Microsoft.OffAzure/HyperVSites/write | Erstellt oder aktualisiert die Hyper-V-Site. |
> | Aktion | Microsoft.OffAzure/Operations/read | Liest die verfügbar gemachten Vorgänge. |
> | Aktion | Microsoft.OffAzure/register/action | Registriert ein Abonnement bei einem Microsoft.OffAzure-Ressourcenanbieter. |
> | Aktion | Microsoft.OffAzure/VMwareSites/delete | Löscht die VMware-Site. |
> | Aktion | Microsoft.OffAzure/VMwareSites/jobs/read | Ruft die Eigenschaften eines VMware-Auftrags ab. |
> | Aktion | Microsoft.OffAzure/VMwareSites/machines/read | Ruft die Eigenschaften eines VMware-Computers ab. |
> | Aktion | Microsoft.OffAzure/VMwareSites/machines/start/action | Startet VMware-Computer. |
> | Aktion | Microsoft.OffAzure/VMwareSites/machines/stop/action | Beendet die VMware-Computer. |
> | Aktion | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Ruft die Eigenschaften eines VMware-Vorgangsstatus ab. |
> | Aktion | Microsoft.OffAzure/VMwareSites/read | Ruft die Eigenschaften einer VMware-Site ab. |
> | Aktion | Microsoft.OffAzure/VMwareSites/refresh/action | Aktualisiert die Objekte in einer VMware-Site. |
> | Aktion | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Ruft die Eigenschaften eines ausführenden VMware-Kontos ab. |
> | Aktion | Microsoft.OffAzure/VMwareSites/usage/read | Ruft die Verwendungen einer VMware-Site ab. |
> | Aktion | Microsoft.OffAzure/VMwareSites/vcenters/read | Ruft die Eigenschaften für eine VMware vCenter-Instanz ab. |
> | Aktion | Microsoft.OffAzure/VMwareSites/vcenters/write | Erstellt oder aktualisiert die VMware vCenter-Instanz. |
> | Aktion | Microsoft.OffAzure/VMwareSites/write | Erstellt oder aktualisiert die VMware-Site. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.OperationalInsights/linkTargets/read | Listet vorhandene Konten auf, die keinem Azure-Abonnement zugeordnet sind. Verwenden Sie eine Kunden-ID, die von diesem Vorgang in der Eigenschaft für die Kunden-ID des Vorgangs „Arbeitsbereich erstellen“ zurückgegeben wird, um dieses Azure-Abonnement mit einem Arbeitsbereich zu verknüpfen. |
> | Aktion | microsoft.operationalinsights/operations/read | Listet alle verfügbaren OperationalInsights-REST-API-Vorgänge auf. |
> | Aktion | microsoft.operationalinsights/register/action | Registriert das Abonnement |
> | Aktion | Microsoft.OperationalInsights/register/action | Dient zum Registrieren eines Abonnements bei einem Ressourcenanbieter. |
> | Aktion | microsoft.operationalinsights/unregister/action | Hebt die Registrierung des Abonnements auf |
> | Aktion | Microsoft.OperationalInsights/workspaces/analytics/query/action | Führt eine Suche mit der neuen Engine aus. |
> | Aktion | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Rufen Sie das Suchschema V2 ab. |
> | Aktion | Microsoft.OperationalInsights/workspaces/api/query/action | Führt eine Suche mit der neuen Engine aus. |
> | Aktion | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Rufen Sie das Suchschema V2 ab. |
> | Aktion | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Dient zum Löschen von Konfigurationsbereichen. |
> | Aktion | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Dient zum Abrufen von Konfigurationsbereichen. |
> | Aktion | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Dient zum Festlegen von Konfigurationsbereichen. |
> | Aktion | Microsoft.OperationalInsights/workspaces/datasources/delete | Dient zum Löschen von Datenquellen unter einem Arbeitsbereich. |
> | Aktion | Microsoft.OperationalInsights/workspaces/datasources/read | Dient zum Abrufen von Datenquellen unter einem Arbeitsbereich. |
> | Aktion | Microsoft.OperationalInsights/workspaces/datasources/write | Dient zum Erstellen/Aktualisieren von Datenquellen unter einem Arbeitsbereich. |
> | Aktion | Microsoft.OperationalInsights/workspaces/delete | Löscht einen Arbeitsbereich. Wenn der Arbeitsbereich bei seiner Erstellung mit einem vorhandenen Arbeitsbereich verknüpft war, wird der Arbeitsbereich, mit dem er verknüpft war, nicht gelöscht. |
> | Aktion | Microsoft.OperationalInsights/workspaces/gateways/delete | Entfernt ein für den Arbeitsbereich konfiguriertes Gateway. |
> | Aktion | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Generiert ein Registrierungszertifikat für den Arbeitsbereich. Dieses Zertifikat wird verwendet, um Microsoft System Center Operation Manager mit dem Arbeitsbereich zu verbinden. |
> | Aktion | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Deaktiviert ein Intelligence Pack für einen bestimmten Arbeitsbereich. |
> | Aktion | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Aktiviert ein Intelligence Pack für einen bestimmten Arbeitsbereich. |
> | Aktion | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Listet alle für einen angegebenen Arbeitsbereich sichtbaren Intelligence Packs auf und gibt an, ob das Pack für diesen Arbeitsbereich aktiviert oder deaktiviert ist. |
> | Aktion | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Löscht verknüpfte Dienste im angegebenen Arbeitsbereich. |
> | Aktion | Microsoft.OperationalInsights/workspaces/linkedServices/read | Ruft verknüpfte Dienste im angegebenen Arbeitsbereich ab. |
> | Aktion | Microsoft.OperationalInsights/workspaces/linkedServices/write | Erstellt oder aktualisiert verknüpfte Dienste im angegebenen Arbeitsbereich. |
> | Aktion | Microsoft.OperationalInsights/workspaces/listKeys/action | Ruft die Listenschlüssel für den Arbeitsbereich ab. Diese Schlüssel werden verwendet, um Microsoft Operational Insights-Agents mit dem Arbeitsbereich zu verbinden. |
> | Aktion | Microsoft.OperationalInsights/workspaces/listKeys/read | Ruft die Listenschlüssel für den Arbeitsbereich ab. Diese Schlüssel werden verwendet, um Microsoft Operational Insights-Agents mit dem Arbeitsbereich zu verbinden. |
> | Aktion | Microsoft.OperationalInsights/workspaces/managementGroups/read | Ruft die Namen und Metadaten für über System Center Operations Manager verwaltete Gruppen ab, die mit diesem Arbeitsbereich verbunden sind. |
> | Aktion | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Ruft Metrikdefinitionen im Arbeitsbereich ab. |
> | Aktion | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Löscht die Benachrichtigungseinstellungen für den Arbeitsbereich des Benutzers. |
> | Aktion | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Ruft die Benachrichtigungseinstellungen für den Arbeitsbereich des Benutzers ab. |
> | Aktion | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Legt die Benachrichtigungseinstellungen für den Arbeitsbereich des Benutzers fest. |
> | Aktion | microsoft.operationalinsights/workspaces/operations/read | Ruft den Status eines OperationalInsights-Arbeitsbereichsvorgangs ab. |
> | Aktion | Microsoft.OperationalInsights/workspaces/purge/action | Löscht die angegebenen Daten aus dem Arbeitsbereich. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Liest Daten aus der Tabelle AADDomainServicesAccountLogon |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Liest Daten aus der Tabelle AADDomainServicesLogonLogoff |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der ADAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Hiermit lesen Sie Daten aus der ADReplicationResult-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der ADSecurityAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/Alert/read | Hiermit lesen Sie Daten aus der Alert-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Hiermit lesen Sie Daten aus der AlertHistory-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Liest Daten aus der AppCenterError-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Hiermit lesen Sie Daten aus der ApplicationInsights-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Dient zum Lesen von Daten aus der AuditLogs-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Liest Daten aus der AutoscaleEvaluationsLog-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Liest Daten aus der AutoscaleScaleActionsLog-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Liest Daten aus der AWSCloudTrail-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Hiermit lesen Sie Daten aus der AzureActivity-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Hiermit lesen Sie Daten aus der AzureMetrics-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Hiermit lesen Sie Daten aus der BoundPort-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Hiermit lesen Sie Daten aus der CommonSecurityLog-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Hiermit lesen Sie Daten aus der ComputerGroup-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Hiermit lesen Sie Daten aus der ConfigurationChange-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Hiermit lesen Sie Daten aus der ConfigurationData-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Hiermit lesen Sie Daten aus der ContainerImageInventory-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Hiermit lesen Sie Daten aus der ContainerInventory-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Hiermit lesen Sie Daten aus der ContainerLog-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Liest Daten aus der ContainerNodeInventory-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Hiermit lesen Sie Daten aus der ContainerServiceLog-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Liest Daten aus der Tabelle DatabricksAccounts |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Liest Daten aus der Tabelle DatabricksClusters |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Liest Daten aus der Tabelle DatabricksDBFS |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Liest Daten aus der Tabelle DatabricksJobs |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Liest Daten aus der Tabelle DatabricksNotebook |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Liest Daten aus der Tabelle DatabricksSecrets |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Liest Daten aus der Tabelle DatabricksSQLPermissions |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Liest Daten aus der Tabelle DatabricksSSH |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Liest Daten aus der Tabelle DatabricksTables |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Liest Daten aus der Tabelle DatabricksWorkspace |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Hiermit lesen Sie Daten aus der DeviceAppCrash-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Hiermit lesen Sie Daten aus der DeviceAppLaunch-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Hiermit lesen Sie Daten aus der DeviceCalendar-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Hiermit lesen Sie Daten aus der DeviceCleanup-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Hiermit lesen Sie Daten aus der DeviceConnectSession-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Hiermit lesen Sie Daten aus der DeviceEtw-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Hiermit lesen Sie Daten aus der DeviceHardwareHealth-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Hiermit lesen Sie Daten aus der DeviceHealth-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Hiermit lesen Sie Daten aus der DeviceHeartbeat-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Hiermit lesen Sie Daten aus der DeviceSkypeHeartbeat-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Hiermit lesen Sie Daten aus der DeviceSkypeSignIn-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Hiermit lesen Sie Daten aus der DeviceSleepState-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Hiermit lesen Sie Daten aus der DHAppFailure-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Hiermit lesen Sie Daten aus der DHAppReliability-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Hiermit lesen Sie Daten aus der DHDriverReliability-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Hiermit lesen Sie Daten aus der DHLogonFailures-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Hiermit lesen Sie Daten aus der DHLogonMetrics-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Hiermit lesen Sie Daten aus der DHOSCrashData-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Hiermit lesen Sie Daten aus der DHOSReliability-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Hiermit lesen Sie Daten aus der DHWipAppLearning-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Hiermit lesen Sie Daten aus der DnsEvents-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Hiermit lesen Sie Daten aus der DnsInventory-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Hiermit lesen Sie Daten aus der ETWEvent-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/Event/read | Hiermit lesen Sie Daten aus der Event-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der ExchangeAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der ExchangeOnlineAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Hiermit lesen Sie Daten aus der Heartbeat-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Liest Daten aus der HuntingBookmark-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der IISAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Hiermit lesen Sie Daten aus der InboundConnection-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Liest Daten aus der Tabelle InsightsMetrics |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Liest Daten aus der Tabelle „IntuneAuditLogs“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Liest Daten aus der Tabelle „IntuneOperationalLogs“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Liest Daten aus der Tabelle „KubeEvents“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Hiermit lesen Sie Daten aus der KubeNodeInventory-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Hiermit lesen Sie Daten aus der KubePodInventory-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Liest Daten aus der Tabelle „KubeServices“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Hiermit lesen Sie Daten aus der LinuxAuditLog-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Hiermit lesen Sie Daten aus der MAApplication-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Hiermit lesen Sie Daten aus der MAApplicationHealth-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Liest Daten aus der Tabelle „MAApplicationHealthAlternativeVersions“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Liest Daten aus der Tabelle „MAApplicationHealthIssues“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Hiermit lesen Sie Daten aus der MAApplicationInstance-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Liest Daten aus der Tabelle „MAApplicationInstanceReadiness“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Hiermit lesen Sie Daten aus der MAApplicationReadiness-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Hiermit lesen Sie Daten aus der MADeploymentPlan-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Hiermit lesen Sie Daten aus der MADevice-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Liest Daten aus der MADeviceNotEnrolled-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Liest Daten aus der Tabelle MADeviceNRT |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Liest Daten aus der Tabelle „MADevicePnPHealth“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Liest Daten aus der Tabelle „MADevicePnPHealthAlternativeVersions“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Liest Daten aus der Tabelle „MADevicePnPHealthIssues“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Hiermit lesen Sie Daten aus der MADeviceReadiness-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Liest Daten aus der Tabelle „MADriverInstanceReadiness“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Hiermit lesen Sie Daten aus der MADriverReadiness-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Hiermit lesen Sie Daten aus der MAOfficeAddin-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Liest Daten aus der Tabelle „MAOfficeAddinEntityHealth“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Hiermit lesen Sie Daten aus der MAOfficeAddinHealth-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Liest Daten aus der Tabelle „MAOfficeAddinHealthEventNRT“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Liest Daten aus der Tabelle „MAOfficeAddinHealthIssues“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Hiermit lesen Sie Daten aus der MAOfficeAddinInstance-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Liest Daten aus der Tabelle „MAOfficeAddinInstanceReadiness“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Hiermit lesen Sie Daten aus der MAOfficeAddinReadiness-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Hiermit lesen Sie Daten aus der MAOfficeApp-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Liest Daten aus der Tabelle „MAOfficeAppCrashesNRT“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Hiermit lesen Sie Daten aus der MAOfficeAppHealth-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Hiermit lesen Sie Daten aus der MAOfficeAppInstance-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Liest Daten aus der Tabelle „MAOfficeAppInstanceHealth“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Hiermit lesen Sie Daten aus der MAOfficeAppReadiness-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Liest Daten aus der Tabelle „MAOfficeAppSessionsNRT“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Hiermit lesen Sie Daten aus der MAOfficeBuildInfo-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Hiermit lesen Sie Daten aus der MAOfficeCurrencyAssessment-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Hiermit lesen Sie Daten aus der MAOfficeCurrencyAssessmentDailyCounts-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Hiermit lesen Sie Daten aus der MAOfficeDeploymentStatus-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Liest Daten aus der Tabelle MAOfficeDeploymentStatusNRT |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Liest Daten aus der Tabelle „MAOfficeMacroErrorNRT“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Liest Daten aus der Tabelle MAOfficeMacroGlobalHealth |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Liest Daten aus der Tabelle „MAOfficeMacroHealth“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Liest Daten aus der Tabelle „MAOfficeMacroHealthIssues“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Liest Daten aus der Tabelle „MAOfficeMacroIssueInstanceReadiness“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Hiermit lesen Sie Daten aus der MAOfficeMacroIssueReadiness-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Hiermit lesen Sie Daten aus der MAOfficeMacroSummary-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Hiermit lesen Sie Daten aus der MAOfficeSuite-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Hiermit lesen Sie Daten aus der MAOfficeSuiteInstance-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Hiermit lesen Sie Daten aus der MAProposedPilotDevices-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Hiermit lesen Sie Daten aus der MAWindowsBuildInfo-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Hiermit lesen Sie Daten aus der MAWindowsCurrencyAssessment-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Hiermit lesen Sie Daten aus der MAWindowsCurrencyAssessmentDailyCounts-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Hiermit lesen Sie Daten aus der MAWindowsDeploymentStatus-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Liest Daten aus der Tabelle MAWindowsDeploymentStatusNRT |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Liest Daten aus der Tabelle „MAWindowsSysReqInstanceReadiness“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebW3CLog/read | Liest Daten aus der Tabelle MicrosoftWebW3CLog |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Hiermit lesen Sie Daten aus der NetworkMonitoring-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Hiermit lesen Sie Daten aus der OfficeActivity-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/Operation/read | Hiermit lesen Sie Daten aus der Operation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Hiermit lesen Sie Daten aus der OutboundConnection-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/Perf/read | Hiermit lesen Sie Daten aus der Perf-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Hiermit lesen Sie Daten aus der ProtectionStatus-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/read | Dient zum Ausführen von Abfragen für die Daten im Arbeitsbereich. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Hiermit lesen Sie Daten aus der ReservedAzureCommonFields-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Hiermit lesen Sie Daten aus der ReservedCommonFields-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SCCMAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SCOMAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Hiermit lesen Sie Daten aus der SecurityAlert-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Hiermit lesen Sie Daten aus der SecurityBaseline-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Hiermit lesen Sie Daten aus der SecurityBaselineSummary-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Hiermit lesen Sie Daten aus der SecurityDetection-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Hiermit lesen Sie Daten aus der SecurityEvent-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Liest Daten aus der SecurityIoTRawEvent-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Liest Daten aus der SecurityRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Hiermit lesen Sie Daten aus der ServiceFabricOperationalEvent-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Hiermit lesen Sie Daten aus der ServiceFabricReliableActorEvent-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Hiermit lesen Sie Daten aus der ServiceFabricReliableServiceEvent-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SfBAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SfBOnlineAssessmentRecommendatio-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SharePointOnlineAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Dient zum Lesen von Daten aus der SigninLogs-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SPAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der SQLAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Hiermit lesen Sie Daten aus der SQLQueryPerformance-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Hiermit lesen Sie Daten aus der Tabelle „SqlThreatProtectionLoginAudits“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Hiermit lesen Sie Daten aus der Tabelle „SqlVulnerabilityAssessmentResult“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Hiermit lesen Sie Daten aus der Syslog-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Hiermit lesen Sie Daten aus der SysmonEvent-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Hiermit lesen Sie Daten aus einem beliebigen benutzerdefinierten Protokoll. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Liest Daten aus der ThreatIntelligenceIndicator-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Hiermit lesen Sie Daten aus der UAApp-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Hiermit lesen Sie Daten aus der UAComputer-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Hiermit lesen Sie Daten aus der UAComputerRank-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Hiermit lesen Sie Daten aus der UADriver-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Hiermit lesen Sie Daten aus der UADriverProblemCodes-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Hiermit lesen Sie Daten aus der UAFeedback-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Hiermit lesen Sie Daten aus der UAHardwareSecurity-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Hiermit lesen Sie Daten aus der UAIESiteDiscovery-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Hiermit lesen Sie Daten aus der UAOfficeAddIn-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Hiermit lesen Sie Daten aus der UAProposedActionPlan-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Hiermit lesen Sie Daten aus der UASysReqIssue-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Hiermit lesen Sie Daten aus der UAUpgradedComputer-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/Update/read | Hiermit lesen Sie Daten aus der Update-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Hiermit lesen Sie Daten aus der UpdateRunProgress-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Hiermit lesen Sie Daten aus der UpdateSummary-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/Usage/read | Hiermit lesen Sie Daten aus der Usage-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Liest Daten aus der Tabelle „VMBoundPort“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Liest Daten aus der Tabelle „VMConnection“. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Hiermit lesen Sie Daten aus der W3CIISLog-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Hiermit lesen Sie Daten aus der WaaSDeploymentStatus-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Hiermit lesen Sie Daten aus der WaaSInsiderStatus-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Hiermit lesen Sie Daten aus der WaaSUpdateStatus-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Hiermit lesen Sie Daten aus der WDAVStatus-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Hiermit lesen Sie Daten aus der WDAVThreat-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der WindowsClientAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Dient zum Lesen von Daten aus der WindowsEvent-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Hiermit lesen Sie Daten aus der WindowsFirewall-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Hiermit lesen Sie Daten aus der WindowsServerAssessmentRecommendation-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WireData/read | Hiermit lesen Sie Daten aus der WireData-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Lesen von Daten aus der Tabelle „WorkloadMonitoringPerf“ |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Hiermit lesen Sie Daten aus der WUDOAggregatedStatus-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Hiermit lesen Sie Daten aus der WUDOStatus-Tabelle. |
> | Aktion | Microsoft.OperationalInsights/workspaces/read | Ruft einen vorhandenen Arbeitsbereich ab. |
> | Aktion | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Generiert den angegebenen gemeinsam verwendeten Schlüssel des Arbeitsbereichs neu. |
> | Aktion | microsoft.operationalinsights/workspaces/rules/read | Ruft alle Warnungsregeln ab. |
> | Aktion | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Löscht eine gespeicherte Suchabfrage. |
> | Aktion | Microsoft.OperationalInsights/workspaces/savedSearches/read | Ruft eine gespeicherte Suchabfrage ab. |
> | Aktion | microsoft.operationalinsights/workspaces/savedsearches/results/read | Ruft die Ergebnisse gespeicherter Suchvorgänge ab. Veraltet |
> | Aktion | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Löscht geplante Suchaktionen. |
> | Aktion | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Ruft geplante Suchaktionen ab. |
> | Aktion | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Erstellt oder aktualisiert geplante Suchaktionen. |
> | Aktion | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Löscht geplante Suchvorgänge. |
> | Aktion | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Ruft geplante Suchvorgänge ab. |
> | Aktion | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Erstellt oder aktualisiert geplante Suchvorgänge. |
> | Aktion | Microsoft.OperationalInsights/workspaces/savedSearches/write | Erstellt eine gespeicherte Suchabfrage. |
> | Aktion | Microsoft.OperationalInsights/workspaces/schema/read | Ruft das Suchschema für den Arbeitsbereich ab.  Ein Suchschema umfasst die verfügbar gemachten Felder und die dazugehörigen Typen. |
> | Aktion | Microsoft.OperationalInsights/workspaces/search/action | Führt eine Suchabfrage aus. |
> | Aktion | microsoft.operationalinsights/workspaces/search/read | Ruft Suchergebnisse ab. Veraltet. |
> | Aktion | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Ruft die gemeinsam verwendeten Schlüssel für den Arbeitsbereich ab. Diese Schlüssel werden verwendet, um Microsoft Operational Insights-Agents mit dem Arbeitsbereich zu verbinden. |
> | Aktion | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Ruft die gemeinsam verwendeten Schlüssel für den Arbeitsbereich ab. Diese Schlüssel werden verwendet, um Microsoft Operational Insights-Agents mit dem Arbeitsbereich zu verbinden. |
> | Aktion | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Löscht eine Speicherkonfiguration. Daraufhin werden von Microsoft Operational Insights keine Daten mehr aus dem Speicherkonto gelesen. |
> | Aktion | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Ruft eine Speicherkonfiguration ab. |
> | Aktion | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Erstellt eine neue Speicherkonfiguration. Diese Konfigurationen werden dazu verwendet, Daten von einem Speicherort in einem vorhandenen Speicherkonto abzurufen. |
> | Aktion | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Ruft das Übersetzungsfehlerprotokoll für das Upgrade der Suche für den Arbeitsbereich ab. |
> | Aktion | Microsoft.OperationalInsights/workspaces/usages/read | Ruft Nutzungsdaten für einen Arbeitsbereich ab, einschließlich der durch den Arbeitsbereich gelesenen Datenmenge. |
> | Aktion | Microsoft.OperationalInsights/workspaces/write | Erstellt einen neuen Arbeitsbereich oder stellt eine Verknüpfung mit einem vorhandenen Arbeitsbereich her, indem die Kunden-ID für den vorhandenen Arbeitsbereich bereitgestellt wird. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.OperationsManagement/managementAssociations/delete | Löscht eine vorhandene Verwaltungszuordnung. |
> | Aktion | Microsoft.OperationsManagement/managementAssociations/read | Ruft eine vorhandene Verwaltungszuordnung ab. |
> | Aktion | Microsoft.OperationsManagement/managementAssociations/write | Erstellt eine neue Verwaltungszuordnung. |
> | Aktion | Microsoft.OperationsManagement/managementConfigurations/delete | Löscht eine vorhandene Verwaltungskonfiguration. |
> | Aktion | Microsoft.OperationsManagement/managementConfigurations/read | Ruft eine vorhandene Verwaltungskonfiguration ab. |
> | Aktion | Microsoft.OperationsManagement/managementConfigurations/write | Erstellt eine vorhandene Verwaltungskonfiguration. |
> | Aktion | Microsoft.OperationsManagement/register/action | Dient zum Registrieren eines Abonnements bei einem Ressourcenanbieter. |
> | Aktion | Microsoft.OperationsManagement/solutions/delete | Dient zum Löschen vorhandener OMS-Lösungen. |
> | Aktion | Microsoft.OperationsManagement/solutions/read | Dient zum Abrufen vorhandener OMS-Lösungen. |
> | Aktion | Microsoft.OperationsManagement/solutions/write | Dient zum Erstellen neuer OMS-Lösungen. |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.PolicyInsights/asyncOperationResults/read | Hiermit wird das Ergebnis des asynchronen Vorgangs abgerufen. |
> | Aktion | Microsoft.PolicyInsights/operations/read | Ruft unterstützte Vorgänge im Microsoft.PolicyInsights-Namespace ab |
> | Aktion | Microsoft.PolicyInsights/policyEvents/queryResults/action | Fragt Informationen zu Richtlinienereignissen ab. |
> | Aktion | Microsoft.PolicyInsights/policyEvents/queryResults/read | Fragt Informationen zu Richtlinienereignissen ab. |
> | Aktion | Microsoft.PolicyInsights/policyStates/queryResults/action | Fragt Informationen zu Richtlinienzuständen ab. |
> | Aktion | Microsoft.PolicyInsights/policyStates/queryResults/read | Fragt Informationen zu Richtlinienzuständen ab. |
> | Aktion | Microsoft.PolicyInsights/policyStates/summarize/action | Fragt zusammenfassende Informationen zu den letzten Richtlinienzuständen ab. |
> | Aktion | Microsoft.PolicyInsights/policyStates/summarize/read | Fragt zusammenfassende Informationen zu den letzten Richtlinienzuständen ab. |
> | Aktion | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Hiermit wird eine neue Konformitätsauswertung für den ausgewählten Bereich ausgelöst. |
> | Aktion | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Hiermit werden Informationen zu Ressourcen abgefragt, die gemäß DeployIfNotExists-Richtlinien erforderlich sind. |
> | Aktion | Microsoft.PolicyInsights/register/action | Registriert den Ressourcenanbieter für Microsoft Policy Insights und ermöglicht entsprechende Aktionen |
> | Aktion | Microsoft.PolicyInsights/remediations/cancel/action | Bricht laufende Microsoft Policy-Wartungen ab |
> | Aktion | Microsoft.PolicyInsights/remediations/delete | Hiermit werden Richtlinienwartungen gelöscht. |
> | Aktion | Microsoft.PolicyInsights/remediations/listDeployments/read | Listet die für eine Richtlinienwartung erforderlichen Bereitstellungen auf. |
> | Aktion | Microsoft.PolicyInsights/remediations/read | Hiermit werden Richtlinienwartungen abgerufen. |
> | Aktion | Microsoft.PolicyInsights/remediations/write | Erstellt oder aktualisiert Microsoft Policy-Wartungen |
> | Aktion | Microsoft.PolicyInsights/unregister/action | Hebt die Registrierung des Ressourcenanbieters für Microsoft Policy Insights auf |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Portal/consoles/delete | Entfernt die Cloud Shell-Instanz. |
> | Aktion | Microsoft.Portal/consoles/read | Hiermit wird die Cloud Shell-Instanz gelesen. |
> | Aktion | Microsoft.Portal/consoles/write | Hiermit erstellen oder aktualisieren Sie eine Cloud Shell-Instanz. |
> | Aktion | Microsoft.Portal/dashboards/delete | Entfernt das Dashboard aus dem Abonnement. |
> | Aktion | Microsoft.Portal/dashboards/read | Liest die Dashboards für das Abonnement. |
> | Aktion | Microsoft.Portal/dashboards/write | Fügt ein Dashboard zu einem Abonnement hinzu oder ändert es. |
> | Aktion | Microsoft.Portal/register/action | Führt die Registrierung bei einem Portal durch. |
> | Aktion | Microsoft.Portal/usersettings/delete | Entfernt die Cloud Shell-Benutzereinstellungen. |
> | Aktion | Microsoft.Portal/usersettings/read | Liest die Cloud Shell-Benutzereinstellungen. |
> | Aktion | Microsoft.Portal/usersettings/write | Dient zum Erstellen oder Aktualisieren einer Cloud Shell-Benutzereinstellung. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.PowerBIDedicated/capacities/delete | Löscht die dedizierte Power BI-Kapazität. |
> | Aktion | Microsoft.PowerBIDedicated/capacities/read | Ruft die Informationen der angegebenen dedizierten Power BI-Kapazität ab. |
> | Aktion | Microsoft.PowerBIDedicated/capacities/resume/action | Setzt die Kapazität fort. |
> | Aktion | Microsoft.PowerBIDedicated/capacities/skus/read | Ruft die verfügbaren SKU-Informationen für die Kapazität ab. |
> | Aktion | Microsoft.PowerBIDedicated/capacities/suspend/action | Hält die Kapazität an. |
> | Aktion | Microsoft.PowerBIDedicated/capacities/write | Erstellt oder aktualisiert die angegebene dedizierte Power BI-Kapazität. |
> | Aktion | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Überprüft, ob der Name für die angegebene Power BI-Kapazität gültig ist und nicht verwendet wird. |
> | Aktion | Microsoft.PowerBIDedicated/locations/operationresults/read | Ruft die Informationen des angegebenen Vorgangsergebnisses ab. |
> | Aktion | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Ruft die Informationen des angegebenen Vorgangsstatus ab. |
> | Aktion | Microsoft.PowerBIDedicated/operations/read | Ruft die Informationen von Vorgängen ab. |
> | Aktion | Microsoft.PowerBIDedicated/register/action | Registriert den dedizierten Power BI-Ressourcenanbieter. |
> | Aktion | Microsoft.PowerBIDedicated/skus/read | Ruft die Informationen der SKUs ab. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.RecoveryServices/locations/allocatedStamp/read | „GetAllocatedStamp“ ist ein interner Vorgang des Diensts. |
> | Aktion | Microsoft.RecoveryServices/locations/allocateStamp/action | „AllocateStamp“ ist ein interner Vorgang des Diensts. |
> | Aktion | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Aktion | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Dient zum Erstellen eines geschützten Elements für die Sicherung. |
> | Aktion | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Gibt die Liste mit allen geschützten Elementen zurück. |
> | Aktion | Microsoft.RecoveryServices/Locations/backupStatus/action | Überprüft den Sicherungsstatus für Recovery Services-Tresore. |
> | Aktion | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Überprüft Features. |
> | Aktion | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Überprüft die Verfügbarkeit des Ressourcennamens als API, um sicherzustellen, dass der Ressourcenname verfügbar ist. |
> | Aktion | Microsoft.RecoveryServices/locations/operationStatus/read | Ruft den Vorgangsstatus eines angegebenen Vorgangs ab. |
> | Aktion | Microsoft.RecoveryServices/operations/read | Der Vorgang gibt die Liste der Vorgänge für einen Ressourcenanbieter zurück. |
> | Aktion | Microsoft.RecoveryServices/register/action | Registriert das Abonnement für den angegebenen Ressourcenanbieter. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupconfig/read | Gibt die Konfiguration für Recovery Services-Tresore zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupconfig/write | Aktualisiert die Konfiguration für Recovery Services-Tresore. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupEngines/read | Gibt alle Sicherungsverwaltungsserver zurück, die beim Tresor registriert sind. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Löscht einen beabsichtigten Sicherungsschutz. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Ruft einen beabsichtigten Sicherungsschutz ab. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Erstellt einen beabsichtigten Sicherungsschutz. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Gibt den Status des Vorgangs zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Ruft alle schützbaren Container ab. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Löscht den registrierten Container. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Führt die Abfrage für Workloads innerhalb eines Containers durch. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Ruft alle Elemente in einem Container ab. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Ruft das Ergebnis eines Vorgangs ab, der für den Schutzcontainer ausgeführt wurde. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Führt eine Sicherung für geschützte Elemente aus. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Löscht geschützte Elemente. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Ruft das Ergebnis eines Vorgangs ab, der für geschützte Elemente ausgeführt wurde. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Gibt den Status eines Vorgangs zurück, der für geschützte Elemente ausgeführt wurde. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Gibt Objektdetails des geschützten Elements zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Dient zum Bereitstellen der sofortigen Elementwiederherstellung für geschützte Elemente. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Dient zum Abrufen von Wiederherstellungspunkten für geschützte Elemente. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Dient zum Wiederherstellen von Wiederherstellungspunkten für geschützte Elemente. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Dient zum Widerrufen der sofortigen Elementwiederherstellung für geschützte Elemente. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Dient zum Erstellen eines geschützten Elements für die Sicherung. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Gibt alle registrierten Container zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Erstellt einen registrierten Container. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aktualisiert die Containerliste. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Dient zum Abbrechen des Auftrags. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Gibt das Ergebnis von Auftragsvorgängen zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupJobs/read | Gibt alle Auftragsobjekte zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Dient zum Exportieren von Aufträgen. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Gibt das Ergebnis eines Sicherungsvorgangs für Recovery Services-Tresore zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupOperations/read | Gibt den Status eines Sicherungsvorgangs für Recovery Services-Tresore zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Dient zum Löschen einer Schutzrichtlinie. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Dient zum Abrufen der Ergebnisse von Richtlinienvorgängen. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Dient zum Abrufen des Status von Richtlinienvorgängen. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Gibt alle Schutzrichtlinien zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Erstellt Schutzrichtlinien. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Gibt die Liste mit allen schützbaren Elementen zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Gibt die Liste mit allen geschützten Elementen zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Gibt alle zum Abonnement gehörenden Container zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listet den gesamten beabsichtigten Sicherungsschutz auf. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Gibt Informationen zur Sicherheits-PIN für Recovery Services-Tresore zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Gibt die Speicherkonfiguration für Recovery Services-Tresore zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Aktualisiert Speicherkonfiguration für Recovery Services-Tresore. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Gibt Zusammenfassungen für geschützte Elemente und geschützte Server für einen Recovery Services-Tresor zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Überprüft Vorgang für geschütztes Element. |
> | Aktion | Microsoft.RecoveryServices/Vaults/certificates/write | Der Vorgang „Ressourcenzertifikat aktualisieren“ aktualisiert das Zertifikat für die Ressourcen-/Tresoranmeldeinformationen. |
> | Aktion | Microsoft.RecoveryServices/Vaults/delete | Der Vorgang „Tresor löschen“ löscht die angegebene Azure-Ressource vom Typ „Tresor“. |
> | Aktion | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Aktion | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Aktion | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Aktion | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ruft die Warnungen für den Recovery Services-Tresor ab. |
> | Aktion | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Löst die Warnung auf. |
> | Aktion | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Ruft die Konfiguration von Recovery Services-Tresorbenachrichtigungen ab. |
> | Aktion | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfiguriert E-Mail-Benachrichtigungen für den Recovery Services-Tresor. |
> | Aktion | Microsoft.RecoveryServices/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Aktion | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Der Vorgang „Registrierung des Containers aufheben“ kann zum Aufheben der Registrierung eines Containers verwendet werden. |
> | Aktion | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Mit dem Vorgang „Vorgangsergebnisse abrufen“ können der Vorgangsstatus und das Ergebnis für den asynchron übermittelten Vorgang abgerufen werden. |
> | Aktion | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Der Vorgang „Container abrufen“ kann zum Abrufen der für eine Ressource registrierten Container verwendet werden. |
> | Aktion | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Der Vorgang „Dienstcontainer registrieren“ kann zum Registrieren eines Containers beim Wiederherstellungsdienst verwendet werden. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Dient zum Lesen beliebiger Warnungseinstellungen |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Erstellt oder aktualisiert Warnungseinstellungen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationEvents/read | Dient zum Lesen beliebiger Ereignisse |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Prüft die Konsistenz des Fabrics. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Löscht Fabrics. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Dient zum Bereitstellen von Prozessserverimages. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migriert Fabric zu AAD. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Dient zum Lesen beliebiger Fabrics |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Dient zum Neuzuordnen des Gateways. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Dient zum Entfernen von Fabrics. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Verlängert das Zertifikat für Fabrics. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Liest logische Netzwerke. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Dient zum Lesen beliebiger Netzwerke |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Löscht Netzwerkzuordnungen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Dient zum Lesen beliebiger Netzwerkzuordnungen |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Erstellt oder aktualisiert Netzwerkzuordnungen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Dient zum Ermitteln des schützbaren Elements. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Dient zum Lesen beliebiger Schutzcontainer |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Dient zum Entfernen von Schutzcontainern. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Löscht die Migrationselemente. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migriert ein Element. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Liest die Wiederherstellungspunkte für die Migration. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Liest die Migrationselemente. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Testet die Migration. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Testet die Bereinigung der Migration. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Dient zum Erstellen oder Aktualisieren von Migrationselementen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Dient zum Lesen beliebiger schützbarer Elemente |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Fügt Datenträger hinzu |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Dient zum Anwenden des Wiederherstellungspunkts. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Löscht geschützte Elemente. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failovercommit |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Geplantes Failover |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Dient zum Lesen beliebiger geschützter Elemente |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Dient zum Lesen beliebiger Wiederherstellungspunkte für die Replikation |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Dient zum Entfernen geschützter Elemente. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Entfernt Datenträger |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Dient zum Reparieren der Replikation. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Dient zum erneuten Schützen geschützter Elemente. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Sendet Feedback. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Liest Zielcomputegrößen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testfailover |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Testfailoverbereinigung |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Dient zum Aktualisieren von Mobility Service. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Erstellt oder aktualisiert geschützte Elemente. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Löscht Schutzcontainerzuordnungen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Dient zum Lesen beliebiger Schutzcontainerzuordnungen |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Dient zum Entfernen von Schutzcontainerzuordnungen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Erstellt oder aktualisiert Schutzcontainerzuordnungen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Dient zum Wechseln von Schutzcontainern. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Erstellt oder aktualisiert Schutzcontainer. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Löscht Recovery Services-Anbieter. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Dient zum Lesen beliebiger Recovery Services-Anbieter |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Dient zum Aktualisieren des Anbieters. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Dient zum Entfernen von Recovery Services-Anbietern. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Erstellt oder aktualisiert Recovery Services-Anbieter. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Dient zum Lesen beliebiger Speicherklassifizierungen |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Löscht Speicherklassifizierungszuordnungen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Dient zum Lesen beliebiger Speicherklassifizierungszuordnungen |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Erstellt oder aktualisiert Speicherklassifizierungszuordnungen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Löscht vCenters. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Dienst zum Lesen beliebiger vCenter |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Erstellt oder aktualisiert vCenters. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Erstellt oder aktualisiert Fabrics. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Dient zum Abbrechen von Aufträgen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationJobs/read | Dient zum Lesen beliebiger Aufträge |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Dient zum Neustarten von Aufträgen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Dient zum Fortsetzen von Aufträgen. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Liest die Migrationselemente. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Dient zum Lesen beliebiger Netzwerkzuordnungen |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Dient zum Lesen beliebiger Netzwerke |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Löscht Richtlinien. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Dient zum Lesen beliebiger Richtlinien |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Erstellt oder aktualisiert Richtlinien. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Dient zum Lesen beliebiger geschützter Elemente |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Dient zum Lesen beliebiger Schutzcontainerzuordnungen |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Dient zum Lesen beliebiger Schutzcontainer |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Löscht Wiederherstellungspläne. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Wiederherstellungsplan für Failovercommit |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Wiederherstellungsplan für geplantes Failover |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Dient zum Lesen beliebiger Wiederherstellungspläne |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Wiederherstellungsplan für erneutes Schützen |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Wiederherstellungsplan für Testfailover |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Wiederherstellungsplan für Testfailoverbereinigung |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Wiederherstellungsplan für Failover |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Erstellt oder aktualisiert Wiederherstellungspläne. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Dient zum Lesen beliebiger Recovery Services-Anbieter |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Dient zum Lesen beliebiger Speicherklassifizierungszuordnungen |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Dient zum Lesen beliebiger Speicherklassifizierungen |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Liest alles.  |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationUsages/read | Liest Verwendungen der Tresorreplikation. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Liest die Integrität der Tresorreplikation. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Aktualisiert die Tresorintegrität. |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Liest alles.  |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Erstellt oder aktualisiert  |
> | Aktion | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Dienst zum Lesen beliebiger vCenter |
> | Aktion | Microsoft.RecoveryServices/vaults/usages/read | Liest Tresorverwendungen. |
> | Aktion | Microsoft.RecoveryServices/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | Aktion | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Der Vorgang „Tresortoken“ kann zum Abrufen des Tresortokens für Back-End-Vorgänge auf Tresorebene verwendet werden. |
> | Aktion | Microsoft.RecoveryServices/Vaults/write | Der Vorgang „Tresor erstellen“ erstellt eine Azure-Ressource vom Typ „Tresor“. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Relay/checkNameAvailability/action | Prüft die Verfügbarkeit des Namespaces unter dem angegebenen Abonnement. |
> | Aktion | Microsoft.Relay/checkNamespaceAvailability/action | Prüft die Verfügbarkeit des Namespaces unter dem angegebenen Abonnement. Diese API ist veraltet. Verwenden Sie stattdessen CheckNameAvailability. |
> | Aktion | Microsoft.Relay/namespaces/authorizationRules/action | Dient zum Aktualisieren von Namespace-Autorisierungsregeln. Diese API ist veraltet. Verwenden Sie stattdessen einen PUT-Aufruf, um die Namespace-Autorisierungsregel zu aktualisieren. Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. |
> | Aktion | Microsoft.Relay/namespaces/authorizationRules/delete | Dient zum Löschen von Namespace-Autorisierungsregeln. Die standardmäßige Namespace-Autorisierungsregel kann nicht gelöscht werden.  |
> | Aktion | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Dient zum Abrufen der Verbindungszeichenfolge für den Namespace. |
> | Aktion | Microsoft.Relay/namespaces/authorizationRules/read | Dient zum Abrufen der Liste mit Beschreibungen für Namespace-Autorisierungsregeln. |
> | Aktion | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Dient zum erneuten Generieren des primären oder sekundären Schlüssels für die Ressource. |
> | Aktion | Microsoft.Relay/namespaces/authorizationRules/write | Dient zum Erstellen von Autorisierungsregeln auf Namespaceebene und zum Aktualisieren der dazugehörigen Eigenschaften. Aktualisiert werden können die Zugriffsrechte für Autorisierungsregeln sowie der primäre und der sekundäre Schlüssel. |
> | Aktion | Microsoft.Relay/namespaces/Delete | Dient zum Löschen von Namespaceressourcen. |
> | Aktion | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Ruft die Schlüssel der Autorisierungsregeln für den primären Namespace für die Notfallwiederherstellung. |
> | Aktion | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Ruft die Autorisierungsregeln des primären Namespace für die Notfallwiederherstellung ab. |
> | Aktion | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Hiermit wird die Notfallwiederherstellung deaktiviert und die Replikation von Änderungen vom primären Namespace zu sekundären Namespaces beendet. |
> | Aktion | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Prüft die Verfügbarkeit des Namespacealias unter dem angegebenen Abonnement. |
> | Aktion | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Hiermit wird die dem Namespace zugeordnete Notfallwiederherstellungskonfiguration gelöscht. Dieser Vorgang kann nur über den primären Namespace aufgerufen werden. |
> | Aktion | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Hiermit wird ein Failover bei der georedundanten Notfallwiederherstellung ausgelöst und der Namespacealias neu konfiguriert, um auf den sekundären Namespace zu zeigen. |
> | Aktion | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Hiermit wird die dem Namespace zugeordnete Notfallwiederherstellungskonfiguration abgerufen. |
> | Aktion | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Hiermit wird die dem Namespace zugeordnete Notfallwiederherstellungskonfiguration erstellt oder aktualisiert. |
> | Aktion | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Vorgang zum Aktualisieren von HybridConnection. Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. Autorisierungsregeln. Verwenden Sie einen PUT-Aufruf, um die Autorisierungsregel zu aktualisieren. |
> | Aktion | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Vorgang zum Löschen von HybridConnection-Autorisierungsregeln. |
> | Aktion | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Dient zum Abrufen der HybridConnection-Verbindungszeichenfolge. |
> | Aktion | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Ruft die Liste der HybridConnection-Autorisierungsregeln ab. |
> | Aktion | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Dient zum erneuten Generieren des primären oder sekundären Schlüssels für die Ressource. |
> | Aktion | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Dient zum Erstellen von HybridConnection-Autorisierungsregeln und zum Aktualisieren der dazugehörigen Eigenschaften. Die Zugriffsrechte für Autorisierungsregeln können aktualisiert werden. |
> | Aktion | Microsoft.Relay/namespaces/HybridConnections/Delete | Vorgang zum Löschen von HybridConnection-Ressourcen. |
> | Aktion | Microsoft.Relay/namespaces/HybridConnections/read | Dient zum Abrufen einer Liste mit HybridConnection-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.Relay/namespaces/HybridConnections/write | Dient zum Erstellen oder Aktualisieren von HybridConnection-Eigenschaften. |
> | Aktion | Microsoft.Relay/namespaces/messagingPlan/read | Hiermit wird der Messagingplan für einen Namespace abgerufen.<br>Diese API ist veraltet.<br>Über die MessagingPlan-Ressource verfügbar gemachte Eigenschaften werden in höheren API-Versionen in die (übergeordnete) Namespaceressource verschoben.<br>Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. |
> | Aktion | Microsoft.Relay/namespaces/messagingPlan/write | Hiermit wird der Messagingplan für einen Namespace aktualisiert.<br>Diese API ist veraltet.<br>Über die MessagingPlan-Ressource verfügbar gemachte Eigenschaften werden in höheren API-Versionen in die (übergeordnete) Namespaceressource verschoben.<br>Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. |
> | Aktion | Microsoft.Relay/namespaces/operationresults/read | Hiermit wird der Status des Namespacevorgangs abgerufen. |
> | Aktion | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Abrufen der Liste mit Ressourcenbeschreibungen für Namespacemetriken. |
> | Aktion | Microsoft.Relay/namespaces/read | Dient zum Abrufen der Liste mit Beschreibungen für Namespaceressourcen. |
> | Aktion | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Entfernt den ACS-Namespace. |
> | Aktion | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Vorgang zum Aktualisieren von WcfRelay. Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. Autorisierungsregeln. Verwenden Sie einen PUT-Aufruf, um die Autorisierungsregel zu aktualisieren. |
> | Aktion | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Vorgang zum Löschen von WcfRelay-Autorisierungsregeln. |
> | Aktion | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Dient zum Abrufen der WcfRelay-Verbindungszeichenfolge. |
> | Aktion | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Dient zum Abrufen der WcfRelay-Autorisierungsregeln. |
> | Aktion | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Dient zum erneuten Generieren des primären oder sekundären Schlüssels für die Ressource. |
> | Aktion | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Dient zum Erstellen von WcfRelay-Autorisierungsregeln und zum Aktualisieren der dazugehörigen Eigenschaften. Die Zugriffsrechte für Autorisierungsregeln können aktualisiert werden. |
> | Aktion | Microsoft.Relay/namespaces/WcfRelays/Delete | Vorgang zum Löschen von WcfRelay-Ressourcen. |
> | Aktion | Microsoft.Relay/namespaces/WcfRelays/read | Dient zum Abrufen einer Liste mit WcfRelay-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.Relay/namespaces/WcfRelays/write | Dient zum Erstellen oder Aktualisieren von WcfRelay-Eigenschaften. |
> | Aktion | Microsoft.Relay/namespaces/write | Dient zum Erstellen einer Namespaceressource und zum Aktualisieren ihrer Eigenschaften. Die Eigenschaften für Markierungen und Kapazität des Namespace können aktualisiert werden. |
> | Aktion | Microsoft.Relay/operations/read | Dient zum Abrufen von Vorgängen. |
> | Aktion | Microsoft.Relay/register/action | Registriert das Abonnement für den Relayressourcenanbieter und ermöglicht die Erstellung von Relayressourcen. |
> | Aktion | Microsoft.Relay/unregister/action | Registriert das Abonnement für den Relayressourcenanbieter und ermöglicht die Erstellung von Relayressourcen. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Ruft den Verfügbarkeitsstatus für die angegebene Ressource ab. |
> | Aktion | Microsoft.ResourceHealth/AvailabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | Aktion | Microsoft.ResourceHealth/events/read | Ruft Service Health-Ereignisse für ein angegebenes Abonnement ab. |
> | Aktion | Microsoft.Resourcehealth/healthevent/action | Zeigt die Änderung des Integritätszustands für die angegebene Ressource an. |
> | Aktion | Microsoft.Resourcehealth/healthevent/Activated/action | Zeigt die Änderung des Integritätszustands für die angegebene Ressource an. |
> | Aktion | Microsoft.Resourcehealth/healthevent/InProgress/action | Zeigt die Änderung des Integritätszustands für die angegebene Ressource an. |
> | Aktion | Microsoft.Resourcehealth/healthevent/Pending/action | Zeigt die Änderung des Integritätszustands für die angegebene Ressource an. |
> | Aktion | Microsoft.Resourcehealth/healthevent/Resolved/action | Zeigt die Änderung des Integritätszustands für die angegebene Ressource an. |
> | Aktion | Microsoft.Resourcehealth/healthevent/Updated/action | Zeigt die Änderung des Integritätszustands für die angegebene Ressource an. |
> | Aktion | Microsoft.ResourceHealth/impactedResources/read | Ruft betroffene Ressourcen für das angegebene Abonnement ab. |
> | Aktion | Microsoft.ResourceHealth/Operations/read | Ruft die verfügbaren Vorgänge für Microsoft ResourceHealth ab. |
> | Aktion | Microsoft.ResourceHealth/register/action | Hiermit wird das Abonnement für Microsoft ResourceHealth registriert. |
> | Aktion | Microsoft.ResourceHealth/unregister/action | Hebt die Registrierung des Abonnements für Microsoft ResourceHealth auf. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Resources/checkPolicyCompliance/action | Überprüft den Konformitätsstatus einer angegebenen Ressource anhand von Ressourcenrichtlinien. |
> | Aktion | Microsoft.Resources/checkResourceName/action | Dient zum Überprüfen der Gültigkeit des Ressourcennamens. |
> | Aktion | Microsoft.Resources/deployments/cancel/action | Bricht eine Bereitstellung ab. |
> | Aktion | Microsoft.Resources/deployments/delete | Löscht eine Bereitstellung. |
> | Aktion | Microsoft.Resources/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | Aktion | Microsoft.Resources/deployments/read | Ruft Bereitstellungen ab oder listet sie auf. |
> | Aktion | Microsoft.Resources/deployments/validate/action | Überprüft eine Bereitstellung. |
> | Aktion | Microsoft.Resources/deployments/whatIf/action | Sagt Änderungen an der Vorlagenbereitstellung voraus |
> | Aktion | Microsoft.Resources/deployments/write | Erstellt oder aktualisiert eine Bereitstellung. |
> | Aktion | Microsoft.Resources/links/delete | Löscht einen Ressourcenlink. |
> | Aktion | Microsoft.Resources/links/read | Ruft Ressourcenlinks ab oder listet sie auf. |
> | Aktion | Microsoft.Resources/links/write | Erstellt oder aktualisiert einen Ressourcenlink. |
> | Aktion | Microsoft.Resources/marketplace/purchase/action | Führt zum Erwerb einer Ressource im Marketplace. |
> | Aktion | Microsoft.Resources/providers/read | Dient zum Abrufen der Anbieterliste. |
> | Aktion | Microsoft.Resources/resources/read | Dient zum Abrufen einer gefilterten Ressourcenliste. |
> | Aktion | Microsoft.Resources/subscriptions/locations/read | Ruft die Liste mit den unterstützten Standorten ab. |
> | Aktion | Microsoft.Resources/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | Aktion | Microsoft.Resources/subscriptions/providers/read | Ruft Ressourcenanbieter ab oder listet sie auf. |
> | Aktion | Microsoft.Resources/subscriptions/read | Ruft die Abonnementliste ab. |
> | Aktion | Microsoft.Resources/subscriptions/resourceGroups/delete | Löscht eine Ressourcengruppe und alle dazugehörigen Ressourcen. |
> | Aktion | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | Aktion | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Ruft den Status von Bereitstellungsvorgängen ab oder listet ihn auf. |
> | Aktion | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Ruft Bereitstellungen ab oder listet sie auf. |
> | Aktion | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Erstellt oder aktualisiert eine Bereitstellung. |
> | Aktion | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Verschiebt Ressourcen aus einer Ressourcengruppe in eine andere. |
> | Aktion | Microsoft.Resources/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | Aktion | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Ruft die Ressourcen für die Ressourcengruppe ab. |
> | Aktion | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Überprüft das Verschieben von Ressourcen aus einer Ressourcengruppe in eine andere. |
> | Aktion | Microsoft.Resources/subscriptions/resourceGroups/write | Erstellt oder aktualisiert eine Ressourcengruppe. |
> | Aktion | Microsoft.Resources/subscriptions/resources/read | Ruft die Ressourcen eines Abonnements ab. |
> | Aktion | Microsoft.Resources/subscriptions/tagNames/delete | Löscht ein Abonnementtag. |
> | Aktion | Microsoft.Resources/subscriptions/tagNames/read | Ruft Abonnementtags ab oder listet sie auf. |
> | Aktion | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Löscht einen Abonnementtagwert. |
> | Aktion | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Ruft Abonnementtagwerte ab oder listet sie auf. |
> | Aktion | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Fügt einen Abonnementtagwert hinzu. |
> | Aktion | Microsoft.Resources/subscriptions/tagNames/write | Fügt ein Abonnementtag hinzu. |
> | Aktion | Microsoft.Resources/tags/delete | Entfernt alle Tags einer Ressource |
> | Aktion | Microsoft.Resources/tags/read | Ruft alle Tags für eine Ressource ab |
> | Aktion | Microsoft.Resources/tags/write | Aktualisiert die Tags einer Ressource durch Ersetzen oder Zusammenführen vorhandener Tags mit einem neuen Satz von Tags oder entfernt die vorhandenen Tags |
> | Aktion | Microsoft.Resources/tenants/read | Ruft die Mandantenliste ab. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Scheduler/jobcollections/delete | Löscht Auftragssammlungen. |
> | Aktion | Microsoft.Scheduler/jobcollections/disable/action | Deaktiviert Auftragssammlungen. |
> | Aktion | Microsoft.Scheduler/jobcollections/enable/action | Aktiviert Auftragssammlungen. |
> | Aktion | Microsoft.Scheduler/jobcollections/jobs/delete | Löscht Aufträge. |
> | Aktion | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Generiert Logik-App-Definitionen auf der Grundlage eines Scheduler-Auftrags. |
> | Aktion | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Ruft den Auftragsverlauf ab. |
> | Aktion | Microsoft.Scheduler/jobcollections/jobs/read | Ruft Aufträge ab. |
> | Aktion | Microsoft.Scheduler/jobcollections/jobs/run/action | Führt Aufträge aus. |
> | Aktion | Microsoft.Scheduler/jobcollections/jobs/write | Erstellt oder aktualisiert Aufträge. |
> | Aktion | Microsoft.Scheduler/jobcollections/read | Dient zum Abrufen von Auftragssammlungen. |
> | Aktion | Microsoft.Scheduler/jobcollections/write | Erstellt oder aktualisiert Auftragssammlungen. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Search/checkNameAvailability/action | Prüft die Verfügbarkeit des Dienstnamens. |
> | Aktion | Microsoft.Search/operations/read | Führt alle verfügbaren Vorgänge des Anbieters Microsoft.Search auf. |
> | Aktion | Microsoft.Search/register/action | Registriert das Abonnement für den Search-Ressourcenanbieter und ermöglicht die Erstellung von Suchdiensten. |
> | Aktion | Microsoft.Search/searchServices/createQueryKey/action | Erstellt den Abfrageschlüssel. |
> | Aktion | Microsoft.Search/searchServices/delete | Löscht den Suchdienst. |
> | Aktion | Microsoft.Search/searchServices/deleteQueryKey/delete | Löscht den Abfrageschlüssel. |
> | Aktion | Microsoft.Search/searchServices/listAdminKeys/action | Liest die Administratorschlüssel. |
> | Aktion | Microsoft.Search/searchServices/listQueryKeys/read | Gibt die Liste der API-Abfrageschlüssel für den angegebenen Azure Search-Dienst zurück. |
> | Aktion | Microsoft.Search/searchServices/read | Liest den Suchdienst. |
> | Aktion | Microsoft.Search/searchServices/regenerateAdminKey/action | Generiert die Administratorschlüssel neu. |
> | Aktion | Microsoft.Search/searchServices/start/action | Startet den Suchdienst. |
> | Aktion | Microsoft.Search/searchServices/stop/action | Beendet den Suchdienst. |
> | Aktion | Microsoft.Search/searchServices/write | Erstellt oder aktualisiert den Suchdienst. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Erzwingt die angegebenen Härtungsregeln für den Datenverkehr durch Erstellen passender Sicherheitsregeln in den angegebenen Netzwerksicherheitsgruppen |
> | Aktion | Microsoft.Security/adaptiveNetworkHardenings/read | Ruft Empfehlungen für das adaptive Erhöhen des Netzwerkschutzes für eine durch Azure geschützte Ressource ab |
> | Aktion | Microsoft.Security/advancedThreatProtectionSettings/read | Ruft die Einstellungen des erweiterten Schutzes vor Bedrohungen für die Ressource ab. |
> | Aktion | Microsoft.Security/advancedThreatProtectionSettings/write | Aktualisiert die Einstellungen des erweiterten Schutzes vor Bedrohungen für die Ressource. |
> | Aktion | Microsoft.Security/alerts/read | Ruft alle verfügbaren Sicherheitswarnungen ab. |
> | Aktion | Microsoft.Security/applicationWhitelistings/read | Ruft die Anwendungswhitelists ab. |
> | Aktion | Microsoft.Security/applicationWhitelistings/write | Erstellt eine neue Anwendungswhitelist oder aktualisiert eine bereits vorhandene. |
> | Aktion | Microsoft.Security/complianceResults/read | Ruft die Konformitätsergebnisse für die Ressource ab. |
> | Aktion | Microsoft.Security/informationProtectionPolicies/read | Ruft die Information Protection-Richtlinien für die Ressource ab. |
> | Aktion | Microsoft.Security/informationProtectionPolicies/write | Aktualisiert die Information Protection-Richtlinien für die Ressource. |
> | Aktion | Microsoft.Security/locations/alerts/activate/action | Aktiviert eine Sicherheitswarnung. |
> | Aktion | Microsoft.Security/locations/alerts/dismiss/action | Schließt eine Sicherheitswarnung. |
> | Aktion | Microsoft.Security/locations/alerts/read | Ruft alle verfügbaren Sicherheitswarnungen ab. |
> | Aktion | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Löscht die JIT-Netzwerkzugriffsrichtlinien. |
> | Aktion | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Löst eine Anforderung einer JIT-Netzwerkzugriffsrichtlinie aus. |
> | Aktion | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Ruft die JIT-Netzwerkzugriffsrichtlinien ab. |
> | Aktion | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Erstellt eine neue JIT-Netzwerkzugriffsrichtlinie oder aktualisiert eine bereits vorhandene. |
> | Aktion | Microsoft.Security/locations/read | Ruft den Speicherort der Sicherheitsdaten ab. |
> | Aktion | Microsoft.Security/locations/tasks/activate/action | Dient zum Aktivieren einer Sicherheitsempfehlung. |
> | Aktion | Microsoft.Security/locations/tasks/dismiss/action | Dient zum Verwerfen einer Sicherheitsempfehlung. |
> | Aktion | Microsoft.Security/locations/tasks/read | Ruft alle verfügbaren Sicherheitsempfehlungen ab. |
> | Aktion | Microsoft.Security/locations/tasks/resolve/action | Dient zum Auflösen einer Sicherheitsempfehlung. |
> | Aktion | Microsoft.Security/locations/tasks/start/action | Dient zum Starten einer Sicherheitsempfehlung. |
> | Aktion | Microsoft.Security/policies/read | Ruft die Sicherheitsrichtlinie ab. |
> | Aktion | Microsoft.Security/policies/write | Aktualisiert die Sicherheitsrichtlinie. |
> | Aktion | Microsoft.Security/pricings/delete | Löscht die Tarifeinstellungen für den Bereich. |
> | Aktion | Microsoft.Security/pricings/read | Ruft die Tarifeinstellungen für den Bereich ab. |
> | Aktion | Microsoft.Security/pricings/write | Aktualisiert die Tarifeinstellungen für den Bereich. |
> | Aktion | Microsoft.Security/register/action | Registriert das Abonnement für das Azure Security Center. |
> | Aktion | Microsoft.Security/securityContacts/delete | Löscht den Sicherheitskontakt. |
> | Aktion | Microsoft.Security/securityContacts/read | Ruft den Sicherheitskontakt ab. |
> | Aktion | Microsoft.Security/securityContacts/write | Aktualisiert den Sicherheitskontakt. |
> | Aktion | Microsoft.Security/securitySolutions/delete | Löscht eine Sicherheitslösung. |
> | Aktion | Microsoft.Security/securitySolutions/read | Ruft die Sicherheitslösungen ab. |
> | Aktion | Microsoft.Security/securitySolutions/write | Erstellt eine neue Sicherheitslösung oder aktualisiert eine bereits vorhandene. |
> | Aktion | Microsoft.Security/securitySolutionsReferenceData/read | Ruft die Verweisdaten für Sicherheitslösungen ab. |
> | Aktion | Microsoft.Security/securityStatuses/read | Ruft den Sicherheitsintegritätsstatus für Azure-Ressourcen ab. |
> | Aktion | Microsoft.Security/securityStatusesSummaries/read | Ruft die Zusammenfassungen der Sicherheitsstatus für den Bereich ab. |
> | Aktion | Microsoft.Security/settings/read | Ruft die Einstellungen für den Bereich ab. |
> | Aktion | Microsoft.Security/settings/write | Aktualisiert die Einstellungen für den Bereich. |
> | Aktion | Microsoft.Security/tasks/read | Ruft alle verfügbaren Sicherheitsempfehlungen ab. |
> | Aktion | Microsoft.Security/unregister/action | Hebt die Registrierung des Abonnements für das Azure Security Center auf. |
> | Aktion | Microsoft.Security/webApplicationFirewalls/delete | Löscht eine Web Application Firewall. |
> | Aktion | Microsoft.Security/webApplicationFirewalls/read | Ruft die Web Application Firewalls ab. |
> | Aktion | Microsoft.Security/webApplicationFirewalls/write | Erstellt eine neue Web Application Firewall oder aktualisiert eine bereits vorhandene. |
> | Aktion | Microsoft.Security/workspaceSettings/connect/action | Ändert die Einstellungen zum erneuten Herstellen einer Verbindung in den Arbeitsbereichseinstellungen. |
> | Aktion | Microsoft.Security/workspaceSettings/delete | Löscht die Arbeitsbereichseinstellungen. |
> | Aktion | Microsoft.Security/workspaceSettings/read | Ruft die Arbeitsbereichseinstellungen ab. |
> | Aktion | Microsoft.Security/workspaceSettings/write | Aktualisiert die Arbeitsbereichseinstellungen. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.SecurityGraph/diagnosticsettings/delete | Löscht eine Diagnoseeinstellung. |
> | Aktion | Microsoft.SecurityGraph/diagnosticsettings/read | Liest eine Diagnoseeinstellung. |
> | Aktion | Microsoft.SecurityGraph/diagnosticsettings/write | Schreibt eine Diagnoseeinstellung. |
> | Aktion | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Liest die Kategorien einer Diagnoseeinstellung. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ServiceBus/checkNameAvailability/action | Prüft die Verfügbarkeit des Namespaces unter dem angegebenen Abonnement. |
> | Aktion | Microsoft.ServiceBus/checkNamespaceAvailability/action | Prüft die Verfügbarkeit des Namespaces unter dem angegebenen Abonnement. Diese API ist veraltet. Verwenden Sie stattdessen CheckNameAvailability. |
> | Aktion | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Löscht die VNET-Regeln im ServiceBus-Ressourcenanbieter für das angegebene VNET. |
> | Aktion | Microsoft.ServiceBus/namespaces/authorizationRules/action | Dient zum Aktualisieren von Namespace-Autorisierungsregeln. Diese API ist veraltet. Verwenden Sie stattdessen einen PUT-Aufruf, um die Namespace-Autorisierungsregel zu aktualisieren. Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. |
> | Aktion | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Dient zum Löschen von Namespace-Autorisierungsregeln. Die standardmäßige Namespace-Autorisierungsregel kann nicht gelöscht werden.  |
> | Aktion | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Dient zum Abrufen der Verbindungszeichenfolge für den Namespace. |
> | Aktion | Microsoft.ServiceBus/namespaces/authorizationRules/read | Dient zum Abrufen der Liste mit Beschreibungen für Namespace-Autorisierungsregeln. |
> | Aktion | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Dient zum erneuten Generieren des primären oder sekundären Schlüssels für die Ressource. |
> | Aktion | Microsoft.ServiceBus/namespaces/authorizationRules/write | Dient zum Erstellen von Autorisierungsregeln auf Namespaceebene und zum Aktualisieren der dazugehörigen Eigenschaften. Aktualisiert werden können die Zugriffsrechte für Autorisierungsregeln sowie der primäre und der sekundäre Schlüssel. |
> | Aktion | Microsoft.ServiceBus/namespaces/Delete | Dient zum Löschen von Namespaceressourcen. |
> | Aktion | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Ruft die Schlüssel der Autorisierungsregeln für den primären Namespace für die Notfallwiederherstellung. |
> | Aktion | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Ruft die Autorisierungsregeln des primären Namespace für die Notfallwiederherstellung ab. |
> | Aktion | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Hiermit wird die Notfallwiederherstellung deaktiviert und die Replikation von Änderungen vom primären Namespace zu sekundären Namespaces beendet. |
> | Aktion | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Prüft die Verfügbarkeit des Namespacealias unter dem angegebenen Abonnement. |
> | Aktion | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Hiermit wird die dem Namespace zugeordnete Notfallwiederherstellungskonfiguration gelöscht. Dieser Vorgang kann nur über den primären Namespace aufgerufen werden. |
> | Aktion | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Hiermit wird ein Failover bei der georedundanten Notfallwiederherstellung ausgelöst und der Namespacealias neu konfiguriert, um auf den sekundären Namespace zu zeigen. |
> | Aktion | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Hiermit wird die dem Namespace zugeordnete Notfallwiederherstellungskonfiguration abgerufen. |
> | Aktion | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Hiermit wird die dem Namespace zugeordnete Notfallwiederherstellungskonfiguration erstellt oder aktualisiert. |
> | Aktion | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Löscht den Event Grid-Filter, der dem Namespace zugeordnet ist. |
> | Aktion | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Ruft den Event Grid-Filter ab, der dem Namespace zugeordnet ist. |
> | Aktion | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Erstellt oder aktualisiert den Event Grid-Filter, der dem Namespace zugeordnet ist. |
> | Aktion | Microsoft.ServiceBus/namespaces/eventhubs/read | Dient zum Abrufen einer Liste mit EventHub-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Löscht eine IP-Filterressource. |
> | Aktion | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Ruft eine IP-Filterressource ab. |
> | Aktion | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Erstellt eine IP-Filterressource. |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/browse/action | Durchsucht Nachrichten |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/defer/action | Verzögert Nachrichten |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Empfangen von Nachrichten |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/schedule/action | Plant Nachrichten |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Senden von Nachrichten |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/setstate/action | Legt den Sitzungsstatus fest |
> | Aktion | Microsoft.ServiceBus/namespaces/messagingPlan/read | Hiermit wird der Messagingplan für einen Namespace abgerufen.<br>Diese API ist veraltet.<br>Über die MessagingPlan-Ressource verfügbar gemachte Eigenschaften werden in höheren API-Versionen in die (übergeordnete) Namespaceressource verschoben.<br>Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. |
> | Aktion | Microsoft.ServiceBus/namespaces/messagingPlan/write | Hiermit wird der Messagingplan für einen Namespace aktualisiert.<br>Diese API ist veraltet.<br>Über die MessagingPlan-Ressource verfügbar gemachte Eigenschaften werden in höheren API-Versionen in die (übergeordnete) Namespaceressource verschoben.<br>Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. |
> | Aktion | Microsoft.ServiceBus/namespaces/migrate/action | Migriert den Namespacevorgang. |
> | Aktion | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Löscht die Migrationskonfiguration. |
> | Aktion | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Ruft die Migrationskonfiguration ab, die den Zustand der Migration und ausstehende Replikationsvorgänge angibt. |
> | Aktion | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Setzt die Migration vom Standard- zum Premium-Namespace zurück. |
> | Aktion | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Weist den dem Standard-Namespace zugeordneten DNS-Wert dem Premium-Namespace zu, wodurch die Migration abgeschlossen und die Ressourcensynchronisierung zwischen dem Standard- und dem Premium-Namespace beendet wird. |
> | Aktion | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Erstellt oder aktualisiert die Migrationskonfiguration. Dadurch wird die Synchronisierung der Ressourcen zwischen dem Standard- und dem Premium-Namespace ausgelöst. |
> | Aktion | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Löscht eine VNET-Regelressource. |
> | Aktion | Microsoft.ServiceBus/namespaces/networkrulesets/read | Ruft die NetworkRuleSet-Ressource ab |
> | Aktion | Microsoft.ServiceBus/namespaces/networkrulesets/write | Erstellt eine VNET-Regelressource. |
> | Aktion | Microsoft.ServiceBus/namespaces/operationresults/read | Hiermit wird der Status des Namespacevorgangs abgerufen. |
> | Aktion | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Dient zum Abrufen der Liste mit Ressourcenbeschreibungen für Namespacediagnoseeinstellungen. |
> | Aktion | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Dient zum Abrufen der Liste mit Ressourcenbeschreibungen für Namespacediagnoseeinstellungen. |
> | Aktion | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Dient zum Abrufen der Liste mit Ressourcenbeschreibungen für Namespaceprotokolle. |
> | Aktion | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Abrufen der Liste mit Ressourcenbeschreibungen für Namespacemetriken. |
> | Aktion | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Vorgang zum Aktualisieren von Queue. Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. Autorisierungsregeln. Verwenden Sie einen PUT-Aufruf, um die Autorisierungsregel zu aktualisieren. |
> | Aktion | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Vorgang zum Löschen von Warteschlangen-Autorisierungsregeln. |
> | Aktion | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Dient zum Abrufen der Warteschlangen-Verbindungszeichenfolge. |
> | Aktion | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Dient zum Abrufen der Liste mit Warteschlangen-Autorisierungsregeln. |
> | Aktion | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Dient zum erneuten Generieren des primären oder sekundären Schlüssels für die Ressource. |
> | Aktion | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Dient zum Erstellen von Warteschlangen-Autorisierungsregeln und zum Aktualisieren der dazugehörigen Eigenschaften. Die Zugriffsrechte für Autorisierungsregeln können aktualisiert werden. |
> | Aktion | Microsoft.ServiceBus/namespaces/queues/Delete | Vorgang zum Löschen von Warteschlangenressourcen. |
> | Aktion | Microsoft.ServiceBus/namespaces/queues/read | Dient zum Abrufen einer Liste mit Warteschlangen-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.ServiceBus/namespaces/queues/write | Dient zum Erstellen oder Aktualisieren von Warteschlangeneigenschaften. |
> | Aktion | Microsoft.ServiceBus/namespaces/read | Dient zum Abrufen der Liste mit Beschreibungen für Namespaceressourcen. |
> | Aktion | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Entfernt den ACS-Namespace. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Vorgang zum Aktualisieren von Topic. Dieser Vorgang wird nicht für die API-Version 2017-04-01 unterstützt. Autorisierungsregeln. Verwenden Sie einen PUT-Aufruf, um die Autorisierungsregel zu aktualisieren. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Vorgang zum Löschen von Themaautorisierungsregeln. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Dient zum Abrufen der Themaverbindungszeichenfolge. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Dient zum Abrufen der Liste mit Themaautorisierungsregeln. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Dient zum erneuten Generieren des primären oder sekundären Schlüssels für die Ressource. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Dient zum Erstellen von Themaautorisierungsregeln und zum Aktualisieren der dazugehörigen Eigenschaften. Die Zugriffsrechte für Autorisierungsregeln können aktualisiert werden. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/Delete | Vorgang zum Löschen von Themaressourcen. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/read | Dient zum Abrufen einer Liste mit Themaressourcenbeschreibungen. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Vorgang zum Löschen von TopicSubscription-Ressourcen. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Dient zum Abrufen einer Liste mit TopicSubscription-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Vorgang zum Löschen von Regelressourcen. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Dient zum Abrufen einer Liste mit Regelressourcenbeschreibungen. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Dient zum Erstellen oder Aktualisieren von Aktualisierungsregeleigenschaften. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Dient zum Erstellen oder Aktualisieren von TopicSubscription-Eigenschaften. |
> | Aktion | Microsoft.ServiceBus/namespaces/topics/write | Dient zum Erstellen oder Aktualisieren von Themaeigenschaften. |
> | Aktion | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Löscht eine VNET-Regelressource. |
> | Aktion | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Ruft eine VNET-Regelressource ab. |
> | Aktion | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Erstellt eine VNET-Regelressource. |
> | Aktion | Microsoft.ServiceBus/namespaces/write | Dient zum Erstellen einer Namespaceressource und zum Aktualisieren ihrer Eigenschaften. Die Eigenschaften für Markierungen und Kapazität des Namespace können aktualisiert werden. |
> | Aktion | Microsoft.ServiceBus/operations/read | Dient zum Abrufen von Vorgängen. |
> | Aktion | Microsoft.ServiceBus/register/action | Registriert das Abonnement für den ServiceBus-Ressourcenanbieter und ermöglicht die Erstellung von ServiceBus-Ressourcen. |
> | Aktion | Microsoft.ServiceBus/sku/read | Dient zum Abrufen einer Liste mit SKU-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.ServiceBus/sku/regions/read | Dient zum Abrufen einer Liste mit SkuRegions-Ressourcenbeschreibungen. |
> | Aktion | Microsoft.ServiceBus/unregister/action | Registriert das Abonnement für den ServiceBus-Ressourcenanbieter und ermöglicht die Erstellung von ServiceBus-Ressourcen. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.ServiceFabric/clusters/applications/delete | Hiermit löschen Sie eine Anwendung. |
> | Aktion | Microsoft.ServiceFabric/clusters/applications/read | Liest eine Anwendung. |
> | Aktion | Microsoft.ServiceFabric/clusters/applications/services/delete | Hiermit löschen Sie einen Dienst. |
> | Aktion | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Liest eine Partition. |
> | Aktion | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Liest ein Replikat. |
> | Aktion | Microsoft.ServiceFabric/clusters/applications/services/read | Liest einen Dienst. |
> | Aktion | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Liest einen Dienststatus. |
> | Aktion | Microsoft.ServiceFabric/clusters/applications/services/write | Hiermit erstellen oder aktualisieren Sie einen Dienst. |
> | Aktion | Microsoft.ServiceFabric/clusters/applications/write | Hiermit erstellen oder aktualisieren Sie eine Anwendung. |
> | Aktion | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Hiermit löschen Sie einen Anwendungstyp. |
> | Aktion | Microsoft.ServiceFabric/clusters/applicationTypes/read | Hiermit lesen Sie einen Anwendungstyp. |
> | Aktion | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Hiermit löschen Sie eine Anwendungstypversion. |
> | Aktion | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Hiermit lesen Sie eine Anwendungstypversion. |
> | Aktion | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Hiermit erstellen oder aktualisieren Sie eine Anwendungstypversion. |
> | Aktion | Microsoft.ServiceFabric/clusters/applicationTypes/write | Hiermit erstellen oder aktualisieren Sie einen Anwendungstyp. |
> | Aktion | Microsoft.ServiceFabric/clusters/delete | Löscht einen Cluster. |
> | Aktion | Microsoft.ServiceFabric/clusters/nodes/read | Liest einen Knoten. |
> | Aktion | Microsoft.ServiceFabric/clusters/read | Liest einen Cluster. |
> | Aktion | Microsoft.ServiceFabric/clusters/statuses/read | Liest einen Clusterstatus. |
> | Aktion | Microsoft.ServiceFabric/clusters/write | Erstellt oder aktualisiert einen Cluster. |
> | Aktion | Microsoft.ServiceFabric/locations/clusterVersions/read | Liest Clusterversionen. |
> | Aktion | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Hiermit lesen Sie eine Clusterversion für eine bestimmte Umgebung. |
> | Aktion | Microsoft.ServiceFabric/locations/operationresults/read | Hiermit lesen Sie Vorgangsergebnisse. |
> | Aktion | Microsoft.ServiceFabric/locations/operations/read | Hiermit lesen Sie Vorgänge nach Standort. |
> | Aktion | Microsoft.ServiceFabric/operations/read | Hiermit lesen Sie verfügbare Vorgänge. |
> | Aktion | Microsoft.ServiceFabric/register/action | Hiermit registrieren Sie eine Aktion. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.SignalRService/locations/checknameavailability/action | Überprüft, ob ein Name für die Verwendung mit einem neuen SignalR-Dienst verfügbar ist. |
> | Aktion | Microsoft.SignalRService/locations/operationresults/signalr/read | Fragt den Status eines asynchronen Vorgangs ab. |
> | Aktion | Microsoft.SignalRService/locations/operationstatus/operationId/read |  |
> | Aktion | Microsoft.SignalRService/locations/usages/read | Ruft den Kontingentbedarf für den Azure SignalR Service ab. |
> | Aktion | Microsoft.SignalRService/operationresults/read | Fragt den Status eines asynchronen Vorgangs ab. |
> | Aktion | Microsoft.SignalRService/operationstatus/read |  |
> | Aktion | Microsoft.SignalRService/register/action | Registriert den Ressourcenanbieter „Microsoft.SignalRService“ bei einem Abonnement. |
> | Aktion | Microsoft.SignalRService/SignalR/delete | Löscht den gesamten SignalR Service. |
> | Aktion | Microsoft.SignalRService/SignalR/listFeatures/read | Listet die angepassten FeatureFlags eines SignalR Service auf. Ausgenommen hiervon sind die Standardelemente. |
> | Aktion | Microsoft.SignalRService/SignalR/listkeys/action | Zeigt den Wert von SignalR-Zugriffsschlüsseln im Verwaltungsportal oder über die API an. |
> | Aktion | Microsoft.SignalRService/SignalR/read | Zeigt die SignalR-Einstellungen und Konfigurationen im Verwaltungsportal oder über die API an. |
> | Aktion | Microsoft.SignalRService/SignalR/regeneratekey/action | Ändert den Wert von SignalR-Zugriffsschlüsseln im Verwaltungsportal oder über die API. |
> | Aktion | Microsoft.SignalRService/SignalR/restart/action | Dient zum Neustarten eines Azure SignalR Service im Verwaltungsportal oder über die API. Hierbei kommt es zu einer Ausfallzeit. |
> | Aktion | Microsoft.SignalRService/SignalR/switchFeatures/action | Aktiviert bzw. deaktiviert SignalR FeatureFlags mit unterstützten Eigenschaften im Verwaltungsportal oder über die API. |
> | Aktion | Microsoft.SignalRService/SignalR/write | Bearbeitet die SignalR-Einstellungen und Konfigurationen im Verwaltungsportal oder über die API. |
> | Aktion | Microsoft.SignalRService/unregister/action | Hebt die Registrierung des Ressourcenanbieters „Microsoft.SignalRService“ bei einem Abonnement auf. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Hiermit werden die Anwendungsartefakte der Anwendungsdefinition aufgelistet. |
> | Aktion | Microsoft.Solutions/applicationDefinitions/delete | Hiermit wird eine Anwendungsdefinition entfernt. |
> | Aktion | Microsoft.Solutions/applicationDefinitions/read | Hiermit wird eine Liste mit Anwendungsdefinitionen abgerufen. |
> | Aktion | Microsoft.Solutions/applicationDefinitions/write | Hiermit wird eine Anwendungsdefinition hinzugefügt oder geändert. |
> | Aktion | Microsoft.Solutions/applications/applicationArtifacts/read | Hiermit werden Anwendungsartefakte aufgelistet. |
> | Aktion | Microsoft.Solutions/applications/delete | Hiermit wird eine Anwendung entfernt. |
> | Aktion | Microsoft.Solutions/applications/read | Hiermit wird eine Liste mit Anwendungen abgerufen. |
> | Aktion | Microsoft.Solutions/applications/refreshPermissions/action | Hiermit werden die Anwendungsberechtigungen aktualisiert. |
> | Aktion | Microsoft.Solutions/applications/updateAccess/action | Hiermit wird der Anwendungszugriff aktualisiert. |
> | Aktion | Microsoft.Solutions/applications/write | Erstellt eine Anwendung |
> | Aktion | Microsoft.Solutions/jitRequests/delete | Hiermit wird eine JitRequest entfernt. |
> | Aktion | Microsoft.Solutions/jitRequests/read | Hiermit wird eine Liste mit JitRequests abgerufen. |
> | Aktion | Microsoft.Solutions/jitRequests/write | Hiermit wird eine JitRequest erstellt. |
> | Aktion | Microsoft.Solutions/locations/operationStatuses/read | Liest den Vorgangsstatus für die Ressource. |
> | Aktion | Microsoft.Solutions/register/action | Hiermit registrieren Sie sich bei Lösungen. |
> | Aktion | Microsoft.Solutions/unregister/action | Hiermit wird die Registrierung von Lösungen aufgehoben. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Sql/checkNameAvailability/action | Überprüfen Sie, ob der angegebene Servername für die weltweite Bereitstellung bei einem bestimmten Abonnement verfügbar ist. |
> | Aktion | Microsoft.Sql/instancePools/delete | Löscht einen Instanzenpool. |
> | Aktion | Microsoft.Sql/instancePools/read | Ruft einen Instanzenpool ab. |
> | Aktion | Microsoft.Sql/instancePools/usages/read | Ruft die Nutzungsinformationen für einen Instanzenpool ab. |
> | Aktion | Microsoft.Sql/instancePools/write | Dient zum Erstellen oder Aktualisieren eines Instanzenpools. |
> | Aktion | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Dient zum Abrufen des Ergebnisses des Set-Vorgangs der Richtlinie für die erweiterte Serverblobüberwachung. |
> | Aktion | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Dient zum Abrufen des Ergebnisses des Set-Vorgangs der Richtlinie für die Serverblobüberwachung. |
> | Aktion | Microsoft.Sql/locations/capabilities/read | Ruft die Funktionen für dieses Abonnement in einem bestimmten Standort ab. |
> | Aktion | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Ruft den Status eines Datenbankvorgangs ab. |
> | Aktion | Microsoft.Sql/locations/databaseOperationResults/read | Ruft den Status eines Datenbankvorgangs ab. |
> | Aktion | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Ruft gegenwärtig ausgeführte Vorgänge für gelöschte Server ab. |
> | Aktion | Microsoft.Sql/locations/deletedServerOperationResults/read | Ruft gegenwärtig ausgeführte Vorgänge für gelöschte Server ab. |
> | Aktion | Microsoft.Sql/locations/deletedServers/read | Gibt die Liste der gelöschten Server zurück oder ruft die Eigenschaften für den angegebenen gelöschten Server ab. |
> | Aktion | Microsoft.Sql/locations/deletedServers/recover/action | Stellt einen gelöschten Server wieder her. |
> | Aktion | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Ruft den asynchronen Vorgang in Azure für einen asynchronen Vorgang in einem Pool für elastische Datenbank ab. |
> | Aktion | Microsoft.Sql/locations/elasticPoolOperationResults/read | Ruft das Ergebnis eines Vorgangs eines Pools für elastische Datenbanken ab. |
> | Aktion | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Ruft gegenwärtig ausgeführte Vorgänge für die Verschlüsselungsschutzvorrichtung von Transparent Data Encryption ab. |
> | Aktion | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Ruft gegenwärtig ausgeführte Vorgänge für die Verschlüsselungsschutzvorrichtung von Transparent Data Encryption ab. |
> | Aktion | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Dient zum Abrufen des Ergebnisses des Set-Vorgangs der Richtlinie für die erweiterte Serverblobüberwachung. |
> | Aktion | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Dient zum Abrufen des Ergebnisses des Set-Vorgangs der Richtlinie für die erweiterte Serverblobüberwachung. |
> | Aktion | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Ruft den Status eines Firewallregelvorgangs ab. |
> | Aktion | Microsoft.Sql/locations/firewallRulesOperationResults/read | Ruft den Status eines Firewallregelvorgangs ab. |
> | Aktion | Microsoft.Sql/locations/instanceFailoverGroups/delete | Löscht eine vorhandene Failovergruppe einer Instanz. |
> | Aktion | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Führt das geplante Failover in einer vorhandenen Failovergruppe einer Instanz durch. |
> | Aktion | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Führt das erzwungene Failover in einer vorhandenen Failovergruppe einer Instanz durch. |
> | Aktion | Microsoft.Sql/locations/instanceFailoverGroups/read | Gibt die Liste der Failovergruppen einer Instanz zurück oder ruft die Eigenschaften für die angegebene Failovergruppe einer Instanz ab. |
> | Aktion | Microsoft.Sql/locations/instanceFailoverGroups/write | Erstellt eine Failovergruppe einer Instanz mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Tags für die angegebene Failovergruppe einer Instanz. |
> | Aktion | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Ruft den Status eines Instanzenpoolvorgangs ab. |
> | Aktion | Microsoft.Sql/locations/instancePoolOperationResults/read | Ruft das Ergebnis eines Instanzenpoolvorgangs ab. |
> | Aktion | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Gibt die Details eines asynchronen Azure-Vorgangs für einen bestimmten Schnittstellenendpunkt zurück. |
> | Aktion | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Gibt die Details des angegebenen Schnittstellenendpunkt-Vorgangs zurück. |
> | Aktion | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Ruft den Status eines Auftrags-Agent-Vorgangs ab. |
> | Aktion | Microsoft.Sql/locations/jobAgentOperationResults/read | Ruft die Ergebnisse eines Auftrags-Agent-Vorgangs ab. |
> | Aktion | Microsoft.Sql/locations/longTermRetentionBackups/read | Listet die Sicherungen zur langfristigen Aufbewahrung für jede Datenbank auf jedem Server an einem Speicherort auf. |
> | Aktion | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Listet die Sicherungen zur langfristigen Aufbewahrung für jede Datenbank auf jedem Server auf. |
> | Aktion | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Löscht eine Sicherung zur langfristigen Aufbewahrung. |
> | Aktion | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Listet die Sicherungen zur langfristigen Aufbewahrung für eine Datenbank auf. |
> | Aktion | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Führt den Vorgang zur Wiederherstellung einer verwalteten Datenbank durch. |
> | Aktion | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Ruft gegenwärtig ausgeführte Vorgänge für die Verschlüsselungsschutzvorrichtung der verwalteten Instanz von Transparent Data Encryption ab. |
> | Aktion | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Ruft gegenwärtig ausgeführte Vorgänge für die Verschlüsselungsschutzvorrichtung der verwalteten Instanz von Transparent Data Encryption ab. |
> | Aktion | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Ruft gegenwärtig ausgeführte Vorgänge für verwaltete Instanzenschlüssel von Transparent Data Encryption ab. |
> | Aktion | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Ruft gegenwärtig ausgeführte Vorgänge für verwaltete Instanzenschlüssel von Transparent Data Encryption ab. |
> | Aktion | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Ruft gegenwärtig ausgeführte Vorgänge für Transparent Data Encryption einer verwalteten Datenbank ab. |
> | Aktion | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Ruft gegenwärtig ausgeführte Vorgänge für Transparent Data Encryption einer verwalteten Datenbank ab. |
> | Aktion | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Ruft das Ergebnis für einen Vorgang auf dem Verbindungsproxy eines privaten Endpunkts ab |
> | Aktion | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Ruft das Ergebnis für einen Vorgang auf dem Verbindungsproxy eines privaten Endpunkts ab |
> | Aktion | Microsoft.Sql/locations/read | Ruft die verfügbaren Standorte für ein bestimmtes Abonnement ab. |
> | Aktion | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Ruft gegenwärtig ausgeführte Vorgänge für Serverschlüssel von Transparent Data Encryption ab. |
> | Aktion | Microsoft.Sql/locations/serverKeyOperationResults/read | Ruft gegenwärtig ausgeführte Vorgänge für Serverschlüssel von Transparent Data Encryption ab. |
> | Aktion | Microsoft.Sql/locations/syncAgentOperationResults/read | Ruft das Ergebnis des Ressourcenvorgangs des Synchronisierungs-Agents ab. |
> | Aktion | Microsoft.Sql/locations/syncDatabaseIds/read | Ruft die Synchronisierungsdatenbank-IDs für eine bestimmte Region und ein bestimmtes Abonnement ab. |
> | Aktion | Microsoft.Sql/locations/syncGroupOperationResults/read | Ruft das Ergebnis des Ressourcenvorgangs der Synchronisierungsgruppe ab. |
> | Aktion | Microsoft.Sql/locations/syncMemberOperationResults/read | Ruft das Ergebnis des Ressourcenvorgangs des Synchronisierungsmitglieds ab. |
> | Aktion | Microsoft.Sql/locations/usages/read | Ruft eine Collection von Nutzungsmetriken für dieses Abonnement an einem Standort ab. |
> | Aktion | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Gibt die Details des asynchronen Vorgangs in Azure für die angegebene VNET-Regel zurück.  |
> | Aktion | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Gibt die Details der VNET-Regeln zurück.  |
> | Aktion | Microsoft.Sql/managedInstances/administrators/delete | Löscht einen vorhandenen Administrator einer verwalteten Instanz. |
> | Aktion | Microsoft.Sql/managedInstances/administrators/read | Ruft eine Liste der Administratoren für verwaltete Instanzen ab. |
> | Aktion | Microsoft.Sql/managedInstances/administrators/write | Erstellt oder aktualisiert den Administrator für verwaltete Instanzen mit den angegebenen Parametern. |
> | Aktion | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Ruft eine kurzfristige Aufbewahrungsrichtlinie für eine verwaltete Datenbank ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Aktualisiert eine kurzfristige Aufbewahrungsrichtlinie für eine verwaltete Datenbank. |
> | Aktion | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Listet die Vertraulichkeitsbezeichnungen einer bestimmten Datenbank auf. |
> | Aktion | Microsoft.Sql/managedInstances/databases/delete | Löscht eine vorhandene verwaltete Datenbank. |
> | Aktion | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Ruft die verfügbaren Protokolle für Datenbanken der verwalteten Instanz ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/read | Ruft eine vorhandene verwaltete Datenbank ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Listet die Vertraulichkeitsbezeichnungen einer bestimmten Datenbank auf. |
> | Aktion | Microsoft.Sql/managedInstances/databases/schemas/read | Ruft ein verwaltetes Datenbankschema ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Ruft eine verwaltete Datenbankspalte ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Löscht die Vertraulichkeitsbezeichnung einer bestimmten Spalte. |
> | Aktion | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Deaktiviert die Vertraulichkeitsempfehlungen für eine bestimmte Spalte. |
> | Aktion | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Aktiviert die Vertraulichkeitsempfehlungen für eine bestimmte Spalte. |
> | Aktion | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Ruft die Vertraulichkeitsbezeichnung einer bestimmten Spalte ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Erstellt oder aktualisiert die Vertraulichkeitsbezeichnung einer bestimmten Spalte. |
> | Aktion | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Ruft eine verwaltete Datenbanktabelle ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Ruft eine Liste der Bedrohungserkennungrichtlinien für verwaltete Datenbanken ab, die für einen bestimmten Server konfiguriert sind. |
> | Aktion | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Dient zum Ändern der Bedrohungserkennungsrichtlinie für eine bestimmte verwaltete Datenbank. |
> | Aktion | Microsoft.Sql/managedInstances/databases/securityEvents/read | Ruft die Sicherheitsereignisse für die verwaltete Datenbank ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Listet die Vertraulichkeitsbezeichnungen einer bestimmten Datenbank auf. |
> | Aktion | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Ruft Details zu Datenbank-TDE (Transparent Data Encryption) für eine bestimmte verwaltete Datenbank ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Ändert Transparent Data Encryption für Datenbanken für eine bestimmte verwaltete Datenbank. |
> | Aktion | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Entfernt die Sicherheitsrisikobewertung für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Ruft die Sicherheitsrisikobewertungs-Richtlinien für eine angegebene Datenbank ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Entfernt die Baseline für die Regel für die Sicherheitsrisikobewertung für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Ruft das Baseline für die Regel für die Sicherheitsrisikobewertung für eine bestimmte Datenbank ab. |
> | Aktion | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Ändert die Baseline für die Regel für die Sicherheitsrisikobewertung für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Konvertiert ein vorhandenes Überprüfungsergebnis in ein lesbares Format. Wenn dies bereits der Fall ist, werden keine Vorgänge ausgeführt. |
> | Aktion | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Dient zum Ausführen einer Datenbanküberprüfung zur Bewertung von Sicherheitsrisiken. |
> | Aktion | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Gibt die Liste der Überprüfungsdatensätze für eine Datenbank-Sicherheitsrisikobewertung oder den Überprüfungsdatensatz für die angegebene Überprüfungs-ID zurück. |
> | Aktion | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Ändert die Sicherheitsrisikobewertung für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/managedInstances/databases/write | Erstellt eine neue Datenbank oder aktualisiert eine bereits vorhandene Datenbank |
> | Aktion | Microsoft.Sql/managedInstances/delete | Löscht eine vorhandene verwaltete Instanz. |
> | Aktion | Microsoft.Sql/managedInstances/encryptionProtector/read | Gibt eine Liste von Schutzvorrichtungen zur Serververschlüsselung zurück oder ruft die Eigenschaften für die angegebene Schutzvorrichtung zur Serververschlüsselung ab. |
> | Aktion | Microsoft.Sql/managedInstances/encryptionProtector/write | Aktualisiert die Eigenschaften für die angegebene Schutzvorrichtung zur Serververschlüsselung. |
> | Aktion | Microsoft.Sql/managedInstances/keys/delete | Löscht einen vorhandenen Schlüssel für die verwaltete Azure SQL-Datenbank-Instanz. |
> | Aktion | Microsoft.Sql/managedInstances/keys/read | Gibt die Liste mit den verwalteten Instanzschlüsseln zurück oder ruft die Eigenschaften für den angegebenen Schlüssel der verwalteten Instanz ab. |
> | Aktion | Microsoft.Sql/managedInstances/keys/write | Erstellt einen Schlüssel mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für den Schlüssel der verwalteten Instanz. |
> | Aktion | Microsoft.Sql/managedInstances/metricDefinitions/read | Ruft Metrikdefinitionen der verwalteten Instanz ab. |
> | Aktion | Microsoft.Sql/managedInstances/metrics/read | Ruft Metriken der verwalteten Instanz ab. |
> | Aktion | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Ruft die verfügbaren Protokolle für verwaltete Instanzen ab. |
> | Aktion | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Zurückgeben verfügbarer Metriken für verwaltete Instanzen. |
> | Aktion | Microsoft.Sql/managedInstances/read | Gibt die Liste der verwalteten Instanzen zurück oder ruft die Eigenschaften für die angegebene verwaltete Instanz ab. |
> | Aktion | Microsoft.Sql/managedInstances/recoverableDatabases/read | Gibt eine Liste mit wiederherstellbaren verwalteten Datenbanken zurück. |
> | Aktion | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Ruft eine kurzfristige Aufbewahrungsrichtlinie für eine gelöschte verwaltete Datenbank ab. |
> | Aktion | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Aktualisiert eine kurzfristige Aufbewahrungsrichtlinie für eine gelöschte verwaltete Datenbank. |
> | Aktion | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Gibt eine Liste der wiederherstellbaren gelöschten verwalteten Datenbanken zurück. |
> | Aktion | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Ruft eine Liste der Bedrohungserkennungrichtlinien für verwaltete Server ab, die für einen bestimmten Server konfiguriert sind. |
> | Aktion | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Dient zum Ändern der Richtlinie zur Bedrohungserkennung bei verwalteten Servern für einen bestimmten verwalteten Server. |
> | Aktion | Microsoft.Sql/managedInstances/tdeCertificates/action | Erstellt oder aktualisiert das TDE-Zertifikat. |
> | Aktion | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Entfernt die Sicherheitsrisikobewertung für eine bestimmte verwaltete Instanz. |
> | Aktion | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Ruft die Sicherheitsrisikobewertungs-Richtlinien für eine angegebene verwaltete Instanz ab. |
> | Aktion | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Ändert die Sicherheitsrisikobewertung für eine bestimmte verwaltete Instanz. |
> | Aktion | Microsoft.Sql/managedInstances/write | Erstellt eine verwaltete Instanz mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für die verwaltete Instanz. |
> | Aktion | Microsoft.Sql/operations/read | Ruft die verfügbaren REST-Vorgänge ab. |
> | Aktion | Microsoft.Sql/register/action | Registriert das Abonnement für den Microsoft SQL-Datenbankressourcenanbieter und ermöglicht die Erstellung von Microsoft SQL-Datenbanken. |
> | Aktion | Microsoft.Sql/servers/administratorOperationResults/read | Ruft gegenwärtig ausgeführte Vorgänge für Serveradministratoren ab. |
> | Aktion | Microsoft.Sql/servers/administrators/delete | Löscht Serveradministratoren. |
> | Aktion | Microsoft.Sql/servers/administrators/read | Dient zum Abrufen von Serveradministratordetails. |
> | Aktion | Microsoft.Sql/servers/administrators/write | Dient zum Erstellen oder Aktualisieren von Serveradministratoren. |
> | Aktion | Microsoft.Sql/servers/advisors/read | Gibt eine Liste mit verfügbaren Ratgebern für den Server zurück. |
> | Aktion | Microsoft.Sql/servers/advisors/recommendedActions/read | Gibt eine Liste mit empfohlenen Aktionen des angegebenen Ratgebers für den Server zurück. |
> | Aktion | Microsoft.Sql/servers/advisors/recommendedActions/write | Dient zum Anwenden der empfohlenen Aktion auf den Server. |
> | Aktion | Microsoft.Sql/servers/advisors/write | Aktualisiert den Status der automatischen Ausführung eines Ratgebers auf der Serverebene. |
> | Aktion | Microsoft.Sql/servers/auditingPolicies/read | Dient zum Abrufen von Details zur Richtlinie für die Standardservertabellen-Überwachung, die für einen bestimmten Server konfiguriert ist. |
> | Aktion | Microsoft.Sql/servers/auditingPolicies/write | Dient zum Ändern der Standardservertabellen-Überwachung für einen bestimmten Server. |
> | Aktion | Microsoft.Sql/servers/auditingSettings/operationResults/read | Dient zum Abrufen des Ergebnisses des Set-Vorgangs der Richtlinie für die Serverblobüberwachung. |
> | Aktion | Microsoft.Sql/servers/auditingSettings/read | Dient zum Abrufen von Details zur Richtlinie für die Serverblobüberwachung, die für einen bestimmten Server konfiguriert ist. |
> | Aktion | Microsoft.Sql/servers/auditingSettings/write | Dient zum Ändern der Serverblobüberwachung für einen bestimmten Server. |
> | Aktion | Microsoft.Sql/servers/automaticTuning/read | Gibt die Einstellungen zur automatischen Optimierung für den Server zurück. |
> | Aktion | Microsoft.Sql/servers/automaticTuning/write | Aktualisiert die Einstellungen zur automatischen Optimierung für den Server und gibt die aktualisierten Einstellungen zurück. |
> | Aktion | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Löscht einen vorhandenen Sicherungsarchivtresor. |
> | Aktion | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Vorgang zum Abrufen eines Sicherungstresors für die langfristige Aufbewahrung. Gibt Informationen zu dem Tresor zurück, der bei diesem Server registriert ist. |
> | Aktion | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Dieser Vorgang wird zum Registrieren eines Sicherungstresors für die langfristige Aufbewahrung bei einem Server verwendet. |
> | Aktion | Microsoft.Sql/servers/communicationLinks/delete | Löscht eine vorhandene Serverkommunikationsverbindung. |
> | Aktion | Microsoft.Sql/servers/communicationLinks/read | Gibt die Liste der Kommunikationsverbindungen eines bestimmten Servers zurück. |
> | Aktion | Microsoft.Sql/servers/communicationLinks/write | Erstellt oder aktualisiert eine Serverkommunikationsverbindung. |
> | Aktion | Microsoft.Sql/servers/connectionPolicies/read | Gibt die Liste der Serververbindungsrichtlinien eines angegebenen Servers zurück. |
> | Aktion | Microsoft.Sql/servers/connectionPolicies/write | Erstellt oder aktualisiert eine Serververbindungsrichtlinie. |
> | Aktion | Microsoft.Sql/servers/databases/advisors/read | Gibt eine Liste mit verfügbaren Ratgebern für die Datenbank zurück. |
> | Aktion | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Gibt eine Liste mit empfohlenen Aktionen des angegebenen Ratgebers für die Datenbank zurück. |
> | Aktion | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Dient zum Anwenden der empfohlenen Aktion auf die Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/advisors/write | Dient zum Aktualisieren des Status der automatischen Ausführung eines Ratgebers auf der Datenbankebene. |
> | Aktion | Microsoft.Sql/servers/databases/auditingPolicies/read | Dient zum Abrufen von Details zur Tabellenüberwachungsrichtlinie, die für eine bestimmte Datenbank konfiguriert ist. |
> | Aktion | Microsoft.Sql/servers/databases/auditingPolicies/write | Dient zum Ändern der Tabellenüberwachungsrichtlinie für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/auditingSettings/read | Dient zum Abrufen von Details zur Blobüberwachungsrichtlinie, die für eine bestimmte Datenbank konfiguriert ist. |
> | Aktion | Microsoft.Sql/servers/databases/auditingSettings/write | Dient zum Ändern der Blobüberwachungsrichtlinie für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/auditRecords/read | Dient zum Abrufen der Datensätze für die Datenbankblobüberwachung. |
> | Aktion | Microsoft.Sql/servers/databases/automaticTuning/read | Gibt die Einstellungen zur automatischen Optimierung für eine Datenbank zurück. |
> | Aktion | Microsoft.Sql/servers/databases/automaticTuning/write | Aktualisiert die Einstellungen zur automatischen Optimierung für eine Datenbank und gibt die aktualisierten Einstellungen zurück. |
> | Aktion | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Ruft den Status eines Datenbankvorgangs ab. |
> | Aktion | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Gibt die Liste der Sicherungsarchivierungsrichtlinien einer angegebenen Datenbank zurück. |
> | Aktion | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Erstellt oder aktualisiert eine Datenbanksicherungs-Archivierungsrichtlinie. |
> | Aktion | Microsoft.Sql/servers/databases/connectionPolicies/read | Dient zum Abrufen von Details zur Verbindungsrichtlinie, die für eine bestimmte Datenbank konfiguriert ist. |
> | Aktion | Microsoft.Sql/servers/databases/connectionPolicies/write | Dient zum Ändern der Verbindungsrichtlinie für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Listet die Vertraulichkeitsbezeichnungen einer bestimmten Datenbank auf. |
> | Aktion | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Gibt die Liste der Richtlinien zur Maskierung von Datenbankdaten zurück. |
> | Aktion | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Dient zum Löschen von Datenmaskierungsrichtlinien-Regeln für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Dient zum Abrufen von Details zur Datenmaskierungsrichtlinien-Regel, die für eine bestimmte Datenbank konfiguriert ist. |
> | Aktion | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Dient zum Ändern von Datenmaskierungsrichtlinen-Regeln für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Dient zum Ändern der Datenmaskierungsrichtlinie für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Gibt die verteilten Abfrageschrittinformationen von Data Warehouse-Abfragen für die ausgewählte Schritt-ID zurück. |
> | Aktion | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Gibt die Data Warehouse-Verteilungsabfrageinformationen für die ausgewählte Abfrage-ID zurück. |
> | Aktion | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Ruft die Benutzeraktivitäten einer SQL Data Warehouse-Instanz ab, einschließlich ausgeführte und angehaltene Abfragen. |
> | Aktion | Microsoft.Sql/servers/databases/delete | Löscht eine vorhandene Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/export/action | Exportiert Azure SQL-Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Dient zum Abrufen von Details zur erweiterten Blobüberwachungsrichtlinie, die für eine bestimmte Datenbank konfiguriert ist. |
> | Aktion | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Dient zum Ändern der erweiterten Blobüberwachungsrichtlinie für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/extensions/read | Ruft eine Collection von Erweiterungen für die Datenbank ab. |
> | Aktion | Microsoft.Sql/servers/databases/extensions/write | Ändert die Erweiterung für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Ruft Richtlinien für die Geosicherung einer bestimmten Datenbank ab. |
> | Aktion | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Erstellt oder aktualisiert eine Richtlinie für die Geosicherung einer Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/importExportOperationResults/read | Ruft gegenwärtig ausgeführte Import-/Exportvorgänge ab. |
> | Aktion | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Ruft eine Liste mit den verfügbaren Wartungsfenstern für eine ausgewählte Datenbank ab. |
> | Aktion | Microsoft.Sql/servers/databases/maintenanceWindows/read | Ruft Wartungsfenstereinstellungen für eine ausgewählte Datenbank ab. |
> | Aktion | Microsoft.Sql/servers/databases/maintenanceWindows/write | Legt Wartungsfenstereinstellungen für eine ausgewählte Datenbank fest. |
> | Aktion | Microsoft.Sql/servers/databases/metricDefinitions/read | Dient zum Zurückgeben verfügbarer Metriktypen für Datenbanken. |
> | Aktion | Microsoft.Sql/servers/databases/metrics/read | Gibt Metriken für Datenbanken zurück. |
> | Aktion | Microsoft.Sql/servers/databases/move/action | Ändert den Namen einer vorhandenen Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/operationResults/read | Ruft den Status eines Datenbankvorgangs ab. |
> | Aktion | Microsoft.Sql/servers/databases/operations/cancel/action | Bricht einen ausstehenden asynchronen Vorgang in Azure SQL-Datenbank ab, der noch nicht abgeschlossen ist. |
> | Aktion | Microsoft.Sql/servers/databases/operations/read | Gibt die Liste der Vorgänge zurück, die für die Datenbank ausgeführt werden. |
> | Aktion | Microsoft.Sql/servers/databases/pause/action | Hält die Azure SQL Data Warehouse-Datenbank an. |
> | Aktion | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Ruft die verfügbaren Protokolle für Datenbanken ab. |
> | Aktion | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Zurückgeben verfügbarer Metriktypen für Datenbanken. |
> | Aktion | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Gibt die Collection von Abfragetexten zurück, die den angegebenen Parametern entsprechen. |
> | Aktion | Microsoft.Sql/servers/databases/queryStore/read | Gibt die aktuellen Werte von Abfragespeichereinstellungen für die Datenbank zurück. |
> | Aktion | Microsoft.Sql/servers/databases/queryStore/write | Aktualisiert die Abfragespeichereinstellung für die Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/read | Gibt die Liste der Datenbanken zurück oder ruft die Eigenschaften für die angegebene Datenbank ab. |
> | Aktion | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Listet die Vertraulichkeitsbezeichnungen einer bestimmten Datenbank auf. |
> | Aktion | Microsoft.Sql/servers/databases/replicationLinks/delete | Dient zum Erzwingen der Beendigung der Replikationsbeziehung mit potenziellem Datenverlust. |
> | Aktion | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Dient zum Ausführen eines Failovers nach der Synchronisierung aller Änderungen der primären Datenbank. Dadurch wird diese Datenbank zur primären Datenbank der Replikationsbeziehung, und die primäre Remotedatenbank wird zur sekundären Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Dient zum Ausführen eines sofortigen Failovers mit potenziellem Datenverlust. Dadurch wird diese Datenbank zur primären Datenbank der Replikationsbeziehung, und die primäre Remotedatenbank wird zur sekundären Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/replicationLinks/read | Gibt die Liste mit den Replikationslinks zurück oder ruft die Eigenschaften für die angegebenen Replikationslinks ab. |
> | Aktion | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Dient zum Beenden der Replikationsbeziehung – entweder erzwungen oder nach der Synchronisierung mit dem Partner. |
> | Aktion | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Dient zum Aktualisieren des Replikationsmodus für die Verknüpfung auf den synchronen oder asynchronen Modus. |
> | Aktion | Microsoft.Sql/servers/databases/restorePoints/action | Erstellt einen neuen Wiederherstellungspunkt. |
> | Aktion | Microsoft.Sql/servers/databases/restorePoints/delete | Löscht den Wiederherstellungspunkt der Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/restorePoints/read | Gibt Wiederherstellungspunkte für die Datenbank zurück. |
> | Aktion | Microsoft.Sql/servers/databases/resume/action | Setzt die Azure SQL Data Warehouse-Datenbank fort. |
> | Aktion | Microsoft.Sql/servers/databases/schemas/read | Ruft ein Datenbankschema ab. |
> | Aktion | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Ruft eine Datenbankspalte ab. |
> | Aktion | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Löscht die Vertraulichkeitsbezeichnung einer bestimmten Spalte. |
> | Aktion | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Deaktiviert die Vertraulichkeitsempfehlungen für eine bestimmte Spalte. |
> | Aktion | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Aktiviert die Vertraulichkeitsempfehlungen für eine bestimmte Spalte. |
> | Aktion | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Ruft die Vertraulichkeitsbezeichnung einer bestimmten Spalte ab. |
> | Aktion | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Erstellt oder aktualisiert die Vertraulichkeitsbezeichnung einer bestimmten Spalte. |
> | Aktion | Microsoft.Sql/servers/databases/schemas/tables/read | Abrufen einer Datentabelle. |
> | Aktion | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Dient zum Abrufen einer Liste mit Indexempfehlungen für eine Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Dient zum Anwenden von Indexempfehlungen. |
> | Aktion | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Ruft eine Liste der Bedrohungserkennungrichtlinien für Datenbanken ab, die für einen bestimmten Server konfiguriert sind. |
> | Aktion | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Ändert die Bedrohungserkennungsrichtlinie für eine angegebene Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/securityMetrics/read | Ruft eine Collection von Metriken für Datenbanksicherheit ab. |
> | Aktion | Microsoft.Sql/servers/databases/sensitivityLabels/read | Listet die Vertraulichkeitsbezeichnungen einer bestimmten Datenbank auf. |
> | Aktion | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Gibt Vorschläge zum zentralen Hoch- oder Herunterskalieren von Datenbanken auf der Grundlage von Abfrageausführungsstatistiken zurück, um die Leistung zu verbessern oder die Kosten zu verringern. |
> | Aktion | Microsoft.Sql/servers/databases/skus/read | Ruft eine Collection von SKUs ab, die für eine Datenbank verfügbar ist. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Bricht die Synchronisierung von Synchronisierungsgruppen ab. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/delete | Löscht eine vorhandene Synchronisierungsgruppe. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Gibt die Liste der Datenbankschemas für Synchronisierungshubs zurück. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/logs/read | Gibt die Liste der Synchronisierungsgruppenprotokolle zurück. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/read | Gibt die Liste der Synchronisierungsgruppen zurück oder ruft die Eigenschaften für die angegebene Synchronisierungsgruppe ab. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Aktualisiert das Datenbankschema des Synchronisierungshubs. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Ruft das Ergebnis des Aktualisierungsvorgangs für das Schema des Synchronisierungshubs ab. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Löscht ein vorhandenes Synchronisierungsmitglied. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Gibt die Liste der Synchronisierungsmitglieder zurück oder ruft die Eigenschaften für das angegebene Synchronisierungsmitglied ab. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Aktualisiert das Schema für das Synchronisierungsmitglied. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Ruft das Ergebnis des Aktualisierungsvorgangs für das Schema für das Synchronisierungsmitglied ab. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Gibt die Liste von Schemas für Synchronisierungsmitglieder zurück. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Erstellt ein Synchronisierungsmitglied mit den angegebenen Parametern oder aktualisiert die Eigenschaften für das angegebene Synchronisierungsmitglied. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Löst die Synchronisierung für die Synchronisierungsgruppe aus. |
> | Aktion | Microsoft.Sql/servers/databases/syncGroups/write | Erstellt eine Synchronisierungsgruppe mit den angegebenen Parametern oder aktualisiert die Eigenschaften für die angegebene Synchronisierungsgruppe. |
> | Aktion | Microsoft.Sql/servers/databases/topQueries/queryText/action | Gibt den Transact-SQL-Text für die ausgewählte Abfrage-ID zurück. |
> | Aktion | Microsoft.Sql/servers/databases/topQueries/read | Gibt aggregierte Laufzeitstatistiken für die ausgewählte Abfrage im ausgewählten Zeitraum zurück. |
> | Aktion | Microsoft.Sql/servers/databases/topQueries/statistics/read | Gibt aggregierte Laufzeitstatistiken für die ausgewählte Abfrage im ausgewählten Zeitraum zurück. |
> | Aktion | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Ruft gegenwärtig ausgeführte Vorgänge für Transparent Data Encryption ab. |
> | Aktion | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Dient zum Abrufen von Status und Details des Transparent Data Encryption-Sicherheitsfeatures für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Ändert den Status von Transparent Data Encryption. |
> | Aktion | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Führt ein Upgrade für die Azure SQL Data Warehouse-Datenbank durch. |
> | Aktion | Microsoft.Sql/servers/databases/usages/read | Ruft Informationen zur Nutzung von Azure SQL-Datenbank ab. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Entfernt die Sicherheitsrisikobewertung für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Ruft die Sicherheitsrisikobewertungs-Richtlinien für eine angegebene Datenbank ab. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Entfernt die Baseline für die Regel für die Sicherheitsrisikobewertung für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Ruft das Baseline für die Regel für die Sicherheitsrisikobewertung für eine bestimmte Datenbank ab. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Ändert die Baseline für die Regel für die Sicherheitsrisikobewertung für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Konvertiert ein vorhandenes Überprüfungsergebnis in ein lesbares Format. Wenn dies bereits der Fall ist, werden keine Vorgänge ausgeführt. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Dient zum Ausführen einer Datenbanküberprüfung zur Bewertung von Sicherheitsrisiken. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Gibt die Liste der Überprüfungsdatensätze für eine Datenbank-Sicherheitsrisikobewertung oder den Überprüfungsdatensatz für die angegebene Überprüfungs-ID zurück. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Ändert die Sicherheitsrisikobewertung für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Dient zum Ausführen einer Datenbanküberprüfung zur Bewertung von Sicherheitsrisiken. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Ruft das Ergebnis des Ausführungsvorgangs für die Datenbank-Sicherheitsrisikobewertung ab. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Dient zum Abrufen von Details zur Sicherheitsrisikobewertung, die für eine bestimmte Datenbank konfiguriert ist. |
> | Aktion | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Ändert die Sicherheitsrisikobewertung für eine bestimmte Datenbank. |
> | Aktion | Microsoft.Sql/servers/databases/write | Erstellt eine Datenbank mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für die angegebene Datenbank. |
> | Aktion | Microsoft.Sql/servers/delete | Löscht einen vorhandenen Server. |
> | Aktion | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Löscht vorhandene Notfallwiederherstellungskonfigurationen für einen bestimmten Server. |
> | Aktion | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Führt ein Failover für DisasterRecoveryConfiguration durch. |
> | Aktion | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Erzwingen Sie ein Failover für DisasterRecoveryConfiguration. |
> | Aktion | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Ruft eine Collection von Notfallwiederherstellungskonfigurationen ab, die diesen Server umfassen. |
> | Aktion | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Ändert die Notfallwiederherstellungskonfiguration des Servers. |
> | Aktion | Microsoft.Sql/servers/elasticPoolEstimates/read | Gibt die Liste mit Schätzwerten für den Pool für elastische Datenbanken zurück, die bereits für diesen Server erstellt wurden. |
> | Aktion | Microsoft.Sql/servers/elasticPoolEstimates/write | Erstellt für die Liste der bereitgestellten Datenbanken neue Schätzwerte für den Pool für elastische Datenbanken. |
> | Aktion | Microsoft.Sql/servers/elasticPools/advisors/read | Gibt eine Liste mit verfügbaren Ratgebern für den Pool für elastische Datenbanken zurück. |
> | Aktion | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Gibt eine Liste mit empfohlenen Aktionen des angegebenen Ratgebers für den Pool für elastische Datenbanken zurück. |
> | Aktion | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Dient zum Anwenden der empfohlenen Aktion auf den Pool für elastische Datenbanken. |
> | Aktion | Microsoft.Sql/servers/elasticPools/advisors/write | Dient zum Aktualisieren des Status der automatischen Ausführung eines Ratgebers auf der Ebene von Pools für elastische Datenbanken. |
> | Aktion | Microsoft.Sql/servers/elasticPools/databases/read | Ruft eine Liste der Datenbanken für einen Pool für elastische Datenbanken zurück. |
> | Aktion | Microsoft.Sql/servers/elasticPools/delete | Löscht einen vorhandenen Pool für elastische Datenbanken. |
> | Aktion | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Dient zum Abrufen von Aktivitäten und Details zu einem bestimmten Pool für elastische Datenbanken. |
> | Aktion | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Dient zum Abrufen von Aktivitäten und Details zu einer bestimmten Datenbank, die einem Pool für elastische Datenbanken angehört. |
> | Aktion | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Dient zum Zurückgeben verfügbarer Metriktypen für Pools für elastische Datenbanken. |
> | Aktion | Microsoft.Sql/servers/elasticPools/metrics/read | Gibt Metriken für Pools für elastische Datenbanken zurück. |
> | Aktion | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Bricht einen ausstehenden asynchronen Vorgang in einem Pool für elastische Azure SQL-Datenbanken ab, der noch nicht abgeschlossen ist. |
> | Aktion | Microsoft.Sql/servers/elasticPools/operations/read | Gibt die Liste der Vorgänge zurück, die für den Pool für elastische Datenbanken ausgeführt werden. |
> | Aktion | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Ruft die Diagnoseeinstellung für die Ressource ab. |
> | Aktion | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Erstellt oder aktualisiert die Diagnoseeinstellung für die Ressource. |
> | Aktion | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Zurückgeben verfügbarer Metriktypen für Pools für elastische Datenbanken. |
> | Aktion | Microsoft.Sql/servers/elasticPools/read | Dient zum Abrufen von Details zu Pools für elastische Datenbanken auf einem bestimmten Server. |
> | Aktion | Microsoft.Sql/servers/elasticPools/skus/read | Ruft eine Collection von SKUs ab, die für einen Pool für elastische Datenbanken verfügbar ist. |
> | Aktion | Microsoft.Sql/servers/elasticPools/write | Dient zum Erstellen eines neuen Pools für elastische Datenbanken oder zum Ändern von Eigenschaften vorhandener Pools für elastische Datenbanken. |
> | Aktion | Microsoft.Sql/servers/encryptionProtector/read | Gibt eine Liste von Schutzvorrichtungen zur Serververschlüsselung zurück oder ruft die Eigenschaften für die angegebene Schutzvorrichtung zur Serververschlüsselung ab. |
> | Aktion | Microsoft.Sql/servers/encryptionProtector/write | Aktualisiert die Eigenschaften für die angegebene Schutzvorrichtung zur Serververschlüsselung. |
> | Aktion | Microsoft.Sql/servers/extendedAuditingSettings/read | Dient zum Abrufen von Details zur Richtlinie für die erweiterte Serverblobüberwachung, die für einen bestimmten Server konfiguriert ist. |
> | Aktion | Microsoft.Sql/servers/extendedAuditingSettings/write | Dient zum Ändern der erweiterten Serverblobüberwachung für einen bestimmten Server. |
> | Aktion | Microsoft.Sql/servers/failoverGroups/delete | Löscht eine vorhandene Failovergruppe. |
> | Aktion | Microsoft.Sql/servers/failoverGroups/failover/action | Führt das geplante Failover in einer vorhandenen Failovergruppe durch. |
> | Aktion | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Führt ein erzwungenes Failover in einer vorhandenen Failovergruppe aus. |
> | Aktion | Microsoft.Sql/servers/failoverGroups/read | Gibt die Liste der Failovergruppen zurück oder ruft die Eigenschaften für die angegebene Failovergruppe ab. |
> | Aktion | Microsoft.Sql/servers/failoverGroups/write | Erstellt eine Failovergruppe mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für die angegebene Failovergruppe. |
> | Aktion | Microsoft.Sql/servers/firewallRules/delete | Löscht eine vorhandene Serverfirewallregel. |
> | Aktion | Microsoft.Sql/servers/firewallRules/read | Gibt die Liste der Serverfirewallregeln zurück oder ruft die Eigenschaften für die angegebene Serverfirewallregel ab. |
> | Aktion | Microsoft.Sql/servers/firewallRules/write | Erstellt eine Serverfirewallregel mit den angegebenen Parametern, aktualisiert die Eigenschaften für die angegebene Regel oder überschreibt alle vorhandenen Regeln durch neue Serverfirewallregel. |
> | Aktion | Microsoft.Sql/servers/import/action | Dient zum Erstellen einer neuen Datenbank auf dem Server und zum Bereitstellen von Schema und Daten aus einem DACPAC-Paket. |
> | Aktion | Microsoft.Sql/servers/importExportOperationResults/read | Ruft gegenwärtig ausgeführte Import-/Exportvorgänge ab. |
> | Aktion | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Löscht das angegebene Schnittstellenendpunktprofil. |
> | Aktion | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Gibt die Eigenschaften für das angegebene Schnittstellenendpunktprofil zurück. |
> | Aktion | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Erstellt ein Schnittstellenendpunktprofil mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Tags für das angegebene Schnittstellenendpunktprofil. |
> | Aktion | Microsoft.Sql/servers/jobAgents/delete | Löscht einen Auftrags-Agent von Azure SQL-Datenbank. |
> | Aktion | Microsoft.Sql/servers/jobAgents/read | Ruft einen Auftrags-Agent von Azure SQL-Datenbank ab. |
> | Aktion | Microsoft.Sql/servers/jobAgents/write | Erstellt oder aktualisiert einen Auftrags-Agent von Azure SQL-Datenbank. |
> | Aktion | Microsoft.Sql/servers/keys/delete | Löscht einen vorhandenen Serverschlüssel. |
> | Aktion | Microsoft.Sql/servers/keys/read | Gibt die Liste der Serverschlüssel zurück oder ruft die Eigenschaften für den angegebenen Serverschlüssel ab. |
> | Aktion | Microsoft.Sql/servers/keys/write | Erstellt einen Schlüssel mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für den angegebenen Serverschlüssel. |
> | Aktion | Microsoft.Sql/servers/operationResults/read | Ruft gegenwärtig ausgeführte Servervorgänge ab. |
> | Aktion | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Löscht einen Verbindungsproxy eines privaten Endpunkts |
> | Aktion | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Gibt eine Liste der Verbindungsproxys eines privaten Endpunkts zurück oder ruft die Eigenschaften für den angegebenen Verbindungsproxy eines privaten Endpunkts ab |
> | Aktion | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Überprüft den Aufruf zum Erstellen einer Verbindung mit einem privaten Endpunkt auf NRP-Seite |
> | Aktion | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Erstellt einen Verbindungsproxy eines privaten Endpunkts mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Tags für den angegebenen Verbindungsproxy eines privaten Endpunkts |
> | Aktion | Microsoft.Sql/servers/privateEndpointConnections/delete | Löscht eine Verbindung mit einem privaten Endpunkt |
> | Aktion | Microsoft.Sql/servers/privateEndpointConnections/read | Gibt eine Liste der Verbindungen mit einem privaten Endpunkt zurück oder ruft die Eigenschaften für die angegebene Verbindung eines privaten Endpunkts ab |
> | Aktion | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Dient zum Zurückgeben verfügbarer Metriken für Server. |
> | Aktion | Microsoft.Sql/servers/read | Gibt die Liste der Server zurück oder ruft die Eigenschaften für den angegebenen Server ab. |
> | Aktion | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Dient zum Abrufen von Metriken für empfohlene Pools für elastische Datenbanken für einen bestimmten Server. |
> | Aktion | Microsoft.Sql/servers/recommendedElasticPools/read | Dient zum Abrufen von Empfehlungen für Pools für elastische Datenbanken, um Kosten zu senken oder die Leistung zu verbessern. Die Empfehlungen basieren auf der Ressourcenverwendung in der Vergangenheit. |
> | Aktion | Microsoft.Sql/servers/recoverableDatabases/read | Vorgang für die Notfallwiederherstellung der Livedatenbank, um die Datenbank auf der Grundlage des letzten als funktionierend bekannten Sicherungspunkts wiederherzustellen. Gibt Informationen zur letzten als funktionierend bekannten Sicherung zurück, stellt die Datenbank aber nicht wieder her. |
> | Aktion | Microsoft.Sql/servers/replicationLinks/read | Gibt die Liste mit den Replikationslinks zurück oder ruft die Eigenschaften für die angegebenen Replikationslinks ab. |
> | Aktion | Microsoft.Sql/servers/restorableDroppedDatabases/read | Dient zum Abrufen einer Liste mit Datenbanken, die auf einem bestimmten Server gelöscht wurden und weiterhin in einer Aufbewahrungsrichtlinie enthalten sind. |
> | Aktion | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Dient zum Abrufen von Ergebnissen des Schreibvorgangs der Richtlinie für die Serverbedrohungserkennung. |
> | Aktion | Microsoft.Sql/servers/securityAlertPolicies/read | Ruft eine Liste der Bedrohungserkennungrichtlinien für Server ab, die für einen bestimmten Server konfiguriert sind. |
> | Aktion | Microsoft.Sql/servers/securityAlertPolicies/write | Dient zum Ändern der Richtlinie für die Serverbedrohungserkennung für einen bestimmten Server. |
> | Aktion | Microsoft.Sql/servers/serviceObjectives/read | Dient zum Abrufen einer Liste mit verfügbaren Servicelevelzielen (auch Leistungsstufen genannt) für einen bestimmten Server. |
> | Aktion | Microsoft.Sql/servers/syncAgents/delete | Löscht einen vorhandenen Synchronisierungs-Agent. |
> | Aktion | Microsoft.Sql/servers/syncAgents/generateKey/action | Generiert einen Registrierungsschlüssel für den Synchronisierungs-Agent. |
> | Aktion | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Gibt die Liste der mit dem Synchronisierungs-Agent verknüpften Datenbanken zurück. |
> | Aktion | Microsoft.Sql/servers/syncAgents/read | Gibt die Liste der Synchronisierungs-Agents zurück oder ruft die Eigenschaften für den angegebenen Synchronisierungs-Agent ab. |
> | Aktion | Microsoft.Sql/servers/syncAgents/write | Erstellt einen Synchronisierungs-Agent mit den angegebenen Parametern oder aktualisiert die Eigenschaften für den angegebenen Synchronisierungs-Agent. |
> | Aktion | Microsoft.Sql/servers/tdeCertificates/action | Erstellt oder aktualisiert das TDE-Zertifikat. |
> | Aktion | Microsoft.Sql/servers/usages/read | Dient zum Zurückgeben des Server-DTU-Kontingents und des aktuellen DTU-Verbrauchs durch alle Datenbanken auf dem Server. |
> | Aktion | Microsoft.Sql/servers/virtualNetworkRules/delete | Löscht eine vorhandene VNET-Regel. |
> | Aktion | Microsoft.Sql/servers/virtualNetworkRules/read | Gibt die Liste der VNET-Regeln zurück oder ruft die Eigenschaften für die angegebene VNET-Regel ab. |
> | Aktion | Microsoft.Sql/servers/virtualNetworkRules/write | Erstellt eine VNET-Regel mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für die angegebene VNET-Regel. |
> | Aktion | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Entfernt die Sicherheitsrisikobewertung für einen bestimmten Server. |
> | Aktion | Microsoft.Sql/servers/vulnerabilityAssessments/read | Ruft die Sicherheitsrisikobewertungs-Richtlinien für einen angegebenen Server ab. |
> | Aktion | Microsoft.Sql/servers/vulnerabilityAssessments/write | Ändert die Sicherheitsrisikobewertung für einen bestimmten Server. |
> | Aktion | Microsoft.Sql/servers/write | Erstellt einen Server mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Markierungen für den angegebenen Server. |
> | Aktion | Microsoft.Sql/unregister/action | Hebt die Registrierung für das Abonnement für den Microsoft SQL-Datenbankressourcenanbieter auf und ermöglicht die Erstellung von Microsoft SQL-Datenbanken. |
> | Aktion | Microsoft.Sql/virtualClusters/delete | Löscht einen vorhandenen virtuellen Cluster. |
> | Aktion | Microsoft.Sql/virtualClusters/read | Gibt die Liste der Regeln für virtuelle Cluster zurück oder ruft die Eigenschaften für das angegebene virtuelle Cluster ab. |
> | Aktion | Microsoft.Sql/virtualClusters/write | Aktualisiert die Markierungen virtueller Cluster. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Storage/checknameavailability/read | Prüft, ob der Kontoname gültig ist und noch nicht verwendet wird. |
> | Aktion | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Benachrichtigt Microsoft.Storage darüber, dass das virtuelle Netzwerk oder Subnetz gelöscht wird. |
> | Aktion | Microsoft.Storage/locations/usages/read | Gibt den Grenzwert und den aktuellen Verwendungszähler für Ressourcen im angegebenen Abonnement zurück. |
> | Aktion | Microsoft.Storage/operations/read | Fragt den Status eines asynchronen Vorgangs ab. |
> | Aktion | Microsoft.Storage/register/action | Registriert das Abonnement für den Speicherressourcenanbieter und ermöglicht die Erstellung von Speicherkonten. |
> | Aktion | Microsoft.Storage/skus/read | Listet die von Microsoft.Storage unterstützten SKUs auf. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Gibt das Ergebnis beim Hinzufügen von Blobinhalten zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Gibt das Ergebnis beim Löschen eines Blobs zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Gibt das Ergebnis der Löschung einer automatischen Momentaufnahme zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Gibt eine Liste der Blobs unter einem Konto mit entsprechendem Tagfilter zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Gibt ein Blob oder eine Liste von Blobs zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Gibt das Ergebnis beim Lesen von Blobtags zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Gibt das Ergebnis beim Schreiben von Blobtags zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Gibt das Ergebnis beim Schreiben eines Blobs zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Löscht die rechtliche Aufbewahrungspflicht für Blobcontainer. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Gibt das Ergebnis beim Löschen eines Containers zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Löscht die Unveränderlichkeitsrichtlinie für Blobcontainer. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Erweitert die Unveränderlichkeitsrichtlinie für Blobcontainer. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Sperrt die Unveränderlichkeitsrichtlinie für Blobcontainer. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Ruft die Unveränderlichkeitsrichtlinie für Blobcontainer ab. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Legt die Unveränderlichkeitsrichtlinie für Blobcontainer fest. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Gibt das Ergebnis des Vorgangs zum Leasen eines Blobcontainers zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/read | Gibt einen Container zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/read | Gibt eine Liste mit Containern zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Legt die rechtliche Aufbewahrungspflicht für Blobcontainer fest. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/write | Gibt das Ergebnis des Patchvorgangs für den Blobcontainer zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/containers/write | Gibt das Ergebnis des PUT-Vorgangs für den Blobcontainer zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Gibt einen Benutzerdelegierungsschlüssel für den Blobdienst zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/read | Gibt Eigenschaften oder Statistiken des Blob-Diensts zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/blobServices/write | Hiermit wird das Ergebnis des Vorgangs zum Festlegen von Eigenschaften des Blob-Diensts zurückgegeben. |
> | Aktion | Microsoft.Storage/storageAccounts/delete | Löscht ein vorhandenes Speicherkonto. |
> | Aktion | Microsoft.Storage/storageAccounts/failover/action | Der Kunde kann bei Verfügbarkeitsproblemen das Failover steuern. |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write |  |
> | Aktion | Microsoft.Storage/storageAccounts/fileServices/read | Ruft Dateidiensteigenschaften ab. |
> | Aktion | Microsoft.Storage/storageAccounts/listAccountSas/action | Gibt das Konto-SAS-Token für das angegebene Speicherkonto zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/listkeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/listServiceSas/action | Gibt das Dienst-SAS-Token für das angegebene Speicherkonto zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/managementPolicies/delete | Löscht Verwaltungsrichtlinien für Speicherkonten. |
> | Aktion | Microsoft.Storage/storageAccounts/managementPolicies/read | Ruft Verwaltungsrichtlinien für Speicherkonten ab. |
> | Aktion | Microsoft.Storage/storageAccounts/managementPolicies/write | Legt Verwaltungsrichtlinien für Speicherkonten fest. |
> | Aktion | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Gibt das Ergebnis beim Löschen einer Warteschlange zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Gibt das Ergebnis beim Hinzufügen einer Nachricht zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Gibt das Ergebnis beim Löschen einer Nachricht zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Gibt das Ergebnis beim Verarbeiten einer Nachricht zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Gibt eine Nachricht zurück. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Gibt das Ergebnis beim Schreiben einer Nachricht zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/queueServices/queues/read | Gibt eine Warteschlange oder eine Liste von Warteschlangen zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/queueServices/queues/write | Gibt das Ergebnis beim Schreiben einer Warteschlange zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/queueServices/read | Ruft Eigenschaften des Warteschlangendiensts ab. |
> | Aktion | Microsoft.Storage/storageAccounts/queueServices/read | Gibt Eigenschaften oder Statistiken des Warteschlangendiensts zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/queueServices/write | Gibt das Ergebnis beim Festlegen von Eigenschaften des Warteschlangendiensts zurück. |
> | Aktion | Microsoft.Storage/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | Aktion | Microsoft.Storage/storageAccounts/regeneratekey/action | Generiert die Zugriffsschlüssel für das angegebene Speicherkonto neu. |
> | Aktion | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Widerruft alle Benutzerdelegierungsschlüssel für das angegebene Speicherkonto. |
> | Aktion | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Dient zum Erstellen/Aktualisieren von Speicherkonto-Diagnoseeinstellungen. |
> | Aktion | Microsoft.Storage/storageAccounts/tableServices/read | Ruft Eigenschaften des Tabellenspeicherdiensts ab. |
> | Aktion | Microsoft.Storage/storageAccounts/write | Erstellt ein Speicherkonto mit den angegebenen Parametern oder aktualisiert die Eigenschaften oder Tags oder fügt eine benutzerdefinierte Domäne zum angegebenen Speicherkonto hinzu. |
> | Aktion | Microsoft.Storage/usages/read | Gibt den Grenzwert und den aktuellen Verwendungszähler für Ressourcen im angegebenen Abonnement zurück. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | microsoft.storagesync/locations/checkNameAvailability/action | Prüft, ob der Name des Speichersynchronisierungsdiensts gültig ist und noch nicht verwendet wird. |
> | Aktion | microsoft.storagesync/locations/workflows/operations/read | Ruft den Status eines asynchronen Vorgangs ab. |
> | Aktion | microsoft.storagesync/storageSyncServices/delete | Löscht Speichersynchronisierungsdienste. |
> | Aktion | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für Speichersynchronisierungsdienste ab. |
> | Aktion | microsoft.storagesync/storageSyncServices/read | Liest Speichersynchronisierungsdienste. |
> | Aktion | microsoft.storagesync/storageSyncServices/registeredServers/delete | Löscht registrierte Server. |
> | Aktion | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für den registrierten Server ab. |
> | Aktion | microsoft.storagesync/storageSyncServices/registeredServers/read | Liest registrierte Server. |
> | Aktion | microsoft.storagesync/storageSyncServices/registeredServers/write | Erstellt oder aktualisiert registrierte Server. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Löscht Cloudendpunkte. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Ruft den Status eines asynchronen Sicherungs-/Wiederherstellungsvorgangs ab. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Ruft diese Aktion nach der Sicherung auf. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Ruft diese Aktion nach der Wiederherstellung auf. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Ruft diese Aktion vor der Sicherung auf. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Ruft diese Aktion vor der Wiederherstellung auf. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Liest Cloudendpunkte. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Stellt den Heartbeat wieder her. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Hiermit erstellen oder aktualisieren Sie Cloudendpunkte. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/delete | Löscht Synchronisierungsgruppen. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für Synchronisierungsgruppen ab. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/read | Liest Synchronisierungsgruppen. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Löscht Serverendpunkte. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für Serverendpunkte ab. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Liest Serverendpunkte. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Ruft diese Aktion auf, um Dateien auf einem Server abzurufen. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Hiermit erstellen oder aktualisieren Sie Serverendpunkte. |
> | Aktion | microsoft.storagesync/storageSyncServices/syncGroups/write | Hiermit erstellen oder aktualisieren Sie Synchronisierungsgruppen. |
> | Aktion | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Ruft den Status eines asynchronen Vorgangs ab. |
> | Aktion | microsoft.storagesync/storageSyncServices/workflows/operations/read | Ruft den Status eines asynchronen Vorgangs ab. |
> | Aktion | microsoft.storagesync/storageSyncServices/workflows/read | Dient zum Lesen von Workflows. |
> | Aktion | microsoft.storagesync/storageSyncServices/write | Erstellt oder aktualisiert Speichersynchronisierungsdienste. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.StorSimple/managers/accessControlRecords/delete | Löscht die Access Control-Datensätze. |
> | Aktion | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/accessControlRecords/read | Listet die Access Control-Datensätze auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/accessControlRecords/write | Dient zum Erstellen oder Aktualisieren der Access Control-Datensätze. |
> | Aktion | Microsoft.StorSimple/managers/alerts/read | Listet die Warnungen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/backups/read | Listet den Sicherungssatz auf oder ruft ihn ab. |
> | Aktion | Microsoft.StorSimple/managers/bandwidthSettings/delete | Löscht vorhandene Bandbreiteneinstellungen (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Dient zum Auflisten der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/bandwidthSettings/read | Listet die Bandbreiteneinstellungen auf (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/bandwidthSettings/write | Erstellt eine neue Bandbreiteneinstellung oder aktualisiert Bandbreiteneinstellungen (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/certificates/write | Dient zum Erstellen oder Aktualisieren der Zertifikate. |
> | Aktion | Microsoft.StorSimple/Managers/certificates/write | Der Vorgang „Ressourcenzertifikat aktualisieren“ aktualisiert das Zertifikat für die Ressourcen-/Tresoranmeldeinformationen. |
> | Aktion | Microsoft.StorSimple/managers/clearAlerts/action | Dient zum Deaktivieren aller Warnungen im Zusammenhang mit dem Geräte-Manager. |
> | Aktion | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Dient zum Auflisten der vom Cloudgerät unterstützten Konfigurationen. |
> | Aktion | Microsoft.StorSimple/managers/configureDevice/action | Konfiguriert ein Gerät. |
> | Aktion | Microsoft.StorSimple/managers/delete | Löscht die Geräte-Manager. |
> | Aktion | Microsoft.StorSimple/Managers/delete | Der Vorgang „Tresor löschen“ löscht die angegebene Azure-Ressource vom Typ „Tresor“. |
> | Aktion | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/alertSettings/read | Listet die Warnungseinstellungen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/alertSettings/write | Dient zum Erstellen oder Aktualisieren der Warnungseinstellungen. |
> | Aktion | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Dient zum Autorisieren des Rollovers des Dienstverschlüsselungsschlüssels von Geräten. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Dient zum Erstellen einer manuellen Sicherung, um eine bedarfsgesteuerte Sicherung aller durch die Richtlinie geschützten Volumes zu erstellen. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Löscht vorhandene Sicherungsrichtlinien (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Dient zum Auflisten der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupPolicies/read | Dient zum Auflisten der Sicherungsrichtlinien (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Löscht vorhandene Zeitpläne. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Dient zum Auflisten der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Dient zum Auflisten der Zeitpläne. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Erstellt einen neuen Zeitplan oder aktualisiert Zeitpläne. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupPolicies/write | Erstellt eine neue Sicherungsrichtlinie oder aktualisiert vorhandene Sicherungsrichtlinien (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/devices/backups/delete | Löscht den Sicherungssatz. |
> | Aktion | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Dient zum Klonen einer Freigabe oder eines Volumes unter Verwendung eines Sicherungselements. |
> | Aktion | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/backups/read | Listet den Sicherungssatz auf oder ruft ihn ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/backups/restore/action | Dient zum Wiederherstellen aller Volumes aus einem Sicherungssatz. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Löscht die Sicherungszeitplangruppen. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Listet die Sicherungszeitplangruppen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Dient zum Erstellen oder Aktualisieren der Sicherungszeitplangruppen. |
> | Aktion | Microsoft.StorSimple/managers/devices/chapSettings/delete | Löscht die CHAP-Einstellungen. |
> | Aktion | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/chapSettings/read | Listet die CHAP-Einstellungen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/chapSettings/write | Dient zum Erstellen oder Aktualisieren der CHAP-Einstellungen. |
> | Aktion | Microsoft.StorSimple/managers/devices/deactivate/action | Deaktiviert ein Gerät. |
> | Aktion | Microsoft.StorSimple/managers/devices/delete | Löscht die Geräte. |
> | Aktion | Microsoft.StorSimple/managers/devices/disks/read | Listet die Datenträger auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/download/action | Dient zum Herunterladen von Updates für ein Gerät. |
> | Aktion | Microsoft.StorSimple/managers/devices/failover/action | Dient zum Ausführen eines Failovers für das Gerät. |
> | Aktion | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/failoverTargets/read | Dient zum Auflisten oder Abrufen von Failoverzielen der Geräte. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Dient zum Erstellen einer Sicherung eines Dateiservers. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/delete | Löscht die Dateiserver. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Listet die Metriken auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Listet die Metrikdefinitionen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/read | Listet die Dateiserver auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Löscht die Freigaben. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Listet die Metriken auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Listet die Metrikdefinitionen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Listet die Freigaben auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Dient zum Erstellen oder Aktualisieren der Freigaben. |
> | Aktion | Microsoft.StorSimple/managers/devices/fileservers/write | Dient zum Erstellen oder Aktualisieren der Dateiserver. |
> | Aktion | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Dient zum Ändern des Controllerenergiezustands von Hardwarekomponentengruppen. |
> | Aktion | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Dient zum Auflisten der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Dient zum Auflisten der Hardwarekomponentengruppen. |
> | Aktion | Microsoft.StorSimple/managers/devices/install/action | Dient zum Installieren von Updates auf einem Gerät. |
> | Aktion | Microsoft.StorSimple/managers/devices/installUpdates/action | Installiert Updates auf den Geräten (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Dient zum Erstellen einer Sicherung eines iSCSI-Servers. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Löscht die iSCSI-Server. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Löscht die Datenträger. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Listet die Metriken auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Listet die Metrikdefinitionen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Listet die Datenträger auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Dient zum Erstellen oder Aktualisieren der Datenträger. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Listet die Metriken auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Listet die Metrikdefinitionen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/read | Listet die iSCSI-Server auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/iscsiservers/write | Dient zum Erstellen oder Aktualisieren der iSCSI-Server. |
> | Aktion | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Dient zum Abbrechen eines ausgeführten Auftrags. |
> | Aktion | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Dient zum Auflisten der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/jobs/read | Listet die Aufträge auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Dient zum Auflisten der Failovergruppen für ein vorhandenes Gerät (nur 8000-er Serie). |
> | Aktion | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Dient zum Auflisten von Failoverzielen der Geräte (nur 8000-er Serie). |
> | Aktion | Microsoft.StorSimple/managers/devices/metrics/read | Listet die Metriken auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Listet die Metrikdefinitionen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Bestätigt eine erfolgreiche Migration und committet sie. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Dient zum Auflisten der Bestätigung des Migrationsstatus. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Dient zum Abrufen des Migrationsbestätigungsstatus. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Dient zum Abrufen des Status für den Migrationsschätzauftrag. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Dient zum Abrufen des Status für die Migration. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Dient zum Importieren von Quellkonfigurationen für die Migration. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Dient zum Auflisten der Schätzung für die Migration. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Dient zum Auflisten des Migrationstatus. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Dient zum Auflisten der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Dient zum Starten der Migration unter Verwendung von Quellkonfigurationen. |
> | Aktion | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Dient zum Starten eines Auftrags zur Ermittlung der voraussichtlichen Dauer des Migrationsvorgangs. |
> | Aktion | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Dient zum Auflisten der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/networkSettings/read | Listet die Netzwerkeinstellungen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/networkSettings/write | Erstellt eine neue Netzwerkeinstellung oder aktualisiert Netzwerkeinstellungen. |
> | Aktion | Microsoft.StorSimple/managers/devices/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Dient zum Auflisten öffentlicher Verschlüsselungsschlüssel des Geräte-Managers. |
> | Aktion | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Veröffentlicht das Supportpaket für ein vorhandenes Gerät. Ein StorSimple-Unterstützungspaket ist ein einfach zu verwendender Mechanismus, der alle relevanten Protokolle zur Unterstützung des Microsoft Supports bei der Behandlung von Problemen mit StorSimple-Geräten erfasst. |
> | Aktion | Microsoft.StorSimple/managers/devices/read | Listet die Geräte auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Dient zum Suchen nach Updates auf einem Gerät. |
> | Aktion | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/securitySettings/read | Dient zum Auflisten der Sicherheitseinstellungen. |
> | Aktion | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Dient zum Synchronisieren des Remoteverwaltungszertifikats für ein Gerät. |
> | Aktion | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Dient zum Aktualisieren der Sicherheitseinstellungen. |
> | Aktion | Microsoft.StorSimple/managers/devices/securitySettings/write | Erstellt eine neue Sicherheitseinstellung oder aktualisiert Sicherheitseinstellungen. |
> | Aktion | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Dient zum Senden einer Testwarnungs-E-Mail an konfigurierte E-Mail-Empfänger. |
> | Aktion | Microsoft.StorSimple/managers/devices/shares/read | Listet die Freigaben auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Dient zum Auflisten der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/timeSettings/read | Listet die Uhrzeiteinstellungen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/timeSettings/write | Erstellt eine neue Uhrzeiteinstellung oder aktualisiert Uhrzeiteinstellungen. |
> | Aktion | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/updateSummary/read | Listet die Updatezusammenfassung auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Löscht vorhandene Volumecontainer (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Dient zum Auflisten der Metriken. |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Dient zum Auflisten der Metrikdefinitionen. |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Dient zum Auflisten der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/read | Dient zum Auflisten der Volumecontainer (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Löscht vorhandene Volumes. |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Dient zum Auflisten der Metriken. |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Dient zum Auflisten der Metrikdefinitionen. |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Dient zum Auflisten der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Dient zum Auflisten der Volumes. |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Erstellt ein neues Volume oder aktualisiert Volumes. |
> | Aktion | Microsoft.StorSimple/managers/devices/volumeContainers/write | Erstellt einen neuen Volumecontainer oder aktualisiert Volumecontainer (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/devices/volumes/read | Dient zum Auflisten der Volumes. |
> | Aktion | Microsoft.StorSimple/managers/devices/write | Dient zum Erstellen oder Aktualisieren der Geräte. |
> | Aktion | Microsoft.StorSimple/managers/encryptionSettings/read | Listet die Verschlüsselungseinstellungen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/extendedInformation/delete | Löscht die erweiterten Tresorinformationen. |
> | Aktion | Microsoft.StorSimple/Managers/extendedInformation/delete | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Aktion | Microsoft.StorSimple/managers/extendedInformation/read | Dient zum Auflisten oder Abrufen der erweiterten Tresorinformationen. |
> | Aktion | Microsoft.StorSimple/Managers/extendedInformation/read | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Aktion | Microsoft.StorSimple/managers/extendedInformation/write | Dient zum Erstellen oder Aktualisieren der erweiterten Tresorinformationen. |
> | Aktion | Microsoft.StorSimple/Managers/extendedInformation/write | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Aktion | Microsoft.StorSimple/managers/features/read | Listet die Features auf. |
> | Aktion | Microsoft.StorSimple/managers/fileservers/read | Listet die Dateiserver auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/getEncryptionKey/action | Dient zum Abrufen des Verschlüsselungsschlüssels für den Geräte-Manager. |
> | Aktion | Microsoft.StorSimple/managers/iscsiservers/read | Listet die iSCSI-Server auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/jobs/read | Listet die Aufträge auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/listActivationKey/action | Ruft den Aktivierungsschlüssel des StorSimple-Geräte-Managers ab. |
> | Aktion | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Dient zum Auflisten öffentlicher Verschlüsselungsschlüssel eines StorSimple-Geräte-Managers. |
> | Aktion | Microsoft.StorSimple/managers/metrics/read | Listet die Metriken auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/metricsDefinitions/read | Listet die Metrikdefinitionen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Dient zum Migrieren von klassischen zu Ressourcen-Manager-Bereitstellungen. |
> | Aktion | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Listet die Migrationsquellkonfigurationen auf (nur 8000er Serie). |
> | Aktion | Microsoft.StorSimple/managers/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Dient zum Erstellen eines neuen Cloudgeräts. |
> | Aktion | Microsoft.StorSimple/managers/read | Listet die Geräte-Manager auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/Managers/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | Aktion | Microsoft.StorSimple/managers/regenerateActivationKey/action | Dient zum erneuten Generieren des Aktivierungsschlüssels für einen vorhandenen StorSimple-Geräte-Manager. |
> | Aktion | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Löscht die Speicherkonto-Anmeldedaten. |
> | Aktion | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/storageAccountCredentials/read | Listet die Speicherkonto-Anmeldedaten auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/storageAccountCredentials/write | Dient zum Erstellen oder Aktualisieren der Speicherkonto-Anmeldedaten. |
> | Aktion | Microsoft.StorSimple/managers/storageDomains/delete | Löscht die Speicherdomänen. |
> | Aktion | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Dient zum Auflisten oder Abrufen der Vorgangsergebnisse. |
> | Aktion | Microsoft.StorSimple/managers/storageDomains/read | Listet die Speicherdomänen auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/managers/storageDomains/write | Dient zum Erstellen oder Aktualisieren der Speicherdomänen. |
> | Aktion | Microsoft.StorSimple/managers/write | Dient zum Erstellen oder Aktualisieren der Geräte-Manager. |
> | Aktion | Microsoft.StorSimple/Managers/write | Der Vorgang „Tresor erstellen“ erstellt eine Azure-Ressource vom Typ „Tresor“. |
> | Aktion | Microsoft.StorSimple/operations/read | Listet die Vorgänge auf oder ruft sie ab. |
> | Aktion | Microsoft.StorSimple/register/action | Registriert den Anbieter Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.StreamAnalytics/locations/quotas/Read | Liest das Stream Analytics-Abonnementkontingent. |
> | Aktion | Microsoft.StreamAnalytics/operations/Read | Liest Stream Analytics-Vorgänge. |
> | Aktion | Microsoft.StreamAnalytics/Register/action | Registriert das Abonnement beim Stream Analytics-Ressourcenanbieter. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/Delete | Dient zum Löschen von Stream Analytics-Aufträgen. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Löscht die Stream Analytics-Auftragsfunktion. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Liest die Vorgangsergebnisse für die Stream Analytics-Auftragsfunktion. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Liest die Stream Analytics-Auftragsfunktion. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Ruft die Standarddefinition einer Stream Analytics-Auftragsfunktion ab. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Testet die Stream Analytics-Auftragsfunktion. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Schreibt die Stream Analytics-Auftragsfunktion. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Dient zum Löschen von Stream Analytics-Auftragseingaben. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Liest die Vorgangsergebnisse für die Stream Analytics-Auftragseingabe. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Dient zum Lesen von Stream Analytics-Auftragseingaben. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Stream Analytics-Auftragseingaben |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Dient zum Testen von Stream Analytics-Auftragseingaben. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Dient zum Schreiben von Stream Analytics-Auftragseingaben. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Dient zum Lesen von Metrikdefinitionen. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Liest die Vorgangsergebnisse für den Stream Analytics-Auftrag. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Dient zum Löschen von Stream Analytics-Auftragsausgaben. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Liest die Vorgangsergebnisse für die Stream Analytics-Auftragsausgabe. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Dient zum Lesen von Stream Analytics-Auftragsausgaben. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Dient zum Testen der Stream Analytics-Auftragsausgabe. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Dient zum Schreiben von Stream Analytics-Auftragsausgaben. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Dient zum Lesen der Diagnoseeinstellung. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Dient zum Schreiben der Diagnoseeinstellung. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Ruft die verfügbaren Protokolle für Streamingaufträge ab. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Ruft die verfügbaren Metriken für Streamingaufträge ab. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/Read | Dient zum Lesen von Stream Analytics-Aufträgen. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/Start/action | Dient zum Starten von Stream Analytics-Aufträgen. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Dient zum Beenden von Stream Analytics-Aufträgen. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Dient zum Löschen von Stream Analytics-Auftragstransformationen. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Dient zum Lesen von Stream Analytics-Auftragstransformationen. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Dient zum Schreiben von Stream Analytics-Auftragstransformationen. |
> | Aktion | Microsoft.StreamAnalytics/streamingjobs/Write | Dient zum Schreiben von Stream Analytics-Aufträgen. |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Subscription/CreateSubscription/action | Erstellt ein Azure-Abonnement. |
> | Aktion | Microsoft.Subscription/register/action | Registriert ein Abonnement bei einem Microsoft.Subscription-Ressourcenanbieter. |
> | Aktion | Microsoft.Subscription/SubscriptionDefinitions/read | Ruft die Definition eines Azure-Abonnements in einer Verwaltungsgruppe ab. |
> | Aktion | Microsoft.Subscription/SubscriptionDefinitions/write | Erstellt die Definition für ein Azure-Abonnement. |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.Support/register/action | Führt die Registrierung beim Supportressourcenanbieter durch. |
> | Aktion | Microsoft.Support/supportTickets/read | Ruft Details zu Supporttickets (einschließlich Status, Schweregrad, Kontaktdetails und Kommunikation) oder die Liste mit Supporttickets (abonnementübergreifend) ab. |
> | Aktion | Microsoft.Support/supportTickets/write | Erstellt oder aktualisiert ein Supportticket. Sie können ein Supportticket zu technischen Problemen bzw. zu Problemen im Zusammenhang mit Abrechnung, Kontingenten oder Abonnementverwaltung erstellen. Der Schweregrad, die Kontaktinformationen und die Kommunikation für vorhandene Supporttickets können aktualisiert werden. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Löscht die Zugriffsrichtlinie. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Dient zum Abrufen der Eigenschaften einer Zugriffsrichtlinie. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Erstellt eine neue Zugriffsrichtlinie für eine Umgebung oder aktualisiert eine vorhandene Zugriffsrichtlinie. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/delete | Löscht die Umgebung. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Löscht die Ereignisquelle. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/eventsources/read | Dient zum Abrufen der Eigenschaften einer Ereignisquelle. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/eventsources/write | Erstellt eine neue Ereignisquelle für eine Umgebung oder aktualisiert eine vorhandene Ereignisquelle. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/read | Dient zum Abrufen der Eigenschaften einer Umgebung. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Löscht das Verweisdataset. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Dient zum Abrufen der Eigenschaften eines Verweisdatasets. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Erstellt ein neues Verweisdataset für eine Umgebung oder aktualisiert ein vorhandenes Verweisdataset. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/status/read | Ruft den Status der Umgebung und den Status der zugehörigen Vorgänge wie eingehende Daten ab. |
> | Aktion | Microsoft.TimeSeriesInsights/environments/write | Erstellt eine neue Umgebung oder aktualisiert eine vorhandene Umgebung. |
> | Aktion | Microsoft.TimeSeriesInsights/register/action | Registriert das Abonnement für den Time Series Insights-Ressourcenanbieter und ermöglicht die Erstellung von Time Series Insights-Umgebungen. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.VisualStudio/Account/Delete | Löscht ein Konto. |
> | Aktion | Microsoft.VisualStudio/Account/Extension/Read | Liest ein Konto bzw. eine Erweiterung. |
> | Aktion | Microsoft.VisualStudio/Account/Project/Read | Liest ein Konto bzw. Projekt. |
> | Aktion | Microsoft.VisualStudio/Account/Project/Write | Legt das Konto bzw. das Projekt fest. |
> | Aktion | Microsoft.VisualStudio/Account/Read | Liest ein Konto. |
> | Aktion | Microsoft.VisualStudio/Account/Write | Legt ein Konto fest. |
> | Aktion | Microsoft.VisualStudio/Extension/Delete | Löscht eine Erweiterung. |
> | Aktion | Microsoft.VisualStudio/Extension/Read | Liest eine Erweiterung. |
> | Aktion | Microsoft.VisualStudio/Extension/Write | Legt eine Erweiterung fest. |
> | Aktion | Microsoft.VisualStudio/Project/Delete | Löscht ein Projekt. |
> | Aktion | Microsoft.VisualStudio/Project/Read | Liest ein Projekt. |
> | Aktion | Microsoft.VisualStudio/Project/Write | Legt ein Projekt fest. |
> | Aktion | Microsoft.VisualStudio/Register/Action | Registriert das Azure-Abonnement beim Microsoft.VisualStudio-Anbieter. |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | microsoft.web/apimanagementaccounts/apiacls/read | Ruft die API-ACLs der API Management-Konten ab. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Löscht die API-ACLs für die APIs der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/apiacls/read | Ruft die API-ACLs für die APIs der API Management-Konten ab. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/apiacls/write | Aktualisiert die API-ACLs für die APIs der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Ruft die Verbindungs-ACLs für die APIs der API Management-Konten ab. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Bestätigt den Zustimmungscode für die API-Verbindungen der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Löscht die Verbindungs-ACLs für die API-Verbindungen der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Ruft die Verbindungs-ACLs für die API-Verbindungen der API Management-Konten ab. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Aktualisiert die Verbindungs-ACLs für die API-Verbindungen der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connections/delete | Löscht die API-Verbindungen der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Ruft die Zustimmungslinks für die API-Verbindungen der API Management-Konten ab. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Listet die Verbindungsschlüssel für die API-Verbindungen der API Management-Konten auf. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Listet die Geheimnisse für die API-Verbindungen der API Management-Konten auf. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connections/read | Ruft die API-Verbindungen der API Management-Konten ab. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/connections/write | Aktualisiert die API-Verbindungen der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/delete | Löscht die APIs der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Löscht die lokalisierten Definitionen für die APIs der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Ruft die lokalisierten Definitionen für die APIs der API Management-Konten ab. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Aktualisiert die lokalisierten Definitionen für die APIs der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/read | Ruft die APIs der API Management-Konten ab. |
> | Aktion | microsoft.web/apimanagementaccounts/apis/write | Aktualisiert die APIs der API Management-Konten. |
> | Aktion | microsoft.web/apimanagementaccounts/connectionacls/read | Ruft die Verbindungs-ACLs der API Management-Konten ab. |
> | Aktion | microsoft.web/availablestacks/read | Dient zum Abrufen verfügbarer Stapel. |
> | Aktion | Microsoft.Web/certificates/Delete | Dient zum Löschen eines vorhandenen Zertifikats. |
> | Aktion | Microsoft.Web/certificates/Read | Dient zum Abrufen der Zertifikatliste. |
> | Aktion | Microsoft.Web/certificates/Write | Dient zum Hinzufügen eines neuen Zertifikats oder zum Aktualisieren eines bereits vorhandenen. |
> | Aktion | microsoft.web/checknameavailability/read | Dient zum Prüfen, ob ein Ressourcenname verfügbar ist. |
> | Aktion | microsoft.web/classicmobileservices/read | Dient zum Abrufen von Mobile Services (klassisch). |
> | Aktion | Microsoft.Web/connectionGateways/Delete | Löscht ein Verbindungsgateway. |
> | Aktion | Microsoft.Web/connectionGateways/Join/Action | Verknüpft ein Verbindungsgateway. |
> | Aktion | Microsoft.Web/connectionGateways/ListStatus/Action | Listet den Status eines Verbindungsgateways auf. |
> | Aktion | Microsoft.Web/connectionGateways/Move/Action | Verschiebt ein Verbindungsgateway. |
> | Aktion | Microsoft.Web/connectionGateways/Read | Dient zum Abrufen der Verbindungsgatewayliste. |
> | Aktion | Microsoft.Web/connectionGateways/Write | Erstellt oder aktualisiert ein Verbindungsgateway. |
> | Aktion | microsoft.web/connections/confirmconsentcode/action | Dient zum Bestätigen des Genehmigungscodes für Verbindungen. |
> | Aktion | Microsoft.Web/connections/Delete | Löscht eine Verbindung. |
> | Aktion | Microsoft.Web/connections/Join/Action | Verknüpft eine Verbindung. |
> | Aktion | microsoft.web/connections/listconsentlinks/action | Dient zum Auflisten von Genehmigungslinks für Verbindungen. |
> | Aktion | Microsoft.Web/connections/Move/Action | Verschiebt eine Verbindung. |
> | Aktion | Microsoft.Web/connections/Read | Dient zum Abrufen der Verbindungsliste. |
> | Aktion | Microsoft.Web/connections/Write | Erstellt oder aktualisiert eine Verbindung. |
> | Aktion | Microsoft.Web/customApis/Delete | Löscht eine benutzerdefinierte API. |
> | Aktion | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrahiert eine API-Definition aus einer WSDL-Schnittstelle. |
> | Aktion | Microsoft.Web/customApis/Join/Action | Verknüpft eine benutzerdefinierte API. |
> | Aktion | Microsoft.Web/customApis/listWsdlInterfaces/Action | Listet WSDL-Schnittstellen für eine benutzerdefinierte API auf. |
> | Aktion | Microsoft.Web/customApis/Move/Action | Verschiebt eine benutzerdefinierte API |
> | Aktion | Microsoft.Web/customApis/Read | Dient zum Abrufen einer benutzerdefinierten API. |
> | Aktion | Microsoft.Web/customApis/Write | Erstellt oder aktualisiert eine Verbindungs-API. |
> | Aktion | Microsoft.Web/deletedSites/Read | Hiermit rufen Sie die Eigenschaften einer gelöschten Web-App ab. |
> | Aktion | microsoft.web/deploymentlocations/read | Dient zum Abrufen von Bereitstellungsorten. |
> | Aktion | Microsoft.Web/geoRegions/Read | Ruft die Liste mit geografischen Regionen ab. |
> | Aktion | microsoft.web/hostingenvironments/capacities/read | Dient zum Abrufen von Hostingumgebungskapazitäten. |
> | Aktion | Microsoft.Web/hostingEnvironments/Delete | Dient zum Löschen einer App Service-Umgebung. |
> | Aktion | microsoft.web/hostingenvironments/detectors/read | Hiermit rufen Sie Erkennungsmodule für Hostingumgebungen ab. |
> | Aktion | microsoft.web/hostingenvironments/diagnostics/read | Dient zum Abrufen von Hostingumgebungsdiagnosen. |
> | Aktion | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Ruft die Netzwerkendpunkte aller eingehenden Abhängigkeiten ab. |
> | Aktion | Microsoft.Web/hostingEnvironments/Join/Action | Tritt einer App Service-Umgebung bei. |
> | Aktion | microsoft.web/hostingenvironments/metricdefinitions/read | Dient zum Abrufen von Metrikdefinitionen für Hostingumgebungen. |
> | Aktion | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Dient zum Abrufen von MultiRole-Poolmetrikdefinitionen für Hostingumgebungen. |
> | Aktion | microsoft.web/hostingenvironments/multirolepools/metrics/read | Dient zum Abrufen von MultiRole-Poolmetriken für Hostingumgebungen. |
> | Aktion | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Dient zum Abrufen der Eigenschaften eines FrontEnd-Pools in einer App Service-Umgebung. |
> | Aktion | microsoft.web/hostingenvironments/multirolepools/skus/read | Dient zum Abrufen von MultiRole-Pool-SKUs für Hostingumgebungen. |
> | Aktion | microsoft.web/hostingenvironments/multirolepools/usages/read | Dient zum Abrufen von MultiRole-Poolverwendungen für Hostingumgebungen. |
> | Aktion | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Dient zum Erstellen eines neuen FrontEnd-Pools in einer App Service-Umgebung oder zum Aktualisieren eines bereits vorhandenen. |
> | Aktion | microsoft.web/hostingenvironments/operations/read | Dient zum Abrufen von Hostingumgebungsvorgängen. |
> | Aktion | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Ruft die Netzwerkendpunkte aller ausgehenden Abhängigkeiten ab. |
> | Aktion | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Bestätigt Verbindungen mit privaten Endpunkten |
> | Aktion | Microsoft.Web/hostingEnvironments/Read | Dient zum Abrufen der Eigenschaften einer App Service-Umgebung. |
> | Aktion | Microsoft.Web/hostingEnvironments/reboot/Action | Dient zum Neustarten aller Computer in einer App Service-Umgebung. |
> | Aktion | microsoft.web/hostingenvironments/resume/action | Dient zum Fortsetzen von Hostingumgebungen. |
> | Aktion | microsoft.web/hostingenvironments/serverfarms/read | Dient zum Abrufen von App Service-Plänen für Hostingumgebungen. |
> | Aktion | microsoft.web/hostingenvironments/sites/read | Dient zum Abrufen von Web-Apps für Hostingumgebungen. |
> | Aktion | microsoft.web/hostingenvironments/suspend/action | Dient zum Anhalten von Hostingumgebungen. |
> | Aktion | microsoft.web/hostingenvironments/usages/read | Dient zum Abrufen von Hostingumgebungsverwendungen. |
> | Aktion | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Dient zum Abrufen von Workerpool-Metrikdefinitionen für Hostingumgebungen. |
> | Aktion | microsoft.web/hostingenvironments/workerpools/metrics/read | Dient zum Abrufen von Workerpoolmetriken für Hostingumgebungen. |
> | Aktion | Microsoft.Web/hostingEnvironments/workerPools/Read | Dient zum Abrufen der Eigenschaften eines Workerpools in einer App Service-Umgebung. |
> | Aktion | microsoft.web/hostingenvironments/workerpools/skus/read | Dient zum Abrufen von Workerpool-SKUs für Hostingumgebungen. |
> | Aktion | microsoft.web/hostingenvironments/workerpools/usages/read | Dient zum Abrufen von Workerpoolverwendungen für Hostingumgebungen. |
> | Aktion | Microsoft.Web/hostingEnvironments/workerPools/Write | Dient zum Erstellen eines neuen Workerpools in einer App Service-Umgebung oder zum Aktualisieren eines bereits vorhandenen. |
> | Aktion | Microsoft.Web/hostingEnvironments/Write | Dient zum Erstellen einer neuen App Service-Umgebung oder zum Aktualisieren einer bereits vorhandenen. |
> | Aktion | microsoft.web/ishostingenvironmentnameavailable/read | Dient zum Abrufen, ob der Hostingumgebungsname verfügbar ist. |
> | Aktion | microsoft.web/ishostnameavailable/read | Dient zum Prüfen, ob der Hostname verfügbar ist. |
> | Aktion | microsoft.web/isusernameavailable/read | Dient zum Prüfen, ob der Benutzername verfügbar ist. |
> | Aktion | Microsoft.Web/listSitesAssignedToHostName/Read | Dient zum Abrufen der Namen von Websites, die dem Hostnamen zugewiesen sind. |
> | Aktion | microsoft.web/locations/apioperations/read | Dient zum Abrufen von Standorten für API-Vorgänge. |
> | Aktion | microsoft.web/locations/connectiongatewayinstallations/read | Dient zum Abrufen von Standorten für Verbindungsgatewayinstallationen. |
> | Aktion | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | VNET- oder-Subnetzlöschbenachrichtigung für Standorte. |
> | Aktion | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrahiert eine API-Definition aus einer WSDL-Schnittstelle für Speicherorte. |
> | Aktion | microsoft.web/locations/listwsdlinterfaces/action | Listet WSDL-Schnittstellen für Speicherorte auf. |
> | Aktion | microsoft.web/locations/managedapis/apioperations/read | Dient zum Abrufen verwalteter API-Vorgänge. |
> | Aktion | Microsoft.Web/locations/managedapis/Join/Action | Verknüpft eine verwaltete API. |
> | Aktion | microsoft.web/locations/managedapis/read | Dient zum Abrufen verwalteter APIs für Standorte. |
> | Aktion | microsoft.web/operations/read | Dient zum Abrufen von Vorgängen. |
> | Aktion | microsoft.web/publishingusers/read | Dient zum Abrufen von Veröffentlichungsbenutzern. |
> | Aktion | microsoft.web/publishingusers/write | Dient zum Aktualisieren von Veröffentlichungsbenutzern. |
> | Aktion | Microsoft.Web/recommendations/Read | Dient zum Abrufen der Liste mit Empfehlungen für Abonnements. |
> | Aktion | microsoft.web/register/action | Dient zum Registrieren des Microsoft.Web-Ressourcenanbieters für das Abonnement. |
> | Aktion | microsoft.web/resourcehealthmetadata/read | Ruft Metadaten zur Ressourcenintegrität ab. |
> | Aktion | microsoft.web/serverfarms/capabilities/read | Dient zum Abrufen von Funktionen für App Service-Pläne. |
> | Aktion | Microsoft.Web/serverfarms/Delete | Löschen eines vorhandenen App Service-Plans |
> | Aktion | microsoft.web/serverfarms/firstpartyapps/settings/delete | Dient zum Löschen von Erstanbieter-App-Einstellungen für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/firstpartyapps/settings/read | Dient zum Abrufen von Erstanbieter-App-Einstellungen für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/firstpartyapps/settings/write | Dient zum Aktualisieren von Erstanbieter-App-Einstellungen für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Dient zum Löschen von Relaynamespace-Agents für Hybridverbindungen für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Dient zum Abrufen von Relaynamespace-Agents für Hybridverbindungen für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Dient zum Abrufen von Hybridverbindungsnamespace-Relay-Web-Apps für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Dient zum Abrufen von Hybridverbindungsplan-Grenzwerten für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/hybridconnectionrelays/read | Dient zum Abrufen von Hybridverbindungsrelays für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/metricdefinitions/read | Dient zum Abrufen von Metrikdefinitionen für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/metrics/read | Dient zum Abrufen von Metriken für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/operationresults/read | Dient zum Abrufen von Vorgangsergebnissen für App Service-Pläne. |
> | Aktion | Microsoft.Web/serverfarms/Read | Dient zum Abrufen der Eigenschaften für einen App Service-Plan. |
> | Aktion | Microsoft.Web/serverfarms/restartSites/Action | Dient zum Neustarten aller Web-Apps in einem App Service-Plan. |
> | Aktion | microsoft.web/serverfarms/sites/read | Dient zum Abrufen von Web-Apps für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/skus/read | Dient zum Abrufen von SKUs für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/usages/read | Dient zum Abrufen von Verwendungen für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Dient zum Aktualisieren von Gateways für Verbindungen mit virtuellen Netzwerken für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/virtualnetworkconnections/read | Dient zum Abrufen von Verbindungen mit virtuellen Netzwerken für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Dient zum Löschen von Routen für Verbindungen mit virtuellen Netzwerken für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Dient zum Abrufen von Routen für Verbindungen mit virtuellen Netzwerken für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Dient zum Aktualisieren von Routen für Verbindungen mit virtuellen Netzwerken für App Service-Pläne. |
> | Aktion | microsoft.web/serverfarms/workers/reboot/action | Dient zum Neustarten von Workern für App Service-Pläne. |
> | Aktion | Microsoft.Web/serverfarms/Write | Dient zum Erstellen eines neuen App Service-Plans oder zum Aktualisieren eines bereits vorhandenen. |
> | Aktion | microsoft.web/sites/analyzecustomhostname/read | Dient zum Analysieren des benutzerdefinierten Hostnamens. |
> | Aktion | Microsoft.Web/sites/applySlotConfig/Action | Dient zum Anwenden der Web-App-Slotkonfiguration aus dem Zielslot auf die aktuelle Web-App. |
> | Aktion | Microsoft.Web/sites/backup/Action | Dient zum Erstellen einer neuen Web-App-Sicherung. |
> | Aktion | microsoft.web/sites/backup/read | Dient zum Abrufen der Web-App-Sicherung. |
> | Aktion | microsoft.web/sites/backup/write | Dient zum Aktualisieren der Web-App-Sicherung. |
> | Aktion | microsoft.web/sites/backups/action | Ermittelt eine vorhandene App-Sicherung, die aus einem Blob im Azure-Speicher wiederhergestellt werden kann. |
> | Aktion | microsoft.web/sites/backups/delete | Dient zum Löschen von Web-App-Sicherungen. |
> | Aktion | microsoft.web/sites/backups/list/action | Dient zum Auflisten von Web-App-Sicherungen. |
> | Aktion | Microsoft.Web/sites/backups/Read | Dient zum Abrufen der Eigenschaften einer Web-App-Sicherung. |
> | Aktion | microsoft.web/sites/backups/restore/action | Dient zum Wiederherstellen von Web-App-Sicherungen. |
> | Aktion | microsoft.web/sites/backups/write | Hiermit aktualisieren Sie Web-App-Sicherungen. |
> | Aktion | microsoft.web/sites/config/delete | Dient zum Löschen der Web-App-Konfiguration. |
> | Aktion | Microsoft.Web/sites/config/list/Action | Dient zum Auflisten der sicherheitsrelevanten Einstellungen der Web-App. Hierzu zählen etwa Veröffentlichungsanmeldeinformationen, App-Einstellungen und Verbindungszeichenfolgen. |
> | Aktion | Microsoft.Web/sites/config/Read | Dient zum Abrufen von Web-App-Konfigurationseinstellungen. |
> | Aktion | microsoft.web/sites/config/snapshots/read | Dient zum Abrufen von Momentaufnahmen der Web-Apps-Konfiguration. |
> | Aktion | Microsoft.Web/sites/config/Write | Dient zum Aktualisieren der Konfigurationseinstellungen der Web-App. |
> | Aktion | microsoft.web/sites/containerlogs/action | Hiermit rufen Sie gezippte Containerprotokolle für die Web-App auf. |
> | Aktion | microsoft.web/sites/containerlogs/download/action | Lädt Containerprotokolle von Web-Apps herunter. |
> | Aktion | microsoft.web/sites/continuouswebjobs/delete | Dient zum Löschen fortlaufender Webaufträge für Web-Apps. |
> | Aktion | microsoft.web/sites/continuouswebjobs/read | Dient zum Abrufen fortlaufender Webaufträge für Web-Apps. |
> | Aktion | microsoft.web/sites/continuouswebjobs/start/action | Dient zum Starten fortlaufender Webaufträge für Web-Apps. |
> | Aktion | microsoft.web/sites/continuouswebjobs/stop/action | Dient zum Beenden fortlaufender Webaufträge für Web-Apps. |
> | Aktion | Microsoft.Web/sites/Delete | Dient zum Löschen einer vorhandenen Web-App. |
> | Aktion | microsoft.web/sites/deployments/delete | Dient zum Löschen von Web-App-Bereitstellungen. |
> | Aktion | microsoft.web/sites/deployments/log/read | Dient zum Abrufen des Web-App-Bereitstellungsprotokolls. |
> | Aktion | microsoft.web/sites/deployments/read | Dient zum Abrufen von Web-App-Bereitstellungen. |
> | Aktion | microsoft.web/sites/deployments/write | Dient zum Aktualisieren von Web-App-Bereitstellungen. |
> | Aktion | microsoft.web/sites/detectors/read | Hiermit rufen Sie Erkennungsmodule für die Web-App auf. |
> | Aktion | microsoft.web/sites/diagnostics/analyses/execute/Action | Dient zum Ausführen der Analyse von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/analyses/read | Dient zum Abrufen der Analyse von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/aspnetcore/read | Ruft Web-Apps-Diagnosen für die ASP.NET Core-App ab. |
> | Aktion | microsoft.web/sites/diagnostics/autoheal/read | Dient zum Abrufen von AutoHeal für Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/deployment/read | Dient zum Abrufen einer Bereitstellung für Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/deployments/read | Dient zum Abrufen von Bereitstellungen für Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/detectors/execute/Action | Dient zum Ausführen der Erkennung von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/detectors/read | Dient zum Abrufen der Erkennung von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Dient zum Abrufen fehlerhafter Anforderungen bei Web-Apps-Diagnosen pro URI. |
> | Aktion | microsoft.web/sites/diagnostics/frebanalysis/read | Dient zum Abrufen der FREB-Analyse von Web-App-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/loganalyzer/read | Dient zum Abrufen der Protokollanalyse von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/read | Dient zum Abrufen der Kategorien von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/runtimeavailability/read | Dient zum Abrufen der Laufzeitverfügbarkeit für Web-App-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/servicehealth/read | Dient zum Abrufen der Dienstintegrität von Web-App-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Dient zum Abrufen der CPU-Analyse von Websites von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/sitecrashes/read | Dient zum Abrufen von Websiteabstürzen von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/sitelatency/read | Dient zum Abrufen von Websitelatenzen von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Dient zum Abrufen der Websitespeicheranalyse von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Dient zum Abrufen des Websiteupdates für die Einstellungen zum Neustarten der Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Dient zum Abrufen des vom Benutzer initiierten Websiteneustarts der Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/siteswap/read | Dient zum Abrufen des Websiteaustauschs von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/threadcount/read | Dient zum Abrufen der Threadanzahl von Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/workeravailability/read | Dient zum Abrufen der Workerverfügbarkeit für Web-App-Diagnosen. |
> | Aktion | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Dient zum Abrufen der Workerprozesswiederverwendung für Web-App-Diagnosen. |
> | Aktion | microsoft.web/sites/domainownershipidentifiers/read | Dient zum Abrufen von Domänenbesitz-IDs für Web-Apps. |
> | Aktion | microsoft.web/sites/domainownershipidentifiers/write | Dient zum Aktualisieren von Domänenbesitz-IDs für Web-Apps. |
> | Aktion | microsoft.web/sites/extensions/delete | Dient zum Löschen von Web-Apps-Websiteerweiterungen. |
> | Aktion | microsoft.web/sites/extensions/read | Dient zum Abrufen von Web-Apps-Websiteerweiterungen. |
> | Aktion | microsoft.web/sites/extensions/write | Dient zum Aktualisieren von Web-Apps-Websiteerweiterungen. |
> | Aktion | microsoft.web/sites/functions/action | Functions-Web-Apps |
> | Aktion | microsoft.web/sites/functions/delete | Dient zum Löschen von Web-App-Funktionen. |
> | Aktion | microsoft.web/sites/functions/listsecrets/action | Dient zum Auflisten der Geheimnisse für Web-App-Funktionen. |
> | Aktion | microsoft.web/sites/functions/masterkey/read | Dient zum Abrufen des Hauptschlüssels von Web-Apps-Funktionen. |
> | Aktion | microsoft.web/sites/functions/read | Dient zum Abrufen von Web-App-Funktionen. |
> | Aktion | microsoft.web/sites/functions/token/read | Dient zum Abrufen des Funktionstokens von Web-Apps. |
> | Aktion | microsoft.web/sites/functions/write | Dient zum Aktualisieren von Web-App-Funktionen. |
> | Aktion | microsoft.web/sites/hostnamebindings/delete | Dient zum Löschen von Hostnamenbindungen für Web-Apps. |
> | Aktion | microsoft.web/sites/hostnamebindings/read | Dient zum Abrufen von Hostnamenbindungen für Web-Apps. |
> | Aktion | microsoft.web/sites/hostnamebindings/write | Dient zum Aktualisieren von Hostnamenbindungen für Web-Apps. |
> | Aktion | microsoft.web/sites/hostruntime/functions/keys/read | Ruft Funktionsschlüssel der Hostruntime für Web-Apps ab. |
> | Aktion | Microsoft.Web/sites/hostruntime/host/_master/read | Abrufen des Hauptschlüssels der Funktionen-App für Aministratorvorgänge |
> | Aktion | Microsoft.Web/sites/hostruntime/host/action | Durchführen von Laufzeitaktionen für die Funktionen-App wie Synchronisieren von Triggern, Hinzufügen von Funktionen, Aufrufen von Funktionen, Löschen von Funktionen usw. |
> | Aktion | microsoft.web/sites/hostruntime/host/read | Ruft den Hostruntime-Host für Web-Apps ab. |
> | Aktion | microsoft.web/sites/hybridconnection/delete | Dient zum Löschen der Hybridverbindung für Web-Apps. |
> | Aktion | microsoft.web/sites/hybridconnection/read | Dient zum Abrufen der Hybridverbindung für Web-Apps. |
> | Aktion | microsoft.web/sites/hybridconnection/write | Dient zum Aktualisieren der Hybridverbindung für Web-Apps. |
> | Aktion | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Dient zum Löschen der Relaynamespace-Agents für Hybridverbindungen für Web-Apps. |
> | Aktion | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Dient zum Auflisten der Schlüssel von Relaynamespace-Agents für Hybridverbindungen für Web-Apps. |
> | Aktion | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Dient zum Abrufen von Relaynamespace-Agents für Hybridverbindungen für Web-Apps. |
> | Aktion | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Dient zum Aktualisieren des Relaynamespace-Agents für Hybridverbindungen für Web-Apps. |
> | Aktion | microsoft.web/sites/hybridconnectionrelays/read | Dient zum Abrufen von Hybridverbindungsrelays für Web-Apps. |
> | Aktion | microsoft.web/sites/instances/deployments/delete | Dient zum Löschen von Web-Apps-Instanzbereitstellungen. |
> | Aktion | microsoft.web/sites/instances/deployments/read | Dient zum Abrufen von Prozessen für Web-App-Bereitstellungen. |
> | Aktion | microsoft.web/sites/instances/extensions/log/read | Dient zum Abrufen des Web-App-Instanzerweiterungsprotokolls. |
> | Aktion | microsoft.web/sites/instances/extensions/processes/read | Ruft Prozesse der Erweiterungen von Web-App-Instanzen ab. |
> | Aktion | microsoft.web/sites/instances/extensions/read | Dient zum Abrufen der Erweiterungen von Web-App-Instanzen. |
> | Aktion | microsoft.web/sites/instances/processes/delete | Dient zum Löschen von Prozessen für Web-App-Instanzen. |
> | Aktion | microsoft.web/sites/instances/processes/modules/read | Ruft Module der Prozesse für Web-App-Instanzen ab. |
> | Aktion | microsoft.web/sites/instances/processes/read | Dient zum Abrufen von Prozessen für Web-App-Instanzen. |
> | Aktion | microsoft.web/sites/instances/processes/threads/read | Dient zum Abrufen von Prozess-Threads für Web-App-Instanzen. |
> | Aktion | microsoft.web/sites/instances/read | Dient zum Abrufen von Web-App-Instanzen. |
> | Aktion | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Web-Apps zum Auflisten des Triggerstatus der Synchronisierungsfunktion |
> | Aktion | microsoft.web/sites/metricdefinitions/read | Dient zum Abrufen von Metrikdefinitionen für Web-Apps. |
> | Aktion | microsoft.web/sites/metrics/read | Dient zum Abrufen von Web-App-Metriken. |
> | Aktion | microsoft.web/sites/metricsdefinitions/read | Dient zum Abrufen von Metrikdefinitionen für Web-Apps. |
> | Aktion | microsoft.web/sites/migratemysql/action | Dient zum Durchführen von Migrationen mithilfe von MySql und Web-Apps. |
> | Aktion | microsoft.web/sites/migratemysql/read | Dient zum Abrufen von Web-Apps für die Migration mit MySql. |
> | Aktion | microsoft.web/sites/networktrace/action | Netzwerkablaufverfolgungs-Web-Apps |
> | Aktion | microsoft.web/sites/networktraces/operationresults/read | Ruft Vorgangsergebnisse für die Web-Apps-Netzwerkablaufverfolgung ab. |
> | Aktion | microsoft.web/sites/newpassword/action | NewPassword-Web-Apps |
> | Aktion | microsoft.web/sites/operationresults/read | Dient zum Abrufen der Ergebnisse von Web-App-Vorgängen. |
> | Aktion | microsoft.web/sites/operations/read | Dient zum Abrufen von Web-Apps-Vorgängen. |
> | Aktion | microsoft.web/sites/perfcounters/read | Dient zum Abrufen von Leistungsindikatoren für Web-Apps. |
> | Aktion | microsoft.web/sites/premieraddons/delete | Dient zum Löschen von Premier-Add-Ons für Web-Apps. |
> | Aktion | microsoft.web/sites/premieraddons/read | Dient zum Abrufen von Premier-Add-Ons für Web-Apps. |
> | Aktion | microsoft.web/sites/premieraddons/write | Dient zum Aktualisieren von Premier-Add-Ons für Web-Apps. |
> | Aktion | microsoft.web/sites/privateaccess/read | Abrufen von Daten für die Aktivierung des privaten Websitezugriffs und autorisierter virtueller Netzwerke, die auf die Website zugreifen können |
> | Aktion | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Bestätigt Verbindungen mit privaten Endpunkten |
> | Aktion | microsoft.web/sites/processes/modules/read | Dient zum Abrufen von Web-Apps-Prozessmodulen. |
> | Aktion | microsoft.web/sites/processes/read | Dient zum Abrufen von Web-Apps-Prozessen. |
> | Aktion | microsoft.web/sites/processes/threads/read | Dient zum Abrufen von Web-Apps-Prozessthreads. |
> | Aktion | microsoft.web/sites/publiccertificates/delete | Löscht die öffentlichen Zertifikate für Web-Apps. |
> | Aktion | microsoft.web/sites/publiccertificates/read | Ruft die öffentlichen Zertifikate für Web-Apps ab. |
> | Aktion | microsoft.web/sites/publiccertificates/write | Aktualisiert die öffentlichen Zertifikate für Web-Apps. |
> | Aktion | Microsoft.Web/sites/publish/Action | Dient zum Veröffentlichen einer Web-App. |
> | Aktion | Microsoft.Web/sites/publishxml/Action | Dient zum Abrufen des Veröffentlichungsprofils (XML) für eine Web-App. |
> | Aktion | microsoft.web/sites/publishxml/read | Dient zum Abrufen des XML-Codes für die Veröffentlichung von Web-Apps. |
> | Aktion | Microsoft.Web/sites/Read | Dient zum Abrufen der Eigenschaften einer Web-App. |
> | Aktion | microsoft.web/sites/recommendationhistory/read | Dient zum Abrufen des Empfehlungsverlaufs für Web-Apps. |
> | Aktion | microsoft.web/sites/recommendations/disable/action | Dient zum Deaktivieren von Web-App-Empfehlungen. |
> | Aktion | Microsoft.Web/sites/recommendations/Read | Dient zum Abrufen der Liste mit Empfehlungen für Web-Apps. |
> | Aktion | microsoft.web/sites/recover/action | Stellt Web-Apps wieder her. |
> | Aktion | Microsoft.Web/sites/resetSlotConfig/Action | Dient zum Zurücksetzen der Web-App-Konfiguration. |
> | Aktion | microsoft.web/sites/resourcehealthmetadata/read | Ruft Metadaten zur Ressourcenintegrität von Web-Apps ab. |
> | Aktion | Microsoft.Web/sites/restart/Action | Dient zum Neustarten einer Web-App. |
> | Aktion | microsoft.web/sites/restore/read | Dient zum Abrufen der Web-App-Wiederherstellung. |
> | Aktion | microsoft.web/sites/restore/write | Dient zum Wiederherstellen von Web-Apps. |
> | Aktion | microsoft.web/sites/restorefrombackupblob/action | Wiederherstellen einer Web-App aus dem Sicherungsblob |
> | Aktion | microsoft.web/sites/restorefromdeletedwebapp/action | Hiermit stellen Sie Web-Apps aus einer gelöschten App wieder her. |
> | Aktion | microsoft.web/sites/restoresnapshot/action | Stellt Momentaufnahmen von Web-Apps wieder her. |
> | Aktion | microsoft.web/sites/siteextensions/delete | Dient zum Löschen von Web-Apps-Websiteerweiterungen. |
> | Aktion | microsoft.web/sites/siteextensions/read | Dient zum Abrufen von Web-Apps-Websiteerweiterungen. |
> | Aktion | microsoft.web/sites/siteextensions/write | Dient zum Aktualisieren von Web-Apps-Websiteerweiterungen. |
> | Aktion | microsoft.web/sites/slots/analyzecustomhostname/read | Dient zum Abrufen des benutzerdefinierten Hostnamens für die Analyse von Web-Apps-Slots. |
> | Aktion | Microsoft.Web/sites/slots/applySlotConfig/Action | Dient zum Anwenden der Web-App-Slotkonfiguration aus dem Zielslot auf den aktuellen Slot. |
> | Aktion | Microsoft.Web/sites/slots/backup/Action | Dient zum Erstellen einer neuen Web-App-Slotsicherung. |
> | Aktion | microsoft.web/sites/slots/backup/read | Dient zum Abrufen von Sicherungen von Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/backup/write | Dient zum Aktualisieren der Web-App-Slotsicherung. |
> | Aktion | microsoft.web/sites/slots/backups/action | Dient zum Erkennen von Sicherungen von Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/backups/delete | Dient zum Löschen von Sicherungen von Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/backups/list/action | Dient zum Auflisten von Web-App-Slotsicherungen. |
> | Aktion | Microsoft.Web/sites/slots/backups/Read | Dient zum Abrufen der Eigenschaften einer Sicherung von Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/backups/restore/action | Dient zum Wiederherstellen von Web-App-Slotsicherungen. |
> | Aktion | microsoft.web/sites/slots/config/delete | Dient zum Löschen der Konfiguration von Web-App-Slots. |
> | Aktion | Microsoft.Web/sites/slots/config/list/Action | Dient zum Auflisten der sicherheitsrelevanten Einstellungen des Web-App-Slots. Hierzu zählen etwa Veröffentlichungsanmeldeinformationen, App-Einstellungen und Verbindungszeichenfolgen. |
> | Aktion | Microsoft.Web/sites/slots/config/Read | Dient zum Abrufen der Konfigurationseinstellungen des Web-App-Slots. |
> | Aktion | Microsoft.Web/sites/slots/config/Write | Dient zum Aktualisieren der Konfigurationseinstellungen des Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/containerlogs/action | Hiermit rufen Sie gezippte Containerprotokolle für den Web-App-Slot auf. |
> | Aktion | microsoft.web/sites/slots/containerlogs/download/action | Lädt Containerprotokolle von Web-App-Slots herunter. |
> | Aktion | microsoft.web/sites/slots/continuouswebjobs/delete | Dient zum Löschen fortlaufender Webaufträge für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/continuouswebjobs/read | Dient zum Abrufen fortlaufender Webaufträge für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/continuouswebjobs/start/action | Dient zum Starten fortlaufender Webaufträge für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/continuouswebjobs/stop/action | Dient zum Beenden fortlaufender Webaufträge für Web-App-Slots. |
> | Aktion | Microsoft.Web/sites/slots/Delete | Dient zum Löschen eines vorhandenen Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/deployments/delete | Dient zum Löschen von Web-App-Slotbereitstellungen. |
> | Aktion | microsoft.web/sites/slots/deployments/log/read | Dient zum Abrufen des Protokolls für Web-App-Slotbereitstellungen. |
> | Aktion | microsoft.web/sites/slots/deployments/read | Dient zum Abrufen von Web-App-Slotbereitstellungen. |
> | Aktion | microsoft.web/sites/slots/deployments/write | Dient zum Aktualisieren von Web-App-Slotbereitstellungen. |
> | Aktion | microsoft.web/sites/slots/detectors/read | Dient zum Abrufen von Web-App-Sloterkennungen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Dient zum Ausführen der Analyse von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/analyses/read | Dient zum Abrufen der Analyse von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Ruft Web-Apps-Slotdiagnosen für die ASP.NET Core-App ab. |
> | Aktion | microsoft.web/sites/slots/diagnostics/autoheal/read | Dient zum Abrufen von AutoHeal für Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/deployment/read | Dient zum Abrufen einer Bereitstellung für Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/deployments/read | Dient zum Abrufen von Bereitstellungen für Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Dient zum Ausführen der Erkennung von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/detectors/read | Dient zum Abrufen der Erkennung von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Dient zum Abrufen der FREB-Analyse von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Dient zum Abrufen der Protokollanalyse von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/read | Dient zum Abrufen der Web-Apps-Slotdiagnose. |
> | Aktion | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Dient zum Abrufen der Laufzeitverfügbarkeit für Web-Apps-Diagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/servicehealth/read | Dient zum Abrufen der Dienstintegrität von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Dient zum Abrufen der CPU-Analyse von Websites von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Dient zum Abrufen der Siteabstürze von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/sitelatency/read | Dient zum Abrufen der Websitelatenzen von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Dient zum Abrufen der Websitespeicheranalyse von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Dient zum Abrufen des Websiteupdates für die Einstellungen zum Neustarten der Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Dient zum Abrufen des vom Benutzer initiierten Websiteneustarts der Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/siteswap/read | Dient zum Abrufen des Websiteaustauschs von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/threadcount/read | Dient zum Abrufen der Threadanzahl von Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/workeravailability/read | Dient zum Abrufen der Workerverfügbarkeit für Web-App-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Dient zum Abrufen der Workerprozesswiederverwendung für Web-Apps-Slotdiagnosen. |
> | Aktion | microsoft.web/sites/slots/domainownershipidentifiers/read | Dient zum Abrufen von Domänenbesitz-IDs für Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/functions/listsecrets/action | Dient zum Auflisten der Geheimnisse für Web-App-Slot-Funktionen. |
> | Aktion | microsoft.web/sites/slots/functions/read | Dient zum Abrufen von Web-App-Slotfunktionen. |
> | Aktion | microsoft.web/sites/slots/hostnamebindings/delete | Dient zum Löschen von Hostnamenbindungen für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/hostnamebindings/read | Dient zum Abrufen von Hostnamenbindungen für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/hostnamebindings/write | Dient zum Aktualisieren von Hostnamenbindungen für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/hybridconnection/delete | Dient zum Löschen der Hybridverbindung für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/hybridconnection/read | Dient zum Abrufen der Hybridverbindung für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/hybridconnection/write | Dient zum Aktualisieren der Hybridverbindung für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Dient zum Löschen der Relaynamespace-Agents für Hybridverbindungen für Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Dient zum Aktualisieren der Relaynamespace-Agents für Hybridverbindungen für Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/hybridconnectionrelays/read | Dient zum Abrufen der Relay-Agents für Hybridverbindungen für Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/instances/deployments/read | Dient zum Abrufen von Bereitstellungen für Web-App-Slotinstanzen. |
> | Aktion | microsoft.web/sites/slots/instances/processes/delete | Dient zum Löschen von Prozessen für Web-Apps-Slotinstanzen. |
> | Aktion | microsoft.web/sites/slots/instances/processes/read | Dient zum Abrufen von Prozessen für Web-App-Slotinstanzen. |
> | Aktion | microsoft.web/sites/slots/instances/read | Dient zum Abrufen von Web-App-Slotinstanzen. |
> | Aktion | microsoft.web/sites/slots/metricdefinitions/read | Dient zum Abrufen von Metrikdefinitionen für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/metrics/read | Dient zum Abrufen von Metriken für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/migratemysql/read | Dient zum Abrufen von Web-Apps-Slots für die Migration mit MySql. |
> | Aktion | microsoft.web/sites/slots/networktrace/action | Web-Apps-Slots für die Netzwerkablaufverfolgung |
> | Aktion | microsoft.web/sites/slots/networktraces/operationresults/read | Ruft Vorgangsergebnisse für die Netzwerkablaufverfolgung von Web-Apps-Slots ab. |
> | Aktion | microsoft.web/sites/slots/newpassword/action | NewPassword-Web-App-Slots |
> | Aktion | microsoft.web/sites/slots/operationresults/read | Dient zum Abrufen der Ergebnisse von Web-App-Slotvorgängen. |
> | Aktion | microsoft.web/sites/slots/operations/read | Dient zum Abrufen von Web-Apps-Slotvorgängen. |
> | Aktion | microsoft.web/sites/slots/perfcounters/read | Dient zum Abrufen von Leistungsindikatoren für Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/phplogging/read | Dient zum Abrufen der PHP-Protokollierung für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/premieraddons/delete | Dient zum Löschen von Premier-Add-Ons für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/premieraddons/read | Dient zum Abrufen von Premier-Add-Ons für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/premieraddons/write | Dient zum Aktualisieren von Premier-Add-Ons für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/processes/read | Ruft Prozesse von Web-App-Slots ab. |
> | Aktion | microsoft.web/sites/slots/publiccertificates/delete | Hiermit löschen Sie öffentliche Zertifikate für Web-Apps-Slots ab. |
> | Aktion | microsoft.web/sites/slots/publiccertificates/read | Ruft die öffentlichen Zertifikate für Web-Apps-Slots ab. |
> | Aktion | microsoft.web/sites/slots/publiccertificates/write | Erstellt oder aktualisiert die öffentlichen Zertifikate für Web-Apps-Slots. |
> | Aktion | Microsoft.Web/sites/slots/publish/Action | Dient zum Veröffentlichen eines Web-App-Slots. |
> | Aktion | Microsoft.Web/sites/slots/publishxml/Action | Dient zum Abrufen des Veröffentlichungsprofils (XML) für einen Web-App-Slot. |
> | Aktion | Microsoft.Web/sites/slots/Read | Dient zum Abrufen der Eigenschaften eines Web-App-Bereitstellungsslots. |
> | Aktion | microsoft.web/sites/slots/recover/action | Stellt Web-App-Slots wieder her. |
> | Aktion | Microsoft.Web/sites/slots/resetSlotConfig/Action | Dient zum Zurücksetzen der Web-App-Slotkonfiguration. |
> | Aktion | microsoft.web/sites/slots/resourcehealthmetadata/read | Ruft Metadaten zur Ressourcenintegrität von Web-Apps-Slots ab. |
> | Aktion | Microsoft.Web/sites/slots/restart/Action | Dient zum Neustarten eines Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/restore/read | Dient zum Abrufen der Wiederherstellung für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/restore/write | Dient zum Wiederherstellen von Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/restorefrombackupblob/action | Wiederherstellen eines Web-App-Slots aus dem Sicherungsblob |
> | Aktion | microsoft.web/sites/slots/restorefromdeletedwebapp/action | Hiermit stellen Sie Web-App-Slots aus einer gelöschten App wieder her. |
> | Aktion | microsoft.web/sites/slots/restoresnapshot/action | Stellt Momentaufnahme von Web-App-Slots wieder her. |
> | Aktion | microsoft.web/sites/slots/siteextensions/delete | Dient zum Löschen von Websiteerweiterungen von Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/siteextensions/read | Dient zum Abrufen von Websiteerweiterungen von Web-Apps-Slots. |
> | Aktion | microsoft.web/sites/slots/siteextensions/write | Dient zum Aktualisieren von Websiteerweiterungen von Web-Apps-Slots. |
> | Aktion | Microsoft.Web/sites/slots/slotsdiffs/Action | Dient zum Abrufen von Konfigurationsunterschieden zwischen Web-App und Slots. |
> | Aktion | Microsoft.Web/sites/slots/slotsswap/Action | Dient zum Austauschen von Web-App-Bereitstellungsslots. |
> | Aktion | microsoft.web/sites/slots/snapshots/read | Dient zum Abrufen von Web-Apps-Slotmomentaufnahmen. |
> | Aktion | Microsoft.Web/sites/slots/sourcecontrols/Delete | Dient zum Löschen der Konfigurationseinstellungen für die Quellcodeverwaltung des Web-App-Slots. |
> | Aktion | Microsoft.Web/sites/slots/sourcecontrols/Read | Dient zum Abrufen der Konfigurationseinstellungen für die Quellcodeverwaltung des Web-App-Slots. |
> | Aktion | Microsoft.Web/sites/slots/sourcecontrols/Write | Dient zum Aktualisieren der Konfigurationseinstellungen für die Quellcodeverwaltung des Web-App-Slots. |
> | Aktion | Microsoft.Web/sites/slots/start/Action | Dient zum Starten eines Web-App-Slots. |
> | Aktion | Microsoft.Web/sites/slots/stop/Action | Dient zum Beenden eines Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/sync/action | Dient zum Synchronisieren von Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/triggeredwebjobs/delete | Dient zum Löschen ausgelöster Webaufträge für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/triggeredwebjobs/read | Dient zum Abrufen ausgelöster Webaufträge für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/triggeredwebjobs/run/action | Dient zum Ausführen ausgelöster Webaufträge für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/usages/read | Dient zum Abrufen von Verwendungen für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/virtualnetworkconnections/delete | Dient zum Löschen von Verbindungen mit virtuellen Netzwerken für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Dient zum Aktualisieren von Gateways für Verbindungen mit virtuellen Netzwerken für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/virtualnetworkconnections/read | Dient zum Abrufen von Verbindungen mit virtuellen Netzwerken für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/virtualnetworkconnections/write | Dient zum Aktualisieren von Verbindungen mit virtuellen Netzwerken für Web-App-Slots. |
> | Aktion | microsoft.web/sites/slots/webjobs/read | Dient zum Abrufen von Webaufträgen für Web-App-Slots. |
> | Aktion | Microsoft.Web/sites/slots/Write | Dient zum Erstellen eines neuen Web-App-Slots oder zum Aktualisieren eines bereits vorhandenen. |
> | Aktion | Microsoft.Web/sites/slotsdiffs/Action | Dient zum Abrufen von Konfigurationsunterschieden zwischen Web-App und Slots. |
> | Aktion | Microsoft.Web/sites/slotsswap/Action | Dient zum Austauschen von Web-App-Bereitstellungsslots. |
> | Aktion | microsoft.web/sites/snapshots/read | Dient zum Abrufen von Web-App-Momentaufnahmen. |
> | Aktion | Microsoft.Web/sites/sourcecontrols/Delete | Dient zum Löschen der Konfigurationseinstellungen für die Quellcodeverwaltung der Web-App. |
> | Aktion | Microsoft.Web/sites/sourcecontrols/Read | Dient zum Abrufen der Konfigurationseinstellungen für die Quellcodeverwaltung der Web-App. |
> | Aktion | Microsoft.Web/sites/sourcecontrols/Write | Dient zum Aktualisieren der Konfigurationseinstellungen für die Quellcodeverwaltung der Web-App. |
> | Aktion | Microsoft.Web/sites/start/Action | Dient zum Starten einer Web-App. |
> | Aktion | Microsoft.Web/sites/stop/Action | Dient zum Beenden einer Web-App. |
> | Aktion | microsoft.web/sites/sync/action | Synchronisierungs-Web-Apps |
> | Aktion | microsoft.web/sites/syncfunctiontriggers/action | Synchronisierungsfunktionstrigger für Web-Apps. |
> | Aktion | microsoft.web/sites/triggeredwebjobs/delete | Dient zum Löschen ausgelöster Webaufträge für Web-Apps. |
> | Aktion | microsoft.web/sites/triggeredwebjobs/history/read | Dient zum Abrufen des ausgelösten WebJobs-Verlaufs für Web-Apps. |
> | Aktion | microsoft.web/sites/triggeredwebjobs/read | Dient zum Abrufen ausgelöster Webaufträge für Web-Apps. |
> | Aktion | microsoft.web/sites/triggeredwebjobs/run/action | Dient zum Ausführen ausgelöster Webaufträge für Web-Apps. |
> | Aktion | microsoft.web/sites/usages/read | Dient zum Abrufen von Web-App-Verwendungen. |
> | Aktion | microsoft.web/sites/virtualnetworkconnections/delete | Dient zum Löschen von Verbindungen mit virtuellen Netzwerken für Web-Apps. |
> | Aktion | microsoft.web/sites/virtualnetworkconnections/gateways/read | Dient zum Abrufen von Gateways für Verbindungen mit virtuellen Netzwerken für Web-Apps. |
> | Aktion | microsoft.web/sites/virtualnetworkconnections/gateways/write | Dient zum Aktualisieren von Gateways für Verbindungen mit virtuellen Netzwerken für Web-Apps. |
> | Aktion | microsoft.web/sites/virtualnetworkconnections/read | Dient zum Abrufen von Verbindungen mit virtuellen Netzwerken für Web-Apps. |
> | Aktion | microsoft.web/sites/virtualnetworkconnections/write | Dient zum Aktualisieren von Verbindungen mit virtuellen Netzwerken für Web-Apps. |
> | Aktion | microsoft.web/sites/webjobs/read | Dient zum Abrufen von Webaufträgen für Web-Apps. |
> | Aktion | Microsoft.Web/sites/Write | Dient zum Erstellen einer neuen Web-App oder zum Aktualisieren einer bereits vorhandenen. |
> | Aktion | microsoft.web/skus/read | Dient zum Abrufen von SKUs. |
> | Aktion | microsoft.web/sourcecontrols/read | Dient zum Abrufen von Quellcodeverwaltungen. |
> | Aktion | microsoft.web/sourcecontrols/write | Dient zum Aktualisieren von Quellcodeverwaltungen. |
> | Aktion | microsoft.web/unregister/action | Dient zum Aufheben der Registrierung des Microsoft.Web-Ressourcenanbieters für das Abonnement. |
> | Aktion | microsoft.web/validate/action | Dient zur Überprüfung. |
> | Aktion | microsoft.web/verifyhostingenvironmentvnet/action | Überprüft das VNET der Hostingumgebung. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Aktionstyp | Vorgang | Beschreibung |
> | --- | --- | --- |
> | Aktion | Microsoft.WorkloadMonitor/components/read | Ruft Komponenten für die Ressource ab |
> | Aktion | Microsoft.WorkloadMonitor/componentsSummary/read | Ruft eine Zusammenfassung von Komponenten ab |
> | Aktion | Microsoft.WorkloadMonitor/monitorInstances/read | Ruft Überwachungsinstanzen für die Ressource ab |
> | Aktion | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Ruft eine Zusammenfassung von Überwachungsinstanzen ab |
> | Aktion | Microsoft.WorkloadMonitor/monitors/read | Ruft Überwachungen für die Ressource ab |
> | Aktion | Microsoft.WorkloadMonitor/monitors/write | Überwachung für die Ressource konfigurieren |
> | Aktion | Microsoft.WorkloadMonitor/notificationSettings/read | Ruft Benachrichtigungseinstellungen für die Ressource ab |
> | Aktion | Microsoft.WorkloadMonitor/notificationSettings/write | Benachrichtigungseinstellungen für die Ressource konfigurieren |
> | Aktion | Microsoft.WorkloadMonitor/operations/read | Ruft die unterstützten Vorgänge ab |

## <a name="next-steps"></a>Nächste Schritte

- [Benutzerdefinierte Rollen für Azure-Ressourcen](custom-roles.md)
- [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](built-in-roles.md)
