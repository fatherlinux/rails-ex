Rails Sample App With External Postgres Database on OpenShift
============================
This repository is a fork of OpenShift Rails Example [1]. It was originally created for a this workshop [3] and is designed to be used with an external database provisioned as a VM in an external system such as OpenStack or RHEV. As long as you pass this template the right variables, it will attempt to connect to an external database instead of provisioning a pod.

This quickstart utilizes an OpenShift/Atomic Platform Service Object which is linked to an End Point Object (instructions here [2]) which will communicate with the external database.

##Usage##
This quickstart can be manually provisioned or driven from Ansible, CloudForms or other automated means by instantiating it with the following format:
   
   ```oc process -f /root/rails-ex-external/openshift/templates/rails-postgresql.json -v DATABASE_SERVICE_PORT=5432,DATABASE_SERVICE_HOST=${DB_IP_ADDRESS},DATABASE_USER=${DB_USERNAME},DATABASE_PASSWORD=${DB_PASSWORD},DATABASE_NAME=${DB_NAME}```

##Hack Usage##
This is not propor usage, but might be useful for getting it up and running. There are a couple of work arounds that need explained. First, sleep was used to solve some kind of concurrency problem where old objects were not deleted before "oc new-project" tried to create them again, causing a failure. Second, sed was used to fix a small bug in the oc process command [4].
  
  ```export KUBECONFIG=/etc/origin/master/admin.kubeconfig
  /bin/oc delete namespace test
  sleep 15
  /bin/oc new-project test
  oc process -f /root/rails-ex-external/openshift/templates/rails-postgresql.json -v DATABASE_SERVICE_PORT=5432,DATABASE_SERVICE_HOST=${DB_IP_ADDRESS},DATABASE_USER=${DB_USERNAME},DATABASE_PASSWORD=${DB_PASSWORD},DATABASE_NAME=${DB_NAME} | sed -e 's/"port": "5432"/"port": 5432/g' | oc create -f -
oc policy add-role-to-user admin admin -n test```

[1]: https://github.com/openshift/rails-ex
[2]: https://docs.openshift.com/enterprise/3.0/dev_guide/integrating_external_services.html
[3]: https://github.com/fatherlinux/workshop
[4]: https://bugzilla.redhat.com/show_bug.cgi?id=1277188
