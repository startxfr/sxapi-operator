<img align="right" height="50" src="https://raw.githubusercontent.com/startxfr/sxapi-core/master/docs/assets/logo.svg?sanitize=true">

# sxapi operator ![sxapi](https://img.shields.io/badge/latest-v0.1.3-blue.svg)

[![last commit](https://img.shields.io/github/last-commit/startxfr/sxapi-operator.svg)](https://github.com/startxfr/sxapi-operator)
[![Doc](https://readthedocs.org/projects/sxapi-core/badge)](https://sxapi-core.readthedocs.io)
[![Build Status](https://travis-ci.org/startxfr/sxapi-operator.svg?branch=devel)](https://travis-ci.org/startxfr/sxapi-operator)

Operator used to deploy **sxapi** applications. SXApi for **s**imple and e**x**tensible **api** (Application Programming Interface) is an an open-source framework for quickly building simple and small API based on microservice architecture.

Very light (application less than 100Ko, full container stack for less than 30Mo) and configured with a single json file, you can build instantly small atomic API endpoints as well as fully featured enterprise-sized API.

You can get some sample code and components usage by [reading the sxapi documentation](https://sxapi-core.readthedocs.io).


## Install the SXApi operator

### Install into an Openshift cluster

The SXApi operator is available via the Operatorhub, available via the community catalog already loaded into an openshift cluster.

#### Using the web console

You can go to the the **`operator > operatorHub`** menu, and search for sxapi into the operator catalog. You can follow
the installation and wait for your operator to be deployed globally.

#### Using the oc client

You can use the following files to create a subscription for a global installation of you SXApi operator. This configuration
allow you to deploy applications into all namespaces.

```bash
# for alpha channel (devel)
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/devel/load-sub.yaml
# for fast channel (sandbox, testing)
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/main/load-sub.yaml
# for stable channel (production)
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/stable/load-sub.yaml
```

### Install into a kubernetes cluster

For other kubernetes distribution, you must install the Operator lifecycle Manager (OLM) into your cluster prior the execute the following commands.

```bash
# for alpha channel (devel)
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/devel/load-catalog.yaml
# for fast channel (sandbox, testing)
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/main/load-catalog.yaml
# for stable channel (production)
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/stable/load-catalog.yaml
```

For more information on how to use this operator, see [sxapi operator repository](https://github.com/startxfr/sxapi-operator)

## Run your sxapi instance

### Install the default app

After installing the sxapi operator, you can run you first sxapi application with the following example.

Application is deployed in the same namespace as the SXApi custom resource. You should create a project
to host this example, with ```oc new-project demo-sxapi-default```, prior to creating this resource.

```yaml
apiVersion: sxapi.startx.fr/v1alpha1
kind: SXApi
metadata:
  name: default-sxapi
spec:
  sxapi:
    expose:
      enabled: true
    data: |
      sxapi.yml: |-
        name: simple-frontend
        description: simple webpage
        version: "0.0.1"
        server:
          port: '8077'
          endpoints:
          - path: "/"
            body: |-
                <html>
                <head><title>Simple frontend (demo)</title></head>
                <body>
                  <h1>Simple frontend (demo)</h1>
                  <p>
                    This example is deployed using the sxapi operator based on the 
                    <a href="https://helm-repository.readthedocs.io/en/latest/charts/sxapi.html" target="_blank">sxapi chart</a>
                    available in the <a href="https://helm-repository.readthedocs.io" target="_blank">startx helm repository</a>. 
                  </p>
                </body>
                </html>
```

You can instanciate the default sxapi application with the following command

```bash
oc new-project demo-sxapi-default
vi sxapi-apps.yaml
# copy and paste the content of the previous yaml content
oc apply -f sxapi-apps.yaml
```

### Example marketing prod

Deploying the version **0.0.1** of the application **blackfriday** running for the **marketing BU**.
Staged to **production**, the application is **exposed**, replicated to **3** and has debug mode disabled.
The application is executed with the **0.3.57** version of the [sxapi container image](https://hub.docker.com/r/startx/sxapi)
and use the **prod:start** profile.

For tenancy purpose, this application will be deployed into its **mkg-blackfriday-prod** namespace.

```bash
# create and goto project
oc new-project mkg-blackfriday-prod
# create the sxapi application
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/main/config/samples/marketing-blackfriday-prod.yaml
```

### Example marketing preprod

Deploying the version **0.0.1** of the application **blackfriday** running for the **marketing BU**.
Staged to **preprod**, the application is **exposed**, replicated to **2** and has debug mode disabled.
The application is executed with the **0.3.57** version of the [sxapi container image](https://hub.docker.com/r/startx/sxapi)
and use the **prod:start** profile.

For tenancy purpose, this application will be deployed into its **mkg-blackfriday-preprod** namespace.

```bash
# create and goto project
oc new-project mkg-blackfriday-preprod
# create the sxapi application
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/main/config/samples/marketing-blackfriday-prod.yaml
```

### Example marketing stagging

A simple example deploy the version **0.0.2** of the application **blackfriday** running for the **marketing BU**.
Staged to **stagging**, the application is **not exposed**, replicated to **2** and has **debug mode** enabled.
The application is executed with the **0.3.58** version of the [sxapi container image](https://hub.docker.com/r/startx/sxapi)
and use the **prod:start** profile.

For tenancy purpose, this application will be deployed into its **mkg-blackfriday-sandbox** namespace, collocated with testing and dev
runtimes.

```bash
# create and goto project
oc new-project mkg-blackfriday-sandbox
# create the sxapi application
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/main/config/samples/marketing-blackfriday-stagging.yaml
```

### Example marketing testing

A simple example deploy the version **0.0.3** of the application **blackfriday** running for the **marketing BU**.
Staged to **testing**, the application is **not exposed**, replicated to **1** and has **debug mode** enabled.
The application is executed with the **latest** version of the [sxapi container image](https://hub.docker.com/r/startx/sxapi)
and use the **prod:start** profile.

For tenancy purpose, this application will be deployed into its **mkg-blackfriday-sandbox** namespace, collocated with stagging and dev
runtimes.

```bash
# create and goto project
oc new-project mkg-blackfriday-sandbox
# create the sxapi application
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/main/config/samples/marketing-blackfriday-testing.yaml
```

### Example marketing dev

A simple example deploy the version **0.0.4** of the application **blackfriday** running for the **marketing BU**.
Staged to **dev**, the application is **not exposed**, replicated to **1** and has **debug mode** enabled.
The application is executed with the **testing** version of the [sxapi container image](https://hub.docker.com/r/startx/sxapi)
and use the **prod:start** profile.

For tenancy purpose, this application will be deployed into its **mkg-blackfriday-sandbox** namespace, collocated with stagging and testing
runtimes.

```bash
# create and goto project
oc new-project mkg-blackfriday-sandbox
# create the sxapi application
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/main/config/samples/marketing-blackfriday-dev.yaml
```

## Contributing

Read the [contributing guide](https://github.com/startxfr/sxapi-core/tree/master/docs/guides/5.Contribute.md) for details on our code of conduct, and the process for submitting pull requests to us.

## Authors

This project is mainly developped by the [startx](https://www.startx.fr) dev team. You can see the complete list of contributors who participated in this project by reading [contributors.md](https://github.com/startxfr/sxapi-core/tree/master/docs/contributors.md).

## License

This project is licensed under the GPL Version 3 - see the [LICENSE.md](https://github.com/startxfr/sxapi-core/tree/master/docs/LICENSE.md) file for details
