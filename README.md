# Wordle Backend Project 3
-------------------------------------------------
Test case examples are not provided in the tar. 
If you want to test endpoints without looking
into the code, refer to the end of this readme.
-------------------------------------------------

Group 3 team members:
Ming Chen
Nolan O'Donnell
Henry Chen

Steps to run the project:

0. Prepare your own litefs, and copy it to bin folder.
   Litefs is not provided in bin folder of this tar.
   
Note: if you use code downloaded from this repo, you need to add
      mount & data directory manually. 
      To avoid this, use the tar provided in this repo. 

1. Copy the nginx configuration file to sites-enabled by running:

   sudo cp etc/tutorial.txt /etc/nginx/sites-enabled/tutorial
   
   Note:remember to reload nginx.

2. Start the API by running

   foreman start

3. Initialize the database and start the API:

   sh ./bin/init.sh

4. Populate the data base by running the python script:

   python3 dbpop.py

Note: populating DB before starting services will cause
      mount to fail.      


5. Using http://tuffix-vm should yield in redirecting requests
   over to provided appropriate microservices.

   You may refer to /etc/tutorial.txt to find all provided
   redirection for tuffix-vm.

   location of each service as local ip
   game1,2,3:   
   http://127.0.0.1:5200   
   http://127.0.0.1:5400   
   http://127.0.0.1:5600   

   user:  
   http://127.0.0.1:5000

   Note: as auth is set to be internal, accessing it with
         tuffix-vm server name will return error code.
         It works correctly as a pop up.

   leaderboard:
   http://127.0.0.1:5700

   Note: nosql DB is NOT populated by default.
   To populate it, access the /add-score/ endpoint or
   http://127.0.0.1:5700/scores/ to add scores to it.


Files to turn in:

1. Python source code:

   game.py  
   user.py  
   leaderboard.py

2. Procfile:

   Procfile contains 3 microservices game, leaderboard and
   user that have no coupling. It also contains

3. Initialization and population scripts for the databases:

   dbpop.py - populates the database with wordle words  
   game.sql - contains database for game microservice  
   user.sql - contains database for user microservice  
   init.sh - Initializes the databases to be created  

4. Nginx configuration files:

   tutorial.txt

5. Any other necessary configuration files:

   correct.json  
   valid.json - both populate the database with dbpop.py file  
   game.toml  
   user.toml - allows the source to connect to the database
   primary.yml
   secondary.yml
   secondary2.yml- configuration files for litefs

#Sample Test Cases Using httpie:

-registering an new account
command
http http://127.0.0.1:5000/users/ "first_name=alex" "last_name=chen" "user_name=Alex" "password=449"

sample output
![image](https://user-images.githubusercontent.com/54679891/206137205-71d502ab-4fb2-43a7-b6db-5f5b2bcf1736.png)


Use this account for auth afterwards.

If you wish to access this endpoint as tuffix-vm/register/ without having to 
use auth, remove 

	location / {
		#login popup
		auth_request /auth;
		auth_request_set $auth_status $upstream_status;
  	}

In tutorial.txt, and update nginx config again.

The command would now be
http http://tuffix-vm/register/ "first_name=alex" "last_name=chen" "user_name=Alexc" "password=449"

Sample output

![image](https://user-images.githubusercontent.com/54679891/206137019-76929821-8ad6-4660-a656-fea1da2164e0.png)


-post a score to redis 
command
http post http://tuffix-vm/add-score/ "user"="Alex clone1" "score"="1"

sample output
![image](https://user-images.githubusercontent.com/54679891/206151156-5ad7ce80-8668-442e-92bf-817c0fc4b58a.png)

-retrive top ten score
command
http get http://tuffix-vm/top-ten/

sample output
![image](https://user-images.githubusercontent.com/54679891/206151575-095e0265-19eb-416b-9dca-80c6fec3ac76.png)





