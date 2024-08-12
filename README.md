# titiler-xarray

---

**Source Code**: <a href="https://github.com/developmentseed/titiler-xarray" target="_blank">https://github.com/developmentseed/titiler-xarray</a>

---

## Running Locally

```bash
# It's recommanded to use virtual environment
python -m pip install --upgrade virtualenv
virtualenv .venv
source .venv/bin/activate

python -m pip install -e . uvicorn
uvicorn titiler.xarray.main:app --reload
```

To access the docs, visit http://127.0.0.1:8000/api.html.
![](https://github.com/developmentseed/titiler-xarray/assets/10407788/4368546b-5b60-4cd5-86be-fdd959374b17)

## Testing

Tests use data generated locally by using `tests/fixtures/generate_test_*.py` scripts.

To run all the tests:

```bash
python -m pip install -e ".[test]"
python -m pytest --cov titiler.xarray --cov-report term-missing -s -vv
```

To run just one test:

```bash
python -m pytest tests/test_app.py::test_get_info --cov titiler.xarray --cov-report term-missing -s -vv
```

## VEDA Deployment

The Github Actions workflow defined in [.github/workflows/ci.yml](./.github/workflows/ci.yml) deploys code to AWS for the VEDA project.

* There are 2 stacks - one production and one development.
* The production stack is deployed when the `main` branch is tagged, creating a new release. The production stack will deploy to a stack with an API Gateway associated with the domain prod-titiler-xarray.delta-backend.com/.
* The development stack will be deployed upon pushes to the `dev` and `main` branches. The development stack will deploy to a stack with an API Gateway associated with the domain dev-titiler-xarray.delta-backend.com/.


## New Deployments

The following steps detail how to to setup and deploy the CDK stack from your local machine.

1. Install CDK and connect to your AWS account. This step is only necessary once per AWS account.

    ```bash
    # Download titiler repo
    git clone https://github.com/developmentseed/titiler-xarray.git

    # Create a virtual environment
    python -m pip install --upgrade virtualenv
    virtualenv infrastructure/aws/.venv
    source infrastructure/aws/.venv/bin/activate

    # install cdk dependencies
    python -m pip install -r infrastructure/aws/requirements-cdk.txt

    # Install node dependency
    npm --prefix infrastructure/aws install

    # Deploys the CDK toolkit stack into an AWS environment
    npm --prefix infrastructure/aws run cdk -- bootstrap

    # or to a specific region and or using AWS profile
    AWS_DEFAULT_REGION=us-west-2 AWS_REGION=us-west-2 AWS_PROFILE=myprofile npm --prefix infrastructure/aws run cdk -- bootstrap
    ```

2. Update settings

    Set environment variable or hard code in `infrastructure/aws/.env` file (e.g `STACK_STAGE=testing`).

3. Pre-Generate CFN template

    ```bash
    npm --prefix infrastructure/aws run cdk -- synth  # Synthesizes and prints the CloudFormation template for this stack
    ```

4. Deploy

    ```bash
    STACK_STAGE=staging npm --prefix infrastructure/aws run cdk -- deploy titiler-xarray-staging

    # Deploy in specific region
    AWS_DEFAULT_REGION=us-west-2 AWS_REGION=us-west-2 AWS_PROFILE=smce-veda STACK_STAGE=production  npm --prefix infrastructure/aws run cdk -- deploy titiler-xarray-production
    ```


**Important**

In AWS Lambda environment we need to have specific version of botocore, S3FS, FSPEC and other libraries.
To make sure the application will both work locally and in AWS Lambda environment you can install the dependencies using `python -m pip install -r infrastructure/aws/requirement-lambda.txt`
