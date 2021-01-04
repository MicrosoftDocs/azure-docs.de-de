---
title: 'Tutorial: Bereitstellen einer Django-App mit App Service und Azure Database for PostgreSQL Flexible Server (Vorschau) im virtuellen Netzwerk'
description: Bereitstellen einer Django-App mit App Service und Azure Database for PostgreSQL Flexible Server (Vorschau) im virtuellen Netzwerk
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bcc9ca0175e0e03c62c2ce2b91d8ec337756a3cc
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490100"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Tutorial: Bereitstellen einer Django-App mit App Service und Azure Database for PostgreSQL Flexible Server (Vorschau)

> [!IMPORTANT]
> Azure Database for PostgreSQL Flexible Server befindet sich in der Vorschau.

In diesem Tutorial erfahren Sie, wie Sie eine Django-Anwendung in Azure mit App Services und Azure Database for PostgreSQL Flexible Server in einem virtuellen Netzwerk bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Für den Artikel müssen Sie mindestens Version 2.0 der Azure-Befehlszeilenschnittstelle lokal ausführen. Führen Sie den Befehl `az --version` aus, um die installierte Version anzuzeigen. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Sie müssen sich mithilfe des Befehls [az login](/cli/azure/authenticate-azure-cli) bei Ihrem Konto anmelden. Beachten Sie die Eigenschaft **id** aus der Befehlsausgabe für den entsprechenden Abonnementnamen.

```azurecli
az login
```

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account) die Abonnement-ID unter Ihrem Konto aus. Ersetzen Sie den Platzhalter für die Abonnement-ID durch die **subscription ID**-Eigenschaft der Ausgabe von **az login** für Ihr Abonnement.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>Klonen oder Herunterladen der Beispiel-App

# <a name="git-clone"></a>[Git-Klon](#tab/clone)

Klonen Sie das Beispielrepository:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Wechseln Sie dann zu diesem Ordner:

```terminal
cd djangoapp
```

# <a name="download"></a>[Download](#tab/download)

Besuchen Sie [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp), wählen Sie **Code** aus, und wählen Sie anschließend die Option **Download ZIP** (ZIP herunterladen) aus.

Entpacken Sie die ZIP-Datei in einem Ordner namens *djangoapp*.

Öffnen Sie dann ein Terminalfenster im Ordner *djangoapp*.

---

Das Beispiel „djangoapp“ enthält die datengesteuerte Django-Umfrage-App, die Sie erhalten, indem Sie die in der Django-Dokumentation unter [Schreiben Ihrer ersten Django-App](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) beschriebenen Schritte ausführen. Der Einfachheit halber wird Ihnen hier die fertige App zur Verfügung gestellt.

Das Beispiel wird außerdem für die Ausführung in einer Produktionsumgebung wie App Service angepasst:

- Die Produktionseinstellungen befinden sich in der Datei *azuresite/production.py*. Die Entwicklungsdetails befinden sich in *azuresite/settings.py*.
- Von der App werden Produktionseinstellungen verwendet, wenn die Umgebungsvariable `DJANGO_ENV` auf „production“ festgelegt ist. Diese Umgebungsvariable wird später in diesem Tutorial zusammen mit anderen Umgebungsvariablen für die PostgreSQL-Datenbankkonfiguration erstellt.

Diese Änderungen gelten nicht speziell für App Service, sondern ermöglichen die Ausführung von Django in einer beliebigen Produktionsumgebung. Weitere Informationen finden Sie in der [Bereitstellungsprüfliste für Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Erstellen einer PostgreSQL Flexible Server-Instanz in einem virtuellen Netzwerk

Erstellen Sie einen privaten flexiblen Server und eine Datenbank innerhalb eines virtuellen Netzwerks (VNET) mit dem folgenden Befehl:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

- Erstellen Sie die Ressourcengruppe, wenn sie noch nicht vorhanden ist.
- Generiert einen Servernamen, wenn er nicht angegeben ist.
- Erstellen Sie ein neues virtuelles Netzwerk für Ihre neue PostgreSQL-Serverinstanz. **Notieren Sie sich den Namen des virtuellen Netzwerks und des Subnetzes**, die für Ihre Serverinstanz erstellt wurden, da Sie die Web-App dem gleichen virtuellen Netzwerk hinzufügen müssen.
- Erstellt einen Administratorbenutzernamen und ein Kennwort für Ihren Server, sofern nicht angegeben. **Notieren Sie sich den Benutzernamen und das Kennwort** zur Verwendung in den nächsten Schritten.
- Erstellen Sie eine Datenbank ```postgres```, die für die Entwicklung verwendet werden kann. Sie können [**psql** ausführen, um eine Verbindung mit der Datenbank herzustellen](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql), um eine andere Datenbank zu erstellen.

> [!NOTE]
> Notieren Sie sich das Kennwort, das für Sie generiert wird, wenn es nicht angegeben wird. Wenn Sie das Kennwort vergessen haben, müssen Sie das Kennwort mit dem ``` az postgres flexible-server update```-Befehl zurücksetzen.


## <a name="deploy-the-code-to-azure-app-service"></a>Bereitstellen des Codes in Azure App Service

In diesem Abschnitt erstellen Sie einen App-Host in der App Service-App, verbinden die App mit der Postgres-Datenbank und stellen anschließend Ihren Code auf dem Host bereit.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>Erstellen der App Service-Web-App in einem virtuellen Netzwerk

Vergewissern Sie sich im Terminal, dass Sie sich am Repositorystamm (`djangoapp`) mit dem App-Code befinden.

Erstellen Sie mithilfe des Befehls [`az webapp up`](/cli/azure/webapp#az-webapp-up) eine App Service-App (den Hostprozess):

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- Verwenden Sie für das Argument `--location` den gleichen Standort wie für die Datenbank im vorherigen Abschnitt.
- Ersetzen Sie *\<app-name>* durch einen innerhalb von Azure eindeutigen Namen. (Der Serverendpunkt ist `https://\<app-name>.azurewebsites.net`.) Zulässige Zeichen für *\<app-name>* sind `A`-`Z`, `0`-`9` und `-`. Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.
- Erstellen des [App Service-Plans](../../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* im Basic-Tarif (B1), sofern er nicht vorhanden ist. `--plan` und `--sku` sind optional.
- Erstellen der App Service-App (sofern noch nicht vorhanden)
- Aktivieren der Standardprotokollierung für die App (sofern noch nicht aktiviert)
- Hochladen des Repositorys per ZIP-Bereitstellung mit aktivierter Buildautomatisierung
- Mit dem Befehl **az webapp vnet-integration** wird die Web-App im gleichen virtuellen Netzwerk wie der Postgres-Server hinzugefügt.
- Vom App-Code werden Datenbankinformationen in einer Reihe von Umgebungsvariablen erwartet. Mithilfe des Befehls [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) werden App-Einstellungen erstellt, um Umgebungsvariablen in App Service festzulegen.

> [!TIP]
> Von vielen Azure CLI-Befehlen werden allgemeine Parameter wie etwa der Name der Ressourcengruppe und der App Service-Plan in der Datei *.azure/config* zwischengespeichert. Daher müssen bei späteren Befehlen nicht noch mal die gleichen Parameter angegeben werden. Wenn Sie die App also beispielsweise erneut bereitstellen möchten, nachdem Sie Änderungen vorgenommen haben, können Sie `az webapp up` einfach ohne Parameter erneut ausführen.

### <a name="run-django-database-migrations"></a>Ausführen von Django-Datenbankmigrationen

Durch Django-Datenbankmigrationen wird sichergestellt, dass das Schema in der PostgreSQL-Datenbank in Azure mit den Angaben im Code übereinstimmt.

1. Öffnen Sie eine SSH-Sitzung im Browser, indem Sie zu *https://\<app-name>.scm.azurewebsites.net/webssh/host* navigieren, und melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos an (nicht mit den Anmeldeinformationen des Datenbankservers).

1. Führen Sie in der SSH-Sitzung die folgenden Befehle aus. (Befehle können mithilfe von **STRG**+**UMSCHALT**+**V** eingefügt werden.)

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. Durch den Befehl `createsuperuser` werden Sie zur Eingabe von Superuser-Anmeldeinformationen aufgefordert. Verwenden Sie für dieses Tutorial den Standardbenutzernamen `root`, drücken Sie für die E-Mail-Adresse die **EINGABETASTE**, um sie leer zu lassen, und geben Sie `postgres1` als Kennwort ein.

### <a name="create-a-poll-question-in-the-app"></a>Erstellen einer Frage für die Umfrage in der App

1. Öffnen Sie in einem Browser die URL *http:\//\<app-name>.azurewebsites.net*. In der App sollte „No polls are available“ (Keine Umfragen verfügbar) angezeigt werden, da die Datenbank noch keine spezifischen Umfragen enthält.

1. Navigieren Sie zu *http:\//\<app-name>.azurewebsites.net/admin*. Melden Sie sich unter Verwendung der Superuser-Anmeldeinformationen aus dem vorherigen Abschnitt an (`root` und `postgres1`). Wählen Sie unter **Polls** (Umfragen) neben **Questions** (Fragen) die Option **Add** (Hinzufügen) aus, und erstellen Sie eine Frage für eine Umfrage mit mehreren Auswahlmöglichkeiten.

1. Navigieren Sie wieder zu *http:\//\<app-name>.azurewebsites.net/* , und vergewissern Sie sich, dass die Fragen nun angezeigt werden. Beantworten Sie Fragen nach Belieben, um Daten in der Datenbank zu generieren.

**Glückwunsch!** Sie führen eine Python-Django-Web-App in Azure App Service für Linux mit einer aktiven Postgres-Datenbank aus.

> [!NOTE]
> Zur Erkennung von Django-Projekten wird von App Service jedes Unterverzeichnis nach einer Datei namens *wsgi.py* durchsucht. Diese wird standardmäßig durch `manage.py startproject` erstellt. Wird die Datei von App Service gefunden, wird die Django-Web-App geladen. Weitere Informationen finden Sie unter [Konfigurieren einer Linux-Python-App für Azure App Service](../../app-service/configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Vornehmen von Codeänderungen und erneutes Bereitstellen

In diesem Abschnitt werden lokale Änderungen an der App vorgenommen, und der Code wird erneut in App Service bereitgestellt. Dabei wird eine virtuelle Python-Umgebung zur Unterstützung laufender Arbeiten eingerichtet.

### <a name="run-the-app-locally"></a>Lokales Ausführen der App

Führen Sie in einem Terminalfenster die folgenden Befehle aus. Folgen Sie beim Erstellen des Superusers den Anweisungen:

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```
Nachdem die Web-App vollständig geladen wurde, wird vom Django-Entwicklungsserver die lokale App-URL in der Meldung „Starting development server at http://127.0.0.1:8000/. Quit the server with CTRL-BREAK“ (Entwicklungsserver wird unter „http://127.0.0.1:8000/“ gestartet. Drücken Sie zum Beenden des Servers STRG+UNTBR.) bereitgestellt.

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Beispielausgabe des Django-Entwicklungsservers":::

Testen Sie die App lokal:

1. Navigieren Sie in einem Browser zu *http:\//localhost:8000*. Daraufhin sollte die Meldung „No polls are available“ (Keine Umfragen verfügbar) angezeigt werden.

1. Navigieren Sie zu *http:\//localhost:8000/admin*, und melden Sie sich unter Verwendung des zuvor erstellten Administratorbenutzers an. Wählen Sie erneut unter **Polls** (Umfragen) neben **Questions** (Fragen) die Option **Add** (Hinzufügen) aus, und erstellen Sie eine Frage für eine Umfrage mit mehreren Auswahlmöglichkeiten.

1. Navigieren Sie erneut zu *http:\//localhost:8000*, und beantworten Sie die Frage, um die App zu testen.

1. Drücken Sie **STRG**+**C**, um den Django-Server zu beenden.

Bei lokaler Ausführung wird von der App eine lokale SQLite3-Datenbank verwendet, und Ihre Produktionsdatenbank wird nicht beeinträchtigt. Auf Wunsch kann auch eine lokale PostgreSQL-Datenbank erstellt werden, um die Produktionsumgebung besser zu simulieren.



### <a name="update-the-app"></a>Aktualisieren der App

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


### <a name="redeploy-the-code-to-azure"></a>Erneutes Bereitstellen des Codes in Azure

Führen Sie am Repositorystamm den folgenden Befehl aus:

```azurecli
az webapp up
```

Bei diesem Befehl werden die in der Datei *.azure/config* zwischengespeicherten Parameter verwendet. Da von App Service erkannt wird, dass die App bereits vorhanden ist, wird lediglich der Code erneut bereitgestellt.



### <a name="rerun-migrations-in-azure"></a>Erneutes Ausführen von Migrationen in Azure

Da Sie Änderungen am Datenmodell vorgenommen haben, müssen Sie die Datenbankmigrationen in App Service erneut ausführen.

Öffnen Sie erneut eine SSH-Sitzung im Browser, indem Sie zu *https://\<app-name>.scm.azurewebsites.net/webssh/host* navigieren. Führen Sie anschließend die folgenden Befehle aus:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Überprüfen der App in der Produktion

Navigieren Sie zu *http:\//\<app-name>.azurewebsites.net*, und testen Sie die App erneut in der Produktionsumgebung. (Da Sie lediglich die Länge eines Datenbankfelds geändert haben, macht sich die Änderung nur bemerkbar, wenn Sie versuchen, bei der Frageerstellung eine längere Antwort einzugeben.)

> [!TIP]
> Sie können mit [django-storages](https://django-storages.readthedocs.io/en/latest/backends/azure.html) statische und Medienobjekte in Azure Storage speichern. Sie können mit Azure CDN GZIP auf statische Dateien anwenden.


## <a name="manage-your-app-in-the-azure-portal"></a>Überwachen Ihrer App im Azure-Portal

Suchen Sie im [Azure-Portal](https://portal.azure.com) nach dem App-Namen, und wählen Sie die App in den Ergebnissen aus.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Navigieren zur Python-Django-App im Azure-Portal":::

Standardmäßig wird im Portal die Seite **Übersicht** mit einer allgemeinen Leistungsansicht für Ihre App angezeigt. Hier können Sie auch einfache Verwaltungsaufgaben durchführen, z. B. Durchsuchen, Beenden, Neustarten und Löschen. Die Registerkarten links auf der Seite zeigen die verschiedenen Konfigurationsseiten, die Sie öffnen können.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Verwalten der Python-Django-App auf der Seite „Übersicht“ im Azure-Portal":::


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die App behalten oder mit dem nächsten Tutorial fortfahren möchten, springen Sie direkt zu [Nächste Schritte](#next-steps). Andernfalls können Sie die für dieses Tutorial erstellte Ressourcengruppe löschen, um laufende Gebühren zu vermeiden:

```azurecli
az group delete -g myresourcegroup
```

Bei diesem Befehl wird der zwischengespeicherte Ressourcengruppenname aus der Datei *.azure/config* verwendet. Wenn Sie die Ressourcengruppe löschen, wird auch die Zuordnung aller darin enthaltenen Ressourcen aufgehoben, und die Ressourcen werden gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Ihrer App einen benutzerdefinierten DNS-Namen zuordnen:

> [!div class="nextstepaction"]
> [Tutorial: Zuordnen eines benutzerdefinierten DNS-Namens zu Ihrer App](../../app-service/app-service-web-tutorial-custom-domain.md)

Erfahren Sie, wie eine Python-App von App Service ausgeführt wird:

> [!div class="nextstepaction"]
> [Konfigurieren einer Python-App](../../app-service/configure-language-python.md)
