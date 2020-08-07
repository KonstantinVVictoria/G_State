a global state management paradigm.

# G_State

![G_State Logo](https://i.imgur.com/CVtR8VU.png)

## What is G_State?

G_State is a global state management paradigm for react that makes it easy to share properties between components.

In this paradigm, you define a global state, decide and document how it is changed, and render the components that are affected.

## What are the components of G_State?

G_State has three main components:

1. **A global state** and all of its **global properties**.
2. **Events** that describe how a property is changed.
3. **Dependencies** that state which components will be rendered.

## What is the G_State paradigm?

The G_State paradigm is an organizational model where the events that change the global properties are written in the same place where the global
properties are defined. This makes it easier to find where things go wrong. For example, if a global property was changed by an event to an unintentional value, you don't have to dig around to find where the event was written. It will be in your
G_State.js file below the global state. Also in this paradigm, all the changes made to the global properties are formally described. This helps with debugging and readability.

## What are the perks of using G_State?

1. **An easy-to-visualize, manage, and organize global state.**
2. **Selective and efficient rendering.**
3. **Easy-to-follow syntax.**
4. **Monitor global state events and renders.**
5. **Cleaner component definitions**
6. **Work with both functional and class components.**

## How does it work?

You start by creating a global state and defining its properties. The global properties are the properties that are shared by more than one component. You define how these global properties will be changed as method properties of the global properties. In your component files, you define which properties will render a component when they are changed, and you can also call the method events whenever needed and access the global properties freely.

**Creating a global state**
Creating a global state is as easy as creating an object. Create a G_State.js file under your src folder. In the G_State.js file import the G_State library. Define a state object with the global properties needed. Afterwards, define the global properties further, and design events for them.

**Designing Events**
You can design events explicitly or implicitly. An explicit definition describes a future state. An implicit
definition calls the embedded changeTo method in a property. Note, however; that you can only design an event implicitly if a property has a value property. Either way, **any changes made to the global properties must be described as an event**. An event only changes one property and returns a description of the action that takes place.

    //In G_State.js
    import G_State from "g_state-management"
    let GlobalState = { // Create a global state
        property: {
    	    value : 0 ,
    	    increment : () => {},
        },
        favoriteNumber: () => {}
    }
    //Define the global properties further
    GlobalState.favoriteNumber = () => {
        return GlobalState.property.increment() + GlobalState.property.value + 3
    }

    //Design events below explicitly or implicitly.

    GlobalState.property.increment = () => {
    //Explicitly:
        let newState = {} //Define a new state object.
        newState.property = () => { /
        /*Add a method property that is the
        same name of the property being changed.*/

    	    GlobalState.property.value++; //Describe how it is changed
    	    return "incremented_property_value //Name the event
        }
        G_State.changesTo(newState)//Commit the event using the G_State library.
    //----------//
    }

    GlobalState.property.increment = () => {
        //Implicitly: *note that it only works with properties that have a value property
    	let newValue = GlobalState.property.value++
    	GlobalState.property.changesTo("incremented",newValue")
        //The embedded method changesTo is only available to properties that have a value property.
        //It can be overrided.
        //-------------------//
    }
    export default GlobalState;

**Accessing global properties**

    //In Component.jsx
    import  React  from "react";
    import G_State from "g_state-management";

    let {property} = G_State.now
    //Note that G_State.now is equivalent to the GlobalState object in G_State.js

    const Component = () => {
        return(<button onMouseDown={property.increment}>+</button>)
    }
    export default Component;

**Updating the components that need to be updated**
//Note: If a component doesn't need to be updated, you do not have to do this.
Functional Components:

    //In DependantFunctonalComponent.jsx

    import  React  from "react";
    import G_State from "g_state-management";

    let {property, favoriteNumber} = G_State.now
    let dependancies = G_State.link("property") //This component will rerender if the property property is changed.

    const DependantFunctonalComponent = () => {
        return(<div>{favoriteNumber()}</div>)
        //Whenever property is changed by the other component,
        //we need this component to rerender.
    }

    export default G_State.updates(DependantFunctonalComponent, dependancies);
    //This connects the component to the global state and defines which properties affects it.

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
    		return(<div onMouseDown = {this.pressed} >{property.value} </div>) //Everytime property changes, we need to rerender the component to display the latest value.
    	}
    }

    export default DependantClassComponent

# Documentation

example: https://github.com/KonstantinVVictoria/webscrapeProj

**G_State.now**  
-- The global state.

**G_State.updates( component, dependancies )**  
-- Connects the component to the global state. Creates a listener that listens to any G_State.changesTo(newState) calls.  
-- Only the components that need to be rerendered needs to this statement in their definition.  
-- Works for functional and class components.

**G_State.link( "property_1", "property_2", ... )**  
-- Returns an object that describes the dependancies.

**G_State.changesTo( newState )**  
-- Commits the events described in a newState object, broadcasts the changes to all components listening, and renders the components that have  
the name of the properties that were changed by the events listed in its dependencies object.

**G_State.now["property"].changesTo( "event name", newValue )**  
-- This is an embedded method. Whenever a property has a value property, this method is automatically embedded.
-- Commits the event, broadcasts the changes of a property to all components, and rerenders the components that have the name of the properties that were changed by the events listed in its dependencies object.
-- This only works if the property has a value property.
-- This can only change a property's value property

**G_State.debug( {setting1: true/false, setting2: true/false} )**  
-- When activated, it will store all the events descriptions into window.G_Events, and the current state into window.G_State.  
-- To read the event descriptions or current state, simply console log their corresponding window objects.  
-- The only settings currently available is {live: true/false, showMount: true/false} which will console log the events and mounts when they are committed.

# Bugs and Collaboration

Want to report a bug or interested in collaborating? Email me at: scythodemes@gmail.com.
