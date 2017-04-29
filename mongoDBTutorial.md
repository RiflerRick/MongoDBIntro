# MongoDB

### A Basic Introduction

MongoDB is a typical example of a nosql database. In case of mongodb data is not stored in the form of rows and columns(or tables) but in the form of documents. Like a very good example of document would be the way in which we are sending data to elasticsearch if familiar. Data is send in a json like format. 

The way the mongodb database is designed, it has a mongodb server that is always running at a particular port. The server has mnay databases running. Considering the hierarchy the databases run below the mongodb server. Every database always gets spawned below the mongodb server.

The way to run the server is to run the mongod executable from the mongodb directory from program files. The dbpath must be configured otherwise we will get an error. So first the dbpath is configured or atleast created and then during the running of the server the dbpath is set using the following command:

running executable: ``` mongod --dbpath C:\temp ``` assuming that C:\temp exists.

Once the server is running we can start using the database.

The command line interface that we can use to interact with the database can be started using mongo executable present in the mongodb directory in program files.

So like we were talking about the hierarchy in which mongodb functions. At the top we have the mongo db server which has databases below it which can be spawned at any point of time. Below them are the **collections** which are nothing but collections of documents. Below them are the actual documents.

There are a few commands we can use for various purposes of interacting with mongodb

- ``` show dbs ``` for showing the databases that exists.
- ``` show collections ``` for showing collections within a 
database.

First you would generally want to switch your database before you actually start working there. This is done using the command ``` use <name of the database>``` just like sql. Database remember is higher in the hierarchy and hence they come first, that is first you get the database switched and then you switch and then you select the collection.

#### Importing csv, tsv or json files into mongodb

When importing json documents, each document must be a separate line of the input file.

```
mongoimport --host <hostname> --db <database name> --collection <collection name> < <json file name with extension> 
```

Here the file name with the extension can also be a path with the file name and the extension.

However there are other ways to import data into the database . Most importantly the data may not be in the format of where each document is in a new line. One simple way can also be to put the data or write the data in the form of a json array and use the flag --jsonArray so that the command line knows that.

#### Commands

- ``` db.bank_data.count() ``` here db is the instance to the current database, it is always gonna be db and bank_data is the name of a collection. Understand that the database and the collection can be used this way simply as objects and we can use functions on those objects, in this case for instance count()

- ``` db.bank_data.findOne() ``` simply returns the first document in the collection bank_data

- Mongo db assigns a primary key by the name of **id** in each document. It is recommended not to mess around trying to change or modify that key.

- ``` db.bank_data.find({'_id' : ObjectId("84552554sd85e8c59")})[0] ```. Here we are finding out a document that matches the object id given in the object passed inside the find function. Understand that sometimes it may happen that the command returns not one but many documents or parts from many documents, in which case the documents are stored in the form of arrays or array like structures. 

- ``` db.bank_data.find({'_id' : ObjectId("84552554sd85e8c59")}).count() ```: Again we are simply counting the number of results we are getting.

- ``` db.bank_data.find()[7] ```picks the 7th document of the collection and returns it.

- ``` db.bank_data.find({ last_name: "SMITH" }).count() ```as you can probably expect here trying to find out those documents where the last name is "SMITH". Note that when we are passing the queries as json objects, it seems as though the object name is already defined before hand --> last_name for instance in this case.

- ``` db.bank_data.find({ last_name: "SMITH" })[50] ```: Get the 50th document.

#### Projections:

- ``` db.bank_data.find({ last_name: "SMITH" }, {first_name:1, last_name: 1})[2] ```: A projection means that we wanna get only some selected information from the documents that match that query. The query is the very first arguement of the find function and the projection is the second arguement of the find function. When we say 1 for a value of a key in the projection object it means 'include it' an 0 (only used for the '_id') field means not to include it.

- So here in the documents we have a sub-document called 'accounts'. We can pick a single entry from the accounts sub document in the following fashion
    ```     
    > var x='SMITH'
    > var smithPersons=db.bank_data.find({ last_name:'SMITH'}, {first_name: 1, 'last_name':1 })
    smithPersons.count()
    > for (var i=0;i< smithPersons.count();i++){
        print(smithPersons[i].first_name+' '+smithPersons[i].last_name)
    }
    ```
    Mind you that the way all this is working is that we can use smithPersons as an object and it will be having the first_name and the last_name as attributes or properties that I can use.

- ``` db.bank_data.find({'accounts.account_balance': 842.25})[12]```: As you can probably figure out we are trying to find the matching account and give the entire data of the account. There is no projection in this case.  

- ``` db.bank_data.find({'accounts.accountBalance':2512.24}, {'accounts.$':1})```: This query will be able to match the account with the accountBalance 2512.24 and return us with only the account information and not the whole document. The dollar sign means that although there are other sub documents inside the account object as well, we would only like theat particular entry.

Essentially all read queries are just find().

#### Querying using operators: and, or etc

- ``` db.bank_data.find({'first_name':'James', 'last_name':'smith'})```:When we are separating the key value pairs in a query object using a comma it is taken that we want an **and** operation between the 2 key value pairs. This is important.

- 