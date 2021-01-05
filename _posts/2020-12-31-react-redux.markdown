---
layout: post
title:  React Redux
date:   2020-12-10 08:51:00 -0600
---

The next step of the redux journey was to build out [react-redux](https://react-redux.js.org/). Feel free to have a more detailed
look at my code [here](https://github.com/avni510/react-website). React Redux allows us to
inject the state into our components. I also decided to inject in commands which are in charge of dispatching actions
and making an API call. Dispatching actions will update our store. I'll explain this further in another blogpost, but right now we
want to inject in the state and a container that contains commands into our components. In this example here are the types
for `State` and `AppContainer`.

```typescript
export type AppContainer = {
  photoCommands: PhotoCommands
};

export type State = {
  photos: PhotoState
};

export type PhotoState = {
  isLoading: boolean,
  photo?: string
};
```

We're going to utilize react context in order to do that.

Context is part of react. It's an object that is available to components at any part of the component tree. More info can be found [here](https://reactjs.org/docs/context.html). 
This allows us to avoid having to pass down state from the top level component and through the entire chain. Instead we can utilize the context and add the state and the container
straight into the context. I decided to create a component that will add the state and container to the context. I call it `Provider`.

```javascript
 type Props<TContainer, TState> = {
   container: TContainer
   store: Store<TState>
 }

 export const ContainerContext = React.createContext({});
 export const StoreContext = React.createContext({});

 export default class Provider<TContainer, TState> extends React.Component<Props<TContainer, TState>, {}> {

   public render() {
     return (
       <StoreContext.Provider value={this.props.store}>
         <ContainerContext.Provider value={this.props.container}>
           {this.props.children}
         </ContainerContext.Provider>
       </StoreContext.Provider>
     )
   }
 }
```

The top level react component now just needs to be wrapped in `Provider` and then our state and container will be available to use through `context`. 
```
export default class App extends React.Component {
  private setup: Setup;

  constructor(props: any) {
    super(props);
    this.setup = new Setup();
  }

  public render(): JSX.Element {
    const store = this.setup.executeState();
    const container = this.setup.executeContainer(store);
    return (
      <Provider
        store={store}
        container={container}
      >
        <Router>
          {routes}
          <Landing />
        </Router>
      </Provider>
    );
  }
}
```

To consume the context we would call
```javascript
<StoreContext.Consumer>
  {store => {
       // component here 
    }
  }
</StoreContext.Consumer>
```

and 

```javascript
<ContainerContext.Consumer>
  {container => {
       // component here 
    }
  }
</ContainerContext.Consumer>
```

In order to try to contain this complexity in one spot, a component can pass in arguments to a function about which parts it needs from the state and container. The function can then
inject those into the caller component. Let's say the component has these `Props`

```javascript
type Props = {
  isLoading?: boolean,
  photo?: string,
  photoCommands?: PhotoCommands
}
```

`isLoading` and `photo` come from the state and `photoCommands` comes from the container. The component that has these props is called `PhotoTest`. We're going to create a helper function called
`connect` which will take in the target component - `PhotoTest`, a function that will inject in specific props from the container, and a function that will inject in the specific props from the state.
`connect` will return a new component that has the appropiate values of the props injected in.

```
export default connect(
  PhotoTest,
  injectCommands<AppContainer, Props> (c => ({
    photoCommands: c.photoCommands
  })),
  injectState<State, Props>((state, props) => {
    const isLoading = state.photos.isLoading
    const photo = state.photos.photo
    return {
      isLoading,
      photo
    }
  })
)
```

Here `injectCommands` is in charge of taking the `Target` component (`PhotoTest`) and injecting in `photoCommands` as a prop for it use. `injectState` is in charge of taking the `Target` component 
(`PhotoTest`) and injecting in `isLoading` and `photo`. 

Here comes the confusing bit. `injectCommands` and `injectState` are higher order functions themselves. They take in a function that maps the container to props in the `Target` component. It returns another function
that takes in the `Target` component and returns a new component. This new component has the existing values for the props of the `Target` component, but it also fills in values for the props that are needed from the container
and state respectively. 

Here's the implementation for `injectState`.

```
export type Connector<TProps> = (c: ComponentClass<TProps>) =>
  ComponentClass<TProps>

export type StateInjector<TGlobalState, TProps> = (state: TGlobalState, props: TProps) =>
   Partial<TProps>

/*
 * This returns a function that takes in a component and injects in 
* the appropiate state variables into the component as props
 */
export function injectState<TGlobalState, TProps>(func: StateInjector<TGlobalState, TProps>): Connector<TProps> {
  return (Target: ComponentClass<TProps>) => {
    return class injectStateHOC extends React.Component<TProps, ComponentState> {
      public render(): JSX.Element {
        return (
          <StoreContext.Consumer>
            // values from the state are passed in here as props
            {store => (<Target
                {...this.props}
                {...this.inject(store as Store<TGlobalState>)}
              />)
            }
          </StoreContext.Consumer>
        )
      }

      private inject(store: Store<TGlobalState>): Partial<TProps> {
        if(!store) {
          throw 'Store not found'
        }

        /* 
        // this is the function specified in the target component. Ex:
        //  (state, props) => {
        //     const isLoading = state.photos.isLoading
        //     const photo = state.photos.photo
        //     return {
        //       isLoading,
        //       photo
        //     }
        //   }
        */
        return func(store.getState(), this.props)
      }
    }
  }
}
```

The implementation for `injectCommands` is pratically identical. 

```javascript
export type CommandsInjector<TContainer, TProps> = (container: TContainer) =>
  Partial<TProps>

export function injectCommands<TContainer, TProps>(func: CommandsInjector<TContainer, TProps>): Connector<TProps> {
  return (Target: ComponentClass<TProps>) => {
    return class injectHOC extends React.Component<TProps, ComponentState> {
      public render(): JSX.Element {
        return (
          <ContainerContext.Consumer>
           // values from the container are passed in here as props
            {container => (<Target
              {...this.props}
              {...this.inject(container as TContainer)}
            />)
            }
          </ContainerContext.Consumer>
        )
      }

      private inject(container: TContainer): Partial<TProps> {
        if (!container) {
          throw 'Container not found'
        }
       /* 
       // this is the function specified in the target component. Ex:
       // (c => ({
       //   photoCommands: c.photoCommands
       // })),
       */
        return func(container)
      }
    }
  }
}
```

So now we're able to take a `Target` component and give it values from the state and container through the `context`. 

Based on the fact that `injectState` and `injectCommands` follow the same higher order function pattern (returns a function that takes in the `Target` component). 
The `connect` function now has a simple implemention using `reduce`.

```
export function connect<TProps>(
    targetComponent: ComponentClass<TProps>,
    ...connectors: Connector<TProps>[]
): ComponentClass<TProps> {
    return connectors.reduce((component, nextConnector) => nextConnector(component), targetComponent);
}
```

We now have a `targetComponent` and `connectors`, which in this case is `injectState` and `injectCommands`. Both of these functions return a new function that returns a new component. We can continue to pass along
the new component to the `nextConnector` which will again return a new component. The beauty of this pattern is nothing is mutated. New components are created and added to. 

This certainly took me a bit of time to think through and sit with. So it's ok if it doesn't click right away! But it's a powerful pattern that allows us to greatly simplify our code and 
inject in props from the state and commands into our component. 
