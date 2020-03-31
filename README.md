# Bugshop

Bugshop is heavily WIP, but stay tuned for new updates (using new Argo Server directly instead of Events) planned in April. :-)

## Pre-requsites
1. Working Kubernetes cluster (preferably on GKE due to IAP setup)
2. Functional `kubectl` and `argo` CLI communicating with your cluster.
3. 2 GitLab repositories (targets-data, targets-alerts) with a write deploy key.

## Setup
### Bugshop
1. Install all manifests located in `manifests` folder. `for f in manifests/*; do kubectl apply -f $f; done` If something is not working during the install, please refer to the official Argo installation guide located at https://argoproj.github.io/.
2. Create 3 GCS buckets and replace the template name with the name of your bucket. 
    - `perma` for the mirroring of wordlists from the Bugshop repository
    - `workflowconfigs` for the mirroring of workflows from the Bugshop repository
    - `artifacts` for the artifacts from Argo Workflows
3. Configure `artifacts` as the Argo storage according to the https://github.com/argoproj/argo/blob/master/ARTIFACT_REPO.md
3. Setup desired configs (amass / fingerprints.json)
4. Setup k8 secrets 
    - `minio-secrets` with legacy GCS storage secrets
    - `gitlab-ssh` with `ssh-private-key` deploy key to GitLab repositories
    - `config` 
5. Overwrite `http://argo-gateway.domain.com:12000/secret-webhook-endpoint` with your Argo HTTP gateway port and endpoint (specified in `webhook-event-source.yml`).
6. Add GCS secrets to GitLab CI according to the `.gitlab-ci.yml`

### Bugtab
1. Add `GCS_ACCESS_KEY_ID` and `GCS_SECRET_ACCESS_KEY` secrets to the Bugtab GitLab CI secrets.
2. Setup periodic schedules with the desired intervals
3. Setup hourly schedule for the public programs
4. Setup daily schedule (13:00) for the private hackerone invites.

## (optional) IAP for Argo UI
1. Expose service as a NodePort (`kubectl patch svc argo-ui -n argo -p '{"spec": {"type": "NodePort"}}`)
)
2. Delete LB and apply ingress.yaml resource
3. Add `beta.cloud.google.com/backend-config: '{"default": "config-default"}'` to the `argo-ui` Service. (`kubectl edit svc argo-ui --namespace argo`)
