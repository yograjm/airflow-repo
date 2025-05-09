# airflow-repo


## Steps:

- Start a Postgres docker container in an Amazon EC2 instance
  
  Launch and EC2 instance and install Docker in it
  ```yml
  sudo apt-get update
  sudo apt-get upgrade -y
  sudo apt install docker.io -y
  sudo systemctl start docker
  sudo systemctl enable docker
  sudo docker --version
  ```

  To start a DB container:
  `sudo docker run -it -d -p 5432:5432 -e POSTGRES_PASSWORD=mypassword --name=postgrescont postgres:latest`
  
- Create a Postgres database - `storedb`

  To enter into container:
  `sudo docker exec -it postgrescont psql -U postgres`

  To list all databases: 
  `SELECT datname FROM pg_database WHERE datistemplate = false;`

  To create a new DB
  `CREATE DATABASE storedb;`

  To switch to a particular DB:
  `\c storedb;`

- Create a table - `loans_data`

  To see tables inside a DB:
  `\dt`

  To create table:
    ```yml
    CREATE TABLE loans_data (
        person_age INT,
        person_income INT,
        person_home_ownership VARCHAR(20),
        person_emp_length FLOAT,
        loan_intent VARCHAR(20),
        loan_grade CHAR(1),
        loan_amnt INT,
        loan_int_rate FLOAT,
        loan_status INT,
        loan_percent_income FLOAT,
        cb_person_default_on_file CHAR(1),
        cb_person_cred_hist_length INT
    );
    ```

    To check table rows: (Press `q` if the output is being displayed in a pager)

    `SELECT * FROM loans_data LIMIT 10;`

    `SELECT * FROM loans_data ORDER BY person_age DESC LIMIT 10;`

    `SELECT COUNT(*) FROM loans_data;`

    Use `\q` to quit.

- On Codespace, create a Python script, `process_data.py`, to add data to `storedb` from the csv file
  - Load date from the csv file
  - Add data to db

- Airflow to run the Python script `process_data.py` periodically
  - Ref (https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html)

    `curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.10.5/docker-compose.yaml'`

    `mkdir -p ./dags ./logs ./plugins ./config`

    Add `processing_data.py` file to `./dags` folder

    Create `append_data_to_db_pipeline.py` file in `./dags` folder

    `echo -e "AIRFLOW_UID=$(id -u)" > .env`

    `docker compose up airflow-init`

    `docker compose up`

  - UI running
  - DAG creating
  - Pipeline execution success -- data appending to the DB
  - Stop Airflow: 
  
    `docker compose down --volumes --rmi all`


