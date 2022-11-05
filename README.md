# student-database-new
student database
import pymongo
import json
from pymongo import MongoClient, InsertOne

client = pymongo.MongoClient("mongodb://localhost:27017")
db = client["New_Result"]
collection = db["student"]
collection1=db["passedstudent"]
collection2=db["failedstudent"]
collection3=db["average_student_in_exam"]
newdata= []

with open(r'C:\Users\Lenovo\Desktop\students (1).json') as f:
    for jsonObj in f:
        myDict = json.loads(jsonObj)
        newdata.append(InsertOne(myDict))

result = collection12.bulk_write(newdata)

#1)max of all the three type of categories in separate
from pymongo import MongoClient

client = MongoClient('mongodb://localhost:27017/')
result = client['New_Result']['student'].aggregate([
    {
        '$unwind': {
            'path': '$scores'
        }
    }, {
        '$group': {
            '_id': '$scores.type', 
            'maximun': {
                '$max': '$scores.score'
            }
        }
    }
])
for i in result:
    print(i)

#2)students who scored pass mark in exam only
myquery={"scores.0.score":{"$gte":40,"$lte":48}}
mydoc=mycollection.find(myquery)
for i in mydoc:
    print(i)

#3)student who scoreced pass and fail in each categories

#student who scored pass mark and assigned them as pass
mydocument = collection.update_many({'scores.0.score':{'$gte':40}},{"$set":{"examResult":"pass"}})
mydocument = collection.update_many({'scores.1.score':{'$gte':40}},{"$set":{"quizResult":"pass"}})
mydocument = collection.update_many({'scores.2.score':{'$gte':40}},{"$set":{"homeworkResult":"pass"}})

#student who scored fail mark and assigned them as fail
mydocument = collection.update_many({'scores.0.score':{'$lt':40}},{"$set":{"examResult":"fail"}})
mydocument = collection.update_many({'scores.1.score':{'$lt':40}},{"$set":{"quizResult":"fail"}})
mydocument = collection.update_many({'scores.2.score':{'$lt':40}},{"$set":{"homeworkResult":"fail"}})


for i in collection.find().limit():    
    print(i)

#3)students who passed in all categories
mydoc=collection.find( {"scores": { "$all": [{ "$elemMatch":{"score":{"$gte":40},"type":"exam"}},
                                               {"$elemMatch" : {"score":{"$gte":40},"type":"homework" }},
                                               {"$elemMatch" : {"score":{"$gte":40}, "type":"quiz"}}
                                                ]}
                                    })
for i in mydoc:
    print(i)

mydoc=collection.find( {"scores": { "$all": [{ "$elemMatch":{"score":{"$lt":40},"type":"exam"}},
                                               {"$elemMatch" : {"score":{"$lt":40},"type":"homework" }},
                                               {"$elemMatch" : {"score":{"$lt":40}, "type":"quiz"}}
                                                ]}
                                   })

for i in mydoc:
    print(i)
  
#4)Average of all the three type of categories in separate
from pymongo import MongoClient

client = MongoClient('mongodb://localhost:27017/')
result = client['New_Result']['student'].aggregate([
    {
        '$unwind': {
            'path': '$scores'
        }
    }, {
        '$group': {
            '_id': '$scores.type', 
            'avgr': {
                '$avg': '$scores.score'
            }
        }
    }
])
for i in result:
    print(i)

#5)student who scored below average and above pass mark
mydoc=collection.find( {"scores": { "$all": [{"score":{"$gte":40,"$lte":48},"type":"exam"},
                                               {"score":{"$gte":40,"$lte":67},"type":"homework" },
                                                {"score":{"$gte":40,"$lte":48}, "type":"quiz"}
                                             ]}
                                    })
for i in mydoc:
  collection3.insert_one(i)
    print(i)

#6 student who scored fail mark

client = MongoClient('mongodb://localhost:27017/')

result = client['New_Result']['student'].aggregate([
    {
        '$unwind': {
            'path': '$scores'
        }
    }, {
        '$match': {
            'scores.score': {
                '$lt': 40
            }
        }
    }
])

for i in result:
  collection2.insert_one(i)
    print(i)

#7 student who scored pass mark

client = MongoClient('mongodb://localhost:27017/')

result = client['New_Result']['student'].aggregate([
    {
        '$unwind': {
            'path': '$scores'
        }
    }, {
        '$match': {
            'scores.score': {
                '$gte': 40
            }
        }
    }
])

for i in result:

  collection1.insert_one(i)
    print(i)
