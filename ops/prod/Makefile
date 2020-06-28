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

db.console:
	psql $(POSTGRES_URL)

db.create:
	psql $(POSTGRES_URL) -c "CREATE DATABASE chirp_prod;"
