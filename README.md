# Wordle Backend Project 3
-------------------------------------------------
<mark>Test case examples are not provided in the tar. 
If you want to test endpoints without looking
into the code, refer to the end of this readme.</mark>
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

# Sample Test Cases Using httpie:

- registering an new account   
  command:   
    http http://127.0.0.1:5000/users/ "first_name=alex" "last_name=chen" "user_name=Alex" "password=449"

sample result
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

Sample result
![image](https://user-images.githubusercontent.com/54679891/206137019-76929821-8ad6-4660-a656-fea1da2164e0.png)


- post a score to redis   
  command:   
  http post http://tuffix-vm/add-score/ "user"="Alex clone1" "score"="1"

sample result
![image](https://user-images.githubusercontent.com/54679891/206151156-5ad7ce80-8668-442e-92bf-817c0fc4b58a.png)

- retrive top ten score   
  command:   
  http get http://tuffix-vm/top-ten/

sample result
![image](https://user-images.githubusercontent.com/54679891/206151575-095e0265-19eb-416b-9dca-80c6fec3ac76.png)

- start a new game   
  command:   
  http post http://Alex:449@tuffix-vm/new-game/
  
sample result  
![image](https://user-images.githubusercontent.com/54679891/206152409-3f8f3c1a-d594-41fc-aa5b-f8ee7e7fe6d9.png)

we can see that data is then being backuped in replica dbs
  ![image](https://user-images.githubusercontent.com/54679891/206152943-4dd2b0ec-4213-4eca-afad-4d8648e2882b.png)

- make a move    
  command:   
  http post http://Alex:449@tuffix-vm/game-guess/ "gameid"="7b4f3128-1449-4d52-b569-2a521c2be0e9" "word"="clomp"

  Note: you will need to use an id that exist in your db to make a move. 
  Using this command directly is very unlikely to give the same result.
  
  sample result:
![image](https://user-images.githubusercontent.com/54679891/206163126-04c86b3b-40f1-4eca-8989-07e19d2638b0.png)

  Note2: original contributor of this project did not implement valid and
  correct word in the way it should be. API never checks if guess is a correct 
  word and only checks if guess is the answer or if guess is a valid word. 
  You may check this in game.py line 213-263.
  Due to the purpose of this project and time constrain, I did not try to
  fix this issue in afraid of causing more bugs that are hard to fix.

- retrive in progress games   
  command:   
  http get http://Alex:449@tuffix-vm/get-user-games/
  
  sample result:
  ![image](https://user-images.githubusercontent.com/54679891/206166526-5955bb23-1acc-4eca-94d2-35833dea3b10.png)

- get game status by id   
  command:   
  http get http://Alex:449@tuffix-vm/grab-game-by-id/7b4f3128-1449-4d52-b569-2a521c2be0e9
  
  sample result:
  ![image](https://user-images.githubusercontent.com/54679891/206166892-b0eaf645-e8df-4921-8ec2-c49599f40eb6.png)

  

