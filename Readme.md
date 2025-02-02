# How setup github action CI for native solana program

## Step 1: Create a new repository

## Step 2: Create Solana Program

Follow this [install guide](https://solana.com/de/developers/guides/getstarted/local-rust-hello-world)
Just make sure you have the prgoram in the root of the repo like here in this repository.

Dont forget to add a `gitignore` file to the root of the repo to ignore the `target` directory.

```bash file=.gitignore
.secrets
.env
.anchor
.DS_Store
target
**/*.rs.bk
node_modules
test-ledger
.yarn
```

## Step 3: Create Github Actions Workflow

Create a new file in the `.github/workflows` directory called `build.yaml`.

```yaml
name: Devnet Build and Deploy with IDL

on:
  workflow_dispatch:
    inputs:
      priority_fee:
        description: "Priority fee for transactions"
        required: true
        default: "300000"
        type: string

jobs:
  build:
    uses: Woody4618/github-action/.github/workflows/reusable-build.yaml@main
    with:
      program: "transaction_example"
      network: "devnet"
      deploy: true
      upload_idl: true
      verify: false
      use-squads: false
      priority-fee: ${{ github.event.inputs.priority_fee }}
    secrets:
      DEVNET_SOLANA_DEPLOY_URL: ${{ secrets.DEVNET_SOLANA_DEPLOY_URL }}
      DEVNET_DEPLOYER_KEYPAIR: ${{ secrets.DEVNET_DEPLOYER_KEYPAIR }}
      PROGRAM_ADDRESS_KEYPAIR: ${{ secrets.PROGRAM_ADDRESS_KEYPAIR }}
```

Replace your program name in the `program` field.

Set the environment variables in the repository settings.

You can even create your own keypair and let the workflow deploy it for you fpr PROGRAM_ADDRESS_KEYPAIR.

```bash
solana-keygen grind --starts-with ci:1
```

## Step 4: Test the workflow

Run the workflow manually in the actions tab in your repository to make sure it works.
