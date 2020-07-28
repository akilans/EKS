### Sentry Local setup
* URL - https://hub.docker.com/_/sentry/
* docker run -d --name sentry-redis redis
* docker run -d --name sentry-postgres -e POSTGRES_PASSWORD=secret -e POSTGRES_USER=sentry postgres
* docker run --rm sentry config generate-secret-key
* 7=v@5g0s5lv7d-bkrwwiow)s74+hppix4r@v-0^ixl&we0lqrs
* docker run -it --rm -e SENTRY_SECRET_KEY='7=v@5g0s5lv7d-bkrwwiow)s74+hppix4r@v-0^ixl&we0lqrs' --link sentry-postgres:postgres --link sentry-redis:redis sentry upgrade


* docker run -d --name my-sentry -e SENTRY_SERVER_EMAIL="akil.dove@gmail.com" -e SENTRY_EMAIL_HOST="email-smtp.us-east-2.amazonaws.com" -e SENTRY_EMAIL_PORT="25" -e SENTRY_EMAIL_USER="sdfsffs" -e SENTRY_EMAIL_PASSWORD="xcvzxcvzxvxxzcvzxczvzxvczcxc" -e SENTRY_EMAIL_USE_TLS="True" -e SENTRY_SECRET_KEY='7=v@5g0s5lv7d-bkrwwiow)s74+hppix4r@v-0^ixl&we0lqrs' -p 8000:9000 --link sentry-redis:redis --link sentry-postgres:postgres sentry

* docker run -d --name sentry-cron -e SENTRY_SERVER_EMAIL="akil.dove@gmail.com" -e SENTRY_EMAIL_HOST="email-smtp.us-east-2.amazonaws.com" -e SENTRY_EMAIL_PORT="25" -e SENTRY_EMAIL_USER="sdfsffs" -e SENTRY_EMAIL_PASSWORD="xcvzxcvzxvxxzcvzxczvzxvczcxc" -e SENTRY_EMAIL_USE_TLS="True" -e SENTRY_SECRET_KEY='7=v@5g0s5lv7d-bkrwwiow)s74+hppix4r@v-0^ixl&we0lqrs' --link sentry-postgres:postgres --link sentry-redis:redis sentry run cron

* docker run -d --name sentry-worker-1 -e SENTRY_SERVER_EMAIL="akil.dove@gmail.com" -e SENTRY_EMAIL_HOST="email-smtp.us-east-2.amazonaws.com" -e SENTRY_EMAIL_PORT="25" -e SENTRY_EMAIL_USER="sdfsffs" -e SENTRY_EMAIL_PASSWORD="xcvzxcvzxvxxzcvzxczvzxvczcxc" -e SENTRY_EMAIL_USE_TLS="True" -e SENTRY_SECRET_KEY='7=v@5g0s5lv7d-bkrwwiow)s74+hppix4r@v-0^ixl&we0lqrs' --link sentry-postgres:postgres --link sentry-redis:redis sentry run worker

* kubectl apply -f redis-dep.yaml
* kubectl apply -f postgres-dep.yaml

* kubectl expose deployment redis --port=6379 --type=ClusterIP
* kubectl expose deployment postgres --port=5432 --type=ClusterIP

* kubectl apply -f sentry-dep.yaml

* kubectl exec -it sentry-dep-dfsf -- bash - ./entrypoint upgrade # Create DB tables

* kubectl expose deployment my-sentry --port=9000 --type=NodePort

* Access the web page

* kubectl apply -f sentry-cron.yaml
* kubectl apply -f sentry-worker.yaml

* https://github.com/helm/charts/blob/master/stable/sentry/values.yaml
* https://github.com/ginocbjr/sentry-helm/blob/master/init/templates/deployment-memcached.yml
* https://github.com/sentry-kubernetes/charts
