# Creating and testing the mock database

## Creating the database

#### createMock.py
Run `python createMock.py` to start main(). This python script accesses mongo's MLAB online database platform via a username and password for a User of the database with admin abilities. Users can be added at will to MLAB directly. The special URL was added to the .env file for the application for easy access. 
```
  DIR = os.path.dirname(os.getcwd())
  load_dotenv(DIR + '/.env')
  URI = os.getenv('MLAB_URI')
```

First, a number of WolfTutor users are added to the user collection (represented by coll). WolfTutor users have: 
  1. a unique user id that is 9 characters long
  
  2. 100 WolfTutor points assigned by the application
  
  3. their name, email and phone number collected from Slack at registration. For our testing purposes, names were "User N" were N is the number of the user in order of creation (1...number of users). Email was userN@ncsu.edu, and phone was left blank as it was not needed for testing.
```
coll.insert_one(
            {
                "user_id": id_,
                "name": name,
                "email": email,
                "phone": "",
                "points": 100
            }
        }
```

Secondly, tutors were created from the list of created users. Tutors have:
  1. A list of subjects to choose from, e.g. `["Operating Systems", "Algorithms", "AI", "Data Mining"]`. For testing, all tutors could tutor all 4 subjects

  2. A single randomly generated availability

  3. Ranomdly generated reviews, major, degree of that major, gpa for that degree, and a pay point rate.

```
coll.insert_one(
            {
                "subjects": [
                    {
                        "name": subjects[0]
                    },
                    {
                        "name": subjects[1]
                    },
                    {
                        "name": subjects[2]
                    },
                    {
                        "name": subjects[3]
                    }
                ],
                "availability": [ #from 700 am to 2200 pm
                    {
                        "day": day,
                        "from": time1,
                        "to": time2
                ],
                "reviews": reviews,
                "user_id": tutor,
                "major": major,
                "degree": degree,
                "gpa": gpa,
                "rate": rate,
                "summary": "",
            }
        )
```

----

## Testing


#### readDB.py
Run `python readDB.py` to start main(). This file accesses and reads documents in a mongo database collection. The database was accesses via URI just as for createMock.py: 
  ```
  DIR = os.path.dirname(os.getcwd())
  load_dotenv(DIR + '/.env')
  URI = os.getenv('MLAB_URI')
  ```
A list of documents is created with `coll.find()`. The tutors are returned as database objects exactly as they appear in the database.

#### distribution.py
Run `python distribution.py` to start main(). This file manually generates a histogram for the frequency of GPAs and average review scores. It creates lists of new users and tutors represented by the defined classes. These classes assign values randomly like the createMock.py file, but not every field used is created as only unique ids, GPAs, and reviews are needed.

After histogram creating, the histogram is written to a Microsoft Excel sheet for analyzation. `writeToExcel.py` requries a string title to name the file, the histogram list of values, and the number of tutors used to create the histogram. The manual histogram was created as follows: 
```
def histogram(bins, data, low, high):
    '''
    bins: number of histogram values starting at 0
    data: list of numerical values to normalize in histogram
    low: lowest numerical data value
    high: highest numerical data value
    '''
    step = (high - low) / (bins)

    histogram = [0]*(bins+1)

    for x in data:
        val = int((x - low) / step)
        histogram[val] += 1
    
    return histogram
```


