include ops/prod/.env

BIN := DATABASE_URL=$(DATABASE_URL) \
	SECRET_KEY_BASE=$(SECRET_KEY_BASE) \
	_build/prod/rel/chirp/bin/chirp

setup:
	cp ops/prod/.env.sample ops/prod/.env

app.deploy: app.build app.release app.run

app.build:
	MIX_ENV=prod mix phx.digest
	MIX_ENV=prod mix release chirp --force --overwrite

app.release:
	docker-compose up -d
	$(BIN) eval "Chirp.Release.migrate"

app.run:
	docker-compose up -d
	$(BIN) start

container.build:
	docker build -f ops/prod/Dockerfile -t chirp:builder --target=builder .
	docker build -f ops/prod/Dockerfile -t chirp:deps --target=deps .
	docker build -f ops/prod/Dockerfile -t chirp:frontend --target=frontend .
	docker build -f ops/prod/Dockerfile -t chirp:releaser --target=releaser .
	echo "Would normally tag with $$(git rev-parse HEAD)"
	docker build -f ops/prod/Dockerfile -t chirp:$(TAG) .

container.release:
	docker run -it --network chirp_default -p "4000:4000" --rm --env-file ops/prod/.env chirp:$(TAG) eval "Chirp.Release.migrate"

container.run:
	docker run -it --network chirp_default -p "4000:4000" --rm --env-file ops/prod/.env --name chirp chirp:$(TAG)

container.version:
	docker run -it --rm elixir:1.10.3-alpine sh -c 'head -n1 /etc/issue'

container.remote:
	docker exec -it chirp /app/bin/chirp remote

container.bash:
	docker run -it --rm --env-file ops/prod/.env --entrypoint /bin/bash chirp:latest

db.console:
	psql $(POSTGRES_URL)

db.create:
	psql $(POSTGRES_URL) -c "CREATE DATABASE chirp_prod;"
