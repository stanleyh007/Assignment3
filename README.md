# Assignment3

Use jupyter notebook to open the file.


Make sure that you have MongoDB ready on Docker and already had database ready on Docker.


For make to application run, you need python and some imports like pymongo and pprint.


## Questions
### How many Twitter users are in the database?

Query: db.tweets.aggregate([{$group: {_id: "$user" }}, {$count: "count"}])


Output: {"count" : 659774}

### Which Twitter users link the most to other Twitter users? (Provide the top ten.)

Query: db.tweets.aggregate([{$match:{text:/@\w+/}},{$group:{_id: "$user",user: {$first: "$user"},count: { $sum:1 }}},{$sort: { count: -1 }},{$limit: 10},{$project: {_id:0,user:1,count:1}}])

Output: [{'_id': 'lost_dog', 'count': 549},
         {'_id': 'tweetpet', 'count': 310},
         {'_id': 'VioletsCRUK', 'count': 251},
         {'_id': 'what_bugs_u', 'count': 246},
         {'_id': 'tsarnick', 'count': 245},
         {'_id': 'SallytheShizzle', 'count': 229},
         {'_id': 'mcraddictal', 'count': 217},
         {'_id': 'Karen230683', 'count': 216},
         {'_id': 'keza34', 'count': 211},
         {'_id': 'TraceyHewins', 'count': 202}]


### Who is/are the most mentioned Twitter users? (Provide the top five.)

Query: db.tweets.aggregate([{$match: { text: /@\w+/}},{$project:{user:1,text: { $split: ["$text", " "] }}},{$unwind: "$text"},{$match: { text: /@\w+/}},{$project:{user:1,text: { $split: ["$text", "@"] }}},{$project:{user:1,text: { $arrayElemAt: [ "$text", 1 ] }}},{_id: "$text",user: {$first: "$text"},count: { $sum:1 }}},{$sort: { count: -1 }},{$limit: 5},{$project:{_id:0,user:1,count:1}}])


Output: [{'_id': 'mileycyrus', 'count': 4325},
         {'_id': 'tommcfly', 'count': 3841},
         {'_id': 'ddlovato', 'count': 3356},
         {'_id': 'Jonasbrothers', 'count': 1267},
         {'_id': 'DavidArchie', 'count': 1227},

### Who are the most active Twitter users (top ten)?

Query: db.tweets.aggregate([{$group:{_id: "$user",count: { $sum:1 }}},{$sort: { count: -1 }},{ $limit: 10}])


Output: [{'_id': 'lost_dog', 'count': 549},
         {'_id': 'webwoke', 'count': 345},
         {'_id': 'tweetpet', 'count': 310},
         {'_id': 'SallytheShizzle', 'count': 281},
         {'_id': 'VioletsCRUK', 'count': 279},
         {'_id': 'mcraddictal', 'count': 276},
         {'_id': 'tsarnick', 'count': 248},
         {'_id': 'what_bugs_u', 'count': 246},
         {'_id': 'Karen230683', 'count': 238},
         {'_id': 'DarkPiano', 'count': 236}]

### Who are the five most grumpy (most negative tweets) and the most happy (most positive tweets)?

Query_1: db.tweets.aggregate([{$match: { polarity: 0 }},{$group:{_id: "$user",user: {$first: "$user"},count: { $sum:1 }}},{$sort: { count: -1}},{ $project: {_id:0,user:1,count:1}},{$limit: 5}])


Output_1: [{'count': 549, 'user': 'lost_dog'},
           {'count': 310, 'user': 'tweetpet'},
           {'count': 264, 'user': 'webwoke'},
           {'count': 210, 'user': 'mcraddictal'},
           {'count': 210, 'user': 'wowlew'}]
           

Query_2: db.tweets.aggregate([{ $match: { polarity: 4 } },{$group:{_id: "$user",user: {$first: "$user"},count: { $sum:1 }}},{$sort: { count: -1 }},{$limit: 5},{$project:{_id:0,user:1,count:1}}])


Output2: [{'count': 246, 'user': 'what_bugs_u'}]
