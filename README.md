# PMN Data Pipeline

Data pipeline for PMN based on Dagster and TimescaleDB.

## Quick start

On Mac
```
brew install timescaledb libpq duckdb postgresql@16
```

TimescaleDB setup
```
timescaledb-tune --quiet --yes
timescaledb_move.sh
brew services restart postgresql@16
```

Create Postgresql table schema
```
psql postgres -f pmn/sql/*.sql
```

Run dagster
```
cd pmn
python -m venv .ve
source .ve/bin/activate
pip install -r requirements.txt
dagster dev
# open http://localhost:3000
```

-------------------------------------------------------------------------------------
# On Docker

### prerequisite: install superbase docker container
```
# Get the code
git clone --depth 1 https://github.com/supabase/supabase

# Go to the docker folder
cd supabase/docker

# Copy the fake env vars
cp .env.example .env # modify the .env if needed

# Pull the latest images
docker compose pull

# Start the services
docker compose up -d
```
open superbase on http://localhost:8000

### build the docker images form dagster and pmn
```
# go to datapipeline directory
cd pmn-main/pmn/datapipeline#

# copy the fake env vars
cp .env.example .env # modify the .env if needed

# build the docker images
docker compose build 

# start the containers services
docker compose up -d
```
open dagster on http://localhost:3000 # username and password are in .env file

### apply all the Postgresql table schema one by one
```
cat pmn/sql/PMN_init.sql | docker exec -i supabase-db psql -U postgres
cat pmn/sql/OSND_init.sql | docker exec -i supabase-db psql -U postgres
cat pmn/sql/001_init.sql | docker exec -i supabase-db psql -U postgres
# or apply manually if you face any issue
```

`To-Do:` from superbase UI Table Editor add an organization and then add a unified_network to it (in future it will be automated).
start `Materialize all`

------------------------------------------------------------------------------------
directory:
## Project Directory Structure

The project directory is organized as follows:

### Directory Details

- **`pmn/`**: The main package directory for the PMN data pipeline.
  - **`__init__.py`**: Initializes the package.
  - **`.env`**: Environment variables file (ignored; never commit to source control).
  - **`definitions.py`**: Defines jobs for the data pipeline.
  - **`resources/`**: Contains resource definitions for connecting to the database, Mist API wrapper, OSND API wrapper, etc.
    - **`__init__.py`**: Initializes the resources package.
    - **`db_resources.py`**: Resource for connecting to the database.
    - **`mist_resources.py`**: Resource for the Mist API wrapper.
  - **`assets/`**: Stores data pipeline assets.
    - **`mist_assets.py`**: Assets for miscellaneous Mist resources.
    - **`mist_site_assets.py`**: Assets for Mist site resources.
    - **`mist_org_assets.py`**: Assets for Mist organization resources.
  - **`configs/`**: Contains configuration files.
    - **`db_config.py`**: Database configurations.
    - **`config.py`**: Configurations for the pipeline.
- **`README.md`**: This file, providing an overview and setup instructions for the project.
