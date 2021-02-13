# ReactArchitectureProposal (RAP)
> A way to standarize and manage React Applications in a way that are scalable but maintain simplicity. Allows for teams to work in one big app without damaging each others work, promoting strength withing the project and reusability.

---


## Introduction
> It is a necessary to standarize the development of applications in a way that can be understood by other coders and can grow and evolve with the application that's being built allowing scalability, moreover and standard will allow the share of functionality across aplications with ease.


## Definitions

### *Component*
> A component is the most basic type of artifact to render, it can be compose for other more primary components. But tipically a component doesn't have much logic and is as simple as posible.
Many simple components can be writen together in the same file as a familly, as an example we can have multiple buttons Components that are simple variations on color but most share the same Interface.

### *Container*
> A container is a complex Component, this can be formed by composition using Components. It handles logic like user interactions and sometimes is a familly of components working together to achieve a functionallity. It can use Context to avoid prop driping.
A contanier can be formed using Components and other more basic Containers. Example, a container will have a List component working together with a Selector to filter the data


### *Page*
> A page is a component that is referenced in the router, his job is to render the Containers and Components as well as loading common resources needed for achieving the page purpose. For example, a page will render a list of clients.


### *Module*
> Is a section on the project that can have more than one page. In the module we handle the router to render the pages that belongs to the same funtionallity. For example a module can handle the CRUD operations for managing clients within the app, having the pages for Create/Update and other for listing in a table.


### *Resuming*
> As a resume, we can say that a Module handle many pages, a page handle many Containers and components, a container handles other simpler Containers and Components and that a Component some times can handle simpler components
> 
* Module 1..* Pages
* Page 1..* Containers+Components 
* Container 1..* Containers+Components
* Components 0..* Components
----

## Folder Structure

> The folder structure is hierarchical, allowing to organize the app in layers that can grow in deep as the modules evolve.

> Every layer will have a representation of the concepts defined above, an every component or container can be used on the level it was declared or in the levels below, this means that a top level module cannot use low leven module's component but a low level component can use a higher level module's component

> A team working on a low level module most respect the hierarchy and cannot change a component or container that is in a higher module, It can use but can't modify it.

> In order to reuse the components and containers as much as possible, the higher level should handle details as little as possible, given the childrens the task of managing the details.

Example folder structure

    ├── src
        ├── components
        │   ├── Buttons.jsx
        │   ├── Dialog.jsx
        │   ├── Inputs.jsx
        │   ├── Text.jsx
        │   └── Header.jsx
        ├── containers
        │   ├── Navbar.jsx   
        │   └── Panel.jsx
        └── pages
            ├── Home
            │   ├── components
            │   │   ├── Chips.jsx
            │   │   └── Dropdown.jsx
            │   ├── containers
            │   │   ├── Actions.jsx
            │   │   └── Table.jsx
            │   └── index.jsx
            └── Menu
                ├── Components
                ├── Pages
                ├── Detail
                │   ├── components
                │   │   ├── Amount.jsx
                │   │   └── Title.jsx
                │   ├── containers
                │   │   └── Ingredients.jsx
                │   └── index.jsx
                ├── index.jsx
                └── List
                    ├── components
                    │   ├── Element.jsx
                    │   └── Product.jsx
                    ├── containers
                    │   ├── Category.jsx
                    │   └── SearchBar.container.jsx
                    └─── index.jsx

> In this example, the component in the root level (Buttons, Dialog, Inputs.. etc) can be use anywhere in the app.

> Notice that Menu is a module that contains the pages Detail and List.

> The routes for the app will be:
 * Home (Will reference root index.jsx)
 * Menu (For convenience the default will be the List page)
 * Menu/Detail (Detail Page within the Menu folder)

> Using the example above, notice that the Components defined Below Menu, cannot be used on Home and viceversa. And the team working on the Menu pages can use the root level Components but cannot modify them without all the teams being notified and agree the change.
  
> Is fundamental that the structure of the pages mirror the app's routing

----
## Application State
> This way to manage the state was an evolution of the regular way to work with the [Flux pattern](https://facebook.github.io/flux/docs/in-depth-overview), however the idea is to abstract the state management and logic related to the app's data to another layer with the goal in mind that the components that handle the app layout wont be coupled to an specific library and know as little as possible about the state layer.


### Folder structure
> The state folder is a core module of the APP and can be placed in the src/ folder directly and inside this folder we will have a subfolder for every piece of data we have in our APP. For example, an app that handles clients and orders may have an structue like:



             state
               ├── products
               │   ├── actions.js
               │   ├── dispatcher.js
               │   ├── reducer.js
               │   ├── sagas.js
               │   ├── selector.js
               │   └── index.js
               └── orders
                   ├── actions.js
                   ├── dispatcher.js                   
                   ├── reducer.js
                   ├── sagas.js
                   ├── selector.js
                   └── index.js


## **use**TheHook

As described in [Flux pattern](https://facebook.github.io/flux/docs/in-depth-overview) we have our state and dispatchers, with the dispatcher the components can dispatch actions (pretty obvious I know), but the way we expose the state and dispatcher is using a custom hook. A common wat to use the state and the dispatcher in the components is by using methods like mapStateToProps and mapDispatchToProps and then doing a few steps in every component that require access to the state or the dispatcher incrementing the complexity and requiring to import and declare in the whole application specific libraries and methods. 

The proposed to avoid this is to maintain the code relative to the State management in the state layer and expose the dispatcher and the state to the components using custom hooks. This custom hooks is declare in the index.js file, so if a component need access to it just need to import it from the state like this:


    import useProducts from 'state/products';

and then inside the component we access the state and dispatcher as simple as:

    const [state,dispatcher] = useProducts();

a typical index.js that creates the hook file looks like:

    import { useDispatch, useSelector, shallowEqual } from 'react-redux';

    import Dispatcher from './dispatcher';
    import selector from './selector';


    const useProduct = () => {
        const dispatcher = new Dispatcher(useDispatch());
        const state = useSelector(selector, shallowEqual);

        return [state, dispatcher]
    }


    export default useProduct;



----

## Incentive
> @todo