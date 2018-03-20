React Lazy Load Image Component
===============================

React Component to lazy load images and other components/elements. Includes a HOC to track window scroll position to improve performance.

[![Build Status](https://travis-ci.org/Aljullu/react-lazy-load-image-component.svg?branch=master)](https://travis-ci.org/Aljullu/react-lazy-load-image-component)
[![Dependency Status](https://img.shields.io/david/Aljullu/react-lazy-load-image-component/master.svg)](http://david-dm.org/Aljullu/react-lazy-load-image-component)
[![Download counter](https://img.shields.io/npm/dt/react-lazy-load-image-component.svg)](https://www.npmjs.com/package/react-lazy-load-image-component)
[![License](https://img.shields.io/npm/l/react-lazy-load-image-component.svg)](https://www.npmjs.com/package/react-lazy-load-image-component)

## Features

* Includes two components (`LazyLoadImage` and `LazyLoadComponent`) and a HOC (`trackWindowScroll`) which adds scroll position tracking to any component you wish.
* Handles scroll events, resize events and re-renders that might change the position of the components. And, of course, above-the-fold on initial render.
* Placeholder by default with the same size of the image/component.
* A custom placeholder component or image can be specified.
* Built-in on-visible effects (blur, black and white and opacity transitions).
* threshold is set to 100px by default and can be modified.
* `beforeLoad` and `afterLoad` events.
* `debounce` and `throttle` included by default and configurable.

## Installation

```bash
# Yarn
$ yarn add react-lazy-load-image-component

# NPM
$ npm i --save react-lazy-load-image-component
```


## `LazyLoadImage` usage

```javascript
import React from 'react';
import { LazyLoadImage } from 'react-lazy-load-image-component';

const MyImage = ({ image }) => (
  <div>
    <LazyLoadImage
      alt={image.alt}
      height={image.height}
      src={image.src} // use normal <img> attributes as props
      width={image.width} />
    <span>{image.caption}</span>
  </div>
);

export default MyImage;
```

### Props

| Prop | Type | Default | Description |
|:---|:---|:---|:---|
| afterLoad | `Function` |  | Function called after the image has been completely loaded. |
| beforeLoad | `Function` |  | Function called right before the placeholder is replaced with the image element. |
| delayMethod | `String` | `throttle` | Method from lodash to use to delay the scroll/resize events. It can be `throttle` or `debounce`. |
| delayTime | `String` | 300 | Time in ms sent to the delayMethod. |
| effect | `String` |  | Name of the effect to use. Please, read next section with an explanation on how to use them. |
| placeholder | `ReactClass` | `<span>` | React element to use as a placeholder. |
| placeholderSrc | `String` | | Image src to display while the image is not visible or loaded. |
| threshold | `Number` | 100 | Threshold in pixels. So the image starts loading before it appears in the viewport. |
| visibleByDefault | `Boolean` | false | Whether the image must be visible from the beginning. |
| wrapperClassName | `string` |  | In some occasions (for example, when using a placeholderSrc) a wrapper span tag is rendered. This prop allows setting a class to that element. |
| ... |  |  | Any other image attribute |


### Using effects

`LazyLoadImage` includes several effects ready to be used, they are useful to add visual candy to your application, but are completely optional in case you don't need them or want to implement some other effect.

They rely on CSS and the corresponding CSS file must be imported:

```javascript
import React from 'react';
import { LazyLoadImage } from 'react-lazy-load-image-component';
import 'react-lazy-load-image-component/src/effects/blur.css';

const MyImage = ({ image }) => (
  <LazyLoadImage
    alt={image.alt}
    effect="blur"
    src={image.src} />
);
```

The current available effects are:

* blur: renders a blurred image based on `placeholderSrc` and transitions to a non-blurred one when the image specified in the src is loaded.
* black-and-white: renders a black and white image based on `placeholderSrc` and transitions to a colorful image when the image specified in the src is loaded.
* opacity: renders a blank space and transitions to full opacity when the image is loaded.


## `LazyLoadComponent` usage

```javascript
import React from 'react';
import { LazyLoadComponent } from 'react-lazy-load-image-component';
import { ArticleContent, ArticleComments } from 'my-app';

const Article = ({ articleId }) => (
  <div>
    <ArticleContent id={articleId} />
    <LazyLoadComponent>
      <ArticleComments id={articleId} />
    </LazyLoadComponent>
  </div>
);

export default Article;
```

### Props

| Prop | Type | Default | Description |
|:---|:---|:---|:---|
| afterLoad | `Function` |  | Function called after the component has been rendered. |
| beforeLoad | `Function` |  | Function called right before the component is rendered. |
| delayMethod | `String` | `throttle` | Method from lodash to use to delay the scroll/resize events. It can be `throttle` or `debounce`. |
| delayTime | `String` | 300 | Time in ms sent to the delayMethod from lodash. |
| placeholder | `ReactClass` | `<span>` | React element to use as a placeholder. |
| threshold | `Number` | 100 | Threshold in pixels. So the component starts loading before it appears in the viewport. |
| visibleByDefault | `Boolean` | false | Whether the component must be visible from the beginning. |


## Using `trackWindowScroll` HOC to improve performance

When you have many elements to lazy load in the same page, you might get poor performance because each one is listening to the scroll/resize events. In that case, it's better to wrap the deepest common parent of those components with a HOC to track those events (`trackWindowScroll`).

For example, if we have an `App` which renders a `Gallery`, we would wrap the `Gallery` component with the HOC.

```javascript
import React from 'react';
import { LazyLoadImage, trackWindowScroll }
  from 'react-lazy-load-image-component';

const Gallery = ({ images, scrollPosition }) => (
  <div>
    {images.map((image) =>
      <LazyLoadImage
        key={image.key}
        alt={image.alt}
        height={image.height}
        // Make sure to pass down the scrollPosition,
        // this will be used by the component to know
        // if it must track the scroll position or not
        scrollPosition={scrollPosition}
        src={image.src}
        width={image.width} />
    )}
  </div>
);
// Wrap Gallery with trackWindowScroll HOC so it receives
// a scrollPosition prop to pass down to the images
export default trackWindowScroll(Gallery);
```

You must set the prop `scrollPosition` to the lazy load components. This way, they will know the scroll/resize events are tracked by a parent component and will not subscribe to them.

### Props

`LazyLoadImage`

| Prop | Type | Default | Description |
|:---|:---|:---|:---|
| scrollPosition | `Object` |  | Object containing `x` and `y` with the curent window scroll position. Required. |
| afterLoad | `Function` |  | Function called after the image has been rendered. |
| beforeLoad | `Function` |  | Function called right before the image is rendered. |
| placeholder | `ReactClass` | `<span>` | React element to use as a placeholder. |
| threshold | `Number` | 100 | Threshold in pixels. So the image starts loading before it appears in the viewport. _Defaults to 100._ |
| visibleByDefault | `Boolean` | false | Whether the image must be visible from the beginning. |
| ... |  |  | Any other image attribute |

Component wrapped with `trackWindowScroll` (in the example, `Gallery`)

| Prop | Type | Default | Description |
|:---|:---|:---|:---|
| delayMethod | `String` | `throttle` | Method from lodash to use to delay the scroll/resize events. It can be `throttle` or `debounce`. |
| delayTime | `String` | 300 | Time in ms sent to the delayMethod from lodash. |

Notice you can do the same replacing `LazyLoadImage` with `LazyLoadComponent`.


## When to use `visibleByDefault`?

The prop `visibleByDefault` makes the LazyLoadImage to behave like a normal `<img>`. Why is it useful, then?

Imagine you are going to lazy-load an image you have already loaded in the same page. In that case, there is no need to lazy-load it because it's already stored in the cache of the user's browser. You can directly display it.

Maybe the following code snippet will make it more clear:

```javascript
import React from 'react';
import { LazyLoadImage, trackWindowScroll }
  from 'react-lazy-load-image-component';

const Gallery = ({ images, scrollPosition }) => (
  <div>
    // We are loading landscape.jpg here
    <img src="/landscape.jpg" alt="Beautiful landscape" />
    {images.map((image) =>
      <LazyLoadImage
        key={image.key}
        alt={image.alt}
        scrollPosition={scrollPosition}
        src={image.src}
        // If the image we are creating here has the same src than before,
        // we can directly display it with no need to lazy-load.
        visibleByDefault={image.src === '/landscape.jpg'} />
    )}
  </div>
);

export default trackWindowScroll(Gallery);
```


## Screenshot

<a href="https://raw.githubusercontent.com/Aljullu/react-lazy-load-image-component/master/screenshots/example.gif"><img src="https://raw.githubusercontent.com/Aljullu/react-lazy-load-image-component/master/screenshots/example.gif" alt="" /></a>

Get the [full code of this example](https://github.com/Aljullu/weather-app).


## Common errors

### Warning: setState(...): Can only update a mounted or mounting component.

That warning might appear if there are two components using `trackWindowScroll` at the same time. Notice it's not possible to have a LazyLoadImage/LazyLoadComponent inside another LazyLoadComponent for now. Also, make sure you are passing down `scrollPosition` to all components wrapped inside `trackWindowScroll`.
