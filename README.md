# 🔐 web3-google-hsm


<div align="center" markdown>

| Feature       | Value                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Technology    | [![Python](https://img.shields.io/badge/Python-3776AB.svg?style=flat&logo=Python&logoColor=white)](https://www.python.org/) [![Hatch project](https://img.shields.io/badge/%F0%9F%A5%9A-Hatch-4051b5.svg)](https://github.com/pypa/hatch) [![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF.svg?style=flat&logo=GitHub-Actions&logoColor=white)](https://github.com/features/actions) [![Pytest](https://img.shields.io/badge/Pytest-0A9EDC.svg?style=flat&logo=Pytest&logoColor=white)](https://github.com/Ankvik-Tech-Labs/web3-google-hsmweb3-google-hsm/actions/workflows/tests.yml/badge.svg)                                                                                                                                                                                                                                                                           |
| Type Checking | [![Ruff](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/ruff/main/assets/badge/v2.json)](https://github.com/astral-sh/ruff) [![Checked with mypy](http://www.mypy-lang.org/static/mypy_badge.svg)](http://mypy-lang.org/)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| CI/CD         | [![Release](https://github.com/Ankvik-Tech-Labs/web3-google-hsm/actions/workflows/build.yml/badge.svg)](https://github.com/Ankvik-Tech-Labs/web3-google-hsm/actions/workflows/build.yml) [![Tests](https://github.com/Ankvik-Tech-Labs/web3-google-hsm/actions/workflows/tests.yml/badge.svg)](https://github.com/Ankvik-Tech-Labs/web3-google-hsm/actions/workflows/tests.yml) [![Labeler](https://github.com/Ankvik-Tech-Labs/web3-google-hsm/actions/workflows/labeler.yml/badge.svg)](https://github.com/Ankvik-Tech-Labs/web3-google-hsm/actions/workflows/labeler.yml) [![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white)](https://github.com/pre-commit/pre-commit) [![codecov](https://codecov.io/gh/Ankvik-Tech-Labs/web3-google-hsm/graph/badge.svg?token=CK69S336BL)](https://codecov.io/gh/Ankvik-Tech-Labs/web3-google-hsm) |
| Docs          | [![Docs](https://github.com/Ankvik-Tech-Labs/web3-google-hsm/actions/workflows/documentation.yml/badge.svg)](https://github.com/Ankvik-Tech-Labs/web3-google-hsm/actions/workflows/build.yml)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Package       | [![PyPI - Version](https://img.shields.io/pypi/v/web3-google-hsm.svg)](https://pypi.org/project/web3-google-hsm/) [![PyPI - Python Version](https://img.shields.io/pypi/pyversions/web3-google-hsm)](https://pypi.org/project/web3-google-hsm/) [![PyPI - License](https://img.shields.io/pypi/l/web3-google-hsm)](https://pypi.org/project/web3-google-hsm/)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Meta          | [![GitHub license](https://img.shields.io/github/license/Ankvik-Tech-Labs/web3-google-hsm?&color=1573D5)](https://github.com/Ankvik-Tech-Labs/web3-google-hsm/blob/main/LICENSE) [![GitHub last commit](https://img.shields.io/github/last-commit/Ankvik-Tech-Labs/web3-google-hsm?style=flat&color=1573D5)](https://github.com/Ankvik-Tech-Labs/web3-google-hsmweb3-google-hsm/commits/main) [![GitHub commit activity](https://img.shields.io/github/commit-activity/m/Ankvik-Tech-Labs/web3-google-hsm?style=flat&color=1573D5)](https://github.com/Ankvik-Tech-Labs/web3-google-hsmweb3-google-hsm/graphs/commit-activity) [![GitHub top language](https://img.shields.io/github/languages/top/Ankvik-Tech-Labs/web3-google-hsm?style=flat&color=1573D5)](https://github.com/Ankvik-Tech-Labs/web3-google-hsmweb3-google-hsm)                                                                  |

</div>

---

# 🎯 Description

A Python library for using `Google Cloud HSM` services to sign Ethereum transactions.

# ✨ Features

- 🔒 Cloud HSM integration for secure key management
- 🌐 Support for web3-google-hsm (extensible to other providers)
- 🛡️ Type-safe configuration using Pydantic


# 📦 Installation

- Install using `pip`
```py
pip install web3-google-hsm
```

![demo](media/demo.gif)


# 🛠️ Environment Setup

### 🔑 Google Cloud HSM Key

Make sure you have created a key of type `ec-sign-secp256k1-sha256`
in the Google cloud console. Which will look something like the following

![gcp_hsm_key](media/gcp_hsm_key.png)

## 🔐 Secure Service Account Setup

### Overview

Instead of using developer accounts with broad permissions, we'll create a dedicated service account with minimal permissions specifically for Cloud KMS signing operations.

### Step-by-Step Setup

#### 1. Create Service Account

```bash
# Create a new service account specifically for KMS signing
gcloud iam service-accounts create eth-kms-signer \
    --description="Service account for Ethereum KMS signing operations" \
    --display-name="ETH KMS Signer"
```

#### 2. Create Custom IAM Role

Create a custom role with minimal permissions needed for KMS signing:

```bash
# Create a custom role for KMS signing operations
gcloud iam roles create kms_crypto_signer \
    --project=YOUR_PROJECT_ID \
    --title="KMS Crypto Signer" \
    --description="Minimal permissions for KMS signing operations" \
    --permissions=\
cloudkms.cryptoKeyVersions.useToSign,\
cloudkms.cryptoKeyVersions.viewPublicKey,\
cloudkms.cryptoKeys.get,\
cloudkms.keyRings.get
```

#### 3. Bind Role to Service Account

```bash
# Get the full service account email
export SA_EMAIL="eth-kms-signer@YOUR_PROJECT_ID.iam.gserviceaccount.com"

# Bind the custom role to the service account for specific key
# bash/sh/zsh
gcloud kms keys add-iam-policy-binding YOUR_KEY_NAME \
    --keyring=YOUR_KEYRING_NAME \
    --location=YOUR_LOCATION \
    --member="serviceAccount:${SA_EMAIL}" \
    --role="projects/YOUR_PROJECT_ID/roles/kms_crypto_signer"
```

For `fish` shell
```bash
gcloud kms keys add-iam-policy-binding YOUR_KEY_NAME \
    --keyring=YOUR_KEYRING_NAME \
    --location=YOUR_LOCATION \
    --member="serviceAccount:$SA_EMAIL" \
    --role="projects/YOUR_PROJECT_ID/roles/kms_crypto_signer"
```

#### 4. Create and Download Service Account Key

```bash
# Create and download the key file
# bash/sh/zsh
gcloud iam service-accounts keys create eth-kms-key.json \
    --iam-account="${SA_EMAIL}"

# Convert to environment variable format
export GCP_ADC_CREDENTIALS_STRING=$(cat eth-kms-key.json | jq -c)
```

For `fish` shell
```bash
gcloud iam service-accounts keys create eth-kms-key.json \
    --iam-account="$SA_EMAIL"

# Convert to environment variable format
export GCP_ADC_CREDENTIALS_STRING=(cat eth-kms-key.json | jq -c)
```

### Permissions Explained

The custom role includes only the minimal permissions needed:

- `cloudkms.cryptoKeyVersions.useToSign`: Allow signing operations
- `cloudkms.cryptoKeyVersions.viewPublicKey`: Allow retrieving public key
- `cloudkms.cryptoKeys.get`: Allow reading key metadata
- `cloudkms.keyRings.get`: Allow reading keyring metadata

### Security Best Practices

1. **Principle of Least Privilege**: The service account has only the permissions needed for signing operations
2. **Key Restriction**: Service account only has access to specific KMS keys
3. **Access Scope**: Permissions are limited to specific keyring/key combinations
4. **Key Rotation**: Set up regular rotation for service account keys
5. **Audit Logging**: Enable audit logging for all KMS operations

### Monitoring and Audit

1. Enable detailed audit logging:
```bash
gcloud services enable cloudkms.googleapis.com
gcloud services enable monitoring.googleapis.com
gcloud services enable logging.googleapis.com
```

2. Set up alerts for:
- Failed signing attempts
- High frequency of signing operations
- Access from unexpected IP ranges
- Service account key usage from multiple locations

### ⚠️ Troubleshooting

Common issues and solutions:

1. **Permission Denied**: Check IAM bindings and key permissions
```bash
gcloud kms keys get-iam-policy YOUR_KEY_NAME \
    --keyring=YOUR_KEYRING_NAME \
    --location=YOUR_LOCATION
```

2. **Invalid Key Version**: Ensure key version is active
```bash
gcloud kms keys versions list \
    --key=YOUR_KEY_NAME \
    --keyring=YOUR_KEYRING_NAME \
    --location=YOUR_LOCATION
```

3. **Network Issues**: Check VPC Service Controls if enabled
```bash
gcloud access-context-manager perimeters describe YOUR_PERIMETER
```

The above steps should be enough. But if we want to dig deep please refer to the [google cloud serive account docs page](https://cloud.google.com/iam/docs/keys-create-delete#iam-service-account-keys-create-console).

### 🌟 Required Environment Variables

Before using this library, you need to set up the following environment variables:

```plaintext
GOOGLE_CLOUD_PROJECT=your-project-id
GOOGLE_CLOUD_REGION=us-east1
KEY_RING=eth-keyring
KEY_NAME=eth-key
GOOGLE_APPLICATION_CREDENTIALS=path/to/your/service-account.json
```

### 💻 Bash
```bash
# Add to ~/.bashrc or ~/.bash_profile
export GOOGLE_CLOUD_PROJECT="your-project-id"
export GOOGLE_CLOUD_REGION="us-east1"
export KEY_RING="eth-keyring"
export KEY_NAME="eth-key"
export GOOGLE_APPLICATION_CREDENTIALS="path/to/your/service-account.json"

# Apply changes
source ~/.bashrc  # or source ~/.bash_profile
```

### 🐚 Zsh
```bash
# Add to ~/.zshrc
export GOOGLE_CLOUD_PROJECT="your-project-id"
export GOOGLE_CLOUD_REGION="us-east1"
export KEY_RING="eth-keyring"
export KEY_NAME="eth-key"
export GOOGLE_APPLICATION_CREDENTIALS="path/to/your/service-account.json"

# Apply changes
source ~/.zshrc
```

### 🐟 Fish
```bash
# Add to ~/.config/fish/config.fish
set -x GOOGLE_CLOUD_PROJECT "your-project-id"
set -x GOOGLE_CLOUD_REGION "us-east1"
set -x KEY_RING "eth-keyring"
set -x KEY_NAME "eth-key"
set -x GOOGLE_APPLICATION_CREDENTIALS "path/to/your/service-account.json"
set -x INFURA_KEY "your-infura-key"
set -x WEB3_PROVIDER_URI "https://mainnet.infura.io/v3/$INFURA_KEY"

# Apply changes
source ~/.config/fish/config.fish
```

### 📄 Using .env File
You can also create a `.env` file in your project root:

```plaintext
# .env
GOOGLE_CLOUD_PROJECT=your-project-id
GOOGLE_CLOUD_REGION=us-east1
KEY_RING=eth-keyring
KEY_NAME=eth-key
GOOGLE_APPLICATION_CREDENTIALS=path/to/your/service-account.json
```

Then load it in your Python code:
```python
from dotenv import load_dotenv
load_dotenv()
```

## 📋 Prerequisites

Before you begin, ensure you have:
1. 🔧 Set up your environment variables (see [README.md](https://github.com/Ankvik-Tech-Labs/web3-google-hsm?tab=readme-ov-file#-required-environment-variables))
2. 🐍 Python `3.10` or higher installed
3. 🌐 Access to a `Web3` provider (local or remote) (Optional)


### 📝 Environment Variable Descriptions

- 🌐 `GOOGLE_CLOUD_PROJECT`: Your Google Cloud project ID
- 📍 `GOOGLE_CLOUD_REGION`: The region where your KMS resources are located (e.g., us-east1, europe-west1)
- 🔑 `KEY_RING`: The name of your KMS key ring
- 🗝️ `KEY_NAME`: The name of your KMS key
- 📜 `GOOGLE_APPLICATION_CREDENTIALS`: Path to your Google Cloud service account JSON key file

### ✅ Verifying Setup

You can verify your environment setup with:

```python
from web3_google_hsm.config import BaseConfig

try:
    config = BaseConfig.from_env()
    print("Environment configured successfully!")
    print(f"Project ID: {config.project_id}")
    print(f"Region: {config.location_id}")
except ValueError as e:
    print(f"Configuration error: {e}")
```

# 📚 Usage Guide

## 📚 As A CLI Tool

![cli_tool](media/cli_demo.gif)

### 🔑 Key Generation

Generate a new Ethereum signing key in Google Cloud HSM:

```bash
# Specify explicitly
web3-google-hsm generate \
  --project-id my-project \
  --location us-east1 \
  --keyring eth-keyring \
  --key-id eth-key-1 \
  --retention-days 365
```

```bash
web3-google-hsm generate --project-id hsm-testing-445507 --location nam10 --keyring eth-keyring --key-id cli_key
```

Options:

- `--project-id`: Google Cloud project ID (env: GOOGLE_CLOUD_PROJECT)
- `--location`: Cloud KMS location (env: GOOGLE_CLOUD_REGION)
- `--keyring`: Name of the key ring (env: KEY_RING)
- `--key-id`: ID for the new key (env: KEY_NAME)
- `--retention-days`: Days to retain key versions (default: 365)

Example output:
```
✅ Created Ethereum signing key: projects/my-project/locations/us-east1/keyRings/eth-keyring/cryptoKeys/eth-key-1
🔑 Ethereum address: 0x742d35Cc6634C0532925a3b844Bc454e4438f44e
```

### 📝 Message Signing

Sign a message using your HSM key:

```bash
# Sign a simple message
web3-google-hsm sign "Hello Ethereum!" --account 0x742d35Cc6634C0532925a3b844Bc454e4438f44e

# Sign a hex message
web3-google-hsm sign "0x4d7920686578206d657373616765" --account 0x742d35Cc6634C0532925a3b844Bc454e4438f44e
```

Arguments:
- `message`: The message to sign (text or hex)
- `--account, -a`: Ethereum address of the signing account

Example output:
```
✅ Message signed successfully!
📝 Message: Hello Ethereum!
🔏 Signature: 0x4d7920686578206d657373616765000000000000000000000000000000000000
📊 Components:
  v: 27
  r: 0x1b7e9c7c039d8f4688a743b0c5c0e509209e6f200d956bf7f4e89f5ad330c135
  s: 0x0d27e9c7c039d8f4688a743b0c5c0e509209e6f200d956bf7f4e89f5ad330c13
```

This guide demonstrates how to use the `Google Cloud KMS` Ethereum signer library for message and transaction signing.


## 🚀 Basic Setup

```python
from web3_google_hsm.accounts.gcp_kms_account import GCPKmsAccount

account = GCPKmsAccount()

# Get the Ethereum address derived from your GCP KMS key
print(f"GCP KMS Account address: {account.address}")
```

## 📝 Message Signing

### ✍️ Simple Message Signing
```python
# Sign a message
message = "Hello Ethereum!"
signed_message = account.sign_message(message)

# Access signature components
print(f"R: {signed_message.r.hex()}")
print(f"S: {signed_message.s.hex()}")
print(f"V: {signed_message.v}")
print(f"Full signature: {signed_message.to_hex()}")
```

### ✔️ Message Signature Verification
```python
from eth_account.messages import encode_defunct
from web3 import Web3
# Create message hash
message_hash = encode_defunct(text=message)

# Initialize Web3 and GCP KMS account
w3 = Web3(Web3.HTTPProvider("http://localhost:8545"))

# Verify the signature using web3.py
recovered_address = w3.eth.account.recover_message(
    message_hash,
    vrs=(signed_message.v, signed_message.r, signed_message.s)
)

# Check if signature is valid
is_valid = recovered_address.lower() == account.address.lower()
print(f"Signature valid: {is_valid}")
```

## 💳 Transaction Signing

### 📤 Creating a Transaction
```python
tx = {
    "from": account.address,
    "chain_id": w3.eth.chain_id,
    "nonce": w3.eth.get_transaction_count(account.address),
    "value": w3.to_wei(0.000001, "ether"),
    "data": "0x00",
    "to": "0xa5D3241A1591061F2a4bB69CA0215F66520E67cf",
    "type": 0,
    "gas_limit": 1000000,
    "gas_price": 300000000000,
}

# Convert dict to Transaction object and sign
signed_tx = account.sign_transaction(Transaction.from_dict(tx))
```

### 📡 Sending a Transaction
```python
if signed_tx:
    # Send the transaction
    tx_hash = w3.eth.send_raw_transaction(signed_tx)

    # Wait for transaction receipt
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)

    print(f"Transaction hash: {receipt['transactionHash'].hex()}")
    print(f"From: {receipt['from']}")
    print(f"To: {receipt['to']}")
    print(f"Gas used: {receipt['gasUsed']}")
```

### 🔍 Transaction Signature Verification
```python
# Verify the transaction signature
recovered_address = w3.eth.account.recover_transaction(signed_tx)
is_valid = recovered_address.lower() == account.address.lower()
print(f"Signature valid: {is_valid}")
```

## 🏗️ Working with Local Test Networks

### Funding Your Account (for testing with Anvil/Hardhat)
```python
# Use a test account (Anvil's default funded account)
funded_account = w3.eth.account.from_key(
    "0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80"
)

# Create funding transaction
fund_tx = {
    "from": funded_account.address,
    "to": account.address,
    "value": w3.to_wei(0.1, "ether"),
    "gas": 21000,
    "gasPrice": w3.eth.gas_price,
    "nonce": w3.eth.get_transaction_count(funded_account.address),
    "chainId": w3.eth.chain_id,
}

# Send funding transaction
signed_fund_tx = w3.eth.account.sign_transaction(fund_tx, funded_account.key)
fund_tx_hash = w3.eth.send_raw_transaction(signed_fund_tx.raw_transaction)
fund_receipt = w3.eth.wait_for_transaction_receipt(fund_tx_hash)
```

## 🌐 Using with Different Networks

### 🏠 Local Network (Anvil/Hardhat)
```python
w3 = Web3(Web3.HTTPProvider("http://localhost:8545"))
```

### 🌍 Mainnet (via Infura)
```python
w3 = Web3(Web3.HTTPProvider(f"https://mainnet.infura.io/v3/{INFURA_KEY}"))
```

### 🧪 Testnet (sepolia)
```python
w3 = Web3(Web3.HTTPProvider(f"https://sepolia.infura.io/v3/{INFURA_KEY}"))
```

## ⚠️ Error Handling

```python
from web3_google_hsm.types.ethereum_types import Transaction

try:
    signed_message = account.sign_message("Hello")
except Exception as e:
    print(f"Signing error: {e}")

try:
    signed_tx = account.sign_transaction(Transaction.from_dict(tx))
    if not signed_tx:
        print("Failed to sign transaction")
except Exception as e:
    print(f"Transaction error: {e}")
```

## 🚀 CI/CD Pipeline Integration

### 🔄 Using in GitHub Actions or Other CI/CD Pipelines

For CI/CD environments where you can't use traditional environment variables or service account files, you can pass the credentials directly as a JSON string:

```sh
# save the credentials as a string
export GCP_ADC_CREDENTIALS_STRING=$(cat ~/path/to/creds/eth-kms-key.json | jq -c)
```

```python
from web3_google_hsm.accounts.gcp_kms_account import GCPKmsAccount
from web3_google_hsm.config import BaseConfig
import json

# Create config from environment variables
config = BaseConfig(
    project_id="your-project-id",
    location_id="your-location",
    key_ring_id="your-keyring",
    key_id="your-key-id"
)

# Load credentials from CI/CD secret
credentials = json.loads(os.environ["GCP_ADC_CREDENTIALS_STRING"])

# Initialize account with both config and credentials
account = GCPKmsAccount(config=config, credentials=credentials)

# or Let the class read the values from env variables
account = GCPKmsAccount(credentials=credentials)

```

### 🔒 GitHub Actions Example

```yaml
name: Deploy with HSM Signing

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.10'

      - name: Install dependencies
        run: pip install web3-google-hsm

      - name: Sign and Deploy
        env:
          GOOGLE_CLOUD_PROJECT: ${{ secrets.GCP_PROJECT_ID }}
          GOOGLE_CLOUD_REGION: ${{ secrets.GCP_REGION }}
          KEY_RING: ${{ secrets.GCP_KEYRING }}
          KEY_NAME: ${{ secrets.GCP_KEY_NAME }}
          GCP_ADC_CREDENTIALS_STRING: ${{ secrets.GCP_ADC_CREDENTIALS_STRING }}
        run: |
          python your_deployment_script.py
```

### 📝 Example Deployment Script
```python
import os
import json
from web3_google_hsm.accounts.gcp_kms_account import GCPKmsAccount
from web3_google_hsm.config import BaseConfig
from web3_google_hsm.types.ethereum_types import Transaction

def deploy_contract():
    # Initialize with both config and credentials
    config = BaseConfig.from_env()  # Uses environment variables
    credentials = json.loads(os.environ["GCP_ADC_CREDENTIALS_STRING"])

    account = GCPKmsAccount(config=config, credentials=credentials)

    # Your deployment logic here
    print(f"Deploying from address: {account.address}")

    # Example transaction
    tx = Transaction(
        nonce=0,
        gas_price=2000000000,
        gas_limit=1000000,
        to="0x...",
        value=0,
        data="0x...",
        chain_id=1
    )

    signed_tx = account.sign_transaction(tx)
    # Send transaction...

if __name__ == "__main__":
    deploy_contract()
```

### 🔑 Required Secrets for CI/CD

Set these secrets in your CI/CD environment:

- `GCP_PROJECT_ID`: Your Google Cloud project ID
- `GCP_REGION`: The region where your KMS resources are located
- `GCP_KEYRING`: The name of your KMS key ring
- `GCP_KEY_NAME`: The name of your KMS key
- `GCP_ADC_CREDENTIALS_STRING`: Your service account credentials JSON as a string




---

For more information see the following links.

**📚 Documentation**: <a href="https://ankvik-tech-labs.github.io/web3-google-hsm" target="_blank">https://ankvik-tech-labs.github.io/web3-google-hsm/</a>

**💻 Source Code**: <a href="https://github.com/Ankvik-Tech-Labs/web3-google-hsm" target="_blank">https://github.com/Ankvik-Tech-Labs/web3-google-hsm</a>

---

<details close>
<summary>Development</summary>
<br>


## 👨‍💻 Development

### 🔧 Setup environment

We use [Hatch](https://hatch.pypa.io/latest/install/) to manage the development environment and production build. Ensure it's installed on your system.

### 🧪 Run unit tests

You can run all the tests with:

```bash
hatch run test
```

### ✨ Format the code

Execute the following command to apply linting and check typing:

```bash
hatch run lint
```

### 📦 Publish a new version

You can bump the version, create a commit and associated tag with one command:

```bash
hatch version patch
```

```bash
hatch version minor
```

```bash
hatch version major
```

Your default Git text editor will open so you can add information about the release.

When you push the tag on GitHub, the workflow will automatically publish it on PyPi and a GitHub release will be created as draft.

## 📚 Serve the documentation

You can serve the Mkdocs documentation with:

```bash
hatch run docs-serve
```

It'll automatically watch for changes in your code.


</details>


## 📖 Further Reading

- [Google Cloud KMS Documentation](https://cloud.google.com/kms/docs)
- [Ethereum Key Management Best Practices](https://docs.ethhub.io/using-ethereum/wallets/intro-to-ethereum-wallets/)
- [Web3.py Documentation](https://web3py.readthedocs.io/)


## 📜 License

This project is licensed under the terms of the BSD license.
