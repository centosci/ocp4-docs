# Adding OIDC Authentication
In CentOS, we have an instance of Ipsilon[1] which we currently use to authenticate many of our services. 


### Steps
This SOP covers configuring ocp.ci/ocp.stg.ci with an OpenID identity provider which is used to communicate with our ACO Ipsilon instance and provide authentication to the cluster.

- Authenticate with the ocp.ci/ocp.stg.ci cluster via the cli
- Create an Openshift Secret containing the ACO/Ipsilon clientSecret
- Create an Openshift Oauth object with the identityProvider configuration


See below for sample template which achieves this.


```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: openshift-oidc-config
objects:
- kind: Secret
  apiVersion: v1
  metadata:
    name: openid-client-secret-ocp-ci
    namespace: openshift-config
  data:
    clientSecret: <base64 encoded OIDC client secret>
  type: Opaque
- apiVersion: config.openshift.io/v1
  kind: OAuth
  metadata:
    name: cluster
  spec:
    identityProviders:
      - mappingMethod: claim
        name: accounts-centos-org
        openID:
          claims:
              email:
              - email
              - custom_email_claim
            name:
              - name
              - nickname
              - given_name
            preferredUsername:
              - email
          clientID: ocp.ci.centos
          clientSecret:
            name: openid-client-secret-ocp-ci
          extraScopes:
            - email
            - profile
          issuer: 'https://id.centos.org/idp/openidc'
        type: OpenID
```



### Resources:
- [1] [Ipsilon](https://ipsilon-project.org/)
 
