# Configuring chrony time service

If the cluster was already deployed and we have to update the machine configuration after cluster deployment just base64 encode the desired content of /etc/chrony.conf, for example as follows:
```
cat << EOF | base64 -w0
server clock.redhat.com iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
EOF
```
Result is this base64 encoded string:
```
c2VydmVyIGNsb2NrLnJlZGhhdC5jb20gaWJ1cnN0CmRyaWZ0ZmlsZSAvdmFyL2xpYi9jaHJvbnkvZHJpZnQKbWFrZXN0ZXAgMS4wIDMKcnRjc3luYwpsb2dkaXIgL3Zhci9sb2cvY2hyb255Cg==
```

Replace <CHRONY-CONFIG> with the resulting string in 
90_masters-chrony-configuration.yaml

```
sed -i 's/<CHRONY-CONFIG>/<BASE64-ENCODED-STRING>/' 90_masters-chrony-configuration.yaml
```

Apply the new machine-config
```
oc apply -f  90_masters-chrony-configuration.yaml
```

Do the same for each mcp you have configured e.g. worker and infra ...

> RHCOS machines are configured to run **UTC** timezone by default
>
> Changing the timezone isn't supported

The new machine config will be directlz rolled out to the defined pool
If you plan to add more than one machine config to the same pool in a row it will save time to pause the pool first.
A paused pool will not deploy a newly rendered machine config!

#### Pause a machine config pool (e.g. mcp master)
```
oc patch --type=merge --patch='{"spec":{"paused":true}}' machineconfigpool/master
```
#### Unpause a machine config poool (e.g. mcp master)
```
oc patch --type=merge --patch='{"spec":{"paused":false}}' machineconfigpool/master
```
