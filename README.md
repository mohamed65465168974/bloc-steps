# bloc-steps

## create states
### 1 - create abstract class 
### 2 - extend states from this abstract class
### 3 - each inherited state can recieve a variable value form ....
## create events
### 1 - create abstract class 
### 2 - extend events from this abstract class
### 3 - each inherited event can recieve a variable value form ....
## connect event to state
### 1 - create class extending from bloc class 
### 2 - pass initial state object to the parent class 
### 3 - in the body of the child constructor call ```on``` method which registers one event handler for each event 
### 4 - create an event handler method for each event which takes different event classes and abstract state class 
### 5 - store current state inside a variable 
### 6 - check if state is equal to a specific state then you can emit a new state with the modification required 
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



