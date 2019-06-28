# Helm bug demo
This repo is here to allow easy reproduction of an obscure helm bug.

The bug occurs if you new resources get added during a deployment that
fails. When you then fix the reason for that failure it will fail to
roll out the new fix.

## Reproducing instructions

1. Deploy the healthy deployment
```
helm upgrade -i helm-bug-demo ./1-successful-deploy --wait --timeout 120
```

2. Deploy the failing deployment that also adds a new resource. In this
example cronjob-3 fails because the name is too long.
```
helm upgrade -i helm-bug-demo ./2-failing-deploy-too-long-name --wait --timeout 120
```
You will notice cronjobs cj-1 and cj-2 are both created, but cj-3 fails
as expected.

3. Deploy again with the too long name fixed
```
helm upgrade -i helm-bug-demo ./3-all-fixed-but-helm-bug --wait --timeout 120
```
You get the following error:   
`Error: UPGRADE FAILED: no CronJob with the name "cj-2" found`  
It's unclear why. cj-2 is indeed present. I expect Tiller doesn't
realise it successfully created cj-2 because the last deployment failed.
