#  React Native Navigation v2 (WIP)
We are rebuilding react-native-navigation

## Why Rebuild react-native-navigation? 

### A New Improved Core Architecture
react-native-navigation has a few issues which are unsolvable in it’s current architecture. <br>
These issue originate from the same problem: you cannot specify on which screen you wish to make an action. Whenever you want to push, show modal or any other action, the action defaults to originate from your current screen. This covers most use cases but there are some edge cases: <br>
* What if you want to update your navbar icons and the user pops the screen? Your icons might update on the wrong screen.
* What if you want to push a screen as a result of a redux action? 

There are ways to solve some of these problems in v1 but they are not straightforward. We want to change that. 

#### New API
To solve this problem in v2, every screen receives as a prop it’s screenId. Whenever you want to perform an action from that screen you use the from method: 
```js
Navigator.from(this.props.screenId).pop()
```   
### Built for Contributors 
Currently, it requires a lot of work to accept pull requests. We need to manually make sure that everything works before we approve them because v1 is not thoroughly tested. <br>
v2 is written with contributors in mind from day one.

#### Written In TDD
v2 is written in Test Driven Development. We have a test for every feature including features that are not implemented yet. This makes accepting pull requests extremely easy: If our tests pass, your pull request is accepted.


## Where is it standing now?
v2 currently supports most of react-native-navigation’s basic functions but it is still behind v1.
Here is the full comparison of features between v1 and v2 (will be updated regulary):
### Top Level API

|    API              | v1  | v2 |
|--------------------|-----|----|
| startTabBasedApp   |   ✅    |   ✅  |
| startSinglePageApp |   ✅   |  ✅   |
| registerScreen     |   ✅   |  ✅   |
| drawer             |    ✅  |    ✅ |
### Screen API

|  API              | v1     | v2         |
|---------------------|--------|------------|
| push                |  ✅     |   ✅       |
| pop                 |  ✅     |  ✅         |
| showModal           |  ✅     |  ✅   |
| popToRoot           |   ✅     |   ✅         |
| resetTo             |   ✅     |    [Contribute](CONTRIBUTING.md)      |
| dismissModal        |   ✅     |     ✅       |
| dismissAllModals    |   ✅     |      ✅      |
| showLightBox        |   ✅     |      [Contribute](CONTRIBUTING.md)      |
| dismissLightBox     |   ✅     |       [Contribute](CONTRIBUTING.md)       |
| handleDeepLink      |   ✅     |       [Contribute](CONTRIBUTING.md)       |
| setOnNavigatorEvent |   ✅     |       [Contribute](CONTRIBUTING.md)       |
| setButtons          |   ✅     |         [Contribute](CONTRIBUTING.md)     |
| setTitle            |   ✅     |         [Contribute](CONTRIBUTING.md)     |
| toggleDrawer        |   ✅     |        [Contribute](CONTRIBUTING.md)   |
| toggleTabs          |   ✅     |        [Contribute](CONTRIBUTING.md)     |
| setTabBadge         |    ✅    |       [Contribute](CONTRIBUTING.md)     |
| switchToTab         |    ✅    |        [Contribute](CONTRIBUTING.md)    |
| toggleNavBar        |   ✅     |      [Contribute](CONTRIBUTING.md)      |
| Screen Visibility   |   ✅     |       ✅     |

Element tranisitions, adding buttons and styles are not supported yet.  

## Getting started with v2
If v2 supports everything you need for your app we encourage you to use it. Here are the docs
## Docs


### Installation 
1. Download react-native-navigation v2
```bash
yarn add react-native-navigation@2.x
```
##### iOS 
Follow steps 2 - 5 [here](https://wix.github.io/react-native-navigation/#/installation-ios)
##### Android
Follow steps 2 - 6 [here](https://wix.github.io/react-native-navigation/#/installation-android)
### Top Screen API

#### Navigation
```js
import Navigation from 'react-native-navigation';
```
#### Events - On App Launched
```js
Navigation.events().onAppLaunched(() => {
    Navigation.setRoot({
      container: {
        name: 'navigation.playground.WelcomeScreen'
      }
    });
  });
```

#### registerContainer(screenID, generator, store = undefined, Provider = undefined)
Every screen component in your app must be registered with a unique name. The component itself is a traditional React component extending React.Component.
```js
Navigation.registerContainer(`navigation.playground.WelcomeScreen`, () => WelcomeScreen);
```

#### setRoot({params})
Start a Single page app with two side menu: 
```js
Navigation.setRoot({
      container: {
        name: 'navigation.playground.WelcomeScreen'
      },
      sideMenu: {
        left: {
          container: {
            name: 'navigation.playground.TextScreen',
            passProps: {
              text: 'This is a left side menu screen'
            }
          }
        },
        right: {
          container: {
            name: 'navigation.playground.TextScreen',
            passProps: {
              text: 'This is a right side menu screen'
            }
          }
        }
      }
    });
```
Start a tab based app: 
```js
Navigation.setRoot({
      tabs: [
        {
          container: {
            name: 'navigation.playground.TextScreen',
            passProps: {
              text: 'This is tab 1',
              myFunction: () => 'Hello from a function!'
            }
          }
        },
        {
          container: {
            name: 'navigation.playground.TextScreen',
            passProps: {
              text: 'This is tab 2'
            }
          }
        }
      ]
    });
```
### Screen API

#### push(params)
Push a new screen into this screen's navigation stack.
```js
Navigation.from(this.props.screenId).push({
      name: 'navigation.playground.PushedScreen',
      passProps: {}
    });
```
#### pop(screenId)
Pop the top screen from this screen's navigation stack.
```js
Navigation.from(this.props.screenId).pop();
```
#### popTo(params)
```js
Navigation.from(this.props.screenId).popTo(this.props.previousScreenIds[0]);
```
#### popToRoot()
Pop all the screens until the root from this screen's navigation stack
```js
Navigation.from(this.props.screenId).popToRoot();
```
#### showModal(params = {})
Show a screen as a modal.
```js
Navigation.showModal({
      container: {
        name: 'navigation.playground.ModalScreen',
        passProps: {
            key: 'value'
        }
      }
    });
```
#### dismissModal(screenId)
Dismiss modal.
```js
Navigation.dismissModal(this.props.screenId);
```
#### dismissAllModals()
Dismiss all the current modals at the same time.
```js
Navigation.dismissAllModals();
```
#### Screen Lifecycle - onStop() and onStart()



# old notes 
## Major Changes
Behind being written in TDD, v2 has a significantly better design which in turn changes the API. <br>
Here are the major changes: 


1) One of the major weaknesses in v1 was the inability to define on which screen you want an action to be performed.
  V2 makes it possible by changing `this.props.navigator.push()` to `Navigation.on(this.props.navigatorId).push()`.
  This removes bugs which couldn't be solved before. 
 ```js
 // v1
 this.props.navigator.pop()
 // v2
 Navigation.from(this.props.screenId).pop();
```

2) You now need to import Navigation to every screen where you wish to use it, instead of it being automatically passed by props
```js
 import Navigation from 'react-native-navigation';
 
 ...
 
 Navigation.from(this.props.screenId).pop();
```



