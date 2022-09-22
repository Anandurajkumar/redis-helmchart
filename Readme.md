helm install redis-test --set persistence.storageClass=nfs-client,redis.replicas.persistence.storageClass=nfs-client bitnami/redis --set volumePermissions.enabled=true

export REDIS_PASSWORD=$(kubectl get secret --namespace default redis-test -o jsonpath="{.data.redis-password}" | base64 --decode)


kubectl run --namespace default redis-client --restart='Never'  --env REDIS_PASSWORD=$REDIS_PASSWORD  --image docker.io/bitnami/redis:6.2.5-debian-10-r63 --command -- sleep infinity



kubectl exec --tty -i redis-client --namespace default -- bash


redis-cli -h redis-test-master -a $REDIS_PASSWORD



redis-cli -h redis-test-replicas -a $REDIS_PASSWORD
PING
