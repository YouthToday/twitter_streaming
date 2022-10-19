# twitter_streaming
Before start the whole process, you should already create a project on GCP and a cloud storage bucket.
1. Create a cluster, using the command below.
```bash
gcloud beta dataproc clusters create {cluster-name} \
--region={project-region} \
--optional-components=ANACONDA,JUPYTER \
--image-version=1.4 \
--enable-component-gateway \
--metadata 'PIP_PACKAGES=requests_oauthlib google-cloud-bigquery tweepy' \
--metadata gcs-connector-version=1.9.16 \
--metadata bigquery-connector-version=1.0.0 \
--project {project-ID} \
--bucket {buckect-name} \
--initialization-actions=gs://dataproc-initialization-actions/python/pip-install.sh,gs://dataproc-initialization-actions/connectors/connectors.sh \
--single-node
```
