poc-sync-s3
===========

This Proof of Concept demonstrate how to sync an s3 bucket on all nodes and schedule a pod reading this bucket.

To run this PoC:
- create a registry namespace
- build and push the docker image in the folder `image` in the registry
- create a bucket in object storage and fill with some files
- create an API key with access to created bucket
- complete `demo-sync.yaml` with the needed info: `<ACCESS_KEY>`, `<SECRET_KEY>`, `<registry-name>`, `<source-bucket>`
- `kubectl apply -f demo-sync.yaml`
- validate all pods from daemonset are ok in namespace `sync-system`
- navigate to external ip of service `demo-usage` in namespace `default`

How it works:
- a service account and associated RBAC is created to allow labeling of nodes from a pod
- a daemonset is run on each node to `rclone sync` the bucket and apply a label (sync-date) when done
- the pod has a `nodeAffinity` to allow running only on labeled nodes

For the sake of the demonstration, an initContainer is used to demonstrate that it is possible to run different commands when doing the first sync and the subsequent one (which run every hour through `sleep 3600`)
