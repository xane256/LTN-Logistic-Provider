# LTN-Logistic-Provider
A factorio blueprint design for a sophisticated train stop that can dynamically provide many items of different types to trains loading cargo. Please see [Very Important Settings](#very-important-settings) below before using.

# What is This?
- This is a provider train stop designed for use with LTN and AAI Containers (Space Exploration) that can provide many items via logistics bots to a train where LTN handles all the requests and decides what items to request on the train. The design has gone through several iterations of revision on the circuit design and many hours of successful playtesting and tweaking in a space exploration world.
- Requester stops can request many items at once with LTN. The LTN Scheduler can use this train stop to provide whatever items are in your logistics mall / network.
- This station pairs well with the multi-item requester in the same book.

# Requester Station
- There's a requester station that is useful for building factories or making temporary requests. Reuquests go in 2 sets of combinators. Green wire = temporary (one-time) requests. Useful for requesting buildings or buiding materials. Red wire = recurring requests that try to keep the logistics network stocked. USE POSITIVE NUMBERS FOR REQUESTS.
- There's a constant combinator that emits the signal R with value 1 when it is switched on.
- There's a memory cell that trakcs what has been dropped off. To reset the meory state, switch the R combinator on and then off again.

# Provider Configuration Settings
- Open the constant combinator next to LTN lamp after pasting, and switch it from "OFF" to "ON"
- Set the min / max number of cargo wagons you want to use. Default is "min length 2, max length 5" which does 1-wagon up to 4-wagon trains. You can go longer by copy-pasting the tileable section for each train car (copy an 8-wide section including lamps / poles at both ends, and tile so that lamps connect together).
- Connect the passive provider chest to logistics network storage with a GREEN wire. More advanced option is to pre-process the input signals instead of just connecting a roboport, to "reserve" items that should be kept available in the logistics network. This is useful if you also have a requester attached to the same logistics network and you don't want the provider to make trains move items from the network back into itself. The trick is to use a constant combinator to define how much you want in the network, multiply by -1, and add to logistics contents from roboport. If its positive, it can go to the provider which means items are available to export. If its negative, it means the network has too little and the provider won't provide that item. 
- COOLDOWN SETTING: There's a cooldown timer that kicks out the train after an inactivity cooldown. The train leaves the station if the cooldown timer ever counts up to the threshold number of ticks, and the cooldown timer resets to 0 whenever the cargo inventory changes. There are two combinators that control the timeout threshold used to kick out a train after an inactivity cooldown. The first one is the decider that outputs a GREEN signal. The second one is a decider two tiles to the side that outputs a T signal. The default cooldown is 600 ticks or 30 seconds. The cooldown should be long enough that a bot carrying any item to a requester / buffer chest has time to get and drop off the item from wherever the item is stored. But it also sets the amount of time the train sits in the station before leaving if its requests can't be 100% fullfilled.


## Very Important Settings
- PERFECT LOADING IS IMPOSSIBLE / INFEASIBLE: If you request 2 stacks of iron for example, and LTN schedules this stop to provide it, you will get a half-stack in each of 4 wagons assuming it uses a 4-wagon train. This is fine but it demonstrates that wagon slots in general will be under-utilized. If you request a bunch of items of mixed types and quantities, this fact means the train might not get all the items requested because LTN may assume optimally dense slot usage. Therefore in the schedule LTN generates for the train, the train might fail the loading condition of getting all the items it wants, and it will get stuck. There are 3 possible fixes for this:
  1. Only use 1-wagon trains with this stop
  2. Change the LTN stop timeout setting
  3. (This is what we did) Change the LTN setting “Schedule Circuit Conditions” to True. That way the train will be kicked out by a cooldown timer circuit that the stop uses (it sends the GREEN signal to the "LTN Train Stop Input" aka lamp). **MAJOR DOWNSIDE** is you have to connect every LTN station to something (lamp / power pole) with a wire so every LTN stop on every planet / surface has a circuit connection. This is easy enough to do with navigation satellite where you can connect train stops to power poles with free red or green wires.


# FAQ
Q: There's items leftover in inserter hands after trains leave! Are my trains at risk of getting incorrect items?
A: No. This behavior is unusual (due to stack size signals) but totally normal. The items will get loaded and unloaded from the next train before that train leaves the station.

Q: Why are there buffer chests and requester chests?
A: This addresses a behavior where a small number of rare items are requested, and there are so few available that due to division / modulus math, 8 out of 10 train-loading inserters are disabled even though the chest behind has items. The fix used here is to use bots to move the items from green chests to blue chest. Another fix that is very likely to work just fine is to connect all 10 train-loading inserters together with a red wire, repeat for each cargo wagon.

# Fun Facts
- If you set up a train schedule yourself and route the train to the provider, any item conditions like "Iron >= 234" will get output to the LTN stop output (yellow combinator) when the train is on the way to the station. If you are already at the station and want to update the signal you'd have to tell the train to go to some other stop then back to the provider to reset the station's requests to match the new schedule conditions. This is kinda useful if you as the player want to move a couple wagons of stuff around manually (play-tested) or if you want to use a non-LTN train with this provider (not play-tested).
- I used techniques I learned from [this video](https://youtu.be/anbbAq--ewU) by @fooluaintblack to set item filters and hand stack sizes for inserters which greatly improved the precision of the design.

