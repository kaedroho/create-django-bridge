.PHONY: setup deps-server deps-client migrate superuser start

setup: deps-server deps-client migrate ## Sets up development environment
	docker compose run client npm install

deps-server: ## Install Python dependencies
	docker compose run server uv sync

deps-client: ## Install JavaScript dependencies
	docker compose run client npm install

migrate: ## Run Django migrations
	docker compose run server django-admin migrate

superuser: ## Create a superuser
	docker compose run server django-admin createsuperuser

start: ## Starts the docker containers
	docker compose up
