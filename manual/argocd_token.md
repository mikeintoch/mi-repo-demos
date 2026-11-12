--- Create ARGOCD token

ARGOCD_SERVER=$(oc get route argocd-server -n other-gitops -o jsonpath='{.spec.host}')

ADMIN_PASS=$(oc get secret argocd-cluster -ojsonpath='{.data.admin\.password}' -n other-gitops | base64 -d)

# NOTE:If your cluster uses self signed certs use -k in curl
ARGO_TOKEN=$(curl -s -H "Content-Type: application/json" https://$ARGOCD_SERVER/api/v1/session -d '{"username":"admin","password":"'$ADMIN_PASS'"}' | jq -r '.token')

curl -s -H "Authorization: Bearer $ARGO_TOKEN" -H "Content-Type: application/json" -X POST -d '{"expiresIn": 0,"id": "rhdh","name": "RHDH top level token"}' https://$ARGOCD_SERVER/api/v1/account/rhdh/token
{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9…………"}