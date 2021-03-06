#lazysizes
**lazysizes** is a SEO-friendly and fast (jank-free) lazyloader for images (including responsive images), iframes, scripts/widgets and much more. It may become also your number one tool to integrate responsive images. Due to the fact that it can also automatically calculate the ``sizes`` attribute for your responsive images, it helps to separate layout (CSS) from content/structure (HTML) and makes integrating responsive images into any environment simply simple.

##How to

1. Download the [lazysizes.min.js script](lazysizes.min.js) and include **lazysizes** in your webpage.

    ```html
    <script src="lazysizes.min.js" async=""></script>
    ```

2. lazysizes does not need any JS configuration: Add the ``class`` ``"lazyload"`` to your images/iframes in conjunction with a ``data-src`` or ``data-srcset`` attribute. Optionally you can also add a ``src`` attribute with a low quality image:

    ```html
    <!-- non-responsive: -->
    <img src="low-quality-src.jpg" data-src="normal-quality-src.jpg" class="lazyload" />
    ```
    ```html
    <!-- responsive example with automatic sizes calculation: -->
    <img
        data-sizes="auto"
        src="lqip-src.jpg"
        data-srcset="lqip-src.jpg 220w,
        image2.jpg 300w,
        image3.jpg 600w,
        image4.jpg 900w" class="lazyload" />
    ```
    ```html
    <!-- iframe example -->
    <iframe frameborder="0" 
    	class="lazyload" 
        allowfullscreen="" 
        data-src="//www.youtube.com/embed/ZfV-aYdU4uE">
    </iframe>
    ```

##What makes lazysizes so awesome:
**lazysizes** is different than other lazy image loaders.

1. **Works without any configuration**: The script detects any changes to the visibility of an image/iframe automatically no matter whether it becomes visible through a user scroll, a CSS animation triggered through ``:hover`` or through any kind of JS behavior (carousel, infinite scroll, AJAX)...
2. **Future-proof**: It directly includes standard responsive image support (``picture`` and ``srcset``)
3. **Separation of concerns**: For responsive image support it adds an automatic ``sizes`` calculation feature.
4. **Performance**: It's based on high efficient code (runtime **and** memory) to work jank-free at 60fps.
5. **Extendable**: It provides JS and CSS hooks to extend lazySizes with any kind of lazy loading or effects.

##[Demo with code examples](http://afarkas.github.io/lazysizes/#examples)
Can be seen [here](http://afarkas.github.io/lazysizes/#examples).

##About responsive image support
For full cross browser responsive image support you must use a polyfill like [respimage (recommended) (srcset and picture polyfill)](https://github.com/aFarkas/respimage) or [picturefill](https://github.com/scottjehl/picturefill).

##More about the API
**lazysizes** comes with a simple markup and JS API. Normally you will only need to use the markup API.

###Markup API
Add the ``class`` ``lazyload`` to all ``img`` and ``iframe`` elements, which should be loaded lazy. Instead of a ``src`` or ``srcset`` attribute, use a ``data-src`` or ``data-srcset`` attribute:

```html
<img data-src="image.jpg" class="lazyload" />
<!-- retina optimized image: -->
<img data-srcset="responsive-image1.jpg 1x, responsive-image2.jpg 2x" class="lazyload" />
```

**lazysizes** supports setting the ``sizes`` attribute automatically corresponding to the current size of your image. To add support for this add the value ``auto`` to the ``data-sizes`` attribute:

```html
<img
	data-sizes="auto"
	data-srcset="responsive-image1.jpg 300w,
    responsive-image2.jpg 600w,
    responsive-image3.jpg 900w" class="lazyload" />
```

**Important: How ``sizes`` is calculated**: The automatic sizes calculation takes the width of the image and the width of its parent element and uses the largest number of those two calculated numbers. It's therefore important that all images with a ``data-sizes="auto"`` attribute are constrained in width by its parent element. Otherwise a wrong (too big) ``sizes`` attribute will be calculated.

##Recommended markup patterns
For some image bots (search engines and social networks), legacy browsers (IE8) or JS disabled browsers, it is important to serve a usable ``src`` attribute:

###LQIP
The LQIP pattern (low quality image placeholder): Simply add a low quality image as the ``src``:

```html
<!-- responsive example: -->
<img
	data-sizes="auto"
    src="lqip-src.jpg"
	data-srcset="lqip-src.jpg 220w,
    image2.jpg 300w,
    image3.jpg 600w,
    image4.jpg 900w" class="lazyload" />
    
<!-- or non-responsive: -->
<img src="lqip-src.jpg" data-src="image.jpg" class="lazyload" />
```

The LQIP pattern has the following advantages: The lqip-src is not hidden from the preload parser and loads very fast, which leads to an extreme fast first impression and in case of legacy / JS disabled browsers/devices or some searchengines (bots) as a good enough fallback (IE8 and Android 2 devices as also JS disabled).

###Simple pattern

In case you want to save more initial image data the LQIP pattern can't be used (an extreme fuzzy image does neither work as a good enough first impression nor as a fallback), you do use client side rendering (no preload parser advantage) or you can't even generate a LQIP src, simply add a data uri as fallback ``src``. Due to the fact, that lazysizes starts a priorized queued download after onload the images are not hidden from google bot (Yes, the google bot does execute JS).

```html
<img src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw=="
	class="lazyload"
	data-srcset="image.jpg 1x, image2.jpg 2x"
    alt="my image" />
```

###The noscript pattern

In case disabled javascript is a concern you can combine the simple pattern with an image inside a ``noscript`` element.

```html
<style>
	.no-js img.lazyload {
    	display: none;
    }
</style>

<!-- noscript pattern -->
<noscript>
	<img src="image.jpg" />
</noscript>
<img src="grey.jpg" data-src="image.jpg" class="lazyload" />
<!--<![endif]-->
```

Note: While the noscript and the simple pattern do save more bandwidth, they often still generate a slower perceived performance than the LQIP pattern due to the lack of the preload parser advantage.

###JS API
**lazysizes** automatically detects new elements with the class ``lazyload`` so you won't need to call or configure anything in most situations.

####JS API - options
Options can be set by declaring a global configuration option object named ``lazySizesConfig``. This object must be defined before the lazysizes script. A basic example:

```js
window.lazySizesConfig = {
    lazyClass: 'postbone', // use .postbone instead of .lazyload
    preloadAfterLoad: true // preload all lazy elements in a download queue
};
```

Here the list of options:

* ``lazySizesConfig.lazyClass`` (default: ``"lazyload"``): Marker class for all elements which should be lazy loaded (There can be only one ``class``. In case you need to add some other element, without the defined class, simply add it per JS: ``$('.lazy-others').addClass('lazyload');``)
* ``lazySizesConfig.preloadAfterLoad`` (default: ``true``): Wether lazysizes should load all elements after the window onload event. (Note: The default of this option was changed to ``true`` with version 0.6.0). Setting this to ``false`` and not providing a suitable low quality image placeholder will hide below the fold images from google.
* ``lazySizesConfig.addClasses`` (default: ``false``): Wether lazysizes should add loading and loaded classes. This can be used to add unveil effects or to apply new styles (background-image).
* ``lazySizesConfig.loadingClass`` (default: ``"lazyloading"``): If ``addClasses`` is set to ``true`` this ``class`` will be added to ``img`` element as soon as image loading starts. Can be used to add unveil effects.
* ``lazySizesConfig.loadedClass`` (default: ``"lazyloaded"``): If ``addClasses`` is set to ``true`` this ``class`` will be added to any element as soon as the image is loaded or the image comes into view. Can be used to add unveil effects or to apply styles.
* ``lazySizesConfig.onlyLargerSizes`` (default: ``true``): In case a responsive image had the ``data-sizes="auto"`` attribute and the computed new size decreases, lazysizes won't normally change the ``sizes`` attribute to a lower value.
* ``lazySizesConfig.clearAttr`` (default: ``false``): Set this to ``true`` if you want lazysizes to remove the ``data-`` attributes after doing it's work.
* ``lazySizesConfig.srcAttr`` (default: ``"data-src"``): The attribute, which should be transformed to ``src``.
* ``lazySizesConfig.srcset`` (default: ``"data-srcset"``): The attribute, which should be transformed to ``srcset``.
* ``lazySizesConfig.sizesAttr`` (default: ``"data-sizes"``): The attribute, which should be transformed to ``sizes``.

####JS API - events
**lazysizes** provides two events to modify or extend the behavior of **lazysizes**.

* ``lazybeforeunveil``: This event will be fired on each lazyload element right before of the "unveil" transformation. This event can be used to extend the unveil functionality. In case the event is ``defaultPrevented`` the default transformation action will be prevented (see also the [ls.unveilhooks.js plugin](plugins/unveilhooks/ls.unveilhooks.js)):
```js
//add simple support for background images:
document.addEventListener('lazybeforeunveil', function(e){
    var bg = e.target.getAttribute('data-bg');
    if(bg){
        e.target.style.backgroundImage = bg;
        e.target.removeAttribute('data-bg');
        e.preventDefault();
    }
}, false);
```

The ``lazybeforeunveil`` event can also be used to add unveil effects using JS:

```html 
<style>
img.lazyload {
    opacity: 0;
}
</style>

<script>
$(document).on('lazybeforeunveil', (function(){
	var onLoad = function(e){
		$(e.target)
			.animate({opacity: 1})
			.off('load error', onLoad)
		;

	};
	return function(e){
		if(!e.isDefaultPrevented()){
			$(e.target)
				.filter('img')
					.on('load error', onLoad)
			;
		}
	};
})());
</script>
```

For CSS transition/animations use the ``addClasses`` option:

```html 
<style>
.lazyload,
.lazyloading {
	opacity: 0;
}
.lazyloaded {
	opacity: 1;
	transition: opacity 300ms;
}
</style>

<script>
window.lazySizesConfig = {
	addClasses: true
};
</script>
```

* ``lazybeforesizes``: This event will be fired on each element with the ``data-sizes="auto"`` attribute right before the calculated ``sizes`` attribute will be set. The ``event.details.width`` property is set to the calculated width of the element and can be changed to any number. In case the event is ``defaultPrevented`` the ``sizes`` attribute won't be set.

####JS API - methods
#####``lazySizes.unveilLazy(DOMNode)``

In case a developer wants to show an image even if it is not inside the viewport the ``lazySizes.unveilLazy(DOMNode)`` can be called:

```js
lazySizes.unveilLazy(imgElem);
```

#####``lazySizes.updateAllSizes()``

In case one or more image elements with the attribute ``data-sizes="auto"`` have changed in size ``lazySizes.updateAllSizes`` can be called (For example to implement element queries):

```js
lazySizes.updateAllSizes();
```

##Browser Support
**lazysizes** supports the following browsers: IE9+, Firefox 21+, Chrome 27+, Safari 6.1+, iOS Safari 7.0+, Android 4.1+

##Contributing
Fixes, PRs and issues are always welcome, make sure to create a new branch from the **master** (not the gh-pages branch), validate against JShint and test in all browsers. In case of an API/documentation change make sure to also document it here in the readme.md.

##Available plugins in this repo

###[RIAS plugin](plugins/rias)
The [RIAS plugin](plugins/rias) plugin enables lazySizes to hook into any third party (ReSrc, Pixtulate, mobify, WURFL's Image Tailor ...) or self hosted restful responsive image service (responsive image on demand). It makes responsive images even more easier without any need for another third party script.

###[OPTIMUMX plugin](plugins/optimumx)
The ``srcset`` attribute with the *w* descriptor and ``sizes`` attribute automatically also includes high DPI images. But each image has a different optimal pixel density, which might be lower (1.5x) than the pixel density of your device (2x or 3x). This information is unknown to the browser and therefore can't be optimized for. The [lazySizes optimumx extension](plugins/optimumx) gives you more control to trade between perceived quality vs. perceived performance.

###[unveilhooks plugin](plugins/unveilhooks)
The [unveilhooks plugin](plugins/unveilhooks) plugin enables lazySizes to lazyload background images, widgets/components/scripts, styles and video/audio elements.

###[include plugin](plugins/include)
The [include plugin](plugins/include) plugin enables lazySizes to lazyload content, styles or AMD modules either simply postboned or conditionally (for example matching certain media queries). This extension also heavily simplifies architecture of conditional, dynamically changing responsive behavior and has an extreme great scalability.

###[bgset plugin](plugins/bgset)
The bgset plugin allows lazyload multiple background images with different resolutions/sizes (responsive background images). In case you only need one image use the unveilhooks extension.

###[print plugin](plugins/unveilhooks)
The [print plugin](plugins/unveilhooks) plugin enables lazySizes to unveil all elements as soon as the user starts to print. (Or set ``lazySizesConfig.preloadAfterLoad`` to ``true``).

##Why lazysizes
In the past I often struggled using lazy image loaders, because the "main check function" is called repeatedly and with a high frequency. Which makes it hard to fullfill two purposes runtime and memory efficiency. And looking into the source code of most so called lazy loaders often also unveils lazy developers...

But in a world of responsive retina optimized images on the one hand and JS widgets like carousels or tabs (a lot of initially hidden images) on the other hand lazy loading images becomes more and more important. And therefore I created this project. And in fact **lazysizes** is different.

Due to the fact, that it is designed to be invoked with a high frequency and therefore works highly efficient, it was possible to hook into all kind of events as also add a mutationobserver and therefore this lazyloader works as a simple drop in solution, you simply write/render your markup and no matter wether it was added by AJAX or revealed by a JS or CSS animation it will be picked up by **layzsizes**.

```html
<!-- responsive example: -->
<img
	data-sizes="auto"
    src="lqip-src.jpg"
	data-srcset="lqip-src.jpg 100w,
    image2.jpg 300w,
    image3.jpg 600w,
    image4.jpg 900w" class="lazyload" />
    
<!-- or non-responsive: -->
<img src="lqip-src.jpg" data-src="image.jpg" class="lazyload" />
```

##Specifying image dimensions (minimizing reflows)
To minimize reflows and content jumping the width **and** the height of an image should be specified. For "static" images this can done using either CSS or using the content attributes:

```html
<img
	src="http://placehold.it/175x75"
    width="350"
    height="150"
	data-srcset="http://placehold.it/350x150 1x,
    http://placehold.it/700x300 2x" class="lazyload" />
```

For flexible responsive images [CSS intrinsic ratio scaling](http://alistapart.com/article/creating-intrinsic-ratios-for-video/) can be used:

```html
<style>
.ratio-container {
	position: relative;
    padding-bottom: 42.86%; /* 56.25%: 16:9 ratio */
    height: 0;
    overflow: hidden;
}
.ratio-container img,
.ratio-container video,
.ratio-container iframe {
	position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
</style>

<div class="ratio-container">
    <img
        src="http://placehold.it/175x75"
        data-sizes="auto"
        data-srcset="http://placehold.it/175x75 175w,
        http://placehold.it/350x150 350w,
        http://placehold.it/700x300 700w,
        http://placehold.it/1400x600 1400w" class="lazyload" />
</div>
```
