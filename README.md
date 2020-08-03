![enter image description here](https://i.imgur.com/CVtR8VU.png)

# G_State

## What is G_State?

G_State is a global state management paradigm for react that makes it easy to share properties between components.  

In this paradigm, you define a global state, decide and document how it is changed, and render the components that are affected.  

## What are the components of G_State?

G_State has three main components:  

1. **A global state** and all of its **global properties**.  
2. **Events** that describe how a property is changed.  
3. **Dependencies** that state which properties will cause a component to rerender when changed.  

## What is the G_State paradigm?

The G_State paradigm is an organizational model where the events that change the global properties are written in the same place where they are defined. This makes it easier to find where things go wrong. For example, if a global property was changed to an unintentional value, you don't have to dig around to find it. It will be in your
G_State.js file below the global state. Also, changes made to the global properties must be formally described. This helps with debugging and readability.

## What are the perks of using G_State?

1. **An easy-to-visualize, manage, and organize global state.**  
2. **Selective and efficient rendering.**  
3. **Easy-to-follow syntax and declarations that make sense.**  
4. **Monitor global state events and renders.**  
5. **Cleaner component definitions**  
6. **Work with both functional and class components.**  

## How does it work?

You start by creating a global state and defining its properties. The global properties are the properties that are shared by more than one component. You define how these global properties will be changed as method properties of the global properties. In these method properties or "events", you define how a global property value is changed. In your component files, you can define which properties will render a component when they are changed, and you can also call the method events whenever needed and access the global properties freely.

**Creating a global state**
Create a global state is as easy as creating an object. Create a G_State.js file under your src folder. In the G_State.js file import the G_State library. Define a state object with the global properties needed. Afterwards, define the global properties further, and design events for them.

**Designing Events**
You can design events explicitly or implicitly. An explicit definition describes a future state. An implicit
definition calls the embedded changeTo method in a property. Note, however; that you can only design an event implicitly if a property has a value property. Either way, **any changes made to the global properties must be described as an event**. An event only changes one property and returns a description of the action that takes place.

    //In G_State.js
    import G_State from "g_state-management"
    let State = {
        property: {
    	    value : 0 ,
    	    increment : () => {},
        },
        favoriteNumber: () => {}
    }
    //Define properties further.
    State.favoriteNumber = () => {
        return State.property.increment() + State.property.value + 3
    }

    //Design events below explicitly or implicitly.
    //Explicitly:
    State.property.increment () => {
        let newState = {} //Define a new state object.
        newState.property = () => { /
        /*Add a method property that is the
        same name of the property being changed.*/

    	    State.property.value++; //Describe how it is changed
    	    return "incremented_property_value //Name the event
        }
        G_State.changesTo(newState)//Commit the event.
    }
    //Implicitly: *note that it only works with properties that have a values property
    State.property.increment () => {
    	let newValue = State.property.value++
    	State.property.changesTo("incremented",newValue") //changesTo method can be overrided.
    }
    export default State;

**Accessing global properties**

    //In Component.jsx
    import  React  from "react";
    import G_State from "g_state-management";

    let {property} = G_State.now
    //Note that G_State.now is equivalent to the State object in G_State.js
    const Component = () => {
    return(<button onMouseDown={property.increment}>+</button>)
    }
    export default Component;

**Updating the components that need to be updated**
Functional Components:

    //In DependantFunctonalComponent.jsx

    import  React  from "react";
    import G_State from "g_state-management";

    let {property, favoriteNumber} = G_State.now
    let dependancies = G_State.link("property")

    const DependantFunctonalComponent = () => {
        return(<div>{favoriteNumber()}</div>)
        //Whenever property is changed by the other component,
        //we need this function to rerender.
    }

    export default G_State.updates(DependantFunctonalComponent, dependancies);
    //This connects the component to the global state and define which properties affects it.

Class Components:

    import  React, {Component}  from "react";
    import G_State from "g_state-management";

    let {property, favoriteNumber} = G_State.now

    class DependantClassComponent extends Component{
    	constructor(props){
    		super(props)
    		this.state= {}
    		this.state.dependancies = G_State.link("property") //List dependancies
    		G_State.updates(this) //Link the component to the global state.
    	}
    	pressed = () => {
    		property.changesTo("changed_property", 7 )
    		//You can define events implicitly and explicitly in components as well
    	}
    	render(){
    		return(<div onMouseDown = {this.pressed} > </div>)
    	}
    }

    export default DependantClassComponent

# Documentation

**G_State.now**  
-- The global state.  

**G_State.updates( component, dependancies )**  
-- Connects the component to the global state. Adds a listener that listens to change events.  
-- Only the components that need to be rerendered needs to this statement.  
-- Works for functional and class components.  

**G_State.link( "property_1", "property_2", ... )**  
-- Returns an object that describes the dependancies.  

**G_State.changesTo( newState )**  
-- Commits the event, broadcasts the changes to all components, and renders the components that have  
the property changed listed in its dependency.  

**G_State.now["property"].changesTo( newState )**  
-- Commits the event, broadcasts the changes to all components, and renders the components that have  
the property changed listed in its dependency.  

**G_State.debug( {setting1: true/false}, {setting2: true/false} )**  
-- When activated, it will store all the events descriptions into window.G_Events, and the current state into window.G_State.  
-- To read the event description or current state, simply console log the window objects.  
-- The only settings currently available is {live: true/false} which will console log the events when they are committed.  
