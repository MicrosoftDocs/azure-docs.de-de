---
title: 'Tutorial: Bereitstellen einer Python-Django-App mit Postgres'
description: Hier erfahren Sie, wie Sie eine Python-Web-App mit einer PostgreSQL-Datenbank erstellen und in Azure bereitstellen. In diesem Tutorial wird das Django-Framework verwendet, und die App wird in Azure App Service für Linux gehostet.
ms.devlang: python
ms.topic: tutorial
ms.date: 02/02/2021
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 6112247a99c519aad8aadf7946c7707480b3b491
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558422"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Tutorial: Bereitstellen einer Django-Web-App mit PostgreSQL in Azure App Service

In diesem Tutorial erfahren Sie, wie Sie eine datengesteuerte Python-[Django](https://www.djangoproject.com/)-Web-App in [Azure App Service](overview.md) bereitstellen und mit einer Azure Database for Postgres-Datenbank verbinden. Von App Service wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt.

In diesem Tutorial wird die Azure CLI verwendet, um folgende Aufgaben auszuführen:

> [!div class="checklist"]
> * Einrichten der anfänglichen Umgebung mit Python und der Azure CLI
> * Erstellen einer Azure Database for PostgreSQL-Datenbankinstanz
> * Bereitstellen von Code für Azure App Service und Herstellen einer Verbindung mit PostgresSQL
> * Aktualisieren Ihres Codes und erneutes Bereitstellen
> * Anzeigen von Diagnoseprotokollen
> * Verwalten der Web-App im Azure-Portal

Sie können auch die [auf dem Azure-Portal basierende Version dieses Tutorials](/azure/developer/python/tutorial-python-postgresql-app-portal) verwenden.


## <a name="1-set-up-your-initial-environment"></a>1. Einrichten der anfänglichen Umgebung

1. Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Installieren Sie <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 oder höher</a>.
1. Installieren Sie die <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.18.0 oder höher, mit der Sie Befehle in einer beliebigen Shell ausführen, um Azure-Ressourcen bereitzustellen und zu konfigurieren.

Öffnen Sie ein Terminalfenster, und überprüfen Sie, ob mindestens die Python-Version 3.6 installiert ist:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Vergewissern Sie sich, dass Sie mindestens Version 2.18.0 der Azure CLI verwenden:

```azurecli
az --version
```

Wenn Sie ein Upgrade durchführen müssen, versuchen Sie es mit dem Befehl `az upgrade` (erfordert mindestens Version 2.11), oder lesen Sie die Informationen unter <a href="/cli/azure/install-azure-cli" target="_blank">Installieren der Azure CLI</a>.

Melden Sie sich anschließend über die CLI bei Azure an:

```azurecli
az login
```

Dieser Befehl öffnet einen Browser zum Erfassen Ihrer Anmeldeinformationen. Wenn der Befehl abgeschlossen ist, wird eine JSON-Ausgabe mit Informationen zu Ihren Abonnements angezeigt.

Nachdem Sie sich angemeldet haben, können Sie Azure-Befehle mit der Azure CLI ausführen, um Ressourcen in Ihrem Abonnement zu verwenden.

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="2-clone-or-download-the-sample-app"></a>2. Klonen oder Herunterladen der Beispiel-App

# <a name="git-clone"></a>[Git-Klon](#tab/clone)

Klonen Sie das Beispielrepository:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Navigieren Sie anschließend in diesen Ordner:

```terminal
cd djangoapp
```

# <a name="download"></a>[Download](#tab/download)

Besuchen Sie [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp), wählen Sie **Code** aus, und wählen Sie anschließend die Option **Download ZIP** (ZIP herunterladen) aus. 

Entpacken Sie die ZIP-Datei in einem Ordner namens *djangoapp*. 

Öffnen Sie dann ein Terminalfenster im Ordner *djangoapp*.

---

Das Beispiel „djangoapp“ enthält die datengesteuerte Django-Umfrage-App, die Sie erhalten, indem Sie die in der Django-Dokumentation unter [Schreiben Ihrer ersten Django-App](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) beschriebenen Schritte ausführen. Der Einfachheit halber wird Ihnen hier die fertige App zur Verfügung gestellt.

Das Beispiel wird außerdem für die Ausführung in einer Produktionsumgebung wie App Service angepasst:

- Die Produktionseinstellungen befinden sich in der Datei *azuresite/production.py*. Die Entwicklungseinstellungen befinden sich in *azuresite/settings.py*.
- Von der App werden Produktionseinstellungen verwendet, wenn die Umgebungsvariable `WEBSITE_HOSTNAME` festgelegt ist. Von Azure App Service wird diese Variable automatisch auf die URL der Web-App festgelegt, z. B. `msdocs-django.azurewebsites.net`.

Die Produktionseinstellungen gelten nicht speziell für App Service, sondern ermöglichen die Konfiguration von Django für die Ausführung in einer beliebigen Produktionsumgebung. Weitere Informationen finden Sie in der [Bereitstellungsprüfliste für Django](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/). Ausführliche Informationen zu einigen Änderungen finden Sie unter [Produktionseinstellungen für Django in Azure](configure-language-python.md#production-settings-for-django-apps).

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="3-create-postgres-database-in-azure"></a>3. Erstellen einer Postgres-Datenbankinstanz in Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Installieren Sie die Erweiterung `db-up` für die Azure CLI:

```azurecli
az extension add --name db-up
```

Wird der Befehl `az` nicht erkannt, vergewissern Sie sich, dass die Azure CLI wie unter [Einrichten der anfänglichen Umgebung](#1-set-up-your-initial-environment) beschrieben installiert wurde.

Erstellen Sie anschließend mithilfe des Befehls [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) die Postgres-Datenbank in Azure:

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- **Ersetzen** Sie *\<postgres-server-name>* durch einen **innerhalb von Azure eindeutigen** Namen. (Der Serverendpunkt ist `https://<postgres-server-name>.postgres.database.azure.com`.) Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einem anderen eindeutigen Wert.
- Geben Sie für *\<admin-username>* und *\<admin-password>* Anmeldeinformationen ein, um einen Administratorbenutzer für diesen Postgres-Server zu erstellen. Der Administratorbenutzername darf nicht *azure_superuser*, *azure_pg_admin*, *admin*, *administrator*, *root*, *guest* oder *public* lauten. Er kann nicht mit *pg_* beginnen. Das Kennwort muss **8 bis 128 Zeichen** aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0 bis 9) und nicht alphanumerische Zeichen (z. B. !, #, %). Das Kennwort darf nicht den Benutzernamen enthalten.
- Verwenden Sie das Zeichen `$` nicht im Benutzernamen oder Kennwort. Später erstellen Sie Umgebungsvariablen mit diesen Werten. Dabei hat das Zeichen `$` innerhalb des Linux-Containers, der zum Ausführen von Python-Apps verwendet wird, eine besondere Bedeutung.
- Der hier verwendete [Tarif](../postgresql/concepts-pricing-tiers.md) „B_Gen5_1“ (Basic, 5. Generation, ein Kern) ist der günstigste. Lassen Sie bei Produktionsdatenbanken das Argument `--sku-name` weg, um stattdessen den Tarif „GP_Gen5_2“ (Universell, 5. Generation, zwei Kerne) zu verwenden.

Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

- Erstellen einer [Ressourcengruppe](../azure-resource-manager/management/overview.md#terminology) namens `DjangoPostgres-tutorial-rg` (sofern noch nicht vorhanden)
- Erstellen eines nach dem Argument `--server-name` benannten Postgres-Servers
- Erstellen eines Administratorkontos mithilfe der Argumente `--admin-user` und `--admin-password`. Sie können diese Argumente weglassen, damit der Befehl eindeutige Anmeldeinformationen für Sie generiert.
- Erstellen einer nach dem Argument `--database-name` benannten `pollsdb`-Datenbank
- Ermöglichen des Zugriffs über Ihre lokale IP-Adresse
- Ermöglichen des Zugriffs über Azure-Dienste
- Erstellen eines neuen Datenbankbenutzers mit Zugriff auf die Datenbank `pollsdb`

Die Schritte können zwar auch separat mit anderen Befehlen vom Typ `az postgres` und `psql` ausgeführt werden, mit `az postgres up` werden jedoch alle Schritte gemeinsam ausgeführt.

Nach Abschluss der Befehlsausführung wird ein JSON-Objekt mit verschiedenen Verbindungszeichenfolgen für die Datenbank sowie mit der Server-URL, einem generierten Benutzernamen (beispielsweise „joyfulKoala@msdocs-djangodb-12345“) und einem GUID-Kennwort zurückgegeben. **Kopieren Sie den Benutzernamen und das Kennwort zur späteren Verwendung in diesem Tutorial in eine temporäre Textdatei.**

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`: Kann auf eine beliebige [Azure-Region](https://azure.microsoft.com/global-infrastructure/regions/) festgelegt werden. Sie können die Regionen, die für Ihr Abonnement verfügbar sind, mit dem [`az account list-locations`](/cli/azure/account#az-account-list-locations)-Befehl abrufen. Legen Sie bei Produktions-Apps für Ihre Datenbank und Ihre App den gleichen Standort fest.

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. Bereitstellen des Codes in Azure App Service

In diesem Abschnitt erstellen Sie einen App-Host in der App Service-App, verbinden die App mit der Postgres-Datenbank und stellen anschließend Ihren Code auf dem Host bereit.

### <a name="41-create-the-app-service-app"></a>4.1 Erstellen der App Service-App

Vergewissern Sie sich im Terminal, dass Sie sich im Repositoryordner *djangoapp* befinden, der den App-Code enthält.

Erstellen Sie mithilfe des Befehls [`az webapp up`](/cli/azure/webapp#az-webapp-up) eine App Service-App (den Hostprozess):

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Verwenden Sie für das Argument `--location` den gleichen Standort wie für die Datenbank im vorherigen Abschnitt.
- **Ersetzen** Sie *\<app-name>* durch einen innerhalb von Azure eindeutigen Namen. (Der Serverendpunkt ist `https://<app-name>.azurewebsites.net`.) Zulässige Zeichen für *\<app-name>* sind `A`-`Z`, `0`-`9` und `-`. Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.

Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Erstellen einer [Ressourcengruppe](../azure-resource-manager/management/overview.md#terminology) (sofern noch nicht vorhanden). Verwenden Sie in diesem Befehl die gleiche Ressourcengruppe, in der Sie zuvor schon die Datenbank erstellt haben.
- Erstellen des [App Service-Plans](overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* im Basic-Tarif (B1), sofern er nicht vorhanden ist. `--plan` und `--sku` sind optional.
- Erstellen der App Service-App (sofern noch nicht vorhanden)
- Aktivieren der Standardprotokollierung für die App (sofern noch nicht aktiviert)
- Hochladen des Repositorys per ZIP-Bereitstellung mit aktivierter Buildautomatisierung
- Zwischenspeichern allgemeiner Parameter wie dem Namen der Ressourcengruppe und dem App Service-Plan in der Datei *.azure/config*. Daher müssen bei späteren Befehlen nicht noch mal die gleichen Parameter angegeben werden. Wenn Sie die App also beispielsweise erneut bereitstellen möchten, nachdem Sie Änderungen vorgenommen haben, können Sie `az webapp up` einfach ohne Parameter erneut ausführen. Bei Befehlen von CLI-Erweiterungen (beispielsweise `az postgres up`) wird der Cache dagegen aktuell nicht genutzt. Daher müssen hier die Ressourcengruppe und der Standort mit der anfänglichen Verwendung von `az webapp up` angegeben werden.

Nach erfolgreicher Bereitstellung wird eine JSON-Ausgabe wie im folgenden Beispiel generiert:

![Beispielausgabe des Befehls „az webapp up“](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4.2 Konfigurieren der Umgebungsvariablen für die Datenbankverbindung

Nachdem der Code nun in App Service bereitgestellt ist, muss als Nächstes eine Verbindung zwischen der App und der Postgres-Datenbank in Azure hergestellt werden.

Vom App-Code werden Datenbankinformationen in vier Umgebungsvariablen namens `DBHOST`, `DBNAME`, `DBUSER` und `DBPASS` erwartet.

Mithilfe des folgenden Befehls [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) werden App-Einstellungen erstellt, um Umgebungsvariablen in App Service festzulegen:

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Ersetzen Sie *\<postgres-server-name>* durch den Namen, den Sie zuvor mit dem Befehl `az postgres up` verwendet haben. Der Code in *azuresite/production.py* hängt automatisch `.postgres.database.azure.com` an, um die vollständige Postgres-Server-URL zu erstellen.
- Ersetzen Sie *\<username>* und *\<password>* durch die Administratoranmeldeinformationen, die Sie mit dem Befehl `az postgres up` weiter oben verwendet haben, oder durch die Anmeldeinformationen, die von `az postgres up` für Sie generiert wurden. Der Code in *azuresite/production.py* erstellt automatisch den vollständigen Postgres-Benutzernamen aus `DBUSER` und `DBHOST`. Lassen Sie daher den Teil `@server` weg. (Wie bereits erwähnt, sollten Sie das Zeichen `$` in keinem der Werte verwenden, da es eine besondere Bedeutung für Linux-Umgebungsvariablen hat.)
- Die Namen für Ressourcengruppe und App werden aus den zwischengespeicherten Werten in der Datei *.azure/config* abgerufen.

In Ihrem Python-Code wird auf diese Einstellungen in Form von Umgebungsvariablen mit Anweisungen wie `os.environ.get('DBHOST')`zugegriffen. Weitere Informationen finden Sie unter [Zugreifen auf Umgebungsvariablen](configure-language-python.md#access-environment-variables).

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).

### <a name="43-run-django-database-migrations"></a>4.3 Ausführen einer Django-Datenbankmigration

Durch Django-Datenbankmigrationen wird sichergestellt, dass das Schema in der PostgreSQL-Datenbank in Azure mit den Angaben im Code übereinstimmt.

1. Öffnen Sie eine SSH-Sitzung **im Browser**, indem Sie zu folgender URL navigieren und sich mit den Anmeldeinformationen Ihres Azure-Kontos anmelden (nicht mit den Anmeldeinformationen des Datenbankservers).

    ```
    https://<app-name>.scm.azurewebsites.net/webssh/host
    ```

    Ersetzen Sie `<app-name>` durch den Namen, den Sie zuvor in dem Befehl `az webapp up` verwendet haben.

    Sie können alternativ mit dem Befehl [`az webapp ssh`](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az_webapp_ssh) eine Verbindung mit einer SSH-Sitzung herstellen. Unter Windows ist für diesen Befehl die Azure CLI 2.18.0 oder höher erforderlich.

    Können Sie keine Verbindung mit der SSH-Sitzung herstellen, konnte die App selbst nicht gestartet werden. Ausführliche Informationen finden Sie in den [Diagnoseprotokollen](#6-stream-diagnostic-logs). Wenn Sie beispielsweise nicht die erforderlichen App-Einstellungen im vorherigen Abschnitt erstellt haben, ist in den Protokollen `KeyError: 'DBNAME'`angegeben.

1. Führen Sie in der SSH-Sitzung die folgenden Befehle aus. (Befehle können mithilfe von **STRG**+**UMSCHALT**+**V** eingefügt werden.)

    ```bash
    # Change to the app folder
    cd $APP_PATH
    
    # Activate the venv
    source /antenv/bin/activate

    # Install requirements
    pip install -r requirements.txt

    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    Sollten Fehler auftreten, die mit der Herstellung der Datenbankverbindung zusammenhängen, überprüfen Sie die Werte der im vorherigen Abschnitt erstellten Anwendungseinstellungen.

1. Durch den Befehl `createsuperuser` werden Sie zur Eingabe von Superuser-Anmeldeinformationen aufgefordert. Verwenden Sie für dieses Tutorial den Standardbenutzernamen `root`, drücken Sie für die E-Mail-Adresse die **EINGABETASTE**, um sie leer zu lassen, und geben Sie `Pollsdb1` als Kennwort ein.

1. Wenn eine Fehlermeldung angezeigt wird, dass die Datenbank gesperrt ist, stellen Sie sicher, dass Sie den Befehl `az webapp settings` im vorherigen Abschnitt ausgeführt haben. Ohne diese Einstellungen kann der Migrationsbefehl (migrate) nicht mit der Datenbank kommunizieren, was zu einem Fehler führt.

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4.4 Erstellen einer Frage für die Umfrage in der App

1. Öffnen Sie in einen Browser die URL `http://<app-name>.azurewebsites.net`. In der App sollten „Polls app“ (Umfrage-App) und „No polls are available“ (Keine Umfragen verfügbar) angezeigt werden, da die Datenbank noch keine spezifischen Umfragen enthält.

    Wird „Anwendungsfehler“ angezeigt, haben Sie wahrscheinlich entweder die erforderlichen Einstellungen im vorherigen Schritt ([Konfigurieren der Umgebungsvariablen für die Datenbankverbindung](#42-configure-environment-variables-to-connect-the-database)) nicht erstellt, oder diese Werte enthalten Fehler. Führen Sie den Befehl `az webapp config appsettings list` aus, um die Einstellungen zu überprüfen. Sie können auch die [Diagnoseprotokolle überprüfen](#6-stream-diagnostic-logs), um bestimmte Fehler beim App-Start anzuzeigen. Wenn Sie beispielsweise die Einstellungen nicht erstellt haben, wird in den Protokollen der Fehler `KeyError: 'DBNAME'` angezeigt.

    Nachdem Sie die Einstellungen zum Beheben von Fehlern aktualisiert haben, räumen Sie eine Minute für den Neustart der App ein, und aktualisieren Sie dann den Browser.

1. Navigieren Sie zu `http://<app-name>.azurewebsites.net/admin`. Melden Sie sich unter Verwendung der Superuser-Anmeldeinformationen für Django aus dem vorherigen Abschnitt (`root` und `Pollsdb1`) an. Wählen Sie unter **Polls** (Umfragen) neben **Questions** (Fragen) die Option **Add** (Hinzufügen) aus, und erstellen Sie eine Frage für eine Umfrage mit mehreren Auswahlmöglichkeiten.

1. Navigieren Sie wieder zu `http://<app-name>.azurewebsites.net`, und vergewissern Sie sich, dass die Fragen nun angezeigt werden. Beantworten Sie Fragen nach Belieben, um Daten in der Datenbank zu generieren.

**Glückwunsch!** Sie führen eine Python-Django-Web-App in Azure App Service für Linux mit einer aktiven Postgres-Datenbank aus.

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> Zur Erkennung von Django-Projekten wird von App Service jedes Unterverzeichnis nach einer Datei namens *wsgi.py* durchsucht. Diese wird standardmäßig durch `manage.py startproject` erstellt. Wird die Datei von App Service gefunden, wird die Django-Web-App geladen. Weitere Informationen finden Sie unter [Konfigurieren einer Linux-Python-App für Azure App Service](configure-language-python.md).

## <a name="5-make-code-changes-and-redeploy"></a>5. Vornehmen von Codeänderungen und erneutes Bereitstellen

In diesem Abschnitt werden lokale Änderungen an der App vorgenommen, und der Code wird erneut in App Service bereitgestellt. Dabei wird eine virtuelle Python-Umgebung zur Unterstützung laufender Arbeiten eingerichtet.

### <a name="51-run-the-app-locally"></a>5.1 Lokales Ausführen der App

Führen Sie in einem Terminalfenster die folgenden Befehle aus. Folgen Sie beim Erstellen des Superusers den Anweisungen:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[Befehlszeile](#tab/cmd)

```cmd
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install dependencies
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Nachdem die Web-App vollständig geladen wurde, wird vom Django-Entwicklungsserver die lokale App-URL in der Meldung „Starting development server at http://127.0.0.1:8000/. Quit the server with CTRL-BREAK“ (Entwicklungsserver wird unter „http://127.0.0.1:8000/“ gestartet. Drücken Sie zum Beenden des Servers STRG+UNTBR.) bereitgestellt.

![Beispielausgabe des Django-Entwicklungsservers](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Testen Sie die App lokal:

1. Navigieren Sie in einem Browser zu `http://localhost:8000`, woraufhin die Meldung „No polls are available“ (Keine Abstimmungen verfügbar) angezeigt werden sollte. 

1. Wechseln Sie zu `http:///localhost:8000/admin`, und melden Sie sich mit dem Administratorbenutzer an, den Sie zuvor erstellt haben. Wählen Sie erneut unter **Polls** (Umfragen) neben **Questions** (Fragen) die Option **Add** (Hinzufügen) aus, und erstellen Sie eine Frage für eine Umfrage mit mehreren Auswahlmöglichkeiten. 

1. Navigieren Sie erneut zu *http:\//localhost:8000*, und beantworten Sie die Frage, um die App zu testen. 

1. Drücken Sie **STRG**+**C**, um den Django-Server zu beenden.

Bei lokaler Ausführung wird von der App eine lokale SQLite3-Datenbank verwendet, und Ihre Produktionsdatenbank wird nicht beeinträchtigt. Auf Wunsch kann auch eine lokale PostgreSQL-Datenbank erstellt werden, um die Produktionsumgebung besser zu simulieren.

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="52-update-the-app"></a>5.2 Aktualisieren der App

Suchen Sie in `polls/models.py` nach der Zeile, die mit `choice_text` beginnt, und ändern Sie den Parameter `max_length` in „100“:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Da Sie das Datenmodell geändert haben, müssen Sie eine neue Django-Migration erstellen und die Datenbank migrieren:

```
python manage.py makemigrations
python manage.py migrate
```

Führen Sie den Entwicklungsserver erneut mit `python manage.py runserver` aus, und testen Sie die App unter *http:\//localhost:8000/admin*:

Beenden Sie den Django-Webserver erneut durch Drücken von **STRG**+**C**.

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).

### <a name="53-redeploy-the-code-to-azure"></a>5.3 Erneutes Bereitstellen des Codes in Azure

Führen Sie am Repositorystamm den folgenden Befehl aus:

```azurecli
az webapp up
```

Bei diesem Befehl werden die in der Datei *.azure/config* zwischengespeicherten Parameter verwendet. Da von App Service erkannt wird, dass die App bereits vorhanden ist, wird lediglich der Code erneut bereitgestellt.

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).

### <a name="54-rerun-migrations-in-azure"></a>5.4 Erneutes Ausführen von Migrationen in Azure

Da Sie Änderungen am Datenmodell vorgenommen haben, müssen Sie die Datenbankmigrationen in App Service erneut ausführen.

Öffnen Sie erneut eine SSH-Sitzung im Browser, indem Sie zu `https://<app-name>.scm.azurewebsites.net/webssh/host` navigieren. Führen Sie anschließend die folgenden Befehle aus:

```
cd $APP_PATH
source /antenv/bin/activate
pip install -r requirements.txt
python manage.py migrate
```

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).

### <a name="55-review-app-in-production"></a>5.5 Überprüfen der App in der Produktion

Navigieren Sie zu `http://<app-name>.azurewebsites.net`, und testen Sie die App erneut in der Produktion. (Da Sie lediglich die Länge eines Datenbankfelds geändert haben, macht sich die Änderung nur bemerkbar, wenn Sie versuchen, bei der Frageerstellung eine längere Antwort einzugeben.)

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).

## <a name="6-stream-diagnostic-logs"></a>6. Streamen von Diagnoseprotokollen

Auf die generierten Konsolenprotokolle kann innerhalb des Containers zugegriffen werden, in dem die App in Azure gehostet wird.

Führen Sie den folgenden Azure CLI-Befehl aus, um den Protokolldatenstrom anzuzeigen. Bei diesem Befehl werden zwischengespeicherte Parameter aus der Datei *.azure/config* verwendet.

```azurecli
az webapp log tail
```

Falls Sie nicht sofort Konsolenprotokolle sehen, können Sie es nach 30 Sekunden noch einmal versuchen.

Zum Beenden des Protokollstreamings können Sie jederzeit **STRG**+**C** drücken.

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> Sie können die Protokolldateien auch im Browser unter `https://<app-name>.scm.azurewebsites.net/api/logs/docker` untersuchen.
>
> `az webapp up` aktiviert die Standardprotokollierung für Sie. Aus Leistungsgründen wird diese Protokollierung nach einiger Zeit deaktiviert, aber jedes Mal wieder eingeschaltet, wenn Sie `az webapp up` erneut ausführen. Führen Sie zur manuellen Aktivierung folgenden Befehl aus:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="7-manage-your-app-in-the-azure-portal"></a>7. Überwachen Ihrer App im Azure-Portal

Suchen Sie im [Azure-Portal](https://portal.azure.com) nach dem App-Namen, und wählen Sie die App in den Ergebnissen aus.

![Navigieren zur Python-Django-App im Azure-Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Standardmäßig wird im Portal die Seite **Übersicht** mit einer allgemeinen Leistungsansicht für Ihre App angezeigt. Hier können Sie auch einfache Verwaltungsaufgaben durchführen, z. B. Durchsuchen, Beenden, Neustarten und Löschen. Die Registerkarten links auf der Seite zeigen die verschiedenen Konfigurationsseiten, die Sie öffnen können.

![Verwalten der Python-Django-App auf der Seite „Übersicht“ im Azure-Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).

## <a name="8-clean-up-resources"></a>8. Bereinigen von Ressourcen

Wenn Sie die App behalten oder mit den zusätzlichen Tutorials fortfahren möchten, gehen Sie direkt zu [Nächste Schritte](#next-steps). Andernfalls können Sie die für dieses Tutorial erstellte Ressourcengruppe löschen, um laufende Gebühren zu vermeiden:

```azurecli
az group delete --no-wait
```

Bei diesem Befehl wird der zwischengespeicherte Ressourcengruppenname aus der Datei *.azure/config* verwendet. Wenn Sie die Ressourcengruppe löschen, wird auch die Zuordnung aller darin enthaltenen Ressourcen aufgehoben, und die Ressourcen werden gelöscht.

Das Löschen aller Ressourcen kann einige Zeit dauern. Das Argument `--no-wait` ermöglicht, dass der Befehl sofort zurückgegeben wird.

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Ihrer App einen benutzerdefinierten DNS-Namen zuordnen:

> [!div class="nextstepaction"]
> [Tutorial: Zuordnen eines benutzerdefinierten DNS-Namens zu Ihrer App](app-service-web-tutorial-custom-domain.md)

Erfahren Sie, wie eine Python-App von App Service ausgeführt wird:

> [!div class="nextstepaction"]
> [Konfigurieren einer Python-App](configure-language-python.md)
