az acr login -n wdatpacreus.azurecr.io


# run this from ./src folder
docker build . -t mdm-scraper-3 -f .\Promitor.Agents.Scraper\Dockerfile.linux
docker tag mdm-scraper-3 wdatpacreus.azurecr.io/mgmt.promitor.scraper:20220915.1
docker push wdatpacreus.azurecr.io/mgmt.promitor.scraper:20220915.1


helm upgrade --install mgmt-promitor-mdm promitor-agent-scraper -n management

