# twitter_streaming
Before starting the whole process, you should already create a project on GCP and a cloud storage bucket.
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
2. Run twitterHTTPClient.ipynb
3. After Step-2, run sparkStreaming.ipynb. Make sure you open two windows to have both running.
4. Find the generated stream_data.csv file in your cluster's VM instance. The file is in the directory "/". Use gsutil to upload the file to gs bucket.
```bash
gsutil -cp stream_data.csv {your_gs_bucket_path}
```
5. Run LDA.ipynb to do the machine learning clustering.
