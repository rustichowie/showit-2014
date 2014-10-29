ScenePlanNext
=============

## Documentation

### Introduction
This is a Student project that has been developed as part of Knowits "Sommer Interns" program. The application is a event/project planning tool for [Carte Blanche](http://www.carteblanche.no/) and other companies in the culture domain. We have been asked to create a new version of their old system which Knowit developed some years ago. This is only a prototype/proof of concept, and will be a reference for future development on the application.

The applications purpose is to help the administrators plan and manage their companies upcoming schedule, and also be able to distribute information about events and projects to those involved. As well as displaying the upcoming schedule digitally, it should be possible to print the different types of schedules. When making a schedule it should be easy to filter out the events of interest.

The following sections will describe the system - including the usecases for each entity, views and functionality of the application.



### Preview
The application is currently up on staging:
[Link](http://sceneplannext.staging01.reaktorutv.no/)

Username: havard.olsen@knowit.no

Password: 123123

### Technology stack
#### Backend
[SQLServer 2008 R2](https://www.microsoft.com/en-us/server-cloud/products/sql-server/)

[Entity Framework 6 - Code-First](http://msdn.microsoft.com/en-us/data/jj819164.aspx)

[ASP.NET MCV 5 + Web Api 2](www.asp.net)

#### Frontend
General: HTML5 / CSS3 / Javascript

[Bourbon](bourbon.io)

[Bourbon Neat](neat.bourbon.io)

[Flat-UI](http://designmodo.github.io/Flat-UI/) - Builds on top of Bootstrap 3.

[React](http://facebook.github.io/react/index.html) - Used in the filter widget

[Fullcalendar](http://arshaw.com/fullcalendar/) - Used on the frontpage

[Datetimepicker](http://xdsoft.net/jqplugins/datetimepicker/) - Used on every datepick inputfield in the app

[Tablesorter](http://tablesorter.com/docs/) - Used on every table in the app

### Patterns and Principles
**Repository pattern** for communicating with the database

**Data Transfer Objects** for sending model entities as json data (Avoiding infinite loops)

**DbContext**:  We use a single DbContext object for all entities.

**Model <--> ViewModel <--> View** for better control and customization of what our views should contain.

### Testing
The application contains a test project, which we intended to use. However we never got around to writing tests, and it doesn't really contain anything useful at the present moment.

### System Description
This section will break down the different parts of the application. It will give a brief description of what the application does, what its main entities are and the logic behind it.

#### Authentication and Handling of users
We have used Entity Framework and Identity 1.0 for authentication and authorization. There are currently two levels of access: Admin and normal user. The only way to add new users is for the administrators to create them. Users then get an email with a link to a "confirm account" page. Here they can set a password and login to the application. This is because it is a closed internal application and administrators should have full control over who uses it.

The link expires after 24 hours so the user becomes inactive after that. Currently there is no way to send out a new mail or reactivate the account after the 24 hours, so in this case one must delete the account and then recreate it. A way for admins to reactivate the account on their site should be implemented in the future.

Normal users can only see their own data + events they are connected to. They can also change password, and edit some limited information about themself (Name, email, phone). This functionality can be changed based on requirement.

#### Entities in the system
The following section will describe the main building blocks of SceneplanNext - its entities. You can read about all the differerent entities, what properties they have and (in relevant cases) examples of their use. 

##### Events
SceneplanNext is centered around events, trying to keep track of them in reasonable ways. For an administrator/coordinator this gives a good overall view of what is going on in the specific company, for a normal user it helps them keep track of their schedule. It is important for administrators to know who are doing what and at what time. It is possible to keep track of this by adding users to the company events, which will make administrators able to see the schedule for every user and see who the available personnel are. 

While administrators are still planning, they might create unfinished events, or events that will most likely change later. It is not beneficial for them to show these events to other users because it may lead to inconsistent information. The application tries to solve this problem by letting administrators have unpublished events which they can later publish.
 
##### Projects
Projects in SceneplanNext are used to group collections of related events. You cannot create events which doesn't belong to a project. An example of this at Carte Blanche may for instance be a production or a performance. Administrators using the application will be working primarily with projects as they are the systems top-level component. They will start by creating a project and add new events to this project during planning. Some benefits this workflow gives the administrator is the ability to publish events based on project, print out a plan for that project, and it also helps with keeping the application structured.

##### Users
Users are the people that actually use SceneplanNext. They are expected to be employees of the companies that acquire the product. Users will be assigned a group (Technical support, artist, developer etc..). By using groups we for example make it possible to get events for all the artist. This also gives us the opportunity to quickly add an entire group to an event instead of adding the users one by one. The latter feature is not yet implemented, but is a feature that can be added in the future.

###### Administrators
Administrators have access to all of the different parts of the system. They can view all projects, events and users, not needing to be specifically associated with any of them. Administrators are also allowed to create, update and delete the aforementioned types of entities, in addition to entities related to the former entities (e.g. project categories).  

###### Normal users
Normal users only have a limited access to SceneplanNext, as described in the section about "Authentication and Handling of users". 

##### Contacts
Contacts are people that are external to the company using ScenePlanNext. These people may contribute to projects and events, and they may also be associated with one or more Locations, yet they do not have their own user login to the system. The system keeps track of their contact information, for instance making it easy to find the contact details and get in touch with the contacts. In addition to name, phone number and email address, the contacts can also be given a user group, which will make it easier to see what type of role they have. Contacts for instance allow administrators to store information about someone working at a hotel that the company stays at when traveling, or about key persons that doesn't need access to the application.

##### User Groups
User Groups are a way of strucuring users and contacts. User Groups have a name and a description. They are meant to give a high level overview of the job type of the user/contact. Examples of relevant user groups in the culture domain are "Administration", "Technical", "Artist" (alternatively Artist can be split up into more descriptive names such as "Dancer" or "Painter"). 

##### Event Categories
Event categories are, as the name implies, used to categorize events. Every event is categorized with one event category. Event categories have a name and a description. Using an event category it is possible to group together events that are related, yet part of different projects, such as meetings, rehearsals and performances. 

##### Project Categories
Project categories are used to categorize projects. Every project is categorized with one project category. Project categories may have a name and a description. Using a project category it is possible to group together projects that are related - for instance a project category can be used to group together projects that are all a part of a specific tour. 

##### Venues
Venues are the places where events take place. They have a name and an address. Venues may have varying levels of specificity, depending on user needs. A high-level example of a venue is a city. A more fine-grained example of a venue is a meeting room in a specific building.

##### Shadow Users
Shadow users are placeholders for real users in an event. A shadow user has a name, a description and number. The number says how many users the shadow user is a placeholder for. An example of when you can use a shadow user is when you make a dance performance event and you know you need 5 dancers, but you are not sure which dancers you need. You can then add a shadowuser with name="Dancer" and number = "5". Later, when you have found these 5 dancers, you can remove the shadowuser, and add the 5 actual users instead.

#### Views 
##### FrontPage
The frontpage contains two different ways of displaying events: A calendar, and a list
###### Calendar
The calendar shows all events available for the user. As an administrator you can use the calendar to make quick changes to events, either by changing the name, description or date. You can switch between a monthly, weekly or daily calendar. This makes it possible to quickly change the schedule without having to go in and edit each event. The calendar is only meant to help with small changes, larger or more complex changes to an event should be done by going to the project of an event and editing it there. One thing one might not notice when using the calendar is that it is possible to drag the events between days, and also while in weekly and daily view one can increase/decrease the timeframe.

The Calendar is based on the javascript plugin [Fullcalendar](http://arshaw.com/fullcalendar/).

![alt text](https://github.knowit.no/kyber/ScenePlanNext/blob/master/Web/images/documentation-images/Calendar.PNG?raw=true "Calendar")
###### Eventlist
On the other part of the homepage you will find a list. This list initially contains the last ten events sorted by end date. If there is more then ten events in the database, it it possible to load more by clicking on the "show more events" button. In addition to a list of events, the application need some sort of long term event view. This is solved by allowing users to switch between a montly view and the listview. The monthly view will show the events from the next 6 months sorted on months and days(Note!, this is a work in progress and only added to show how it could look). From the list you can also click on the "Publish Events" button. This will make it possible to publish all events from a given start date to a given end date. A confirmation view will display all the events that will be published. This is useful if you have been planning 3-4 months ahead and want to publish all events in that timeframe.

Jquerys [tablesorter](http://tablesorter.com/docs/) plugin is used to make the eventlist sortable. It is also possible to search and filter events in the table. You can read more about the search and filter functions later in this readme file.

![alt text](https://github.knowit.no/kyber/ScenePlanNext/blob/master/Web/images/documentation-images/Agenda.PNG?raw=true "Agenda")
##### Projects
On the projects site you will find a list of all projects in the system, a search field and a create new project function. It is possible to click on a project to get more information about it and to see all the associated events in a table. The table is filterable and searchable and the user can get additonal information about a specific event by clicking on it in this table. Because every event is connected to exactly one project it is only possible to create a new event through a project. When displaying details for a project it is also possible to publish all events for this project. This can be achieved by clicking on the "publish events" button, which will display a confirmation page showing a list of all the events that will be published.

This picture shows the flow from list of projects, to details about one project, to details about one event in that project: ![alt text](https://github.knowit.no/kyber/ScenePlanNext/blob/master/Web/images/documentation-images/ProjectList.PNG?raw=true "ProjectFlow")

##### Users
On the users site you will find a list of all users in the system, a search field and a create new user function. It is possible to click on a user to get more information about the user and to see the list of events the specific user is participating on.  

This picture shows the flow from list of user to details about one specific user: ![alt text](https://github.knowit.no/kyber/ScenePlanNext/blob/master/Web/images/documentation-images/UserFlow.png?raw=true "UserFlow")

##### To Be Answered (Support Data)
"To be answered" gives access to administration of some different entities; more specifically contacts, user groups, event categories, project categories and venues. The entities were placed in this view as they are not considered to be a part of the core functionality of SceneplanNext. As the name implies this name should be changed to something else in the future. As of now we have not found an optimal name for this view. Navigating through this view it is possible to create, edit and delete the specific type of entities. All entity types in the view have their own search that can be used for filtering them, as described below. 

A picture of the user group entity overview in "To be answered": ![alt text](https://github.knowit.no/kyber/ScenePlanNext/blob/master/Web/images/documentation-images/Tba.PNG?raw=true "Tba")

#### System functionality

##### Contact List
The application gives administrators the possibility of creating a contact list for users and contacts that are associated with a project. The contact list can be seen when looking at the detail view of a specific project. It is also customized for printing. The contact list displays the name, phone number, email and user groups of the users and contacts that are listed. Users and contacts that are members of one or more events in a project are automatically added to the projects contact list. They can not be removed from the contact list without also removing them from all their events that belong to the project. Contacts can also be added to the specific project (not needing them to be a member of any associated event) - either when creating/editing the contact, or by clicking "Edit contact list" for the specific project. When editing the contact list it is possible to select from all contacts that are not a member of any of the project's events by clicking on the "Unassigned contacts button", to associate them with the project. Clicking on the plus icon beside their name will add them to the project. Clicking on the minus icon (beside their name) will remove them from the project. Contact names are displayed in italics font, whereas user names are displayed in normal font. For changes to be saved the "Confirm contact list" button must be pressed.

here is a picture of the contactlist to a project: ![alt text](https://github.knowit.no/kyber/ScenePlanNext/blob/master/Web/images/documentation-images/ContactList.PNG?raw=true "ContactList")

##### Filter Application
SceneplanNext comes with a Filtering application, which makes it possible to filter out events based on every property associated with them (Category, Project, Users, TimeFrame, UserGroups). The application is created using [React](http://facebook.github.io/react/index.html) and some basic Jquery. Based on what you select the application sends a request to the server and gets back the corrosponding events without refreshing the entire page. You can select/unselect items by dragging them between the two lists, or just by clicking on them.

Here is a picture showing the filter application: ![alt text](https://github.knowit.no/kyber/ScenePlanNext/blob/master/Web/images/documentation-images/FilterApp.png?raw=true "Filterapp 1")

##### Api
The application has a simple API which exposes lists of entities and PUT requests for updating an event. In the application the only use for this is to send json data to the filter application, and the calendar. The update actions are used by the calendar.

The filter application is used several places in the application: Home page, user page and project page. Because of this we have customized the lists of items to filter by to suit each page. For example on the user page, we can only see projects, locations etc. which the user has a relation to (i.e has events belonging to that project, or in that location). The same goes for project.

##### Printing
The application makes it possible to print out the list of events (Ctrl + P). The nice part about this is that you print exactly what is on your screen (with different formatting). This means that if you search for something, filter, specify date etc, the print will then only show these events. 

Currently this is only implemented for what we call a "project plan", which splits events up based on day. You can do this on all pages that displays a list of events, but it is mostly meant to be a part of the project page.

The contact list is also available when printing the specified project plan, showing users and contacts for the specified project.

We have started working on a simpler formatting for printing longterm plans (half a year ++), which shows less information and and the plans are grouped by month. On the frontpage(List) you can switch to monthly view and checkout the current solution. The printing is customized to fit on a A3 page.

There has been talk about creating a weekly schedule, but we wanted to keep this digital and purhaps make it responsive enough to fit on infoscreens.

##### Search
A simple search has been added for projects, users, events, venues, project categories, user groups, event categories and contacts. It can, in addition to the filter application, be used for filtering. The search is case insensitive and it is possible to match words partially (for instance the search string "now" will match the word "knowit"). The search is context dependent: For projects the search will match project name and names of events associated with the project. For users the search will match the name of the user and the title of the user. For events the search will match event names. For venues the search will match venue name and venue address. For project categories the search will match project category name and project category description. For user groups the search will match user group name and address. For event categories the search will match event category name and description. For contacts the search will match contact name and description.

##### Repeatable Events
It is possible to make more than one event at the time. If you have an event that is going to repeat itself multiple times, you can make them all at once. To repeat an event you need to click on the repeat button in the create event view. When a user clicks on the repeat button checkboxes for the weekdays and a selectlist with numbers from 1 to 30 will appear. The first event will have the timeframe you set in the timeframe boxes, and the next events will be identical to the first event, just with different dates, depending on what checkboxes has been checked out. For example if you want an event to happen every weekday for the next two weeks, you check out monday to friday and set the number to 10. 

Here is a picture showing the view for repeating events: ![alt text](https://github.knowit.no/kyber/ScenePlanNext/blob/master/Web/images/documentation-images/Repeat.PNG?raw=true "Repeat")

### Future Work / Discussed Ideas
#### Timeline
The Timeline is a concept we came up with late in the project, and we see this as a great tool for this application. The concept is a sort of horizontal view seperated by month. The purpose is that you can drag *elements* onto the timeline to get a full view of every event related to it. *Elements* in this context can for example be a Venue, by dragging it onto the timeline you can see every event that is happening at that Venue. Another example can be a User, to see every event the user is apart of. After you have dragged *elements* onto the timeline, you should be able to edit, delete or create new events for it as a part of the tool.

The main benefit of having the timeline is that it can make planning a lot more efficient. It can be used in planning meetings, and you can edit the plan as you see fit during the meeting. No need to pass around multiple copies of printed out plans, which you then write the changes on and after the meeting someone has to update the digital version.

Here is a picture of an excel page which shows the overall layout we were thinking: ![alt text](https://github.knowit.no/kyber/ScenePlanNext/blob/master/Web/images/documentation-images/timeline.PNG?raw=true "Timeline 1")

#### Aggregate data
Since each user will be signed up for different events you get a pretty good estimate of how much work they put in. This gives us the opportunity to collect data in order to uphold different work regulations such as total workhours per week, and if there is any special regulations like working during weekends we can better plan ahead.

#### Editable Events table
For now it is only possible to search and to filter in the event tables. An editable table, where you can remove, add and swap two rows, would do it possible to print more specified reports. For example if a user want a report with every dance performance except one, it would be possible to filter on dance performance and then remove the one you dont need from the printable table view. 

#### User access on a project level
Instead of giving users access to specific events, users can get access to whole projects including all events accociated with that project. When a user is created it would be decided which projects the specific user would get access to. It should also be possible to add and remove project access after the user is created. 
 
#### Smaller Ideas

- Give venues a website field
- The eventlist on the frontpage can initially load all events from the current month, and then load more if the "load more" button get clicked
- If a user sets the events timeframe outside the associated projects timeframe, the user will get some sort of warning, but it should be possible to make the event anyway. 
- Holidays must be included in the reports
- Users can save information about their next of kin in the system
- It should be possible for users to set the timeframe on the long term printing view function on the frontpage

### Developers
##### Students
Håvard Rustad Olsen - haavard.olsen@live.com

Jeanette Samuelsen - jeanette.samuelsen@gmail.com

Astrid Tessem - astrid.tessem@gmail.com
