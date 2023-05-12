---
layout: post
title: SPFx React State management on component property changes
date: 2023-05-12 +13:00
published: true
category: SPFx
tags: [spfx, react, bug fixing, typescript, state management, site page copying, cross tenant]
---

With React in SPFx projects, Class Components provided 3 lifecycle methods that'd force re-rendering and give the ability for the developer to handle lifecycle within the scope of any particular component.

- componentDidMount
- componentDidUpdate
- componentWillDismount

The problem with these is that they're inherently complex and introduction of convoluted bugs is very easy.

The counter to this was the introduction of Functional React Components way back in 2018. Why is this suddenly being discussed here? It's been a while since I've worked on SPFx solutions so it's suddenly relevant... let's crack on.


# Handling SPFx property updates and forcing re-render of elements

The introduction of Functional React Components has brought a significant simplification to how component lifecycle is handled. We must now simply handle the property change and the framework abstracts away the complexity, re-rendering when necessary.

Upon changing of a property attached to the component (from the Web Part property panel for example) the component will receive the property change specified within `useEffect` and invoke whatever functionality is specified within.

Here's a simple example component that has two properties, which will re-render when the values in the property panel is updated.

~~~ts
import * as React from 'react';
import styles from '../TitleComponent.module.scss';
import ITitleComponentProps from './ITitleComponentProps';

// Example of handling functional component property changes in Functional SPFx React components
const TitleComponent: React.FunctionComponent<ITitleComponentProps> = (props) => {
  // state initialisers
  const [title, setTitle] = React.useState<string>(props.title);
  const [subTitle, setSubTitle] = React.useState<string>(props.subTitle);
  
  // Effects
  React.useEffect(() => { // execute Effect on change of specified property; props.title
    setTitle(props.title); // set state value, forcing re-render of related component element(s)
  }, [props.title]);

  React.useEffect(() => { // execute Effect on change of specified property; props.subTitle
    setSubTitle(props.subTitle); // set state value, forcing re-render of related component element(s)
  }, [props.subTitle]);
  
  // HTML elements
  let htmlElement: JSX.Element = null;
  
  // simple HTML element using title and subTitle from state, re-rendering on change of the backing property
  htmlElement =
    <div className={`${styles.headingWrapper}`}>
      <h1 className={`${styles.mainheading}`}>{title}</h1>
      <h2 className={`${styles.subheading}`}>{subTitle}</h2>
    </div>

  return htmlElement;
  
  };

export default TitleComponent;
~~~

This was intended to be a simple example of how state and properties should be handled within an SPFX React Functional Component. Let me know if you've got any questions or if you want this fleshed out any further.

Have a good one!
