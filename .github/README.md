# Short README
(complete documentation [here](/README.md))

## Overview

Composer Local Development CLI tool streamlines Apache Airflow DAG development
for Cloud Composer 2 by running an Airflow environment locally. This local
Airflow environment uses an image of a specific Cloud Composer version.

## Prerequisites

In order to run the CLI tool, install the following prerequisites:

- Python 3.7-3.10 with `pip`
- [gcloud CLI](https://cloud.google.com/sdk/docs/install)
- Docker

Docker must be installed and running in the local system.

## Configure credentials

If not already done,
[get new user credentials to use for Application Default Credentials](https://cloud.google.com/sdk/gcloud/reference/auth/application-default/login):

```bash
gcloud auth application-default login
```

Login in `gcloud` using your Google account:

```bash
gcloud auth login
```

## Install from the source code

1. Clone this repository
2.  In the top-level directory of the cloned repository, run:

    ```bash
    pip install .
    ```

## Create a local Airflow environment from a Cloud Composer environment

Only the following information is taken from a Cloud Composer
environment:

- [Image version](https://cloud.google.com/composer/docs/concepts/versioning/composer-versions) (versions of Cloud Composer and
    Airflow used in your environment).
- List of [custom PyPI packages](https://cloud.google.com/composer/docs/composer-2/install-python-dependencies) installed in your
    environment.
- Commented list of names of [environment variables](https://cloud.google.com/composer/docs/composer-2/set-environment-variables) set in your environment.

    **Important:** Cloud Composer **does not copy the values** of
    environment variables. You can manually uncomment environment variables
    [in the configuration file](#configure-environment-variables) and set their
    values, as required.

Other information and configuration parameters from the environment, such as
DAG files, DAG run history, Airflow variables, and connections, are not copied
from your Composer environment.

To create a local Airflow environment from an existing
Cloud Composer environment:

```bash
composer-dev create LOCAL_ENVIRONMENT_NAME \
    --from-source-environment ENVIRONMENT_NAME \
    --location LOCATION \
    --project PROJECT_ID \
    --port WEB_SERVER_PORT \
    --dags-path LOCAL_DAGS_PATH
```

Example:

```bash
composer-dev create example-local-environment \
  --from-source-environment example-environment \
  --location us-central1 \
  --project example-project \
  --port 8081 \
  --dags-path example_directory/dags
```

## Start a local Airflow environment

To start a local Airflow environment, run:

```bash
composer-dev start LOCAL_ENVIRONMENT_NAME
```

## Stop or restart a local Airflow environments

When you restart a local Airflow environment, Composer Local Development CLI
tool restarts the Docker container where the environment runs. All Airflow
components are stopped and started again. As a result, all DAG runs that are
executed during a restart are marked as failed.

To restart or start a stopped local Airflow environment, run:

```bash
composer-dev restart LOCAL_ENVIRONMENT_NAME
```

To stop a local Airflow environment, run:

```bash
composer-dev stop LOCAL_ENVIRONMENT_NAME
```

**Note:** The `stop` command does not [delete the local Airflow environment](#delete-a-local-airflow-environment).

## Add and update DAGs

Dags are stored in the directory that you specified in the `--dags-path`
parameter when you created your local Airflow environment. By default, this
directory is `./composer/<local_environment_name>/dags`. You can get the
directory used by your environment with the
[`describe` command](#get-a-list-and-status-of-local-airflow-environments).

To add and update DAGs, change files in this directory. You do not need to
restart your local Airflow environment.

## View local Airflow environment logs

You can view recent logs from a Docker container that runs your local Airflow
environment. In this way, you can monitor container-related events and check
Airflow logs for errors such as dependency conflicts caused by PyPI packages
installation.

**Note:** Composer Local Development CLI tool does not write DAG run and task
logs to files. You can view these logs in the Airflow UI of your local Airflow
environment.

To view logs from a Docker container that runs your local Airflow environment,
run:

```bash
composer-dev logs LOCAL_ENVIRONMENT_NAME --max-lines 10
```

To follow the log stream, omit the `--max-lines` argument:

```bash
composer-dev logs LOCAL_ENVIRONMENT_NAME
```

### Configure environment variables

To configure environment variables, edit the `variables.env` file in the
environment directory:
`./composer/<local_environment_name>/variables.env`.

The `variables.env` file must contain key-value definitions, one line for each
environment variable. 

```bash
EXAMPLE_VARIABLE=True
ANOTHER_VARIABLE=test
AIRFLOW__WEBSERVER__DAG_DEFAULT_VIEW=graph
```

### Install or remove PyPI packages

To install or remove PyPI packages, modify the `requirements.txt` file in the
environment directory: `./composer/<local_environment_name>/requirements.txt`.


To apply the changes, restart your local Airflow environment.
