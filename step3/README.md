# How is it working ?

This step use Hashicorp Vault dynamic secrets with database.

Each time you will go to the website, the application will use a new couple user/password for database access.

It use also Encryption as a Service. Each time you will go to the website, the application will Encrypt and Decrypt value. You can store it into the database as data encryption.

## Initialisation

First, init your terraform folder:

```bash
cd terraform
$ docker run -i -t -v $(pwd):/app/ -w /app/ hashicorp/terraform:light init
cd -
```

Or if you can use Makefile: `make init`

## Infrastructure

As an Ops, you need to deploy the infrastructure:

```bash
$ docker-compose up
```

Or if you can use Makefile: `make infra`

### Service access

* Vault address: [http://127.0.0.1:8200](http://127.0.0.1:8200)

## Application

As an Dev, you need to deploy the infrastructure. In this case, using Vault, your application use [Approle](https://www.vaultproject.io/docs/auth/approle.html) and need **Role_ID** and **Secret_ID**.

Here how to retrieve **Role_ID** and **Secret_ID**:

```bash
$ role_id=$(docker run -i -t -v $(pwd)/terraform:/app/ -w /app/ hashicorp/terraform:light output approle_role_id)
$ secret_id=$(docker run -i -t -v $(pwd)/terraform:/app/ -w /app/ hashicorp/terraform:light output approle_secret_id)
```

And launch your application:

```
$ docker-compose -f app.yml run -e VLT_ROLE_ID=$role_id -e VLT_SECRET_ID=$secret_id --service-ports web
```

Or if you can use Makefile: `make app`

### Service access

* Application address: [http://127.0.0.1:8080](http://127.0.0.1:8080)

## Cleanup

Do the following commands:

```bash
$ docker-compose down
$ rm terraform/terraform.tfstate
```

Or if you can use Makefile: `make clean`