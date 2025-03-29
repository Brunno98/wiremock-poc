# Wiremock

Projeto para aprender e testar algumas funcionalidades do [Wiremock](https://wiremock.org/docs/)

### Funcionalidades:
- [x] Rodar com docker
- [x] Rodar com docker-compose
    - [x] passar os mappings para dentro do container para inicar já com as rotas desejadas
- [x] Criar rotas com o record (proxy)

### Utilidades:
- plugin Intelij https://plugins.jetbrains.com/plugin/18860-wiremocha
    - Fornece integracao com o framework wiremock
    - Auxilia na escrita dos mappings
- https://wiremock.org/external-resources/
    - pagina com documentações externas com diferentes casos de uso

## Rodar com docker

para rodar com o docker basta executar o seguinte comando:

```shell
docker run -it --rm \
  -p 8080:8080 \
  --name wiremock \
  wiremock/wiremock:3.12.1

```

após isso, basta acessar a url http://localhost:8080/__admin/mappings para ver as rotas configuradas.

Acessar a [documentação da API de admin](https://wiremock.org/docs/standalone/admin-api-reference) para fazer as configurações de mappings


## Rodar com docker compose

A partir do arquivo [compose.yml](compose.yml), basta rodar o comando `docker compose up -d` para subir o container do wiremock

### Definindo os stubs com arquivos

Os stubs são arquivos json que ficam na pasta (mappings)[mappings/] e que devem ser copiados para a pasta `/home/wiremock/mappings` do container.


Para saber mais, acesse a [documentação dos stubbings](https://wiremock.org/docs/stubbing/)

## Recorder

O recorder pode ser usado para gerar stubs a partir de requisições reais.  
Quando o recorder está ativo, o wiremock funciona como um proxy e vai gravar toda request e response que passar
por ele. Quando o recorder for finalizado, serão gerados arquivos de stub de cada request feita e que podem ser
usados futuramente.
Os stubs gerados são salvos na pasta mappings.

### Iniciando o recorder

O recorder pode ser iniciado acessando a UI localizada no path `/__admin/recorder` ou fazendo a seguinte requisição:
```shell
curl --location 'localhost:8080/__admin/recordings/start' \
--header 'Content-Type: application/json' \
--data '{
    "targetBaseUrl": "http://example.mocklab.io",
    "filters": {
        "urlPathPattern": "/api/.*",
        "method": "GET"
    },
    "captureHeaders": {
        "Accept": {},
        "Content-Type": {
            "caseInsensitive": true
        }
    },
    "requestBodyPattern": {
        "matcher": "equalToJson",
        "ignoreArrayOrder": false,
        "ignoreExtraElements": true
    },
    "extractBodyCriteria": {
        "textSizeThreshold": "2048",
        "binarySizeThreshold": "10240"
    },
    "persist": false,
    "repeatsAsScenarios": false,
    "transformers": [
        "modify-response-header"
    ],
    "transformerParameters": {
        "headerValue": "123"
    }
}'
```

## Utilidades

Algumas requests de test estão salvas na pasta [postman](postman/).  
Para usa-las basta importar os arquivos `postman_collection` e `postman_environment` no postamn.