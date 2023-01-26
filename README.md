# Demo to run encrypted images on OpenShift

### Step 1 create an encrypted image
openssl genrsa -out private.pem
openssl rsa -in private.pem -pubout -out public.pem
podman pull docker.io/maxisses/gotminingui:latest
skopeo copy --encryption-key jwe:./public.pem docker://docker.io/maxisses/gotminingui:latest docker://docker.io/maxisses/gotminingui:encrypted

## deploy to OpenShift
oc apply -f sample-deployment.yaml

## observe an error
## create a machine config, modifying the crio config and adding the private key to all the worker nodes
oc apply -f machine-config-crio-decrypt.yaml

## wait, while ALL worker nodes get restarted, one by one, creating the files on the hosts filesystem

... your image should get decrypted and run fine now!