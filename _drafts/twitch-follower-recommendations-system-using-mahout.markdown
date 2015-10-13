---
layout: post
title:  "Twitch Follower Recomendations System using Mahout"
date:   2015-10-07 15:16:48
categories: mahout recommendations
---

Mahout is a great application for creating recommendations, classification, and clustering (neighborhoods). This makes it a great tool for finding creating recommendations for Twitch streams.

Twitch API allows you to get the last 1600 follows for every channel. We will be using the list of streamers currently live to create a list to index for follows. We will need to get the channel id, and meta information on the channel for creating links and showing profile images.

	https://api.twitch.tv/kraken/streams

After we get a list of live streams we will need to query all their follows, up to 1600. This list can go in both directions, with the `direction=asc` or `direction=desc` values. This allows us to get a list of 3200 follows per stream. You will need to check for duplicate follows if their follows list is < 3200.

At this point, you should have the # of live streams which ranges from 18,000 to 22,000 plus all their follows. You can directly integrate your data store (MongoDB, Cassandra, MySQL) or use a file base (CSV, TSV) to create your data model. In this example I will be using CSV to show how the data should be organized.

The format for the CSV should be

	channel, user
	1382883, 439837534
	33843, 49304453

{% highlight java %}
DataModel dm
	= new FileDataModel(new File("data/follows.csv"));
{% endhighlight %}

Recommendations require DataModel, Similarity, and Neighborhoods.

The list of built-in similarity algorithms with documentation in the code. The implementations are quite simple, so making your own algorithms is pretty easy.

[../apache/mahout/cf/taste/impl/similarity](https://github.com/apache/mahout/tree/mahout-0.10.x/mr/src/main/java/org/apache/mahout/cf/taste/impl/similarity)

* [LogLikelihoodSimularity][likelihood]
* [PearsonCorrelationSimilarity][pearson]
* [TanimotoCoefficientSimilarity][tanimoto]

{% highlight java %}
UserSimilarity sim = new LogLikelihoodSimilarity(dm);
{% endhighlight %}

Next we will be using neighborhoods to get nearest user to given user using the simularity. Increasing the nearest user base can increase simular items recommended at the next step.

{% highlight java %}
UserNeighborhood nh
	= new NearestNUserNeighborhood(100, sim, dm);
{% endhighlight %}

Next we create our recommendations using the data model, neighborhood, and simularities.

{% highlight java %}
GenericUserBasedRecommender recommender
	= new GenericUserBasedRecommender(dm, nh, sim);
{% endhighlight %}

At this point you'll want to save a number of recommendations per user into a database or file. I'll be using MongoDB to store the results, but you can write to another file or HDFS for access with other processing systems for more analysis.

{% highlight java %}
MongoClient client = new MongoClient();
MongoDatabase database = client.getDatabase("twitch");
MongoCollection<Document> collection = database.getCollection("twitch_recommendations");

// Loop through each of the users in the data model
for (LongPrimitiveIterator users = dm.getUserIDs(); users.hasNext();) {
	long userId = users.nextLong();

	// Get 25 recommendations for the userId
	List<RecommendedItem> recommendations
		= recommender.recommend(userId, 25);

	List<Long> list = new ArrayList<Long>();

	// Add each recommendation to a list to save to mongo
	for(RecommendedItem recommendation : recommendations) {
		list.add(recommendation.getItemID());
	}

	// Save the recommended user list
	Document document
		= new Document("user", userId).append("recommended", list);
	collection.insertOne(document);
}
{% endhighlight %}

With the scalibility of Mahout with using a Hadoop cluster, you can process large amounts of data and create recommendations. New intergrations with spark to do user and item similarity allow for realtime recommendations based on user preferences.

[likelihood]: https://github.com/apache/mahout/blob/mahout-0.10.x/mr/src/main/java/org/apache/mahout/cf/taste/impl/similarity/LogLikelihoodSimilarity.java
[tanimoto]: https://github.com/apache/mahout/blob/mahout-0.10.x/mr/src/main/java/org/apache/mahout/cf/taste/impl/similarity/TanimotoCoefficientSimilarity.java
[pearson]: https://github.com/apache/mahout/blob/mahout-0.10.x/mr/src/main/java/org/apache/mahout/cf/taste/impl/similarity/PearsonCorrelationSimilarity.java