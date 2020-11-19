# HTPASSWD authentication

##### Creating an HTPasswd file

```
# htpasswd -c -B -b </path/to/users.htpasswd> <user_name> <password>
```

The command generates a hashed version of the password

##### Add more users or update already existing users to the HTPasswd file

```
# htpasswd -b </path/to/users.htpasswd> <user_name> <password>
```

##### Create an OpenShift Container Platform secret `htpass-secret` that contains the HTPasswd users file

```
# oc create secret generic htpass-secret --from-file=htpasswd=</path/to/users.htpasswd> -n openshift-config
```

##### Create the custom resource

```
# oc apply -f htpasswd.cr.yaml
```

### Updating the HTPasswd file

##### Dump current `htpass-secret` content to htpasswd file

```
# oc get secret htpass-secret -n openshift-config -o jsonpath={.data.htpasswd} | base64 -d > htpass-secret
```

##### Add or update user and/or passwords

```
# htpasswd -Bb htpass-secret USER PASSWORD
```

##### Patch htpass-secret data with content from file

```
# oc patch secret htpass-secret -n openshift-config -p '{"data":{"htpasswd":"'$(base64 -w0 htpasswd-secret)'"}}'
```
