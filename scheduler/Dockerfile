FROM rhscl/python-36-rhel7

ARG AIRFLOW_VERSION=1.10.0

ENV AIRFLOW_HOME=/usr/local/airflow

ENV SLUGIFY_USES_TEXT_UNIDECODE=yes

ENV USER=airflow USER_UID=1001

RUN echo "Airflow version: ${AIRFLOW_VERSION}"

#RUN useradd -ms /bin/bash -d ${AIRFLOW_HOME} --uid ${USER_UID} airflow 

RUN pip install --no-cache-dir apache-airflow['crypto','kubernetes','postgres']

COPY ./scripts/entrypoint.sh /entrypoint.sh

#RUN chown -R airflow: ${AIRFLOW_HOME}

USER ${USER_UID}
WORKDIR ${AIRFLOW_HOME}
ENTRYPOINT ["/entrypoint.sh"]
