cluster.config:
	kubectl apply -f ops/k8s/config_map.yaml
	kubectl apply -f ops/k8s/secrets.yaml

cluster.deploy:
	kubectl apply -f ops/k8s/job-db-migration.yaml
	kubectl apply -f ops/k8s/deployment.yaml
	kubectl apply -f ops/k8s/service.yaml

cluster.deploy.force:
	kubectl rollout restart deployment/chirp

cluster.scale.down:
	kubectl scale --replicas=1 -f ops/k8s/deployment.yaml

cluster.scale.up:
	kubectl scale --replicas=3 -f ops/k8s/deployment.yaml