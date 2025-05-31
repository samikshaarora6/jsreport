# Accessing the jsreport Application

## 1. Prerequisites
- Docker and Docker Compose installed
- jsreport and PostgreSQL images built and available in your local Docker registry

## 2. Start the Application
From the root of your repository, run:

```sh
docker-compose up --build -d
```
This will start both the jsreport and PostgreSQL containers.

## 3. Access the jsreport Web Interface
- Open your browser and go to: [http://localhost:5488](http://localhost:5488)
- Login credentials (default):
  - **Username:** admin
  - **Password:** admin

## 4. Using the API with Postman
1. **Import the provided Postman collection** (e.g., `jsreport.postman_collection.json`).
2. **Create a template:**
   - Send a POST request to `/odata/templates` with a body like:
     ```json
     {
       "name": "My Invoice Template",
       "content": "<h1>Hello {{:foo}}</h1>",
       "engine": "handlebars",
       "recipe": "html"
     }
     ```
3. **List templates:**
   - Send a GET request to `/odata/templates` to retrieve available templates and their `shortid` values.
4. **Generate a report:**
   - Send a POST request to `/api/report` with a body like:
     ```json
     {
       "template": { "shortid": "YOUR_TEMPLATE_SHORTID" },
       "data": { "foo": "World" }
     }
     ```
   - Replace `YOUR_TEMPLATE_SHORTID` with the actual `shortid` from your template.
5. **List generated reports:**
   - Send a GET request to `/odata/reports` to see all generated reports and their `shortid` values.
6. **Download or view a report (if supported):**
   - Use a GET request like `http://localhost:5488/odata/reports('YOUR_REPORT_SHORTID')` or `http://localhost:5488/api/report/YOUR_REPORT_SHORTID` (depending on your jsreport version and configuration).

## 5. Checking the PostgreSQL Database
You can inspect the PostgreSQL database directly using the `psql` tool from inside the running container:

1. **Open a shell in the postgres container:**
   ```sh
   docker-compose exec postgres bash
   ```
2. **Connect to the database:**
   ```sh
   psql -U jsreport -d jsreport
   ```
3. **Run SQL commands as needed.**
4. **Exit psql and the container shell when done:**
   ```sh
   \q
   exit
   ```

## 6. Stopping the Application
To stop the containers, run:
```sh
docker-compose down
```

## 7. Troubleshooting
- Ensure both containers are running: `docker ps`
- Check logs for errors:
  - jsreport: `docker-compose logs jsreport`
  - postgres: `docker-compose logs postgres`
- If you change configuration files, rebuild with `docker-compose up --build -d`

## 8. Quick Reference: Checking jsreport Data in PostgreSQL

### 1. List Running Containers
```sh
docker ps
```

### 2. Access the PostgreSQL Container
```sh
docker exec -it jsreport-postgres-1 psql -U jsreport -d jsreport
```

### 3. List All Tables
```sql
\dt
```

### 4. View Recent Templates
```sql
SELECT "name", "shortid", "creationDate" FROM "jsreport_TemplateType" ORDER BY "creationDate" DESC LIMIT 5;
```

## 9. Configuration Summary
- **Postgres storage** is enabled with:
  - **User:** `jsreport`
  - **Password:** `jsreport`
  - **Database:** `jsreport`
- **Host:** `postgres` (Docker service name, correct for Compose)
- **Blob storage:** Filesystem (`fs`) — binary data is stored on disk, not in Postgres.
- **Studio:** Enabled (because `"studio"` is in `extensionsList`)
- **Authentication:** Disabled (`"enabled": false` in config)

## 10. Starting the GitHub Actions Self-Hosted Runner

If you are using a self-hosted GitHub Actions runner, you can start it with the following steps:

1. Navigate to your GitHub Actions runner directory (replace the path as needed):
   ```sh
   cd /path/to/actions-runner
   ```
2. Start the runner:
   ```sh
   ./run.sh
   ```

**Sample script to automate starting the runner:**
```bash
#!/bin/bash

# Navigate to the GitHub Actions runner directory
cd /path/to/actions-runner || exit 1

# Run the runner
./run.sh
```

Replace `/path/to/actions-runner` with the actual path to your runner directory.

---
For more details, see the official [jsreport documentation](https://jsreport.net/learn/api). 