# Airflow-Openshift
This project provides a basic example which can be used to get [Apache Airflow](https://airflow.apache.org/) running in OpenShift. The provided template will set up an Airflow Scheduler and an Airflow Webserver in an OpenShift project by default running with the `SequentialExecutor`. Further work along the lines of setting up [Celery](http://www.celeryproject.org/) or [RabbitMQ](https://www.rabbitmq.com/) is not included. The setup draws from work done in the [puckel/docker-airflow](https://github.com/puckel/docker-airflow) and [mumoshu/kube-airflow](https://github.com/mumoshu/kube-airflow) projects.

## Instructions 
  1. Create an OpenShift project (`airflow` is the expected default).
  ``` 
    oc new-project airflow 
  ```
  2. Change to dir openshift-applier
  3. One time only Run
  ``` 
    ansible-galaxy install -r requirements.yml --roles-path=roles 
  ```
  4. Run 
  ``` 
    ansible-playbook -i inventory/ apply.yml 
  ```

This will deploy an Airflow Webserver with 1 pod and an Airflow Scheduler with 0 pods. This allows for the Webserver to start up and initialise the database before the Scheduler connects to it. Once the Webserver is up and has initialised the database, you can copy DAG's into the `${AIRFLOW_HOME/dags}` directory in the shared PVC mounted in the Scheduler and Webserver deployments (a test dag can be found under `/dags` in this repo). Scale the Scheduler to 1 pod and the system is ready to process DAG runs. You can access the Webserver dashboard through the route created to in your OpenShift project.
