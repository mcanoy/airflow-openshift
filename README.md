# Airflow-Openshift
This project provides a basic example which can be used to get [Apache Airflow](https://airflow.apache.org/) running in OpenShift. The provided template will set up an Airflow Scheduler and an Airflow Webserver in an OpenShift project by default running with the `SequentialExecutor`. Further work along the lines of setting up [Celery](http://www.celeryproject.org/) or [RabbitMQ](https://www.rabbitmq.com/) is not included. The setup draws from work done in the [puckel/docker-airflow](https://github.com/puckel/docker-airflow) and [mumoshu/kube-airflow](https://github.com/mumoshu/kube-airflow) projects.

## Instructions 

  1. Change to dir openshift-applier
  2. One time only Run
  ``` 
    ansible-galaxy install -r requirements.yml --roles-path=roles 
  ```
  4. Run 
  ``` 
    ansible-playbook -i inventory/ apply.yml 
  ```

  Note: If you want to just run the Airflow without setting up the project (quite likely) then run

  ```
    ansible-playbook -i inventory/ apply.yml --limit seed-hosts
  ```

This will deploy an Airflow Webserver with 1 pod and an Airflow Scheduler with 1 pods. Once the Webserver is up and has initialised the database, you can `oc rsync` DAG's into the `${AIRFLOW_HOME/dags}` directory in the shared PVC mounted in the Scheduler and Webserver deployments (a test dag can be found under `/dags` in this repo). Scale the Scheduler to 1 pod and the system is ready to process DAG runs. You can access the Webserver dashboard through the route created to in your OpenShift project.

### Node selector via namespace

If you manually creating your project, then after creating your project you can alter the namespace to ensure apps in that namespace only deploy to a specific node. First, ensure you have a node that can be specifically labelled. 

```
oc get nodes --show-labels
```

You could label a node like so:

```
oc label node app-node-0.s11.labs-core.osp.rht-labs.com region=airflow-region
```

Edit the namespace (project)

```
oc edit project airflow-project
```

Add in the appropriate selector

```
apiVersion: v1
kind: Namespace
metadata:
  annotations:
    openshift.io/node-selector: "type=user-node,region=airflow-region"
```

Your done. Deploy away. (Note: this could be done in other ways. ie - in the dc).
