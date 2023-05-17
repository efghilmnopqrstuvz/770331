# Machine Learning Project - Customer Segmentation
Contributors:
- Emanuele Carosi 770331
- Matteo Antonazzo 760721
- Gaia Alberti 762671

## Introduction
The project addresses the need of a large corporation seeking to segment its customers in order to run targeted marketing campaigns. The aim is to extract valuable insights from distinct customer segments, enabling the creation of personalized and relevant marketing advertisements. By tailoring the ads to individual preferences and needs, the corporation seeks to enhance customer retention and foster loyalty. In order to accomplish this objective, a variety of clustering techniques were employed to identify the optimal number of segments. An analysis was, then, conducted on each individual cluster to acquire pertinent insights that could be leveraged for marketing purposes.
The dataset consists of 26 variables encompassing information related to orders, customers, sellers, payments, products, and geolocation. However, only three variables were used for customer segmentation: **recency**, representing the time elapsed since a customer's last purchase; **frequency**, indicating the number of times a customer has made a purchase; and **monetary value**, representing the total amount of money spent by a customer.

## Methods
### Variables
Presented below the explanation of each of the 26 variables of the dataset:
- order_id: unique order identifier.
- customer_id: the key to the orders dataset. Each order has a unique customer_id.
- customer_unique_id: the unique identifier of a customer.
- customer_city: customer city name.
- customer_state: customer state.
- order_item_id: sequential number identifying the number of items included in the same order.
- product_id: product unique identifier.
- price: item price.
- freight_value: item freight value item (if an order has more than one item, the freight value is split between items).
- payment_type: method of payment chosen by the customer.
- payment_installments: number of installments chosen by the customer.
- payment_value: transaction value.
- order_status: the order status (delivered, shipped, etc).
- order_purchase_timestamp: purchase timestamp.
- order_approved_at: purchase approval timestamp.
- order_delivered_carrier_date: order posting timestamp. When it was handled by the logistic partner.
- order_delivered_customer_date: actual order delivery date to the customer.
- order_estimated_delivery_date: the estimated delivery date informed to the customer at the purchase moment.
- shipping_limit_date: seller shipping limit date for handling the order over to the logistic partner.
- product_category_name: root product category, in Portuguese.
- product_category_name_english: root category of product, in English .
- product_name_lenght: number of characters extracted from the product name.
- product_description_lenght: number of characters extracted from the product description.
- seller_id: seller unique identifier.
- seller_city: seller city name .
- seller_state: seller state.

### Methods Section Overview
<img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/flowchart/methods.svg" width="800" height="500" />

### Data preparation and cleaning
Starting with data preparation, an initial stage entailed ensuring the absence of missing values, which revealed to be zero; secondly, identifying and eliminating 83 duplicated observations; and lastly, verifying the variable types. In the final step, the date variables were converted from an object type to datetime, facilitating subsequent analysis.
Following that, the dataset underwent a detailed analysis, which proved to be a fundamental step in our project, enabling a thorough understanding of the significance of each individual observation. At a first look, the dataset’s rows might represent unique orders. However, it became quickly evident that each row of the dataset represents an item of a  cart, implying that a unique order would comprise a specific number of rows representing the different items in that specific cart. For sake of clarity and simplicity, a compressed subset of the original dataset was created so that each row would represent a unique order. This grouped dataset was then used for Exploratory Data Analysis. 

In order to group the dataset we had to make specific choices on how to handle it:
- Specific columns were left out since they will not be used for Exploratory Data Analysis purposes
- We sum the “price” of each order items to get the total price of the order
- We sum the “freight value” of each order items to get the total freight value of the order
- All the other columns’ values displayed were kept as they are in the original data frame since the values of each specific column are the same across all the items of the same order.  

After grouping the dataset, we looked more closely at the column “order_status”. This categorical variable displays 4 different classes: canceled, processing, shipped and delivered. A particular pattern must be noted: the orders labeled as “canceled” display the arrival date at the customer’s destination. Taking into consideration this anomaly behavior and the fact that there were only 86 of these observations, we proceeded to drop them. 

### Exploratory data analysis
The following step involved carrying out exploratory analysis on the grouped dataset. We started by analyzing the numerical variables by computing the correlation. It is important to highlight that the variable _“price”_ and the variable _“payment_value”_ have a high correlation of 0.90. This is explained by the fact that the payment value represents the transaction value and therefore, most of the time, it results to be the sum of the _"price"_ and the _"freight value"_. However, there are specific cases in which the payment value was less than the total price of the order (price + freight value).  This pattern was caused by the use of vouchers as a method of payment which covers a portion of the total price, lowering the _“payment value”_ column. No other significant correlations were displayed.
For what concerns the distribution of the different numerical variables it is evident that the dataset is very imbalanced. A focus on the price distribution revealed that the distribution plot has a long right tail, with 50% of observations placed in between 0 and 100. Furthermore, there are several outliers with extreme high prices exceeding 1000. Notably, two orders stand out with values around 8000.

  <img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/img_readme/price_distribution.png" width="800" height="500" />

The subsequent step involved analyzing the distribution of the categorical variables. As for the price, also the customer city distribution and the seller city distribution highlight important imbalances. Most of the customers' orders come from Sao Paulo, while the other customer cities, whose frequency is way smaller, are mostly located near the south-east coast of Brazil. For what concerns the seller city distribution, a similar pattern is displayed. Three main insights about the city distribution of customers and sellers must be pointed out: 
- In the northern regions there are few customers and very few sellers
- In the central regions of Brazil there are few customers and zero sellers
- The majority of both customers and sellers are located in the southern coastal region

  <img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/img_readme/customer_city_map.jpeg" width="380" height="400" /><img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/img_readme/seller_city_map.jpeg" width="380" height="400" />

### Data Preprocessing
In this step, the focus was on creating an additional dataset with rows representing unique customers and with columns representing their _“Recency”_, the _“Frequency”_ and the _“Monetary_value”_.

The _“Recency”_ variable represents the time elapsed since the last order in the dataset. The lower the value, the more recent the order is. In computing this variable we took as a point of reference the _“order_approved_at”_ variable.

The _“Frequency”_ variable indicates the number of times a customer has made a purchase. It is fundamental to highlight that its distribution is highly imbalanced. In fact, 98% of the customers have purchased only one time, 1% has purchased two times and only 8 customers have purchased three times.

The _“Monetary_value”_ variable represents the total amount of money spent by a customer. Also here we can notice how the distribution is skewed to the right since it is a value obtained by summing the values of the highly skewed variable  _“price”_ of the grouped dataset corresponding to the same customer.

This new dataset will be used to cluster the customers in different segments with the final goal of applying different targeted ads strategies.
In order to cluster our customers, the dataset needs to be scaled. The reason for this choice is based on the fact that the three variables do not have the same magnitude. By using not scaled data, certain clustering methods, like the K-means, are not able to form meaningful clusters since the variables do not get weighted equally, leading to a model that might become biased towards the variables with a higher magnitude. For this reason the Standard scaler was applied so that all the variables were displaying a mean of 0 and standard deviation of 1.

### Evaluation metrics
The quality of the clusters can be measured by looking at the intra-cluster and inter-cluster variance. High-quality clusters minimize intra-cluster variance implying high similarity between observations in a single cluster, and maximize inter-cluster variance implying low similarity between observations in different clusters.
In order to decide the number of clusters to form and to evaluate the quality of our clusters, two different evaluation scores were calculated: the **Within Cluster Sum of Squares** (WCSS) and the **Silhouette score**. 
The optimal value of k was chosen based on the results of the **Elbow method**, which plots the number of clusters against WCSS, the chosen metric for clustering quality. The idea behind it is that, beyond a certain point, the improvement in WCSS becomes less significant, resulting in a smooth curve with an elbow shape. The number of clusters corresponding to the elbow point is considered to be the optimal k.
Besides the Elbow method, the Silhouette score was computed to evaluate the quality of clusters. The Silhouette score provides a measure of how closely related data points are within a cluster and how distinct each cluster is from the others. The Silhouette score ranges from -1 to 1, where a higher score indicates better quality clusters.
The selection of the optimal number of clusters was determined through a comprehensive evaluation incorporating both the results of the elbow method and the silhouette score. In the event of divergent outcomes between the two methods, the final decision was made by considering additional factors, including the specific requirements of the business and the ultimate objectives of the project. This approach ensured a well-informed choice that aligned with both the performance of the cluster quality metrics and the broader context of the clustering analysis. 

### Clustering methods adopted
Here below a brief explanation of all the clustering methods adopted in our project:
- **K-means**: It involves randomly initializing K cluster centroids, assigning each data point to the nearest centroid, and updating the centroids by calculating the mean of the assigned points. This process iterates until convergence, resulting in clusters that maximize intra-cluster similarity and minimize inter-cluster similarity. This clustering method needs to define in advance the number of clusters to evaluate.
- **K-means ++**: This method is a clever version of K-means. A key issue with k-means, in fact, is the random initialization of the centroids that often leads to very different clusters for a different iteration of the same model. K-means++, instead of randomly initializing the centroids, assigns the first one to a random point, then following centroids are chosen subsequently, according to a weighted probability function based on their distance from existing centroids. The higher the distance of a data point the higher probabilities to become a centroid. Also, in this case the number of clusters must be set in advance.
- **Hierarchical Clustering (Agglomerative)** : It starts by considering each data point as a separate cluster and then iteratively merges couples of clusters based on their similarity. This process creates a dendrogram, which visualizes the relationships between clusters at different levels. Hierarchical clustering is flexible, as it does not require a predefined number of clusters and can reveal hierarchical structures within the data.
- **DBSCAN (Density-Based Spatial Clustering of Applications with Noise)**: is a clustering algorithm that groups together data points that are close to each other based on a density criterion. Unlike K-means and K-means++, DBSCAN does not require the number of clusters to be specified beforehand and can discover clusters of different shapes and sizes. DBSCAN is also used for detecting anomalies in the dataset. 

## Experimental Design
### Introduction
In this section, a description of all the implemented models will be presented. This section is divided in two subsections: one concerning the experimental process including the iterative process of trials and errors and the second focusing on the definitive models applied for the clustering analysis.

### Models
#### Experimentation process

<img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/flowchart/experimental_design.svg" width="800" height="500" />

The first step involved applying a baseline model: the K-Means clustering technique. As previously stated, when applying K-Means clustering, the optimal number of clusters must be predefined beforehand. This was done by implementing both the Elbow method and the Silhouette. The results of the two metrics did not clearly converge on the same number of clusters. However, it was chosen 3 because it was the number scoring the highest silhouette, 0.500, and 3 was also a suboptimal value for WCSS. 

As a second step, the K-Means++ was applied, trying to understand whether the solution reached by K-means was just a local optimum. With the K-Means++ there is a greater convergence between the Elbow method and the Silhouette score. The elbow method, in fact, pointed to 5 as the second best number of clusters and the silhouette showed 5 as the best score (0.550).
From this score, it is evident how the 5 clusters created with K-Means ++ have a higher quality respect to K-means, since its Silhouette score is 0.07 points higher than the baseline K-Means’ score . 

The third approach in this iterative process involved the application of a new clustering technique: the Agglomerative Hierarchical clustering. As the first step, we plotted the dendrogram by applying the Ward method and the Euclidean distance metric. The Ward method is a criterion for choosing the pair of clusters to merge at each step based on minimizing the within-cluster variance. The vertical height of the dendrogram indicates the dissimilarity between merged clusters measured by the Euclidean distance.  The optimal number of clusters is chosen by identifying the cutoff point used to cut the dendrogram and resulting in clusters that are dissimilar enough to represent separate meaningful groups. In this case, the horizontal cut-off line delineates 4 clusters. The silhouette score for 4 clusters with Agglomerative Hierarchical Clustering is 0.457, and the WCSS of 8268 .
By analyzing the performance metrics of these three methods, it is evident how the best clustering method remains the K-means ++,  which achieves better performance for both Silhouette Score and WCSS. 

As a fourth approach, a density-based clustering method, namely DBSCAN, was incorporated into the analysis.  In order to achieve high quality clusters, an algorithm evaluating the Silhouette score of all the combinations of lambda from 1 to 30 and of epsilon from 1 to 20 was implemented. Then, the combination of parameters yielding the highest  Silhouette score was used. The results, however, were not optimal since the DBSCAN algorithm partitioned the data points into three different clusters, each one containing the points corresponding to the 3 different values of the frequency. 
Consequently, DBSCAN's applicability as a clustering method is limited in scope as it relies solely on frequency-based density considerations. 

#### Final process for clustering

<img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/flowchart/final_process.svg" width="800" height="500" />

The final process adopted was influenced by one key insight detected in the previous sections. Through Exploratory Data Analysis, it became apparent that the distribution of data points was not balanced. The presence of anomalies, exhibiting extreme values,  had a severe impact on the assignment of the centroids with the K-means, causing them to deviate from the optimal position and leading to a decreased accuracy. To address this challenge, the first step involved identifying the anomalies present in the dataset. It was evident that  the DBSCAN algorithm, while unsuitable for clustering purposes, could serve as a valuable tool for anomaly detection; therefore it was applied to the data. For this purpose it was evaluated once again the highest silhouette returned by an algorithm changing the values of epsilon and lambda. In this case though, the range of values of the two parameters were restricted in order to detect more significant anomalies. 
The DBSCAN algorithm identified a total of 43 anomalies within the dataset. These anomalies were subsequently removed from the original dataset and placed into a separate dataset for further analysis.

<img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/img_readme/dbscan_out.png" width="400" height="400" />

Then, K-means++, which was the clustering method with highest silhouette score in the previous analyses, was implemented for the dataset without anomalies and the dataset with only anomalies. Using the Elbow method and the Silhouette score, the optimal number of clusters for both dataset was chosen. 

<img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/img_readme/wcss_with.png" width="400" height="400" /><img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/img_readme/sil_with.png" width="400" height="400" />

<img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/img_readme/wcss_without.png" width="400" height="400" /><img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/img_readme/sil_without.png" width="400" height="400" />

As It is possible to visualize from the plots above, the best number of clusters for the main dataset is 4, while for the anomaly dataset, the Elbow method shows 5 optimal clusters. After the application of the K-Means++ the Silhouette scores were respectively 0.549 and 0.772. The last step of the analysis was to merge back together all the observations and their corresponding segments, resulting in 9 different clusters: 4 of them representing the “pillars” cluster and 5 of them representing the “anomaly” clusters. The clustering with 9 different segments presented a Silhouette Score of 0.545. In this regard it is important to highlight that when considering the entire data points, the best silhouette score totalized was 0.550 for 4 clusters. By partitioning the dataset in two, applying k-means separately and merging the results together, a very similar Silhouette score was achieved with the fundamental difference that now the dataset is segmented in 9 clusters and not anymore in 4.

## Results
In the following section, the clusters previously defined will be investigated and characterized. 
Initial analysis will be focused on the Recency, Frequency, and Monetary values of the cluster centroids. Then, a thorough exploration of each cluster will encompass the examination of additional variables from the original dataset, providing valuable insights into the distinctive characteristics of customers within each cluster. These variables include the item category, customer city, average number of items bought per customer, and the type of payment method.
It is important to note, however, that for the ultimate marketing objective of targeted advertising, it is not convenient for the business to employ distinct marketing strategies for each individual cluster. This is primarily due to the limited number of observations within the anomaly clusters, making them less suitable for tailored marketing approaches.
Consequently, while the nine clusters serve as a foundation for investigating and extracting valuable insights regarding each customer group, the development of diverse marketing strategies will take into consideration the business perspective and incorporate multiple clusters in the same ads strategy in case they share important commonalities, thereby optimizing the overall marketing objective. The first step involves analyzing the first 4 clusters, which are the four pillars of our clustering. Subsequently, an additional analysis will be conducted for the anomaly clusters. 

<img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/img_readme/final_clusters.jpeg" width="400" height="400" />

### CLUSTER 0 MAGENTA -  _"Newcomers"_ 
The cluster _‘0’_ is composed of 5318 customers, representing 47,5% of the total. These users have made just one purchase in the last recent period, spending a small amount of money. The low monetary amount spent by the customers of this cluster is consistent with the average items bought per customer, which is on average 1. Regarding the type of items bought, this feature highlights the fact that this customer segment purchases standard and everyday items, with just some exceptions for watches gifts. It can be concluded that this customer segment represents the _“Newcomers”_: indicating their recent initiation into the customer journey. These individuals have made their first purchase, typically consisting of a single item of relatively low cost. Their initial purchase may have happened by chance or driven by a genuine interest in the product or brand. Irrespective of the specific circumstances surrounding their initial purchase, it is crucial for businesses to prioritize nurturing these newcomers and transforming them from one-time buyers into loyal customers. The marketing efforts should be focused on retaining the newcomers and not let them slip away since it is always cheaper to retain existing customers than acquiring new ones.

### CLUSTER 1 LIGHT BLUE - _“To Revive Customers”_ 
This cluster displays very similar patterns with the _“Newcomers”_ clusters , with one main difference regarding the recency variable. As for the previous cluster, this cluster is composed of 5286 customers, displaying a similar density. The predominant categories of items purchased remain consistent, with four out of five most purchased categories being the same. Additionally, the average number of items per customer is similar, hovering around 1 item per customer, as observed for the Newcomers.
The only difference between these clusters, as previously mentioned, is the recency: on average, the last and only purchase was made more than 3 months ago (109 days). This timeframe is nearly three times longer than that of the Newcomers cluster, where customers made their last and only purchase approximately one month ago.
It could be assumed that the purchase made by customers of these cluster was either by chance or resulted in a negative experience, leading them to refrain from further purchases
Consequently, it becomes essential to devise a targeted marketing strategy focused on customer retention to re-engage and incentivize them to make subsequent purchases. Implementing measures to win back these customers or providing attractive incentives to encourage repeat purchases can be key factors in reviving their interest and re-establishing a positive customer relationship.
For this reason, this cluster can be defined as _“To revive customers”_.

### CLUSTER 2 PINK - _“Promising Loyal Customers”_ 
Cluster _'2'_ comprises customers who have made two purchases from the company, with an average elapsed time of two months since their last purchase. The average total monetary value per customer within this cluster amounts to 211, aligning with the previous two clusters that exhibited monetary values ranging from 110 to 120, the half since they have just made a purchase once. The relatively modest monetary value within this cluster is consistent with the average number of items bought per customer, which fluctuates around 1 and 2. Additionally, the category preferences for items purchased in this cluster closely resemble those observed in the previous clusters.
From the insights collected so far, it is possible to infer that this cluster is composed of exactly the same customers of the _“Newcomers”_ cluster, but with a huge difference: the customers of this cluster came to buy on our site again. This information is critical as it suggests a potential loyal behavior exhibited by these customers. Consequently, the marketing efforts targeting this cluster should prioritize customer retention and fostering increased loyalty within this segment. By implementing strategies aimed at nurturing and enhancing the loyalty of these customers, the company can maximize their long-term value and strengthen the customer relationship.

### CLUSTER 3 LIGHT GREEN - _"Potentially Profitable Customers"_ 
This cluster is composed of 418 customers who have bought just once, on average more than two months ago. What makes this cluster valuable is that on average each customer has spent 1001 almost 9 times larger than the amount spent by the _“Newcomers”_, _“To revive customers”_ and _“Promising Loyal Customers”_. The large monetary value amount is consistent to the fact that each customer ordered an average number of items fluctuating between 2 and 3. Moreover, these customers not only bought more products, but they purchased higher priced items. This can be confirmed by looking at the pie plot which displays a share of 12,6% for watches_gift whose price is 200. Moreover, among the _“other categories”_ section, we find in smaller percentage other high priced items such us musical instruments, computers, construction tools and small appliances, increasing the overall average price. 
Overall, the customers of this cluster can be considered as _“Potentially Profitable Customers”_. The fact that the average number of items purchased fluctuates between 2 and 3, reveals that the customers were not just randomly on the website, but they intentionally chose to commit to the brand’s products. Therefore, retaining these customers is crucial due to their significant contribution to the company's revenue. 

### CLUSTER 4 DARK GREEN - _“Loyal Customers”_ 
Cluster 4 is composed of a small subset of only 8 customers, who stand out as the most loyal customers of the shops. These individuals have demonstrated their commitment by making three purchases, thereby earning the distinction of being the _“Loyal customers”_. On average, they have spent approximately 139 euros per order, slightly surpassing the monetary value observed in the first three clusters ( _“Newcomers”_, _“To revive customers”_ and _“Promising Loyal Customers”_), although the difference is not significantly notable. Therefore, even though they have made frequent purchases, they are not valuable customers in terms of the total monetary value. However, these customers are important to the company, as they consistently contribute to its revenue stream through regular purchases.
Targeting efforts should be directed towards the _“Newcomers”_ (cluster 0) and _“Promising Loyal Customers”_ (cluster 2), with the ultimate objective of converting them into long-term Loyal Customers. By incentivizing and encouraging these customers to increase their frequency of purchases and emulate the loyalty displayed by best friend customers, the company can maximize customer retention and strengthen its overall financial performance.

### CLUSTER 5 PURPLE -  _“Stranded Customers”_ 
This cluster, composed of just 6 customers, comprises those users that have made one purchase more than one year ago ( Recency centroid = 377). They have spent on average 182 euros per order, an amount higher than the previous clusters with the only exception of cluster 3 _“Potentially Profitable Customers”_. The higher amount of money spent can be explained by the fact that on average each customer has purchased almost 3 items. They represent the lost customers of the company that could have represented a potential source of revenue, however, they have not been retained successfully.  

### CLUSTER 6 RED -  _“Elite Customers”_
Cluster 6 is composed of 18 customers that have spent a considerable amount of money in their first and only purchase. In fact, the average total monetary value spent by each customer is  3320 euros, a way larger amount with respect to all the previous clusters. The higher monetary amount can be attributed to two key factors. Firstly, the average number of items per order within this cluster is 5, increasing the overall monetary value. Secondly, the specific categories of items purchased play a crucial role. Notably, the inclusion of high-value items such as computers, which have an average cost of 800 euros, substantially contributes to the overall amount spent by customers within this cluster.
Cluster 6 represents the _“Elite Customer”_, they have purchased only once but still invested a considerable amount of money. The company should be very keen in retaining them since they provide great financial inflows.

### CLUSTER 7 BLUE - _“Promising Profitable Loyal Customer”_ 
Clusters 7 comprises 9 customers that have purchased twice from the company. The insights of this cluster are very consistent with the ones of cluster 2: _“Promising Loyal customer”_, with just an important difference for the monetary value, which is about 5 times higher than the one of cluster 2. For this reason we can define this segment as _“Promising Loyal Customer”_. The company should put effort in retaining them and strengthen the customer relationship in order not to lose them. Since they are a cluster anomaly and they share similar characteristics with cluster 2, it could be very effective for the company to apply the same tailored marketing campaign aiming to increase and stimulate the loyalty of these customer segments.

### CLUSTER 8 ORANGE -  _“Rare Stellar Customers”_ 
Cluster 8 is composed of only two customers that have made just one purchase but have spent a huge amount of money with an average of 7660 euros, the highest amount among all the clusters. This can be explained by the fact that these two customers have purchased on average 4 items that belong to very expensive categories like _“fixed_telephony”_. 
Therefore, these two individuals epitomize a unique and highly valuable customer segment, characterized by their remarkable purchasing power. Attracting and retaining such customers is a difficult yet fruitful undertaking, given that their loyalty provide huge money inflow. Since it is a cluster of just two observations and since they display similar characteristics of the _“Potentially Profitable Customers”_ and  _“Elite customers”_, apart the magnitude of monetary value, the same targeting ads techniques could be applied to incentivize them to return and buy again from the shop. 
To conclude it is important to note, that all these 9 clusters did not show any relevant patterns with regard to the distribution of the customer city and the type of payment that characterized each cluster. 

### Cluster Centroids Overview

<img src="https://github.com/efghilmnopqrstuvz/770331/blob/main/img_readme/cluster_rfm.png" width="800" height="800" />

## Conclusions
In conclusion the final outcome was obtained in this way:
- we detect anomalies with dbscan
- we apply k-means++ for two different datasets: one composed by anomalies and the other without anomalies
- we merged the results obtaining 9 different clusters

We proceed as stated before, because k-means outcome is very sensible to outliers, and applying it without anomalies shifted significatively the centroids. Then we decided to cluster also the anomalies in order not to lose information. This clusters, in fact, could anticipate future behaviour of customers and in the future the may be more numerous.
The identified nine clusters provide a valuable foundation for understanding and extracting insights related to each customer group. However, it is crucial to develop diverse marketing strategies that align with the business objectives and consider the shared characteristics among clusters. By incorporating multiple clusters into the same advertising strategy when they exhibit significant commonalities, the overall marketing objective can be optimized effectively. 
To conclude, it is important to remark that the specifications of each targeted advertising strategy will be decided by the Marketing team guided by the analyses carried out and the insights obtained in this project. 


