
### user Service:
   #### Create/Signup For an account
   * Browser first establishes the SSL connection 
   * POST /users/
   User Object
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
 * How do you send login/password information to server?. 
 * With Key/Value access being the main pattern, how do you support secondary use-case in CP system? How does secondary indexes work in distributed datastores? My understanding is that in order to user it, you still need the shard key first as it only works at the Node Level
 * Is 3TB small enough to store the data into RDMS? We can create a secondary hash index on RDMS easily. 
 * Defining the REST objects and request/response on of the same partial REST object.
 
