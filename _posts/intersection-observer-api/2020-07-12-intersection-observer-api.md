---
title: "Infinite Scrolling: The Modern Way"
date: 2020-07-12 17:17:00 +07:00
tags: [Javascript, Infinite Scroll, React]
description: 
twitter: https://twitter.com/ayush_garg341
github: https://github.com/ayush-garg341/intersection-observer-api
---

I hope everyone who has got some experience in web development, has gone through this onScroll event to provide better UI/UX experience. But everyone has their own way to achieve this, some uses libraries, some uses OnScroll event but what is common in all these method is **Performance**. You certainly do not want to hit your API un-necessarily. So today we will cover one such method, which is highly optimized i.e. **Intersection Observer API**.

### Intersection Observer API

Let's understand the concept behind this. In this method we do not use onScroll event rather we track element visibility to fetch more results. The Intersection Observer API allows us to watch a certain element and run a callback when its visibility changes in relation to the view port or another element.

Let's get into more depth, suppose on a single API hit you are fetching 20 results to display and as soon as user reaches the bottom of those 20 elements, you have a loader to show, as soon as loader get into view port ( visible area of a webpage on a display device ) you fetch another 20 records to display.

Demo code for Tracking visibility of an element.
```
import React, { Component } from "react";

class TrackVisibility extends Component {
  constructor(props) {
    super(props);
    this.state = {};

    this.ref = React.createRef();
  }

  async componentDidMount() {
    const observer = new IntersectionObserver(
      ([entry]) => {
          if (entry.intersectionRatio >= 0.5) {
          //callback function
        }
      },
      {
        root: null,
        rootMargin: "0px",
        threshold: 0.5
      }
    );

    if (this.ref.current) {
      observer.observe(this.ref.current);
    }
  }

  render() {
    return (
      <div ref={this.ref} className="show-loader">
        Loading ..................
      </div>
    );
  }
}

export default TrackVisibility;
```

Here what we have done, we have made an observer function inside componentDidMount, with some parameters and then calling observe method to observe the target/ref element. As soon as this `Loading .........` text gets in view port, according to threshold value set in params, it will fire a callback function. 

**Understanding Parameters**

* Intersection Ratio

An intersection ratio tell the Intersection Observer what percentage of our element needs to be visible in order to trigger the callback. This must be a value between 0.0 and 1.0. The value of 1 means fully visible, 0.5 means half visible and there are other decimal values that we can set according to our requirements. Threshold is the value that get passed as intersectionRatio.

**`Note`**:- We can also pass an array of values to the threshold variable and call the callback accordingly.
```
const observer = new IntersectionObserver(
      ([entry]) => {
          if (entry.intersectionRatio === 0.1) {
          //callback when element came into view
        }
	else if(entry.intersectionRatio === 0.5){
	//callback when half the element is visible
	}

	else if(entry.intersectionRatio === 1.0){
	//callback when element fully in view
	}
      },
      {
        root: null,
        rootMargin: "0px",
        threshold: [0.1, 0.5, 1.0]
      }
    );
```

* Root 

This is the element in relevance to which we will be watching for intersections. Most of the time this will be the full view port which means that we need to pass null as a value.

* Root margin

The IntersectionObserver interface's read-only rootMargin property is a string with syntax similar to that of the CSS margin property. Each side of the rectangle represented by rootMargin is added to the corresponding side in the root element's bounding box before the intersection test is performed.

To completely understand it's working practically and integrating it with react applications, I have created a <a href="{{page.github}}" target="_blank">github</a> project. Go and checkout the project, it will be fun.

*This is the end of this article on Intersection Observer API. If you like this article or have leaned something from it, leave your valuable comments on twitter. Stay in touch. Please follow on <a href="{{page.twitter}}" target="_blank">Twitter</a>*