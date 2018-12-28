# Teeny-Video-Web-Application

# SCOPE

Our team’s project is Teeny Video Web Application. We set up a web site, and its target is to support a platform to apply resources for both users who have dreams of becoming actors or actresses and film companies and directors who are trying to find “stars”. 

The idea comes up with a movie, LA LA Land. LA is a magic city, many people have a dream to become a “super star” and give their own performance to all the audience. As we known, USC, which is located near Hollywood, has the best school of Cinematic Arts, it has abundant resources. According to all these materials, we want to build a web application to help providing opportunities to people, who want to show their performance and find a job as an actor or actress. And movie industry is becoming international and genera. American movies need Asian actors and vice versa, our platform offers a repository for groups to seek for their most ideal actors. 

We have two parties: actors or ordinary people whoever want to be an actor; companies or any other form of groups who is seeking actors. Not like the business of other video websites on which you can share whatever you want.

But we want something different from the ordinary steps of web implementation. Some “magic” may be involved in this project. So we come up with a idea that we want to build a utilized component, which fits almost all the operations between the frond-end and back-end. This solution can significantly reduce the total amount of codes we need to write. And ease the way to build a web application. Only some small amount of parameters and logic we need to set, then we can use this new type of architecture web application freely.

# Architecture Web

![Alt text](graph-1.png?raw=true "Title")    

The architecture of our web application is shown above.

# Implementation

There are a lot web sites out there, the basic common functionality is viewing, uploading, searching and managing the videos and users information, namely the CRUD operations on database and operations on file I/O.

Here are some of our decisions:
We apply MVC pattern to the frontend. Every webpage has a model, which contains the data structure from backend which is needed to be parsed to render the page and data structure needed to carry the parameter sent to backend. We use pure html5 instead of most other popular technologies such as JSP, freemarker, velocity in order to improve efficiency. We use pure asynchronous request to improve the coherency of the operations on webpage, and the data format is JSON only, which makes the traffic load smallest.

We apply MVC pattern to the backend and use the most popular frameworks (spring and hibernate) to support the pattern. We didn’t choose myBatis or any other ORM frameworks because we intend to build a totally generic backend, meaning that we don’t write code about specific business (based on entity). These frameworks, except hibernate, enforces to write SQL statements, which don’t allow us to build a high level generic business logic in backend.

The most novel parts are the generic components. Traditionally, we need controllers, services, DAOs to fulfill the functions for every module/entity. And those modules may have similar CRUD operations. This duplicated code mode is our cut point. We decided to build a generic controller, service and DAO to enhance the code reusability most. As a result, the code amount will be reduced by about 70%.

Here is a snapshot of a normally implemented system:

![Alt text](graph-2.png?raw=true "Title")    
Here is the hierarchy of our system

![Alt text](graph-3.png?raw=true "Title")    
Here is the real implementation of hierarchy


The approach how we achieved this is that we utilized the reflection and generic features of Java:

Every time front-end sends a request to the back-end, the request carries a parameter used to specify to which module the CRUD belongs. And the back-end uses this parameter to do the reflection. Controller receives the parameters, and decides which module the operation will belong to by iterating the classes constant which are predefined matching the class with the parameter. Then, wrap the JSON data from the frontend into an actual entity and call service function. 

At generic service layer, the instance of service get the entity and can gain the knowledge about the entity and module. At this layer, we also judge whether there is file stream which is needed to write into the disk.

At DAO layer the final CRUD operations happen.

# Configure the system

As for the constant of the system, such as state list, city list, and user type list, we persist it in database instead of configure it in the system code, because we think even it is constant, it still might change at some time. And we cache it by using the second level cache of hibernate to improve the efficiency.

# AOP

We originally plan to use AOP to deal with some special transactions by adding AOPs onto generic service function, such as peculiar queries. But we think this decision will finally lead to architecture erosion as the system grows. Because whenever we have some special operations we have to add an AOP on the method. As a result, all the general procedural calls have to check every AOP aspect to decide whether they need to execute the AOPs, meaning that this side-effect will take a lot of unnecessary time.

# Drawback

The architecture, applying the generic layer, takes more time compared to the one, which calls procedure directly to actual service and database, doesn’t use this kind of extra layer. Transforming data, requests and response have more complex layers and operations, since it needs to check whether it fits any model it has and it should be able to figure out which service to apply. 
