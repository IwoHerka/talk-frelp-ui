## Functional Relational Programming

### In UI Frameworks ###

_Jon Eisen_

LambdaConf

May 26, 2016

http://joneisen.me/r/frpui

-----

## Contents

- What is FRP?
- Examples in modern UI Frameworks
- Why it matters

-----

## What is FRP?

_Functional Relational Programming_ was defined in _Out of the Tar Pit_ (2006 Marks and Moseley).

> In FRP all essential state takes the form of relations, and the essential logic is expressed using relational algebra extended with (pure) user defined functions.

>>>>>

### What is FRP? ###

> The primary, overriding goal behind the FRP architecture is **elimination of complexity**.

>>>>>

#### What is FRP? ####

First of all, its a **Relational Model** using the **Relational Algebra**.

Relational Variables:

```
def relvar Room :: { address:address roomName:string
    width:double breadth:double type:roomType }

def relvar Offer :: { address:address offerPrice:price
    offerDate:date bidderName:name bidderAddress:address }
```

(All examples come from a real estate example in _Out of the Tar Pit_)

>>>>>

#### What is FRP?

Derived variables (views):
```
RoomInfo = extend(Room, (roomSize = width*breadth))

RawSales = project_away(join(Acceptance,
    join(CurrentOffer,
        project(Property, address agent
            dateRegistered))),
                offerDate bidderName bidderAddress)
```

>>>>>

#### What is FRP? ####

Relational Constraints:

```
candidate key Room = (address, roomName)

foreign key Room (address) in Property

count(restrict(PropertyInfo | numberOfRooms < 1)) == 0
```

>>>>>

#### What is FRP?

Specifying Accidental Complexity:

```
# Hint to denormalize Room and Floor in storage
declare store shared Room Floor

# Hint to store large blobs separately
declare store separate Property (photo)
```

>>>>>

#### What is FRP?

Declarative statement of _effects_:

- **Feeders**: Modify the underlying state on input.
- **Observers**: Listeners on a relation that show export data or update UI

(These notions are very underdeveloped in the paper)

-----

### What is FRP?
#### TL;DR

- Relational model specifies essential state
- Relational algebra specifies interesting views on state in terms of relvars
- Observers listen on a view to update UI
- Accidental complexity is pushed to declarative infrastructure

-----

### Examples in UIs

????
