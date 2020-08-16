## Functional Requirements:
    * Able to create user acccount
    * Able to upload photos
    * Able to Follow other users
    * Able to view timeline (recent activies from the people user is following) 
## Capacity Estimations
    1. number of users z
    1. number of uploads 10 per user per day (10*Z)
    2. number of read 25 per user per day (25*z) 

## Number of MicroServices:
### user Service:
   #### Create/Signup For an account
      * Browser first establishes the SSL connection 
      * POST /users/
          form data: username, password, metadata
      * Response is http = 201 with the user object. 
   ~~~
       User{
           id: "",
           userName: "", 
           password: "", 
           metaData: 
              " email: " 
              " created_time: " 
              " dob: "
        }
   ~~~   
   Within a service we have, APP SERVER --> CACHE SERVER --> STORAGE SERVER. 
  * Storage is Row based key-value distributed store. Sharded by User_id. 
       Each Row Size is : 2KB thumbnail, 1kb of other data = 3KB 
       Total Size for 1 billion users: 1B * 3KB == 3TB. 
       Logical View: UserId, user_profile, user_hash (User Hash is the secondary index) 
   #### Login Request
      *  Browser first establish a SSL connection. 
      *  GET http://username:password@domain.com/users/
      *  Response is the SESSION_ID
   App Server creates a hash out of (username, password) and verify that against DB using secondary index. 
   If valid user entry exists, it creates an Session Key using Session Service. 
   
### Session Service:
   #### Create Session
      * POST /session/
      Session Object:
          ~~~{
               user_id: "",
           }~~~
   Thin APP layer with write around cache, creates a random SESSION_KEY and simply writes to distributed key_value_store. 
   #### Get Session
      * GET /session/${session_id}
           ~~~{
               user_id: "",
               session_creation_time: "",
            }~~~
   APP Layer, mostly takes care of write around cache part.. Reads from Cache if not available, reads from DB and populates it back. 
   
 Questions: 
    * How do you send login/password information back to users. 
    * Defining the REST objects and request/response on of the same partial REST object.
