# Code to build chatbot application using VertexAI and PaLM2 model

# Development
1. Setup Free Tier GCP project in console.cloud.google.com
    - create new project
    - enable VertexAI API
    - authenticate in GCP: `gcloud auth application-default login`
    - set project id: `gcloud config set project PROJECT_ID`
    - enable apis:
        ```
        /bin/bash
        gcloud services enable cloudbuild.googleapis.com cloudfunctions.googleapis.com run.googleapis.com logging.googleapis.com storage-component.googleapis.com aiplatform.googleapis.com
        ```
    - export env variables:
        ```
        export GCP_PROJECT='PROJECT_ID'
        export GCP_REGION='REGION'
        export GOOGLE_APPLICATION_CREDENTIALS=/path/to/.json
        ```

2. Setup local python environment
    - `python -m venv .venv`
    - `source .venv/bin/activate`
    - `pip install --upgrade pip`

3. Example: https://github.com/GoogleCloudPlatform/generative-ai/tree/main/language/sample-apps/chat-streamlit

# How to run
1. locally:
    ```
    /bin/bash
    streamlit run app.py --server.port=8080 --server.address=0.0.0.0
    ```

2. in GCP
- build with Cloud Build
    ```
    /bin/bash
    export AR_REPO='<REPLACE_WITH_YOUR_AR_REPO_NAME>'  # Name of the artifact registry
    export SERVICE_NAME='chat-streamlit-app' # Name of our Application and Cloud Run service
    gcloud artifacts repositories create "$AR_REPO" --location="$GCP_REGION" --repository-format=Docker
    gcloud auth configure-docker "$GCP_REGION-docker.pkg.dev"
    gcloud builds submit --tag "$GCP_REGION-docker.pkg.dev/$GCP_PROJECT/$AR_REPO/$SERVICE_NAME"
    ```
- run with Cloud Run
    ```
    /bin/bash
    gcloud run deploy "$SERVICE_NAME" \
    --port=8080 \
    --image="$GCP_REGION-docker.pkg.dev/$GCP_PROJECT/$AR_REPO/$SERVICE_NAME" \
    --allow-unauthenticated \
    --region=$GCP_REGION \
    --platform=managed  \
    --project=$GCP_PROJECT \
    --set-env-vars=GCP_PROJECT=$GCP_PROJECT,GCP_REGION=$GCP_REGION
    ```

