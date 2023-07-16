---
title: Smart Environment Toolkit
date: 2023-03-20
categories: [University Projects, 3rd year]
tags: [java, c++, python, flask, grafana, influx, sql, microbits]  # TAG names should always be lowercase
---

Note this project was completed in a group of 3 people with a rotating scrum master between each 3 of us and my role was everything except front end (I made the graphing page for the front end but that was just embedding grafana through an iFrame) and work too much on the microbits embedded side of things this time unlike the [previous project (Smart Lab)](https://michael-perdue.github.io/posts/Smart-Lab/) that I did work on the microbits extensively and this projects extends that previous project. So I was focused on the Base Station, API, InfluxDB, MySQL DB and grafana for this project.

# TLDR of the project

This project is an extension of the [Smart Lab](https://michael-perdue.github.io/posts/Smart-Lab/) project I made and takes the idea of a smart lab IOT with microbits and brings it to a whole environment spanning multiple buildings and rooms all configurable by the user (ie amount of buildings, areas, rooms etc). It provides a website that can be accessed anywhere that provides real time statics of the building (amount of people in each building and room, the temperature, noise level and light level) and also management of the rooms light remotely (turn on and off from anywhere with internet). This is done through microbits being coded to record this information and communicates as a mesh network with one microbit per room writing over serial to a computer which processes it in java and sends it to a api written in python on a vps where it is then stored in an influx db and the website then calls the api to get the information. Then the capacity of the people is done through users carrying microbits which communicate with the sensors every 10 seconds saying that they are there and these users can also unlock doors through their microbits. You can easily design the system to work for any real world scenario as the creation of buildings/rooms/floors etc and the mapping of microbits can be all done through the website. Each microbit can be configured to work as any role from the website so you can set a microbit to be a sensor then remove it and set it to be a light. The deletion of a building will then remove any roles of the microbits in that building to allow for easy setting up of the system again and again.

The system is thoughrouly tested through unit tests, user testing, end to end testing, integration testing and testing of the system to ensure it can run 24/7. Sadly microbits are quite limiting especially for a system like this so they don't always perform perfectly so during our test their would ocasionaly be lost data for a minute or so along with some other minor bugs however that is due to the limitation of microbits hardware and the [codal library](https://github.com/lancaster-university/codal-microbit-v2) they run on which we had to edit a decent amount to allow for this system to work. Below you can read an indepth dive of the system including class diagrams, screenshots, clips of the IOT system running, code snippets and more. 


# The structure of the backend of the system

Here is a diagram of our backend structure:

![](https://michael-perdue.github.io/assets/ToolkitDiagram.jpg)

After the first project the [Smart Lab](https://michael-perdue.github.io/posts/Smart-Lab/) we already had a extensive idea on the limitation of a microbit and how to best utilise the radio of the microbits. So the microbits communicate with each other over radio on a specified band and radio group then the all the sensor microbits will find the closest basecamp microbit and this basecamp then sends this data over serail to the base station. The base station (a java progrma continually running on the pc) then processes this data, filters our junk messages and sends the data to the API through the appropriate routes. The REST API (written in python) will then process this data and if its specific data points it will send it to influxDB (which is what influx was built to store) through flux statements and if it is anything relating to roles of the microbits then it will be stored into the MySQL DB. Grafana also uses influxDB and using flux statements combines the MySQL data which was a much nicer way of dealing with both DB's then using Grafana's built in functions which don't particularly work. The api and website both auto deploy through a docker container on the VPS which lets us easily update the live API and Website with just a click of a button. The front end website then gets various sets of data, graphs etc all through the API routes and it can manage the microbits and the whole system through these different routes. The website lets you add and remove users, set roles, change the microbits roles, change the structure of the environment.

This brings me onto the logical structure of the system which can be seen in this diagram below:

![](https://michael-perdue.github.io/assets/SET-structure.png)

This structure allows for the smart environment to work for any environment as the end user can define what a zone is, how many zones they have and how many areas each zone has. This system also supports as many walkers, doors and sensor microbits the only requirement for rooms is that there is atleast one basecamp in any room you have a sensor for. However if you don't need to have a sensor in the room then a room can exist without any basecamps thus a room can exist without any microbits. This is all then managable from the front end so you can delete,add and change rooms, areas and zones. In addition you can also assign,unassign and change the role (so sensor to door to walker etc) of all microbits.

## Microbits
The code for the microbits is written in C++ and is written using the [codal library](https://github.com/lancaster-university/codal-microbit-v2) and this works by first writing the code in C++ then compiling that code to hex through codal and then flashing that hex onto the microbits 

So our setup is similar to last time however due to a better knowledge of the microbit radio we no longer had to use atomic multicast like we did on the previous project which in turn decreased the amount of processing and lost packets of the microbits. The walker microbit is used for opening doors and as basically a location/room current capacity tracker and it itself does not need to communicate with the basecamp. When the walker wants to open the door the door will contact the base camp and the base camp will write over serial to the base station the packet and the base sation will turn it into API request which will return the results of if it can open the door and that packet will be sent do the door. The walker then also send a message to the nearest sensor saying that it is still alive every 10 seconds so that the sensor can then forward on the information and keep live tracking of its location. In addition the sensor will also send the temperature, noise and light reading every 3 seconds. 

The microbits when turned on use a simple dhcp which is that they will send a request to the basecamp for its ID and role, this is then written over serial and a request to the API is made which looks up a MySQL table that has all the serial ids of microbits along with a user friendly ID (starts at 1 and increments after each new microbit) and the role, this is then returned over serial and a packet is sent. The microbit will then flash If the microbit has no role then it will send a keep alive request every 30 seconds and when that request comes in it will check if a new role has been given. You then know what microbit is giving what data as they send along a little bit of meta data saying the size of the packet, the message id, the packet id, the microbit id then the data. The message id would then be used for making sure messages are only proccessed once by as they are a randomly generated 32 uint number.

## Base Station
The Base Station is effectively responsible for the turning bytes sent from a microbit serially into the correct data and then calling the relevant API routes to ensure that the data is handled correctly and stored if needed. The code itself is written in java and is event driven following the [observer design pattern](https://refactoring.guru/design-patterns/observer) which is shown in the class diagram below where the serial manager class will read packets and report them to the packet manager and then this packet manager will loop through all listeners and report to them if they are subscribed to that packet type that one has appeared, the listeners on creation need to register themselves as a listener by calling the method in the packet manger class. 

Then the listeners all have a set way they deal with each event/packet they are registered to listen to, so for data related packets report listener will call the rest api manager with the packet type and the packet which the api manager will subsequently compile a correct url to call on the api to store that data. The door listener is used for packets to check if a microbit can open a door or not through the api manager and it will then construct a packet to send back over through the serial manager stating whether the door can be opened or not. The address listener is for assigning the microbits ids whenever they turn on so it will send a request through the api asking whether the serial id (uint32) of the microbit has an assigned easy to read id(starts at 1) and the api will return one and if one didnt exist it will make a new one by the incrementing the current highest id number. The light actuation listener does the same as the door listener but just gets back its status. Then the listeners that need to send a packet will do so via sending the packet to the serial manager and this will then write it over serial.

The base station also performs additional checks to ensure no garbage/corrupted data is processed and this is done by checking the size of the packets ensuring that not only it is big enough to process but once it is being processed by the serial manager it checks that for its packet type it is the correct size and if its not then 4 bytes are cleared from the buffer and the next 4 bytes are read from buffer until a non corrupted packet is found. This is done on top of the microbits checking for duplicate packets based of ID's so in total the system deals with corruption of data and duplication of data.

When it comes to testing of the base station along side integration testing and end to end testing I have made multiple unit tests using Junit and it tests parsing of each packet type, alongside the creation of the url routes for the api. In addition the base stations has a debug mode in which toggling a boolean flag allows you to easily see all information about the packets being proccesed, routes being used and any other imporant diagnositc information.

Rather then including code snippets of the java code I have instead made a class diagram as unlike with the API you won't be able to make sense of any of the code just from snippets. Below is a class diagram of the base station, note doted line means it iteracts/uses that classes methods or properties, the blue line dictates classs inheritance, the green line is when a class is implementing a intergace and a white solid line is when a class must contain a specific amount of instances of another class:

![](https://michael-perdue.github.io/assets/ToolKitClass.png)

## API
The API for this project was a REST API written in python by using [Flask](https://flask.palletsprojects.com/en/2.3.x/). It has around 50 routes most of which can be further expanded on by adding arguements so for example the route for a single reading of data lets you specify the micrbit you want, the offset (i.e. last data point or 2nd last data point etc) and the type (temperature, noise or light level). Then to get specific data was fine as I just had a flux query to get the data for microbit ID x, however it did mean that I had to learn flux which was quite a task in comparision to sql as influx/flux returns multiple tables rather then just one nice combined table like SQL and below you can see an example of a flux statement.

Example of a flux statement and the code in python to get the last record from x microbit with x offset:
```python
    #Gets the last record from the table given with the microbit and the offset of the result (i.e if you want the 2nd last result)
    queryResult = query_api.query('from(bucket: "scc331") ' 
        '|> range(start: 0) '
        '|> filter(fn: (r) => r["_measurement"] == "' + str(table) + 'Result") '
        '|> filter(fn: (r) => r["microbitID"] == "' + str(microbitID) + '")'
        '|> sort(columns: [\"_time\"], desc: true) '
        '|> limit(n:1, offset: ' + str(offset) + ')')
    records = []
    try:
        #Gets the time and microbit ID and value for the record from the flux result
        records.append([queryResult[0].get_time(), queryResult[0].values.get("microbitID"), queryResult[0].get_value()])
    except: #incase no results were found for the microbit ID
        records = []
    return records
```

When it came to getting aggregated results so for example how many people are in x building then I would first use a SQL statment for the MySQL table (two seperate dbs as Influx is built for just data points) and get the microbit id's for all the zones and areas in the building and then I would get the last known location of all the microbits through flux and count all the walkers with the location the same as any of the zones or areas. Then for just any other routes that would just need to check the MySQL tables then I just have a simple SQL statment to check it and the way that I coded this in python can be seen below with the example of changing a microbits role and was done by using the [mysql connector library](https://www.mysql.com/products/connector/) for python:

```python
db = mysql.connector.pooling.MySQLConnectionPool(host=mysql_address, user=mysql_user, password=mysql_password, database="SCC331", autocommit=True, pool_reset_session=True, pool_size=5)
#creates and executes a prepared statement given the query and arguments and then returns the result
def execute(query, arguments=None):
    if arguments is None:
        arguments = []
    #opens the mysql connection
    con = db.get_connection()
    cursor = con.cursor()
    try: #tries to execute the query
        cursor.execute(query, arguments)
        res = cursor.fetchall()
        return res
    finally:  #ensures the mysql connection is closed and no memory leaks happen
        cursor.close()
        con.close()
#changes a microbits role given thier ID and the new desired role
def changeMicrobit(microbitID,type):
    try:
        execute("update microbit_dhcp set type = %s where microbit_address = %s;", (type, microbitID))
        return True
    except:
       return False
```

Finally the API itself has authentication through flasks session so a user first must call a login a route with a username and password, this is password is then hashed with bcrypt (what we use to store hashed passwords) and checked if it matches the hash in the dabase and if so that route then returns a cookie that is sent along with each request that states their authentication level and every route has a required auth level the user must be to access it. This means if the user tries to skip a the login page they just get a 404 error rather then any data and this is achieved with the code below:

```python
#Login route takes a username and password
@app.route('/login',methods=["GET"])
def logingRequest():
    user = request.args.get("user", type=str, default="user")
    password = request.args.get("password", type=str, default="password")
    sql = execute("SELECT password, `role` FROM users WHERE username = %s;", (user,))   #gets the users hashed password from the table
    try:
        password_hashed = sql[0][0]
        auth_level = roleFromString(sql[0][1])

        if bcrypt.checkpw(password.encode("utf-8"), password_hashed.encode("utf-8")):   #checks the users hashed password matches the password given once hashed
            session['logged_in'] = True
            session['auth_level'] = auth_level
            return jsonify({"success":True,"auth":auth_level})
        else:   #if the users password doesn't match then they arent authorised
            return jsonify({"success": False,"auth":0})
    except:
        return jsonify({"success": False,"auth":0})

#This function checks that the user for this session has logged in and has the valid auth level
def authenticated(requiredRole):
    def actual_decorator(function):
        @wraps(function)
        def authentication(*args, **kwargs):
            if session:
                if session['logged_in'] and session['auth_level'] >= int(requiredRole):
                    return function(*args, **kwargs)
            return "UNAUTHORISED", 401
        return authentication
    return actual_decorator

#every function/route has this attached which calls the authenticated function to check if they are authorised
@authenticated(Role.Admin)
def findAddress():
```
Finally when it comes to testing of the API along side integration testing and end to end testing I have also unit tested the API to ensure logging in is persistant, routes work and return the correct information and other things. This was achieved through using the unittest library.

## Database

The project uses two databases a Influx database and a MySQL database. The Influx db is used to store data points so it stores the date time, microbit ID, type of data and value of a data point. This then also can be easily mapped onto a graph using grafana and setting up a date/time line graph which made making the graphs nicer then just using MySQL. MySQL is used for storing the users details (passwords and usernames) and the overall logical structure of the system so the buildings,areas,zones etc along with the microbit information of what each microbit's role and ID is.

# The systems features

## Demo of overall system

Here is a video of the website working from a phone:

[![Mobile Demo](https://img.youtube.com/vi/edvk2hz0MMY/0.jpg)](https://www.youtube.com/watch?v=edvk2hz0MMY&t "Mobile Demo")

Here is how the system looks from the hardware sides of things:

![](https://michael-perdue.github.io/assets/AllToolkit.gif)

In the demo above you can see the system in practive where each of the clusters are a room and its microbits asocciated with that room (note in practice and it has been tested in much bigger environments but for the sake of recording that is not possible to show). The computer then which has a base camp microbit attached to it and then this bace camp continually flashes B to let you know it has just processed some messages. Then on the actual computer proccessing the messages you can see it has a console window which is prints the amount of messages proccessed a second, the microbits that are connecting and disconnecting to this basecamp and any errors. Finally the computer shows the of the homepage dashboard of the website with live data.

## Realtime environmental data (including location tracking)

Each microbit sensors records the temperature, light and noise level through the built in hardware of a microbit. The temperature value is measured in celcius, however both light and noise are measured from an arbiturary value so light is just on a scale of 0 to 255 and noise is just a reading in the 9000s so we coverted these to be thresholds meaning if its below x then display that its 'quiet' or for lights its 'dark'. Then each of these are displayed on the website home page under the zone, area and room the sensor is located in.

Finally the other evnrionmental data that we took the capacity of each zone, and this was effectively just the amount of people with microbits in each zone. This worked off each walker sending out pings to other sensors to find the closest microbit then this location would be reported back every 10 seconds and this is then be stored and the api only returns the capacity from records in the last 5 mins to ensure non-active microbits which could have left the zone and are no longer near any zones aren't still being recorded. No walker is ever counted more then once as it goes of its id and finds the last record and only the last one.

Here you can see a video of the location tracking working looking on the left screen you will see the current capacity numbers change within 10 seconds of the microbit walker being moved and you can also see the live temp,noise and light level:

[![Location tracking](https://i.ytimg.com/vi/U2OOp0CowE4/hqdefault.jpg)](https://www.youtube.com/watch?v=U2OOp0CowE4 "Location tracking")

## Light actuation

Here is a video of the a microbit being made a sensor via the website and then on the website turning that sensors light on:

![](https://michael-perdue.github.io/assets/LightToolkit.gif)

## Door unlocking and locking

Here is a video of the a walker trying to open a door they dont have permission to, then giving them permission and them opening the door:

![](https://michael-perdue.github.io/assets/DoorToolkit.gif)


## Graphs of live and historic data


## System structure and user management 

As you can see in the previous screenshots, gifs and videos you can manage the whole system from the front end. So you can not only make new zones which are just clusters of areas and an area is just user defined so a floor, a building or whatever depending on the scenario but you can also easily manage all the microbits in those zones. None of the system is hardcoded which means that everything can be catered to end user and we successfully tested this by using the same system to make a smart campus then a smart hotel with no code being changed. The result of which showed their was zero issues in swapping between the two and their was no issues with the system not being able to adapt to the different scenarios. In addition their is two user levels a user and an admin the user can create an account and an admin can promote a user to an admin, delete an account and only admins can configure the system.

Below I have included the design mockups of the system (rather then just a massive amount of webpages) and the only thing to note is institutes was instead renamed to area to fit a better generalisible system:

![](https://michael-perdue.github.io/assets/ToolKitPages.gif)

# Costings of the system

|               | Hardware               | Amount                       | Cost per hardware | Total cost    | Cost of whole organisation                                                                                                          |
|---------------|------------------------|------------------------------|-------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Small System  | Micro:bit              | 15                           | £15               | £225          | £447 initial and £120 upkeep per year                                                                                               |
|               | Bat:bit3(battery pack) | 15                           | £9                | £135          |                                                                                                                                     |
|               | Rechargeable Batteries | 30                           | £2                | £60           |                                                                                                                                     |
|               | Base Station PC        | 1                            | £27               | £27           |                                                                                                                                     |
|               | VPS                    | 1                            | £120 per year     | £120 per year |                                                                                                                                     |
| Medium System | Microbit               | 50                           | £15               | £750          | £1,508 initial and £240 upkeep per year                                                                                             |
|               | Bat:bit3(battery pack) | 50                           | £9                | £450          |                                                                                                                                     |
|               | Rechargeable Batteries | 100                          | £2                | £200          |                                                                                                                                     |
|               | Base Station PC        | 4                            | £27               | £108          |                                                                                                                                     |
|               | VPS                    | 2                            | £120 per year     | £240 per year |                                                                                                                                     |
| Large System  | Microbit               | 150                          | £15               | £2,250        | £3,516 initial and £600 upkeep per year                                                                                             |
|               | Bat:bit3(battery pack) | 150                          | £9                | £450          |                                                                                                                                     |
|               | Rechargeable Batteries | 300                          | £2                | £600          |                                                                                                                                     |
|               | Base Station PC        | 4                            | £27               | £216          |                                                                                                                                     |
|               | VPS                    | 4                            | £120 per year     | £600 per year |                                                                                                                                     |
| Custom System | Microbit               | Mc                           | £15               | Mc*15         |                                                                                                                                     |
|               | Bat:bit3(battery pack) | Bp = Amount of microbits     | £9                | Bp*9          |                                                                                                                                     |
|               | Recargable Batteries   | Rb = Amount of microbits*2   | £2                | Rb*2          |                                                                                                                                     |
|               | Base Station PC        | Bs = Amount of microbits/20  | £27               | Bs*27         |                                                                                                                                     |
|               | VPS                    | VPS = Amount of microbits/40 | £120 per year     | VPS*120       |                                                                                                                                     |
|               |                        |                              |                   |               | £29.25Mc = initial setup cost where Mc is total number of microbits as system = 15Mc + 9Bp + 2Rb + 27B which = 15Mc + 4Mc + 1.35Mc  |
|               |                        |                              |                   |               | £3Mc as VPS is just microbit/40 *120 = upkeep per year where Mc is the amount of microbits in a system                              |

## Total costings Hardware
Small system = £447 initial hardware + £120 Vps upkeep per year 
Medium system = £1,508 initial hardware + £240 Vps upkeep per year		
Large system = £3,516 initial hardware + £600 Vps upkeep per year
Custom system = £29.35 initial hardware per microbit + £3 Vps upkeep per year per microbit

*Note this does not calculate/include the electricity cost as it fluctuates and due to limited testing we do not know how much would be consumed 
