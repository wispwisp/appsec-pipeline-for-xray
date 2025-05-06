# xray appsec


## semgrep
```bash
docker pull semgrep/semgrep:latest
docker run --rm -v $(pwd -P)/Xray-core:/src semgrep/semgrep semgrep scan
```


## Trivy
```bash
docker pull aquasec/trivy

docker run --rm -v $(pwd -P)/Xray-core:/app -t aquasec/trivy fs --scanners vuln,secret,misconfig /app
```


## dependency-track
###### start servive
```bash
cd dependencytrack
curl -LO https://dependencytrack.org/docker-compose.yml
docker-compose up
```
###### work with dependecytrack
```bash
# generate sbom file for a project
# - by trivy [https://github.com/aquasecurity/trivy]
docker run --rm -v $(pwd -P)/Xray-core:/app -t aquasec/trivy fs --scanners vuln --format cyclonedx --output bom.json /app
# - by  [https://github.com/CycloneDX/cdxgen]
docker run --rm -v $(pwd -P)/Xray-core:/app -t ghcr.io/cyclonedx/cdxgen:master -r /app -o /app/bom.json
# - by syft [https://github.com/anchore/syft]
docker run --rm -v $(pwd -P)/Xray-core:/app -t anchore/syft --output cyclonedx-json=/app/bom.json dir:/app
# POST it
curl -X "POST" "http://127.0.0.1:8081/api/v1/bom" \
     -H 'Content-Type:multipart/form-data' \
     -H 'X-Api-Key:<FROM LEFT PANEL->ADMINISTRATION->Access Managment->Teams->Automation->Add Keys>' \
     -F "project=<Your project->view details->Object Identifier>" \
     -F "bom=@bom_IN_CURRENT_DIRECTORY.json"
```


# sonarqube
[https://hub.docker.com/_/sonarqube]

```bash
docker pull sonarqube

docker run -d --name sonarqube-db -e POSTGRES_USER=sonar -e POSTGRES_PASSWORD=sonar -e POSTGRES_DB=sonarqube postgres:alpine

docker run -d --name sonarqube -p 9000:9000 --link sonarqube-db:db -e SONAR_JDBC_URL=jdbc:postgresql://db:5432/sonarqube -e SONAR_JDBC_USERNAME=sonar -e SONAR_JDBC_PASSWORD=sonar sonarqube
```
