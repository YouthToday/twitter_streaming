# twitter_streaming

This project is a streaming analysis process. The architecture is as follows. A socket requests data from Twitter API and sends data to the Spark streaming
process. Spark reads real-time data to do analysis. It also saves temp streaming results to Google Storage. After the streaming process terminates, Spark reads the final data from Google Storage and saves it to BigQuery, and then cleans the data in Storage. Finally, LDA is used to classify the data in the streaming.
![image](https://user-images.githubusercontent.com/49230870/198406606-2cb80722-d2cc-41ab-8335-9a675a65a745.png)

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

Calculate the accumulated hashtags count sum for 600 seconds and sort it by descending order of the count. Hashtag usually starts with "#" followed by a series of alphanumeric. Hashtags are case insensitive.

Filter the chosen 5 words (you can pick up any five words as your interest) and calculate the appearance frequency of them in 60 seconds for every 60 seconds (no overlap). For example, word ‘data’ appears 30 times from T1 to T2 (T2-T1=60s), and 20 times from T2 to T3(T3-T2=60s), then the output DStream should have inner structure (RDD@T2(‘data’, 30, T2), RDD@T3(‘data’,20, T3), RDD@T4 …). Collect the data for 600 seconds. The words are: 'data', 'spark', 'ai', 'movie', 'good'. Ignore case sensitive when filtering the data.

Save results to google BigQuery.

4. Find the generated stream_data.csv file in your cluster's VM instance. The file is in the directory "/". Use gsutil to upload the file to gs bucket.
```bash
gsutil -cp stream_data.csv {your_gs_bucket_path}
```
5. Run LDA.ipynb to do the machine learning clustering.
