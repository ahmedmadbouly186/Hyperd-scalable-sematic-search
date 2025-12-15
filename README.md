Hyperd scalable indexer  
Try to test it on Google Collab link
Integrating between  LSH and IVFFLAT with the assumption that the indexer will be built on all the data, if there is new data then the indexer must rebuild with the ability to paralyzation the rebuild and use disk to best optimize the memory usage. This indexer is designed to have high scalability and distinct time efficiency. It also supports a very good speed-recall tradeoff.
The Indexing architecture of the two layers is as follows: 


The indexing strategy used is based on combining local sensitive hashing (LSH) and K-means.
clustering.
● Given the input vectors, the LSH uses 3 vectors to divide the data into 8 subdomains:
○ The LSH calculates cosine similarity for each vector in the input data with the 3
vectors of the LSH.
○ A threshold is applied to each of the scores calculated from the three vectors so
that it either represents a 1 or a 0.
○ The result from the previous step is a 3-bit binary number that represents the
index of the bucket that the current vector should be applied to.
○ The process is repeated for each vector in the data until all the vectors are in the
correct subdomain bucket.
● For each of the buckets, we then apply k-means to divide the vectors in the bucket into
smaller clusters.
○ We decided that the number of clusters in each bucket should be 1000.
○ We create a KMeans object from the Sklearn library with the given number of
clusters, and then the function fit is called.
○ The output of the previous step is the centroids of the clusters in the bucket and
all the records in each cluster. (1000 centroid)
○ The centroids and cluster records are then saved into files to be used later in the
retrieval process.

 Retrieval architecture 
The retrieval process simply applies the same steps of indexing for a single query to retain the top k similar vectors. Given a query (vector) to search for: 
● First we apply the same hash function on the given vector by calculating the cosine similarity between the query and the same 3 vectors used in the indexing of the LSH. 
● After applying the threshold on the returned scores we get the 3-bit binary number that indicates the query hashes to which bucket. 
● We applied a modification so that the retrieval process is more accurate so that we don’t just search in the given bucket but also in neighboring buckets. 
● This means that the query is in the same subdomain as the vectors in the same bucket 
● Then we read the saved centroids of each bucket from the indexer and calculate the cosine similarity to get the target clusters to search for the query in. 
● We also apply the same modification here to search for the query in multiple clusters as well.
 ● After retrieving the target clusters, we then apply linear search using cosine similarity and save the scores to a list which is then sorted. 
● The previous step is applied to all the target clusters and buckets and the list is extended to include the top k similar vectors from each one. 
● Finally the final list is sorted and the top k similar vectors are returned.

Results : 
LSH buckets =8, we look into ‘nprobes for LSH’=6		‘remove part of the data’
IVFFLAT clusters =1000 , nprobes =150

LSH buckets =8, we look into ‘nprobes for LSH’=6		‘remove part of the data’
IVFFLAT clusters =1000 , nprobes =50

-40 means we are far by 40 ids from the best answer, it doesn't mean much when number of records  is 20M
