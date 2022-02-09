# bloc-steps

## create states
### 1 - create abstract class 
### 2 - extend states from this abstract class
### 3 - each inherited state can recieve a variable value form .... remote or local repo 
## create events
### 1 - create abstract class 
### 2 - extend events from this abstract class
### 3 - each inherited event can recieve a variable value form .... widget triggering event 
## connect event to state
### 1 - create class extending from bloc class 
### 2 - pass initial state object to the parent class 
### 3 - in the body of the child constructor call ```on``` method which registers one event handler for each event 
### 4 - create an event handler method for each event which takes different event classes and abstract state class 
### 5 - depending on the event handler type which can be used for 
  - loading data 
    - use a sync and await 
    - emit a state for loaing data 
    - after one second emit a state that data is loaded 
    - catch errors in a state 
  - adding data 
    - store current state inside a variable 
    - check if state is equal to a specific state then you can emit a new state with the modification required which is added data from an event 
  - removing data
    - store current state inside a variable 
    - check if state is equal to a specific state then you can emit a new state with the modification required which is removed data from an event 
## create an object from the bloc and pass it an initial event object 
### 1 - wrap material app with multi bloc provider 
### 2 - use bloc provider to create the bloc object so that a single instance of a bloc can be provided to multiple widgets within a subtree.
### 3 - pass it an initial event object 
## update UI based on state 
### 1 - wrap widget updating state on the ui with a bloc builder widget 
### 2 - data type will be object from the bloc and state 
### 3 - check if the state is still loading so we can show circular progress indicator 
### 3 - check if the state is loaded so we can show the actual data  
### 4 - modify the data so so it reflects the data coming from state   
## take data input from ui as an event 
### 1 - wrap widget triggering event on the ui with a bloc builder widget 
### 2 - data type will be object from the bloc and state 
### 3 - when the user press a button use context to read the bloc 
### 4 - add event to it and pass the event the data it needs (ex: object)


```dart
  void _onAddProductToWishlist(
      AddProductToWishlist event, Emitter<WishlistState> emit) {
    final state = this.state;
    if (state is WishlistLoaded) {
      try {
        emit(
          WishlistLoaded(
            wishlist: Wishlist(
                products: List.from(state.wishlist.products)
                  ..add(event.product)),
          ),
        );
      } on Exception {
        emit(WishlistError());
      }
    }
  }
```



