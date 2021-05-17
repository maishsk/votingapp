### Votingapp

This is a simple API service built for various tests purposes. This was built to test AWS App Runner specifically but it can be used for other purposes. This application is a scaled down version of (and it's been inspired by) [Yelb](https://github.com/mreferre/yelb/).

The application puts and stores "votes" in a Amazon DynamoDB table. You can vote by just CURLing (or similar) to 4 APIs: 
```
/api/outback
/api/bucadibeppo
/api/ihop
/api/chipotle
```
In addition to vote, you can query the status by CURLing (or similar) the `/api/getvotes` API. Note that there is an experimental feature in the code to artificially consume more memory/CPU. This is available by hitting the `/api/getheavyvotes` API. The amount of artificial load is determined by two variables (`MEMSTRESSFACTOR` and `CPUSTRESSFACTOR` which default to `1`). You can tweak the amount of load by, for example, setting them to `0.1` if you want less overhead or `10` if you want more overhead. 

### How to set up the application

This is a classic Python application. To use it with AWS App Runner you can build the image upfront (a `Dockerfile` is provided) and push it to ECR or you can provide the source code directly. If you are deploying the application with AWS App Runner, the root of the repository contains the `apprunner.yaml` file used to configure the required parameters for runtime. The only requirements creating the DynamoDB table and set the proper permissions. In the [preparation](/preparation) folder there are instructions and code to make this happen. 

#### AWS App Runner console deployment

First, please run the `preparation.sh` script in this repository to create the DynamoDB table and the various roles and policies required. Also, clone this repo in your GH account. Then move to the AWS App Runner console.

- Click on `Create Service` 
- Repository type: `Source code repository`
- Connect to your GitHub account and select the fork of this repository (use the `main` branch)
- Deployment trigger: `Manual`
- In the build settings select `Use a configuration file`
- Give this service a name 
- In the `Security` section select the `votingapp-role` IAM role created by the `preparation.sh` script  

Please note that the `apprunner.yaml` configuration file set the `DDB_AWS_REGION` variable to `us-east-1`. If your DynamoDB table is in another region (and/or if you opted to create a table with a different name) please change/add the variables values accordingly in the file. 

#### Other deployments

This app has been created to test deployments to AWS App Runner. However this is a standard Python application you can use in any other context provided you adhere to the architecture and prerequisites. The application comes with its `requirements.txt` file and `Dockerfile`. 

#### Variables

- `DDB_AWS_REGION` this variable is required and needs to be set to the region of the DynamoDB table.
- `DDB_TABLE_NAME` this variable is optional and contains the DynamoDB table name (default: `votingapp-restaurants`)
- `MEMSTRESSFACTOR` and `CPUSTRESSFACTOR` are optional and governs the behaviour of the artificial load (experimental)


#### Licensing

This application is made available under the [MIT license](./LICENSE). The Python prerequisite required to run this application and their licensing are as follows:
```
Flask - BSD License 
Flask-Cors - MIT License
Boto3 - Apache License 2.0
Botocore - Apache License 2.0
Simplejson - Academic Free License (AFL), MIT License (MIT License)
```

