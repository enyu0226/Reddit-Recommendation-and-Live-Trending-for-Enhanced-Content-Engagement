## Reddit Recommendation and Live Trending for Enhanced Content Engagement

A scalable real-time big data pipleline using Kafka, PySpark, Storm, Parquet, S3 and Cassandra.

1. Create real-time trends on reddit
2. Subreddit Recommendation System at scale
3. Finding User posts

## Data Pipeline

The historical Reddit dataset is drawn from October 2007-December 2015. The entire repo is <a href="http://couch.whatbox.ca:36975/reddit/submissions/monthly/"> here</a>. The data is a monthly dump of bz2 file format. The file is uploaded to S3 on AWS and uncompressed using an automated bash script.

To ensure fast processing on Spark, the file from S3 was processed and compressed into Parquet, a columnar store that helps us store the JSON data with its schema on HDFS, thereby leveraging the parallel processing and resiliency nature of the HDFS. The original file has a size of 1 TB which was compressed into Parquet with a size of 200 GB, translating to huge space saving while also supporting running queries at rate of 3x faster on Spark.

Spark is used for batch processing, including querying and downloading all the posts of a user as a json file which may then be used to carry out user profiling. The user can also receives recommendation of reddit posts based on user interaction subreddit posts. The idea is to connect users who have interacted with each other by a directed graph and grouping them by the subreddits. Once this is done, we can then compute the indegree and outdegree of every node in the clusters of subreddits. This allows us to track the most influential and active users within a particular subreddits. This would bring value to the platform as it can facilitate maximization of content engagement. The recommendation achieved by looking up at the subreddits that the influential users of a subreddit of your liking are active on and then suggesting these subreddits as recommendations.

The recommendation is implemented by using both collaborative filter algorithm (ALS) and the user network approach by using SQL query to join user with others whom the users have interacted with. Both approach generated similar recommendations, although the user graph model performs much faster in terms compute time of about 5 mins for computing recommendations as compared to the ALS approach, which took 20 iterations and more than 13 hours.

Real time processing was done using Storm and Kafka was used as the publish-subscribe broker. This is used to generate trending posts.
