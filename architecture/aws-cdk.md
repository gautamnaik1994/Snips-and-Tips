# AWS CDK

### Git Action Workflow for automatic deployment

```yaml
name: Deploy CDK Stack

on:
  push:
    branches:
      - main  # Adjust the branch as needed

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    # 1. Checkout the repository
    - name: Checkout code
      uses: actions/checkout@v3

    # 2. Set up Node.js environment
    - name: Set up Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18' # Specify your Node.js version

    # 3. Install AWS CDK
    - name: Install AWS CDK
      run: npm install -g aws-cdk

    # 4. Handle .env file (non-sensitive)
    # If .env is in the repository and not sensitive, uncomment this step.
    # Ensure `.env` is added to `.gitignore` if sensitive.
    - name: Copy .env (non-sensitive)
      run: cp .env.example .env

    # 5. Handle .env file (encrypted)
    # If you encrypted .env using GPG, decrypt it during the workflow.
    - name: Decrypt .env (if encrypted)
      env:
        GPG_PASSPHRASE: ${{ secrets.GPG_PASSPHRASE }}  # Set this secret in GitHub
      run: |
        echo "$GPG_PASSPHRASE" | gpg --batch --yes --passphrase-fd 0 --output .env --decrypt .env.gpg

    # 6. Load .env variables into the environment
    - name: Load .env variables
      run: |
        if [ -f .env ]; then
          export $(cat .env | xargs)
        else
          echo ".env file not found, skipping environment variable loading."
        fi

    # 7. Configure AWS credentials
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v3
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1 # Replace with your AWS region

    # 8. Install dependencies for the CDK app
    - name: Install dependencies
      run: npm install
      working-directory: cdk-app # Adjust if your CDK app is in a subdirectory

    # 9. Synthesize the CloudFormation template
    - name: CDK Synth
      run: cdk synth
      working-directory: cdk-app # Adjust if your CDK app is in a subdirectory

    # 10. Deploy the CDK stack
    - name: CDK Deploy
      run: cdk deploy --require-approval never
      working-directory: cdk-app # Adjust if your CDK app is in a subdirectory

```
