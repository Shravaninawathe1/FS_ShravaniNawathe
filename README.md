# FS_ShravaniNawathe
* Approach (How I Will Do It)
1. Understanding the Need
Students often commute to the same location (school/college) individually, which leads to wasted time, cost, and increased traffic.
A smart solution is to connect students traveling on similar routes.
2.Breaking Down the Solution
# Frontend (student-facing)
A map-based interface where students enter home location and destination.
Show nearby students on similar routes using icons.
Enable anonymous chatting by clicking on a student’s icon.
# Backend (system logic)
Compare routes of multiple students.
Identify overlaps in travel paths.
Generate possible carpooling matches.
Assign unique, anonymous usernames for privacy.
#Design Priorities
Privacy: Real names & credentials remain hidden.
Usability: Simple UI, minimal steps for students.
Scalability: Should work for multiple users without lag.

* Technical Aspects (Implementation)
🔹 Frontend
Framework: React.js (for dynamic UI)
Map API: Google Maps API / OpenStreetMap (for routes & markers)
Features:
Input fields for location & destination
Route display with polylines
Icons for nearby students
Chatbox (WebSocket-based or Firebase real-time chat)
##  Pseudo Code 
### 🔹 Frontend (React + Map API)
pseudo
FUNCTION onStudentSubmit(home, destination):
    coordinates = MAP_API.getCoordinates(home, destination)
    SEND coordinates TO Backend via API
    DISPLAY route on map
    WAIT for response (matched students)

FUNCTION displayMatches(matches):
    FOR each student IN matches:
        PLACE icon on map at student.location
        ON icon click:
            OPEN chatWindow(student.username)

🔹 Backend
Framework: Node.js + Express.js
Database: MongoDB (to store user profiles, routes, chats)
Features:
Store students’ source/destination as coordinates
Route-matching algorithm (Haversine formula / path overlap check)
i.e  Convert student home/destination into GPS coordinates, Generate route polylines using Map API,
Compare the new student’s route with existing routes in DB, Use Haversine formula to check closeness of points, Count overlapping segments and calculate overlap percentage,
If overlap ≥ threshold (e.g., 40%), return that student as a match.
Unique username generator (hash + random string)
Chat service (Socket.IO / Firebase).
## Pseudo code
API POST /submitRoute(student_home, student_destination):
    coordinates = convertToCoordinates(student_home, student_destination)
    anonymous_username = generateUniqueUsername()
    SAVE {username, coordinates} INTO MongoDB

    matches = []
    FOR each student_route IN database:
        overlap = calculateRouteOverlap(coordinates, student_route)
        IF overlap >= threshold:
            ADD student_route.username TO matches

    RETURN matches TO Frontend
    
🔹 Database(MongoDB)
  Collection: Students
{
    username: "anon123",
    home: {lat, lng},
    destination: {lat, lng},
    route: polyline_coordinates
}

🔹 Chat Service (Socket.IO / Firebase)
ON studentIconClick(target_username):
    OPEN secureChannel(current_username, target_username)

ON messageSend(message):
    ENCRYPT message
    STORE in chatHistory
    EMIT message TO target_username
    
🔹 Security & Privacy
Anonymous usernames (no duplication allowed)
Encrypted chat messages
Location data limited to route-matching only
## Pseudo code
FUNCTION generateUniqueUsername():
    base = "anon"
    number = random(1000, 9999)
    IF usernameExists(base+number):
        REPEAT
    RETURN base+number

FUNCTION encryptMessage(message):
    RETURN AES.encrypt(message, secretKey)

⭐ High level diagram flow :
            start
              |
    Students enter home and destination
              |
        System display route on map
              |
    System send data to backend
              |
Backend logic compares multiple route
              |
    Are overlaps or partner found
          yes  / \ No
          /       \
     suggest ride    show no nearby found
     sharing matches            

         |
Assign hidden name and unique username
           |
      Student views nearby travelers
             |
        open chat to interact
              |
              End

### Challenges & Solutions
- **Challenge:** Protecting student privacy while enabling route sharing.  
  **Solution:** Use anonymous usernames and encrypt chat messages.  

- **Challenge:** Efficiently matching routes in real-time as users keep joining.  
  **Solution:** Use geospatial indexes in MongoDB and optimize route overlap calculation.  

- **Challenge:** Handling scale when hundreds of students use the system simultaneously.  
  **Solution:** Implement caching (Redis) and WebSocket-based real-time updates. 

🔹 Future Enhancement Ideas:
1. Carbon Footprint Tracker:
Show how much CO₂ is saved by sharing rides instead of traveling solo.
2. Gamification: Reward students with points/badges for regular carpooling.

    

    

