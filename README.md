# Factorio-CTS
A Factorio Circuit based train scheduler for generic depot-based trains.

**`This is a work in progress page. The system blueprints has not been released yet as of 2024/4/10. But they will be soon. I basically just need to finish documentation, and decide on the best way to post it.`**

After around 250 hours of work I can finally present version 1 of my circuit based train scheduler. I have been thinking of wanting to make train depots possible for ages, but I never got anywhere because of the complex nature of how (and if) it cound be done, until last september when I decided to just go for it anyway. 
It didn't take long before the prototype started getting somewhere. Slowly the system grew one module and function at a time. And by december I had the first "working" prototype with all core functionality there. Trains, stations and depots could be added, and the system would automatically assign trains once a Sender station and Receiver station of matching cargo was found. Returning the trains to a depot after each schedule.

While at this point it did work, and actually worked pretty well even. There was a LOT of work left.

One massive problem making a system like this is that it is placed inside the game world, and therefor are subject to everything that can happen in the world. Right now it would be enough for the user to accidentally cut the Mains connection  to a station for a split second at the wrong time for the system to basically spiral out of control and require a complete system reset, including replacing all the station cores.

Here is a small list of things that would cause massive problems if performed.
- Peacefully removed a station after a request was sent.
- Breaking a rail preventing a train from reaching its designated target.
- Hijacking a train for a quick lift somewhere else than scheduled.
- Accidentally connecting a station to the Mains wires again after it has been peacefully removed.
- Not having a valid path from a trains current location and the next scheduled stop.
- 
- 

Making the system as resistent to problems as possible has become by far the largest time sink in this project. The first working prototype worked by trust that everything is set up perfectly and nothing is touched after the start. Now months later I think I have got it to a point where it is stable enough to actually be used in game. 

It is still possible to break it enough to require a complete reset, but it should be rather difficult to get to that point. Most things that can break basically requires the user to manually break something. And even then it should be possible to fix most things without requireing a system reset. A restart might be necessary, or manual removal of entries in queues or lists, but again, you as a user would likely never need to go in and fix something. 



## Features

### Stations
Each station consists of one station core, one train stop module, and a custom amount of storage sensors.
Once all pieces has been connected the station can then be enabled, and configured with a requested/sent resource. The station will send a schedule request to the CPU when the storage sensors confirms there are enough resources to fill one train, or there is enough space in storage for one train load.

### Depots
Depots act as the parking spots for trains without orders, doubling as refueling stations. You can never have more trains than depots.

### Service Stations
The service stations are where trains are added and deleted. The user has three different input options. 
- Request a item train to be deleted.
- Request a fluid train to be deleted.
- Request permission to add a new train to the system.

In the case of train deletion the station will contact the CPU and request a train of matching type be deleted from internal memory and sent to the service station to then be handed over to user control. If no train of correct type is found a error is sent and the buttons get enabled again.

In the case of adding a train the CPU will check the total amount of depots in the system, and total amount of trains. If the depot count is higher than total trains then permission is granted and the service station enables itself. The system then enters a paused state until the train is connected. 
At this point the train to be added must be set to automatic and allowed to pathfind to the service station in question.
When the train has arrived the service station checks what type of train it is, and then sends the train information to the CPU. After which the system resumes and the train is sent to either a depot or station.


### CPU
The brain of the system. Contains all logic and memory required for the system to function. Acts as the controller of the mains wires, controlling what gets the communicate and when.
