# nosql-challenge
## Part 1: Database and Jupyter Notebook Set Up (15 points)
To receive all points, your Jupyter notebook setup file must have all of the following:
1. Include the mongoimport command text you used to import establishments.json in a markdown cell at the beginning of your Jupyter notebook file (3 points)

    - Code has been added to a markdown cell at the beginning of the file

2. The mongoimport command text correctly drops any existing establishments collection before importing establishments.json into MongoDB (2 points)

    - Code below includes command to drop existing tables

            mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json

3. The database is named uk_food and the collection is named establishments (2 points)

    - See below for code used

            mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json

4. Correctly imports PyMongo and Pretty Print (2 points)

    - see below for code used

            from pymongo import MongoClient
            from pprint import pprint

5. An instance of the Mongo Client is created (1 point)

    - see below for code used

            mongo = MongoClient(port=27017)

6. Lists the databases you have in Mongo, which includes uk_food (1 point)

    - see below for code used

            print(mongo.list_database_names())

7. Lists the collection(s) in the uk_food database, which includes establishments in the output (1 point)

    - see below for code used

            print(db.list_collection_names())

8. Uses find_one() and pprint to display one document in the establishments collection (2 points)

    - see below for code used

            pprint(db.establishments.find_one())

9. The establishments collection is assigned to a variable (1 point)

    - see below for code used

            establishments = db['establishments']



    **Code for part 1 was sourced from activities we performed in class**


## Part 2: Update the Database (20 points)
1. To receive all points, your Jupyter notebook setup file must have all of the following:
The supplied data for the "Penang Flavours" restaurant is correctly inserted into the establishments collection (3 points)

    - first created a dictionary of the data for Penang Flavours
    - then inserted the data into the establishments collection

            new_info = {
            "BusinessName":"Penang Flavours",
            "BusinessType":"Restaurant/Cafe/Canteen",
            "BusinessTypeID":"",
            "AddressLine1":"Penang Flavours",
            "AddressLine2":"146A Plumstead Rd",
            "AddressLine3":"London",
            "AddressLine4":"",
            "PostCode":"SE18 7DY",
            "Phone":"",
            "LocalAuthorityCode":"511",
            "LocalAuthorityName":"Greenwich",
            "LocalAuthorityWebSite":"http://www.royalgreenwich.gov.uk",
            "LocalAuthorityEmailAddress":"health@royalgreenwich.gov.uk",
            "scores":{
                "Hygiene":"",
                "Structural":"",
                "ConfidenceInManagement":""
            },
            "SchemeType":"FHRS",
            "geocode":{
                "longitude":"0.08384000",
                "latitude":"51.49014200"
            },
            "RightToReply":"",
            "Distance":4623.9723280747176,
            "NewRatingPending":True
            }

            establishments.insert_one(new_info)

2. A query is performed to find the BusinessTypeID for "Restaurant/Cafe/Canteen" and returns only the BusinessTypeID and BusinessType fields (3 points)

    - Used the following code to pull all results for Restaurant/Cafe/Canteen and used the variable fields to only pull the desired data

            # Created the query to search for documents where the BusinessType equalled "Restaurant/Cafe/Canteen"

            query = {'BusinessType': {'$regex': 'Restaurant/Cafe/Canteen'}}

            # Created a variable to only pull 2 specific fields from the document

            fields = {'BusinessTypeID': 1, 'BusinessType': 1}

            # Created a variable to pull the results from the collection using the query and fields variables

            results = establishments.find(query, fields)

            #print the results so that we can see the BusinessTypeID 
            
            for the next activity
            for i in range(3):
                pprint(results[i])

3. The "Penang Flavours" document is updated with the correct value for BusinessTypeID (3 points)

    - Took the businesstypeid from the results above and added it to the Penang Flavours document

            db.establishments.update_one(
            {'BusinessName': 'Penang Flavours'},
            {'$set': {'BusinessTypeID': 1}
            }
            )

4. A query is correctly performed to delete all the documents in the collection where "Dover Local Authority" is the value for LocalAuthorityName (3 points)

    - Used delete_many to delete all documents with the indicated value

            db.establishments.delete_many({'LocalAuthorityName': 'Dover'})

5. A count_documents() check is performed before and after the removal of the Dover documents to ensure the documents were removed (4 points)

    - Used count_documents to count and ensure there were 0 documents with the indicated value

            db.establishments.count_documents({'LocalAuthorityName': 'Dover'})

6. An update_many() query is performed to convert the latitude and longitude fields from strings to decimal numbers and RatingValue to integers (4 points)

    - Used update_many to convert the field types from strings to decimals

            db.establishments.update_many(
            {}, 
            [{'$set': {'geocode.longitude': {'$toDecimal': '$geocode.longitude'}, 'geocode.latitude': {'$toDecimal': '$geocode.latitude'}}}])


    **Code for this part of the assignemnt was taken from activities performed in class**


## Part 3: Exploratory Analysis (55 points)
To receive all points, your Jupyter notebook analysis file must have all of the following:

1. Question 1: Which establishments have a hygiene score equal to 20? (8 points)

    - A query is correctly performed to find the establishments with a hygiene score of 20 (2 points)

    - count_documents() is used to list the correct number of documents (answer: 41) (2 points)

    - The first result is printed using pprint (2 points)

        - I used the query variable to create my query and then results to find the data given the query I had entered
        - used count_documents to count the results
        - used pprint to pretty print the first result

                # Find the establishments with a hygiene score of 20
                query = {'scores.Hygiene': {'$eq': 20}}
                hygiene_score_results = establishments.find(query)
                # Use count_documents to display the number of documents in the result
                num_documents = establishments.count_documents(query)
                print(f"Number of documents in result: {num_documents}")
                # Display the first document in the results using pprint
                for i in range(1):
                    pprint(hygiene_score_results[i])

    - The results are converted to a Pandas DataFrame and displays the first 10 rows (2 points)
        - Converted the results to a pandas dataframe

                # Convert the result to a Pandas DataFrame
                hygiene_score_results_df = pd.DataFrame(hygiene_score_results)
                # Display the number of rows in the DataFrame
                row_num = len(hygiene_score_results_df)
                print(f'Number of rows in the df: {row_num}')
                # Display the first 10 rows of the DataFrame
                hygiene_score_results_df.head(10)

    **Code used for this part of the assignment was taken from activities performed in class**

2. Which establishments in London have a RatingValue greater than or equal to 4? (12 points)
    - A query is correctly performed to find the establishments in London with a RatingValue greater than or equal to 4 (4 points)
    - The query uses the $regex operator to locate the London establishments (2 points)
    - count_documents() is used to list the correct number of documents (answer: 33) (2 points)
    - The first result is printed using pprint (2 points)
        - Created my query variable to match Londo and the particular rating value
        - used count_documents to count the number of documents and then print the result
        - pretty printed the first results using pprint


                # Find the establishments with London as the Local Authority and has a RatingValue greater than or equal to 4.
                query = {'LocalAuthorityName': {'$regex': 'London'}, 
                        'RatingValue': {'$lte': 4}}
                rating_value_results = establishments.find(query)
                # Use count_documents to display the number of documents in the result
                num_documents = establishments.count_documents(query)
                print(f"Number of documents in result: {num_documents}")
                # Display the first document in the results using pprint
                for i in range(1):
                    pprint(rating_value_results[0])

    - The results are converted to a Pandas DataFrame and displays the first 10 rows (2 points)
        
            # Convert the result to a Pandas DataFrame
            rating_value_results_df = pd.DataFrame(rating_value_results)
            # Display the number of rows in the DataFrame
            row_num = len(rating_value_results_df)
            print(f'Number of rows in the df: {row_num}')
            # Display the first 10 rows of the DataFrame
            rating_value_results_df.head(10)

    **Code for this part of the assignment was taken from activiites performed in class**

3.  What are the top 5 establishments with a RatingValue of 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"? (15 points)
    - A query is correctly performed to find the establishments within 0.01 degree of the "Penang Flavours" restaurant (4 points)
    - The query also limits the results to establishments with a RatingValue of 5 (2 points)
    - The query uses the sort() method in PyMongo to sort in ascending order on the hygiene score (2 points)
    - The query uses the limit() method in PyMongo to limit the results to 5 (2 points)
    - All five results are printed using pprint (3 points)
        - added the exact latitude and longitude coordinates and assigned them to the variable as I was unable to make it work trying to pull it directly from the document
        - put together a query using the 3 variables
        - used the osrt method to sort in ascending order on hygiene
        - used limit to limit the results
        - printed results using pprint

                degree_search = 0.01
                latitude = 51.49014200
                longitude = 0.08384000

                query = {'geocode.latitude': {'$gte': latitude-degree_search, '$lte': latitude+degree_search}, 
                        'geocode.longitude': {'$gte': longitude-degree_search, '$lte': longitude+degree_search}, 
                        'RatingValue': {'$eq': 5}}
                sort = [('scores.Hygiene', 1)]
                limit = 5
                top_five_results = list(establishments.find(query).sort(sort).limit(limit))
                # Print the results
                pprint(top_five_results) 

    - The results are converted to a Pandas DataFrame and displayed (2 points)
        - converted results to dataframe and displayed them

                top_five_results_df = pd.DataFrame(top_five_results)
                top_five_results_df.head()

    **I struggled with the latitude and longitude parts of the code and chatGPT helped put together my query variable**

4.  How many establishments in each Local Authority area have a hygiene score of 0? Sort the results from highest to lowest, and print out the top ten local authority areas. (20 points)
    - An aggregation pipeline is built to include a match query, group, and sort (3 points)
    - The match query matches documents with a hygiene score of 0 (2 points)
    - The group step of the pipeline is grouped on LocalAuthorityName and counts the number of documents (4 points)
    - The sort step of the pipeline sorts the count of the documents in descending order (2 points)
    - The aggregation pipeline is correctly sent to the aggregate() method (2 points)
    - The results from the aggregation query is cast as a list and then saved to a variable (2 points)
    - The first ten results are printed using pprint (3 points)
        - built a pipeline using match_query, goup_query, and sort_values
        - built the pipeline into a variable that we then ran through the aggregate method and saved the results to a variable
        - printed the number of of documents
        - pretty printed the results

                # 1. Matches establishments with a hygiene score of 0
                match_query = {'$match': {'scores.Hygiene': {'$eq': 0}}}
                # 2. Groups the matches by Local Authority
                group_query = {'$group': {'_id': '$LocalAuthorityName', 'count': {'$sum': 1}}}
                # 3. Sorts the matches from highest to lowest
                sort_values = {'$sort': {'count': -1}}
                # Created my pipeline variable
                pipeline = [match_query, group_query, sort_values]
                # Run the pipeline through the aggregate method and then save the results to a variable
                hygiene_results = list(establishments.aggregate(pipeline))
                # Print the number of documents in the result
                print(f'Number of countries in result: ', len(hygiene_results))
                # Print the first 10 results
                pprint(hygiene_results[0:10])

    - The results are converted to a Pandas DataFrame and displays the first 10 rows (2 points)
        - Converted the results to a pandas dataframe

                # Convert the result to a Pandas DataFrame
                hygiene_results_df = pd.DataFrame(hygiene_results)
                # Display the number of rows in the DataFrame
                row_num = len(hygiene_results_df)
                print(f'Number of rows in the df: {row_num}')
                # Display the first 10 rows of the DataFrame
                hygiene_results_df.head(10)

    **Code from this assignment was taken from activities we performed in class**