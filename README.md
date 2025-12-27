Here is a README.md file formatted specifically for GitHub. It includes code blocks, badges, and clear headers to make the project easy to navigate and set up.Markdown# Apache Airflow in Docker (Python 3.12)

![Airflow Version](https://img.shields.io/badge/Airflow-Latest-blue)
![Python Version](https://img.shields.io/badge/Python-3.12-yellow)
![Docker](https://img.shields.io/badge/Container-Docker-informational)

A quick-start setup for running **Apache Airflow** using the official Docker images, customized to run on **Python 3.12** and accessible via **Port 80**.

**CAUTION:** This setup is intended for local development, learning, and exploration. It does **not** provide the security guarantees required for production environments. For production, please refer to the [Official Airflow Helm Chart](https://airflow.apache.org/docs/helm-chart/stable/index.html).

---

## Prerequisites

Before you begin, ensure you have the following installed:

* **Docker Desktop/Engine** (Allocated Memory: **>4GB** recommended)
* **Docker Compose** (v2.14.0 or newer)

---

## Installation & Setup

### 1. Create Project Directory
Start by creating a clean directory for your project:

```bash
mkdir airflow-docker
cd airflow-docker
2. Download Compose FileFetch the official docker-compose.yaml file:Bashcurl -LfO '[https://airflow.apache.org/docs/apache-airflow/3.1.5/docker-compose.yaml](https://airflow.apache.org/docs/apache-airflow/3.1.5/docker-compose.yaml)'
3. Configure docker-compose.yamlYou need to edit the downloaded file to change the Python version and the web server port.Open docker-compose.yaml and make the following changes:A. Update the Image Tag (Python 3.12)Locate the x-airflow-common section and update the image line:YAMLx-airflow-common:
  &airflow-common
  # Update to latest-python3.12
  image: ${AIRFLOW_IMAGE_NAME:-apache/airflow:latest-python3.12}
B. Update the Port Mapping (Port 80)Locate the airflow-apiserver service and update the ports section:YAML  airflow-apiserver:
    # ...
    ports:
      # Map Host Port 80 to Container Port 8080
      - "80:8080"
4. Initialize EnvironmentCreate the necessary directories for volumes and set the user permissions (Linux/Mac):Bashmkdir -p ./dags ./logs ./plugins ./config
echo -e "AIRFLOW_UID=$(id -u)" > .env
(Windows users can manually create an .env file containing AIRFLOW_UID=50000)5. Initialize the DatabaseRun the database migrations and create the default admin user:Bashdocker compose up airflow-init
Wait for the message: airflow-init-1 exited with code 06. Start AirflowStart all services in detached mode:Bashdocker compose up -d
Accessing the UIOnce the services are running, access the Airflow web interface at:URL: http://localhostUsername: airflowPassword: airflow🛠️ Common CommandsActionCommandCheck Container Statusdocker psStop & Remove Volumesdocker compose down --volumes --remove-orphansRun CLI Commanddocker compose run airflow-worker airflow infoView Logsdocker compose logs -f📂 Project StructureAfter initialization, your directory will look like this:Plaintextairflow-docker/
├── dags/                # Place your DAG files here
├── logs/                # Task logs
├── plugins/             # Custom plugins
├── config/              # Custom configurations
├── docker-compose.yaml  # Service definition
└── .env                 # Environment variables (UID)
TroubleshootingPermission Denied on airflow.cfg?If you are on Linux with SELinux enabled, append :z to the volume mounts in your docker-compose.yaml:YAMLvolumes:
  - ${AIRFLOW_PROJ_DIR:-.}/dags:/opt/airflow/dags:z
  - ${AIRFLOW_PROJ_DIR:-.}/logs:/opt/airflow/logs:z
  # ... etc# apache_airflow
