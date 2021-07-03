<img align="right" height="50" src="https://raw.githubusercontent.com/startxfr/sxapi-core/master/docs/assets/logo.svg?sanitize=true">

# sxapi operator ![sxapi](https://img.shields.io/badge/latest-v0.1.0-blue.svg)

[![last commit](https://img.shields.io/github/last-commit/startxfr/sxapi-operator.svg)](https://github.com/startxfr/sxapi-operator)
[![Doc](https://readthedocs.org/projects/sxapi-core/badge)](https://sxapi-core.readthedocs.io)
[![Build Status](https://travis-ci.org/startxfr/sxapi-operator.svg?branch=devel)](https://travis-ci.org/startxfr/sxapi-operator)

Operator used to deploy **sxapi** applications. SXApi for **s**imple and e**x**tensible **api** (Application Programming Interface) is an an open-source framework for quickly building simple and small API based on microservice architecture.

Very light (application less than 100Ko, full container stack for less than 30Mo) and configured with a single json file, you can build instantly small atomic API endpoints as well as fully featured enterprise-sized API.

You can get some sample code and components usage by [reading the sxapi documentation](https://sxapi-core.readthedocs.io).

## Install the SXApi operator

Install the SXApi operator into an openshift cluster using the following command. This command will install :

- The `sxapi-catalog` **CatalogSource**, into the `openshift-marketplace` namespace, running the `quay.io/startx/sxapi-operator-catalog:v0.1.0` catalog image
- The `sxapi-group` **OperatorGroup**, into the `openshift-marketplace` namespace, targeting the `default`, `demo-sxapi` and `sxapi` namespace
- The `sxapi-operator` **Subscription**, into the `default` namespace, using the `sxapi-catalog` catalog

```bash
oc apply -f https://raw.githubusercontent.com/startxfr/sxapi-operator/main/load-catalog.yaml
```

## Run a sxapi application

After installing the sxapi operator, you can run an sxapi application with the following application definition

```yaml
apiVersion: sxapi.startx.fr/v1alpha1
kind: SXApi
metadata:
  name: sxapi-sample
  namespace: default
spec:
  context:
    app: example-sxapi
    cluster: localhost
    component: sxapi
    environment: myenv
    scope: myscope
    version: "0.0.1"
  sxapi:
    data: |
      sxapi.yml: |-
        name: default
        description: Description of the example sxapi instance (defined in sxapi operator)
        version: "0.3.57"
        debug: true
        resources:
          serviceinfo-sample:
            _class: serviceinfo
        log:
          filters:
            level: '0,1,2,3,4'
            type: debug,info,error,warn
        server:
          port: '8077'
          endpoints:
          - path: "/"
            body: |-
                <html>
                <head><title>Example application (operator default values)</title></head>
                <body>
                  <h1>SXAPI is live ! (operator's managed)</h1>
                  <p>
                    This example is deployed using the sxapi operator example based on the 
                    <a href="https://helm-repository.readthedocs.io/en/latest/charts/sxapi.html" target="_blank">sxapi chart</a>
                    available in the <a href="https://helm-repository.readthedocs.io" target="_blank">startx helm repository</a>. 
                  </p>
                  <p>
                    You could check the following endpoints :</p>
                  <ul>
                    <li><a href="./health">Health status (used by readiness probe)</a></li>
                    <li><a href="./info">Information about the application</a></li>
                  </ul>
                </body>
                </html>
          - path: "/health"
            resource: serviceinfo-sample
            endpoint: health
          - path: "/info"
            resource: serviceinfo-sample
            endpoint: info
    debug: true
    expose:
      enabled: true
    profile: prod:start
    replicas: 1
```

You can instanciate this application with the following command

```bash
vi sxapi-apps.yaml
# copy and paste the content of the previous yaml content
oc apply -f sxapi-apps.yaml
```

## Contributing

Read the [contributing guide](https://github.com/startxfr/sxapi-core/tree/master/docs/guides/5.Contribute.md) for details on our code of conduct, and the process for submitting pull requests to us.

## Authors

This project is mainly developped by the [startx](https://www.startx.fr) dev team. You can see the complete list of contributors who participated in this project by reading [contributors.md](https://github.com/startxfr/sxapi-core/tree/master/docs/contributors.md).

## License

This project is licensed under the GPL Version 3 - see the [LICENSE.md](https://github.com/startxfr/sxapi-core/tree/master/docs/LICENSE.md) file for details
