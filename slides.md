## Functional Relational Programming

### In UI Frameworks ###

_Jon Eisen_, Activision Publishing

LambdaConf

May 28, 2016

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

>>>>>

### What is the ESSENCE of FRP?

- Separation of _state_ from UI and external actions
- Separation of _essential_ and _accidential_ complexity
- Relational data model
- Observers and Feeders are **pure** functions

-----

### UI Design Influences

> I believe modern UI programming models are influenced from the ideas of Functional Relational Programming.

>>>>>

## Timeline Note

- Feb 3, 2006: `jQuery` published
- Feb 6, 2006: _Out of the Tar Pit_ published


-----

### Examples in Web UI

- Redux
- Elm
- Om


-----

#### Redux

Based on _Flux Architecture_:

![flux-arch](flux-simple-f8-diagram-with-client-action-1300w.png)

>>>>>

#### Redux

```javascript
let store = createStore(update) //update: state -> action -> state

// Observer!
store.subscribe(() =>
  console.log(store.getState())
)

// Feeder!
store.dispatch(action)
```

```javascript
// React Component
class Counter extends Component {
  render() {
    return (
      <div>
        <span>{this.props.value}</span>
      </div>
    )
  }
}
```

>>>>>

#### Redux
##### Like FRP?

- Separation of _data_ from _UI_ (**Observers**)
- Separation of _actions_ from _data_ (**Feeders**)
- Pure Data Changes (Reductions)

-----

#### Elm Architecture

Basic Pattern of Elm Architecture

- Model: the state of your application
- Update: a way to update your state
- View: a way to view your state as HTML

>>>>>

#### Elm Architecture

```elm
type alias Model = { ... }
```

``` elm
-- UPDATE
type Msg = Reset | ...

update : Msg -> Model -> Model
update msg model =
  case msg of
    Reset -> ...
    ...

```

``` elm
-- VIEW
view : Model -> Html Msg
view model =
  ...
```

>>>>>

#### Elm

Much like Redux,

- Separation of _data_ from _UI_ (**Observers**)
- Separation of _actions_ from _data_ (**Feeders**)
- Pure Data Changes (Reductions)

-----

#### Om ####

```clojure
;; Read from the data
(defmulti read om/dispatch)

;; Mutate the data
(defmulti mutate om/dispatch)

```

```clojure

(defui Counter
  static om/IQuery (query [this] [{:cntr [:app/counter]}])
  Object (render [this]
           (let [{count :app/counter :as entity}
                 (get-in (om/props this) [:cntr 0])]
      (dom/div
        (dom/span nil (str "Count: " count))
        (dom/button
          #js {:onClick
               #(om/transact! this [[:app/increment entity]])}
          "Click me!")))))

```


>>>>>

#### Om/Datascript

```clojure
(require '[datascript.core :as d])

(def conn (d/create-conn {}))

(d/transact! conn [{:app/id :the-one-counter
                    :app/count 0}])
```

```clojure
(defmethod read :app/counter
  [{:keys [state query]} _ _]
  {:value (d/q '[:find [(pull ?e ?selector) ...]
                 :in $ ?selector
                 :where [?e :app/id]]
            (d/db state) query)})

(defmethod mutate :app/increment
  [{:keys [state]} _ entity]
  {:value {:keys [:app/counter]}
   :action (fn [] (d/transact! state
                    [(update-in entity [:app/count] inc)]))})
```

>>>>>

#### Om/Datascript

Just like Elm and Redux:

- Separation of _data_ from _UI_ (**Observers**)
- Separation of _actions_ from _data_ (**Feeders**)

But now...

- **Relational** and **Pure** data changes!

-----

## Conclusions

- The ideas of FRP were not popular when published
- But the basic architecture is visible in today's UI frameworks
- Om/datascript is the most popular embodiment of FRP

>>>>>

### Does FRP solve the _Software Crisis_?

<img src="tyson-cold.jpg" height=300>

Probably Not.

-----

## Thanks

_Jon Eisen_

http://joneisen.me

Enjoy LambdaConf!
