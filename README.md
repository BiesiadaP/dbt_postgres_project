## Project Report: Building Data Models and ELT Process using dbt, Target DuckDB

🚀 Project Overview

The project aimed to establish an analytical and transformation environment utilizing dbt (data build tool) with DuckDB as the target data warehouse. This entire environment was deployed on an AWS EC2 instance using Docker containers to create an integrated and networked workspace.

⚙️ Infrastructure and Environment Setup

    AWS EC2 Deployment: The foundational infrastructure was provisioned on an AWS EC2 instance.

    Dockerized Environment: Two essential Docker containers were launched on the EC2 instance, networked together:

        PostgreSQL Container: Served as the primary data source.

        VS Code Server Container: Provided a browser-accessible IDE (on port 8888) and contained the pre-configured dbt and DuckDB environment.

    Docker Network Communication: Communication between the dbt environment and the source database was seamless, relying on the Docker network. This allowed the dbt configuration to reference the PostgreSQL database simply by its container name, eliminating the need to specify an IP address.

💾 Data Source and Integration

    Source Data: The source data originated from the sample Sakila database, containing information on films, actors, and rentals.

    DuckDB-PostgreSQL Integration: Data was accessed directly by DuckDB from PostgreSQL using the postgres_scan function within the staging (stg_) dbt models. This feature enabled the PostgreSQL tables to be treated as external data sources without requiring an upfront physical copy or load into DuckDB, effectively implementing a modern ELT (Extract, Load, Transform) pattern.

🏗️ Data Transformation with dbt

    dbt Project: A dbt project named dbt_postgres_project was configured to use DuckDB as its target database.

    Modeling: Data transformations were performed in two main layers:

        Staging Models (stg_*): These models directly utilize the postgres_scan function to connect to the source tables and perform basic cleansing and standardization.

        Fact Table (fct_rental): This model represents the core business logic, likely joining and aggregating the staging models to create a central fact table for analytical queries.

    Testing: Data quality was ensured through the use of:

        Built-in dbt tests (unique, not_null).

        Custom dbt tests developed for specific business rules.

📄 Documentation and Visualization

After the models were built and tested, the following dbt commands were executed:

    dbt run: Executes the transformation SQL to build the data models in DuckDB.

    dbt test: Runs all defined tests against the materialized models.

    dbt docs generate: Generates the static documentation files.

    dbt docs serve: Launches a local web server to view the project documentation, including a graphic representation of the data lineage and transformations.

📝 Component Installation Guide on EC2

    AWS Infrastructure Launch via Terraform

        Location: /infra folder.

        Action: Run terraform apply after updating the S3 bucket name in the configuration.

    SSH Tunnel Creation

        A secure SSH tunnel was established to forward local ports to the EC2 instance for accessing the container services:
        Bash

    ssh -N -f -L 8888:localhost:8888 -L 8080:localhost:8080 -L 8000:localhost:8000 -i ~/Downloads/kp.pem ec2-user@${aws_instance.lab_instance.public_ip}

Launching Containers on EC2

    Access the EC2 console:
    Bash

    ssh -i ~/Downloads/kp.pem ec2-user@${aws_instance.lab_instance.public_ip}

    PostgreSQL Container: Launched on port 5432 (e.g., named brave_snyder) using instructions from the provided Sakila repository.

    VS Code Server Container: Launched with dbt installed (auto-installed via startup.sh script), exposing port 8888.

In-Container Installation (Inside VS Code Container)

    The following steps were executed to install necessary PostgreSQL client utilities and the dbt adapter:
    Bash

        sudo yum clean metadata
        sudo yum install postgresql postgresql-server postgresql-contrib -y
        python3 -m venv dbt-env
        source dbt-env/bin/activate
        pip install --upgrade pip
        pip install dbt-postgres

This configuration successfully created an integrated analytical environment, allowing local use of the VS Code interface (via localhost:8888) and enabling the PostgreSQL-to-DuckDB data flow through dbt.
