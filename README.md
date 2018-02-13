# Image Prediction API Pipeline Demo using SAM Local

![alt text](https://github.com/darkreapyre/itsacat-local/blob/master/assets/images/Prediction_Architecture.png "Architecture")

## Pre-requisites
1. Install AWS CLI.
2. Install SAM Local and Docker.

## Pre-demo Setup
1. Clone the Repository
```terminal
$ git clone https://github.com/darkreapyre/itsacat-local.git
```
2. Build the CI/CD Pipeline
```terminal
$ bin/deploy
```
>**Note:** The deployment should fail getting the parameters.
3. Generate "bad" parameters.
    - Run *Trainer* using SAM Local.
    ```terminal
        $ cd lambda
        $ docker run -v $PWD:/var/task -it lambci/lambda:build-python3.6 /bin/bash -c './create_package.sh'
        $ sam local generate-event s3 --bucket itsacat-local --key training_input/datasets.h5 > event.json
        $ sam local invoke "Trainer" -e event.json
    ```
    - Cleanup the environment.
    ```terminal
        $ rm -rf venv
        $ rm event.json
        $ rm package.zip
    ```
    - Comment the last line of `trainer.py`.
    - Switch from "Blue" to "Green" in `src/templates/results.html`.
    - Commit changes to GitHub.
    - Reject any requests for pipeline approval.
    - Delete the Python runtime Docker container and image.

## Execute the demo.

>**Note:** Don't forget to uncomment `trainer.py` (as well as rebuilding the deployment package) before uploading the Lambda package to AWS for "production" testing.

1. Update `num_interations` to `3000` and comment oput last line, then save.
2. Update deployment package.
3. Generate the `events.json`.
4. `invoke` the function locally.
5. Uncomment last line of `trainer.py`.
6. Update deployment package.
7. Package the function for the cloud.
```terminal
    $ sam package --template-file template.yaml --s3-bucket itsacat-local --output-template-file packaged.yaml
```
8. Deploy to produciton.
```terminal
    $ sam deploy --template-file packaged.yaml --stack-name demo-lambda --capabilities CAPABILITY_IAM
```
9. Trigger event and monitor.