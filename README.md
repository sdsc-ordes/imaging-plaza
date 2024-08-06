# imaging-plaza
This repository contains an script and docker-compose.yml able to deploy and run all the services needed for an imaging plaza cold start. 

1. GraphDB
2. Gimie
3. SHACL validator
4. FAIR Indicator
5. Frontend

The following services still rely in third-party providers:

- Firebase Authentication
- Firebase Database

## How to run it?

1. First add the credentials in `./config/.env.local` file.
2. Uncompress the initial database (`db.zip` file not shared in this repo) in `./db`.
    - Alternatively you can create an empty directory and initiate graphDB manually. 
3. Execute the following docker compose commands

``` bash
docker compose config
docker compose build
docker compose up
```

4. Access to the page in [http://0.0.0.0:3000](http://0.0.0.0:3000)

Other services deployed are: 

- GraphDB Workbench [http://0.0.0.0:7515](http://0.0.0.0:7515)
- Fair Indicator GUI [http://0.0.0.0:8502](http://0.0.0.0:8502)
- SHACL Validator GUI [http://0.0.0.0:8501](http://0.0.0.0:8501)

## How to run the developer mode?

> [!NOTE]  
> This repository makes use of submodules. Therefore, when cloning it you need to include them.
>  
> `git clone --recurse-submodules https://github.com/sdsc-ordes/imaging-plaza`

In development mode you can make use of the submodules to change to the development branch you would like to test.

``` bash
docker compose -f docker-compose.dev.yml config
docker compose -f docker-compose.dev.yml build
docker compose -f docker-compose.dev.yml up
```

Also the submodules app folders are mounted as volumes in the `/app` folders in docker.

For instace:

``` yaml
volumes:
    - "./gimie-api/app:/app
...
```

Finally, the entrypoint are replaced by bash so the services are required to be initialized manually.

- **gimie-api**: `uvicorn app.main:app --host 0.0.0.0 --port 15400`
- **shacl-validator-api**: `bash /app/entrypoint.sh`
- **fair-indicator-api**: `bash /app/imaging_plaza_fair_indicator_api/entrypoint.sh`
- **imaging-plaza-frontend**: `sh entrypoint.sh`

If you want to disable this for some services, please comment the following lines in the `docker-compose.dev.yml`:

``` yaml
# entrypoint: bash 
# stdin_open: true
# tty: true
```

Once you have the different services up and running you can open with `vscode` (or any other IDE) the submodule folder and modify the different files. In the case of `imaging-plaza-frontend` those change will be applied automatically.


## Documentation

### How to test a remote microservice?

Docker compose use internal alias for the services. The name of the container plus the port can be used in order to communicate among them. If you want to test a deployed server, please change the host in `./config/.env.local` by the `IP` where the services is deployed:

For instance, if we want to connect to a remote graphDB deployed in `REMOTE_IP` you can do:

```
IP_DB_URL=http://REMOTE_IP:7200
IP_GIMIE_URL=http://gimie-api:15400/gimie/jsonld
IP_INFERENCE_URL=http://shacl-validator-api:15400/inference-jsonld?jsonInput=true&jsonOutput=true
IP_FAIR_LEVEL_INDICATOR=http://fair-indicator-api:15400/indicate
```
