## Functional Requirements:
    * Able to create user acccount
    * Able to upload photos
    * Able to Follow other users
    * Able to view timeline (recent activies from the people user is following) 
Capacity Estimations
    1. number of users z
    1. number of uploads 10 per user per day (10*Z)
    2. number of read 25 per user per day (25*z) 

Number of MicroServices:
    1. Upload Service   (token, blob, meta_data) 
          meta_data = time, location, thumbnail, 
    2. Read Service (token, photoes older than) 
                    (token, object_id, object_type, shared_with = List<user_ids>)
                    

Upload Service:
    1. appserver --> backend row based (k,v) store. 
    row based distributed store.  shard_by = user_id, time
 
Read Service:
    1. appserver --> cache_
