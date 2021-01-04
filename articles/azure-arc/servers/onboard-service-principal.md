---
title: Verbinden von Hybridcomputern mit Azure im großen Stil
description: In diesem Artikel erfahren Sie, wie Sie Computer über Azure Arc-fähige Server unter Verwendung eines Dienstprinzipals mit Azure verbinden.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: f71bbc46ccac533db39176363f206ab033e60316
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360120"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Verbinden von Hybridcomputern mit Azure im großen Stil

Sie können Azure Arc-fähige Server je nach Anforderungen mithilfe verschiedener flexibler Optionen für mehrere Windows- oder Linux-Computer in Ihrer Umgebung aktivieren. Mithilfe des von uns bereitgestellten Vorlagenskripts können Sie jeden Schritt der Installation automatisieren, einschließlich des Einrichtens der Verbindung mit Azure Arc. Allerdings müssen Sie dieses Skript interaktiv mit einem Konto ausführen, das über erhöhte Berechtigungen auf dem Zielcomputer und in Azure verfügt. Um die Computer mit Azure Arc-fähigen Servern zu verbinden, können Sie einen Azure Active Directory-[Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md) verwenden, anstatt Ihre privilegierte Identität zum [interaktiven Verbinden des Computers](onboard-portal.md) zu nutzen. Bei einem Dienstprinzipal handelt es sich um eine spezielle eingeschränkte Verwaltungsidentität, der nur die Mindestberechtigung erteilt wird, die erforderlich ist, um Computer mithilfe des Befehls `azcmagent` mit Azure zu verbinden. Dies ist sicherer als die Verwendung eines Kontos mit höherer Berechtigung, wie z. B. einem Mandantenadministrator, und befolgt unsere bewährten Methoden für die Sicherheit der Zugriffssteuerung. Der Dienstprinzipal wird nur während des Onboardings verwendet und nicht für andere Zwecke.  

Die Methoden zum Installieren und Konfigurieren des Connected Machine-Agents erfordern, dass die von Ihnen verwendete automatisierte Methode über Administratorberechtigungen auf den Computern verfügt. Unter Linux muss hierfür das root-Konto verwendet werden. Unter Windows müssen Sie Mitglied der Gruppe „Lokale Administratoren“ sein.

Bevor Sie beginnen, sollten Sie die [Voraussetzungen](agent-overview.md#prerequisites) überprüfen und sicherstellen, dass Ihr Abonnement und Ihre Ressourcen auch diese Anforderungen erfüllen. Informationen zu unterstützten Regionen und andere Überlegungen finden Sie unter [Unterstützte Azure-Regionen](overview.md#supported-regions).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Am Ende dieses Prozesses haben Sie dann Ihre Hybridcomputer erfolgreich mit Azure Arc-fähigen Servern verbunden.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Erstellen eines Dienstprinzipals für flexibles Onboarding

Sie können [Azure PowerShell](/powershell/azure/install-az-ps) verwenden, um einen Dienstprinzipal mit dem Cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) zu erstellen. Alternativ können Sie für diese Aufgabe auch die unter [Erstellen eines Dienstprinzipals mit dem Azure-Portal](../../active-directory/develop/howto-create-service-principal-portal.md) aufgeführten Schritte ausführen.

> [!NOTE]
> Beim Erstellen eines Dienstprinzipals muss Ihr Konto Besitzer oder Benutzerzugriffsadministrator in dem Abonnement sein, das Sie für das Onboarding verwenden möchten. Falls Sie nicht über ausreichende Berechtigungen zum Erstellen von Rollenzuweisungen verfügen, wird der Dienstprinzipal zwar vielleicht erstellt, kann aber keine Computer integrieren.
>

Um den Dienstprinzipal mithilfe der PowerShell zu erstellen, führen Sie Folgendes aus.

1. Führen Sie den folgenden Befehl aus. Sie müssen die Ausgabe des Cmdlets [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) in einer Variablen speichern, da Sie sonst das in einem späteren Schritt benötigte Kennwort nicht abrufen können.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Führen Sie zum Abrufen des in der Variablen `$sp` gespeicherten Kennworts den folgenden Befehl aus:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Suchen Sie in der Ausgabe den Kennwortwert unter dem Feld **Kennwort** , und kopieren Sie ihn. Suchen Sie außerdem den Wert unter dem Feld **ApplicationId** , und kopieren Sie ihn ebenfalls. Speichern Sie beide zur späteren Verwendung an einem sicheren Ort. Sollten Sie Ihr Dienstprinzipalkennwort vergessen oder verlieren, können Sie es mithilfe des Cmdlets [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) zurücksetzen.

Die Werte der folgenden Eigenschaften werden mit Parametern verwendet, die an `azcmagent` übergeben werden:

* Der Wert der Eigenschaft **ApplicationId** wird für den Parameter `--service-principal-id` verwendet.
* Der Wert der Eigenschaft **password** wird für den Parameter `--service-principal-secret` verwendet, um eine Verbindung mit dem Agent herzustellen.

> [!NOTE]
> Stellen Sie sicher, dass Sie die Eigenschaft **ApplicationId** des Dienstprinzipals verwenden, nicht die Eigenschaft **Id**.
>

Die Rolle **Onboarding von Azure Connected Machine** umfasst nur die Berechtigungen, die für das Onboarding eines Computers erforderlich sind. Sie können die Dienstprinzipalberechtigung zuweisen, damit deren Umfang eine Ressourcengruppe oder ein Abonnement umfasst. Informationen zum Hinzufügen von Rollenzuweisungen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md) oder [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Installieren des Agents und Herstellen einer Verbindung mit Azure

Mit den folgenden Schritten können Sie den Connected Machine-Agent auf Ihren Hybridcomputern mithilfe der Skriptvorlage installieren und konfigurieren, die ähnliche Schritte ausführt, wie im Artikel [Verbinden von Hybridcomputern mit Azure über das Azure-Portal](onboard-portal.md) beschrieben. Der Unterschied besteht im letzten Schritt, in dem Sie mit dem Befehl `azcmagent` unter Verwendung des Dienstprinzipals eine Verbindung mit Azure Arc herstellen.

Im Folgenden finden Sie die Einstellungen, die Sie für den Befehl `azcmagent` konfigurieren, damit dieser sie für den Dienstprinzipal verwendet.

* `tenant-id` : Die eindeutige ID (GUID), die Ihre dedizierte Instanz von Azure AD darstellt.
* `subscription-id` : Die Abonnement-ID (GUID) Ihres Azure-Abonnements, in das Computer aufgenommen werden sollen.
* `resource-group` : Der Name der Ressourcengruppe, zu der Ihre verbundenen Computer gehören sollen.
* `location` : Siehe [Unterstützte Azure-Regionen](overview.md#supported-regions). Bei diesem Standort kann es sich um denselben Standort wie bei der Ressourcengruppe oder um einen anderen Standort handeln.
* `resource-name` : ( *Optional* ) Wird für die Azure-Ressourcendarstellung Ihres lokalen Computers verwendet. Ohne Angabe dieses Werts wird der Hostname des Computers verwendet.

Weitere Informationen zum `azcmagent`-Befehlszeilentool finden Sie in der [Azcmagent-Referenz](./manage-agent.md).

### <a name="windows-installation-script"></a>Windows-Installationsskript

Im Folgenden finden Sie ein Beispiel für das Installationsskript für den Connected Machine-Agent für Windows, das so geändert wurde, dass der Dienstprinzipal zur Unterstützung einer vollständig automatisierten, nicht interaktiven Installation des Agents verwendet wird.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

>[!NOTE]
>Das Skript unterstützt nur die Ausführung in einer 64-Bit-Version von Windows PowerShell.
>

### <a name="linux-installation-script"></a>Linux-Installationsskript

Im Folgenden finden Sie ein Beispiel für das Installationsskript für den Connected Machine-Agent für Linux, das so geändert wurde, dass der Dienstprinzipal zur Unterstützung einer vollständig automatisierten, nicht interaktiven Installation des Agents verwendet wird.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

>[!NOTE]
>Sie müssen auf Linux-Computern über *Stamm* zugriffsberechtigungen verfügen, um **azcmagent** ausführen zu können.

Vergewissern Sie sich im Azure-Portal, dass die Serververbindung erfolgreich hergestellt wurde, nachdem Sie den Agent installiert und für die Verbindungsherstellung mit Azure Arc-fähigen Servern konfiguriert haben. Zeigen Sie Ihre Computer im [Azure-Portal](https://aka.ms/hybridmachineportal) an.

![Erfolgreiche Serververbindung](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung des Connected Machine-Agents](troubleshoot-agent-onboard.md).

- Erfahren Sie, wie Sie Ihren Computer mithilfe von [Azure Policy](../../governance/policy/overview.md) verwalten, wie z. B. bei der VM-[Gastkonfiguration](../../governance/policy/concepts/guest-configuration.md), dem Überprüfen, ob der Computer dem erwarteten Log Analytics-Arbeitsbereich Bericht erstattet, beim Aktivieren der Überwachung mit [Azure Monitor mit VMs](../../azure-monitor/insights/vminsights-enable-policy.md) und vieles mehr.

- Weitere Informationen zum [Log Analytics-Agent](../../azure-monitor/platform/log-analytics-agent.md). Der Log Analytics-Agent für Windows und Linux ist erforderlich, wenn Sie Daten zur Betriebssystem- und Workloadüberwachung erfassen, diese mithilfe von Automation Runbooks oder Funktionen wie Updateverwaltung oder andere Azure-Dienste wie [Azure Security Center](../../security-center/security-center-introduction.md) nutzen möchten.