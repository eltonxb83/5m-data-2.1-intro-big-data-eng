# Assignment

## Brief

Write the Python codes for the following questions.

## Instructions

Paste the answer as Python in the answer code section below each question.

### Question 1

Question: From the `movies` collection, return the documents with the `plot` that starts with `"war"` in acending order of released date, print only title, plot and released fields. Limit the result to 5.

Answer:

```python
movies = db.movies
for m in movies.find(
	{"plot":{"$regex": "^war"}},
	{"title": 1, "plot": 1, "released": 1, "_id":0}
).sort("released", pymongo.ASCENDING).limit(5):
	print(f"Title: {m['title']}\nPlot: {m['plot']}\nRleased: {m['released']}")
  
```

### Question 2

Question: Group by `rated` and count the number of movies in each.

Answer:

```python

group_rated = {"$group":{
	"_id" : "$rated",
	"movie_count": {"$sum": 1}
	}}

results = movies.aggregate([group_rated])
for r in results:
	print(f"Rated: {r['_id']} Movie Count: {r['movie_count']}")

```

### Question 3

Question: Count the number of movies with 3 comments or more.

Answer:

```python

stage_lookup_comments = {
	"$lookup" :{
		"from" : "comments",
		"localField" : "_id",
		"foreignField" : "movie_id",
		"as" : "related_comments"
		}
	}

stage_comment_count = {
	"$addFields" :{ 
		"comment_count" :{
			"$size" : "$related_comments"}
	}
}

stage_match_comment_number ={
	"$match" :{
		"comment_count" : { "$gte" : 3}
	}
}

pipeline = [
	stage_lookup_comments,
	stage_comment_count,
	stage_match_comment_number
	]

results = movies.aggregate(pipeline)

for m in results:
	print(m["title"])
	print(f"Comment_count: {m['comment_count']}")
	print(m["related_comments"]) 
		

```

## Submission

- Submit the URL of the GitHub Repository that contains your work to NTU black board.
- Should you reference the work of your classmate(s) or online resources, give them credit by adding either the name of your classmate or URL.
