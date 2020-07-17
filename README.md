# Overview

This is an Ansible playbook that can install Keycloak with a Postgres database. The Postgres database is managed by an Postgres database operator and you can use either the [Crunchydata](https://github.com/CrunchyData/postgres-operator) or the [Zalando](https://github.com/zalando/postgres-operator) Postgres operator.

# Prerequisites

* An OpenShift 4.4 cluster. OperatorHub is installed on the cluster (it should be by default).
* Keycloak operator and CrunchyData Postgres operator are available from the OperatorHub (they should be there by default)
* [Ansible](https://www.ansible.com/)

# Usage

1. Login to the cluster as cluster-admin and get a token.
2. Clone the repo and cd into it
3. Then run the following commands:
   ```
   export HOST=<openshift host>
   export TOKEN=<auth token>
   # Run the playbook with CrunchyData postgres operator
   ansible-playbook -i hosts.yaml main.yaml -e openshift_host=$HOST -e openshift_token=$TOKEN --tags=crunchydata,sso
   # Or using the Zalando postgres operator:
   ansible-playbook -i hosts.yaml main.yaml -e openshift_host=$HOST -e openshift_token=$TOKEN --tags=zalando,sso
   ```

# Known Issues

* It will take a few minutes for Keycloak pods to get ready. If the pods are not ready after a while and they are consistently getting killed by OpenShift, you should be able to fix that by doing the following:
  * Scale down the keycloak-operator deployment
  * Update the Keycloak StatefulSet configuration to increase the `failureThreshold` for `readinessProbe`. Increase it a value like 10. Save the update.
  * Delete the existing keycloak pods. The StatefulSet will create them again.
