# bloc-steps summary 

## create states
###### 1 - create abstract class 
###### 2 - extend states from this abstract class
###### 3 - each inherited state can recieve a variable value form .... remote or local repo 
## create events
###### 1 - create abstract class 
###### 2 - extend events from this abstract class
###### 3 - each inherited event can recieve a variable value form .... widget triggering event 
## connect event to state
###### 1 - create class extending from bloc class 
###### 2 - pass initial state object to the parent class 
###### 3 - in the body of the child constructor call ```on``` method which registers one event handler for each event 
###### 4 - create an event handler method for each event which takes different event classes and abstract state class 
###### 5 - depending on the event handler type which can be used for 
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
###### 1 - wrap material app with multi bloc provider 
###### 2 - use bloc provider to create the bloc object so that a single instance of a bloc can be provided to multiple widgets within a subtree.
###### 3 - pass it an initial event object 
## update UI based on state 
###### 1 - wrap widget updating state on the ui with a bloc builder widget 
###### 2 - data type will be object from the bloc and state 
###### 3 - check if the state is still loading so we can show circular progress indicator 
###### 3 - check if the state is loaded so we can show the actual data  
###### 4 - modify the data so so it reflects the data coming from state   
## take data input from ui as an event 
###### 1 - wrap widget triggering event on the ui with a bloc builder widget 
###### 2 - data type will be object from the bloc and state 
###### 3 - when the user press a button use context to read the bloc 
###### 4 - add event to it and pass the event the data it needs (ex: object)

*********************************************************************************************

# bloc-steps in details 

## create states
### 1 - create abstract class 
```dart
abstract class WishlistState extends Equatable {
  const WishlistState();
  @override
  List<Object> get props => [];
}
```
### 2 - extend states from this abstract class
```dart 
class WishlistLoading extends WishlistState {}

class WishlistLoaded extends WishlistState {
  final Wishlist wishlist;
  const WishlistLoaded({this.wishlist = const Wishlist()});

  @override
  List<Object> get props => [wishlist];
}

class WishlistError extends WishlistState {}
```
### 3 - each inherited state can recieve a variable value form .... remote or local repo 
## create events
### 1 - create abstract class 
```dart
abstract class WishlistEvent extends Equatable {
  const WishlistEvent();
  @override
  List<Object> get props => [];
}
```
### 2 - extend events from this abstract class
```dart
class LoadWishlist extends WishlistEvent {}

class AddProductToWishlist extends WishlistEvent {
  final Product product;
  const AddProductToWishlist(this.product);
  @override
  List<Object> get props => [product];
}

class RemoveProductFromWishlist extends WishlistEvent {
  final Product product;
  const RemoveProductFromWishlist(this.product);
  @override
  List<Object> get props => [product];
}
```
### 3 - each inherited event can recieve a variable value form .... widget triggering event 
```dart
BlocBuilder<WishlistBloc, WishlistState>(
                builder: (context, state) {
                  return IconButton(
                    onPressed: () {
                      context
                          .read<WishlistBloc>()
                          .add(AddProductToWishlist(product));
                    },
                    icon: Icon(Icons.favorite),
                    color: Colors.black,
                  );
                },
              ),
```
## connect event to state
### 1 - create class extending from bloc class 
```dart
class WishlistBloc extends Bloc<WishlistEvent, WishlistState> {
  WishlistBloc() : super() {
  }
```
### 2 - pass initial state object to the parent class 
```dart
class WishlistBloc extends Bloc<WishlistEvent, WishlistState> {
  WishlistBloc() : super(WishlistLoading()) {
  }
```
### 3 - in the body of the child constructor call ```on``` method which registers one event handler for each event 
```dart
class WishlistBloc extends Bloc<WishlistEvent, WishlistState> {
  WishlistBloc() : super(WishlistLoading()) {
    on<LoadWishlist>(_onLoadWishlist);
    on<AddProductToWishlist>(_onAddProductToWishlist);
    on<RemoveProductFromWishlist>(_onRemoveProductToWishlist);
  }
```
### 4 - create an event handler method for each event which takes different event classes and abstract state class 
### 5 - depending on the event handler type which can be used for 
  - loading data 
    - use a sync and await 
    - emit a state for loaing data 
    - after one second emit a state that data is loaded 
    - catch errors in a state 
    ```dart
    void _onLoadWishlist(LoadWishlist event, Emitter<WishlistState> emit) async {
    emit(WishlistLoading());
    try {
      await Future<void>.delayed(const Duration(seconds: 1));
      emit(WishlistLoaded());
    } catch (_) {
      emit(WishlistError());
    }
  }
     ```
  - adding data 
    - store current state inside a variable 
    ```dart
    final state = this.state;
    ```
    - check if state is equal to a specific state then you can emit a new state with the modification required which is added data from an event 
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
  - removing data
    - store current state inside a variable 
    ```dart
    final state = this.state;
    ```
    - check if state is equal to a specific state then you can emit a new state with the modification required which is removed data from an event 
    ```dart
      void _onRemoveProductToWishlist(
      RemoveProductFromWishlist event, Emitter<WishlistState> emit) {
    final state = this.state;
    if (state is WishlistLoaded) {
      try {
        emit(
          WishlistLoaded(
            wishlist: Wishlist(
                products: List.from(state.wishlist.products)
                  ..remove(event.product)),
          ),
        );
      } on Exception {
        emit(WishlistError());
      }
    }
  }
    ```
## create an object from the bloc and pass it an initial event object 
### 1 - wrap material app with multi bloc provider 
```dart
return MultiBlocProvider(
      providers: [
        
      ],
      child: GetMaterialApp(
        debugShowCheckedModeBanner: false,
        title: 'Flutter',
        theme: ThemeData(
          primarySwatch: Colors.blue,
        ),
        home: HomeScreen(),
      ),
    );
```
### 2 - use bloc provider to create the bloc object so that a single instance of a bloc can be provided to multiple widgets within a subtree.
### 3 - pass it an initial event object 
```dart
return MultiBlocProvider(
      providers: [
        BlocProvider(create: (_) => WishlistBloc()..add(LoadWishlist()))
      ],
      child: GetMaterialApp(
        debugShowCheckedModeBanner: false,
        title: 'Flutter second commit',
        theme: ThemeData(
          primarySwatch: Colors.blue,
        ),
        home: HomeScreen(),
      ),
    );
```
## update UI based on state 
### 1 - wrap widget updating state on the ui with a bloc builder widget 
### 2 - data type will be object from the bloc and state 
### 3 - check if the state is still loading so we can show circular progress indicator 
### 3 - check if the state is loaded so we can show the actual data  
### 4 - modify the data so so it reflects the data coming from state   
```dart
body: BlocBuilder<WishlistBloc, WishlistState>(
        builder: (context, state) {
          if (state is WishlistLoading) {
            return Center(
              child: CircularProgressIndicator(),
            );
          }
          if (state is WishlistLoaded) {
            return GridView.builder(
                padding: EdgeInsets.symmetric(horizontal: 8.0, vertical: 16.0),
                gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
                    crossAxisCount: 1, childAspectRatio: 1.7),
                itemCount: state.wishlist.products.length,
                itemBuilder: (BuildContext context, int index) {
                  return Center(
                    child: ProductCard(
                      product: state.wishlist.products[index],
                      widthFactor: 1.1,
                      isWishlist: true,
                    ),
                  );
                });
          } else {
            return Text('something went wrong');
```
## take data input from ui as an event 
### 1 - wrap widget triggering event on the ui with a bloc builder widget 
### 2 - data type will be object from the bloc and state 
### 3 - when the user press a button use context to read the bloc 
### 4 - add event to it and pass the event the data it needs (ex: object)
```dart
BlocBuilder<WishlistBloc, WishlistState>(
                builder: (context, state) {
                  return IconButton(
                    onPressed: () {
                      context
                          .read<WishlistBloc>()
                          .add(AddProductToWishlist(product));
                    },
                    icon: Icon(Icons.favorite),
                    color: Colors.black,
                  );
                },
              ),
```

